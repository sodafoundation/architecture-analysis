# Welcome to StackEdit!

Hi! I'm your first Markdown file in **StackEdit**. If you want to learn about StackEdit, you can read me. If you want to play with Markdown, you can edit me. Once you have finished with me, you can create new files by opening the **file explorer** on the left corner of the navigation bar.


# Files

StackEdit stores your files in your browser, which means all your files are automatically saved locally and are accessible **offline!**

## Create files and folders

The file explorer is accessible using the button in left corner of the navigation bar. You can create a new file by clicking the **New file** button in the file explorer. You can also create folders by clicking the **New folder** button.

## Switch to another file

All your files and folders are presented as a tree in the file explorer. You can switch from one to another by clicking a file in the tree.

## Rename a file

You can rename the current file by clicking the file name in the navigation bar or by clicking the **Rename** button in the file explorer.

## Delete a file

You can delete the current file by clicking the **Remove** button in the file explorer. The file will be moved into the **Trash** folder and automatically deleted after 7 days of inactivity.

## Export a file

You can export the current file by clicking **Export to disk** in the menu. You can choose to export the file as plain Markdown, as HTML using a Handlebars template or as a PDF.


# Synchronization

Synchronization is one of the biggest features of StackEdit. It enables you to synchronize any file in your workspace with other files stored in your **Google Drive**, your **Dropbox** and your **GitHub** accounts. This allows you to keep writing on other devices, collaborate with people you share the file with, integrate easily into your workflow... The synchronization mechanism takes place every minute in the background, downloading, merging, and uploading file modifications.

There are two types of synchronization and they can complement each other:

- The workspace synchronization will sync all your files, folders and settings automatically. This will allow you to fetch your workspace on any other device.
	> To start syncing your workspace, just sign in with Google in the menu.

- The file synchronization will keep one file of the workspace synced with one or multiple files in **Google Drive**, **Dropbox** or **GitHub**.
	> Before starting to sync files, you must link an account in the **Synchronize** sub-menu.

## Open a file

You can open a file from **Google Drive**, **Dropbox** or **GitHub** by opening the **Synchronize** sub-menu and clicking **Open from**. Once opened in the workspace, any modification in the file will be automatically synced.

## Save a file

You can save any file of the workspace to **Google Drive**, **Dropbox** or **GitHub** by opening the **Synchronize** sub-menu and clicking **Save on**. Even if a file in the workspace is already synced, you can save it to another location. StackEdit can sync one file with multiple locations and accounts.

## Synchronize a file

Once your file is linked to a synchronized location, StackEdit will periodically synchronize it by downloading/uploading any modification. A merge will be performed if necessary and conflicts will be resolved.

If you just have modified your file and you want to force syncing, click the **Synchronize now** button in the navigation bar.

> **Note:** The **Synchronize now** button is disabled if you have no file to synchronize.

## Manage file synchronization

Since one file can be synced with multiple locations, you can list and manage synchronized locations by clicking **File synchronization** in the **Synchronize** sub-menu. This allows you to list and remove synchronized locations that are linked to your file.


# Publication

Publishing in StackEdit makes it simple for you to publish online your files. Once you're happy with a file, you can publish it to different hosting platforms like **Blogger**, **Dropbox**, **Gist**, **GitHub**, **Google Drive**, **WordPress** and **Zendesk**. With [Handlebars templates](http://handlebarsjs.com/), you have full control over what you export.

> Before starting to publish, you must link an account in the **Publish** sub-menu.

## Publish a File

You can publish your file by opening the **Publish** sub-menu and by clicking **Publish to**. For some locations, you can choose between the following formats:

- Markdown: publish the Markdown text on a website that can interpret it (**GitHub** for instance),
- HTML: publish the file converted to HTML via a Handlebars template (on a blog for example).

## Update a publication

After publishing, StackEdit keeps your file linked to that publication which makes it easy for you to re-publish it. Once you have modified your file and you want to update your publication, click on the **Publish now** button in the navigation bar.

> **Note:** The **Publish now** button is disabled if your file has not been published yet.

## Manage file publication

Since one file can be published to multiple locations, you can list and manage publish locations by clicking **File publication** in the **Publish** sub-menu. This allows you to list and remove publication locations that are linked to your file.


# Markdown extensions

StackEdit extends the standard Markdown syntax by adding extra **Markdown extensions**, providing you with some nice features.

> **ProTip:** You can disable any **Markdown extension** in the **File properties** dialog.


## SmartyPants

