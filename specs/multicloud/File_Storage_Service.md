
# Fileshare Management in Multi-Cloud

**Authors:** [Ashit Kumar](https://github.com/kumarashit), [Himanshu Varshney](https://github.com/himanshuvar)

## Goals

This document provides the requirement, use case, design and implementation guidelines for supporting File Storage support in SODA core project: multi-cloud (Gelato).

It will discuss different aspects and considerations for supporting File Storage support for different cloud providers.

With SODA V1 release, it should deliver Cloud File Share or Filestore management support for:
1.  AWS EFS
2.  Azure File Share
3.  Google Cloud Filestore

With subsequent release, it will target to support more Cloud vendors.
Multi-cloud File Storage service will provide
1.  Creation/Deletion/Updation of File shares
2.  Creating/Deleting/Updating the mount-targets
3.  Set/Un-set File Share Access Policies

## Motivation and background
SODA Foundation multi-cloud (Gelato) project provides policy based data mobility across public, private and Hybrid clouds. This mobility is currently limited to Object Storage only.

The purpose is to provide a unified Data framework acros core and cloud. Currently SODA Core projects already provide the native Fileshare support for on-premise clusters. This support of Cloud Fileshare/Storage will extend this support to Cloud.

The purpose is to narrow the gap between on-premise and Cloud File Storage access.
The proposal here is to broaden the scope to File Storage for multi-cloud. Different Cloud vendors provide File storage services. Filesystem based storage is important for the applications of instances.

File storage commonly referred to as Network Fileshare is the centralized repository for files. It is very useful for sharing the contents across multiple servers. Fileshares can be accessed by instances into the Cloud as well as the on-premise instances and across AZs and Regions.

These File storage provides services which fits into the services multicloud provides.

   **Availability**: The File share snapshots provide the feature to create backup and recover as and when required.

   **Data Lifecycle Management**: File shares provide the lifecycle better storage and cost management. Infrequently accessed shares can be moved to low cost storage. (This capability depends upon the service by Cloud providers)
    Example: For AWS: There are two storage Classes 1) Standard 2) EFS IA

   **Scalability**: Cloud FileShare/File Storage can scale to PBs without affecting the underlying applications

   **Security**: The File Storage provided by the vendors provide data security for both Data at Rest as well Data in-transit

   **Extensibility**: The server data can be shared across Cloud as well as on-premises

## Non-Goals
1.  Creating network and security for mounting the File Shares across VPC/Region/Cloud-premise
2.  Instance creation
3.  Mounting the File Shares
4.  AWS FSx support
5.  Creation/Deletion of File and Directory on Shares

## Assumptions and Constraints
TBD

## Use case View
   **Migration**: Migrate on-premise File or File-Share based applications to cloud. Simply mount the Cloud FileShare to your on-premise server and copy data.

   **LTR (Long Term Retention)**: Data Deluge, ever changing business requirements and different compliance regulations demands LTR. With Cloud Vendor File Share backups or moving on-premises data to Cloud can help for Long term retention

   **Cloudburst**: Copy on-premises data to Cloud FileShare, analyze the data and get back the report on-premise. Additional benefit to it is the FileShare scalability, performance, throughput and low TCO

   **LiftNShift**:  Fileshares are commonly used storage on-premise. While migrating to Cloud this can be simply a lift and shift using Cloud services like AWS Manage Fileshares or Azure File Service

   **User Local Data Space**: Fileshares can be used as the private space for users. They can use File storage for accessing the data from any Cloud instance inside the region

   **Lifecycle policy**:  Life Cycle for File shares to move the infrequently accessed shares to low cost storage. (Similar to object storage lifecycle management)

## Requirements
1.  File Storage support for multi cloud
2.  File Share management for multi cloud

### Input Requirements

High level requirement is to add File storage for Cloud Data Management. Already Object storage is supported for in multi-cloud for Data Migration and Lifecycle policies

### Feature Requirements
Add File Storage Management in SODA multi-cloud/Gelato.

#### Requirement Analysis
File Storage Management: Though Object storage solves the provisioning management issues presented by the storage expansion and data growth needs as well as the need for resiliency, File storage provides easy integration. Object Storage provides the Web based storage service where one needs to use the RESTFull API calls. Whereas one can access FileShares using the familiar Operating System calls which makes it an easy integration for existing services.

Gelato/SODA multi-cloud project should provide the flexibility and unified ease of use for managing the File shares. This include:
1.  Fileshare creation/deletion/update
2.  Fileshare mount targets management
3.  Lifecycle Management policy

#### Functional Requirements
1.  SODA multi-cloud project should be able to Manage Cloud File storage like Azure File System, AWS EFS, Google Cloud Filestor
2.  Services are provided for different Cloud Providers like AWS, Azure, GCP, IBM etc
3.  Use the Cloud Credentials in a multi-tenant environment for providing services

#### Non Functional Requirements
1.  Include the Vendor libs for File Storage management
2.  SODA multi-cloud installation handles any specific library installation/uninstallation requirements for File storage support


## Architecture Analysis
Here is the High level Architecture for multi cloud File storage support

 ![Arch FileService Multicloud](resources/ArchFileServiceMulticloud.png)




### Module Architecture

![Module Arch FileShare](resources/MulticloudFileModuleArch.png)



### High Level Module architecture
1.  Client calls multi-cloud API for File Shares
2.  Call is handled by API Handler
3.  Authenticates the API call
4.  Send the request to the new service called “File Service”
5.  File service updates the metadata and call the Backend adapter
6.  Backend adapter based upon the request connects to the required/specific Cloud backend and serves the request back
7.  Any required data received from Backend is updated in DB


