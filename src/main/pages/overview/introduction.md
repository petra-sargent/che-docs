---
title: Introduction to Eclipse Che
keywords: overview
tags: [overview, getting-started]
sidebar: user_sidebar
permalink: index.html
folder: overview
summary: Eclipse Che is a developer workspace server and cloud IDE

---

## Introducing Eclipse Che
Eclipse Che is a browser based IDE that uses a workspace model. Workspaces include an IDE, project files, and runtimes. Che provides a large number of plugins for many programming languages, frameworks, and source control tools. Che also provides a SDK for authoring new extenstions, packaging extensions into plug-ins, and grouping plug-ins into an assembly.

### Modes Supported in Eclipse Che
Eclipse Che is available in two different modes:
- **Single-user**: This is best suited for personal desktop environments.
- **Multi-user**: This is an advanced setup for Che and is best suited for organizations and developer teams.

See [Single and Multi-User][single-multi-user] to learn more.

### Platforms Supported in Eclipse Che
Eclipse Che runs on different platforms:
- Docker
- OpenShift
- Kubernetes (soon)

See [Infrastructures supported in Eclipse Che][infra-support] to learn more.

## Getting Started with Eclipse Che
You can get started with Che by:
- Installing it on Docker in either [Single-user][docker-single-user] or [Multi-user][docker-multi-user] mode
- Installing it on OpenShift in either [Single-user][openshift-single-user] or [Multi-user][openshift-multi-user] mode
- Creating a hosted SaaS account

See the [Quick Start Guide][quick-start] to quickly install Che in the single user mode.


## Understanding Che''s Workspace Model

Che defines a workspace as the project code files and all of their dependencies necessary to edit, build, run, and debug. The IDE and the development runtime are treated as a dependency of the workspace. These items are embedded and included with the workspace. In the past, workspaces include the project code, but the developer binds the IDE and uses the localhost to provide a runtime.

{% include image.html file="intro/intro-che-workspace.png" %}

A workspace contains one or more runtimes. The default runtime within our workspaces are Docker containers, but these runtimes can be replaced with other types of "machines" that offer different characteristics. We, for example, provide an SSH machine type and will soon provide localhost machines. The advantage of Docker as the runtime type allows users to define the contents of their runtime using Dockerfiles, for which we can then dynamically construct workspace runtimes without the user having to learn a lot of complex Docker syntax.
{% include image.html file="intro/intro-workspace-runtimes.png" %}


### Introducing Workspaces and Projects

A workspace can have 0..n projects, with each project mapping to 0..1 remote version control repositories such as git, subversion, or mercurial. Projects are mounted into the workspace, so that they are available both inside of the workspace and also available on long term storage. Each project has a "type", such as "maven", which when selected will activate a series of plugins that alter the behavior of the workspace to accommodate that project type. Projects can have different types and they can also have remodules which are sub-portions of a project that have their own typing and behaviors.

{% include image.html file="intro/intro-project-mounted.png" %}

### Introducing Browser IDE

Each workspace has its own private browser IDE hosted within it. The browser IDE provided by Che is packaged as JavaScript and CSS, but our IDE could be replaced with other IDEs. Since each workspace has its own server runtimes, each workspace can have a customized IDE with different plugins packaged within it.

By default, each workspace also configures its own SSH server.  This allows remote clients and desktop IDEs to SSH mount into the workspace. By SSH mounting, you can let IDEs like IntelliJ or Eclipse work with the projects and runtimes contained within Che.

{% include image.html file="intro/intro-SSH-connect.png" %}

### Introducing Che Server and Runtimes

Workspaces are hosted in the Che server, which is a lightweight runtime for managing workspaces. A single Che server can manage large volumes of workspaces, which themselves may or may not be running on the same host. Since Che workspace runtimes have their own runtimes, each workspace can be running on the same host or another host, managed by a docker daemon.  The Che server is also a Docker container by default, which itself could be operated by compose or Swarm.

{% include image.html file="intro/intro-share-workspace.png" %}

Since the workspaces are servers that have their own runtimes, they are collaborative and shareable. This allows multiple users to access the same workspace at the same time. Each workspace has its own unique URL which allows multi-user access.  We currently support multiple users within a single workspace on a last-write-wins policy. Before the end of 2016, we'll have multi-cursor editing using an operational transform.

Each workspace is defined with a JSON data model that contains the definition of its projects, its runtimes, its IDE, and other necessary information that allows a Che server to create replicas. This allows workspaces to move from one location to another, such as from one Che server to another Che server.  You will never have the "but it runs on that computer" issue again.  Workspaces can also have their internal state snapshot and saved in a registry, so replicas can be created from the original template, or from images that contain modifications made after a user started working with the workspace.

Both the Che server and each workspace have their own embedded RESTful APIs. Everything that is done by the user dashboard Web application and the browser IDE is done over RESTful APIs. You can access these APIs using swagger as Swagger configurations are provided within each service. The API set within the server and each workspace dynamically changes based upon the plugins that have been deployed by the admin or the user.

{% include image.html file="intro/intro-share-workspace.png" %}

