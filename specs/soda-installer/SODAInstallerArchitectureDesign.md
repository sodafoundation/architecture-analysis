# SODA Installer Architecture Design

Author(s): [Sanil Kumar](https://github.com/skdwriting) ; <please add here>

## Introduction
SODA Installer is a project for the overall SODA Installer of every SODA release. Each SODA Release will integrate multiple SODA Projects to provide integrated use cases and features for the users of SODA Releases. Hence, SODA Installer will be the master installer through which the complete setup of SODA Release happens. It needs to work with individual project installers, provide guidelines for individual project installers, and streamline and enhance the overall installation experience of SODA Release.

## Motivation and background
Currently, SODA installer based on ansible is directly using ansible-playbook with limited options like install and clean. Many other supported installation methods are not really maintained and managed, like salt etc.

All the installation methods from soda foundation for soda releases and its documentations are scattered.

In this improvement proposal, trying to make a simple and unified install interface for SODA Releases considering all the install methods supported.

Also post installation management facilities can be provided through the same tools.

## Goals

-   Enhance and simplify the installation for SODA Release
-   Improve the user experience
-   Consolidate all the installation documentations and methods supported in SODA for SODA Release
-  Manage all the dependencies, prerequisites etc
-   Post installation system monitoring/logs etc.
- Provide configuration options
- Seamless end to end installation of SODA Release
- Tools and methods to debug
- Instal fail recovery, restart, optimized downloads
- Upgrade, Rollback and so on.

This architecture design can consider all different options for installer:
- GUI based
- Commandline based
- Different types of installations (Autoinstall, quick install, dev install etc)
- Different ways/tools (ansible, helm, or any such better options)


### Non-Goals

-   Project Level installation and its configuration (Each component installation to be handled by the specific installer of the project or components)
- This architecture design will give the guidelines
    

### Assumptions and Constraints

- Each SODA Project to follow SODA Installer guidelines from this document. Otherwise, certain features of this installer may not work as needed.
- The implementation can be in phased manner. Each phase needs to provide valuable e2e features of SODA installer


### Requirement Analysis

### Input Requirements (need to refine more)

-   Single command-line interface for soda release installation
    
-   Reasonable control on the installation configurations
    
-   Installation log
    
-   History information for last 5 installations on the same system
    
-   precheck, post-install check supported
    
-   Post installation management/monitoring (Minimum features)
    

### Feature Requirements

#### Requirement Analysis

Provide the input requirements in cli

#### List of Requirements

##### Functional Requirements

-   Support installation of all or specific projects
    
-   Support uninstallation of all or specific projects
    
-   Start and stop services
    
-   Provide logging
    
-   Provide configuration
    
-   Each installation methods can be integrated
    
-   Separate clean (delete runtime files) and purge (delete install downloaded files etc = full clean without any trace!)
    
-   Trace installation (last 5 installation information for the same system)
    
-   Start / Stop services
    
-   Upgrade? (currently not considered below) -- TBD
    
-   Custom install path? (currently not considered below) - TBD
    
-   Pre / post-install checks
    
-   Support post install logs
    
-   Support post install management (basic)
    

##### Non Functional Requirements

Performance Requirements

-   Installation time < All selected Project installation time + 30 seconds
- Optimized downloads
    

Security Requirements

NA

Other Non-Functional Requirements (Scalability, HA etc…)

-   Easy to add new project installation support
    
-   Easy to add new installation methods
- Upgrade
- rollback
- fail recovery
- restart recovery
- Incremental installation
    

## Architecture Analysis
### Ovearll Architecture
**SODA Installer**
| *[Common Config, Project MetaData, Utils]*
|--> Project 1 Installer
|--> Project 2 Installer
|--> Project n Installer

(Diagram will be added)


### Module Architecture

### High Level Module View

### Architecture Tenets

-   Unified single cli interface for SODA Release installation
-   Can support different installation methods
-   Extensible to add new installation methods
-   Extensible to add new project installations
-   Traceable installations (5 installation history)

### High Level Module Architecture

Provides a wrapper install cli which inturn calls the specific project installation hooks/scripts. The various specific install steps need to be supported by each project installation are:

-   precheck: Project installation dependency check-precheck : Checks the depependency readiness for the installation based on the configuration file
    
-   install: Checks the prerequisite and install all the packages of the project (based on the project configuration file)
    
-   uninstall: Uninstall all the packages of the projects ( based on the project configuration file)
    
-   clean: Uninstall and clean up the files created for all the installation and run time of the components. (db, other run time files etc)
    
-   purge: clean and delete all the downloaded files and install setup files. Caution!!
    
-   chkinstall: Checks the last installation and provides a summary of project installations
    
-   Info format guidelines:
-   Installation Time: < date and time - long string>
-   Project Name:
-   Project Version:
-   Packages Installed:
-   Config File :
-   Log File :

-   services-<tag> <param>:

-   list: List of services part of the installer and its status
-   stop: Stop all services Or stop the specific service
-   Example:
    -   services-stop docker: stops docker service
    -   start: Start all services or start specific service
    
-   Example:
    -   services-start docker: starts docker service
    

## Detailed Design

As above

### Use case View

#### List of Typical Usecases

-   Quick Install
	- For Users
	- For Developers
- Normal Install
	- For Users
	- For Developers
- Custom Installation
	- For Developers
- Auto Install
	- Users (Like auto cloud setup for experiencing)
- Usecase Install
	- Specific Use case level install
- Light install
	- Resource constraint situations
	- Demo cases
- Upgrade and Roll Back
	- Overall SODA Version
	- Only project version (compatible) ?
- Incremental installation
   
#### Usecase context model

NA

#### Interface Model

NA

External Interfaces

CLI as in the help

#### End User Context

Can use it within any methods where it can trigger cli

### Functional Context

SODA Platform Manager Tool “sodapmt” is the name for the top-level installer cum management tool for soda releases. It will be high level wrapper which will do some bookkeeping and primarily call the respective hooks or scripts from components included in the soda release.

The tool name:

sodapmt

sodapmt --help

sodapmt -<install-method>* -<install-options>* -<install-params> -c <config-file> -l <log-file> / --nolog


Notes on sodapmt

-----------------

* indicates mandatory

commandline options overrides the config-file

creates an installer info file (current-folder/sodapmt-instinfo.info)based on which the install summary and history can be tracked up to last 5 installations.

This cannot be configured.

sodapmt will have a version number. Will be available within sodapmt-instinfo.info

Future: sodapmt information can be encrypted?!

Description:

--help :

Provides the help. From the help the user can get all the supported methods.

[Gives the currently supported install methods to be used as <install-method>]

Example:
Supported Methods (you can use one of these as -<install-method>; default -ansible
-ansible
-salt

-<install-method>* : (default: -ansible)

Use any of the supported installation method. --help will get the list of supported installation methods

-<install-options>*

-precheck : Checks the depependency readiness for the installation based on the configuration file

-install : Checks the prerequite and install all the packages based on the configuration file

-uninstall: Uninstall all the packages based on the configuration file

-clean: Uninstall and clean up the files created for all the installation and run time of the components.

-purge: clean and delete all the downloaded files and install setup files. Caution!!

-chkinstall: Checks the last installation and provides a summary of last installation on the system
 
-c <config-file>

Non default config file input

Default : Current folder/configure_sodainst.cfg
 

-l <log-file> [Default: Current folder/log_sodainstall.log OR path from the config-file]

Non default logfile input (overrides the config-file)

By default the log will be created.

If you do not want to log - disable in config-file or use --nolog in the command line

### Non Functional Context

#### Performance

Slow network etc. Hence, we need to ensure less network or resource as much possible.

#### Security

NA

#### Other Non Functional Aspects

Extensible to add install methods and new projects

### Data View

#### Data and Control Data Contexts

NA

#### Data Model

NA

### Development and Deployment Context

#### Code

Bash script OR python?

Running on Ubuntu 16.04, 18.04 or on the project supported platforms (challenge)

#### Debug Model

#### Logging, Install summary information

#### Build & Package

TBD

Can we have some package manager ?

#### Deployment

Download, extract and run!

### Execution View

Create the information file created with relevant installation information

## Sequence Diagrams

NA

## Design Alternatives and other notes

NA

## Open Issues

NA

## Design Requirements / Tasks

NA

## Scratchpad

Some useful references:

**Team Osive Inputs:** 
- https://openitems.soda.osive.com/  
- https://www.figma.com/file/BdqdGjZYTunmSG2mUUPdRH/Figma-Admin-Dashboard-UI-Kit-Community?node-id=0%3A1  
this is a very simple UI we wanted guidance on this Design approach  

**From https://github.com/noelmcloughlin** 
- https://www.data-infrastructure.eu/  
- https://www.data-infrastructure.eu/#id1854484  
- I have a kubernetes deployer in travis CI: https://travis-ci.com/github/saltstack-formulas/kubernetes-formula/builds/190837333  
- This is Developer Experience project I maintain: https://github.com/saltstack-formulas/salter#saltstack-formulas-namespace

**From SODA:**  
https://docs.sodafoundation.io/soda-gettingstarted/quickstart/  


