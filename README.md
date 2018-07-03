# DSpace-Installation

# 1. Steps for installation of DSpace with Docker  

# Requirements

      Basic requirement for this installation process:
        Git, apt-utils, Docker, Docker-Compose
        
      $ sudo apt-get install git
      $ sudo apt-get install apt-utils
  
   Docker - https://docs.docker.com/install/linux/docker-ce/ubuntu/#set-up-the-repository

   Docker Compose -- https://docs.docker.com/compose/install/
   
# Installation Steps   

Step-1) Update the System

      $ sudo apt-get update

Step-2) Clone the Repository "fresearchgroup/DSpace-Docker-Installation"
       
      $ git clone https://github.com/fresearchgroup/DSpace-Docker-Installation
      
Step-3) Move to the main working directory

      $ cd DSpace-Docker-Installation/dspace-dev-docker
      
Step-4) Also, add m2-repo and dspace-build folders, which is useful while running the Containers.

      $ mkdir m2-repo dspace-build
     
Step-5) If you already have a working copy of DSpace checked out on your computer:
       
      $ ln -s /path/to/your/dspace/working/copy dspace-src
      
   NOTE : The folder structure is expected this type.
               
               dspace-dev-docker
                  |-- dspace-src
                  |-- dspace-build
                  +-- m2-repo
   

Step-6) Changing Ownership of Folders to your Users.Replace "youruser" with your user account name.
      
      $ sudo chown -R youruser:youruser dspace-src dspace-build m2-repo

               
               
               ----------------------Edit the DSpace configuration---------------------------------
               
               

Step -7) Find the "local.cfg.EXAMPLE" file inside the dspace-src/dspace/config directories
           
           $ cd dspace-src/dspace/config

Step-8) Change the "local.cfg.EXAMPLE" file name with "local.cfg".

Step-9) Edit the "local.cfg" file with the reference below.

      ##########################
      # SERVER CONFIGURATION   #
      ##########################
      dspace.dir=/srv/dspace
      
      # Name of the site
      dspace.name = OER for Collaboration System
      
      ##########################
      # DATABASE CONFIGURATION #
      ##########################
      # DSpace only supports two database types: PostgreSQL or Oracle
      
      db.url = jdbc:postgresql://postgres:5432/dspace
     
      
   NOTE: 
      
      Have ports 8080 (tomcat), 5432 (postgresql), 1043 and 8000 (remote debugging) open.
      Otherwise, you can modify the mappings in docker-compose.yml file to use whichever ports you prefer.
      Also modify the attributes port in "local.cfg" file with correct ports.
      
      
