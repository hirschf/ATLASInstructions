OHDSI-WebAPI Installation Instructions
===============================
Installation instructions for the open source tool ATLAS. This installation is for macOS. Similar steps can be followed for Linux users. If you are using Linux, installation of PostgreSQL requires `apt-get` instead of `brew install`

## Step 1: Install PostgreSQL

Begin by making sure you have the latest version of Homebrew installed:

`brew update`

Next, install PostgreSQL:

`brew install postgresql`

Confirm PostgreSQL was successfully installed by checking the version:

`postgres --help`

## Step 2: Create a database

Once you have postgres installed, enter the database with the following command:

`psql`

If this is your first time entering postgres, you should be prompted with something that looks like this

```
psql (9.6.1)
Type "help" for help.

user=#
```

When you enter a query and press `return`, postgres will not execute the query until you have finished by adding a `;` at the end. Let's set up the admin user for our profile. Enter the following below:

```
CREATE ROLE ohdsi_admin
CREATEDB REPLICATION
VALID UNTIL `infinity`;
COMMENT ON ROLE ohdsi_admin
IS `Administration group for OHDSI applications`;
```

## Step 3: Install Maven

`brew install maven`

To confirm that maven was successfully installed:

`mvn -version`

Once you have maven installed, you need to set the JAVA_HOME and CATALINA_HOME (we will need this for a future step) environment variables to point to the JDK. Do this by opening `~./bash_profile` in your favorite text editor. Once you're here, add:

`export JAVA_HOME=$(/usr/libexec/java_home)`
`export CATALINA_HOME=/opt/tomcat`

## Step 4: Clone the WebAPI project and make the .war file

For this next step, navigate to a directory you'd like your project to be placed and run:

`git clone https://github.com/OHDSI/WebAPI.git`

Next, the .war file must be deployed in Tomcat. If you don't have Tomcat installed, go to the website and download the latest .tar.gz file. Once it's downloaded, run the following command in the folder you downloaded it to:

`sudo tar xvf apache-tomcat-9.0.0.M17.tar.gz -C /opt/tomcat --strip-components=1`

This will unpackage it, and place it in the /opt/tomcat folder. If this fails, try running this:

`sudo mkdir /opt/tomcat` 

You will also need to give Tomcat the appropriate permissions:

`sudo chmod -R g+r /opt/tomcat/`

Launch Tomcat using the following command:

`sudo /opt/tomcat/bin/startup.sh`

You should receive an output that has confirmed Tomcat has started

```
Using CATALINA_BASE:   /opt/tomcat
Using CATALINA_HOME:   /opt/tomcat
Using CATALINA_TMPDIR: /opt/tomcat/temp
Using JRE_HOME:        /Library/Java/JavaVirtualMachines/jdk1.8.0_25.jdk/Contents/Home
Using CLASSPATH:       /opt/tomcat/bin/bootstrap.jar:/opt/tomcat/bin/tomcat-juli.jar
Tomcat started.
```

To confirm you have successfully installed Tomcat, open your browser and navigate to `localhost:8080`

If everything has installed correctly, you should be presented with the Tomcat page in your browser. From here, we need to generate the .war file and deploy it. Begin by making a `settings.xml` file in `/WebAPI`. Then, copy and paste the following inside:

```
<settings>
<profiles>
  <profile>
    <id>webapi-postgresql</id>
    <properties>
      <datasource.driverClassName>org.postgresql.Driver</datasource.driverClassName>
      <datasource.url>jdbc:postgresql://localhost:5432/OHDSI</datasource.url>
      <datasource.username>ohdsi_app_user</datasource.username>
      <datasource.password>app1</datasource.password>
      <datasource.dialect>postgresql</datasource.dialect>
      <datasource.ohdsi.schema>webapi</datasource.ohdsi.schema>
      <flyway.datasource.driverClassName>${datasource.driverClassName}</flyway.datasource.driverClassName>
      <flyway.datasource.url>${datasource.url}</flyway.datasource.url>
      <flyway.datasource.username>ohdsi_admin_user</flyway.datasource.username>
      <flyway.datasource.password>!PASSWORD!</flyway.datasource.password>
      <flyway.locations>classpath:db/migration/postgresql</flyway.locations>
    </properties> 
  </profile>  
</profiles>
</settings>
```

Note: Your settings.xml may vary depending on the webapi schema you created earlier. Keep this in mind when you set up your user and password

Next, generate the .war file:

`mvn clean package -s /WebAPIConfig/settings.xml -P webapi-postgresql`

Note: the trailing parameter `webapi-postgresql` depends on what you name the `<id>` in the `settings.xml` file. Again, this can be different depending on your configuration

This command should generate a `WebAPI.war` file in your `/WebAPI` directory. You'll need to place this file inside `/opt/tomcat/webapps`. You may have to modify permissions to be able to access this directory, or use the Tomcat manager application. 