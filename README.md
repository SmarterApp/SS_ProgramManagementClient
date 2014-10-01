# Welcome to Program Management Client (PM Client) #
The Program Management Component is responsible for configuration management and UI custom branding. The Program Management Client allow other applications to interface with the Program Management Component in order to retrieve bootstrap initialization parameters and to interrogate tenancy-related information.

## License ##
This project is licensed under the [AIR Open Source License v1.0](http://www.smarterapp.org/documents/American_Institutes_for_Research_Open_Source_Software_License.pdf).

## Getting Involved ##
We would be happy to receive feedback on its capabilities, problems, or future enhancements:

* For general questions or discussions, please use the [Forum](forum_link_here).
* Use the **Issues** link to file bugs or enhancement requests.
* Feel free to **Fork** this project and develop your changes!

### Client Modules
There are three modules that make up the client:

1. The Client Interfaces module contains the interface classes for the client, AOP advice and point cuts, bootstrap initialization
1. The Null Client module is a client implementation that sends all messages to the local logging subsystem (SLF4J)
1. The Integrated Client sends all messages to the advertised REST endpoints from the REST module

In order to use one of the client implementations:

1) Add the Maven dependencies to the POM of the project that is using Program Management.

```
<dependency>
	<groupId>org.opentestsystem.shared</groupId>
	<artifactId>prog-mgmnt-client<artifactId>
	<version>${progman.client.version}<version>
</dependency>
        
<dependency>
	<groupId>org.opentestsystem.shared</groupId>
	<artifactId>prog-mgmnt-client-null-impl</artifactId>
	<version>${progman.client.version}</version>
</dependency>

<dependency>
	<groupId>org.opentestsystem.shared</groupId>
	<artifactId>prog-mgmnt-client-interfaces<artifactId>
	<version>${progman.client.version}<version>
</dependency>
```

2) Add a context initializer class name to the web.xml

```
<context-param>
    <param-name>contextInitializerClasses</param-name>
    <param-value>org.opentestsystem.shared.progman.init.InitSpringPropertyConfigLoad</param-value>
</context-param>
```

3) Within the file system of the deployment (local file system if running locally or within tomcat file directories), create a configuration folder structure as follows:

```
{CONFIG-FOLDER-NAME}/progman/
example: /my-app-config/progman/
``` 
4) Within the deepest folder ('/progman/'), place a file named 'pm-client-security.properties' with the following contents:

```
#security props
oauth.access.url={the URL of OAuth2 access token provider}
pm.oauth.client.id={Client ID for program management client, can be shared amongst all client users or application/consumer specific values}
pm.oauth.client.secret={Password for program management client, can be shared amongst all client users or application/consumer specific values}

working example:
oauth.access.url=https://drc-dev-secure.opentestsystem.org:443/auth/oauth2/access_token?realm=/sbac
pm.oauth.client.id=pm
pm.oauth.client.secret=sbac12345
```

5) Add environment variables to application server startup
	* ```-DSB11_CONFIG_DIR="{CONFIG-FOLDER-NAME}"```
		* From earlier example: ```-DSB11_CONFIG_DIR="/my-app-config/"```
	* ```-Dspring.profiles.active="progman.client.impl.integration"```
		* Use ```progman.client.impl.integration``` to use the fully integrated client
		* Use ```progman.client.impl.null``` to use the null client
	* ```-Dprogman.baseUri=http://localhost:8089/prog-mgmnt.rest/```
		* This URI is the base URI for where the Program Management REST module is deployed
	* ```-Dprogman.locator=tib,dev[,overlay];```
		* The locator variable describes which combinations of name and environment (with optional overlay) should be loaded from Program Management.  For example: ```"component1-urls,dev"``` would look up the name component1-urls for the dev environment at the configured REST endpoint.  Multiple lookups can be performed by using a semi-colon to delimit the pairs (or triplets with overlay): ```"component1-urls,dev;component1-other,dev"```.
	
	All four environment variables are required. 

6) Before attempting to start the application that integrates with Program Management, be sure that the name/environment configuration parameters are configured in the Program Management UI.

7) The reason that the OAuth2 credentials are contained in the pm-client-security.properties file and are stored in the file system, instead of the Program Management application itself, is because the OAuth2 credentials are required during application startup before dependency injection can occur, and so that the consumer application can then begin securely retrieving the rest of the properties stored in a Program Management configuration.

## Build
These are the steps that should be taken in order to build all of the Program Management Client related artifacts.

### Pre-Dependencies
* Mongo 2.0 or higher
* Tomcat 6 or higher
* Maven (mvn) version 3.X or higher installed
* Java 7
* Access to sb11-shared-build repository
* Access to sb11-shared-code repository
* Access to sb11-rest-api-generator repository

### Build order

* sb11-shared-build
* sb11-shared-code
* sb11-rest-api-generator
* sb11-program-management-client

## Dependencies
Program Management Client has a number of direct dependencies that are necessary for it to function.  These dependencies are already built into the POM files.

### Compile Time Dependencies
* Apache Commons IO
* Apache Commons Beanutils
* Jackson Datatype Joda
* Google Guava
* Hibernate Validator
* Apache Commons File Upload
* Jasypt
* SB11 Shared Code
	* Logback
	* SLF4J
	* JCL over SLF4J
	* Spring Core
	* Spring Beans
	* Spring Data MongoDb
	* Mongo Data Driver
	* Spring Context
	* Spring WebMVC
	* Spring Web
	* Spring Aspects
	* AspectJ RT
	* AspectJ Weaver
	* Javax Inject
	* Apache HttpClient
	* JSTL API
	* Apache Commons Lang
	* Joda Time
	* Jackson Core
	* Jackson Annotations
	* Jackson Databind

### Test Dependencies
* Spring Test
* Hamcrest
* JUnit 4
* Podam
* Log4J over SLF4J
* Mockito

### Runtime Dependencies
* Servlet API
* Persistence API