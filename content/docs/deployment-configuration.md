---
description: 'How to define which files should be persistent on the runtime and which commands should be executed.'
sidebar: 'docs'
prev: '/docs/configuration-overview/'
next: '/docs/github-actions-customization/'
editable: true
---

# Deployment configuration

If your project requires a runtime, you might wish to define which files should be persistent and which commands should be executed remotely. You can do this by using the `config.yaml` in `.deploy-now`. The information stored in this file is used to define the part of the GitHub action that manages the deployment to our infrastructure.

*When working with static sites, the only deployment setting you need ist the dist folder. You can adapt this directly by [customizing GitHub actions](/docs/github-actions-customization).*

:::note
Supporting PHP runtimes is currently in alpha. You can join our alpha by following [these](/docs/php-alpha) instructions.
::: 

## Examplary `config.yaml`

``` yml
version: 1.0
deploy:
  # comment in one of the following lines to force the use of the recurring or bootstrap configuration
#  force: recurring
#  force: bootstrap

  # configure the initial deployment of each branch
  bootstrap:
    # directories that are not overwritten or removed by the next deployment
    excludes:
      - samplefolder
      - samplefile.txt
      - folder/withfile.txt
      
    # commands that are executed on the runtime after new files are copied
    post-deployment-remote-commands:
      - touch database.sqlite
      - php8.0-cli -r "echo 'do something with php';"

  # configure all following deployments of each branch
  recurring:
    # directories that are not overwritten or removed by the next deployment
    excludes:
      - samplefolder
      - samplefile.txt
      - folder/withfile.txt
      - database.sqlite
      
    # commands that are executed on the runtime before new files are copied
    pre-deployment-remote-commands:
      - echo "starting maintenance mode"
      
    # commands that are executed on the runtime after new files are copied
    post-deployment-remote-commands:
      - echo "clearing caches"
      - php8.0-cli -r "echo 'do something with php again';"
      - echo "leaving maintenance mode"
      - echo "back again"

```

An examplary `config.yaml` for a Laravel project can be found [here](https://github.com/ionos-deploy-now/laravel-starter), a Symfony example can be found [here](https://github.com/ionos-deploy-now/symfony-starter).

## Configure initial and following deployments

The directories you want to exclude and the commands you want to execute on your runtime might differ between initial deployments (`bootstrap`) and any following deployment (`recurring`). By default, the first deployment action of a newly connected branch always uses `bootstrap`, whereas any following deployment action is based on `recurring`. You have the option to force the use of either one.

## Manage persistency 

Per default, all files in your defined dist folder are copied to the infrastructure after every git commit. If you want to prevent certain directories from being copied, you can list them under `excludes`. `Excludes` also prevent files that are created by your application from beeing deleted. If you want to copy files to the infrastructure on your initial deployment, but keep them persistent afterwards, you can do this by adding them to the `excludes` in `bootstrap`. 

## Executing commands on the runtime

You can execute commands on your runtime after `bootstrap` and `recurring` deployments by listing them under `post-deployment-remote-commands` and before `recurring` deployments using `pre-deployment-remote-commands`. 
