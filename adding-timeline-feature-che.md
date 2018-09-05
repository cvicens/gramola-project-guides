## Adding the Timeline Feature
As time goes by you need to add new features to your systems... Let's imagine we want to add a Timeline like feature so that a user can see and add timestamped comments during an event.

This is a potential addition to our storyboard to fulfill the new need.

> Once a user selects and events it should be possible see the timeline of her comments related to the event.
	![List of events]({% image_path gramola-timeline.png %}){:width="300px"}

In order to provide this new feature we need to create a 'Timeline' service, and as before we're going to start at the Red Hat Developer Launch.

Again, before we actually generate our base code we need to log in [here](https://developers.redhat.com/launch/).
![Login]({% image_path launch-site-login.png %}){:width="700px"}

After successfully loging in you'll land here, where you have to click on the 'LAUNCH YOUR PROJECT' button to access the Missions area.

![Launch]({% image_path launch-site.png %}){:width="700px"}

#### Generate Base Application Code
Please enter 'timeline' as the name of the application and click 'Continue'.
![Create application]({% image_path launch-site-create-application-timeline.png %}){:width="700px"}

In this lab we're going to generate a skeleton for our File Service based on Node.js and modify it locally to run it later in our own Openshift cluster. So, 
let's select 'Build and Run Locally' as our target enviroment. Should you wanted to use your own Red Hat operated Openshift cluster you should choose 'Code Locally, Build and Deploy'.
![Build and Run Locally]({% image_path launch-site-build-run-timeline.png %}){:width="700px"}

Now let's choose 'Rest API Level 0' and Node.js 8.x (RHOAR) as our mission and runtime.

![Select Mission and Runtime]({% image_path launch-site-mission-runtime-timeline.png %}){:width="700px"}

Time to generate our Node.js REST enabled sample application to be the base of our File Service. Please click on 'Set Up Application' and a zip file should download automatically. 
![Set Up Application]({% image_path launch-site-setup-application-timeline.png %}){:width="700px"}

You should notice a file being downloaded. This zip file contains the base code we need.

> **IMPORTANT:** Unfortunately the naming is not correct and the zip file is named as 'booster.zip' instead of 'files.zip'. Please rename this file as **'booster-timeline.zip'**.

![Next Steps]({% image_path launch-site-next-steps-timeline.png %}){:width="700px"}

#### Import our base code into an Eclipse Che Workspace
Go to the [Eclipse Che url]({{ ECLIPSE_CHE_URL }}) in order to configure your development workspace: {{ ECLIPSE_CHE_URL }}

Let's upload the file we just generated (should be 'booster-timeline.zip' if you renamed as instructed). Please click on **'{{PROJECT_FOLDER_NAME}}'** in the Project Explorer on the left and then select **'Project→Upload File'**.

> **WARNING: If you don't select a project in the Project Explorer the option 'Upload File' is not displayed.**

![Upload booster 1]({% image_path timeline-service-upload-booster-1.png %}){:width="700px"}

Choose our booster file and click on **'Upload'**.
![Upload booster 2]({% image_path timeline-service-upload-booster-2.png %}){:width="700px"}

At this point our file is up and we have to unzip it.

![Upload booster 3]({% image_path timeline-service-upload-booster-3.png %}){:width="700px"}

Go the Che terminal and run this commands.

~~~shell
$ cd $CHE_PROJECTS_ROOT/{{PROJECT_FOLDER_NAME}}
$ unzip booster-timeline.zip
~~~

> **WARNING:** Maybe you'd need to change the folder name from 'booster' to 'timeline'.
> 
> ~~~shell
> $ mv booster timeline
> ~~~

One more step before we can work with our code. Right click on 'timeline' folder on the Project Explorer and select **'Convert To Project'**.
![Convert To Project 1]({% image_path timeline-service-convert-to-project-1.png %}){:width="700px"}

Select NODE.JS/Node.js and click **'Save'**.
![Convert To Project 2]({% image_path timeline-service-convert-to-project-2.png %}){:width="700px"}

#### Test and Deploy before making any changes
Please open the Che terminal window and check Node.js 8.x is available.

~~~shell
$ node --version
v8.11.1
$ npm --version
5.6.0
~~~

At this time 'oc' tool should be properly installed and you should be logged in already. If not, please go back to 'Getting Started - Explore OpenShift with OpenShift CLI' and login as suggested. Then go to your project as shown here.

~~~shell
$ oc project {{PROJECT_NAME}}
~~~

Open file **'package.json'** and replace 'booster' with 'timeline', which is the name we want for our service. Next picture shows 'package.json' file after the change.

![Replacing booster]({% image_path timeline-service-replace-booster-with-timeline.png %}){:width="700px"}

Let's install packages and run our base code.

~~~shell
$ cd $CHE_PROJECTS_ROOT/{{PROJECT_FOLDER_NAME}}/timeline
$ npm install && npm start
...
> timeline@1.0.0-SNAPSHOT postinstall /projects/gramola-project/timeline
> license-reporter report -s && license-reporter save -s --xml licenses.xml


> timeline@1.0.0-SNAPSHOT prepare /projects/gramola-project/timeline
> echo 'To confirm CVE compliance, run "npm run security-check"'

To confirm CVE compliance, run "npm run security-check"
added 1189 packages in 112.009s

> timeline@1.0.0-SNAPSHOT start /projects/gramola-project/timeline
> node .
~~~

Now let's check the traditional 'hello world' works properly, to do so, open a new terminal window. Click on the '+' sign and click on terminal...

![New Che terminal]({% image_path new-che-terminal.png %}){:width="700px"}

...and run the following.

~~~shell
$ curl http://localhost:8080/api/greeting
{"content":"Hello, World!"}
~~~

Finally let's check our application can be deployed to our Openshift cluster directly. Go back to the other terminal window and type 'Ctrl-C' to stop the process, later use the `oc` tool to check we're at {{PROJECT_NAME}} and run `npm run openshift` to deploy our code to Openshift.

~~~shell
$ oc project
Using project "{{PROJECT_NAME}}" on server "{{OPENSHIFT_CONSOLE_URL}}".
$ npm run openshift

> files@1.0.0-SNAPSHOT openshift /Users/cvicensa/openshift/gramola-cicd/timeline
> nodeshift --strictSSL=false --dockerImage=registry.access.redhat.com/rhoar-nodejs/nodejs-8

2018-08-14T15:31:31.976Z INFO loading configuration
...
2018-08-14T15:31:56.759Z INFO route host mapping timeline-gramola-cicd.apps.192.168.50.100.nip.io
2018-08-14T15:31:56.759Z INFO complete
~~~

Now our base code has been packed, a container image has been generated using Openshift Source to Image (s2i), along with some assets, such as an image stream, a deployment configuration, a service and even a route accesible from the outside of our cluster.

To understand this have a look to folder **'.nodeshift'**, there you'll find the following files.

~~~shell
$ ls .nodeshift/
deployment.yml	route.yml
~~~

Let's check again if the service works now using the route generated as part of the  'npm run openshift' command.

~~~shell
$ oc get route
NAME      HOST/PORT                                       PATH      SERVICES   PORT      TERMINATION   WILDCARD
{{TIMELINE_SERVICE_NAME}}   {{TIMELINE_SERVICE_NAME}}-{{PROJECT_NAME}}.{{BASE_APPS_HOST}}             {{TIMELINE_SERVICE_NAME}}      8080                    None
...
$ curl http://{{TIMELINE_SERVICE_NAME}}-{{PROJECT_NAME}}.{{BASE_APPS_HOST}}/api/greeting
{"content":"Hello, World!"}
~~~


#### Modify Base Application Code
So far we have tested the base code, and we're ready to add packages and additional assets so that we have our Timeline Services and not just the sample code deployed.

In summary we have to:

* Deploy a database to store timeline entries
* Add additional packages with 'npm install'
* Copy/Modify source code to acutally provide the file management features

Before starting creating YAML files for our assets, let's create a folder for our resources under our 'files' folder, we're going to name it **'base-resources'**. 

> If you close all your Che terminal windows, or want to start in a new one, you can use the '+' sign and select terminal. If you do, then change directory: `cd $CHE_PROJECTS_ROOT/{{PROJECT_FOLDER_NAME}}/files`.

~~~shell
$ mkdir base-resources
~~~

> Note: Either your using Minishift/Cluster Up or a proper Openshift cluster are already created Persistent Volumes have to be ready to use. Cluster Up and Minishift should have already created PVs for you, in a proper cluster an administrator should have take care of this.

##### Deploy Timeline Database
In order to deploy the Timeline database we have to:

* Create a configmap and a secret to hold properties to define the database
* Add a Persistent Volume (or use an existing one)
* Create a Persistent Volume Claim
* Associate a PVC with a volume for the POD to consume
* Deploy a service for the database

Create a configmap descriptor:

*  database_name: "timeline"
*  database_service_name: "timeline-database"
 * database_service_port: "27017"

~~~shell
$ cat << EOF > ./base-resources/configmap.yml
apiVersion: v1
kind: ConfigMap
metadata:
     name: timeline-configmap
data:
  database_name: "timeline"
  database_service_name: "timeline-database"
  database_service_port: "27017"
EOF
~~~

Create a secret descriptor:

* user: "luke"
* password: "secret"
* admin-password: "secret"
  
~~~shell
$ cat << EOF > ./base-resources/secret.yml
apiVersion: "v1"
kind: "Secret"
metadata:
  name: "timeline-database-secret"
stringData:
  user: "luke"
  password: "secret"
  admin-password: "secret"
EOF
~~~

Create a PVC (Persistence Volume Claim) named 'timeline-database'.

~~~shell
$ cat << EOF > ./base-resources/database.pvc.yml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: timeline-database
  annotations:
    description: Defines the PVC associated to MongoDB
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
EOF
~~~

Create a deployment configuration descriptor for our database.

~~~shell
$ cat << EOF > ./base-resources/database.dc.yml
kind: DeploymentConfig
apiVersion: v1
metadata:
  name: timeline-database
  labels:
    application: timeline
    deploymentConfig: timeline
  annotations:
    description: Defines how to deploy the database
spec:
  replicas: 1
  selector:
    name: timeline-database
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        name: timeline-database
        application: timeline
      name: timeline-database
    spec:
      containers:
      - env:
        - name: MONGODB_USER
          valueFrom:
            secretKeyRef:
              name: timeline-database-secret
              key: user
        - name: MONGODB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: timeline-database-secret
              key: password
        - name: MONGODB_ADMIN_PASSWORD
          valueFrom:
            secretKeyRef:
              name: timeline-database-secret
              key: admin-password
        - name: MONGODB_DATABASE
          valueFrom:
              configMapKeyRef:
                name: timeline-configmap
                key: database_name
        image: mongodb:3.2
        livenessProbe:
          initialDelaySeconds: 30
          tcpSocket:
            port: 27017
          timeoutSeconds: 1
        name: mongodb
        ports:
        - containerPort: 27017
        readinessProbe:
          exec:
            command:
            - /bin/sh
            - -i
            - -c
            - mongo 127.0.0.1:27017/\$MONGODB_DATABASE -u \$MONGODB_USER -p \$MONGODB_PASSWORD
              --eval="quit()"
          initialDelaySeconds: 3
          timeoutSeconds: 1
        resources:
          limits:
            memory: 256Mi
            cpu: 200m
        volumeMounts:
        - mountPath: /var/lib/mongodb/data
          name: timeline-database-data
      volumes:
      - name: timeline-database-data
        persistentVolumeClaim:
          claimName: timeline-database
  triggers:
  - imageChangeParams:
      automatic: true
      containerNames:
      - mongodb
      from:
        kind: ImageStreamTag
        name: mongodb:3.2
        namespace: openshift
    type: ImageChange
  - type: ConfigChange
EOF
~~~

Create a service descriptor for our database.

~~~shell
$ cat << EOF > ./base-resources/database.svc.yml
kind: Service
apiVersion: v1
metadata:
  name: timeline-database
  annotations:
    description: Exposes the database server
    template.openshift.io/expose-uri: mongodb://{.spec.clusterIP}:{.spec.ports[?(.name=="mongo")].port}
spec:
  ports:
  - name: mongodb
    port: 27017
    targetPort: 27017
  selector:
    name: timeline-database
EOF
~~~

In the following deployment configuration snippet, there are interesting elements:

* Environment variables filled in with either a secret or a configmap
* Resource restrictions
* Updated liveness and readiness probes

~~~shell
$ cat << EOF > ./.nodeshift/deployment.yml
spec:
  template:
    spec:
      containers:
        - env:
            - name: DB_USERNAME
              valueFrom:
                 secretKeyRef:
                   name: timeline-database-secret
                   key: user
            - name: DB_PASSWORD
              valueFrom:
                 secretKeyRef:
                   name: timeline-database-secret
                   key: password
            - name: DB_ADMIN_PASSWORD
              valueFrom:
                 secretKeyRef:
                   name: timeline-database-secret
                   key: password
            - name: DB_SERVICE_NAME
              valueFrom:
                 configMapKeyRef:
                   name: timeline-configmap
                   key: database_service_name
            - name: DB_SERVICE_PORT
              valueFrom:
                 configMapKeyRef:
                   name: timeline-configmap
                   key: database_service_port
            - name: DB_NAME
              valueFrom:
                 configMapKeyRef:
                   name: timeline-configmap
                   key: database_name
          resources:
            limits:
              cpu: 200m
              memory: 200Mi
            requests:
              cpu: 100m
              memory: 100Mi
          livenessProbe:
            httpGet:
              path: /api/health/liveness
              port: 8080
              scheme: HTTP
            initialDelaySeconds: 10
            periodSeconds: 5
            timeoutSeconds: 1
          readinessProbe:
            httpGet:
              path: /api/health/readiness
              port: 8080
              scheme: HTTP
            initialDelaySeconds: 10
            periodSeconds: 5
            timeoutSeconds: 1
EOF
~~~

So far we have created descriptor for some assets, now we have to actually create them in our Openshift cluster.

> Note: Assets will be created in the current project or you have to add -n NAME_SPACE to the command to specify the destination namespace

~~~shell
$ oc create -f base-resources/
configmap "timeline-configmap" created
deploymentconfig.apps.openshift.io "timeline-database" created
persistentvolumeclaim "timeline-database" created
service "timeline-database" created
secret "timeline-database-secret" created
~~~

##### Add additional packages
Our code uses a module called [**mongodb**](https://www.npmjs.com/package/mongodb) (MongoDB driver), [**mongoose**](https://www.npmjs.com/package/mongoose) (object modelling for MongoDB).
Additionally we use [**'cors'**](https://www.npmjs.com/package/cors) for cross-origin resource sharing.

Let's install these packages doing as follows.

> **WARNING: Be sure package.json file is closed in Eclipse Che before running the next command.**

~~~shell
$ npm install mongodb mongoose cors --save
~~~

===> TODO Carlos: explain this in detail or delete...

**Optional**
To make the life of the developer easier there are means to reload a node app if a file is updated, you can also install [**'nodemon'**](https://www.npmjs.com/package/nodemon) 
In addition to installing the package we need to add a script entry to file package.json

===> TODO

##### Copy/Modify source code
Finally it's time to add and update some source code.

Open file 'app.js' by double-clicking on it in the Project Explorer and substitute with the following code.

~~~javascript
'use strict';

const path = require('path');
const express = require('express');
const bodyParser = require('body-parser');

const _mongoose = require('./lib/mongoose-wrapper');
_mongoose.initDatabase();

const app = express();

app.use(bodyParser.json());
app.use(bodyParser.urlencoded({extended: false}));
app.use('/', express.static(path.join(__dirname, 'public')));

// Expose the license.html at http[s]://[host]:[port]/licences/licenses.html
app.use('/licenses', express.static(path.join(__dirname, 'licenses')));

// Hello World endpoint
app.use('/api/greeting', (request, response) => {
  const name = request.query ? request.query.name : undefined;
  response.send({ content: `Hi there, ${name || 'World!'}` });
});

// TODO: Add timeline API
app.use('/api/timeline', require('./lib/timeline.js')());

// TODO: Add liveness and readiness probes
app.use('/api/health/liveness', (request, response) => {
  if (_mongoose.getDatabaseInitTries() <= 0) {
    console.log('liveness', false);
    response.status(500).send({ status: 'failure', message: 'databaseInitTries reached zero' });
    return;  
  }
  console.log('liveness', true);
  response.send({ status: 'success' });
});

app.use('/api/health/readiness', (request, response) => {
  console.log('readiness', _mongoose.readiness());
  response.status(_mongoose.readiness() ? 200 : 500 ).send({ status: _mongoose.readiness() ? 'success' : 'failure' });
});

module.exports = app;
~~~

Timeline API code is at ./lib/timeline.js and that file doesn't exist yet, additionally there is a convenient wrapper for mongoose configuration at './lib/mongoose-wrapper.js'. Let's take care of this issue. Go again to the Che terminal and do as follows.

> **Note:** If you cannot see new files added from the terminal in the Project Explorer, right click on the project and select 'Refresh'

~~~shell
$ mkdir lib
$ curl {{PROJECT_GIT_RAW_URL}}/timeline/lib/timeline.js -o ./lib/timeline.js
$ curl {{PROJECT_GIT_RAW_URL}}/timeline/lib/mongoose-wrapper.js -o ./lib/mongoose-wrapper.js
~~~

Before we proceed with testing the code locally let's modify entry 'files' in **package.json** because command 'npm run openshift' will take care only of files/directories specified in this entry. After adding "lib" 'files' entry should be.

> In package.json 'files' refers to the files/folders nodeshift through command 'npm run openshift' will take care of to generate the container image with our code.

~~~json
"files": [
    "package.json",
    "app.js",
    "public",
    "bin",
    "LICENSE",
    "licenses",
    "lib"
  ]
~~~

##### Let's test our code locally
Before we test our code, let's set up some environment variables.

~~~shell
export DB_USERNAME=luke
export DB_PASSWORD=secret
export DB_NAME=timeline
export DB_SERVICE_NAME=timeline-database.user1-gramola-cicd.svc
export DB_SERVICE_PORT=27017
~~~

It's time to test our code locally, so.

~~~shell
$ npm start
~~~

And from another terminal we're going to create some timeline entries for userId **'trever'**:

* 2 for the Guns'n'Roses event (eventId = 1)
* 2 for the Foo Fighters event (eventId = 2)

~~~shell
export SERVICE_URL=http://localhost:8080/api/timeline
curl --header "Content-Type: application/json" \
  --request POST \
  --data '{
    "id": 1,
    "eventId": 1,
    "userId": "trever",

    "title": "Waiting at the entrance!",

    "date": "2018-04-27",
    "time" : "18:00",

    "body": "Lorem ipsum...",
    "image": "images/guns-P1080795.png" }' \
  ${SERVICE_URL}
  
 curl --header "Content-Type: application/json" \
  --request POST \
  --data '{
    "id": 2,
    "eventId": 1,
    "userId": "trever",

    "title": "Checking in!",

    "date": "2018-04-27",
    "time" : "18:32",

    "body": "Lorem ipsum...",
    "image": "images/guns-P1080795.png" }' \
  ${SERVICE_URL}
  
curl --header "Content-Type: application/json" \
  --request POST \
  --data '{
    "id": 3,
    "eventId": 2,
    "userId": "trever",

    "title": "At the entrance...",

    "date": "2018-07-27",
    "time" : "20:31",

    "body": "Lorem ipsum...",
    "image": "images/guns-P1080795.png" }' \
  ${SERVICE_URL}
  
curl --header "Content-Type: application/json" \
  --request POST \
  --data '{
    "id": 4,
    "eventId": 2,
    "userId": "trever",

    "title": "About to start...",

    "date": "2018-07-27",
    "time" : "20:46",

    "body": "Lorem ipsum...",
    "image": "images/guns-P1080795.png" }' \
  ${SERVICE_URL}
