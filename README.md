# TimeSeries-Forecasting

Project Description: In this assignment I am getting the data for the mentioned repositories for the past 2 years: 1.https://github.com/angular/angular 2.https://github.com/angular/material 3.https://github.com/angular/angular-cli 4.https://github.com/SebastianM/angular-google�maps 5.https://github.com/d3/d3 6.https://github.com/facebook/react 7.https://github.com/tensorflow/tensorflow 8.https://github.com/keras-team/keras 9.https://github.com/pallets/flask

I am getting the data from GITHUB API through a github token. I have used python and flask as a backend service and React and JavaScript for Front-End.

In flask I am recieving the data for all the repositories and using that data for time series forcasting.

To get the issues data for the repositories I have used GITHUB API and then stored that data in issue_response array. After this I converted this array to a dataframe so that I can plot all the requirements needed.

Implementing requirement 5 of the assignment: I have plotted the created_at and the closed_at issues for every repository using react. The data from flask is sent to react microservices as a key value pair. For the plotting of the line chart I have created a seperate java script file. This file uses the data sent by home.js file. home.js file sends the title and the data using props variable.

Implementing requirements 6,7,8,9 of the assignment: I have created a seperate javascript file for the plotting of these bar graphs. The file name is Barcharts.js. This file uses the data sent by home.js file. -- to plot the bar chart for total stars and forks I have extracted the values directly from the obtained json file.

Implementing requirements 10 of the assignment: I have created a seperate javascript file for the plotting of these stack-bar graphs. The file name is StackBarCharts. This file uses the data sent by home.js file.

To get the pulls data I have used https://api.github.com/search/issues?q=type:pr%20repo:angular/angular%20created:2020-04-22..2022-05-23&per_page=100 this api. This API gives the data for the past 2 years of created and closed issues. I have saved the pulls created date and the pulls closed date for all the pulls. This data would be later used for the forcasting.

To get the commits I have used https://api.github.com/search/commits?q=repo:pallets/flask%20committer-date:2020-04-23..2022-05-23&per_page=100 this api. This API gives the data for the past 2 years of created commits. I have saved the created date for all the commits. This data would be later used for the forcasting.

Now, for the forcasting I have created json objects containing key value pairs. The first key value pair consists of the data which contains the created and closed data obtained from the repositories. The second key value pair contains the type of data eg created_data or the closed_data. The fourth repository contains the repo name for which the data is obtained.

I am connecting the flast microservices from a URL that is obtained from the cloud run.

Now, I am performing a request.post request. In this request I am sending the above generated URL and the json object mentioned above. The value obtained from the json file is used to for forcasting using LSTM and statsmodel.

I have created a single app.py file for the forcast of LSTM and statsmodel. This file is named as LSTM-forecast.

Steps to deploy on google cloud

