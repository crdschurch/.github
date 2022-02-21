# .github
Contains workflow templets for CI/CD with GitHub Actions 


# Github Actions Setup
This document will show you how to use enterprise templates to quickly and easily set up a new or existing repo to use github actions for it’s CI pipeline. This document assumes that you have access to github and knowledge about the configurations needed to build/deploy the repo in question.


## Log into Github
## Pull up the repo you wish to configure
## Set up Secrets
Github actions uses “secrets” to store sensitive data that shouldn’t be human readable by anyone. It also gives us a place to store configuration variables that will make using our predefined actions much easier. To add the needed configurations, go to the “Settings” section in your repo:

From there, scroll down to the bottom of the left hand menu and select “Secrets”

You should see a list of pre-defined organization level secrets. These are used to store variables that shouldn’t change from project to project. You shouldn’t need to do anything with these variables, but if you do, please contact Devy first.

Now, there are 2 repository level secrets that you will need to add to use the organizational templates we have created. You can do this by clicking the “New repository secret” button in the top right. The 2 secrets that need to be added are:

**New_Relic_App_Name**
This will be the name for your app in our monitoring system. The templates automatically suffix this value with environment, so all you need is a friendly name for your app. 
Example “Location API”

**Project_Solution_Name**
This needs to be the name of your solution file MINUS the .sln and should exactly match the file name of your solution file.

This value will be used for building the correct solution file AND for naming the docker containers that are created and used during the build process.
Example “crds-service-location”

## Set up Actions
Next we will add the actual github actions that will build and deploy your application. To get to this screen, use the “Actions” toolbar option


## Click the “New workflow” button in the list on the left

## Add pre built Crossroads Actions
Scroll down a bit and find the “By Crossroads Church” section. Select the relevant build types you wish to add
When you click the “Configure” button, github will take you to a page that will create a new file in the correct location for the action to work. You shouldn’t need to modify anything in the actions file, but you can if you want/need to. Once you are happy with the file, you can use the “Start Commit” button in the top right corner.

## Repeat the above step for other environments


# Optional build steps

## Database sync scripting
If you would like to store DB change scripts in your repo, you can add the following GHA step to automatically run those scripts against different environments.

-----------------------------------------------
- name: Run SQL Scripts \
  uses: wei/curl@master \
  with: \
    args: 'https://api-int.crossroads.net/dbsync/sync/process?key=${{ secrets.DBSYNC_RUN_KEY }}&Repo=${{ github.event.repository.name }}&Env=${{ env.CRDS_ENV }}'
----------------------------------------------- 
	 
For this feature to work, there are a few things you will need:

**Folder structure** \
Script files must live in the following folders (they are case sensative) \
/deployment/DBScripts/MP/Data       (Any data insert/update/delete scripts) \
/deployment/DBScripts/MP/SPs	    (Stored procedure scripts, must be "CREATE OR ALTER XXXX") \
/deployment/DBScripts/MP/Structure  (Table/Field addition,removal, etc)

**Immutable** \
Any script that you create must be runnable multiple times without breaking anything. The script sync process runs **ALL** of the scripts in the above folders everytime it is triggered. This is why your SP scripts must be "Create or Alter" and all of your data/structure changes should be wrapped in an IF that checks to see if the change has already been applied to the DB before running.  \
Example:
---------------------------------------------------

---------------------------------------------------

# Important Required Settings
The current templates provided all require that your project contain certain files in specific places if they are going to work properly. These files are used to build docker containers and deploy the containers to K8S. Here is the list of files and locations (all are relative to location of your SLN file)

/deployment/docker/docker-compose.yml		Used to pass config data to DockerFile

/deployment/docker/dockerfile			Config steps for building your service

/deployment/kubernetes/deployment.yml		Configs for K8s deployment

/deployment/kubernetes/ingress.yml			Host URL info for K8s deployment

/deployment/kubernetes/service.yml			Port configs for K8s deployment


If you aren’t sure what these files are supposed to look like, you can get some samples in many of the crds-service-XXXX projects; for example crds-service-location. 


https://docs.google.com/document/d/1TbWRPWTklM63IPV-uS51ap6_nU8F73_nr9e-ZtXwfD0/edit 