~~~

For each of the objects posted you should get a response like the next one.

~~~json
{
   "_id":"5b8e4546545687035ce10b87",
   "id":"4",
   "eventId":"2",
   "userId":"trever",
   "title":"About to start...",
   "date":"2018-07-27",
   "time":"20:46",
   "body":"Lorem ipsum...",
   "image":"images/guns-P1080795.png",
   "__v":0
}
~~~

You can now stop the process with 'Ctrl-C'.

##### Deploy this version of our application to Openshift
Before we redeploy and because we have modified the deployment configuration we have to delete it first.

~~~shell
$ oc delete dc {{TIMELINE_SERVICE_NAME}}
~~~

Now we can use nodeshift to deploy our new code.

~~~shell
$ npm run openshift

> timeline@1.0.0-SNAPSHOT openshift /projects/gramola-project/timeline
> nodeshift --strictSSL=false --dockerImage=registry.access.redhat.com/rhoar-nodejs/nodejs-8
...
2018-08-14T19:15:01.557Z INFO complete
~~~

##### Tests after deployment
Check if 'timeline' pod is up and running.

~~~shell
$ oc get po -w
NAME                READY     STATUS      RESTARTS   AGE
...
timeline-1-v7qlk   1/1       Running   0         13s
...
~~~

Find the route to the service.

