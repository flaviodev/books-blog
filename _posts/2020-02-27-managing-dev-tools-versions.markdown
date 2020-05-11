---
layout: post
title:  "Improving version management of dev-tools on Linux"
date:   2020-02-27 23:21:59 -0300
categories: [ideias, dev-env]
---

#### In some situations, version management of development tools can bring some complications to the environment configuration.
If you've ever needed to maintain multiple projects developed in different technologies with different language and framework versions. So, you know how much   it's complicated to manage everything on the same machine.

#### But the packaging tools of Linux distributions (like Ubuntu's apt), they have this mission, don't they? 
They work for stable tools, like the JDK (Java Development Kit), which are not updated frequently.

If you want to use unstable versions or tools that change frequently, packaging tools are not a good option, although it also doesn't solve the problem of installing more than one version of the same tool (for example: two versions of nodejs).

#### Most common issues with managing versions of development tools:
* Services installation and configuration
* Many different places to download installers
* time spent installing and configuring a development environment
* Organization (directory tree)
* PATH configuration for different versions of the same tool

#### For services version management there is an obvious solution: Docker
Using Docker, you can easily activate and deactivate any type of service when necessary, and thus easily manage different versions of a specific service (for example: different versions of postgres).
Another big advantage is the fact that the images of the main services needed are in DockerHub (which makes it easy to find) and, with a reasonable knowledge of docker technology, you can quickly create scripts to create and run containers organized in a single location, where you can take them to another computer or just make a backup.

```` bash
    
    $ docker run -d --name pg9 -e POSTGRES_PASSWORD=password -p 5432:5432 postgres:9.6-alpine

    $ docker run -d --name pg10 -e POSTGRES_PASSWORD=password -p 65432:5432 postgres:10.12-alpine

````

#### Universal package manager for installed development tools

Docker is a good way to run services, but when you need to use tools to compile and run code (like clojure), it's even possible, but it's horrible, this type of tool requires installation. To solve this problem, there is a project on github called [asdf-vm](https://asdf-vm.com), an extendable version manager with support for Ruby, Node.js, Elixir, Erlang & much more, this tool allows you to manage multiple runtime versions with a single CLI.

The main motivations to use it are the single global config keeping defaults in one place, the possiblitiy to automatically switches runtime versions as you traverse your directories and a simple plugin system to add support for your language of choice ([all plugins list](https://asdf-vm.com/#/plugins-all)).

##### To demonstrate the adsf-vm features, let's install Kotlin and NodeJS:

* First of all, it's necessary to install adsf-vm ([get startd](https://asdf-vm.com/#/core-manage-asdf-vm))

* Install Kotlin and NodeJS plugins by asdf-vm


    Get plugins at [plugin list](https://asdf-vm.com/#/plugins-all) ([Kotlin](https://github.com/missingcharacter/asdf-kotlin) and [NodeJS](https: //github.com/asdf-vm/asdf-nodejs)), on each plugin page there will be installation instructions with commands like these:

    Installing Kotlin

    ```` bash

        $ asdf plugin-add kotlin https://github.com/missingcharacter/asdf-kotlin.git

    ````

    Installing NodeJs

    ```` bash

        $ asdf plugin-add nodejs https://github.com/asdf-vm/asdf-nodejs.git

        $ bash ~/.asdf/plugins/nodejs/bin/import-release-team-keyring

    ````

* Listing asdf installed plugins and versions

    ```` bash

        $ asdf list

    ````

    Result:

    ```` bash

        kotlin
        No versions installed
        nodejs
        No versions installed

    ````

    This means that we can now choose the plugin versions to be installed, to list all versions of a plugin:

    ```` bash

        $ asdf list-all kotlin

    ````

    The result will be a huge list of all versions of the Kotlin. 

* Installing versions

    ```` bash

        $ asdf install kotlin 1.3.61

        $ asdf install kotlin 1.3.70-eap-42

    ````

* Global and local paths

    After to install we need to define the global version for all projects:

    ```` bash

        $ asdf global kotlin 1.3.61

    ````

    However, we can define a specific version for a project:

    ```` bash

        ~/project1$ asdf local kotlin 1.3.70-eap-42

    ````

    To install other tools, simply repeat the steps:

    ```` bash

        $ asdf list-all nodejs

        $ asdf install nodejs 9.10.0

        $ asdf install nodejs 13.9.0

        $ asdf global nodejs 13.9.0

        ~/project1$ asdf local nodejs 9.10.0

    ````

* Updating versions and asdf-vm

    Finally, to update the versions of the installed plugins

    ```` bash

        $ asdf plugin-update --all

    ````

    and the asdf-vm

    ```` bash

        $ asdf update

    ````

##### Conclusion

With these simple tools and strategies for organizing and managing services and development tools, you can avoid version conflicts and less time wasted setting up the environment.