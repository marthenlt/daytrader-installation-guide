# Deployment &amp; setup guide for DayTrader7 application for WLP (WebSphere Liberty Profile) 20.0.0.6 on Oracle Java 8

Running DayTrader7 application on a newer WLP & Java versions.

Source:
- https://www.ibm.com/support/knowledgecenter/linuxonibm/liaag/wascrypt/l0wscry00_daytrader.htm
- https://github.com/WASdev/sample.daytrader7

The DayTrader7 application (see the above github repo) is meant for old WLP running on Java 7. 
So if you are using WLP 20.0.0.6 on Oracle Java 8, you might encounter some issues while deploying this application.

Here is the step-by-step guide on how to deploy the application:
1) DayTrader7 application requires below features where some are not by default available on my WLP 20.0.0.6 on Java 8.

        <feature>ejb-3.2</feature>
        <feature>servlet-3.1</feature>
        <feature>jsf-2.2</feature>
        <feature>jpa-2.1</feature>
        <feature>mdb-3.2</feature>
        <feature>wasJmsServer-1.0</feature>
        <feature>wasJmsClient-2.0</feature>
        <feature>cdi-1.2</feature>
        <feature>websocket-1.1</feature>
        <feature>concurrent-1.0</feature>
        <feature>jsonp-1.0</feature>
        <feature>beanValidation-1.1</feature>
        <feature>localConnector-1.0</feature>

   For instance, based on this IBM documentation https://www.ibm.com/support/knowledgecenter/SSEQTP_liberty/com.ibm.websphere.wlp.doc/ae/rwlp_feat.html
   WLP 20.0.0.6 is already running Servlet 4.0, whereas the DayTrader7 application still requires Servlet 3.1

   So, I have to install it manually using:
   
        <WLP_DIR>/bin/installUtility install servlet-3.1
        <WLP_DIR>/bin/installUtility install jsf-2.2
        <WLP_DIR>/bin/installUtility install cdi-1.2
        <WLP_DIR>/bin/installUtility install jsonp-1.0
        <WLP_DIR>/bin/installUtility install beanvalidation-1.1
        <WLP_DIR>/bin/installUtility install jpa-2.1

   <WLP_DIR> is where your WLP installation directory.
   
   Keep in mind available features may varies from your WLP specific version that you installed.
   Thus it is important to see the WLP console.log file during the server start up to catch more precise features that aren't available in your WLP.

   If you are using WLP for Java 7 earlier version, you "may" no need to do the above step manually.
   But still you need to tail the console.log to see whether the required features specified in the deployment configuration (server.xml) are available in that WLP version.


2) For some reasons the EAR file is not being copied to daytrader-ee7-wlpcfg/servers/daytrader7Sample/apps/ folder
   So, I ran below command to copy it manually:
   
        cp daytrader-ee7/target/daytrader-ee7-1.0-SNAPSHOT.ear daytrader-ee7-wlpcfg/servers/daytrader7Sample/apps/daytrader-ee7.ear


3) Also the Derby library (derby-10.10.1.1.jar) inside the server.xml configuration for JNDI datasource is not available.
   So, I have to manually copy it there since I found it available in my MVN local repository.
   Here's the command I used to copy Derby JAR library:
   
        cp ~/.m2/repository/org/apache/derby/derby/10.10.1.1/derby-10.10.1.1.jar daytrader-ee7-wlpcfg/shared/resources/Daytrader7SampleDerbyLibs/.


4) Start the application using:

        export WLP_USER_DIR=<YOUR_DAY_TRADER/sample.daytrader7/daytrader-ee7-wlpcfg
        <YOUR_WLP_INSTALLATION_DIRECTORY>/bin/server start daytrader7Sample

   Restart the DayTrader application. It should started successfully now.

   Observe the log:
   
        tail -f ${WLP_USER_DIR}/servers/daytrader7Sample/logs/console.log


5) Next step is to create DayTrader database schema (for Derby). Go to http://localhost:9082/daytrader/ and click "Configuration" button at the top, and select "(Re)-create  DayTrader Database Tables and Indexes".
   Wait for a while.


6) Final step is to repopulate DayTrader application with sample data. Go to http://localhost:9082/daytrader/ and click "Configuration" button at the top, and select "(Re)-populate  DayTrader Database".
   Wait for a while this also will take a moment to populate the sample data.
   Once done, you're good to go to test this application and playing around with it.


Hope it helps folks who are interested with running DayTrader7 application on newer WLP on newer Java version.

-- Marthen Luther

