# SODA Installer Architecture Design

Author(s): [Sanil Kumar](https://github.com/skdwriting) ; [Noel McLoughlin](https://github/noelmcloughlin) ;//please add here

## Introduction

SODA Foundation (SF) is an open source project that aims to foster an ecosystem of open source data management and storage software for data autonomy. Linux Foundation's (LF) ecosystem hosts some of the world's most important open source projects, including Linux and SODA. The Cloud Native Computing Foundation (CNCF) hosts critical components of the global technology infrastructure. Both SODA and CNCF are LF member projects. GAIA-X, a project to develop common requirements for a European data infrastructure, compliments SODA goals.[1-4]

SODA Installer is the overall Installer project. Each Release integrates multiple member projects to provide integrated use cases and features for the users of the Releases. It is the master deployment tool for SODA Foundation.  The current solution leverages Ansible, Bash, Helm and YAML models. There is some unit test (UT), continious integration (CI), and continious deployment (CD) coverage.[5]

## Motivation & background
Currently, the SODA installer project is based on Ansible playbooks with limited options like install and clean. Other deployment methods (Salt and Helm) are not clearly supported or maintained. All deployment methods and documentation are scattered. The complexity of modern computer system requires model-driven design, but new open source projects typically focus first on deployment, adding reinstall/clean support later - that is how the SODA Installer evolved. Retro-fitting new features onto a static "installer" is a challenge. This design document proposes an architected SODA installer meeting goals and requirements.

## Goals

- Deliver seamless end to end deployment of member project, and SODA releases.
- Consolidate all deployment methods and documentation across member projects.
- Model-Driven deployment and upgrade (software/configuration).
- Make SODA release adoption simple.
- Improved user experience.
- Tools and method to troubleshoot deployment.
- Upgrade and Rollback with Data Protection.
- Commandline, and possibly Graphical User, interface.
- Different types of deployments (developer, end user).
- Effective and coherent technology (ansible, helm, or any such better options).

### Non-Goals

- Project level deployment and configuration.
- Component level deployment and configuration.

### Assumptions and Constraints

- Each SODA Project should follow guidelines provided by this document, to satisfy features of overall SODA installer.
- The implementation can be phased, evolving valuable features.

### Requirement Analysis

### Input Requirements

Core requirements and technical debt (github issues):

1.  Install/Reinstall/Clean with no data loss [@anvithks, @joseph-v ] [#278, #279, #399]
2.  Reasonable control on deployment configuration (good defaults, minimal sitedata).
3.  Logging.
4.  Post deployment monitoring (minimal feature).
5.  Example Use Cases and Reference Deployment [@rajat-soda] [#404]
6.  Upgrade/Rollback [@noelmcloughlin] [##400 ]
7.  Support for core Linux OS and SystemD [@osiveteam, @kumarashit, @kumarashit ] [#261, #353, #381]
8.  Continous Integration/Deployment (CI/CD) [@kumarashit, @kumarashit ] [#345, #352, #396 ]
9.  Single command-line interfaces with good defaults [@rajat-soda] [#388, #403]
10. Graphical User Interface (UI) [@osiveteam] [#386]
11. Idempotence Installer [@NajmudheenCT, @joseph-v , @Shruthi-1MN ] [#401, #369, #277]
12. Cloud Native Installer [@joseph-v, @sushanthakumar, @PravinRanjan10 ] [#361, #328, #323]
13. Pre-install check and configure [@kumarashit ] [#350]
14. CNCF components install/config [@himanshuvar, @joseph-v, @Shruthi-1MN, @PravinRanjan10, @thatsdone] [#270, #326, #327, #375, #376, #382]
15. Configuration Management [@kumarashit, @sushanthakumar, @NajmudheenCT ] [#318. #306, #301]
16. YIG installer [@joseph-v] [#276]
17. Model Driven Deployment  [@noelmcloughlin]

### Feature Requirements

#### Requirement Analysis

1.  Install/Reinstall/Clean with no data loss

Install is a core system function. The function target may be one-many systems, subsystems, components, and/or configuration. In systems development, the starting point is typically creation "the Installer", adding uninstall and reinstall support aferwards. System state needs to be managed. Apart from on/off, a finite range of states will need to be supported. The install or reinstall process should protect data stores as "good default". The core requirement for SODA is that install, reinstall, and uninstall (clean) be delivered.

2.  Reasonable control on deployment configuration (good defaults, minimal sitedata).

System install should be simple as possible. Good default configuration is best practice. The capability to control configuration consumed by the sytem installer is a general requirement, but should never be forced. By delivering good default configuration we can control and minimise the volume of configuration an end user may reasonably need to control. Site specific configuration data ("sitedata") includes 'version', 'checksum', 'url', 'security', and 'networking' parameters and anything else where a good default is unacceptable. The core requirement is for SODA to allow reasonable control over configuration used during the deployment process.

3.  Logging.

Logging is a common system function. The logging source may be one-many systems, subsystems, components, and/or events. System installers need to log events for the duration of the process so the process can be audited. The basic requirement is that logs be stored on persistent storage (local or remote) so they are never lost on system restart.

4.  Post deployment monitoring (minimal feature).

System monitoring is a common system function. For distributed systems, some kind of central monitoring is preferrable (including logging). The core requirement here is to allow basic surveillance of measurable events and/or outputs of the SODA system after install, as a minimal feature or framework.

5.  Common Use Cases and Reference Deployment [@rajat-soda] [#404]

Systems, and their deployment, can be complex. Example use cases helps simplify the process of deploying and managing complex systems like SODA.  The core requiremnt is to deliver the most common use cases, and reference deployments, which end users are reasonably concerned with.

6.  Upgrade/Rollback [@noelmcloughlin, Arch WG] [##400 ]

System upgrade is a core system function. Systems evolve after initial deployment, particularly complex systems like SODA. The core requirement is to deliver an upgrade feature so that users investing in using SODA do not have to start from scratch. Requested by the SODA Architecture Work Group (Arch WG).  Rollback means the capability to revert the current system version to that in place before last SODA upgrade.

7.  Support for core Linux OS and SystemD [@osiveteam, @kumarashit, @kumarashit ] [#261, #353, #381]

System hardware is controlled by an Operating Systems (OS). The SODA project aims to support Ubunt OS, but adoption requires that other OS such as CentOS and Suse are considered.  Major OS version changes must also be managed. The core OS requirement is CentOS8 and Ubuntu20 as a basic offering.

8.  Continous Integration/Deployment (CI/CD) [@kumarashit, @kumarashit ] [#345, #352, #396 ]

Passing source code through a valiation process is a core software development lifecycle function. The core requirement is to deliver a Continious Integration service for the SODA installer.

9. Single command-line interface with good defaults [@rajat-soda] [#388, #403]

SODA must provide a single command line interface (CLI) for install and deployment. The same CLI instance can allow deployment of SODA system, subsystem, or components (SODA or CNCF) for consistent user and developer experience.  The CLI should support install, reinstall, uninstall, upgrade, and rollback. The CLI should accept paramters allowing 'reasonable control' over the deployment process (as discussed previously).

10. Graphical User Interface (UI) [@osiveteam] [#386]

The requirement is a web based UI deployment rather than bash / ansible cli based. UI design requires a coherent backend design. A Restful API should be considered. For security reasons, and edge cases, a CLI is required.

11. Idempotence Installer [@NajmudheenCT, @joseph-v , @Shruthi-1MN ] [#277, #369, #401]

The current SODA installer cannot be retried. Reinstall fails with errors and is not supported. Idempotence is the concept that operations can be applied multiple times without changing result beyond the first application. SODA should support this property.

12. Cloud Native Installer [@joseph-v, @sushanthakumar, @PravinRanjan10 ] [#323, #328, #361]

The requirement is for SODA to manage a DockerHub account, so that projects can release container images. Helm should be updated to support Container Storage Interface (CSI) and controller, the current helm deployment is not maintained. The alternative to Helm is Kustomize. We need a consistent approach in SODA pproject and native support for Kubernetes and KubeEdge.

13. Pre-install check and configure [@kumarashit ] [#350]

The SODA deployment process should invoke sanity checks before proceeding with install, reinstall, uninstall, upgrade, and rollback operations. Documentation should clearly state Assumptions and Requirements for adopting SODA.

14. CNCF components install/config [@himanshuvar, @joseph-v, @Shruthi-1MN, @PravinRanjan10, @thatsdone] [#270, #326, #327, #375, #376, #382]

SODA requirements also apply to CNCF components. Unsafe defaults are not good defaults (ETCD is installed by ansible with security disabled, depreciated or unused components should not be installed/removed, and hardcoding variable key values is risky). This is an important requirement. The deployment of CEPH is also troublesome and needs improvement.

15. Configuration Management [@kumarashit, @sushanthakumar, @NajmudheenCT ] [#318. #306, #301]

The inability to configure core parameters is unacceptable. The release version for install/upgrade/rollback, and container image tags, must be configurable.  Reasonable control over release versions is important.

16. YIG installer [@joseph-v] [#276]

The multi-cloud feature YIG must be included in the SODA deployment process.

17.  Model Driven Deployment.

Many SODA Foundation projects need self-contained installers and consumed by overall SODA Installer.

- API: common unified data/storage interface
- Controller: Control services
- Dock: Connect hetrogeneous storage backends
- NBP: Industry Integration Point
- Multi-Cloud: Cloud-Vendor Agnostic interface
- Delfin: Infrastructure Manager
- Orchestration: Intelligent Workflow automation
- Anomaly: Predictive Analysis for Delfin/TTelemetry data
- Dashboard: GUI Dashboard Reference (showoff features)
- documentation Integrated SODA docs
- Native: YIG, LinStor, Zenko, (and potentially) Minio, MooseFS, Titron OS, OpenIQ, and Gluster.
- CNCF components .. etc ..

To provide a scalable solution the SODA Installer should be model-driven. This requirement is a consequence of SODA/CNCF system complexity. Each SODA component can provide its own model fragment according to the common schema. We should extend this requirement onto CNCF components required by SODA project. These model fragments are combined to create the overall model.[6]

18. Team Osive requirements must be considered.

The SODA Osive team has produced requirements to guide an improved user experience. The implementation architecture must consider these.[7-8]


#### List of Requirements

##### Functional Requirements

-   Support deployment of all or specific projects
-   Support undeployment of all or specific projects
-   Start and stop services
-   Provide logging
-   Provide configuration
-   Each deployment methods can be integrated
-   Separate clean (delete runtime files) and purge (delete install downloaded files etc = full clean without any trace!)
-   Trace deployment (last 5 deployment information for the same system)
-   Start / Stop services
-   Upgrade? (currently not considered below) -- TBD
-   Custom install path? (currently not considered below) - TBD
-   Pre / post-install checks
-   Support post install logs
-   Support post install management (basic)


##### Non Functional Requirements

Address Technical Debt (ref: https://github.com/sodafoundation/installer/issues/408)

Performance Requirements

- Installation time < All selected Project deployment time + 10 minutes
- Optimized downloads - Skip download if already cached with valid checksum. Allow retries.
- Optimize procedure calls and returns - d parameter passing - only pass needed data between modules.

Security Requirements

See Requirements.

Other Non-Functional Requirements (Scalability, HA etc…)

-   Easy to add new project deployment support
-   Easy to add new deployment methods
-   Upgrade
-   rollback
-   fail recovery
-   restart recovery
-   Incremental deployment

Reuse


## Architecture Analysis
### Ovearll Architecture
**SODA Installer**
| *[Common Config, Project MetaData, Utils]*
|--> Project 1 Model
|--> Project 2 Model
|--> Project n Model

(Diagram will be added)


### Model Architecture

Model-driven software release install means an information model describes the system configuration to be managed, in terms of different software entities. Some entities (i.e. dock, dashboard) are characterized by service providers (ip address/port) and their configuration, while other entities are assigned dynamically by system state managers (kubernetes, Docker swarm, etc) depending on their health and eligibility. A deployment model can describe what components are required (mandatory, optional, fine-grained) and wanted or desired configuration.[9]

Managing, generation and validation of data is a System Description Language (SDL) function. Model driven deployment state operates by gathering information from common familiar data types such as lists, dictionaries, and strings. Renderers translate input from written format into data structures. Data Renderers return a data structure (typically a dictionary).[10]

YAML is a defacto-standard data renderer (SDL) for Infrastructure as code (IaC) and Configuration as code (CaC), and already adopted in SODA. It default in many orchestration tools (i.e. Kubernetes, Ansible, Salt, Helm, Stackstorm, etc).[11]  The following are all data renderers:

  - dson
  - hjson
  - json5
  - json
  - pydsl
  - pyobjects
  - py
  - stateconf
  - yamlex
  - yaml
  - cuelang

Text renderers, or templating engines, return text. JINJA is a defacto-standard text renderer for IaC and CaC. YAML is default in python-based solutions such as Ansible, Salt, Stackstorm, and Apache-Airflow.[12] The following are all text renderers:

  - aws_kms
  - cheetah
  - genshi
  - gpg
  - jinja
  - nunjucks (jinja nodejs library)
  - mako
  - nacl
  - pass
  - py

Models must be validated before usage. Static Code Analysis tools called Linters can be enabled in CI/CD and Integrated Development Environments (Eclipse, Jetbrains, Vscode).[13] SODA should use some/all of the following:

  - rubocop            (ruby syntax)
  - shellcheck         (bash script syntax)
  - PSScriptanalyzer   (Powershell script syntax)
  - commitlint         (enforce message format)
  - yamllint           (yaml syntax)
  - salt-lint          (salt syntax)
  - ansible-lint       (ansible syntax)
  - jinja2-lint        (jinja2 syntax)

Data constraint languages (DCL) helps define and use data. Cuelang has an implementation.[14] The following are all DCL's:

  - cuelang
  - kaleidoscope

Availability of services is an important requirement, so we need standards to support the realization of high available systems. We require a configuration that describes the provided services, their types, and deployment infrastructure. The process of generating a deployable configuration takes as input the description of the component characteristics as well as the Configuration Requirements of the services to be provided.[15]

The proposed solution is model-driven and based on YAML fragments which capture concepts related to Configuration Requirements, software description, and service deployment configurations. Following a common schema, each SODA component delivers its model fragment. At deployment time, these are combined into the desired deployment model. Optionally, end users override defaults by supplying a "sitedata" model (site specific data) as input to the process. A deployment management tool consumes Soda model fragments, builds an in-memory model of merged fragments, realizng the desired SODA deployment using configuration management technology (Ansible, Salt, Stackstorm, Kustomize, etc). Upgrade/Rollback is facilitated by applying new model fragments to the current model to realize the wanted deployment. The soda model format can be reused outside SODA project.

To illustrate the model architecture, two examples of Soda model (called "Sodam") and deployment tool (called "Sodai") are given.

#### Working Example 1

In this example the Sodai deployment tool deploys SODA Delfin and Dock with defaults (no user data was supplied).

Column 1 is the default Sodam-format yaml file released by Soda Delfin project.

Column 2 is the default Sodam-format yaml file released by Soda Dock project.

Column 3 is the dynamic in-memory model generated by Sodai deployment tool.

    # DELFIN model fragment               # DOCK model fragment           # DEFAULTS DEPLOYMENT MODEL
    # defaults.yaml                       # defaults.yaml                 # in memory dictionary
    # format: sodam                       # format: sodam                 # format: sodam

    delfin:                               dock:                           soda:
      supported:                            supported:                      supported:
        # what I support                      # What I support                # What SODA supports
        - delfin                              - dock                          - delfin
                                                                              - dock

      wanted:                               wanted:                         wanted:
        # what I want                         # What I want                   # What End-User wants
        - delfin                              - dock                          - delfin
                                                                              - dock

      depends: []                           depends:                        depends:
        # what I depend on                    # What I depend on              # what SODA depends on
                                              - delfin                        - delfin
                                                                              - cncf  # experimental idea

      config:                               config:                         config:
        # configuration                       # configuration                 # what End-User wants
        delfin: { .. }                        dock: { .. }                    delfin: { .. }
                                                                              dock: { .. }
                                                                              { .. }

      container:                            container:                      container:
        # container details                   # container details             # what End-User wants
        delfin: { .. }                        dock: { .. }                    delfin: { .. }
                                                                              dock: { .. }
                                                                              { .. }

      dir:                                  dir:                            dir:
        # local directories                   # local directories             # local directories needed
        delfin: { .. }                        dock: { .. }                    delfin: { .. }
                                                                              dock: { .. }
                                                                              { .. }

      pkg:                                  pkg:                            pkg:
        # what I support                      # what I support                # What SODA supports
        delfin:                               dock:                           delfin:
          version: 1.0.0                        version: 1.0.0                  version: 1.0.0
          use: archive                          use: archive                    use: archive
          # tarball details                     # tarball details               # tarball details
          archive: { .. }                       archive: { .. }                 archive: { .. }
          # linux pkg dependencies              # linux pkg dependencies        # linux pkg dependencies
          deps: []                              deps: []                        deps: []
          # python dependencies                 # python dependencies           # python dependencies
          pips: []                              pips: []                        pips: []
                                                                              dock:
                                                                                version: 1.1.1
                                                                                use: archive
                                                                                archive: { .. }
                                                                                deps: []
                                                                                pips: []
#### Working Example 2

Here is the same example but user wants Delfin version 12.0.0 instead of default.

Column 1 is userdata deployment model fragment.

Column 2 is the dynamic in-memory model generated by Sodai deployment tool (before merging userdata.yaml)

Column 3 is the dynamic in-memory model generated by Sodai deployment tool (after merging userdata.yaml)::

    # USERDATA                            # DEFAULTS DEPLOYMENT MODEL     # USER DEPLOYMENT MODEL
    # userdata.yaml                       # in memory dictionary          # in memory dictionary
    # format: sodam                       # format: sodam                 # format: sodam

    delfin:                               soda:                           soda:
      pkg:                                  pkg:                            pkg:
        delfin:                               delfin:                         delfin:
          version: 12.0.0                        version: 1.0.0                 version: 12.0.0
                                              dock:                           dock:
                                                 version: 1.0.0                 version: 1.0.0

                                            { .. }                          { .. }

### Module Architecture

### High Level Module View

### Architecture Tenets

-   Unified single cli interface for SODA Release deployment.
-   Different deployment methods, within reason.
-   Extensible to add new deployment methods
-   Extensible to add new project deployments
-   Traceable deployments (5 deployment history)

The proposed solution will be aligned with assumptions, goals, requirements, analysis, and tenets given above.

### High Level Module Architecture

The deployment infrastructure is Physical, Virtual (Machine, Container), and/or Kubernetes. The deployment tool is CLI driven. The CLI style is Cloud Native, following the dominant pattern in modern scalable implementation architectures.[16-17] The implementation of the CLI will be bash script, evolving into Python or Go in future releases. The design is module driven. Modules can be added, promoted, depreciated and removed. A UI and API module can be evolved to compliment CLI.  The user experience will be consistent. The deployment tool supports stdin and (yaml) files. "Sodam" yaml format is used to realize SODA Deployment Configuration Description. The deployment (deploy) module is core. The command design is well known.[16] This module supports agreed deployment use cases, additional functions can be modeled and evolved.

Deployment model:

  - deploy create (install/reinstall/upgrade/rollback): Create resources from file or stdin. Can be forced.
  - deploy delete (uninstall): Delete resources by file, stdin, and/or other selectors. Can be forced.
  - deploy get (show): Display one or many resources. Support selectors. 
  - deploy run (execute): Create and Run a particular container image.
  - deploy expose (start): Expose one or many resources as services.

Other modules beyond "deploy" will be facilated:

  - logs: retrieve logs for running instances of SODA, including deployment history and logs retrieved from persistent storage.
  - addons: allow us to modify SODA addons files (i.e. dashboard, telemetry, etc) using subcommands like "addons enable dashboard".
  - cache: allows us to add, delete, or push local images into SODA. An Image refers to supported package formats (Docker, tarball, k8s).
  - completion: generates command completion for a shell (i.e. bash, zsh).
  - config: modify persistent configuration file values using subcommands like "config set driver lvm". A finite set of fields are supported.
  - other: can be proposed/evolved.

The deployment tool should support some or all of the following actions:

  -  precheck: Project deployment dependency check-precheck : Checks the depependency readiness for the deployment based on the configuration file
  -  install: Checks the prerequisite and install all the packages of the project (based on the project configuration file)
  -  uninstall: Uninstall all the packages of the projects ( based on the project configuration file)
  -  clean: Uninstall and clean up the files created for all the deployment and run time of the components. (db, other run time files etc)
  -  purge: clean and delete all the downloaded files and install setup files. Caution!!
  -  chkinstall: Checks the last deployment and provides a summary of project deployments

The deployment logs will converge to a common format, including: 

  - Installation Time: < date and time - long string>
  - Project Name (or Component)
  - Project Version:
  - Packages or Features installed
  - Configuration file


## Detailed Design

See references.

### Use case View

#### List of Typical Usecases

1. Quick deploy with defaults: Install a reference deployment of SODA project.
2. Quick deploy with sitedata: As above, with site specific data provided in YAML "Sodam" format.
3. Light deploy: Resource constraint situations. KubeEdge support.
4. Upgrade deploy: SODA/Component version n to version n+n
5. Rollback deploy: SODA/Component version n to version n-1.
6. Incremental deploy: Gradually add/remove SODA and/or CNCF components to the deployment.

#### Usecase context model

NA

#### Interface Model

NA

External Interfaces

CLI as in the help.

#### End User Context

Can use it within any methods where it can trigger cli.

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

creates an installer info file (current-folder/sodapmt-instinfo.info)based on which the install summary and history can be tracked up to last 5 deployments.

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

Use any of the supported deployment method. --help will get the list of supported deployment methods

-<install-options>*

-precheck : Checks the depependency readiness for the deployment based on the configuration file

-install : Checks the prerequite and install all the packages based on the configuration file

-uninstall: Uninstall all the packages based on the configuration file

-clean: Uninstall and clean up the files created for all the deployment and run time of the components.

-purge: clean and delete all the downloaded files and install setup files. Caution!!

-chkinstall: Checks the last deployment and provides a summary of last deployment on the system

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

Bash or Python or Go?

Running on Ubuntu 16.04, 18.04 or on the project supported platforms (challenge)

#### Debug Model

#### Logging, Install summary information

#### Build & Package

TBD

Can we have some package manager ?

#### Deployment

Download, extract and run!

### Execution View

Create the information file created with relevant deployment information

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
2. Linux Foundation, https://www.linuxfoundation.org
3. CNCF Landscape, https://landscape.cncf.io
4. Gaia-X, https://www.data-infrastructure.eu/#id1854484
5. Soda Installer, https://github.com/sodafoundation/installer
6. PEP 584: Add Union Operators To dict, https://www.python.org/dev/peps/pep-0584
7. Team Osive Inputs: https://openitems.soda.osive.com
8. Team Osive UI design: https://www.figma.com/file/BdqdGjZYTunmSG2mUUPdRH/Figma-Admin-Dashboard-UI-Kit-Community?node-id=0%3A1
9. Automatic Generation of AMF Compliant Configurations, http://users.encs.concordia.ca/~abdelw/papers/ISAS08-AMFConfiguration.pdf
10. Renderers, https://docs.saltstack.com/en/latest/ref/renderers
11. YAML 1.2, https://yaml.org
12. Jinja, https://jinja.palletsprojects.com/en/2.11.x
13. Lint (software), https://en.wikipedia.org/wiki/Lint_(software)
14. Cuelang, https://cuelang.org
15. A model-driven approach for the generation of configurations for highly available software systems, https://doi.org/10.1007/s11334-018-0320-y
16. Kubectl Commands, https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-strong-getting-started-strong-
17. Minikube Commands, https://minikube.sigs.k8s.io/docs/commands

tbc ...
18. Filesystem Hierarchy Standard, https://refspecs.linuxfoundation.org/FHS_3.0/fhs-3.0.pdf
19. Saltstack Formulas, https://github.com/saltstack-formulas
20. Ansible Galaxy, https://galaxy.ansible.com
21. Kustomize, https://kustomize.io
22. Salter: https://github.com/saltstack-formulas/salter#saltstack-formulas-namespace

**From SODA:**
https://docs.sodafoundation.io/soda-gettingstarted/quickstart/


