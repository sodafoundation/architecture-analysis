# SODA Installer Architecture Design

Author(s): [Sanil Kumar](https://github.com/skdwriting) ; [Noel McLoughlin](https://github/noelmcloughlin) ;//please add here

## Introduction

SODA Foundation (SF) is an open source project that aims to foster an ecosystem of open source data management and storage software for data autonomy. GAIA-X, a project to develop common requirements for a European data infrastructure, has complmentary goals. Linux Foundation (LF), a sustainable open source ecosystem, hosts some of the world's most important open source projects, including Linux and SODA. The Cloud Native Computing Foundation (CNCF) hosts critical components of the global technology infrastructure. Both SODA and CNCF are LF member projects.[1-4]

SODA Installer is the overall Installer project. Each Release integrates multiple member projects to provide integrated use cases and features for the users of the Releases. Hence, SODA Installer will be the master installer for SODA Foundation.  The current SODA installer leverages Ansible, Bash, Helm and YAML models. There is some unit test (UT), continious integration (CI), and continious deployment (CD) coverage.[5]

## Motivation & background
Currently, the SODA installer based on Ansible playbooks with limited options like install and clean. Other installation methods (Salt and Helm) are not clearly supported or maintained. All installation methods and documentation are scattered.  Three type of software package must be handled-

  1. SODA member projects deliver "installers" as scripts/ansible, with no shared model.
  2. CNCF projects deliver "releases" as tarballs, with no common model exposed for installers.
  3. Other projects deliver "packages" (i.e. docker, vagrant, image, yum, zypper, apt, ansible, salt, tarball).

In this improvement proposal, we propose a simple and unified install interface for SODA Releases.

## Goals

- Make SODA release adoption simple.
- Improved user experience.
- Consolidate all installation methods and documentation across member projects.
- Deliver seamless end to end installation of member project, and SODA releases.
- Tools and method to troubleshoot installation.
- Upgrade and Rollback with Data Protection.
- Commandline, and possibly Graphical User, interface.
- Different types of installations (developer, end user).
- Effective and coherent technology (ansible, helm, or any such better options).
- Model-Driven installation and upgrade (software/configuration).

### Non-Goals

- Project level installation and its configuration (scale up).
- Component level installation and its configuration (scale down).

### Assumptions and Constraints

- Each SODA Project should follow guidelines provided by this document, to satisfy features of the SODA installer.
- The implementation can be phased, evolving valuable features for end-to-end SODA installation.

### Requirement Analysis

### Input Requirements (need to refine more)

Some core requirements are:

-   Model Driven Deployment - SODA and Deployment model.
-   Single command-line interface (install, management, development).
-   Reasonable control on the installation configurations (good defaults, sitedata, reference deployment).
-   Installation log.
-   History information for last n installations on the same system
-   Post installation management and monitoring (minimal feature).

A survey of open github issues reveals technical debt.

- Need example use cases (Referenece Deployments) [@rajat-soda] [#404]
- SODA orchestrator? Upgrade paths [@noelmcloughlin] [#400 ]
- Cleanup installation [@anvithks, @joseph-v ] [#399, #278]
- Ubuntu 20.04 / CentOS / SystemD [@osiveteam, @kumarashit, @kumarashit ] [#353, #261, #381]
- Comprehensive CI/CD for installer [@kumarashit, @kumarashit ] [#345, #396 ]
- Installation with single command with good defaults [@rajat-soda] [#388][#403]
- Installer UI [@osiveteam] [#386]
- Idempotence Installer [@NajmudheenCT, @joseph-v , @Shruthi-1MN ] [#401, #369, #277]
- Cloud Native Installer [@joseph-v, @sushanthakumar, @PravinRanjan10 ] [#361, #328, #323]
- Validate Released Artifacts [@kumarashit ] [#352]
- Pre-install check and configure [@kumarashit ] [#350]
- CNCF components configuration (no hardcoding) [@himanshuvar, @joseph-v, @Shruthi-1MN ] [#376, #327, #375, #382]
- CNCF component installers [@PravinRanjan10, @thatsdone ][#326, #270 ]
- SODA configuration [@kumarashit, @sushanthakumar, @NajmudheenCT ] [#318. #306, #301]
- SODA Upgrade/Reinstall, no data loss [@joseph-v ] [#279]
- YIG installer [#276]


### Feature Requirements

#### Requirement Analysis

The command line installer shoud provide the following functions:

- Install SODA
- Install SODA [componentA,]
- Upgrade SODA
- Clean SODA
2. SODA manage
3. SODA clean

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

Address the Technical Debt (ref: https://github.com/sodafoundation/installer/issues/408)

- Example Use cases (Referenece Deployments) [@rajat-soda] [#404]
- Single Command Install for SODA with default configs [@rajat-soda] [#403]
- SODA orchestrator (soda upgrade paths) [@noelmcloughlin] [#400 ]
- Cleanup installation [@anvithks, @joseph-v ] [#399, #278]
- Ubuntu 20.04 / CentOS / SystemD [@osiveteam, @kumarashit, @kumarashit ] [#353, #261, #381]
- CI/CD for installer [@kumarashit, @kumarashit ] [#345, #396 ]
- SODA Installation with Single Command using defaults [@rajat-soda] [#388]
- SODA Installer UI [@osiveteam] [#386]
- SODA Idempotence Installer [@NajmudheenCT, @joseph-v , @Shruthi-1MN ] [#401, #369, #277]
- SODA (?) Cloud Native Installer [@joseph-v, @sushanthakumar, @PravinRanjan10 ] [#361, #328, #323]
- SODA Validate Released Artifacts [@kumarashit ] [#352]
- SODA Pre-install check and configure [@kumarashit ] [#350]
- CNCF compoents configuration (no hardcoding) [@himanshuvar, @joseph-v, @Shruthi-1MN ] [#376, #327, #375, #382]
- CNCF component installers [@PravinRanjan10, @thatsdone ][#326, #270 ]
- SODA configuration [@kumarashit, @sushanthakumar, @NajmudheenCT ] [#318. #306, #301]
- SODA Upgrade/Reinstall, no data loss [@joseph-v ] [#279]
- YIG installer [@276]

Performance Requirements

- Installation time < All selected Project installation time + 30 seconds
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

Reuse


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

References:

1. Soda Foundation, https://sodafoundation.io
2. Gaia-X, https://www.data-infrastructure.eu
3. Linux Foundation, https://www.linuxfoundation.org
4. CNCF Landscape, https://landscape.cncf.io
5. Soda Installer, https://github.com/sodafoundation/installer
5. Filesystem Hierarchy Standard, https://refspecs.linuxfoundation.org/FHS_3.0/fhs-3.0.pdf
6. Saltstack Formulas, https://github.com/saltstack-formulas
7. Ansible Galaxy, https://galaxy.ansible.com
8. YAML 1.2, https://yaml.org
9. PEP 584: Add Union Operators To dict, https://www.python.org/dev/peps/pep-0584
10. Kustomize, https://kustomize.io
11. Cuelang, https://cuelang.org
12. Team Osive Inputs: https://openitems.soda.osive.com
13. Team Osive UI design: https://www.figma.com/file/BdqdGjZYTunmSG2mUUPdRH/Figma-Admin-Dashboard-UI-Kit-Community?node-id=0%3A1
14. Data Infrastructure EU: https://www.data-infrastructure.eu/#id1854484
15. Salter: https://github.com/saltstack-formulas/salter#saltstack-formulas-namespace

**From SODA:**
https://docs.sodafoundation.io/soda-gettingstarted/quickstart/