## Users  
Che has three types of users:
  * **Developers**. Che can be installed and used as a local IDE for any kind of programming language or framework, such as Java and JavaScript. While Che runs as a server, it can be run on a desktop, server, or within an embedded device. You can use Che with our embedded browser IDE or your existing IDE which is allowed to SSH into the Che workspaces.  

  * **Product Owners**. Che provides APIs hosted within its workspace server to manage environments, workspaces, projects, templates, stacks, and intellisense for developer activities such as editing, syntax analysis, compiling, packaging, and debugging. You can use Che to host on-demand workspaces accessed by the Che IDE or a client that your product team authors. For example, SAP uses the Che workspace server to embed its development tools for SAP Hana.

  * **Plugin Providers**. Che provides a SDK to create and package plugins that modify the browser IDE, workspaces, or the Che server. ISVs and tool providers can add new project types, programming languages, tooling extensions, or applications. Che plugins can be authored for the client-side IDE or the server-side.  


## Logical Architecture  

{% include image.html file="intro/intro-che-architecture.png" %}

Che is a workspace server that runs on top of an application server like Tomcat. When the Che server is launched, the IDE is loaded as a Web application accessible via a browser at `http://localhost:8080/`. The browser downloads the IDE as a single page web app from the Che server.  The Web application provides UI components such as wizards, panels, editors, menus, toolbars, and dialog boxes.

As a user interacts with the Web application, they will create workspaces, projects, environments, machines, and other artifacts necessary to code and debug a project. The IDE communicates with Che over RESTful APIs that manage and interact with a Workspace Master.

The Che server controls the lifecycle of workspaces. Workspaces are isolated spaces where developers can work. Che injects various services into each workspace, including the projects, source code, Che plug-ins, SSH daemon, and language services such as JDT core Intellisense to provide refactoring for Java language projects. The workspace also contains a synchronizer which, depending upon whether the workspace is running locally or remotely, is responsible for synchronizing project files from within the machine with Che long term storage.

## Extensibility  
Che provides an SDK for authoring new extensions, packaging extensions into plug-ins, and grouping plug-ins into an assembly. An assembly can either be executed stand alone as a new server, or, it can be installed onto desktops as an application using included installers.

There are a number of aspects that can be modified within Che.

| Type   | Description
| --- | ---
| IDE Extension   | Modify the look-and-feel, panels, editors, wizards, menus, toolbars, and pop-up boxes of the client. IDE extensions are authored in Java and transpiled into a JavaScript Web application that is hosted on the Che server as a WAR file.
| Che Server Extension  (aka, Worskspace Master)   | Add or modify the core APIs that run within the Che server for managing workspaces, environments and machines. Workspace extensions are authored in Java and packaged as JAR files.
| Workspace Extension  (aka, Workspace Agent)   | Create or modify project-specific extensions that run within a workspace machine and have local access to project files. Define machine behaviors, code templates, command instructions, scaffolding commands, and intellisense. The Che Java extension is authored as a workspace agent extension, deployed into the machine, and runs JDT core services from Eclipse to do local intellisense operations against the remote workspace.

Each extension type is packaged separately because they are deployed differently into the assembly. IDE extensions are transpiled using GWT to generate a cross-browser JavaScript. This application is packaged as a WAR file and hosted on the Che server.

Workspace master extensions are deployed as services within the Che server. Once deployed, they activate new management services that can control users, identity and workspaces.

Workspace agent extensions are compiled with Che core libraries and also deployed within an embedded Che server that runs inside of each workspace machine. The Che server is injected into machines created and controlled by the central workspace master Che server. This embedded server hosts your workspace agent extensions and provides a communication bridge between the services hosted within Che and the machines that are hosting the project.

## Machines
When you develop with a desktop IDE, the workspace uses localhost as the execution environment for processes like build, run and debug. In a cloud IDE, localhost is not available, so the workspace server must generate the environments that it needs. These environments must be isolated from one another and scalable. We generate containers that contain the software needed for each environment. Each workspace is given at least one environment, but users may create additional environments for each workspace if they want. Each container can have different software installed. Che installs different software into the machine based upon the project type. For example, a Java project will have the JDK, Git, and Maven installed.  When a user is working within their Workspace, this container is booted by Che and the source code of the project is mounted within it. Developer actions like auto-complete and `mvn clean install` are processes that are executed within the container. Users can provide their own Dockerfiles or Composefile that Che will build into images and extension developers can register Dockerfile templates associated with a project type.  This allows Che to manage a potentially infinite number of environments while still giving users customization flexibility.

## What's Included  
Che ships with a large number of plugins for many programming languages, build systems, source code tools, and infrastructure including Java, Maven, Ant, Git, JavaScript, and Angular.JS. The community is developing their own and many are merged into the main Che repository. Che can be installed on any operating system that supports Docker 1.8+, OpenShift or Java 1.8 – desktop, server or cloud and has been tested on Linux, MacOS and Windows. It is originally licensed as EPL 1.0, and starting from version 6.9.0 and higher - as EPL 2.0.


{% include links.html %}
