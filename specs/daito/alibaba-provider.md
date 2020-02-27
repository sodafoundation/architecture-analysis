# Integration of Alibaba Cloud as a backend in OpenSDS/Multi-cloud
**Author(s)**:  [Click2Cloud Technology Services India Pvt. Ltd.](https://github.com/Click2Cloud)
### Summary
Using this feature, user will be able to do object management mechanism and migration for Alibaba Cloud in multi-cloud .
### Motivation
In existing OpenSDS Gelato project, we don't have any option using which user can manage object management mechanism and migration for Alibaba Cloud. So we adding  alibaba as backend for Object management and migration.
### Goals

* Add support of Alibaba OSS service in OpenSDS Gelato Project.



### Design Details
![picture](alibabacloudbackend.png)

                    Figure: Architecture of Integration of Alibaba Cloud in OpenSDS/Multi-cloud




By adding Alibaba OSS as adapter  in OpenSDS will help users to perform all bucket and object related operations as follow:
* Add Backend for Alibaba OSS (`alibaba-oss` type)
* Create OpenSDS Bucket on Alibaba OSS
* Delete Bucket from Alibaba OSS
* SSE supported
* Upload Object on Alibaba OSS (PUT Object)  
* Download Object from Alibaba OSS (GET Object)
* Support for Multi-part Upload
* Delete Object from Alibaba OSS
* Lifecycle management by Alibaba OSS (Standard, IA, Archive)

| | Tier_1 | Tier_99 | Tier_999 |
|---------------|---------------|---------------|---------------|
| Alibaba OSS | STANDARD | IA | Archive |

* Manage Access control directly from OpenSDS application.
* Also, user will be able to migrate its bucket from any OpenSDS supported public cloud or local storage to Alibaba cloud and vice-versa.

### REST API impact
* ##### Register Backend

Here, user can use ***alibaba-oss*** as ***type*** while registering backend


****POST****: http://127.0.0.1:8089/v1/adminTenantId/backends


***Request Body***
```cassandraql
{  
   "type": "alibaba-oss", 
   "name": "alibababackend", 
   "endpoint": "cn-beijing",
   "region": "oss-cn-beijing.aliyuncs.com",
    "bucketName": "wbtestbuckt",
    "access" : "access",
    "security": "security"
 }  

```
***Response Body***
* ****succeed****

Status Code: 200 ok
```cassandraql
{
 "id": "5bd94a83d84b8000014a1309",
 "name": "alibababackend",
 "type": "alibaba-oss",
 "region": "cn-beijing",
 "endpoint": "oss-cn-beijing.aliyuncs.com",
 "bucketName": "wbtestbuckt",
 "access": "access",
 "security": "security"
}
```
* ##### Create Bucket
****PUT****:http://127.0.0.1:8089/v1/s3/bucketName

***Request Body***
```cassandraql
<CreateBucketConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/"> 
<LocationConstraint>alibababackend</LocationConstraint> 
</CreateBucketConfiguration>
```

***Response Body***
* ****succeed****

Status Code: 200 ok

* ****Failed****

Status Code: 500 Internal Server Error
```cassandraql
<?xml version="1.0" encoding="UTF-8"?>
<Error>
    <Code>InternalError</Code>
    <Message>We encountered an internal error, please try again.</Message>
    <Key></Key>
    <BucketName></BucketName>
    <Resource>/v1/s3/bucket027</Resource>
    <RequestId></RequestId>
    <HostId></HostId>
</Error>
```
* ##### Delete Bucket
****DELETE****:http://127.0.0.1:8089/v1/s3/bucketName

***Response Body***
* ****succeed****

Status Code: 200 ok

* ****Failed****

Status Code: 404 Not found
```cassandraql
<?xml version="1.0" encoding="UTF-8"?>
<Error>
    <Code>NoSuchBucket</Code>
    <Message>The specified bucket does not exist</Message>
    <Key></Key>
    <BucketName></BucketName>
    <Resource>/v1/s3/bucket027</Resource>
    <RequestId></RequestId>
    <HostId></HostId>
</Error>
```
* ##### Upload Object
****PUT****:http://127.0.0.1:8089/v1/s3/bucketName/ObjectName

***Response Body***
* ****succeed****

Status Code: 200 ok

* ****Failed****

Status Code: 404 Not found
```cassandraql
<?xml version="1.0" encoding="UTF-8"?>
<Error><Code>NoSuchBucket</Code>
<Message>The specified bucket does not exist</Message>
<Key></Key><BucketName></BucketName>
<Resource>/v1/s3/bucket026/text.txt</Resource>
<RequestId></RequestId>
<HostId></HostId>
</Error>
```
* ##### Download Object
****GET****:http://127.0.0.1:8089/v1/s3/bucketName/ObjectName

***Response Body***
* ****succeed****

Status Code: 200 ok

* ****Failed****

Status Code: 404 Not found

* ##### Delete Object
****DELETE****:http://127.0.0.1:8089/v1/s3/bucketName/ObjectName

***Response Body***
* ****succeed****

Status Code: 200 ok

* ****Failed****

Status Code: 404 Not found
```cassandraql
<?xml version="1.0" encoding="UTF-8"?>
<Error><Code>NoSuchBucket</Code>
<Message>The specified bucket does not exist</Message>
<Key></Key><BucketName></BucketName>
<Resource>/v1/s3/bucket026/text.txt</Resource>
<RequestId></RequestId>
<HostId></HostId>
</Error>
```

* ##### Create LifeCycle
****PUT****: http://127.0.0.1:8089/v1/s3/bucketName/?lifecycle

***Request Body***
```cassandraql
<LifecycleConfiguration>
<Rule>
  <ID>a1</ID>
  <Filter>
    <Prefix></Prefix>
  </Filter>
  <Status>Enabled</Status>
  <Transition>
    <Days>99</Days>
    <StorageClass>STANDARD_IA</StorageClass>
    <Backend>alibababackend</Backend>
  </Transition>
</Rule>
 </LifecycleConfiguration>
```

***Response Body***
* ****succeed****

Status Code: 200 ok

* ****Failed****

Status Code: 404 Not Found
```cassandraql
<?xml version="1.0" encoding="UTF-8"?>
<Error>
    <Code>NoSuchBucket</Code>
    <Message>The specified bucket does not exist</Message>
    <Key></Key>
    <BucketName></BucketName>
    <Resource>/v1/s3/bucketName/</Resource>
    <RequestId></RequestId>
    <HostId></HostId>
</Error>
```

* ##### Delete LifeCycle
****DELETE****: http://127.0.0.1:8089/v1/s3/bucketName/?lifecycle

***Response Body***
* ****succeed****

Status Code: 200 ok

* ****Failed****

Status Code: 404 Not Found
```cassandraql
<?xml version="1.0" encoding="UTF-8"?>
<Error>
    <Code>NoSuchBucket</Code>
    <Message>The specified bucket does not exist</Message>
    <Key></Key>
    <BucketName></BucketName>
    <Resource>/v1/s3/bucketName/</Resource>
    <RequestId></RequestId>
    <HostId></HostId>
</Error>
```
* ##### Create Plan
****POST****:http://127.0.0.1:8089/v1/adminTenantId/plans

***Request Body***
```cassandraql
{ 
"name": "awstoibm", 
"description": "for test", 
"type": "migration",
 "sourceConn": { 
"storType": "opensds-obj", 
"bucketName": "alibababucket" 
}, 
"destConn": { 
"storType": "opensds-obj",
 "bucketName": "ibmbucket"
 }, 
 "remainSource": true 
}
```
***Response Body***
* ****succeed****

Status Code: 200 ok

```cassandraql

  {  
    "plan": {
        "id": "5e4be32eed90fd0001a70987",
        "name": "awstoibm",
        "description": "for test",
        "type": "migration",
        "sourceConn": {
            "storType": "opensds-obj",
            "bucketName": "alibababucket"
        },
        "destConn": {
            "storType": "opensds-obj",
            "bucketName": "ibmbucket"
        },
        "remainSource": true,
        "tenantId": "tenantId",
        "userId": "userId",
    }
}
```
* ****Failed****
Status Code: 500 Internal server Error

```cassandraql
duplicate name:awstoibm
```

* ##### Run Plan
****POST****: http://127.0.0.1:8089/v1/adminTenantId/plans/5e4be32eed90fd0001a70987/run

* ****succeed****

Status Code: 200 ok

***Response Body***
```cassandraql
 {
      "jobId": "5e4be33eed90fd0001a70988"
  }
```
* ****Failed****

Status Code: 500 Internal server Error
```cassandraql
 Plan does not exist
```

###  Schema impact 
NA

###  Installation impact 
NA

### UI impact
Add Support for Store Type :  ***"alibaba-oss"*** Object Storage.

### References
[Alibaba OSS SDK](https://www.alibabacloud.com/help/doc-detail/32145.htm?spm=a2c63.p38356.b99.308.558b7165BZv8Tp)