Step-10) To Activate the APIs of DSpace, Disable the SSL

      $ cd
      $ cd DSpace-Docker-Installation/dspace-dev-docker
      $ gedit dspace-src/dspace-rest/src/main/webapp/WEB-INF/web.xml
      
      Comment out the <security-constraint> block. Save and Exit.
          For Example:
     <!- -
    <security-constraint>
        <web-resource-collection>
            <web-resource-name>DSpace REST API</web-resource-name>
            <url-pattern>/*</url-pattern>
        </web-resource-collection>
        <user-data-constraint>
            <transport-guarantee>CONFIDENTIAL</transport-guarantee>
        </user-data-constraint>
    </security-constraint>
    -- >
   
      
Step-11) Launch Docker-Compose
       
        $ sudo docker-compose up -d
        
Step-12) Once launched, to get into developer account, follow the step.
        
        $ sudo docker attach dspace-dev-docker_dspace-dev_1
              
Step-13) Compilation of DSpace inside the container

        $ mvn -Dmirage2.on=true -Dmirage2.deps.included=false package
    
Step-14) Once compiled the task' alias is available.Install the java webapps inside the container.
        
        $ task fresh_install
       
Step-15) Now create a user for the DSpace

        $ dspace create-administrator
        
Step-16) Create web application shortcuts, type the followings

        $ cd $CATALINA_HOME/webapps
        
        $ sudo ln -s /srv/dspace/webapps/solr
        
        $ sudo ln -s /srv/dspace/webapps/rest
        
        $ sudo ln -s /srv/dspace/webapps/oai
        
        $ sudo ln -s /srv/dspace/webapps/sword
        
Step-17) Configure the default ROOT webapp

        $ sudo rm -rf /usr/local/tomcat/webapps/ROOT
        
        $ cd /usr/local/tomcat/webapps
        
        $ sudo ln -s /srv/dspace/webapps/xmlui ROOT
        
        $ cd /srv/dspace-src

Step -16) Staring the server

        $ tomcat start && catalina.out
       
Step-17) Just start tomcat with the jpda option

        $ tomcat jpda start
       
 
 -----------------------------------------------------------------------------------------------------------------------
 
             Now open http://localhost:8080/ , you can access the DSpace XMLUI.
      
      
References:

    [1] https://wiki.duraspace.org/display/~terrywbrady/Building+and+Running+DSpace+in+Docker
    [2] http://wiki.lib.sun.ac.za/index.php?title=SUNScholar/Install_DSpace/S04/5.X
    [3] https://github.com/DSpace-Labs/dspace-dev-docker
    [4] https://wiki.duraspace.org/display/DSDOC6x/REST+API#RESTAPI-DisablingSSL  
                 

    ========================================================================================================================
    
    

# 2. DSpace-Installation Steps Without Docker

1. Install Ubuntu 
        NOTE:
          
          All Installation Steps is based on Considering that User Account name is "dspace".
          If you want create a new User Account follow the steps below.
          
          $ sudo adduser dspace
          
          $ su dspace
          
          $ cd
      
2. Prepare Ubuntu
                  
  2.1 Install the Java software dependencies
   
   2.1.1 Install Java
       
        $ sudo apt-get install default-jdk default-jre
       
   2.1.2 Cross check the presence of Java JDK
        
        $ sudo update-java-alternatives -l
        
   2.1.3 Install Ant
   
        $ sudo apt-get install ant ant-optional


  2.2 Install the Maven Java WAR builder
   
   2.2.1 Install maven
   
        $ sudo apt-get install maven
        
   2.2.2 Create the Maven home folder
   
        $ mkdir $HOME/.m2
   2.2.3 (Optinal) Setup the Maven config file
                You can skip this step if you have a direct connection to the internet. The Maven configuration file is only needed if your connection to the internet is via a campus proxy server.
      
   Maven proxy config file
   
        $ nano $HOME/.m2/settings.xml
        
        Add the following:
        NOTE: Replace everything between the % signs with your campus settings !
        -------------------------------------------------------------------------------------
        <settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                      http://maven.apache.org/xsd/settings-1.0.0.xsd">
            <localRepository/>
            <interactiveMode/>
            <usePluginRegistry/>
            <offline/>
            <pluginGroups/>
            <servers/>
            <mirrors/>
              <proxies>
                  <proxy>
                      <id>%my-name-for-maven-settings%</id>
                          <active>true</active>
                          <protocol>http</protocol>
                          <host>%my-campus-proxy-hostname%</host>
                          <port>%my-campus-proxy-port%</port>
                          <username>%my-campus-proxy-username%</username>
                          <password>%my-campus-proxy-password%</password>
                          <nonProxyHosts></nonProxyHosts>
                          </proxy>
                    </proxies>
              <profiles/>
            <activeProfiles/>
        </settings>
        --------------------------------------------------------------------------------------
     
  2.3 Install the Tomcat Java server
   
   2.3.1 Install Tomcat
   
          $ sudo apt-get install tomcat7 
          
   2.3.2 Setup "authbind" for Tomcat
              Edit the /etc/default/tomcat7 file as follows:
          
          $ sudo nano /etc/default/tomcat7
          
         Remove the hash sign from in front of the authbind parameter and change authbind to yes as follows: 
        --------------------------------------------------------------------
          # If you run Tomcat on port numbers that are all higher than 1023, then you
          # do not need authbind.  It is used for binding Tomcat to lower port numbers.
          # NOTE: authbind works only with IPv4.  Do not enable it when using IPv6.
          # (yes/no, default: no)
          AUTHBIND=yes  
          ------------------------------------------------------------------
          Save and exit the file. 
      
          Type the following commands: 
          
          $ sudo touch /etc/authbind/byport/80
          
          $ sudo touch /etc/authbind/byport/443
          
          $ sudo chmod 0755 /etc/authbind/byport/80
          
          $ sudo chmod 0755 /etc/authbind/byport/443
          
          $ sudo chown tomcat7.tomcat7 /etc/authbind/byport/80
          
          $ sudo chown tomcat7.tomcat7 /etc/authbind/byport/443
          
          $ cd /etc/authbind/byport
  
          $ ls -l
          
          You get the following :
          ----------------------------------------------------------------------
          dspace@dspace:/etc/authbind/byport# ls -l
          total 0
          -rwxr-xr-x 1 tomcat7 tomcat7 0 2011-06-10 18:33 443
          -rwxr-xr-x 1 tomcat7 tomcat7 0 2011-06-10 18:33 80
          -------------------------------------------------------------------------
          
  2.3.3 Setup Tomcat to listen on insecure port 80
  
         $ sudo nano /etc/tomcat7/server.xml
         
         Find the connector for port 8080 and change it to port 80.
         ----------------------------------------
         <Connector port="80" protocol="HTTP/1.1" 
               enableLookups="false"
               maxConnections="-1"
               maxThreads="450"
               maxHttpHeaderSize="16384"
               connectionTimeout="20000" 
               URIEncoding="UTF-8"
               redirectPort="443" />
         
         --------------------------------------
        Save and exit the file.
        
 2.3.4  Setup Tomcat to listen on secure port 443
          Setup Tomcat admin users
          
          $ sudo nano /etc/tomcat7/tomcat-users.xml
      
          Delete all the contents of the file and add the following admin and manager roles with a password.
          Replace XXXX with your password!
         -----------------------------------------------------------------------------
         <?xml version='1.0' encoding='utf-8'?>
          <tomcat-users>
              <role rolename="manager-gui"/>
              <role rolename="manager-jmx"/>
              <user username="dspace" password="XXXX" roles="manager-gui,manager-jmx"/>
          </tomcat-users>  
         -------------------------------------------------------------------------------
          Save and exit the file.
          
 2.3.5  Java environment settings for Tomcat webapp server
      
        $ sudo nano /etc/default/tomcat7
        
        After determining how much RAM is installed on your server, it is best practice to use about 50% of the RAM for Java.
        
        ---------------------------------------------------------------
        # You may pass JVM startup parameters to Java here. If unset, the default
        # options (-Djava.awt.headless=true -Xmx128m) will be used.
        #JAVA_OPTS="-Djava.awt.headless=true -Xmx128m"
        JAVA_OPTS="-Djava.awt.headless=true -Xmx2048m -Xms512m -XX:MaxPermSize=1024m"
        --------------------------------------------------------------
        Save and exit the file. 
        
  2.3.6 Setup Tomcat server permissions
  
        $ sudo nano /etc/default/tomcat7
      
        Change "TOMCAT7_SECURITY" to yes. 
        -------------------------------------------------------------------------
        # Use the Java security manager? (yes/no, default: no)
        TOMCAT7_SECURITY=yes
        -------------------------------------------------------------------------
        Save and exit the file. 
        
        $ sudo nano /etc/tomcat7/policy.d/05dspace.policy
        
        Copy and paste the following:
        --------------------------------------------------------------------------
        grant codeBase "file:/home/dspace/-" {
          permission java.security.AllPermission;
        };
        grant codeBase "file:/tmp/-" {
          permission java.security.AllPermission;
        };
        --------------------------------------------------------------------------
        Save and exit the file.
        
        $ sudo chown root.tomcat7 /etc/tomcat7/policy.d/05dspace.policy
        
  2.3.7 Setup user permissions
  
        $ cd
        
        $ sudo adduser tomcat7 dspace
        
        $ sudo adduser dspace tomcat7
        
  2.3.8 Restart the Tomcat server
  
        $ sudo service tomcat7 restart
        
  2.3.9 Post Tomcat installation checks
  
        $ sudo netstat -tapn | grep java
        
        You will get something like that.
        ---------------------------------------------------------------------------------------
        dspace@dspace:~# sudo netstat -tapn | grep java
        tcp6       0      0 127.0.0.1:8005          :::*                    LISTEN      11093/java      
        tcp6       0      0 :::80                   :::*                    LISTEN      11093/java   
        -----------------------------------------------------------------------------------------
          
  2.4  Install the PostgreSQL database server
  
   2.4.1 Increase the kernel shared memory for PostgreSQL server client connections
          Edit the "/etc/sysctl.conf" file: 
          
          $ sudo nano /etc/sysctl.conf
          
          
          Copy and paste the following to the end of the file:
          ------------------------------------------------------------------------------
          # For PostgreSQL server client connections
          kernel.shmmax = 500000000
          kernel.shmall = 500000000
          ------------------------------------------------------------------------------
          Save and exit the file. 
          
          $ sudo sysctl -p
          
   2.4.2  Install PostgreSQL server software
   
          $ sudo apt-get install postgresql-9.5 postgresql-contrib-9.5 libpg-java
          
   2.4.3 Setup the PostgreSQL server host based access permissions
   
          $ sudo sed -i 's/ident/trust/' /etc/postgresql/9.5/main/pg_hba.conf
          
          $ sudo sed -i 's/md5/trust/' /etc/postgresql/9.5/main/pg_hba.conf
          
          $ sudo sed -i 's/md5/trust/' /etc/postgresql/9.5/main/pg_hba.conf
          
          $ sudo service postgresql restart
   
   2.4.4  Create the PostgreSQL "dspace" DB user
   
          $ sudo createuser -U postgres -d -A -P dspace
          
   2.4.5 Create the PostgreSQL "dspace" database 
   
          $ sudo su - postgres

          $ createdb -E UNICODE dspace
     
   2.4.6 Setup PostgreSQL dspace DB user password, ownership and privileges
   
          $ psql -U postgres -d dspace
          
          ALTER ROLE dspace WITH PASSWORD 'dspace';
          
          ALTER DATABASE dspace OWNER TO dspace;
          
          GRANT ALL PRIVILEGES ON DATABASE dspace TO dspace;
          
          CREATE EXTENSION pgcrypto;
          
          \q
          
          exit
          
  2.4.7 Setup the PostgreSQL server host based access permissions to the dspace database
  
          $ sudo -i
          
          $ sudo echo "## DSpace DB user access">> /etc/postgresql/9.5/main/pg_hba.conf
          
          $ sudo echo "host    dspace       dspace      127.0.0.1/32         md5" >> /etc/postgresql/9.5/main/pg_hba.conf
          
          $ exit
          
   2.4.8 Setup maximum number of PostgreSQL server client connections
   
          $ sudo nano /etc/postgresql/9.5/main/postgresql.conf
          
          Change the number of "max_connections" to 300. Please note: If you get connection errors, then adjust this value!
          Save and exit the file. 
          
   2.4.9 Restart the PostgreSQL server
   
          $ sudo service postgresql restart
          
          -------------------------------------------------------------------------------------------------------
  2.5  Configure the environment variables
  
   2.5.1 Java environment settings for other java web applications
   
          $ sudo nano /etc/environment
          
          Copy and paste the following to the bottom of the file.
          -------------------------------------------------------------------------------------
          JAVA_HOME="/usr/lib/jvm/default-java"
          JAVA_OPTS="-Djava.awt.headless=true -Xmx2048m -Xms1024m -Dfile.encoding=UTF-8"
          ------------------------------------------------------------------------------------------
          Save and exit the file. 
   
   2.5.2  Increase number of open files available
          
          $ sudo nano /etc/security/limits.conf
          
          Add the following to the bottom of the file:
          ------------------------------------------------------------------------------------
          *            hard       nofile          65536
          *            soft       nofile          65536
          --------------------------------------------------------------------------------------
          Save and exit the file. 
          
          $ ulimit -n
          
   2.5.3 Setup file creation permissions
   
          $ nano $HOME/.bashrc
          
          Add the following to the bottom of the file.
          ------------------------------------------------------------------------------------------
          umask 002
          -------------------------------------------------------------------------------------------
          Save and exit the file
          
   2.6 Reboot the server.
        
          $ sudo reboot

3. Install DSpace

   3.1 Get DSpace
        
          $ cd
          
          $ wget https://github.com/DSpace/DSpace/releases/download/dspace-6.2/dspace-6.2-src-release.tar.gz
          
   3.2 Unpack DSpace
   
          $ tar -xzvf $HOME/dspace-6.2-src-release.tar.gz
          
          $ cd $HOME
          
          $ ln -s dspace-6.2-src-release source
          
    3.3 Edit the DSpace configuration
            Create your own ./source/dspace/config/local.cfg configuration file (you may
            wish to simply copy the provided ./source/dspace/config/local.cfg.EXAMPLE).
            This local.cfg file can be used to store any configuration changes that you wish
            to make which are local to your installation. ANY setting may be copied into
            this local.cfg file from the dspace.cfg or any other *.cfg file in order to override
            the default setting (see note below). For the initial installation of DSpace, there
            are some key settings you’ll likely want to override, those are provided in the
            ./source/dspace/config/local.cfg.EXAMPLE
            
            Example Config Check the following example for the other settings.
            -------------------------------------------------------------------------------------
            
            # DSpace build.properties
            # This file should be customised to suit your build environment.
            # Note that not all configuration is handled here, only the most common
            # properties that tend to differ between build environments.
            # For adjusting global settings or more complex settings, edit the relevant config file.
            #
            # IMPORTANT: Do not remove or comment out settings in build.properties
            # When you edit the "build.properties" file (or a custom *.properties
            file),
            # take care not to remove or comment out any settings. Doing so, may
            cause
            # your final "dspace.cfg" file to be misconfigured with regards to
            that
            # particular setting. Instead, if you wish to remove/disable a
            particular
            # setting, just clear out its value. For example, if you don’t want
            to be
            # notified of new user registrations, ensure the
            "mail.registration.notify"
            # setting has no value, e.g. "mail.registration.notify="
            #
            ##########################
            # SERVER CONFIGURATION #
            ##########################
            # DSpace installation directory. This is the location where you want
            # to install DSpace. NOTE: this value will be copied over to the
            # "dspace.dir" setting in the final "dspace.cfg" file. It can be
            # modified later on in your "dspace.cfg", if needed.
            dspace.install.dir = /home/dspace
            # DSpace host name - should match base URL. Do not include port number
            dspace.hostname = \%scholar.sun.ac.za\%
            # DSpace base host URL. Include port number etc.
            dspace.baseUrl = \%http://scholar.sun.ac.za\%
            # The user interface you will be using for DSpace. Common usage is
            either xmlui or jspui
            dspace.ui = xmlui
            # Full link your end users will use to access DSpace. In most cases,
            this will be the baseurl followed by
            # the context path to the UI you are using.
            #
            # Alternatively, you can use a url redirect or deploy the web
            application under the servlet container root.
            # In this case, make sure to remove the /\${dspace.ui} from the
            dspace.url property.
            dspace.url = \${dspace.baseUrl}
            # Name of the site
            dspace.name = \%SUNScholar Research Repository\%
            # Solr server

            solr.server = http://localhost/solr
            # Default language for metadata values
            default.language = \%en_ZA\%
            ##########################
            # DATABASE CONFIGURATION #
            ##########################
            # Uncomment the appropriate block below for your database.
            # postgres
            db.driver=org.postgresql.Driver
            db.url=jdbc:postgresql://localhost:5432/dspace
            db.username=\%dspace\%
            db.password=\%dspace\%
            # oracle
            #db.driver= oracle.jdbc.OracleDriver
            #db.url=jdbc:oracle:thin:@//localhost:1521/xe
            #db.username=dspace
            #db.password=dspace
            # Schema name - if your database contains multiple schemas, you can
            avoid
            # problems with retrieving the definitions of duplicate object names
            by
            # specifying the schema name that is used for DSpace.
            # ORACLE USAGE NOTE: In Oracle, schema is equivalent to "username".
            This means
            # specifying a "db.schema" is often unnecessary (i.e. you can leave
            it blank),
            # UNLESS your Oracle DB Account (in db.username) has access to
            multiple schemas.
            db.schema =
            # Maximum number of DB connections in pool
            db.maxconnections = 50
            # Maximum time to wait before giving up if all connections in pool
            are busy (milliseconds)
            db.maxwait = 5000
            # Maximum number of idle connections in pool (-1 = unlimited)
            db.maxidle = 150
            # Determine if prepared statement should be cached. (default is true)
            db.statementpool = true
            # Specify a name for the connection pool (useful if you have multiple
            applications sharing Tomcats dbcp)
            # If not specified, defaults to ’dspacepool’
            
            #######################
           --------------------------------------------------------------------------------------------------
           Keep Other parts of the local.cfg file as it is.
           Save and Exit.
           
      3.4  Build the DSpace Java webapps
      
          $ sudo chown dspace.dspace -R $HOME
          
          $ sudo chmod 0777 -R $HOME

          $ cd $HOME/source
          
          $ mvn -U clean package
          
         A lot of stuff will start to be downloaded and scroll by on the screen.
         When complete you will get a message at the end like this:
         -----------------------------------------------------------------------------------------------------
          [INFO] ------------------------------------------------------------------------
          [INFO] ------------------------------------------------------------------------
          [INFO] BUILD SUCCESSFUL
          [INFO] ------------------------------------------------------------------------
          [INFO] Total time: 5 minutes 15 seconds
          [INFO] Finished at: Fri Aug 03 13:45:02 SAST 2012
          [INFO] Final Memory: 95M/273M
          [INFO] 
         ------------------------------------------------------------------------
         -----------------------------------------------------------------------------------------------------
     
     3.5 Install the DSpace Java webapps
     
          $ cd $HOME/source/dspace/target/dspace-installer
          
          $ ant fresh_install
          
     3.6  Create the DSpace super-admin user
     
      3.6.1 Stop the Tomcat server
      
          $ cd
          
          $ sudo service tomcat7 stop
          
      3.6.2 Create the account
      
          $ $HOME/bin/dspace create-administrator
          
          ---------------------------------------------------------------------------------------
            Creating an initial administrator account
            E-mail address: %emailaddress%
            First name: Hilton
            Last name: Gibson
            WARNING: Password will appear on-screen.
            Password: XXXXXXXX
            Again to confirm: XXXXXXXX
            Is the above data correct? (y or n): y
            Administrator account created
          ------------------------------------------------------------------------------------------
          
      3.6.3 Start the Tomcat server
      
          $ cd
          
          $ sudo service tomcat7 start
          
     3.7 Create web application shortcuts
     
          $ cd /var/lib/tomcat7/webapps
          
          $ sudo ln -s /home/dspace/webapps/solr
          
          $ sudo ln -s /home/dspace/webapps/rest
          
          $ sudo ln -s /home/dspace/webapps/oai
          
          $ sudo ln -s /home/dspace/webapps/sword
        
      3.7.1  Configure the default ROOT webapp
      
          $ sudo rm -rf /var/lib/tomcat7/webapps/ROOT
          
          For JSPUI :
          
          $ cd /var/lib/tomcat7/webapps
          
          $ sudo ln -s /home/dspace/webapps/jspui ROOT
          
          $ cd /var/lib/tomcat7/webapps
          
          $ sudo ls -l
          
      3.7.2 Restart Tomcat Server
      
          $ Restart Tomcat Server
          
     3.8 Perform system tests
     
          $ cd
          
          $ sudo $HOME/bin/dspace database test
          
          $ sudo $HOME/bin/dspace database info
          
     3.9 Restart your server
     
          $ sudo reboot
          
          =========================================================================================================
          
          Please Note:
          If you have installed a test version of DSpace on a local Ubuntu computer,
          then type the following to connect to your test server.
          
          http://localhost
          
          ==========================================================================================================
          
          
4. To Activate the APIs for DSpace
        Disabling SSL          
                  For localhost development purposes, SSL can add additional getting-started difficulty,
                  so security can be         disabled. To disable DSpace REST's requirement to require security/ssl, 
                  [dspace-source]/dspace-rest/src/main/webapp/WEB-INF/web.xml and comment out the 
                  <security-constraint> block, and restart your servlet container.
  
      
     4.1 Create a scripts folder
     
        $ mkdir $HOME/scripts
        
     4.2  Create the script
     
        $ nano $HOME/scripts/build-webapps
     
        Copy and paste the following into the open nano editor.
        ------------------------------------------------------------------------------------------------------
          #!/bin/bash
          
          
          sudo service tomcat7 stop
          sleep 3

          #### Optional ####
          #Remove old cache and log files. Uncomment below to enable.
          #echo "Clean out old xmlui cache files"
          #sudo rm /var/lib/tomcat7/work/Catalina/localhost/_/cache-dir/cocoon-ehcache.data
          #sudo rm /var/lib/tomcat7/work/Catalina/localhost/_/cache-dir/cocoon-ehcache.index
          #echo "Remove old catalina log file"
          #sudo rm /var/log/tomcat7/catalina.out

          #### Optional ####
          #Remove old webapps. Uncomment below to enable.
          #echo "Clean out old webapps"
          #sudo rm -rf /home/dspace/webapps/*

          #### Optional ####
          #Remove old config folder. Uncomment below to enable.
          #echo "Clean out old configs"
          #sudo rm -rf /home/dspace/config/*

          echo "Start MAVEN build"
          cd /home/dspace/source
          mvn -U clean package

          echo "Start ANT updates"
          cd /home/dspace/source/dspace/target/dspace-installer
          ant update

          #### Optional ####
          #Clean backups. Uncomment below to enable.
          #ant clean_backups

          #### Optional ####
          #Overwrite configs. Uncomment below to enable.
          #ant -Doverwrite=true update_configs

          #### Optional ####
          #Geolite database updates.
          # !!!! Your server should be open on the internet before you do this !!!!
          #Uncomment below to enable.
          #sudo ant update_geolite

          #### Optional ####
          #Fix file and folder permissions. Uncomment below to enable.
          #echo "Fixing file permissions. Please wait..."
          #sudo chmod 0777 -R /home/dspace/config
          #sudo chmod 0777 -R /home/dspace/log
          #echo "Fixing file ownership. Please wait..."
          #sudo chown dspace.tomcat7 -R /home/dspace/config
          #sudo chown dspace.tomcat7 -R /home/dspace/log

          sleep 2
          sudo service tomcat7 restart

          echo "Rebuild complete."
        -------------------------------------------------------------------------------------------------------

     4.3 Make the script executable
     
        $ chmod 0755 $HOME/scripts/build-webapps
  
     4.4 Run the script
     
        $ $HOME/scripts/build-webapps
        
        ===============================================================================================================
        
        You can check the working of DSpace APIs using below command through terminal.
        
        $ curl http://localhost/rest/test
        
        Response Example:
        ------------------------------------
        REST api is running.
        ------------------------------
        

          =============================================================================================================
       
           