~~~shell
$ oc get route
NAME      HOST/PORT                                       PATH      SERVICES   PORT      TERMINATION   WILDCARD
{{TIMELINE_SERVICE_NAME}}   {{TIMELINE_SERVICE_NAME}}-{{PROJECT_NAME}}.{{BASE_APPS_HOST}}             {{TIMELINE_SERVICE_NAME}}      8080   None
~~~

Let's get the timeline entries we just created for the Gun'n'Roses event (**#1**) and userId **'trever'**.

~~~shell
$ curl http://{{TIMELINE_SERVICE_NAME}}-{{PROJECT_NAME}}.{{BASE_APPS_HOST}}/api/timeline/api/timeline/1
/trever
[
   {
      "_id":"5b8e4542545687035ce10b84",
      "id":"1",
      "eventId":"1",
      "userId":"trever",
      "title":"Waiting at the entrance!",
      "date":"2018-04-27",
      "time":"18:00",
      "body":"Lorem ipsum...",
      "image":"images/guns-P1080795.png",
      "__v":0
   },
   {
      "_id":"5b8e4542545687035ce10b85",
      "id":"2",
      "eventId":"1",
      "userId":"trever",
      "title":"Checking in!",
      "date":"2018-04-27",
      "time":"18:32",
      "body":"Lorem ipsum...",
      "image":"images/guns-P1080795.png",
      "__v":0
   }
]
~~~

Well done! You are ready to move on to the next lab.
