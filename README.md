# DSpace_Installation in Ubuntu 22.04lts

 One need to install maven 3.X , Java-17.X , tomcat 10.X, solr 8.11.X, node 20.x and ant 1.X

 ## Directory hierarchy 
     -   `mkdir dspace` # anywhere inside your laptop
     -   `cd dspace` 
     -   `mkdir root` 
     -   `mkdir source` # for back-end and front-end repos
     -   `mkdir servers`  # for tomcat and solr
     
 ## Instructions: Database and prerequisites 
 -   `sudo apt install maven`
 -   `sudo apt install openjdk-17-jdk`
 -   `sudo apt update && sudo apt install ant -y`
 -   `sudo apt install  git unzip wget curl -y`
 -   `sudo apt install postgresql postgresql-contrib -y`  # version should 12 , 13 or 14 as on date 28th Feb 2025
 -   `sudo -i -u postgres psql`
 -   `CREATE DATABASE dspace;`
   
     `CREATE USER dspace WITH PASSWORD 'dspace';`
    
     `ALTER DATABASE dspace OWNER TO dspace;`
    
     `GRANT ALL PRIVILEGES ON DATABASE dspace TO dspace;`


     
   make a seperate directory for the dspace and path/to/dspace and lets mark this path as [dspace]



## Instructions: Servers tomcat and solr
   now go to [dspace] / servers

   
   wget https://dlcdn.apache.org/tomcat/tomcat-10/v10.1.36/bin/apache-tomcat-10.1.36.tar.gz
   tar -xvzf apache-tomcat-10.1.36.tar.gz

   wget https://www.apache.org/dyn/closer.lua/lucene/solr/8.11.4/solr-8.11.4.tgz?action=download -O solr-8.11.4.tgz
   tar -xvzf solr-8.11.4.tgz

## Instructions: DSpace installation and setup
    



