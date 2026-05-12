NEXUS INSTALLATION 


   launch instance : 
       >>  nexus_server
       >>  t2.medium
   nexus port number : 8081
   once check <free -h> 
===================NEXUS INSTALLATION ON REDHAT ========================
Step 1: Switch to Root User
 >>  sudo su -               #switch to root user
----------------------------------------------------------------------
Step 2: Verify Java Installation
>>  javac -version     #check whether java is installed or not
----------------------------------------------------------------------
Step 3: Install Java
>>  sudo rpm --import https://yum.corretto.aws/corretto.key
      sudo curl -L -o /etc/yum.repos.d/corretto.repo https://yum.corretto.aws/corretto.repo
      sudo yum install java-1.8.0-amazon-corretto-devel --nogpgcheck
---------------------------------------------------------------------
Verify Java Version
>> java -version
---------------------------------------------------------------------
Step 4: Install Required Packages
>> yum install tar wget tree -y
---------------------------------------------------------------------
Step 5: Create Nexus User
>>sudo useradd nexus
---------------------------------------------------------------------
Step 6: Move to /opt Directory
>> cd /opt
---------------------------------------------------------------------
Step 7: Download Nexus
>> sudo wget https://download.sonatype.com/nexus/3/nexus-3.77.2-02-unix.tar.gz
---------------------------------------------------------------------
Step 8: Extract Nexus Package
>> sudo tar -xvzf  nexus-3.77.2-02-unix.tar.gz
---------------------------------------------------------------------
Step 9: Rename Nexus Folder
>>sudo mv nexus-3.77.2-02  nexus
---------------------------------------------------------------------
Step 10: Remove Tar File
>>rm -rf nexus-3.77.2-02-unix.tar.gz
---------------------------------------------------------------------
Step 11: Give Sudo Permission to Nexus User
>>visudo 
    nexus ALL=(ALL) NOPASSWD: ALL
---------------------------------------------------------------------
Step 12: Change Ownership and Permissions
>>sudo chown -R nexus:nexus /opt/nexus
    sudo chown -R nexus:nexus /opt/sonatype-work
    chmod -R 775 /opt/nexus
    chmod -R 775 /opt/sonatype-work
---------------------------------------------------------------------
Step 13: Configure Nexus User
>>sudo vi /opt/nexus/bin/nexus.rc
       #run_as_user="nexus" remove commet
---------------------------------------------------------------------
Step 14: Create Nexus Service File
>> sudo vi /etc/systemd/system/nexus.service
   ```
        [Unit]
Description=Nexus Repository Manager
After=network.target
[Service]
Type=forking
LimitNOFILE=65536
User=nexus
Group=nexus
ExecStart=/opt/nexus/bin/nexus start
ExecStop=/opt/nexus/bin/nexus stop
Restart=on-abort
[Install]
WantedBy=multi-user.target

```
---------------------------------------------------------------------
Step 15: Switch to Nexus User
>> sudo su - nexus
---------------------------------------------------------------------
Step 16: Start Nexus Service
>> sudo systemctl start nexus
---------------------------------------------------------------------
Step 17: Check Nexus Status
>>sudo systemctl status nexus
-------------------------------------------------------------------==========================================================
AFTER COMPLETE SETUP CHECK IP_ADDRESS:8081
CLICK ON SIGN IN AND USER NAME: admin AND PASSWORD WE CAN SET AGAIN LOGIN 
TERMINAL : cat /opt/sonatype-work/nexus3/admin.password 
 LIKE THIS WE GET PASSWORD : 
  copy :  
        << cbab532d-6b44-4ced-ba00-158ae742dba6 >>
PLEASE ENTER PASSWORD AND SAVE : 
ANONYMOUS ACCESS : 
    MOSTLY WE GIVE DISABLE ONLY
DISABLE :
==============================================================================

👉 “In Maven projects, Nexus repository URLs are configured in pom.xml, while authentication credentials like username and password are configured securely in settings.xml.”

using Apache Maven and Sonatype Nexus Repository:
Nexus URL → usually configured in pom.xml
Username/password → usually configured in settings.xml

 Example pom.xml : 
  
