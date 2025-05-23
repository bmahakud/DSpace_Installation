# DSpace_Installation in Ubuntu 22.04lts

 One need to install Apache Maven 3.8.x or above  , Java-17.X , tomcat 10.X, solr 8.11.X, node 20.x and ant 1.X

 ## Directory hierarchy
   -  go to any area inside your laptop 
   -  `mkdir dspace`
   -  `cd dspace`
   -  `mkdir root` # will be used for compiled files
   -  `mkdir servers` # will be used for tomcat and solr installation
   -  `mkdir source`  # will be used for front-end and back-end installatio

 ## Instructions: prerequisites and database 
 -   `sudo apt update && sudo apt install ant -y`
 -    Install Maven #Apache Maven 3.8.x or above
      -  `sudo apt remove maven` # remove any previous old version maven if there
      -  `wget https://dlcdn.apache.org/maven/maven-3/3.9.6/binaries/apache-maven-3.9.6-bin.tar.gz`
      -  `tar -xvzf apache-maven-3.9.6-bin.tar.gz`
      -  `sudo mv apache-maven-3.9.6 /opt/maven`
      -   `sudo vi  ~/.bashrc` # now set the environment and path properly so that when maven commmand runs, it will point to this path . Put the following two lines inside bashrc
      -   `export M2_HOME=/opt/maven`
      -   `export PATH=$M2_HOME/bin:$PATH`
`   
 -   `sudo apt install openjdk-17-jdk`
 -   `sudo apt install  git unzip wget curl -y`
 -   `sudo apt install postgresql postgresql-contrib -y`  # version should 12 , 13 or 14 as on date 28th Feb 2025
 -   `sudo -i -u postgres psql`
 -   `CREATE DATABASE dspace;`
   
     `CREATE USER dspace WITH PASSWORD 'dspace';`
    
     `ALTER DATABASE dspace OWNER TO dspace;`
    
     `GRANT ALL PRIVILEGES ON DATABASE dspace TO dspace;`
     
     `\c dspace`
     
     `CREATE EXTENSION pgcrypto;`
     
     `\q`
     

## Installation of  servers and search engine :  tomcat and solr
-   Apache Solr is an open-source search engine and enterprise search platform. Apache Tomcat is a free, open-source web server and servlet container that's used to host Java-based web applications.
-   dspace code expects some files from solr and tomcat to be present , so we need to download and untar them first
-   `cd servers/`

-   `wget https://dlcdn.apache.org/tomcat/tomcat-10/v10.1.36/bin/apache-tomcat-10.1.36.tar.gz`
-   `tar -xvzf apache-tomcat-10.1.36.tar.gz`

-   `wget https://www.apache.org/dyn/closer.lua/lucene/solr/8.11.4/solr-8.11.4.tgz?action=download -O solr-8.11.4.tgz`
-   `tar -xvzf solr-8.11.4.tgz`

-   `rm -rf solr-8.11.4.tgz apache-tomcat-10.1.36.tar.gz`




## Installation of dspace  Frontend and Backend codes

-   `cd dspace/source`
  
-   `git clone https://github.com/DSpace/DSpace.git backend`
  
-   `git clone https://github.com/DSpace/dspace-angular.git frontend`
  
-   `touch backend/dspace/config/local.cfg`
  
-   `cp backend/dspace/config/local.cfg.EXAMPLE backend/dspace/config/local.cfg`
  
-    Now you need to go to the root folder and print the path of root with the command `pwd`. It will print something like `/home/bibhu/DiracAI-Services/DSpace/root` . Now put this path inside local.cfg  like `dspace.dir=/home/bibhu/DiracAI-Services/DSpace/root`

## Backend setup

-   `cd backend` # Navigate to backend directory 
-   `mvn clean package`  # in case it says The JAVA_HOME environment variable is not defined correctly, you need to do the following
      -  `sudo update-java-alternatives --list`  # Need to check the path of java environment  it will print like `/usr/lib/jvm/java-17-openjdk-amd64`
      -  `sudo vi ~/.bashrc`# add the follwoinftwo lines inside bashrc
      -  `export JAVA_HOME=//usr/lib/jvm/java-17-openjdk-amd64`
      -  `export PATH=$JAVA_HOME/bin:$PATH`

-    `cd dspace/target/dspace-installer`
-    `ant fresh_install` 

-    `cd dspace/root/bin` # go to root/bin folder
-    `sudo ./dspace database migrate`
-    `sudo ./dspace create-administrator`
-    Put the following values while creating an administrator account
-    username = admin@gmail.com
-    firstname = admin
-    lastname = admin
-    Select  when it asks for Is the above data correct? (y or n)
-    password as admin
-    Re-enter the password again
-    `rm -rf /home/bibhu/DiracAI-Services/DSpace/server/solr-8.11.4/server/solr/configsets/*`
-    `cd /home/bibhu/DiracAI-Services/DSpace/server/solr-8.11.4/server/solr/configsets/`
-    `cp -r /home/bibhu/DiracAI-Services/DSpace/root/solr/* .`
-    `cp -r /home/bibhu/DiracAI-Services/DSpace/root/webapps/server /home/bibhu/DiracAI-Services/DSpace/server/apache-tomcat-10.1.36/webapps/`

-  if Envionment variables are not properly set , it migth get error
-  sudo nano /etc/environment  # Look for any existing JAVA_HOME entry and replace it with
-  JAVA_HOME="/usr/lib/jvm/java-17-openjdk-amd64"
-  source /etc/environment
-  echo $JAVA_HOME

 