a) I have created 3 google cloud projects. --lstm-ms --flask-ms --react-ms b) Steps for LSTM Deploying LSTM to gcloud platform 1: I downloaded Docker(https://www.docker.com/get-started) and Google Cloud SDK(https://cloud.google.com/sdk/docs/install) installed on your computer.
2. Steps to follow while creating LSTM gcloud project: 1. Go to GCP Platform, create a LSTM gcloud project.enable the following: a.billing account b.Conatiner Registry API c.Cloudbuild API 2. After creating the LSTM gcloud project follow the below steps: a. go to gcloud storage of LSTM gcloud project then click on create bucket. b. then add name to your bucket (bucket name should be unique) click on continue. c. then choose where to store your data. d. there choose location type to "region" and location to "us-central1(Iowa)", then click on continue. e. then Choose a default storage class for your data f. choose option "standard" and click continue and then click on create then you will automatically navigated to "bucket details" g. there you will see "objects" and in "objects" you will see "buckets" click on "buckets", you will be able to see your assigned bucket name with checkbox h. click on checkbox, then on the right side you will be able to see "permission" and "labels",in "permission" scroll down you will be able to see "ADD PRINCIPAL" i. click on "ADD PRINCIPAL" you will be able to see "new principals" and "select a role", in "new principals" type "allUsers" and in "select a role" go to "cloud storage" click on "cloud storage" and select "storage object viewer" and hit on save after this j. copy the BUCKET_NAME (your unique assigned bucket name) and paste it in sticky notes for futher use. 3. After this, go to "https://cloud.google.com/docs/authentication/getting-started#create-service-account-console" and there you will see creating service account, go to creating service account, click on console, and hit on creating service account 4. Then select your LSTM created project, in service account details add service name "your unique service name"(service name should be unique) and click on "create and continue", then in Grant this service account access to project click on "select the role",choose "basic" and choose "owner"and hit on done. 5. After that you will see your created service, click on the created service, go to keys, click on add key, and click create new key. 6. Created key will get downloaded in .json format, copy that downloaded file in the given LSTM code 7. Then, on cmd terminal type "set GOOGLE_APPLICATION_CREDENTIALS=KEY_PATH" (Replace KEY_PATH with the path of the JSON file that contains your service account key.) (here the name of my json file is lstm-ms-347614-0922c89604eb)

   3: Type `docker` on cmd terminal and press enter to get all required information

   4: Type `docker build .` on cmd to build a docker image

   5: Type `docker images` on cmd to see our first docker image. After hitting enter, newest created image will be always on the top of the list

   6: Now type `docker tag <your newest image id> gcr.io/<your project-id>/<project-name>` and hit enter 
        Type `docker images` to see your image id updated with tag name

   7: Type `gcloud init` on cmd and it will prompt Create or select a configuration choose existing configurations and hit enter and
      it will prompt Choose a current Google Cloud project, choose your current gcloud project number and hit enter.
      

   8: Type `gcloud auth configure-docker` on cmd hit enter and then type "docker images"

   9: Type `docker push <your newest created tag>` on cmd and hit enter

   10: Go to container registry you will see your newly pushed docker image, click on that docker image, after clicking, you will be able to see
      docker image id and on the right side you will see "⋮", left click on "⋮" there you will be able to see option "deploy it to cloud run" click on that and 
      it will navigate you to cloud run where in container image url hit select and select your latest id and change the Min Instance to 1 instead of 0 and the option 
      to allow unauthorized access when creating new service and then go to container tab and edit container port to '8080', increase the memory limit 
      to 1GiB and go to variable and secrets tab and click on add environment variable as follows(there will three environment variable):
            Name                                 value  
        a. GOOGLE_APPLICATION_CREDENTIALS     "lstm-ms-347614-0922c89604eb.json"
        b. BASE_IMAGE_PATH                    "https://storage.googleapis.com/dev-lstm-ms/"
        c. BUCKET_NAME                         "dev-lstm-ms"
        
  NOTE: For the environment variable, ".json" and "/" is mandatory in there respective environment variable value
        because ".json" have all your gcloud information and secrets keys and "/" is a app route path.

   11: Hit the create, this will create the service on port 8080 and will generate the url, hit the url.

12. Copy the generated LSTM URL - https://lstm-ms-1-4iac4qqnca-uc.a.run.app
c) Steps for Flask

Deploying Flask to gcloud platform enable the following: a.billing account b.Conatiner Registry API c. Cloudbuild API

   2. Copy the LSTM project url from gcloud and paste it in app.js file "//lstm-ms-1-4iac4qqnca-uc.a.run.app/"
   (NOTE: DO NOT REMOVE "/")   

   3: Type `docker` on cmd terminal and press enter to get all required information

   4: Type `docker build .` on cmd to build a docker image

   5: Type `docker images` on cmd to see our first docker image. After hitting enter, newest created image will be always on the top of the list

   6: Now type `docker tag <your newest image id> gcr.io/<your project-id>/<project-name>` and hit enter 
        Type `docker images` to see your image id updated with tag name

   7: Type `gcloud init` on cmd and it will prompt Create or select a configuration choose existing configurations and hit enter and
      it will prompt Choose a current Google Cloud project, choose your current gcloud project number and hit enter.
      
   8: Type `gcloud auth configure-docker` on cmd hit enter and then type "docker images"

   9: Type `docker push <your newest created tag>` on cmd and hit enter

   10: Generate a Personal Access Token on GitHub by following below steps:
      a. Navigate to your Git account settings, then Developer Settings. Click the Personal access tokens menu, then click Generate new token.
      b. Select repo and public as the scope as shown in recording. 
      c. Click Generate Token.
      d. Copy the generated token and paste it in your sticky notes

   11 Go to container registry you will see your newly pushed docker image, click on that docker image, after clicking, you will be able to see
      docker image id and on the right side you will see "⋮", left click on "⋮" there you will be able to see option "deploy it to cloud run" click on that and 
      it will navigate you to cloud run where in container image url hit select and select your latest id and change the Min Instance to 1 instead of 0 and the option 
      to allow unauthorized access when creating new service and then go to container tab and edit container port to '5000', increase the memory limit 
      to 1GiB and go to variable and secrets tab and click on add environment variable as follows
           Name                     value
       a. GITHUB_TOKEN              "Your GitHub generated token"

   12: Click on create, this will create the service on port 5000 and will generate the url, hit the url.

   13: Copy flask gcloud generated url: https://flask-ms-1-3sgk2jxwwq-uc.a.run.app