## Development and Deployment Context
##### AWS EFS Go-SDK:
[https://docs.aws.amazon.com/sdk-for-go/api/service/efs/](https://docs.aws.amazon.com/sdk-for-go/api/service/efs/)

##### GCP Cloud Filestorage REST API
[https://cloud.google.com/filestore/docs/reference/rest](https://cloud.google.com/filestore/docs/reference/rest)

##### Azure FileShare API
[https://docs.microsoft.com/en-us/rest/api/storageservices/File-Service-REST-API?redirectedfrom=MSDN](https://docs.microsoft.com/en-us/rest/api/storageservices/File-Service-REST-API?redirectedfrom=MSDN)
[https://godoc.org/github.com/Azure/azure-sdk-for-go/storage](https://godoc.org/github.com/Azure/azure-sdk-for-go/storage)
[https://github.com/Azure/azure-storage-file-go/](https://github.com/Azure/azure-storage-file-go/)

#### Sample code for AWS EFS services
[https://github.com/kumarashit/cloud_test/blob/master/aws/aws_connect.go](https://github.com/kumarashit/cloud_test/blob/master/aws/aws_connect.go)

#### Sample code for create and List shares in Azure
```
import (
    "context"
    "net/url"
    "fmt"
    "github.com/Azure/azure-storage-file-go/azfile"
    "github.com/Azure/azure-sdk-for-go/storage"
    log "github.com/sirupsen/logrus"
)

// Reference: https://godoc.org/github.com/Azure/azure-storage-file-go/azfile
func ListFileshares(accountName, accountKey string) {
   credential, err := azfile.NewSharedKeyCredential(accountName, accountKey)
   u, _ := url.Parse(fmt.Sprintf("https://%s.file.core.windows.net/aks-share2", accountName))
   if err != nil {
     log.Infof("create credential[Azure Blob] failed, err:%v\n", err)
     return
    }
    shareURL := azfile.NewShareURL(*u, azfile.NewPipeline(credential, azfile.PipelineOptions{}))
    ctx := context.Background()
    _, err = shareURL.Create(ctx, azfile.Metadata{"createdby": "AKS"}, 2)
    if err != nil {
        log.Fatal(err)
    }
    // List file share
    basicClient, client_err := storage.NewBasicClient(accountName, accountKey)
    if client_err != nil {
        fmt.Println("Error in getting client")
        return
    }
    fsc := basicClient.GetFileService()
    rsp, rsp_err := fsc.ListShares(storage.ListSharesParameters{})
    if rsp_err != nil {
        fmt.Println("Error in response")
        return
    }
    fmt.Println(rsp)
```

### Interface Model
FileShare Service is available through any client which can use the Cloud File Storage SODA API
1.  SODA Dashboard
2.  SODA REST APIs

#### Requirements for User Interface:
1.  Provide option to register backend
2.  Type: AWS-Storage/GCP-Storage/Azure-Storage
3.  Required params: Access Key, Secret Key and Region
4.  Create Fileshare
5.  Update/Edit Fileshare
6.  Delete FileShare
7.  Get/List FileShare

### Data Model
Comparision for parameters/attributes across different cloud vendors. This is as per the APIs/Interface provided by the cloud
![Comparision](resources/MulticloudParameterComp.png)


### API spec

#### Register a Backend

```
[POST] /v1/<tenantId>/backends
```
##### Request
```
{
    "Name": "<backendName>",  // required
    "Type": "<backendType>",  // required
    "Region": "<region>",     // required
    "Access": "<accessKey>",  // required
    "Security": "<secretKey>" // required
}
```
##### Response

```
{
    "id": "<backendId>",
    "tenantId": "<tenantId>",
    "userId": "<userId>",
    "name": "<backendName>",
    "type": "<backendType>",
    "region": "<region>",
    "access": "<accessKey>",
    "security": "<secretKey>"
}
```

###### Example 1: Registering an AWS Backend for File Share

```
[POST] /v1/<tenantId>/backends
```
Request
```
{
  "Name": "aws-backend-file",
  "Type": "aws-file",
  "Region": "ap-south-1",
  "Access": "myAccessKey",
  "Security": "mySecretKey"
}
```
Response
```
{
    "id": "5ef1c74be170650001f2d030",
    "tenantId": "94b280022d0c4401bcf3b0ea85870519",
    "userId": "558057c4256545bd8a307c37464003c9",
    "name": "aws-backend-file",
    "type": "aws-file",
    "region": "ap-south-1",
    "access": "myAccessKey",
    "security": "mySecretKey"
}
```

###### Example 2: Registering an Azure Backend for File Share

```
[POST] /v1/<tenantId>/backends
```
Request
```
{
    "Name": "azure-backend-file",
    "Type": "azure-file",
    "Region": "East Asia",
    "Access": "myaccount",
    "Security": "mySecretKey"
}
```
Response
```
{
    "id": "5ef1c6fce170650001f2d02f",
    "tenantId": "94b280022d0c4401bcf3b0ea85870519",
    "userId": "558057c4256545bd8a307c37464003c9",
    "name": "azure-backend-file",
    "type": "azure-file",
    "region": "East Asia",
    "access": "myaccount",
    "security": "mySecretKey"
}
```

#### Create a File share
```
[POST] /v1/<tenantId>/file/shares
```
##### Request
```
{
    "name": "<fileShareName>",                // required
    "description": "<fileShareDescription>",  // optional
    "backendId": "<backendId>",               // required
    "size": "<QuotaSizeInBytes>",             // optional
    "encrypted": <isEncrypted>,               // optional for Azure & required for AWS Encrpted FS -> isEncrypted = true/false
    "encryptionSettings": {                   // optional for Azure & required for AWS Encrpted FS if isEncrypted = true -> key = "KmsKeyId" & value = "ARN/Id"
        "key": "value"
    }
    "tags": [                                 // optional for Azure & required for AWS
      {
        "key": "Name",
        "value": "<fileShareName>"
      }
    ],
    "metadata": {                             // required
      "PerformanceMode": "<PerformanceMode>",                           // optional for Azure & required for AWS PerformanceMode -> "generalPurpose" or "maxIO"
      "ThroughputMode": "<ThroughputMode>",                             // optional for Azure & required for AWS ThroughputMode -> "bursting" or "provisioned"
      "ProvisionedThroughputInMibps": "<ProvisionedThroughputInMibps>", // optional for Azure & required for AWS ProvisionedThroughputInMibps if ThroughputMode = "provisioned" <ProvisionedThroughputInMibps> Valid range is 1-1024 MiB/s
      "key": "value"                                                    // optional for AWS & required for Azure -> Key-Value pair for tagging FS in Azure
    }
}
```
##### Response
```
{
    "name": "<fileShareName>",                // included
    "description": "<fileShareDescription>",  // excluded if not provided in the request i.e. if Empty
    "backendId": "<backendId>",               // included
    "size": "<QuotaSizeInBytes>",             // included if not 0 otherwise excluded
    "encrypted": <isEncrypted>,               // included if true otherwise excluded
    "encryptionSettings": {                   // included if encrypted is true otherwise excluded
        "key": "value"
    }
    "tags": [                                 // included for AWS & excluded for Azure
      {
        "key": "Name",
        "value": "<fileShareName>"
      }
    ],
    "metadata": {                             // included
      "PerformanceMode": "<PerformanceMode>",                           // included for AWS & excluded for Azure
      "ThroughputMode": "<ThroughputMode>",                             // included for AWS & excluded for Azure
      "ProvisionedThroughputInMibps": "<ProvisionedThroughputInMibps>", // included for AWS & excluded for Azure if ThroughputMode = "provisioned"
      "key": "value"                                                    // included
    }
}
```

###### Example 1: Creating File Share for AWS Backend

```
[POST] /v1/<tenantId>/file/shares
```
Request
```
{
    "name": "ashitfs",
    "description": "AWS FileShare",
    "backendId": "5ef1c74be170650001f2d030",
    "tags": [
      {
        "key": "Name",
        "value": "ashitfs"
      }
    ],
    "metadata": {
      "PerformanceMode": "generalPurpose",
      "ThroughputMode": "bursting"
    }
}
```
Response
```
{
    "id": "5ef1d9e0202b510001c3804b",
    "createdAt": "2020-06-23T16:00:56",
    "updatedAt": "2020-06-23T16:00:56",
    "name": "ashitfs",
    "description": "AWS FileShare",
    "tenantId": "94b280022d0c4401bcf3b0ea85870519",
    "userId": "558057c4256545bd8a307c37464003c9",
    "backendId": "5ef1c74be170650001f2d030",
    "backend": "aws-backend-file",
    "size": 6144,
    "status": "available",
    "tags": [
        {
            "key": "Name",
            "value": "ashitfs"
        }
    ],
    "metadata": {
        "fields": {
            "CreationTimeAtBackend": {
                "Kind": {
                    "StringValue": "2020-06-23T10:30:52Z"
                }
            },
            "CreationToken": {
                "Kind": {
                    "StringValue": "CEX5a/)I]#nNP^=V~OH@r7KnBwTW=0!yyJ[}"
                }
            },
            "FileSystemId": {
                "Kind": {
                    "StringValue": "fs-a4e26c75"
                }
            },
            "FileSystemSize": {
                "Kind": {
                    "StructValue": {
                        "fields": {
                            "Timestamp": {
                                "Kind": {
                                    "NumberValue": 0
                                }
                            },
                            "Value": {
                                "Kind": {
                                    "NumberValue": 6144
                                }
                            },
                            "ValueInIA": {
                                "Kind": {
                                    "NumberValue": 0
                                }
                            },
                            "ValueInStandard": {
                                "Kind": {
                                    "NumberValue": 6144
                                }
                            }
                        }
                    }
                }
            },
            "LifeCycleState": {
                "Kind": {
                    "StringValue": "available"
                }
            },
            "Name": {
                "Kind": {
                    "StringValue": "ashitfs"
                }
            },
            "NumberOfMountTargets": {
                "Kind": {
                    "NumberValue": 0
                }
            },
            "OwnerId": {
                "Kind": {
                    "StringValue": "676599967111"
                }
            },
            "PerformanceMode": {
                "Kind": {
                    "StringValue": "generalPurpose"
                }
            },
            "ThroughputMode": {
                "Kind": {
                    "StringValue": "bursting"
                }
            }
        }
    }
}
```

###### Example 2: Creating an encrypted File Share with provisioned throughput for AWS Backend

```
[POST] /v1/<tenantId>/file/shares
```
Request
```
{
    "name": "himanshufs",
    "description": "AWS FileShare",
    "backendId": "5ef1c74be170650001f2d030",
    "encrypted": true,
    "encryptionSettings": {
        "KmsKeyId": "<ARN>"
    },
    "tags": [
        {
            "key": "Name",
            "value": "himanshufs"
        }
    ],
    "metadata": {
        "PerformanceMode": "maxIO",
        "ThroughputMode": "provisioned",
        "ProvisionedThroughputInMibps": 1
    }
}
```
Response
```
{
    "id": "5ef1db51202b510001c3804c",
    "createdAt": "2020-06-23T16:07:05",
    "updatedAt": "2020-06-23T16:07:05",
    "name": "himanshufs",
    "description": "AWS FileShare",
    "tenantId": "94b280022d0c4401bcf3b0ea85870519",
    "userId": "558057c4256545bd8a307c37464003c9",
    "backendId": "5ef1c74be170650001f2d030",
    "backend": "aws-backend-file",
    "size": 6144,
    "status": "available",
    "tags": [
        {
            "key": "Name",
            "value": "himanshufs"
        }
    ],
    "encrypted": true,
    "encryptionSettings": {
        "KmsKeyId": "<ARN>"
    },
    "metadata": {
        "fields": {
            "CreationTimeAtBackend": {
                "Kind": {
                    "StringValue": "2020-06-23T10:37:01Z"
                }
            },
            "CreationToken": {
                "Kind": {
                    "StringValue": "e%*#6!XJbmue6)VA4Jl{BsWbMS1v4xXYK8U}"
                }
            },
            "FileSystemId": {
                "Kind": {
                    "StringValue": "fs-68e26cb9"
                }
            },
            "FileSystemSize": {
                "Kind": {
                    "StructValue": {
                        "fields": {
                            "Timestamp": {
                                "Kind": {
                                    "NumberValue": 0
                                }
                            },
                            "Value": {
                                "Kind": {
                                    "NumberValue": 6144
                                }
                            },
                            "ValueInIA": {
                                "Kind": {
                                    "NumberValue": 0
                                }
                            },
                            "ValueInStandard": {
                                "Kind": {
                                    "NumberValue": 6144
                                }
                            }
                        }
                    }
                }
            },
            "LifeCycleState": {
                "Kind": {
                    "StringValue": "available"
                }
            },
            "Name": {
                "Kind": {
                    "StringValue": "himanshufs"
                }
            },
            "NumberOfMountTargets": {
                "Kind": {
                    "NumberValue": 0
                }
            },
            "OwnerId": {
                "Kind": {
                    "StringValue": "676599967111"
                }
            },
            "PerformanceMode": {
                "Kind": {
                    "StringValue": "maxIO"
                }
            },
            "ProvisionedThroughputInMibps": {
                "Kind": {
                    "NumberValue": 1
                }
            },
            "ThroughputMode": {
                "Kind": {
                    "StringValue": "provisioned"
                }
            }
        }
    }
}
```

###### Example 3: Creating File Share for Azure Backend

```
[POST] /v1/<tenantId>/file/shares
```
Request
```
{
    "name": "sanilfs",
    "description": "Azure FileShare",
    "backendId": "5ef1c6fce170650001f2d02f",
    "size": 1073741824,
    "metadata": {
        "createdby": "himanshu varshney",
        "organization": "soda foundation",
        "email": "himanshuvar@gmail.com"
    }
}
```
Response
```
{
    "id": "5ef1de68202b510001c3804f",
    "createdAt": "2020-06-23T16:20:16",
    "updatedAt": "2020-06-23T16:20:16",
    "name": "sanilfs",
    "description": "Azure FileShare",
    "tenantId": "94b280022d0c4401bcf3b0ea85870519",
    "userId": "558057c4256545bd8a307c37464003c9",
    "backendId": "5ef1c6fce170650001f2d02f",
    "backend": "azure-backend-file",
    "size": 1073741824,
    "status": "available",
    "metadata": {
        "fields": {
            "Content-Length": {
                "Kind": {
                    "NumberValue": 0
                }
            },
            "Date": {
                "Kind": {
                    "StringValue": "Tue, 23 Jun 2020 10:50:15 GMT"
                }
            },
            "Etag": {
                "Kind": {
                    "StringValue": "0x8D8176336A2FE82"
                }
            },
            "Last-Modified": {
                "Kind": {
                    "StringValue": "Tue, 23 Jun 2020 10:50:15 GMT"
                }
            },
            "Server": {
                "Kind": {
                    "StringValue": "Windows-Azure-File/1.0 Microsoft-HTTPAPI/2.0"
                }
            },
            "Share-Usage-Bytes": {
                "Kind": {
                    "NumberValue": 0
                }
            },
            "X-Ms-Client-Request-Id": {
                "Kind": {
                    "StringValue": "0c994aad-2b45-4a70-56b5-06c9a6097485"
                }
            },
            "X-Ms-Has-Immutability-Policy": {
                "Kind": {
                    "StringValue": "false"
                }
            },
            "X-Ms-Has-Legal-Hold": {
                "Kind": {
                    "StringValue": "false"
                }
            },
            "X-Ms-Meta-Createdby": {
                "Kind": {
                    "StringValue": "himanshu varshney"
                }
            },
            "X-Ms-Meta-Email": {
                "Kind": {
                    "StringValue": "himanshuvar@gmail.com"
                }
            },
            "X-Ms-Meta-Organization": {
                "Kind": {
                    "StringValue": "soda foundation"
                }
            },
            "X-Ms-Request-Id": {
                "Kind": {
                    "StringValue": "73734e49-801a-0050-104c-4904b1000000"
                }
            },
            "X-Ms-Share-Quota": {
                "Kind": {
                    "NumberValue": 1
                }
            },
            "X-Ms-Version": {
                "Kind": {
                    "StringValue": "2019-02-02"
                }
            }
        }
    }
}
```

#### List all shares

```
[GET] /v1/<tenantId>/file/shares
```
##### Request
```
  Query Parameter -> “backendId” : “<backendId>”
```
##### Response

```
[
  {
      "name": "<fileShareName>",                // included
      "description": "<fileShareDescription>",  // excluded if not provided at the create/update time i.e. if Empty
      "backendId": "<backendId>",               // included
      "size": "<QuotaSizeInBytes>",             // included if not 0 otherwise excluded
      "encrypted": <isEncrypted>,               // included if true otherwise excluded
      "encryptionSettings": {                   // included if encrypted is true otherwise excluded
          "key": "value"
      }
      "tags": [                                 // included for AWS & excluded for Azure
        {
          "key": "Name",
          "value": "<fileShareName>"
        }
      ],
      "metadata": {                             // included
        "PerformanceMode": "<PerformanceMode>",                           // included for AWS & excluded for Azure
        "ThroughputMode": "<ThroughputMode>",                             // included for AWS & excluded for Azure
        "ProvisionedThroughputInMibps": "<ProvisionedThroughputInMibps>", // included for AWS & excluded for Azure if ThroughputMode = "provisioned"
        "key": "value"                                                    // included
      }
  }
]
```

###### Example 1: List all File Shares

```
[GET] /v1/<tenantId>/file/shares
```

Response
```
{
    "fileshares": [
        {
            "id": "5ef1d9e0202b510001c3804b",
            "createdAt": "2020-06-23T16:00:56",
            "updatedAt": "2020-06-23T16:00:56",
            "name": "ashitfs",
            "description": "AWS FileShare",
            "tenantId": "94b280022d0c4401bcf3b0ea85870519",
            "userId": "558057c4256545bd8a307c37464003c9",
            "backendId": "5ef1c74be170650001f2d030",
            "backend": "aws-backend-file",
            "size": 6144,
            "status": "available",
            "tags": [
                {
                    "key": "Name",
                    "value": "ashitfs"
                }
            ],
            "metadata": {
                "fields": {
                    "CreationTimeAtBackend": {
                        "Kind": {
                            "StringValue": "2020-06-23T10:30:52Z"
                        }
                    },
                    "CreationToken": {
                        "Kind": {
                            "StringValue": "CEX5a/)I]#nNP^=V~OH@r7KnBwTW=0!yyJ[}"
                        }
                    },
                    "FileSystemId": {
                        "Kind": {
                            "StringValue": "fs-a4e26c75"
                        }
                    },
                    "FileSystemSize": {
                        "Kind": {
                            "StructValue": {
                                "fields": {
                                    "Timestamp": {
                                        "Kind": {
                                            "NumberValue": 0
                                        }
                                    },
                                    "Value": {
                                        "Kind": {
                                            "NumberValue": 6144
                                        }
                                    },
                                    "ValueInIA": {
                                        "Kind": {
                                            "NumberValue": 0
                                        }
                                    },
                                    "ValueInStandard": {
                                        "Kind": {
                                            "NumberValue": 6144
                                        }
                                    }
                                }
                            }
                        }
                    },
                    "LifeCycleState": {
                        "Kind": {
                            "StringValue": "available"
                        }
                    },
                    "Name": {
                        "Kind": {
                            "StringValue": "ashitfs"
                        }
                    },
                    "NumberOfMountTargets": {
                        "Kind": {
                            "NumberValue": 0
                        }
                    },
                    "OwnerId": {
                        "Kind": {
                            "StringValue": "676599967111"
                        }
                    },
                    "PerformanceMode": {
                        "Kind": {
                            "StringValue": "generalPurpose"
                        }
                    },
                    "ThroughputMode": {
                        "Kind": {
                            "StringValue": "bursting"
                        }
                    }
                }
            }
        },
        {
            "id": "5ef1db51202b510001c3804c",
            "createdAt": "2020-06-23T16:07:05",
            "updatedAt": "2020-06-23T16:07:05",
            "name": "himanshufs",
            "description": "AWS FileShare",
            "tenantId": "94b280022d0c4401bcf3b0ea85870519",
            "userId": "558057c4256545bd8a307c37464003c9",
            "backendId": "5ef1c74be170650001f2d030",
            "backend": "aws-backend-file",
            "size": 6144,
            "status": "available",
            "tags": [
                {
                    "key": "Name",
                    "value": "himanshufs"
                }
            ],
            "encrypted": true,
            "encryptionSettings": {
                "KmsKeyId": "<ARN>"
            },
            "metadata": {
                "fields": {
                    "CreationTimeAtBackend": {
                        "Kind": {
                            "StringValue": "2020-06-23T10:37:01Z"
                        }
                    },
                    "CreationToken": {
                        "Kind": {
                            "StringValue": "e%*#6!XJbmue6)VA4Jl{BsWbMS1v4xXYK8U}"
                        }
                    },
                    "FileSystemId": {
                        "Kind": {
                            "StringValue": "fs-68e26cb9"
                        }
                    },
                    "FileSystemSize": {
                        "Kind": {
                            "StructValue": {
                                "fields": {
                                    "Timestamp": {
                                        "Kind": {
                                            "NumberValue": 0
                                        }
                                    },
                                    "Value": {
                                        "Kind": {
                                            "NumberValue": 6144
                                        }
                                    },
                                    "ValueInIA": {
                                        "Kind": {
                                            "NumberValue": 0
                                        }
                                    },
                                    "ValueInStandard": {
                                        "Kind": {
                                            "NumberValue": 6144
                                        }
                                    }
                                }
                            }
                        }
                    },
                    "LifeCycleState": {
                        "Kind": {
                            "StringValue": "available"
                        }
                    },
                    "Name": {
                        "Kind": {
                            "StringValue": "himanshufs"
                        }
                    },
                    "NumberOfMountTargets": {
                        "Kind": {
                            "NumberValue": 0
                        }
                    },
                    "OwnerId": {
                        "Kind": {
                            "StringValue": "676599967111"
                        }
                    },
                    "PerformanceMode": {
                        "Kind": {
                            "StringValue": "maxIO"
                        }
                    },
                    "ProvisionedThroughputInMibps": {
                        "Kind": {
                            "NumberValue": 1
                        }
                    },
                    "ThroughputMode": {
                        "Kind": {
                            "StringValue": "provisioned"
                        }
                    }
                }
            }
        },
        {
            "id": "5ef1de68202b510001c3804f",
            "createdAt": "2020-06-23T16:20:16",
            "updatedAt": "2020-06-23T16:20:16",
            "name": "sanilfs",
            "description": "Azure FileShare",
            "tenantId": "94b280022d0c4401bcf3b0ea85870519",
            "userId": "558057c4256545bd8a307c37464003c9",
            "backendId": "5ef1c6fce170650001f2d02f",
            "backend": "azure-backend-file",
            "size": 1073741824,
            "status": "available",
            "metadata": {
                "fields": {
                    "Content-Length": {
                        "Kind": {
                            "NumberValue": 0
                        }
                    },
                    "Date": {
                        "Kind": {
                            "StringValue": "Tue, 23 Jun 2020 10:50:15 GMT"
                        }
                    },
                    "Etag": {
                        "Kind": {
                            "StringValue": "0x8D8176336A2FE82"
                        }
                    },
                    "Last-Modified": {
                        "Kind": {
                            "StringValue": "Tue, 23 Jun 2020 10:50:15 GMT"
                        }
                    },
                    "Server": {
                        "Kind": {
                            "StringValue": "Windows-Azure-File/1.0 Microsoft-HTTPAPI/2.0"
                        }
                    },
                    "Share-Usage-Bytes": {
                        "Kind": {
                            "NumberValue": 0
                        }
                    },
                    "X-Ms-Client-Request-Id": {
                        "Kind": {
                            "StringValue": "0c994aad-2b45-4a70-56b5-06c9a6097485"
                        }
                    },
                    "X-Ms-Has-Immutability-Policy": {
                        "Kind": {
                            "StringValue": "false"
                        }
                    },
                    "X-Ms-Has-Legal-Hold": {
                        "Kind": {
                            "StringValue": "false"
                        }
                    },
                    "X-Ms-Meta-Createdby": {
                        "Kind": {
                            "StringValue": "himanshu varshney"
                        }
                    },
                    "X-Ms-Meta-Email": {
                        "Kind": {
                            "StringValue": "himanshuvar@gmail.com"
                        }
                    },
                    "X-Ms-Meta-Organization": {
                        "Kind": {
                            "StringValue": "soda foundation"
                        }
                    },
                    "X-Ms-Request-Id": {
                        "Kind": {
                            "StringValue": "73734e49-801a-0050-104c-4904b1000000"
                        }
                    },
                    "X-Ms-Share-Quota": {
                        "Kind": {
                            "NumberValue": 1
                        }
                    },
                    "X-Ms-Version": {
                        "Kind": {
                            "StringValue": "2019-02-02"
                        }
                    }
                }
            }
        }
    ],
    "next": 3
}
```

###### Example 2: List File Shares by Backend

```
[GET] /v1/<tenantId>/file/shares?backendId=5ef1c6fce170650001f2d02f
```
Response

```
{
    "fileshares": [
        {
            "id": "5ef1de68202b510001c3804f",
            "createdAt": "2020-06-23T16:20:16",
            "updatedAt": "2020-06-23T16:20:16",
            "name": "sanilfs",
            "description": "Azure FileShare",
            "tenantId": "94b280022d0c4401bcf3b0ea85870519",
            "userId": "558057c4256545bd8a307c37464003c9",
            "backendId": "5ef1c6fce170650001f2d02f",
            "backend": "azure-backend-file",
            "size": 1073741824,
            "status": "available",
            "metadata": {
                "fields": {
                    "Content-Length": {
                        "Kind": {
                            "NumberValue": 0
                        }
                    },
                    "Date": {
                        "Kind": {
                            "StringValue": "Tue, 23 Jun 2020 10:50:15 GMT"
                        }
                    },
                    "Etag": {
                        "Kind": {
                            "StringValue": "0x8D8176336A2FE82"
                        }
                    },
                    "Last-Modified": {
                        "Kind": {
                            "StringValue": "Tue, 23 Jun 2020 10:50:15 GMT"
                        }
                    },
                    "Server": {
                        "Kind": {
                            "StringValue": "Windows-Azure-File/1.0 Microsoft-HTTPAPI/2.0"
                        }
                    },
                    "Share-Usage-Bytes": {
                        "Kind": {
                            "NumberValue": 0
                        }
                    },
                    "X-Ms-Client-Request-Id": {
                        "Kind": {
                            "StringValue": "0c994aad-2b45-4a70-56b5-06c9a6097485"
                        }
                    },
                    "X-Ms-Has-Immutability-Policy": {
                        "Kind": {
                            "StringValue": "false"
                        }
                    },
                    "X-Ms-Has-Legal-Hold": {
                        "Kind": {
                            "StringValue": "false"
                        }
                    },
                    "X-Ms-Meta-Createdby": {
                        "Kind": {
                            "StringValue": "himanshu varshney"
                        }
                    },
                    "X-Ms-Meta-Email": {
                        "Kind": {
                            "StringValue": "himanshuvar@gmail.com"
                        }
                    },
                    "X-Ms-Meta-Organization": {
                        "Kind": {
                            "StringValue": "soda foundation"
                        }
                    },
                    "X-Ms-Request-Id": {
                        "Kind": {
                            "StringValue": "73734e49-801a-0050-104c-4904b1000000"
                        }
                    },
                    "X-Ms-Share-Quota": {
                        "Kind": {
                            "NumberValue": 1
                        }
                    },
                    "X-Ms-Version": {
                        "Kind": {
                            "StringValue": "2019-02-02"
                        }
                    }
                }
            }
        }
    ],
    "next": 1
}
```

#### Get details of a share
```
[GET] /v1/<tenantId>/file/shares/<id>
```
##### Request
```
    Path Parameter -> “id” : “<fileShareId>”
```
##### Response
```
{
    "name": "<fileShareName>",                // included
    "description": "<fileShareDescription>",  // excluded if not provided at the create/update time i.e. if Empty
    "backendId": "<backendId>",               // included
    "size": "<QuotaSizeInBytes>",             // included if not 0 otherwise excluded
    "encrypted": <isEncrypted>,               // included if true otherwise excluded
    "encryptionSettings": {                   // included if encrypted is true otherwise excluded
        "key": "value"
    }
    "tags": [                                 // included for AWS & excluded for Azure
      {
        "key": "Name",
        "value": "<fileShareName>"
      }
    ],
    "metadata": {                             // included
      "PerformanceMode": "<PerformanceMode>",                           // included for AWS & excluded for Azure
      "ThroughputMode": "<ThroughputMode>",                             // included for AWS & excluded for Azure
      "ProvisionedThroughputInMibps": "<ProvisionedThroughputInMibps>", // included for AWS & excluded for Azure if ThroughputMode = "provisioned"
      "key": "value"                                                    // included
    }
}
```

###### Example: Get File Share details for id = 5ef1db51202b510001c3804c

```
[GET] /v1/<tenantId>/file/shares/5ef1db51202b510001c3804c
```
Response

```
{
    "id": "5ef1db51202b510001c3804c",
    "createdAt": "2020-06-23T16:07:05",
    "updatedAt": "2020-06-23T16:07:05",
    "name": "himanshufs",
    "description": "AWS FileShare",
    "tenantId": "94b280022d0c4401bcf3b0ea85870519",
    "userId": "558057c4256545bd8a307c37464003c9",
    "backendId": "5ef1c74be170650001f2d030",
    "backend": "aws-backend-file",
    "size": 6144,
    "status": "available",
    "tags": [
        {
            "key": "Name",
            "value": "himanshufs"
        }
    ],
    "encrypted": true,
    "encryptionSettings": {
        "KmsKeyId": "arn:aws:kms:ap-south-1:676599967111:key/a6e0716e-0e90-406b-b404-b8ee8de891fc"
    },
    "metadata": {
        "fields": {
            "CreationTimeAtBackend": {
                "Kind": {
                    "StringValue": "2020-06-23T10:37:01Z"
                }
            },
            "CreationToken": {
                "Kind": {
                    "StringValue": "e%*#6!XJbmue6)VA4Jl{BsWbMS1v4xXYK8U}"
                }
            },
            "FileSystemId": {
                "Kind": {
                    "StringValue": "fs-68e26cb9"
                }
            },
            "FileSystemSize": {
                "Kind": {
                    "StructValue": {
                        "fields": {
                            "Timestamp": {
                                "Kind": {
                                    "NumberValue": 0
                                }
                            },
                            "Value": {
                                "Kind": {
                                    "NumberValue": 6144
                                }
                            },
                            "ValueInIA": {
                                "Kind": {
                                    "NumberValue": 0
                                }
                            },
                            "ValueInStandard": {
                                "Kind": {
                                    "NumberValue": 6144
                                }
                            }
                        }
                    }
                }
            },
            "LifeCycleState": {
                "Kind": {
                    "StringValue": "available"
                }
            },
            "Name": {
                "Kind": {
                    "StringValue": "himanshufs"
                }
            },
            "NumberOfMountTargets": {
                "Kind": {
                    "NumberValue": 0
                }
            },
            "OwnerId": {
                "Kind": {
                    "StringValue": "676599967111"
                }
            },
            "PerformanceMode": {
                "Kind": {
                    "StringValue": "maxIO"
                }
            },
            "ProvisionedThroughputInMibps": {
                "Kind": {
                    "NumberValue": 1
                }
            },
            "ThroughputMode": {
                "Kind": {
                    "StringValue": "provisioned"
                }
            }
        }
    }
}
```

#### Modify/Update a File share
```
[PUT] /v1/<tenantId>/file/shares/<id>
```
##### Request
```
{
    "description": "<fileShareDescription>",  // optional
    "size": "<QuotaSizeInBytes>",             // optional for AWS & required for Azure
    "tags": [                                 // optional for Azure & required for AWS
      {
        "key": "Name",
        "value": "<fileShareName>"
      }
    ],
    "metadata": {                             //  optional for Azure & required for AWS
      "PerformanceMode": "<PerformanceMode>",                           // optional for Azure & required for AWS PerformanceMode -> "generalPurpose" or "maxIO"
      "ThroughputMode": "<ThroughputMode>",                             // optional for Azure & required for AWS ThroughputMode -> "bursting" or "provisioned"
      "ProvisionedThroughputInMibps": "<ProvisionedThroughputInMibps>", // optional for Azure & required for AWS ProvisionedThroughputInMibps if ThroughputMode = "provisioned" <ProvisionedThroughputInMibps> Valid range is 1-1024 MiB/s          
    }
}
```
##### Response
```
{
    "name": "<fileShareName>",                // included
    "description": "<fileShareDescription>",  // excluded if not provided i.e. if Empty
    "backendId": "<backendId>",               // included
    "size": "<QuotaSizeInBytes>",             // included if not 0 otherwise excluded
    "encrypted": <isEncrypted>,               // included if true otherwise excluded
    "encryptionSettings": {                   // included if encrypted is true otherwise excluded
        "key": "value"
    }
    "tags": [                                 // included for AWS & excluded for Azure
      {
        "key": "Name",
        "value": "<fileShareName>"
      }
    ],
    "metadata": {                             // included
      "PerformanceMode": "<PerformanceMode>",                           // included for AWS & excluded for Azure
      "ThroughputMode": "<ThroughputMode>",                             // included for AWS & excluded for Azure
      "ProvisionedThroughputInMibps": "<ProvisionedThroughputInMibps>", // included for AWS & excluded for Azure if ThroughputMode = "provisioned"
      "key": "value"                                                    // included
    }
}
```

###### Example 1: Updating File Share for AWS Backend to Provisioned Throughput

```
[PUT] /v1/<tenantId>/file/shares/5ef1d9e0202b510001c3804b
```
Request
```
{
    "description": "AWS FileShare Updated",
    "tags": [
        {
            "key": "Name",
            "value": "ashitfs"
        },
         {
            "key": "Deapartment",
            "value": "Testing"
        }
    ],
    "metadata": {
        "ThroughputMode": "provisioned",
        "ProvisionedThroughputInMibps": 1
    }
}
```
Response
```
{
    "id": "5ef1d9e0202b510001c3804b",
    "createdAt": "2020-06-23T16:00:56",
    "updatedAt": "2020-06-23T16:58:43",
    "name": "ashitfs",
    "description": "AWS FileShare Updated",
    "tenantId": "94b280022d0c4401bcf3b0ea85870519",
    "userId": "558057c4256545bd8a307c37464003c9",
    "backendId": "5ef1c74be170650001f2d030",
    "backend": "aws-backend-file",
    "size": 6144,
    "status": "available",
    "tags": [
        {
            "key": "Deapartment",
            "value": "Testing"
        },
        {
            "key": "Name",
            "value": "ashitfs"
        }
    ],
    "metadata": {
        "fields": {
            "CreationTimeAtBackend": {
                "Kind": {
                    "StringValue": "2020-06-23T10:30:52Z"
                }
            },
            "CreationToken": {
                "Kind": {
                    "StringValue": "CEX5a/)I]#nNP^=V~OH@r7KnBwTW=0!yyJ[}"
                }
            },
            "FileSystemId": {
                "Kind": {
                    "StringValue": "fs-a4e26c75"
                }
            },
            "FileSystemSize": {
                "Kind": {
                    "StructValue": {
                        "fields": {
                            "Timestamp": {
                                "Kind": {
                                    "NumberValue": 0
                                }
                            },
                            "Value": {
                                "Kind": {
                                    "NumberValue": 6144
                                }
                            },
                            "ValueInIA": {
                                "Kind": {
                                    "NumberValue": 0
                                }
                            },
                            "ValueInStandard": {
                                "Kind": {
                                    "NumberValue": 6144
                                }
                            }
                        }
                    }
                }
            },
            "LifeCycleState": {
                "Kind": {
                    "StringValue": "available"
                }
            },
            "Name": {
                "Kind": {
                    "StringValue": "ashitfs"
                }
            },
            "NumberOfMountTargets": {
                "Kind": {
                    "NumberValue": 0
                }
            },
            "OwnerId": {
                "Kind": {
                    "StringValue": "676599967111"
                }
            },
            "PerformanceMode": {
                "Kind": {
                    "StringValue": "generalPurpose"
                }
            },
            "ProvisionedThroughputInMibps": {
                "Kind": {
                    "NumberValue": 1
                }
            },
            "ThroughputMode": {
                "Kind": {
                    "StringValue": "provisioned"
                }
            }
        }
    }
}
```

###### Example 2: Updating File Share size = 2147483648 for Azure Backend

```
[PUT] /v1/<tenantId>/file/shares/5ef1de68202b510001c3804f
```
Request
```
{
	"description": "Azure FileShare Updated",
	"size": 2147483648
}
```
Response
```
{
    "id": "5ef1de68202b510001c3804f",
    "createdAt": "2020-06-23T16:20:16",
    "updatedAt": "2020-06-23T17:03:02",
    "name": "sanilfs",
    "description": "Azure FileShare Updated",
    "tenantId": "94b280022d0c4401bcf3b0ea85870519",
    "userId": "558057c4256545bd8a307c37464003c9",
    "backendId": "5ef1c6fce170650001f2d02f",
    "backend": "azure-backend-file",
    "size": 2147483648,
    "status": "available",
    "metadata": {
        "fields": {
            "Content-Length": {
                "Kind": {
                    "NumberValue": 0
                }
            },
            "Date": {
                "Kind": {
                    "StringValue": "Tue, 23 Jun 2020 11:33:02 GMT"
                }
            },
            "Etag": {
                "Kind": {
                    "StringValue": "0x8D817693045AD4A"
                }
            },
            "Last-Modified": {
                "Kind": {
                    "StringValue": "Tue, 23 Jun 2020 11:33:02 GMT"
                }
            },
            "Server": {
                "Kind": {
                    "StringValue": "Windows-Azure-File/1.0 Microsoft-HTTPAPI/2.0"
                }
            },
            "Share-Usage-Bytes": {
                "Kind": {
                    "NumberValue": 0
                }
            },
            "X-Ms-Client-Request-Id": {
                "Kind": {
                    "StringValue": "41149ea8-ca08-417d-7401-17fcc7f9c2ca"
                }
            },
            "X-Ms-Has-Immutability-Policy": {
                "Kind": {
                    "StringValue": "false"
                }
            },
            "X-Ms-Has-Legal-Hold": {
                "Kind": {
                    "StringValue": "false"
                }
            },
            "X-Ms-Meta-Createdby": {
                "Kind": {
                    "StringValue": "himanshu varshney"
                }
            },
            "X-Ms-Meta-Email": {
                "Kind": {
                    "StringValue": "himanshuvar@gmail.com"
                }
            },
            "X-Ms-Meta-Organization": {
                "Kind": {
                    "StringValue": "soda foundation"
                }
            },
            "X-Ms-Request-Id": {
                "Kind": {
                    "StringValue": "160ccda7-c01a-0158-5652-495997000000"
                }
            },
            "X-Ms-Share-Quota": {
                "Kind": {
                    "NumberValue": 2
                }
            },
            "X-Ms-Version": {
                "Kind": {
                    "StringValue": "2019-02-02"
                }
            }
        }
    }
}
```

#### Delete a File Share
```
[DELETE] /v1/<tenantId>/file/shares/<id>
```
##### Request
```
  Path Parameter ->  “id” : “<fileShareId>”
```
###### Response
```
    {}
```

###### Example: Deleting File Share with id = 5ef1de68202b510001c3804f
```
[DELETE] /v1/<tenantId>/file/shares/5ef1de68202b510001c3804f
```

Response
```
    {}
```


### Build & Package
#### Deployment
New  containerized File Service added

## Sequence Diagrams

```
![](https://lh4.googleusercontent.com/bnHSB2cHi93PKGMgVNuHWrbrPK-aM2499n5UaLOe6N1a90MxawWcvloSOz6YaTFIQzQ80N9AGpsiHj4v9d61xwzujYS_TD0NePgMk3-XpQc3L-MCmQ7BC9JBxl1MginCVbxXMN09)
![Sequence Backend Registration](resources/BackendRegistration.png)


#### Fileshare API:
```
/v1/<tenant_id/file/shares [GET]
```

![](https://lh5.googleusercontent.com/IEupPA2vy72VDmZjjXfPwc14gvREkFB4Fj_sniW1P0idP1o8gB9EfAcf5aw_a8LnJWX8LvtGgb8hn8ZGAwzAw4UIFuE1MguKZgwk6hFPmia5hK-NRvsCYjvZ0ECAEKsTf37FbOtw)
![Sequence File list](resources/MultiCloudServiceflow.png)


## Notes

### Response of AWS Create File Share:

    {

        CreationTime: 2020-06-03 10:46:16 +0000 UTC,

        CreationToken: "tokenstring",

        Encrypted: false,

        FileSystemId: "fs-4ffe7d9e",

        LifeCycleState: "creating",

        Name: "aks_fs1",

        NumberOfMountTargets: 0,

        OwnerId: "75027",

        PerformanceMode: "generalPurpose",

        SizeInBytes: {

        Value: 0,

        ValueInIA: 0,

        ValueInStandard: 0

        },

        Tags: [{

        Key: "Name",

        Value: "aks_fs1"

        }],

        ThroughputMode: "bursting"

    }



### Response of AWS List File Shares:

    {
        FileSystems: [{
        CreationTime: 2020-06-03 10:46:16 +0000 UTC,
        CreationToken: "tokenstring",
        Encrypted: false,
        FileSystemId: "fs-4ffe7d9e",
        LifeCycleState: "available",
        Name: "aks_fs1",
        NumberOfMountTargets: 0,
        OwnerId: "75027",
        PerformanceMode: "generalPurpose",
        SizeInBytes: {
        Value: 6144,
        ValueInIA: 0,
        ValueInStandard: 6144
        },
        Tags: [{
        Key: "Name",
        Value: "aks_fs1"
        }],
        ThroughputMode: "bursting"
        },{
        CreationTime: 2020-05-28 06:11:14 +0000 UTC,
        CreationToken: "console-0ed22229",
        Encrypted: false,
        FileSystemId: "fs-d97bfa08",
        LifeCycleState: "available",
        Name: "aks_efs",
        NumberOfMountTargets: 3,
        OwnerId: "75027",
        PerformanceMode: "generalPurpose",

        SizeInBytes: {
        Timestamp: 2020-06-03 11:05:32 +0000 UTC,
        Value: 6144,
        ValueInIA: 0,
        ValueInStandard: 6144
        },

        Tags: [{
        Key: "Name",
        Value: "aks_efs"
        }],

        ThroughputMode: "bursting"
        }]
    }
