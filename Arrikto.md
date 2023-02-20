
![](https://lh5.googleusercontent.com/7Ob6-1Oh2t0rz5lWQLXbnbsYgtlPy9AUD-0SEIIPDIkRWtwd981V_teK3Bei5iVlQcpAB6j62D1CjMTcit4nuoq8kCptsQmzXLUf6JsN-cWN8eLBFFspgtowJ8bvD6HWe-Xqj9BQLn__PJSdrD52ibM)

INTRODUCTION :

  

Arrikto is a complete machine learning platform that simplifies, accelerates, and secures model development through production.

Today, organizations are embarking on digital initiatives to transform the customer experience and unlock new revenue streams. To operationalize these efforts, DevOps continuous workflows and cloud native patterns have been widely adopted to manage code that is being deployed at scale. But while software may be eating the world, it is the data that is defining market leadership (think Uber and taxis, Airbnb and hospitality, or Amazon and retail). How efficiently data is managed and how well it is modeled and leveraged into machine learning algorithms now determines the size of the ‘competitive moat’ a leader has in their industry.

![](https://lh5.googleusercontent.com/V7qu6YBZHwRpqEUGMxiWtnkx-JsU53pXm_xICxLPHZu1v16t53PzUKnnXsYjlzUx32vNRITXH5U7gXgFtwVWOAJn0kDFl4RqQjRh4iLqIHZIeNBFiX-HqKg-2oSH2EfFVXnocvOQjal4rts1cQx5ooI)

  
| WEBSITE :- |  https://www.arrikto.com/ |
|--|--|
| ORGANISATION /FOUNDATION NAME :- | Constantinos Venetsanopoulos |
| LICENSE :- | Arrikto inc.Software end user |
| OPEN / Proprietary | OPEN SOURCE PROJECT |
| Brief description | Arrikto, unlike other MLOps companies, enables MLOps teams to accelerate machine learning models to market 30-times faster than traditional ML platforms. As a leading Kubeflow contributor, Arrikto provides automated workflows, reproducible pipelines, consistent deployment from desktop to cloud, and secure access to data. Arrikto’s Enterprise Kubeflow is available as a multi-node distribution on AWS, GCP, and Azure, and is the preferred MLOps platform used in production today by many Fortune 500 enterprises. The company has over 300 customers across 17 countries and is venture backed by Unusual Ventures and Odyssey VP. |



## Project Summary:
### Project Details

  

KEY FEATURES :

  

-   Overview.
    
-   Iterate on ML Code without Creating New Docker Images.
    
-   Deploy Pipelines Directly from Your Code.
    
-   Run Pipelines from Your IDE.
    
-   Kale Automatically Manages Dependencies.
    
-   Snapshot and Reproduce Pipelines and Steps.
    
-   Tune Hyperparameters.
    
-   Serve Models.
    

  
  

### Iterate on ML Code without Creating New Docker Images

The OSS Kubeflow pipelines platform enables you to define complex workflows in production environments. However, using this platform requires a level of engineering expertise beyond that of most data scientists. OSS Kubeflow requires you to containerize all pipeline components, typically this means that ML Engineers must refactor data scientist’s original code for production. OSS Kubeflow also requires you to define pipelines using the platform’s domain-specific language (DSL). Kale overcomes these obstacles with an experience designed to support data scientists.

Kale enables data scientists to experiment fast and iteratively as they are accustomed to doing in an IDE such as a Jupyter Notebook. Data scientists can then easily orchestrate their workflow using the power and scalability of Kubernetes when ready to do so.

  

### Deploy Pipelines Directly from Your Code

Kale automates creating Kubeflow pipelines, enabling users to move from rapidly prototyping their models to creating reusable and scalable pipelines easily. Jupyter Notebook users can simply label cells in a notebook to identify pipeline steps. Those using other IDEs can use Python decorators provided by the Kale SDK to identify the functions that implement steps of a pipeline.

![](https://lh3.googleusercontent.com/Cb4utm-BwWP2lSjxwmX3NzM_MGN4L9cFHFox721LgWislezzyAK2jDZyun-dTBkB5VWhrHr6rF9vpX5w1Oy2edFhCriwiHc6iEjDSDVbSwbPAzA-v8r3DvzWlcXxH-Un_OQ-4k82xzvD5ayT7WQ6wog)

  

Based on the annotations you supply, Kale

-   Assigns code to specific pipeline components.
    
-   Defines the (execution) dependencies between them.
    
-   Defines the pipeline using lightweight components in the Kubeflow pipelines SDK.
    

Kale ensures that all the processing building blocks are well organized and independent from each other, while also leveraging on the experiment tracking and workflows organization provided out-of-the-box by OSS Kubeflow.

### Run Pipelines from Your IDE

Kale simplifies running pipelines by providing the ability to launch pipelines directly from the Jupyter Notebooks IDE. Once run, you can view your run as a graph and run experiments to compare the results of different pipeline runs as usual in Kubeflow.

### Kale Automatically Manages Dependencies

Kale makes sure that all the newly installed libraries will find their way into pipeline runs. Kale uses the Arrikto EFK Rok component, described below, to implement dependency management. Normally, you should create a new Docker image to be able to run your code as a Kubeflow pipeline, to include newly installed libraries. Fortunately, Rok and Kale make sure that any libraries you install during development will find their way to your pipeline, thanks to Rok’s snapshotting technology and Kale mounting those snapshotted volumes into the pipeline steps.

As a user, you simply iterate on your Python code as usual, importing libraries, declaring variables, creating objects, etc. To ensure that each independent pipeline step has access to the data and other dependencies it requires, Kale runs a series of static analyses over the source Python code to detect where variables and objects are first declared and then used. In this way, Kale creates an internal graph representation describing the data dependencies between the pipeline steps.

Kubeflow pipeline components are necessarily independent to facilitate efficient resource usage and scaling. To enable you to share dependencies from one component to another, Kale uses the dependency graph to determine what objects must be serialized and shared between components. Kale does all this automatically for you by injecting code at the beginning and end of each component to marshal objects into a shared Kubernetes Persistent Volume Claim (PVC) during execution.

### Snapshot and Reproduce Pipelines and Steps

One of the most valuable features of Kale is that it snapshots each run of a pipeline and, more importantly, the start and end of the execution of each pipeline component. Kale uses Rok (see below for an overview) to handle data versioning. Rok snapshots your whole pipeline execution environment when you run a pipeline using Kale.

![](https://lh3.googleusercontent.com/OCP0H6UB83NYKduAvm_8YC_XR9EhOgb-fUZMVF8qOvtHcHHd8yT18vSbCLspcmHids7ritlVyGihN87ql2YrA3TFyWRr-riIC7k1Yskx4CGpopXe-k3YjSwTmt-7IenfjSPm5m_XvQjvB5T2S-NZFL0)

### Tune Hyperparameters

Kale enables you to optimize models using hyperparameter tuning. Kale uses Katib, Kubeflow’s hyperparameter tuning component, for these jobs. Kale will orchestrate Katib and Kubeflow pipeline experiments so that every Katib trial is a pipeline run in Kubeflow Pipelines.

### Serve Models

Serving models via the KServe component in OSS Kubeflow requires learning the KServe SDK and likely requires intervention from ML engineers. With Kale on Enterprise Kubeflow, data scientists can serve models by running a simple command using the Kale SDK. Kale recognizes the type of the model, dumps it locally, then takes a Rok snapshot and creates an inference service via KServe.

Model serving in Arrikto EKF helps address automation gaps in OSS Kubeflow.

## Rok

Rok provides a data management layer for Kubeflow that provisions Persistent Volume Claims (PVCs) on pods in a way that enables directly-attached, local NVMe devices to become primary, persistent storage. Rok is a generic storage and data management solution that provides a StorageClass for Kubernetes and integrates at the Kubernetes level via the Container Storage Interface (CSI). It is a standalone solution that can be used Run Pipelines on High Performance NVMe Devices

Rok integrates via the Kubernetes Container Storage Interface (CSI). It is in the critical control path of provisioning and managing volumes; however, it is not in the critical path of I/O. All I/O flows directly via the Linux kernel. Rok uses mainline kernel utilities to provision what eventually will be a kernel block device provided to the pod. This is fast, because all I/O requests issued by the pod will enter the Linux kernel, get processed by kernel code only, and will eventually get served by the NVMe device, which is directly-attached to the same physical host.

### View and Manage Files in PVCs

Arrikto EKF via Rok enables you to manage files inside PVCs using a convenient file manager UI. Using the file manager, you can browse, upload, download, and delete files a volume contains. This includes files created by services attached to the volume such as those created within a Jupyter Notebooks environment. The EKF file manager UI requires no Kubernetes knowledge. It is ideally suited for data scientists and anyone else who needs to manage files in your clusters.

### Package Your Environment with Snapshots

Rok enables you to create snapshots of local persistent volumes. In Arrikto EKF, this includes volumes containing ML pipeline code and data, JupyterLab environments, datasets, models, and other artifacts or data in your deployment. Taking snapshots does not impact application performance, because it happens outside the application I/O path using Arrikto’s Linux kernel enhancements, which are now part of mainline Linux.

![../_images/rok-local-persistent-volumes.png](https://lh6.googleusercontent.com/IDaOLCCzn7bnayWt-OPcpLdghUnT9oaJvc8YFirdS5TaEmBmhrzsIUWK3V6JYi0jVl45UjKLldnJsL4ZEpqsjhvA3AmlnE4bNiCG3YfLybTeKKFO0KHndusl1_oJQeRY0Ivl1ZtZiwdfTqUxTLad0gg)

Rok hashes, de-duplicates, and versions snapshots and then stores the de-duplicated, content-addressable parts on an Object Storage service (e.g., Amazon S3) that is close to the specific Kubernetes cluster.

![../_images/rok-version-environment.png](https://lh3.googleusercontent.com/RLa-OVUaVDx8J1tTLXhnleAIes-EqkNzV90xjgRaGGLOYWy9d8WDNRDJcyoxxpLlx8PSdP5BCAL7_yrX2a7TdEWkXHPBms8lrbb4gwSYSfHiU_fSJnKPw-IV2AjbPMqUw43gamFxIHM76m3uJefeIIw)

### Schedule Snapshots and Define Retention Policies

You may define a variety of policies on volume snapshots using Rok. These include the following:

-   The schedule on which snapshots are taken and which resources in a volume should be included in a snapshot.
    
-   Retention policies.
    
-   Policies on sharing snapshots.
    

![../_images/rok-restore-across-clusters.png](https://lh5.googleusercontent.com/7nEqZ0Lr8lKaO3AWE7VpZTh19xvBwNYUhM9wne7m-jMuTqYgf5TumgkuBJXFDbbzambmYmTWgQOvn3Lk3rz1ddLHDnXBTZu7Op_pdyRKrqbqCkEWBeZM_raL5c69lxCipfjK6bazlFpKksHPR7rug18)

### Rok Registry

Rok Registry enables you to search, discover, and share snapshots with other users. You can create private or public groups on Rok Registry and can define fine-grained access control lists (ACLs).

  

Rok Registry is to Rok what GitHub is to Git, but decentralized. Rok uses a publish/subscribe model to enable users to share and collaborate across clusters, including clusters running on local environments. Rok Registry enables users to publish buckets containing snapshots of their environment and make them discoverable by other users. Other users with appropriate authorization can subscribe to published buckets. Rok ensures that changes made by a publisher are synced to all those subscribed to the corresponding bucket. Rok captures each published change as a discrete version, enabling you to restore prior versions easily.

ARCHITECTURE :

  

![](https://lh5.googleusercontent.com/DKJCkizE0N8PBTb3-xhYBKohKd-uMLlRcIWtqubnIPmzTUB9QEzjkLYB2uf4_0oBbFZz8LRsN7AnBbz00BdtoIyqS5n9sWNKmOH-iQPumQ8ILx3VQtyO2rptg9uvIvwBjJ0-7Yj7hQTsEICJKe4udOg)

## [Monitoring Targets](https://docs.arrikto.com/user/monitoring/architecture.html#id3)

If you have already deployed Arrikto EKF, then you have also deployed the Rok Monitoring Stack with all its components. In this section we describe how we configure Rok Prometheus to monitor:

### [Physical Nodes](https://docs.arrikto.com/user/monitoring/architecture.html#id6)

To monitor the physical nodes (or cloud VMs) that host Kubernetes and, in turn, applications running on it, we need a way to gather and export critical system metrics in order to expose the overall state regarding CPU usage, memory consumption, disk I/O, network traffic, and other resources.

### [Kubernetes](https://docs.arrikto.com/user/monitoring/architecture.html#id7)

To monitor Kubernetes we need a way to gather and export metrics from core Kubernetes components, such as the Kubernetes API Server, Kubelet, the Kubernetes Scheduler, the Kubernetes Controller Manager, CoreDNS, etc.

  

CURRENT USAGE :

Explore use cases pertaining to retail where machine learning is being used to solve complex business problems:

The retail market has changed globally, requiring retailers to rapidly adapt throughout the value-chain from supply chain, inventory management, consumer buying behaviors, and launching new channels.

To gain a competitive advantage, retailers need to unlock data gathered in all channels and build scalable machine learning models to meet new customer demands and maximize value generated.

  

Explore use cases pertaining to Financial Services where machine learning is being used to solve complex business problems:

  

Financial services institutions are adopting AI/ML solutions to gain a competitive edge in this changing financial services market. Customers are pushing institutions to provide services in all forms of banking with the need of speed. Understanding needs in the marketplace and to refine processes to make quicker decisions smartly allows Financial Services institutions to generate maximum value and retain customers.

  

Explore use cases pertaining to Oil & Gas industry where machine learning is being used to solve complex business problems:

Oil & Gas companies are on the digital journey to retool nearly every aspect of the value chain with sensors that allow companies to collect and store data. Oil & Gas companies are now looking at both structured and unstructured data that is collected and building AI/ML models that analyze data at scale to gain a competitive edge in prediction and optimization while ensuring there is limited downtime.

  

Explore use cases where machine learning is being used to help improve and solve healthcare organizations needs:

Patients’ demands and capacity constraints are forcing provider healthcare organizations to adopt innovative methods with the help of ML and AI. The use of ML and AI have enabled healthcare providers to improve their operational capabilities and provide better solutions and delivery of services in a timely manner while operating in a highly regulated environment. Use of ML and AI has also helped payer organizations in the healthcare industry to improve service levels and improve the overall quality of engagement at scale by having efficiencies in collection, processing and customer service.

  
  

Technical Details:-

Rook runs the Kubernetes slave nodes and uses the disks as specified in the configuration. SSD's used for journaling of the HDD for more and faster IOPS. It creates separate pools only containing SSD's applications which require more IOPS and speed like databases and uses HDD for web applications or backups. For each disk, a pod runs which manages that disk and for each service like a monitor, a metadata server, Rados gateway and monitor a separate pod created.

Rados gateway is for providing object storage. S3 and swift API's are compatible with the Ceph object storage. RGW integrated with LDAP for user management. Rook deployed either in a hyper-converged manner (running in the same node as other applications) or hyperscale by giving the complete node to Ceph services pods only. The hyper-converged solution provides better utilization of resources and reducing infrastructure cost. When running out of storage add more disks and updated the deployment file. For more compute and storage add a new node in the cluster. It creates a storage class based on the pools. It can create separate storage class for SSD, HDD, and filesystem. Then use the storage class according to requirement while provisioning PVC.

Project comparison:-

Ceph

In computing,It is a free-software storage platform, implements object storage on a single distributed computer cluster, and provides interfaces for object-, block- and file-level storage.

Amazon S3

Amazon Simple Storage Service provides a fully redundant data storage infrastructure for storing and retrieving any amount of data, at any time, from anywhere on the web

Google Cloud Storage

Google Cloud Storage allows world-wide storing and retrieval of any amount of data and at any time. It provides a simple programming interface which enables developers to take advantage of Google's own reliable and fast networking infrastructure to perform data operations in a secure and cost effective manner. If expansion needs arise, developers can benefit from the scalability provided by Google's infrastructure.

Azure Storage

Azure Storage provides the flexibility to store and retrieve large amounts of unstructured data, such as documents and media files with Azure Blobs; structured nosql based data with Azure Tables; reliable messages with Azure Queues, and use SMB based Azure Files for migrating on-premises applications to the cloud.

Amazon EBS

Amazon EBS volumes are network-attached, and persist independently from the life of an instance. Amazon EBS provides highly available, highly reliable, predictable storage volumes that can be attached to a running Amazon EC2 instance and exposed as a device within the instance. Amazon EBS is particularly suited for applications that require a database, file system, or access to raw block level storage.

  
  
  

## REFERENCES :

  

[https://www.arrikto.com/](https://www.arrikto.com/) OFFICIAL SITE OF ARRIKTO

[https://docs.arrikto.com/](https://docs.arrikto.com/) KEY FEATURES

[https://portworx.com/](https://portworx.com/) TECHNICAL DETAILS