c) Steps for React

Step 3: Prerequisites to work with this application: 1:You must have following installed on your computer: a.Docker(https://www.docker.com/get-started) b.Google Cloud SDK(https://cloud.google.com/sdk/docs/install) c.NodeJs (https://nodejs.org/en/download/) (You must install NodeJS if you want to run React application on your machine) 1. Rules to follow while installing NodeJs a. Go to the link (https://nodejs.org/en/download/) b. Click the Windows Installer button to download the latest default version and choose the location where you want to store Node.js. (The Node.js installer includes the NPM package manager.) c. Open the newly downloaded Node.js setup. and there you will be able to see "Welcome to the Node.js setup wizard" , "Next", and "cancel". Hit on "Next" d. After clicking next, you will be able to see "End-User Licence Agreement", one checkbox with "I accept the terms in the Licence Agreement", click on that checkbox e. After clicking on next, you will be able to see "Destination Folder", there you dont have to make any changes, you just have to click "next" f. After clicking next, you should see "Custom Setup" and there you have to click "next" g. After clicking next, you will able to see "Tools for Native Modules" and one checkbox where it is written"Automatically install the necessary tools. Note that this will also install chocolatey. The script will pop-up in a new window after installation completes", you have to click that checkbox and then click "next h. After clicking next. you will be able to see "Ready to install Node.js" there you have to click on "Install" and it will start installing i. After installation, it will show "completed the NOde.js Setup Wizard" there you have to click on "finish" j. After clicking on finish, you will navigate to "user access control" where you have to select "yes" k. After clicking on yes, it will navigate you to "Intall additional tools for Node.js", there it will ask for"press any key to continue" there you have to hit "enter" l. you have successfully installed Node.js, NPM and python(Chocolatey will automatically install the latest version of python) m. After you download and install Node, start your terminal/command prompt and run "node -v" and "npm -v" to see which versions you have. n. Your version of NPM should be at least 5.2.0 or newer because create-react-app requires that we have NPX installed. If you have an older version of NPM, run the command to update it:"npm install -g npm"

Step 4: Deploying React to gcloud platform 1: You must have Docker(https://www.docker.com/get-started) and Google Cloud SDK(https://cloud.google.com/sdk/docs/install) installed on your computer. Then, Create a gcloud project and enable the following: a.billing account b.Conatiner Registry API c. Cloudbuild API

    2: Copy the Flask project url from gcloud and paste it in src/setupProxy.js file line 14 where it is written "your_flask_gcloud_url"  

    3: Type `docker` on cmd terminal and press enter to get all required information

    4: Type `docker build .` on cmd to build a docker image

    5: Type `docker images` on cmd to see our first docker image. After hitting enter, newest created image will be always on the top of the list

    6: Now type `docker tag <your newest image id> gcr.io/<your project-id>/<project-name>` and hit enter 
        Type `docker images` to see your image id updated with tag name

    7: Type `gcloud init` on cmd and it will prompt Create or select a configuration choose existing configurations and hit enter and
      it will prompt Choose a current Google Cloud project, choose your current gcloud project number and hit enter.


    8: Type `gcloud auth configure-docker` on cmd hit enter and then type "docker images"

    9: Type `docker push <your newest created tag>` on cmd and hit enter

    10:Go to container registry you will see your newly pushed docker image, click on that docker image, after clicking, you will be able to see
      docker image id and on the right side you will see "⋮", left click on "⋮" there you will be able to see option "deploy it to cloud run" click on that and 
      it will navigate you to cloud run where in container image url hit select and select your latest id and change the Min Instance to 1 instead of 0 and the option 
      to allow unauthorized access when creating new service and then go to container tab and edit container port to '3000', increase the memory limit to 1GiB 
      and click on create.
        
    11: This will create the service on port 3000 and will generate the url, hit the url.   
FINAL GENERATED URL: https://react-ms-1-3mvywwgouq-uc.a.run.app/

The steps to generate the 3 project files were told in the class and I have followed the same steps.
