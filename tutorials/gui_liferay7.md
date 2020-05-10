---
layout: default
exclude: true
---
###### Back to [Resources](/resources) page

# Liferay GUI Tutorial

This tutorial will guide the reader in the creation of a simple GUI interface provided by the Liferay portal with a FutureGateway instance.
This tutorial is linked with the tutorial: [FutureGateway APIs tutorial](/tutorials/fgapis.html) for this reason, before starting this turoal at least the **fgtest** container must be up and running together with futuregateway services running in it.
Please be aware that Liferay requires a sensitive amount of physical resources ins terms of bot RAM and CPUs. It could be necessary to verify the amount of resources that Docker resevers to the conatainers.

By this tutorial the user will:

|---|---|
|[Start test Liferay container](#start-test-liferay-container)|How to start the test Liferay instance|
|[Prepare development environment](#prepare-development-environment)|This part explains the preparation of the Liferay development environment with liferay|
|[Create the portlet](#create-the-portlet)|Use the Blade CLI to create, deploy and install the GUI structure in the portal|
|[Integrate the portlet with FG APIs](#integrate-the-portlet-with-fg-apis)|How change the structure to manage the necessart back-end and front-end operations and use FG APIs to build the portlet interface|
|[Advanced](#advanced)|Advance testing operations are collected in this section|

# Start test Liferay container

Using the same **make** command used to generate **fgtest** container in the [FutureGateway APIs tutorial](/tutorials/fgapis.html), it is possible to startup another test container providing a Lifieray 7.1 instance. This container will use one of the available images specified in [esystemstech/liferay](https://hub.docker.com/r/esystemstech/liferay) available in [dockerhub](https://hub.docker.com).
Please be aware that the specified version in `Makefile` may become obsolete, for that reason it is recommended to check the available image tags in **dockerhub** and apply the proper value in the `Makefile` variable *LIFERAY_IMAGE* before to start this test. Another important check consists in the usage of port 8080 made both by the container and the host machine. To chage this default behavior the user has to change the `Makefile` accordingly in recipe: *liferay*.
To startup the Liferay machine, just execute:

```bash
make liferay liferay_logs
```

The second **Makefile** recipe above (*liferay_logs*) is optional and it is used just to have a look on Liferay container logs during the Liferay serer starup. To stop logging operations just press CTRL-C.
This step may require a while and it is important to check that the Liferay instance is capable to connect the database provided by the **fgtest** machine.
It will be possible to continue this tutorial only when logs are reporting the final startup message:

```text
<timestamp> INFO  [ org.apache.catalina.startup.Catalina ] Server startup in [xxxxxx] milliseconds
```

Now it is possible to test our running test insace connecting from the container host machine with the address: `http://localhost:8080`.
In case the portal will present the standard web page, it will be possible to access the admin user with credentials:

|---|---|
|**Username**|`test@liferay.com`|
|**Password**|`test`|

Accessing this user it will be possible to manage the whole site.
During the first connection, a secure recovery passphrase will be asked, place your favorite question and related asnwer.

# Prepare development environment]

Liferay suggests the use of its **Blade-CLI** utility to perform development operations. For the purposes of this tutorial, this utility will be used to setup a basic MVC portlet. This tutorial will not go in deep with Liferay MVC portlet management, since for our scope it is only necessary to deal between the bakc-end and front-end provided by the portet.

## Blade CLI

This step cannot be provided by any automated installtion script or a set of commands, since the inolved components may change over time.
Below are only described the necessary steps to donwnload and isntall Blade CLI and prepare its development environment

### Retrieve the Blade CLI software

Liferay provides the page [Installing Blade CLI](https://help.liferay.com/hc/en-us/articles/360017885232-Installing-Blade-CLI) to get infroamation and download the software.
The first operation consists in getting the Liferay SDK package which contains the whole files necessary to setup the Blade CLI. The SDK is available in a dedicated [SourceForge](https://sourceforge.net/projects/lportal/files/Liferay%20IDE/) repository, were developers may download different files accordingly to the available SDK versions and after that he destination operating system. For this tutorial, the user has to select first the directory (for instance 3.8.1/), then download the SDK file: `LiferayWorkspace-<timestamp>-linux-x64-installer.run`.
When the download is completed, move the SDK file from Liferay container host machine to the container with:

```bash
export SDKPATH=<path to the LiferayWorkspace-<timestamp>-linux-x64-installer.run file>
```

Then execute:

```bash
make liferaydev
```

Now it is possible to start the SDK installation, this operation is perfomed as non root user, while answers to questions prompted by the setup are explained below the command:

```bash
make liferaysdk
```

Most of all options to specify are already suggested as *default* you can select them just pressing RETURN; below the description and values to specify for each promted option.

|---|---|
|Please select the Java(tm) Runtime to use|Accodrdingly to the image related to this tutorial, use default value (1)|
|Initilalize Liferay Workspace directory|Initialization is the default option, plaes select it (1)|
|Liferay Workspace Directory|Again use the path suggested to host the workspace|
|DXP Bundle or Liferay Portal Community Edition Bundle|Again the default option is the choice|
|Specify initialized liferay bundle version|This time you may have to change the default setting, use the value that refers tot he Liferay images taken from the **dockerhub**, it should be (2) Liferay Bundle 7.1|
|Proxy Configuration|Just skip it (default)|

Finally confirm the installation entering the 'y' key (default).
The installation requires just few seconds and at the end it prompts the successful operation with the message:

```text
Setup has finished installing Liferay Workspace on your computer.
```

# Create the portlet

It is now possible to create the portlet that will be out base structure for the FG GUI example. To accomplish this operation, it is necessary to connect the running **fgliferay** container, this is easily possible using *docker exec* command or using directly:

```bash
make liferaydev_conn
```

Then it is necessary to enter the *liferay-workspace* directory created during the Liferay SDK installation at the step above.

```bash
cd liferay-workspace
```

To create the portlet structure it is necessart to call the **blade** CLI with:

```bash
blade create -t mvc-portlet guitest
```

The output of this command will be:

```text
Successfully created project guitest in /home/liferaydev/liferay-workspace/modules
```

Our project directory will be then: `/home/liferaydev/liferay-workspace/modules/guitest`, then enter this directory

```bash
cd modules/guitest
```

The structure of our project is quite simple

```bash
.
./src
./src/main
./src/main/resources
./src/main/resources/content
./src/main/resources/content/Language.properties
./src/main/resources/META-INF
./src/main/resources/META-INF/resources
./src/main/resources/META-INF/resources/css
./src/main/resources/META-INF/resources/css/main.scss
./src/main/resources/META-INF/resources/init.jsp
./src/main/resources/META-INF/resources/view.jsp
./src/main/java
./src/main/java/guitest
./src/main/java/guitest/constants
./src/main/java/guitest/constants/GuitestPortletKeys.java
./src/main/java/guitest/portlet
./src/main/java/guitest/portlet/GuitestPortlet.java
./.gitignore
./build.gradle
./bnd.bnd
```

For the purposes of this tutorial, this tutorial will principally use the following files:

|---|---|
|`./src/main/resources/META-INF/resources`/|This directory holds static content like css, js, images, etc|
|`./src/main/resources/META-INF/resources/init.jsp`|This component is responsible of the back-end operations of the FG GUI portlet|
|`./src/main/resources/META-INF/resources/view.jsp`|This component is responsible of the front-end operations of the FG GUI  portlet|
|`./src/main/java/guitest/portlet/GuitestPortlet.java`|This components is used to manage portlet MVC (not used)|
|`src/main/java`|Any further external java class must be placed here and used by `init.jsp`|

In order to get familiarity with portlet development, the next step will be to compile and then deploy this portlet structure executing:

```bash
blade deploy
```

Almost at the end of the command output, the message:

```text
Files of project ':modules:guitest' deployed to `/home/liferaydev/liferay-workspace/bundles/osgi/modules`
```

The message informs about the directory containing the compiled module, the final step consists in deploying the module to the portal with:

```bash
cp /home/liferaydev/liferay-workspace/bundles/osgi/modules/guitest.jar /opt/liferay/home/deploy/osgi/modules/
```

After the copy operation, Liferay will recognise the presence of the new module in `/opt/liferay/home/deploy/osgi/modules/` directory and will deploy it in the running portal.
To verify the status of this process, it could be useful to watch Liferay log file with:

```bash
make liferay_srvlog
```

Once the portlet will be installed, in the log there will be a message similar to:

```bash
<timstamp> INFO  [ com.liferay.portal.equinox.log.bridge.internal.BundleStartStopLogger:39 ] STARTED guitest_1.0.0 [1010]
```

The last step in this first **portlet** installation will be to include this portlet in the Liferay site.
To perform this operation, follow the steps:

* Access the portal having administration access rights (see: [Start test Liferay container](#start-test-liferay-container)
* Use the top right simbol '+' to enter the *Add* interface that will appear on the right side of the page
* Select *Widgets*, and in the search text field below type `guitest`, an entry with the created portlet should appear below
* Finally, just drag and drop the new portlet inside the web content interface

The new portlet should be available for use.

# Integrate the portlet with FG APIs

Scope of this part of the tutorial consists in the integration of FG APIs inside the portlet created in the [above](#create-the-portlet) step.
This part can be splitted in two separate steps, a back-end and front-end operations
The back-end operation is performed by Liferay at server level when the user accesses the portlet interface. During this phase, FG UGR APIs will be used to:

* Obtain a super-user access token
* Get the Liferay user and register it as FG user is not yet available
* Obtain a delegated access token and pass it to the front-end

At the front-end level,  the interface will use the access token to execute FG APIs

## Manage back-end operations

As explained [above](#create-the-portlet), the back-end operations are in charge of files:

|---|---|
|`src/main/resources/META-INF/resources/init.jsp`||
|`src/main/java`|Any further external java class must be placed here|


First of all, download from [FG Toolkit](https://github.com/FutureGatewayFramework/fgToolkit) repository the java classes used to manage the Liferay backend operations, in particular the class *FutureGatewayAPIs*.

```bash
make liferaydev_conn
git clone https://github.com/FutureGatewayFramework/fgToolkit.git
cp -r fgToolkit/java/it liferay-workspace/modules/guitest/src/main/java/
```

The *FutureGatewayAPIs* class is now accessible by the `init.jsp` file.

Go to the **guitest** portlet example directory and open with an editor (vi is the defaut editor for this developent machine), the file `init.jsp`.

```bash
cd liferay-workspace/modules/guitest/
vi src/main/resources/META-INF/resources/init.jsp
```

Then replace the existing code with:

```javascript
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<%@ taglib uri="http://java.sun.com/portlet_2_0" prefix="portlet" %>
<%@ taglib uri="http://liferay.com/tld/aui" prefix="aui" %><%@
taglib uri="http://liferay.com/tld/portlet" prefix="liferay-portlet" %><%@
taglib uri="http://liferay.com/tld/theme" prefix="liferay-theme" %><%@
taglib uri="http://liferay.com/tld/ui" prefix="liferay-ui" %>
<%@page import="java.io.*" %>
<%@page import="java.net.*" %>
<%@page import="java.util.Base64" %>
<%@page import="com.liferay.portal.kernel.json.*" %> 
<%@page import="it.infn.ct.FutureGatewayAPIs" %>
<liferay-theme:defineObjects />
<portlet:defineObjects />
<%
    // FG settings
    String fgUser = "futuregateway";
    String fgPassword = "futuregateway";
    String fgBaseUrl = "http://fgtest/fgapiserver";
    String fgAPIVer = "v1.0";
    boolean fgStatus = false;
    String errMessage = "";

    // Application settings
    String appName = "test application 1";
    int appId = 4;
    String appGroup = "users";
    int appGroupId = 2;
    String fgsgGroup = "fgsg_user";

    // Initialize FutureGatewayAPIs object
    FutureGatewayAPIs fgAPIs =
        new FutureGatewayAPIs(fgBaseUrl, fgAPIVer, fgUser, fgPassword);

    // Check FG service
    fgStatus = fgAPIs.checkServer();

    // Retrieve user account information
    String screenName = user.getScreenName();
    String firstName = user.getFirstName();
    String lastName = user.getLastName();
    String emailAddress = user.getEmailAddress();

    // User settings
    int userId = 0;
    boolean userExists = false;
    boolean userGroup = false;
    String portletAccessToken = "";
    String delegatedAccessToken = "";
    String message = "";

    // Use FGAPIs to check user and retieve tokens
    if(fgStatus) {
        // 1st Get the portlet token
        portletAccessToken = fgAPIs.getAccessToken(fgUser, null);

        // 2nd Check if the user exists
        fgAPIs.setAuthMode(FutureGatewayAPIs.AuthModes.BASELINE_TOKEN);
        userExists = fgAPIs.userExists(screenName);
        if(!userExists) {
            // User does not exists, create it
            fgAPIs.createUser(screenName,
                              firstName,
                              lastName,
                              emailAddress,
                              "");
            // Check if the inserted user now exists
            userExists = fgAPIs.userExists(screenName);
        }

        // 3rd If the user exists take care of its group membership
        if(userExists) {
            userGroup = fgAPIs.userHasGroup(screenName, appGroup);
            if(!userGroup) {
              // Register it if not yet belonging
              String[] userGroups = { "" + appGroupId };
              fgAPIs.addUserGroups(screenName, userGroups);
              // Re-Check
              userGroup = fgAPIs.userHasGroup(screenName, appGroup);
            }
            // 4th Retrieve the delegated access token
            delegatedAccessToken = fgAPIs.getAccessToken(fgUser, screenName);
            fgAPIs.setBaselineToken(delegatedAccessToken);
        } else {
          message = "unable to create FG user";
        }
    }
    // The GUI interface has now enough information to perform next activitis
    // using the FutureGateway APIs with the delegatedAccessToken
%>

<script type="text/javascript">

//FGAPIServer status
fgstatus = "<%= fgStatus %>";

// Collect user account info into FG user info
fg_user_info = {
  name: '<%= screenName %>',
  first_name: '<%= firstName %>',
  last_name: '<%= lastName %>',
  email: '<%= emailAddress %>',
  insitute: 'unknown',
  err_message: '<%= errMessage %>',
  access_token: '<%= delegatedAccessToken %>',
  user_exists: '<%= userExists %>',
  user_group: '<%= userGroup %>',
  portlet_token: '<%= portletAccessToken %>', // To be removed!!!
  message: '<%= message %>',
};

// FG API server settings
fg_api_settings = {
  base_url: 'http://localhost/fgapiserver',
  version: 'v1.0',
  enabled: '<%= fgStatus %>',
};

// Application settigns
fg_app_settings = {
  name: '<%= appName %>',
  id: '<%= appId %>',
  group_name: '<%= appGroup %>',
  group_enabled: '<%= userGroup %>',
};
</script>
```

Despite the number of code lines, the purpose of the back-end code above is really simple especially because FG APIs are handled at high level by the java class **FutureGatewayAPIs** making the source more readable. Steps performed by this code can be summarised by:

* Retrieve portal user information from Liferay
* Check the connection with the FG
* Retrieve a *super user* access token using credentials having full access rights
* Check if the portal user is already registered, if not it will be registered
* During this operation also the user group membership will be checked and eventually it will be included. For production cases, this step could be subject to a different behavior. In this example the user has to belong to *users* group (baseline entry).
* If the user exists, a delegated token for the user will be generated
* If everithing is fine, the information about the back-end operations are collected inside three javascript variables: *fg_user_info*, *fg_api_settings*, *fg_app_settings*. The first collecting user information, the second for the FG API Server, the third for the application. Please  notice that in *fg_api_settings*, the **base_url** field is different than the one used inside the `init.jsp` file, this because the client side of the portlet may address differently the API server address.

It is possible to view the content of the javascript variables above, accessing the portal opening the Browser inspector and using its console.

## Manage fron-end

To manage the GUI interface at client level, it is needed to operate on the file:

|---|---|
|`src/main/resources/META-INF/resources/view.jsp`||

Liferay 7.1 supports both [JQuery](https://jquery.com) and [Bootstrap](https://getbootstrap.com), this helps much making really easy the integration of a GUI interface for the FG Applications using FG APIs.
The GUI interface will provide:

  * A different interface in the case the user is logged and capable to run the application
  * A simple user interface made of a submission area and a list of submitted tasks below.

This tutorial will use as FG Application the first application used in the [FutureGateway APIs]({{ site.baseurl }}{% link tutorials/fgapis.md %}) tutorial. This is one of the most simple application to handle and it will require as input only two input parameters.

* A job string identifier
* A String representing the list of arguments used by the destination script in the DCI

Below the che **curl** command used to execute the application:

```bash
curl -s\
     -H "Authorization: $TKN"\
     -H "Content-Type: application/json"\
     -X POST\
     -d "{ \"application\":\"$APP_ID\",
           \"description\":\"testing application 1 with id: $APP_ID\",
           \"arguments\": [\"this is the pased argument app 1\"],
           \"output_files\": [{\"name\": \"$OUTFILE\"}],\
           \"input_files\": [{\"name\": \"$SCRIPT\"}, {\"name\": \"$DATAFILE\"}]}"\
     $FGHOST/tasks
```

Accordlinlgy to the command above, the two input identifiers will populate the input **json** fields: *description* and *arguments*
For the second part of the interface the list of submitted tasks will be placed below the application submission interface.

The client-side code principally manages the portlet web content dinamically using javascript.
To do this, replace the existing **view.jsp** code prepared by the **blade** create command with: 

```html
<%@ include file="/init.jsp" %>

<h3>GUITest Example</h3>
<hr align="left" width="40%">
<!-- main interface -->
<div id="main">
  <p>
  <h4>Task submission</h4>
  <hr align="left" width="70%">
  <!-- Submission interface -->
  <div id="submission">
    <form>
      <div class="form-group">
        <label for="arguments">Arguments</label>
        <input type="text" class="form-control" id="inputArguments" aria-describedby="argumentHelp" placeholder="arg1 arg2 ... argn">
        <small id="argumentHelp" class="form-text text-muted">Place here a space separated list of arguments or use (quotes or double quotes) to specify single arguments having spaces in it.</small>
      </div>
      <div class="form-group">
        <label for="description">Description</label>
        <input type="text" class="form-control" id="inputDescription" placeholder="Job submission description">
      </div>
      <button type="button" class="btn btn-primary" id="buttonSubmit">Submit</button>
    </form>
  </div>
  </p>
  <p>
  <!-- Tasks interface -->
  <h4>Tasks</h4>
  <hr align="left" width="70%">
  <div id="tasks">
  </div>
  </p>
</div>

<!-- interface scripts -->
<script type="text/javascript" src="<%= request.getContextPath() %>/js/fgapis.js"></script>
<script type="text/javascript">

// Build the base URL for FG APIs 
fgAPIBaseURL = fg_api_settings.base_url + '/' + fg_api_settings.version;

// Hold tasks (debugging/development)
tasksData = null;

// Generate the task list
function prepare_task_list() {
  $("#tasks").empty();
  doGet(fgAPIBaseURL +'/tasks',
        fg_user_info.access_token,
        function(data) {
          tasksData = data;
          if(data.tasks.length) {
            for(var i=0; i<data.tasks.length; i++) {
              $("#tasks").append(
                '<div class="row">' +
                '  <div class="col">' + (i+1) + '</div>' +
                '  <div class="col">' + data.tasks[i].date + '</div>' +
                '  <div class="col">' + data.tasks[i].status + '</div>' +
                '  <div class="col">' + data.tasks[i].description + '</div>' +
                '</div>');
            }
          } else {
            $("#tasks").html('<div class="alert alert-primary" role="alert">' +
                             'No tasks are available yet' +
                             '</div>');
          }
        },
        function(data) {
          tasksData = data;
          console.log('ko');
          var message = tasksData.responseJSON.message;
          $("#tasks").html('<div class="alert alert-danger" role="alert">' +
                           'Unable to retrieve the task list: \'' +
                           message + '\'' +
                           '</div>');
        });
}


// Main function (GUI accessible)
function guitest() {
  console.log("guitest front end start");
  prepare_task_list();
}

$( document ).ready(function() {
if(!fg_api_settings.enabled) {
      $("#main").html('<div class="alert alert-danger" role="alert">' +
                      'FG API Server is not reachable, please contact the portal administrator' +
                      '</div>');

    } else if(fg_user_info.user_exists == "false") {
      $("#main").html('<div class="alert alert-danger" role="alert">' +
                      'You need to be logged to access this applciation interface' +
                      '</div>');
    } else {
      guitest();
    }
});
</script>
```

The utility repository [FGTookit](https://github.com/FutureGatewayFramework/fgToolkit) seen in section [Manage back-end operations](#manage-back-end-operations) provides several helper functions to call FG APIs from javascript.
Use the following commands to include them and allowing the code above to find these functions.

```bash
mkdir ~/liferay-workspace/modules/guitest/src/main/resources/META-INF/resources/js
cp ~/fgToolkit/js/fgapis.js\
   ~/liferay-workspace/modules/guitest/src/main/resources/META-INF/resources/js/
```



# Advanced

## Enable logging for FutureGateway components

It is posssible to show log message generated by FutureGateway components prompting this kind of information in Liferay.
To access these logs it is necessary to follow the following stesp:

* Access the administration panel 'I/O' button on the top left corner of the page
* Select menu: **Control Panel/Configuration/Server Administration**
* Click in the page content on the tab label: **LogLevels**
* Select  tab voice: **Add Category** and place LoggerName: **it.infn.ct**, Log Level: **ALL**, then press **Save** button

Then new message will be prompted in liferay log file:

```bash
make liferaydev_logs
```


## INDIGO liferay plugins
curl -L https://github.com/indigo-dc/LiferayPlugIns/releases/download/2.2.1/LiferayPlugins-binary-2.2.1.tgz --output LiferayPlugins-binary-2.2.1.tgz

## GUI (Liferay), old tutorial

```
docker run -d --name fgliferay -p 28080:8080 --link fgtest_0.2 -e JAVA_OPTS="-XX:NewSize=700m -XX:MaxNewSize=700m -Xms1024m -Xmx1024m -XX:MaxPermSize=128m -XX:+UseParNewGC -XX:+UseConcMarkSweepGC -XX:+CMSParallelRemarkEnabled -XX:SurvivorRatio=20 -XX:ParallelGCThreads=8" esystemstech/liferay:7.0.5-ga6

apt-get update
apt-get install -y --no-install-recommends\
            procps\
            curl\
            ca-certificates\
            sudo\
            git\
            mysql-client\
            mlocate\
            vim\
            gnupg\
            build-essential\
            locales\
            jq\
            nodejs

## Nodejs - https://hostadvice.com/how-to/how-to-install-node-js-on-ubuntu-18-04/
curl -sL https://deb.nodesource.com/setup_8.x | sudo bash -
apt-get install -y nodejs

npm install -g yo gulp jquery
npm install -g generator-liferay-theme@7.0.5

## SDK https://sourceforge.net/projects/lportal/files/Liferay%20IDE/

curl -L https://sourceforge.net/projects/lportal/files/Liferay%20IDE/3.7.1/LiferayProjectSDK-201910152009-linux-x64-installer.run/download --output LiferayProjectSDK-201910152009-linux-x64-installer.run

chmod +x LiferayProjectSDK-201910152009-linux-x64-installer.run

./LiferayProjectSDK-201910152009-linux-x64-installer.run

git clone https://github.com/liferay/liferay-blade-samples.git
cd liferay-blade-samples
./build.sh

blade samples -v 7.0 jquery-npm-portlet

blade init -v 7.0 liferay_workspace

## https://github.com/liferay/liferay-blade-samples

blade create -t npm-jquery-portlet testgui

blade create -t npm-jquery-portlet -v 7.0 testgui
```