SmartyPants converts ASCII punctuation characters into "smart" typographic punctuation HTML entities. For example:




## KaTeX

You can render LaTeX mathematical expressions using [KaTeX](https://khan.github.io/KaTeX/):

The *Gamma function* satisfying $\Gamma(n) = (n-1)!\quad\forall n\in\mathbb N$ is via the Euler integral

$$
\Gamma(z) = \int_0^\infty t^{z-1}e^{-t}dt\,.
$$

> You can find more information about **LaTeX** mathematical expressions [here](http://meta.math.stackexchange.com/questions/5020/mathjax-basic-tutorial-and-quick-reference).


## UML diagrams

You can render UML diagrams using [Mermaid](https://mermaidjs.github.io/). For example, this will produce a sequence diagram:

```mermaid
sequenceDiagram
Alice ->> Bob: Hello Bob, how are you?
Bob-->>John: How about you John?
Bob--x Alice: I am good thanks!
Bob-x John: I am good thanks!
Note right of John: Bob thinks a long<br/>long time, so long<br/>that the text does<br/>not fit on a row.

Bob-->Alice: Checking with John...
Alice->John: Yes... John, how are you?
```

And this will produce a flow chart:

```mermaid
graph LR
A[Square Rect] -- Link text --> B((Circle))
A --> C(Round Rect)
B --> D{Rhombus}
C --> D
```

# Kadalu
## Introduction:
Kadalu is an open source project that provides storage solutions for Kubernetes.

It aims to provide highly scalable and reliable storage for containerized applications running on a Kubernetes cluster. Kadalu integrates with existing storage solutions, such as GlusterFS, to provide a unified storage system that can be easily managed and scaled.

The project's main goal is to simplify storage management for Kubernetes users and provide a flexible and scalable solution for their storage needs.

  
  

## Project Summary:

|||
|-------------------------------|-----------------------------|
|Website|https://kadalu.tech/|
|Organization / Foundation Name|Kadalu Technologies|
|License |Kadalu Technologies|
|Open / Proprietary|Open Source|
|Source Path (if open source)|https://github.com/kadalu/kadalu.git|
|Brief Description|Open-source project that aims to provide persistent and scalable storage solutions for containers and virtual machines on Kubernetes|

  
  

## Project Details

### Key Features:
    

  

-   Persistent Volume (PV) and Persistent Volume Claim (PVC) management for stateful applications.
    
-   Support for multiple storage backends, including GlusterFS, NFS, and iSCSI.
    
-   Data migration between different storage backends and cluster nodes.
    
-   Automatic data tiering and data replication to ensure high availability and data durability.
    
-   Dynamic provisioning of storage volumes for stateful applications.
    
-   Integration with Kubernetes for easy management and orchestration of storage resources
    
-   Support for advanced storage features like snapshots, quotas, and backups.
    

  
  
  
  
  
  
  
### Architecture:
    
![architecture diagram](https://ibb.co/HKthbr4)

The architecture of Kadalu consists of three main components:

-   GlusterFS: A scalable, distributed file system that is used to store data.
    
-   Kadalu Daemon: A background process that communicates with GlusterFS and other Kadalu daemons to manage storage.
    
-   Kadalu CLI: A command-line interface that allows users to interact with the Kadalu daemon and manage storage.
    

  

The GlusterFS storage is made up of nodes, which are physical or virtual machines that host the storage. The data is stored on multiple nodes and is automatically replicated for reliability and availability.

  

The Kadalu daemon communicates with GlusterFS and the Kadalu CLI to manage storage, such as creating and deleting volumes, adding and removing nodes, and monitoring storage usage.

  

The Kadalu CLI provides a simple, user-friendly interface for managing storage and interacting with the Kadalu daemon.

  
  
### Technical Details:
    

  

Some of the technical details of Kadalu are:

-   Supports multiple storage backends: Kadalu supports multiple storage backends like GlusterFS, Ceph, NFS and can be extended to support other storage solutions.
    
-   Kubernetes integration: Kadalu integrates with Kubernetes to manage storage volumes and provide persistent storage for containers.
    
-   Snapshot and Backup: Kadalu provides snapshot and backup functionality to enable recovery from data loss and data protection.
    
-   Data tiering: Kadalu supports data tiering that helps to manage the data life-cycle by moving data between different storage tiers.
    
-   Scalability: Kadalu is designed to scale horizontally to accommodate growth in storage needs.
    
-   Performance: Kadalu provides high performance and low latency storage access to containers.
    
-   Open Source: Kadalu is open source software released under the Apache License 2.0.