-      sudo ./solr start -p 8983 -force
-    check if the solr is working by visiting this link: http://localhost:8983/

- export CATALINA_HOME=$HOME/DiracAI-Services/DSpace/server/apache-tomcat-10.1.36  # sometimes it works without this command 
-     cd  ../../apache-tomcat-10.1.36/bin

-     sudo ./startup.sh
-   Check this by visiting this link: http://localhost:8080/server/#/server/api
## Frontend setup

-  now go to frontend cloned path folder

-  `cd ../../../source/frontend`

-  `touch config/config.prod.yml`

- `cp config/config.example.yml config/config.prod.yml`

-  Now open config.prod.yml file and look for the line something like

 `rest:
  ssl: true
  host: sandbox.dspace.org
  port: 443
  nameSpace: /server`

  and change to 

  `rest:
  ssl: false
  host: localhost
  port: 8080
  nameSpace: /server`

  - now start the frontend server
  - `npm install`
  - `nvm use 20` # make sure node version is 20
  - `npm start`
  -  DSpace page will open in this link: http://localhost:4000/

   

## DSpace admin user creation 
 - `cd DSpace/root/bin`
 - `./dspace create-administrator`



   
   
# DSpace API information

## Default DSpace APIs


### Login APIs and login process
  -- The first step is to do GET request to status API to get the CSRF token `http://localhost:8080/server/api/authn/status`
  -- The json return  object for this API: 
     `{
    "id": null,
    "okay": true,
    "authenticated": false,
    "authenticationMethod": null,
    "type": "status",
    "_links": {
        "eperson": {
            "href": "http://localhost:8080/server/api/authn/status/eperson"
        },
        "specialGroups": {
            "href": "http://localhost:8080/server/api/authn/status/specialGroups"
        },
        "self": {
            "href": "http://localhost:8080/server/api/authn/status"
        }
    }`


-- Refer the link for list of apis in communities

https://github.com/bmahakud/DSpace_Installation/blob/main/Communities.md

-- Refer the link for list of apis in collections

https://github.com/bmahakud/DSpace_Installation/blob/main/Collections.md

-- Refer the link for list of apis in Browses

https://github.com/bmahakud/DSpace_Installation/blob/main/Browses.md

-- Refer the link for list of apis in for other endpoints

https://github.com/bmahakud/DSpace_Installation/blob/main/endpoints.md

-- Refer the link for list of apis in for other items

https://github.com/bmahakud/DSpace_Installation/blob/main/eperson.md










  --  Login API 
https://sandbox.dspace.org/server/api/authn/login
https://sandbox.dspace.org/server/api/authn/logout
https://sandbox.dspace.org/server/api/authn/status
https://sandbox.dspace.org/server/api/authz/authorizations
  --  Create Community API
  
  --  Get Community API
  
  --  Update Community API
  
  --  Delete Comminity API
  
  --  Create Collection API
  
  --  Get Collection API
  
  --  Update Collection API
  
  --  Delete Collection API



  
  

  --  Create Item API
  
  --  Get Item API
  
  --  Update Item API
  
  --  Delete Item API
  
=================================================================


  -- Search Menu Dropdown  API

  --- https://sandbox.dspace.org/server/#https://sandbox.dspace.org/server/api/discover/browses

  
  -- Search APIs 
    
  -- By Issue date
  
  --- https://demo.dspace.org/server/api/discover/browses/dateissued/items?sort=default,ASC&page=0&size=20&startsWith=2025&embed=thumbnail&embed=accessStatus
  
  -- By Author 
  
  --- https://demo.dspace.org/server/api/discover/browses/author/entries?sort=default,ASC&page=0&size=20&startsWith=sdgsdfgsd
  
  -- By Title 
  
  --- https://demo.dspace.org/server/api/discover/browses/title/items?sort=dc.title,ASC&page=0&size=20&startsWith=fg&embed=thumbnail&embed=accessStatus
  
  -- By Subject
  
  --- https://demo.dspace.org/server/api/discover/browses/subject/entries?sort=default,ASC&page=0&size=20&startsWith=sxad
  
  -- By Subject Category
  
  --- https://demo.dspace.org/server/api/submission/vocabularies/srsc/entries?filter=zdfg&exact=false

##  Modified DSpace APIs

  -- Modified Search Menu Dropdown  API
  
  --- http://localhost:8080/server/#http://localhost:8080/server/api/discover/browses  
  
  -- Search APIs
  
  -- By Case Type / Number / Year API
  
  --- http://localhost:8080/server/api/discover/search/objects?sort=dc.title,ASC&size=10&f.dc_case_number=8766048755,equals&f.dc_case_type=crime,equals&f.dc_case_year=2025,equals
  
  -- By Judge Name
  
  --- http://localhost:8080/server/api/discover/browses/dc_judge_name/entries?sort=default,ASC&page=0&size=20&startsWith=Judge
     
  -- By Petitioner / Respondent 
  
  --- http://localhost:8080/server/api/discover/search/objects?sort=dc.title,ASC&size=10&f.dc_party_firstrespondent="",equals&f.dc_party_firstpetitioner="",equals
     
  -- By Judgement Date
   
  --- http://localhost:8080/server/api/discover/search/objects?query=dc.date.issued:%5B2002-01-01%20TO%203000-12-31%5D]
     
  -- By FreeText
     
  -- By Video Search

  -- By Gazzette






    



