## Events Service
In the same fashion as we created the previous service, we're goint to use the Red Hat Developer Launch site to create the Events Service.

So again let's log in [here](https://developers.redhat.com/launch/) and after a successful login click on the 'LAUNCH YOUR PROJECT' button to access the Missions area.

#### Generate Base Application Code
Please enter 'events' as the name of the application and click 'Continue'.
![Create application]({% image_path launch-site-create-application-events.png %}){:width="700px"}

In this lab we're going to generate a skeleton for our Events Service based on Spring Boot and modify it locally to run it later in our own Openshift cluster. So, 
let's select 'Build and Run Locally' as our target enviroment. Should you wanted to use your own Red Hat operated Openshift cluster you should choose 'Code Locally, Build and Deploy'.
![Build and Run Locally]({% image_path launch-site-build-run-events.png %}){:width="700px"}

Now let's choose 'CRUD' and Spring Boot 1.5.14.RELEASE (RHOAR) as our mission and runtime.

![Select Mission and Runtime]({% image_path launch-site-mission-runtime-events.png %}){:width="700px"}

Time to generate our Spring Boot REST enabled sample application to be the base of our Events Service. Please click on 'Set Up Application' and a zip file should download automatically. 
![Set Up Application]({% image_path launch-site-setup-application-events.png %}){:width="700px"}

You should notice a file being downloaded. This zip file contains the base code we need.

> Unfortunately the naming is not correct and the zip file is named as 'booster.zip' instead of 'events.zip'

![Next Steps]({% image_path launch-site-next-steps-events.png %}){:width="700px"}

#### Test and Deploy before changes
Please open a terminal window and check Java 8 and Maven are available.

~~~shell
$ java -version
java version "1.8.0_131"
Java(TM) SE Runtime Environment (build 1.8.0_131-b11)
Java HotSpot(TM) 64-Bit Server VM (build 25.131-b11, mixed mode)
$ mvn --version
Apache Maven 3.5.0 (ff8f5e7444045639af65f6095c62210b5713f426; 2017-04-03T21:39:06+02:00)
Maven home: /usr/local/Cellar/maven/3.5.0/libexec
Java version: 1.8.0_131, vendor: Oracle Corporation
Java home: /Library/Java/JavaVirtualMachines/jdk1.8.0_131.jdk/Contents/Home/jre
Default locale: en_GB, platform encoding: UTF-8
OS name: "mac os x", version: "10.13.6", arch: "x86_64", family: "mac"
~~~

Change to our project's directory "{{PROJECT_NAME}}".

~~~shell
$ cd {{PROJECT_NAME}}
~~~

At this time 'oc' tool should be properly installed and you should be logged in already. If not, please go back to 'Getting Started - Explore OpenShift with OpenShift CLI' and login as suggested. Then go to your project as shown here.

~~~shell
$ oc project {{PROJECT_NAME}}
~~~

Copy or move the zip file we just downloaded to our project directory.

~~~shell
$ mv ~/Downloads/booster.zip .
$  unzip booster.zip 
Archive:  booster.zip
   creating: booster/
   creating: booster/.circleci/
  inflating: booster/.circleci/config.yml  
  ...
  inflating: booster/README.adoc    
~~~

Let's rename the directory as 'events' and open your file editor to show the file structure. We're going to use STS (Spring Tool Suite), you could use Eclipse or other IDEs too.

~~~shell
$ mv booster events
~~~

Use or create a new workspace (preferably choose {{PROJECT_NAME}}).
![Create STS workspace]({% image_path create-sts-workspace.png %}){:width="700px"}

Let's add our 'events' project to the workspace by choosing 'File/Open Projects From File System'
![Create STS workspace]({% image_path open-projects-from-filesystem.png %}){:width="150px"}

Choose our 'events' folder where our sample code is.
![Events - Project View]({% image_path import-projects-from-file-system.png %}){:width="700px"}

You should end up with a project view like this.
![Events - Project View]({% image_path events-project-view.png %}){:width="700px"}

In general you want your code to be in a specific package, so let's rename the generic package **'io.openshift.booster'** as **'com.redhat.gramola.events'**. Select 'io.openshift.booster' package under 'src/main/java' and type ⌥⌘R (Alt-Ctr-R) to refactor/rename.
![Renaming java package]({% image_path rename-booster-java-package.png %}){:width="700px"}

Let's do the same refactoring with the tests folder 'src/test/java' as in the next picture, otherwise test classes won't be able to find the classes to run tests on.
![Renaming tests java package]({% image_path rename-booster-tests-java-package.png %}){:width="700px"}

Before we test and run our base code locally take a look to the **'profiles'** section in our **'pom.xml'** file, there should be a **'local'** profile as in the next excerpt where there is a dependency with H2.

~~~xml
...
<profiles>
    <profile>
      <id>local</id>
      <activation />
      <properties>
        <db.name>H2</db.name>
      </properties>
      <dependencies>
        <dependency>
          <groupId>com.h2database</groupId>
          <artifactId>h2</artifactId>
          <scope>runtime</scope>
        </dependency>
      </dependencies>
    </profile>
...
</profiles>
...
~~~

Before running our code let's run some unit tests on our code as follows.

