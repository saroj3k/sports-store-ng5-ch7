# Angular5 Application with Deployment to Openshift

This project deploys the Sportsstore application from Adam Freeman's book to a local installation of _Openshift 3.11 (minishift)_ using _Jenkins pipeline strategy_ and _Virtualbox_. The Pipeline has codeblocks that will execute at each stage. For example: the code will be checked out from a git repo; deppendencies rsolved thru npm package manager;
the output of the ng build is merged with the nginx image; the docker image is created using the Openshift build Config. The deployment and routes are created using the new-app, and linked to the
output of the build config. Please note that the entire build process initiated within a Jenkin slave image within a container

## Prerequisites

### Access to an Openshift 3.11 environment

Install locally via minishift route, or a remote Openshift 3.11 installation you can access via `oc` command.

## Deployment

### Create the pipeline (and Jenkins instance)

Create the Jenkins pipeline definition in Openshift
```
    oc create -f pipeline.yml
```    
THis will create an instance of Jenkins pipeline.

### Confirm NodeJS 8 Installation

The image for the `nodejs v8` is installed with label `nodejs`

### Create binary build configuration 

Create a binary build configuration (BuildConfig) based on Nginx v1.12 targeted for rhel7 
```
    oc new-build . --docker-image=registry.access.redhat.com/rhscl/nginx-112-rhel7 --name=sports-store-ng5-ch7-rhel
``` 
NOTE the first build may fail because it is a binary build and binary input hasn't been provided.

### Start the Jenkins pipeline

Trigger a Jenkins build to initiate the build

```
    oc start-build sports-store-ng5-ch7-pipeline
```

Please Note: the Jenkinsfile, which has the code pockets (ref. build image step) that pipes the output of the Angular build into the binary build-config (sports-store-ng5-ch7-rhel)
created in the previous step.
    
### Create application

One-off requirement is required to create the Openshift *Namespace (aka project)*, *Deployment-config* and deploy the docker image created earlier by the *build-config*.
Please Note: the application.yml file uses the default values for _project, myproject,_ and IP supplied by _Virtualbox_. Therefore, before executing the following command, you may have to edit the values to adapt to your environment. 
For example: _xhyve_ virtual manager uses a different range for IP's. regardless of the IPs' do note to end the url ending with `nip.io`.
```
oc create -f application.yml
```
A route will be required for external visibility

```
oc expose
```
Then access the application via the resulting route. It should execute a fully functional Sportsstore Angular5 application served by `nginx v1.12, nodejs v8 and json server`. 

## TODO

See project issues.

