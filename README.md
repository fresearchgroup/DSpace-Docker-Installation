# DSpace-Docker-Installation


                              Steps for installation of DSpace with Docker
                              
                              
# Requirements

      Basic requirement for this installation process:
        Git, Docker, Docker-Compose
        
      $ sudo apt-get install git
      $ sudo apt-get install apt-utils
  
   Docker - https://docs.docker.com/install/linux/docker-ce/ubuntu/#set-up-the-repository

   Docker Compose -- https://docs.docker.com/compose/install/
   
# Installation Steps   

Step-1) Update the System

      $ sudo apt-get update

Step-2) Clone the Repository "Dspace-Labs/dspace-dev-docker"
       
      $ git clone https://github.com/DSpace-Labs/dspace-dev-docker.git
      $ cd dspace-dev-docker

Step-3) Getting DSpace and Unpack DSpace. Change the name dspace of the directory "dspace-6.0-src-release" to "dspace-src" and remove the tar.gz directory.
      
     
      $ wget https://github.com/DSpace/DSpace/releases/download/dspace-6.2/dspace-6.2-src-release.tar.gz
      
      $ tar -xzvf dspace-6.2-src-release.tar.gz
      
      $ mv dspace-6.2-src-release dspace-src
      
      $ rm dspace-6.2-src-release.tar.gz

Step-4) Also, add m2-repo and dspace-build folders.
          
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

Step-9) Edit the "local.cfg" file with the reference bellow.

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
      Also modify the db.url attribute in "local.cfg" file with correct port for postgres.
      
      
Step-10) To Activate the APIs of DSpace, Disable the SSL

      $ cd
      $ cd dspace-dev-docker
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
                 