<project>
  <groupId>com.company</groupId>
  <artifactId>myapp</artifactId>
  <version>1.0</version>
  <distributionManagement>
    <repository>
      <id>nexus-releases</id>
      <url>
        http://54.158.95.243:8081/repository/maven-releases/
      </url>
    </repository>
    <snapshotRepository>
      <id>nexus-snapshots</id>
      <url>
        http://54.158.95.243:8081/repository/maven-snapshots/
      </url>
    </snapshotRepository>
  </distributionManagement>
</project>
==============================================================================
EXAMPLE settings.xml

cd ~/.m2/settings.xml

<settings>
  <servers>
    <server>
      <id>nexus-releases</id>
      <username>admin</username>
      <password>admin123</password>
    </server>
    <server>
      <id>nexus-snapshots</id>
      <username>admin</username
      <password>admin123</password>
    </server>
  </servers>
</settings>

===============================================================================
HOW WE CAN CHANGE PORT AND CONTEXT PATH OF NEXUS 

[nexus@ip-172-31-1-59 /]$ cd /opt/nexus/etc/
[nexus@ip-172-31-1-59 etc]$ ls
fabric  jetty  karaf  logback  nexus-default.properties  ssl

vi  nexus-default.properties  

## DO NOT EDIT - CUSTOMIZATIONS BELONG IN $data-dir/etc/nexus.properties
##
# Jetty section
application-port=9091
application-host=0.0.0.0
nexus-args=${jetty.etc}/jetty.xml,${jetty.etc}/jetty-http.xml,${jetty.etc}/jetty-requestlog.xml
nexus-context-path=/bala
# Nexus section
nexus-edition=nexus-pro-edition
nexus-features=\
 nexus-pro-feature

systemctl restart nexus

CHECK : http://server-ip:9091/bala
===============================================================================
HOW WE CAN CREATE REPOSITORY  : 
 snapshot repository
  >> go to repository and click create repo
  >> select maven2<hosted>

release repository : 
    >> go to repository and click create repo
    >> select maven2<hosted>


COPY URL : 
release url  : 
 http://65.2.150.21:8081/repository/project-release/
snapshot url : 
http://65.2.150.21:8081/repository/project-snapshot/

we should update in pom.xml

 Example pom.xml : 
  
<project>
  <groupId>com.company</groupId>
  <artifactId>myapp</artifactId>
  <version>1.0</version>
  <distributionManagement>
    <repository>
      <id>nexus-releases</id>
      <url>
        http://65.2.150.21:8081/repository/project-release/
      </url>
    </repository>
    <snapshotRepository>
      <id>nexus-snapshots</id>
      <url> http://65.2.150.21:8081/repository/project-snapshot/ </url>
    </snapshotRepository>
  </distributionManagement>
</project>
===============================================================================
RUN GOAL  :  upload artifact to nexus
mvn clean deploy
===============================================================================
DIFFERENCE BETWEEN SNAPSHOT AND RELEASE : 

snapshot : 
  >>snapshot are used for Development/testing env
>> snapshot will allowing redeploy
   <project>

 <modelVersion>4.0.0</modelVersion>

 <groupId>com.company</groupId>

 <artifactId>myapp</artifactId>

 <version>1.0-SNAPSHOT</version>

</project>

release : 
 >>release are used for production env
>> release will not allowing redeploy

<project>

 <modelVersion>4.0.0</modelVersion>

 <groupId>com.company</groupId>

 <artifactId>myapp</artifactId>

 <version>1.0</version>

</project>
===============================================================================
Role  : 
Read Access  :  nx-repository-view-maven2-*-read
Meaning:
 👉 read all Maven repositories.

Upload Access : nx-repository-view-maven2-*-add
Meaning:
 👉 upload artifacts.

Edit Access : nx-repository-view-maven2-*-edit
Meaning:
 👉 modify repository contents.
Delete Privilege : nx-repository-view-maven2-*-delete
Meaning:
 👉 delete artifacts from Maven repositories.

USERS : 
LOGIN WITH NEW USER WE CAN SEE LIKE THIS :
CLEAN POLICIES : 

