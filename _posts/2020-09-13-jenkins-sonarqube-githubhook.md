---
title: Jenkins - Sonarqube - Github Hook Setup Process
tags:
- cicd
- sonarqube
- jenkins
- githook
---

## Preconditions
### Project
1. Add sonarqube dependency to your project. With maven or gradle, add:
```
org.codehaus.mojo.sonar-maven-plugin
```
2. Add jacoco plugin for test coverage. With maven or gradle, setup with:
```
org.jacoco:jacoco-maven-plugin
```

3. Write pipeline script for [jenkins pipeline](https://www.jenkins.io/doc/book/pipeline/syntax/)  <br>
I'll explain later on "Jenkins setting" section. <br>

### Jenkins
Install "Generic webhook trigger" from Jenkins > Settings > Plugin Manager > Plugins can be installed. <br>
I guess restarting jenkins is not necessary. <br>
[https://plugins.jenkins.io/generic-webhook-trigger/](https://plugins.jenkins.io/generic-webhook-trigger/)

Add permission to utilize variables in script, on Jenkins > Settings > In-progress script approval:
```
method groovy.lang.Binding getVariables
```
### Sonarqube
#### Plugins
Some of the plugins (e.g. sonar-github) is deprecated in recent sonar version. <br>
To use that, you have to build it manually and put it on plugin directory. <br>
## Sonarqube Install
I think it is better to use sonar version below 7.6, as many features are deprecated or blocked in community version recently. <br>
If you have manually-built plugins, push those under sonar-home-dir/extensions/plugins <br>
After you installed sonarqube, you can see:

![]({{ 'images/1_HdDeiZBIefJQI2wva0vYuA.png' | relative_url }})

When you login with admin account you can see administration menu.  <br>
You can download plugin in marketplace or setup system in administration menu. <br>
## Git Hook Setting
Add hook to (jenkins-url)/generic-webhook-trigger/invoke for generic webhook trigger. <br>	
## Jenkins Pipeline Setting
1. Create Pipeline project
2. Setup each keys on Build Triggers -> Generic webhook trigger. Add same variables on "This build has paramters"
3. Check Build -> Pipeline script with SCM and setup github repository. Put jenkins pipeline script path in project on "Script Path" <br>


## Script Detail
1. Check whether triggered job is pr sync or opened event
2. Checkout to target branch
3. Add sonar start comment with github api
4. Run sonar job with sonar-github plugin to create comment on each pr

### 0. preconditions
Add property settings under node block to utilize general webhook trigger plugin in jenkins pipeline:
```
properties([
    pipelineTriggers([
        [$class: 'GenericTrigger',
         genericVariables: [
             [key: '<key1>', value:'<value rule'>,
             ...
         ],
         ...
    ])
])
```
For the detail please refer to [this page](https://plugins.jenkins.io/generic-webhook-trigger/)
### 1. Check whether triggered job is pr sync or opened event
On webhook trigger body, there is action field which indicates pull request action type.
If action field is not "synchronized" or not "opened", abort jenkins job.

### 2. Checkout to target branch
```
checkout([
    $class: 'GitSCM',
    branches: [[name: <branch>]],
    doGenerateSubmoduleConfigurations: false,
    extensions: scm.extensions + [[$class: 'CleanCheckout']],
    userRemoteConfigs: scm.userRemoteConfigs,
    ...
])
```

For the detail please refer to [this page](https://support.cloudbees.com/hc/en-us/articles/226122247-How-to-customize-Checkout-for-Pipeline-Multibranch)

### 3. Add sonar start comment with github api
With [github api](https://developer.github.com/v3/) make http request inside pipeline to github.
### 4. Run sonar job with sonar-github plugin to create comment on each pr
With maven: 
```
mvn \
-Dsonar.host.url=<sonar url> \
-Dsonar.login=<sonar login token> \
-Dsonar.github.endpoint=<github api url> \
-Dsonar.github.repository=<repository full name> \
-Dsonar.github.login=<github user id> \
-Dsonar.github.oauth=<github user oauth token> \
-Dsonar.analysis.mode=preview \
-Dsonar.github.pullRequest=<pull request number> \
... \
clean package sonar:sonar
```
-Dsonar.github.(...) option is for using [sonar github plugin](https://docs.sonarqube.org/display/PLUG/GitHub+Plugin) . <br>

## Summary
In short: <br>
When "pr opened or synchronized" git event is triggered, jenkins pipeline job connected with webhook is executed.  <br>
In the job "sonar started" comment is created on pull request via github api, and sonar static analysis job is executed. <br>
After job is done, github plugin creates comments for issues included in the pull request.
