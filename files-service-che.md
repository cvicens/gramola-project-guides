## File Service with Eclipse Che
To create this service and run it later in Openshift the Red Hat Developer Launch site provides us with so-called missions, wizzard-like accelerators.

In order to enjoy them you need to have a Red Hat Developer account and log in [here](https://developers.redhat.com/launch/) as in the next picture.
![Login]({% image_path launch-site-login.png %}){:width="700px"}

After a successful login you'll land here, where you have to click on the 'LAUNCH YOUR PROJECT' button to access the Missions area.

#### Generate Base Application Code
Please enter 'files' as the name of the application and click 'Continue'.
![Create application]({% image_path launch-site-create-application-files.png %}){:width="700px"}

In this lab we're going to generate a skeleton for our File Service based on Node.js and modify it locally to run it later in our own Openshift cluster. So, 
let's select 'Build and Run Locally' as our target enviroment. Should you wanted to use your own Red Hat operated Openshift cluster you should choose 'Code Locally, Build and Deploy'.
![Build and Run Locally]({% image_path launch-site-build-run-files.png %}){:width="700px"}

Now let's choose 'Rest API Level 0' and Node.js 8.x (RHOAR) as our mission and runtime.

![Select Mission and Runtime]({% image_path launch-site-mission-runtime-files.png %}){:width="700px"}

Time to generate our Node.js REST enabled sample application to be the base of our File Service. Please click on 'Set Up Application' and a zip file should download automatically. 
![Set Up Application]({% image_path launch-site-setup-application-files.png %}){:width="700px"}

You should notice a file being downloaded. This zip file contains the base code we need.

> Unfortunately the naming is not correct and the zip file is named as 'booster.zip' instead of 'files.zip'

![Next Steps]({% image_path launch-site-next-steps-files.png %}){:width="700px"}

#### Import our base code into an Eclipse Che Workspace
In the project explorer pane, click on the **Import Projects...** and enter the following:

  * Type: `ZIP`
  * URL: `{{LABS_DOWNLOAD_URL}}`
  * Name: `labs`
  * Check **Skip the root folder of the archive**

![Eclipse Che - Import Project]({% image_path bootstrap-che-import.png %}){:width="700px"}

Click on **Import**. Make sure you choose the **Blank** project configuration since the zip file contains multiple 
project skeletons. Click on **Save**

![Eclipse Che - Import Project]({% image_path bootstrap-che-import-save.png %}){:width="700px"}

The projects are imported now into your workspace and is visible in the project explorer.

Eclipse Che is a full featured IDE and provides language specific capabilities for various project types. In order to 
enable these capabilities, let's convert the imported project skeletons to a Maven projects. 

In the project explorer, right-click on **catalog-spring-boot** and then click on **Convert to Project**.

![Eclipse Che - Convert to Project]({% image_path bootstrap-che-convert.png %}){:width="600px"}

Choose **Maven** from the project configurations and then click on **Save**

![Eclipse Che - Convert to Project]({% image_path bootstrap-che-maven.png %}){:width="700px"}

Repeat the above for **inventory-wildfly-swarm** and **gateway-vertx** projects.

Note the **Terminal** window in Eclipse Che. For the rest of these labs, anytime you need to run 
a command in a terminal, you can use the Eclipse Che **Terminal** window.

![Eclipse Che - Terminal]({% image_path bootstrap-che-terminal.png %})

#### Test and Deploy before changes
Please open a terminal window and check Node.js 8.x is available.

~~~shell
$ node --version
v8.11.1
$ npm --version
5.6.0
~~~

Create a directory for our project and change to it.

~~~shell
$ mkdir {{PROJECT_NAME}}
$ cd {{PROJECT_NAME}}
~~~

At this time 'oc' tool should be properly installed and you should be logged in already. If not, please go back to 'Getting Started - Explore OpenShift with OpenShift CLI' and login as suggested. Then go to your project as shown here.

~~~shell
$ oc project {{PROJECT_NAME}}
~~~

Copy move the zip file downloaded to our project directory.

~~~shell
$ mv ~/Downloads/booster.zip .
$ unzip booster.zip 
Archive:  booster.zip
   creating: booster/
  inflating: booster/.gitignore      
 ...
inflating: booster/README.adoc  
~~~

Let's rename the directory as 'files' and open your file editor to show the file structure. We're going to use Code, please choose the one you like.

~~~shell
$ mv booster files
$ cd files
$ code .
~~~

Replace 'booster' with 'files', which is the name we want for our service and let's test the app first locally. Next picture shows how we did it with Code.

![Replacing booster]({% image_path replace-booster-with-files.png %}){:width="700px"}

Let's install packages and run our base code.

~~~shell
$ npm install && npm start

> booster@1.0.0-SNAPSHOT postinstall /Users/cvicensa/openshift/gramola-cicd/files
> license-reporter report -s && license-reporter save -s --xml licenses.xml


> booster@1.0.0-SNAPSHOT prepare /Users/cvicensa/openshift/gramola-cicd/files
> echo 'To confirm CVE compliance, run "npm run security-check"' 

To confirm CVE compliance, run "npm run security-check"
added 1189 packages in 16.833s

> booster@1.0.0-SNAPSHOT start /Users/cvicensa/openshift/gramola-cicd/files
> node .
~~~

Now let's check the traditional 'hello world' works properly, to do so, open a new terminal window and run the following.

~~~shell
$ curl http://localhost:8080/api/greeting
{"content":"Hello, World!"}
~~~

Finally let's check our application can be deployed to our Openshift cluster directly.

~~~shell
$ oc project
Using project "gramola-cicd" on server "https://192.168.50.100:8443".
$ npm run openshift

> files@1.0.0-SNAPSHOT openshift /Users/cvicensa/openshift/gramola-cicd/files
> nodeshift --strictSSL=false --dockerImage=registry.access.redhat.com/rhoar-nodejs/nodejs-8

2018-08-14T15:31:31.976Z INFO loading configuration
2018-08-14T15:31:32.007Z INFO using namespace gramola-cicd at https://192.168.50.100:8443
2018-08-14T15:31:32.015Z INFO creating archive of package.json, app.js, public, bin, LICENSE, licenses
2018-08-14T15:31:32.126Z INFO creating build configuration files-s2i
2018-08-14T15:31:32.127Z INFO Using s2i image registry.access.redhat.com/rhoar-nodejs/nodejs-8 with tag latest
2018-08-14T15:31:32.286Z INFO creating image stream files
2018-08-14T15:31:32.363Z INFO uploading binary archive /Users/cvicensa/openshift/gramola-cicd/files/tmp/nodeshift/build/archive.tar
2018-08-14T15:31:35.708Z INFO binary upload complete
2018-08-14T15:31:35.709Z INFO waiting for build to finish
2018-08-14T15:31:40.703Z TRACE ---> Installing application source
2018-08-14T15:31:40.703Z TRACE ---> Building your Node application from source
2018-08-14T15:31:40.703Z TRACE Current git config
2018-08-14T15:31:40.704Z TRACE url.https://github.com.insteadof=git@github.com:
2018-08-14T15:31:40.704Z TRACE url.https://.insteadof=ssh://
2018-08-14T15:31:40.704Z TRACE url.https://github.com.insteadof=ssh://git@github.com
2018-08-14T15:31:40.704Z TRACE ---> Installing dependencies
2018-08-14T15:31:40.704Z TRACE ---> Using 'npm install -s --only=production'
2018-08-14T15:31:48.707Z TRACE added 195 packages in 7.725s
2018-08-14T15:31:48.731Z TRACE ---> Cleaning up npm cache
2018-08-14T15:31:48.793Z TRACE ---> Setting directory write permissions
2018-08-14T15:31:50.365Z TRACE 
2018-08-14T15:31:50.365Z TRACE Pushing image 172.30.1.1:5000/gramola-cicd/files:latest ...
2018-08-14T15:31:50.790Z TRACE Pushed 5/6 layers, 96% complete
2018-08-14T15:31:51.060Z TRACE Pushed 6/6 layers, 100% complete
2018-08-14T15:31:51.331Z TRACE Push successful
2018-08-14T15:31:52.489Z INFO build files-s2i-1 complete
2018-08-14T15:31:56.373Z INFO openshift.yaml and openshift.json written to /Users/cvicensa/openshift/gramola-cicd/files/tmp/nodeshift/resource/
2018-08-14T15:31:56.461Z INFO creating deployment configuration files
2018-08-14T15:31:56.514Z INFO creating new service files
2018-08-14T15:31:56.569Z INFO creating new route files
2018-08-14T15:31:56.759Z INFO route host mapping files-gramola-cicd.apps.192.168.50.100.nip.io
2018-08-14T15:31:56.759Z INFO complete
~~~

Now our base code has been packed, an container image has been generated using Openshift Source to Image (s2i), along with some assets, such as an image stream, a deployment configuration, a service and even a route accesible from the outside of our cluster.

To understand this have a look to folder **'.nodeshift'**, there you'll find the following files.

~~~shell
$ ls .nodeshift/
deployment.yml	route.yml
~~~

Let's check again if the service works now using the route generated as part of the  'npm run openshift' command.

~~~shell
$ oc get route
NAME      HOST/PORT                                       PATH      SERVICES   PORT      TERMINATION   WILDCARD
files     files-gramola-cicd.apps.192.168.50.100.nip.io             files      8080                    None
$ curl http://files-gramola-cicd.apps.192.168.50.100.nip.io/api/greeting
{"content":"Hello, World!"}
~~~


#### Modify Base Application Code
So far we have tested the base code, and we're ready to add packages and additional assets so that we have our Files Services and not just the sample code deployed.

In summary we have to:

* Add storage to be able to store/read images from disk
* Add additional packages with 'npm install'
* Copy/Modify source code to acutally provide the file management features

Before starting creating YAML files for our assets, let's create a folder for our resources under our 'files' folder, we're going to name it base-resources.

~~~shell
$ pwd
/Users/cvicensa/openshift/gramola-cicd/files
$ mkdir base-resources
~~~

> Note: Either your using Minishift/Cluster Up or a proper Openshift cluster are already created Persistent Volumes have to be ready to use. Cluster Up and Minishift should have already created PVs for you, in a proper cluster an administrator should have take care of this.

##### Add storage
In order to provide storage to a POD we have to:
* Add a Persistent Volume (or use an existing one)
* Create a configmap to hold a property to store the name of the mount point of the folder to upload/read files to/from
* Create a Persistent Volume Claim
* Associate a PVC with a volume for the POD to consume

Check the existence of PVs we can use (you need to be admin to check this).

~~~shell
$ oc get pv
~~~

Create a configmap descriptor, pay attention to the key 'upload_dir', it states the directory for uploads and will be used in the deployment configuration.

~~~shell
$ cat << EOF > ./base-resources/configmap.yml
apiVersion: v1
kind: ConfigMap
metadata:
     name: files-configmap
data:
  upload_dir: "/uploads"
EOF
~~~

Create a PVC named 'files-uploads'

~~~shell
$ cat << EOF > ./base-resources/files.pvc.yml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: files-uploads
  annotations:
    description: Defines the PVC associated to files service
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
EOF
~~~

Associate the PVC with a volume in the Deployment Config, to do so, we're going to replace the contents of file .nodeshift/deployment.yml.

In the following deployment configuration snippet, there are interesting elements:

* An enviroment variable 'UPLOAD_DIR' which value will be filled from the value related to key 'upload_dir' in the configmap we created before
* Resource restrictions
* Updated liveness and readiness probes
* A volumen mount pointing to the PVC we just created

~~~shell
$ cat << EOF > ./.nodeshift/deployment.yml
spec:
  template:
    spec:
      containers:
        - env:
            - name: UPLOAD_DIR
              valueFrom:
                configMapKeyRef:
                  name: files-configmap
                  key: upload_dir
          readinessProbe:
            httpGet:
              path: /api/greeting
              port: 8080
              scheme: HTTP
          livenessProbe:
            httpGet:
              path: /api/greeting
              port: 8080
              scheme: HTTP
            initialDelaySeconds: 60
            periodSeconds: 30
          volumeMounts:
            - mountPath: /uploads
              name: files-uploads-data
      volumes:
      - name: files-uploads-data
        persistentVolumeClaim:
          claimName: files-uploads
EOF
~~~

So far we have created descriptor for some assets, now we have to actually create them in our Openshift cluster.

> Note: Assets will be create in the current project or you have to add -n NAME_SPACE to the command to specify the destination namespace

~~~shell
$ oc create -f base-resources/
configmap "files-configmap" created
persistentvolumeclaim "files-uploads" created
~~~

##### Add additional packages
Our code uses a module called [**'multer'**](https://www.npmjs.com/package/multer) to deal with the upload and storage of files.
Additionally we use [**'cors'**](https://www.npmjs.com/package/cors) for cross-origin resource sharing, [**'mime-types'**](https://www.npmjs.com/package/mime-types) to deal with file extensions and mime-types and [**'uuid'**](****) to generate a random uuid v4 to generate the file name when the naming strategy is random.

Let's install these packages doing as follows.

~~~shell
$ npm install multer mime-types uuid cors --save
~~~

===> TODO
**Optional**
To make the life of the developer easier there are means to reload a node app if a file is updated, you can also install [**'nodemon'**](https://www.npmjs.com/package/nodemon) 
In addition to installing the package we need to add a script entry to file package.json
===> TODO

##### Copy/Modify source code
Finally it's time to add and update some source code.

Update file **'app.js'** to add the following snippet above ```module.exports = app;```

~~~javascript
// Files API
app.use('/api/files', require('./lib/files.js')());
~~~

File 'app.js' should look liek this one.

~~~javascript
'use strict';

const path = require('path');
const express = require('express');
const bodyParser = require('body-parser');

const app = express();

app.use(bodyParser.json());
app.use(bodyParser.urlencoded({extended: false}));
app.use('/', express.static(path.join(__dirname, 'public')));

// Expose the license.html at http[s]://[host]:[port]/licences/licenses.html
app.use('/licenses', express.static(path.join(__dirname, 'licenses')));

app.use('/api/greeting', (request, response) => {
  const name = request.query ? request.query.name : undefined;
  response.send({ content: `Hi there, ${name || 'World!'}` });
});

// Files API
app.use('/api/files', require('./lib/files.js')());

module.exports = app;

~~~

As you can see, Files API code is at ./lib/files.js and that file doesn't exit yet. Let's take care of this issue.

~~~shell
$ mkdir lib
$ curl https://raw.githubusercontent.com/cvicens/gramola-project/master/files/lib/files.js -o ./lib/files.js
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  1682  100  1682    0     0  11783      0 --:--:-- --:--:-- --:--:-- 11845
~~~

Before we proceed with testing the code locally let's modify entry 'files' in package.json because command 'npm run openshift' will take care only of files/directories specified in this entry. After adding "lib" 'files' entry should be.

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
Before we test our code, let's download some sample images, you should be in 'files' directory to run the next commands.

~~~shell
$ mkdir sample-images
$ curl https://raw.githubusercontent.com/cvicens/gramola-project/master/files/sample-images/foo-P1000628.jpg -o ./sample-images/foo-P1000628.jpg
$ curl https://raw.githubusercontent.com/cvicens/gramola-project/master/files/sample-images/guns-P1080795.jpg -o ./sample-images/guns-P1080795.jpg
~~~

It's time to test locally our code, so.

~~~shell
$ npm start
~~~

From another terminal being in the same directory 'files'.

~~~shell
$ curl \
  -F "naming_strategy=original" \
  -F "image=@./sample-images/foo-P1000628.jpg" \
  http://localhost:8080/api/files/upload
{"result":"success","filename":"foo-P1000628.jpg"}
~~~

Finally let's check we can download the file we have just uploaded.

~~~shell
$ curl http://localhost:8080/api/files/foo-P1000628.jpg -o /tmp/foo-P1000628.jpg
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  295k  100  295k    0     0  27.7M      0 --:--:-- --:--:-- --:--:-- 28.9M
~~~

##### Deploy this version of our application to Openshift
Before we redeploy and just because we have modified the deployment configuration we have to delete it first.

~~~shell
$ oc delete dc files
~~~

Now we can use nodeshift to deploy our new code.

~~~shell
$ npm run openshift

> files@1.0.0-SNAPSHOT openshift /Users/cvicensa/openshift/gramola-cicd/files
> nodeshift --strictSSL=false --dockerImage=registry.access.redhat.com/rhoar-nodejs/nodejs-8

2018-08-14T19:14:31.947Z INFO loading configuration
...
2018-08-14T19:15:01.557Z INFO complete
~~~

##### Tests after deployment
Check if 'files' pod is up and running.

~~~shell
$ oc get po -w
NAME                READY     STATUS      RESTARTS   AGE
files-3-gwrlv       1/1       Running     0          4d
~~~

Find the route to the service.

~~~shell
$ oc get route
NAME      HOST/PORT                                       PATH      SERVICES   PORT      TERMINATION   WILDCARD
files     files-gramola-cicd.apps.192.168.50.100.nip.io             files      8080                    None
~~~

Upload our sample file

~~~shell
$ curl \
  -F "naming_strategy=original" \
  -F "image=@./sample-images/foo-P1000628.jpg" \
  http://files-gramola-cicd.apps.192.168.50.100.nip.io/api/files/upload
{"result":"success","filename":"foo-P1000628.jpg"}
~~~

Download the file you just uploaded

~~~shell
$ curl http://files-gramola-cicd.apps.192.168.50.100.nip.io/api/files/foo-P1000628.jpg -o /tmp/foo-P1000628.jpg
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  295k  100  295k    0     0  20.3M      0 --:--:-- --:--:-- --:--:-- 20.6M
~~~

Well done! You are ready to move on to the next lab.