~~~shell
$  mvn test
[INFO] Scanning for projects...
...
[INFO] Results:
[INFO] 
[INFO] Tests run: 15, Failures: 0, Errors: 0, Skipped: 0
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 10.073 s
[INFO] Finished at: 2018-08-16T11:26:46+02:00
[INFO] Final Memory: 28M/519M
[INFO] ------------------------------------------------------------------------
~~~

Now let's run our code locally against the embedded H2 database by using the **'-P'** flag.

~~~shell
$ mvn spring-boot:run -P local
...
2018-08-16 12:03:09.048  INFO 4819 --- [  restartedMain] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat started on port(s): 8080 (http)
2018-08-16 12:03:09.054  INFO 4819 --- [  restartedMain] c.r.gramola.events.BoosterApplication    : Started BoosterApplication in 4.396 seconds (JVM running for 4.803)
2018-08-16 12:03:14.367  INFO 4819 --- [nio-8080-exec-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring FrameworkServlet 'dispatcherServlet'
2018-08-16 12:03:14.367  INFO 4819 --- [nio-8080-exec-1] o.s.web.servlet.DispatcherServlet        : FrameworkServlet 'dispatcherServlet': initialization started
2018-08-16 12:03:14.384  INFO 4819 --- [nio-8080-exec-1] o.s.web.servlet.DispatcherServlet        : FrameworkServlet 'dispatcherServlet': initialization completed in 17 ms
2018-08-16 12:03:15.289  INFO 4819 --- [nio-8080-exec-3] o.h.h.i.QueryTranslatorFactoryInitiator  : HHH000397: Using ASTQueryTranslatorFactory
~~~

Let's test the sample API that implements a CRUD façade to manage Fruit objects. Next curl command uses GET '/' which should return all Fruit objects stored.

~~~shell
$ curl http://localhost:8080/api/fruits/
[{"id":1,"name":"Cherry"},{"id":2,"name":"Apple"},{"id":3,"name":"Banana"}]
~~~

You can also open a browser tab and point to http://localhost:8080/ you should be able to see a simple user interface like this one.
![CRUD UI]({% image_path sample-fruits-crud-ui.png %}){:width="700px"}

Next step should be run/test our base code in Openshift, but before that we need to deploy some basic resources.

##### Deploying base resources
We're going to need some resources before we deploy our base code: a PostgreSQL database running in Openshift, a configmap, secret, etc.

Additionally, we are going to make some changed on the sample application code generated in order to make it easier to maintain and share.

Let's create a folder for descriptor for these resources and download some suitable descriptors there.

~~~shell
$ mkdir base-resources
$ curl {{GIT_RAW_URL}}/events/base-resources/configmap.yml -o ./base-resources/configmap.yml
$ curl {{GIT_RAW_URL}}/events/base-resources/secret.yml -o ./base-resources/secret.yml
$ curl {{GIT_RAW_URL}}/events/base-resources/database.dc.yml -o ./base-resources/database.dc.yml
$ curl {{GIT_RAW_URL}}/events/base-resources/database.pvc.yml -o ./base-resources/database.pvc.yml
$ curl {{GIT_RAW_URL}}/events/base-resources/database.svc.yml -o ./base-resources/database.svc.yml
~~~

As we have explained before we use the Fabric 8 plugin to deploy directly on Openshift. Fabric 8 uses resources in folder 'src/main/fabric8' to deploy our code using s2i. We need to change these resources to match the database configuration, secret and configmap we just downloaded.

> If we hadn't made 

Make sure you're still logged in your Openshift cluster and your default project is still {{PROJECT_NAME}}

~~~shell
$ oc project
Using project "gramola-cicd" on server "{{OPENSHIFT_CONSOLE_URL}}".
~~~

Now let's create all these resources in our project.

~~~shell
$ oc create -f ./base-resources


~~~

Before we proceed with the deployment of our Events Service check our data base is up an running.

~~~shell
$ oc get pod -w
NAME                      READY     STATUS      RESTARTS   AGE
events-database-1-kfz9b   1/1       Running     0          4s
files-3-gwrlv             1/1       Running     1          5d
~~~

Finally let's check our application can be deployed to our Openshift cluster directly, note this time we need to use a different profile, **'openshift'**, where the database is set to PostgreSQL. You can have a look to the pom.xml file to check that in this out.

~~~shell
$ mvn clean fabric8:deploy -Popenshift
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
$ curl {{GIT_RAW_URL}}/files/lib/files.js -o ./lib/files.js
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  1682  100  1682    0     0  11783      0 --:--:-- --:--:-- --:--:-- 11845
~~~

Before we proceed with testing the code locally let's modify entry 'files' in package.json because command 'npm run openshift' will take care only of files/directories specified in this entry. After adding "lib" 'files' entry should be.

~~~
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
$ curl {{GIT_RAW_URL}}/files/sample-images/foo-P1000628.jpg -o ./sample-images/foo-P1000628.jpg
$ curl {{GIT_RAW_URL}}/files/sample-images/guns-P1080795.jpg -o ./sample-images/guns-P1080795.jpg
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
Again let's use nodeshift to deploy our polished new code.

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
