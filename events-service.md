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

We use the [Fabric8](http://fabric8.io/) maven plugin to deploy directly to Openshift. Fabric8 uses resources in folder 'src/main/fabric8' to deploy our code using s2i (it plays the same role .nodeshift played in the previous lab). We need to change these resources to match the database configuration, secret and configmap we just downloaded.

> This changes are needed just because we drifted a little away from the sample example to make it a bit closer to a production setup

So, let's make those changes. 

First of all we have to rename some elements from 'booster' to 'events', to do so please run the next commands.

~~~shell
$ sed -i '' 's/<groupId>io.openshift.booster<\/groupId>/<groupId>com.redhat.gramola.events<\/groupId>/g' pom.xml
$ sed -i '' 's/<artifactId>booster<\/artifactId>/<artifactId>events<\/artifactId>/g' pom.xml
$ sed -i '' 's/CRUD Booster/CRUD Events/g' pom.xml
~~~

The second change adds several additional environment variables (filled with configmap and secret values) to the deployment configuration of our service.

~~~shell
$ mv ./src/main/fabric8/credentials-secret.yml ./src/main/fabric8/credentials-secret.yml.orig
$ mv ./src/main/fabric8/deployment.yml ./src/main/fabric8/deployment.yml.orig
$ cat << EOF > ./src/main/fabric8/deployment.yml
apiVersion: v1
kind: Deployment
metadata:
  name: \${project.artifactId}
spec:
  template:
    spec:
      containers:
        - env:
            - name: DB_USERNAME
              valueFrom:
                 secretKeyRef:
                   name: events-database-secret
                   key: user
            - name: DB_PASSWORD
              valueFrom:
                 secretKeyRef:
                   name: events-database-secret
                   key: password
            - name: DB_SERVICE_NAME
              valueFrom:
                 configMapKeyRef:
                   name: events-configmap
                   key: database_service_name
            - name: DB_SERVICE_PORT
              valueFrom:
                 configMapKeyRef:
                   name: events-configmap
                   key: database_service_port
            - name: DB_NAME
              valueFrom:
                 configMapKeyRef:
                   name: events-configmap
                   key: database_name
            - name: JAVA_OPTIONS
              value: "-Dspring.profiles.active=openshift"
EOF
~~~

Spring Boot will read properties from files following this pattern application-<profile>.properties. So for profile 'openshift' this means **application-openshift.properties**. Well, we need to adapt this file to match properties defined as enviroment variables in the deployment configuration descriptor. To do so, please run this command.

~~~shell
$ mv ./src/main/resources/application-openshift.properties ./src/main/resources/application-openshift.properties.orig
$ cat << EOF > ./src/main/resources/application-openshift.properties
spring.datasource.url=jdbc:postgresql://\${EVENTS_DATABASE_SERVICE_HOST}:\${EVENTS_DATABASE_SERVICE_PORT}/\${DB_NAME}
spring.datasource.username=\${DB_USERNAME}
spring.datasource.password=\${DB_PASSWORD}
spring.datasource.driver-class-name=org.postgresql.Driver
spring.jpa.hibernate.ddl-auto=create
EOF
~~~

Make sure you're still logged in your Openshift cluster and your default project is still {{PROJECT_NAME}}

~~~shell
$ oc project
Using project "gramola-cicd" on server "{{OPENSHIFT_CONSOLE_URL}}".
~~~

Now let's create all these resources in our project.

~~~shell
$ oc create -f ./base-resources
configmap "events-configmap" created
deploymentconfig "events-database" created
persistentvolumeclaim "events-database" created
service "events-database" created
secret "events-database-secret" created
~~~

Before we proceed with the deployment of our Events Service let's check if our data base is up an running.

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

Let's check again if the service works now using the route generated as part of the deploy command.

~~~shell
$ oc get route
NAME      HOST/PORT                                         PATH      SERVICES   PORT      TERMINATION   WILDCARD
events    events-gramola-cicd.apps.192.168.50.100.nip.io              events     8080                    None
files     files-gramola-cicd.apps.192.168.50.100.nip.io               files      8080                    None
$ curl http://events-gramola-cicd.apps.192.168.50.100.nip.io/api/fruits
[{"id":1,"name":"Cherry"},{"id":2,"name":"Apple"},{"id":3,"name":"Banana"}]
~~~

#### Modify Base Application Code
So far we have tested the base code, and we're ready to copy some java classes to implement the Events API we committed to implement.

In summary we have to:

* Add sample rows to 'import.sql'
* Copy/Modify source code to acutally provide the events management features

##### Add sample rows to 'import.sql'

~~~shell
$ mv ./src/main/resources/import.sql ./src/main/resources/import.sql.orig
$ cat << EOF > ./src/main/resources/import.sql
insert into fruit (name) values ('Cherry');
insert into fruit (name) values ('Apple');
insert into fruit (name) values ('Banana');

insert into event (name, address, city, province, country, date, start_time, end_time, location, artist, description, image) values ('Not in the Lifetime Tour', 'Cmo. de Perales, 23, 28041', 'MADRID', 'MADRID', 'SPAIN', '2018-08-05', '18:00', '23:00', 'Caja Magica', 'Guns n Roses', 'The revived Guns N’ Roses and ...', 'guns-P1080795.jpg');
insert into event (name, address, city, province, country, date, start_time, end_time, location, artist, description, image) values ('CONCRETE AND GOLD TOUR 2018', '8 Boulevard de Bercy, Paris', 'PARIS', 'PARIS', 'FRANCE', '2018-09-15', '18:00', '23:00', 'AccorHotels Arena', 'Foo Fighters', 'Concrete and Gold Tour is...', 'foo-P1000628.jpg');
EOF
~~~

##### Copy/Modify source code
Now it's time to add and update some source code.

Before we go deep down with the java classes, let's remember the API we defined in the Project Intro. This API should include:

* ***Create event*** which means POST object to **'/'** and return HTTP 201
* ***Search all events*** which means GET from **'/'** and return HTTP 200 with an array of event objects
* ***Search events by country and city and (optionally) by date greater than or equal to a given date*** which means GET from **'/{country}/{city}/{date}?'** and return HTTP 200 with an array of event objects

If you have a look to the sample classes (Fruit, FruitController, FruitRepository), we should have at least (Event, EventController and EventRepository), we will add to that another class (GenericController).

* **Event**: annotated (@Entity) POJO to shape our Event object
* **EventController**: annotated (@RestController) class that provides the REST API itself
* **EventRepository**: extends CrudRepository<X,Y> annotated interface that provides with basic CRUD functionalities by [**Spring Data JPA**](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/)

So we need to add some code or annotation to provide the search of events by country and city and by date greater than or equal to a given date. This means basically this.

~~~java
public interface EventRepository extends CrudRepository<Event, Integer> {
	 public List<Event> findByCountryAndCityAndDateGreaterThanEqual(String country, String city, String date);	
}
~~~

Additionally we need our Event object (annotated as @Entity)

~~~java
...
@Entity
public class Event {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer id;

    private String name;
    private String address;
    private String city;
    private String province;
    private String country;
    private String date;
    private String startTime;
    private String endTime;
    private String location;
    private String artist;
    private String description;
    private String image;
    
    ...
 }
~~~

And finally we need the controller exposing the API at '/api/events'. As you can see below (only relevant parts shown) the search of events has to endpoints and we check with ``date.isPresent() `` if parameter 'date' has been provided or not.

~~~java
@RestController
@RequestMapping(value = "/api/events")
public class EventController {

    private final EventRepository repository;

    public EventController(EventRepository repository) {
        this.repository = repository;
    }
    
    @GetMapping({"/{country}/{city}", "/{country}/{city}/{date}"})
    public List<Event> findByCountryAndCityAndDateGreaterThanEqual(
    		@PathVariable("country") String country,
    		@PathVariable("city") String city,
    		@PathVariable("date") Optional<String> date) {

    	if (date.isPresent()) {
    		return repository.findByCountryAndCityAndDateGreaterThanEqual(country, city, date.get());
    	} else {
    		return repository.findByCountryAndCityAndDateGreaterThanEqual(country, city, "");
    	}
    }
 ...
 }
~~~

Let's copy these files to our 'src' folder.

~~~shell
curl {{GIT_RAW_URL}}/events/src/main/java/com/redhat/gramola/events/service/Event.java -o src/main/java/com/redhat/gramola/events/service/Event.java
curl {{GIT_RAW_URL}}/events/src/main/java/com/redhat/gramola/events/service/EventRepository.java -o src/main/java/com/redhat/gramola/events/service/EventRepository.java
curl {{GIT_RAW_URL}}/events/src/main/java/com/redhat/gramola/events/service/EventController.java -o src/main/java/com/redhat/gramola/events/service/EventController.java
~~~

##### Let's test our code locally
Now, as we did before let's try our new API locally by using the **'-P'** flag.

~~~shell
$ mvn spring-boot:run -P local
~~~

Let's test our brand new API. First getting all events.

~~~shell
$ curl http://localhost:8080/api/events
[{"id":1,"name":"Not in the Lifetime Tour","address":"Cmo. de Perales, 23, 28041","city":"MADRID","province":"MADRID","country":"SPAIN","date":"2018-08-05","startTime":"18:00","endTime":"23:00","location":"Caja Magica","artist":"Guns n Roses","description":"The revived Guns N’ Roses and ...","image":"guns-P1080795.jpg"},{"id":2,"name":"CONCRETE AND GOLD TOUR 2018","address":"8 Boulevard de Bercy, Paris","city":"PARIS","province":"PARIS","country":"FRANCE","date":"2018-09-15","startTime":"18:00","endTime":"23:00","location":"AccorHotels Arena","artist":"Foo Fighters","description":"Concrete and Gold Tour is...","image":"foo-P1000628.jpg"}]
~~~

Second, looking for events in SPAIN/MADRID.

~~~shell
$ curl http://localhost:8080/api/events/SPAIN/MADRID
[{"id":1,"name":"Not in the Lifetime Tour","address":"Cmo. de Perales, 23, 28041","city":"MADRID","province":"MADRID","country":"SPAIN","date":"2018-08-05","startTime":"18:00","endTime":"23:00","location":"Caja Magica","artist":"Guns n Roses","description":"The revived Guns N’ Roses and ...","image":"guns-P1080795.jpg"}]
~~~

Second, looking for events in FRANCE/PARIS/2018-09-01 (there should be one) and (there should be none)

~~~shell
curl http://localhost:8080/api/events/FRANCE/PARIS/2018-09-01
[{"id":2,"name":"CONCRETE AND GOLD TOUR 2018","address":"8 Boulevard de Bercy, Paris","city":"PARIS","province":"PARIS","country":"FRANCE","date":"2018-09-15","startTime":"18:00","endTime":"23:00","location":"AccorHotels Arena","artist":"Foo Fighters","description":"Concrete and Gold Tour is...","image":"foo-P1000628.jpg"}]

$ curl http://localhost:8080/api/events/FRANCE/PARIS/2018-09-16
[]
~~~

##### Deploy this version of our application to Openshift
Next step is to deploy our new code on Openshift, so again we run the following command.

~~~shell
$ mvn clean fabric8:deploy -Popenshift
...
[INFO] Updated DeploymentConfig: target/fabric8/applyJson/gramola-cicd/deploymentconfig-events.json
[INFO] F8: HINT: Use the command `oc get pods -w` to watch your pods start up
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 49.935 s
[INFO] Finished at: 2018-08-17T09:46:20+02:00
[INFO] Final Memory: 76M/755M
[INFO] ------------------------------------------------------------------------
~~~


##### Tests after deployment
Now let's run our tests against the route exposed.

~~~shell
$ oc get route
NAME      HOST/PORT                                         PATH      SERVICES   PORT      TERMINATION   WILDCARD
events    events-gramola-cicd.apps.192.168.50.100.nip.io              events     8080                    None
files     files-gramola-cicd.apps.192.168.50.100.nip.io               files      8080                    None
$ curl http://events-gramola-cicd.apps.192.168.50.100.nip.io/api/events/FRANCE/PARIS/2018-09-01
[{"id":2,"name":"CONCRETE AND GOLD TOUR 2018","address":"8 Boulevard de Bercy, Paris","city":"PARIS","province":"PARIS","country":"FRANCE","date":"2018-09-15","startTime":"18:00","endTime":"23:00","location":"AccorHotels Arena","artist":"Foo Fighters","description":"Concrete and Gold Tour is...","image":"foo-P1000628.jpg"}]
~~~


Well done! You are ready to move on to the next lab.
