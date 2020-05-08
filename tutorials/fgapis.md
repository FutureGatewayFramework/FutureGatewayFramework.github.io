---
layout: default
exclude: true
---
###### Back to [Resources](/resources) page

# FutureGateway APIs tutorial

This step by step guide intoduces to the installation and test of the FutureGateway (FG)
To perform the tutorial, your environment have to satisfy the following **requirements**.

* **Docker** installation with its **compose** capability;
* **Make** GNU make utility.

Tests peformed by this procedure are the same used to test *Ubuntu 18.04* installation scripts and FG core tests perfomed by its development operations.

By this tutorial the user will:

|---|---|
|[Prepare the test environment](#prepare-the-test-environment)|Extract FG setup sources from the repository|
|[Start the installation](#start-the-installation)|Start the installation script|
|[Full testing](#full-testing)|Configure the isntalled environment to host tutorial test|
|[FutureGateway APIS demo](#futureGateway-apis-demo)|Entry point for API usage examples covered by this tutorial|
|[Obtain credentials](#obtain-credentials)|Explaination about FG API credentials and their usage|
|[Create the infrastructure](#create-the-infrastructure)|Simple *infrastructure* creation example|
|[Get infrastructure info](#get-infrastructure-info)|Retrieve information about an **infrastructure**|
|[Application setup](#application-setup)|Setup a FutureGateway **application**|
|[Show application details](#show-application-details)|Retrieve infromation about an **application**|
|[View application input file status (see path value)](#View-application-input-file-status-(see-path-value))|Retrieve the list of application input files|
|[Add files to the application](#add-files-to-the-application)|Add files to an application|
|[Execute the application](#execute-the-application)|FutureGateway **task** creation, how to execute an **application**|
|[View task details](#view-task-details)|Retrieve information about a **task**|
|[View task status](#view-task-status)|Get **task** status directly|
|[Example with python code execution](#example-with-python-code-execution)|Creation of an application executin a python code|
|[Create the Pyhon application task](#create-the-pyhon-application-task)|Executes the python application|
|[Task related input files](#task-related-input-files)|Provides an example of how define applications providing input files at **task** submission level|
|[Resources](#resources)|List of useful resources to learn more on FG API usage|

## Prepare the test environment

The installation will be performed from a test machine named *fgtest*. To start this machine, use the standard docker run command paying particular attention to the **port** settings used by the FG as specified in the Makefile.

This tutorial uses code branch *py2py3*, however it should work as well with the master branch; the *py2py3* is the next release candidate branch.

```bash
git clone https://github.com/FutureGatewayFramework/fgSetup.git -b py2py3
cd fgSetup/test
make image
make run
```

During the installation the following components will be installed:

* fgdb - FG database component;
* fgapiserver - The API Server responsible for REST API processing;
* apiserverdaemon - The component capable to execute tasks on top of **Distributed Computing Infrastructures** DCIs;
* fgliferay - The user may also extend the test to the case of GUI interfaces development using this component.

## Start the installation

The command below wil start the FutureGateway installation process using script based installation on top of the OS: *Ubuntu 18.04*.
The whole operation may require a long while to complete and errors will be eventually reported. Please do not misunderstand reported errors like *tty streams* from real errors. At the end of installation phase only UGR API set will be automatically tested.

```bash
make test
```

## Full testing

The following steps are necessary to setup the core set fo the FG APIs consisting of:

* *Infrastructure* setup, a simple **ssh** resource node accessed by GriEngine executor interface;
* *Application setup*, a very simple application which execute a script accepting parameters, input files and producing an output file;
* *Task execution* The installed application will be executed on the test infrastructure and the resutls will be reported.

To start this operation it is necessary to operate inside the running container, this operation is possible using the command:

```bash
make conn
```

If successful the command above will start a shell session (bash) on the *fgtest* container.

The following commands will reconfigure the APIServer to swhitch off the PTV API handling and configure the SSH node specified by the test infrastructure to point to the *fgtest* machine (localhost).

```bash
sudo su - futuregateway
cd $HOME/fgAPIServer
cp /home/fgtest/fgSetup/docker/test_futuregateway.sh ./test_futuregateway.sh
ESC_FGHOST=$(echo "localhost/fgapiserver/$FGAPISERVER_APIVER" | sed s/\\//\\\\\\//g)
sed -i "s/^FGHOST.*/FGHOST=$ESC_FGHOST/" test_futuregateway.sh
sed -i "s/  fgapisrv_lnkptvflag.*/  fgapisrv_lnkptvflag: False/" fgapiserver.yaml
sudo su - -c "echo \"127.0.0.1    sshnode\" >> /etc/hosts"
sudo su - -c "echo \"127.0.0.1    fgdb\" >> /etc/hosts"
sudo useradd -m -p $(echo "test" |openssl passwd -1 -stdin)  -s /bin/bash test
```

Now you can execute tests with (optionally, otherwise go to the [API Demo](FutureGateway-APIS-demo)

```bash
./test_futuregateway.sh
```

# FutureGateway APIS demo

This section provides instructions to test FG core APIs using the curl utility.
The way used to perform this test is to cut&paste command reported by this guide inside the *fgtest* container.

## Obtain credentials

Not all FG APIs are publicly accessible and it is necessary to obtain an *access token*.
This tutorial uses **UGR** API calls to generate this kind of credential. Alternatively FG foresees the use of the PTV to manage API credentials, however this modality is not covered by this tutorial.

The following command are necessary to obtain an *access token* associated to the default user **futuregateway**.
**Attention** Be aware that the **futuregateway** user has by default all privileges enabled, be careful using the returned token.

```bash
FGHOST=localhost/fgapiserver/v1.0
FGPASS=$(python -c "import base64
print(base64.b64encode('futuregateway'))")
TEST_USER=test
TEST_PASS=test

curl -s\
     -H "Content-type: application/json"\
     -H "Authorization: futuregateway:$FGPASS"\
     -X POST\
     $FGHOST/auth
```

The output of this API call will be similar to the following output:

```json
{
    "token_info": {
        "user_id": 1, 
        "creation": "2020-05-05T10:20:31Z", 
        "expiry": 86400, 
        "valid": true, 
        "user_name": "futuregateway", 
        "lasting": 86400
    }, 
    "token": "0cc13dab-8eba-11ea-90a1-0242ac110003",
    "_links": [
        {
            "href": "/auth", 
            "rel": "self"
        }
    ]

}
```

The most important part of the output above consists in the `token` field that will be used by the following API call.
Tokens have a limited lifetime and they can be seen as a key received after the login process just executed above.
Normally GUI interfaces may use this mechanism to obtain a token and then generate a 'user' token to be used in the GUI client side.
More details about this mechanism are available in the [PALMS web application](https://github.com/osct/oar-palms/blob/master/futuregateway/liferay7-oar-palms/src/main/resources/META-INF/resources/init.jsp).

For the purposes of this tutorial, it is necessary to keep track of the returned token, executing:

```bash
TKN=0cc13dab-8eba-11ea-90a1-0242ac110003
```

## Create the infrastructure

The futuregateway uses the concept of the *infrastructure* to collect the specific values to describe the target Distributed Infrastructure.
The example uses the SSH infrastructure as it is interpreted by the [GridEngine](https://github.com/csgf/grid-and-cloud-engine/tree/FutureGateway) Executor Interface.
To setup the infrastructure, just execute:

```bash
curl -s\
    -H "Authorization: $TKN"\
    -H "Content-Type: application/json"\
    -X POST\
    -d "{ \"name\": \"Test infrastructure\",
          \"parameters\": [
            { \"name\": \"jobservice\",
              \"value\": \"ssh://sshnode\" },
            { \"name\": \"username\",
              \"value\": \"$TEST_USER\" },
            { \"name\": \"password\",
              \"value\": \"$TEST_PASS\" }],
          \"description\": \"sshnode test infrastructure\",
          \"enabled\": true,
          \"virtual\": false }"\
    $FGHOST/infrastructures
```

This infrastructure targets the SSH node named *sshnode* (localhost in this example) using the specified access credentials to access the node.
The API returns the following json output upon success:

```json
{
    "description": "sshnode test infrastructure 2", 
    "enabled": true, 
    "virtual": false, 
    "_links": [
        {
            "href": "/v1.0/infrastructure/4", 
            "rel": "self"
        }
    ], 
    "date": "2020-05-05T14:30:14Z", 
    "id": "4", 
    "name": "Test infrastructure 1"
}
```

The most important result of this API call is the *id* value.
For the purposes of this tutorial it is necessary to keep track of the infrastructure *id* values executing:

```bash
INFRA_ID=4
```

## Get infrastructure info

To get informations about the installed ifnrastructure, it is possible to execute:

```bash
curl -s\
    -H "Authorization: $TKN"\
    $FGHOST/infrastructures/$INFRA_ID
```

which returns:

```json
{
    "_links": [
        {
            "href": "/v1.0/infrastructure/4", 
            "rel": "self"
        }
    ], 
    "name": "Test infrastructure 1", 
    "parameters": [
        {
            "name": "jobservice", 
            "value": "ssh://sshnode", 
            "description": null
        }, 
        {
            "name": "username", 
            "value": "test", 
            "description": null
        }, 
        {
            "name": "password", 
            "value": "test", 
            "description": null
        }
    ], 
    "date": "2020-05-05T14:30:14Z", 
    "enabled": true, 
    "id": "4", 
    "virtual": false, 
    "description": "sshnode test infrastructure 2"
}
```

## Application setup

The following example setup a standard kind of application that executes a **script** using an input **datafile** and produces an **outptu file**.
This example specify input files statically at applciation level, later will be explained how to specify input files at task submission time using the FG API entrypoint: */application/input*.

```bash
SCRIPT=test.sh
DATAFILE=test.data
OUTFILE=test.out
FILES="\"$SCRIPT\", \"$DATAFILE\""

curl -s\
    -H "Authorization: $TKN"\
    -H "Content-Type: application/json"\
    -X POST\
    -d "{ \"infrastructures\": [ $INFRA_ID ],
          \"parameters\": [
          {
            \"name\": \"target_executor\",
            \"value\": \"GridEngine\",
            \"description\": \"CSGF JSAGA Based executor interface\"},
          { \"name\": \"jobdesc_executable\",
            \"value\": \"$SCRIPT\",
            \"description\": \"\"},
          { \"name\": \"jobdesc_output\",
            \"value\": \"stdout.txt\",
            \"description\": \"\"},
          { \"name\": \"jobdesc_error\",
            \"value\": \"stderr.txt\",
            \"description\": \"\"}],
            \"enabled\": true,
           \"files\": [ $FILES ],
          \"name\": \"test application 1\",
         \"description\": \"Tester application 1\"}"\
    $FGHOST/applications
```

The output of the command above will be similar to:

```json
{
    "files": [
        {
            "override": true, 
            "path": "", 
            "name": "test.sh"
        }, 
        {
            "override": true, 
            "path": "", 
            "name": "test.data"
        }
    ], 
    "_links": [
        {
            "href": "/v1.0/application/4/input", 
            "rel": "input"
        }
    ], 
    "name": "test application 1", 
    "parameters": [
        {
            "name": "target_executor", 
            "value": "GridEngine", 
            "description": "CSGF JSAGA Based executor interface"
        }, 
        {
            "name": "jobdesc_executable", 
            "value": "test.sh", 
            "description": ""
        }, 
        {
            "name": "jobdesc_output", 
            "value": "stdout.txt", 
            "description": ""
        }, 
        {
            "name": "jobdesc_error", 
            "value": "stderr.txt", 
            "description": ""
        }
    ], 
    "infrastructures": [
        4
    ], 
    "enabled": true, 
    "id": "4", 
    "description": "Tester application 1"
}
```

The most important value of the returneed json above is the application identifier, so that it is necessart to store its value in a dedicated shell variable using the command:

```bash
APP_ID=4
```

## Show application details

In a similar fashion already seen for the infrastructures, it is possible to get application details anytime issuing the following command:

```bash
curl -s\
     -H "Authorization: $TKN"\
     $FGHOST/applications/$APP_ID
```

which returns:

```json
{
    "files": [
        {
            "override": true, 
            "path": "", 
            "name": "test.sh"
        }, 
        {
            "override": true, 
            "path": "", 
            "name": "test.data"
        }
    ], 
    "creation": "2020-05-05T14:44:16Z", 
    "_links": [
        {
            "href": "/v1.0/application/4/input", 
            "rel": "self"
        }
    ], 
    "name": "test application 1", 
    "parameters": [
        {
            "name": "target_executor", 
            "value": "GridEngine", 
            "description": "CSGF JSAGA Based executor interface"
        }, 
        {
            "name": "jobdesc_executable", 
            "value": "test.sh", 
            "description": ""
        }, 
        {
            "name": "jobdesc_output", 
            "value": "stdout.txt", 
            "description": ""
        }, 
        {
            "name": "jobdesc_error", 
            "value": "stderr.txt", 
            "description": ""
        }
    ], 
    "outcome": "JOB", 
    "enabled": true, 
    "id": "4", 
    "infrastructures": [
        4
    ], 
    "description": "Tester application 1"
}
```

## View application input file status (see path value)

Application input files may be specified at application level or left them unspecified waiting to specify them at task submission time.
It is possible to get the status of application finput files executing:

```bash
curl -s\
     -H "Authorization: $TKN"\
     $FGHOST/applications/$APP_ID/input
```

which returns:

```json
[
    {
        "override": true, 
        "path": "", 
        "name": "test.sh"
    }, 
    {
        "override": true, 
        "path": "", 
        "name": "test.data"
    }
]
```

The boolean flag *override* is used to distinguish between fixed input files and run-time files, when this flag is true, any attempt to upload an alternative file will be ignored.
The path is the physical location as it is in the API server file system; when it is empty means that no files are yet available for that file record.
The third field in file record represents the file name.

## Add files to the application

This tutorial first create a script file using the command:

```bash
cat >$SCRIPT <<EOF
#!/bin/bash
echo "These are the arguments: " \$@
echo "This is the test script execution"
echo "[data content (begin)]"
cat ${DATAFILE}
echo "[data content (end)]"
echo "This is the output file" > ${OUTFILE}
EOF
chmod +x $SCRIPT
```

Now it is possible to **upload** the file to the API server using the command:

```bash
curl -s\
     -H "Authorization: $TKN"\
     -F "file[]=@$SCRIPT" $FGHOST/applications/$APP_ID/input
```

which returns the json outut:

```json
{
    "files": [
        "test.sh"
    ], 
    "application": "4", 
    "message": "uploaded successfully"
}
```

The message field ensures that the operation was successful

The same steps will be done for the **datafile**, the necessary commands are grouped together:

```bash
# create DATAFILE
cat >$DATAFILE <<EOF
--- !THIS IS THE DATAFILE! ---
EOF
```

```bash
curl -s\
     -H "Authorization: $TKN"\
     -F "file[]=@$DATAFILE" $FGHOST/applications/$APP_ID/input
```

and again, listing the status of the application files the situation will be:

```bash
curl -s\
     -H "Authorization: $TKN"\
     $FGHOST/applications/$APP_ID/input
```

This time the output of file status is different:

[
    {
        "override": true, 
        "path": "apps/4", 
        "url": "file?path=apps%2F4&name=test.sh", 
        "name": "test.sh"
    }, 
    {
        "override": true, 
        "path": "apps/4", 
        "url": "file?path=apps%2F4&name=test.data", 
        "name": "test.data"
    }
]

The path value in the file record is not empty and its value can be used to download its content. In such a case the url to retroeve the file has to be built in the following mode:

```bash
curl -s\
     -H "Authorization: $TKN"\
     "$FGHOST/file?path=apps%2F4&name=test.sh"
```

Please pay attention to the use of the " (column) character specifying the file download API call. Special characters like *&* and other url codes may confuse the shel interpreter.
Executing the command above the *test.sh* file content will be prompted

```bash
#!/bin/bash
echo "These are the arguments: " $@
echo "This is the test script execution"
echo "[data content (begin)]"
cat test.data
echo "[data content (end)]"
echo "This is the output file" > test.out
```


## Execute the application

Finally once defined the infrastructurem, the application and its input files, it is possible to execute a *task*.
In FG terminology, the *tasks* are applciation instances that can be created by the command:

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

which returns the following output upon successful

```json
{
    "status": "SUBMIT", 
    "application": "4", 
    "date": "2020-05-05T15:09:04Z", 
    "description": "testing application 1 with id: 4", 
    "output_files": [
        {
            "url": "", 
            "name": "test.out"
        }, 
        {
            "url": "", 
            "name": "stdout.txt"
        }, 
        {
            "url": "", 
            "name": "stderr.txt"
        }
    ], 
    "_links": [
        {
            "href": "/v1.0/tasks/1", 
            "rel": "self"
        }, 
        {
            "href": "/v1.0/tasks/1/input", 
            "rel": "input"
        }
    ], 
    "user": "00000000-0000-0000-0000-000000000000", 
    "input_files": [
        {
            "status": "READY", 
            "url": "file?path=%2Ftmp%2F4e4ceeee-0211-4f56-ae0e-66223de2cd92&name=test.sh", 
            "name": "test.sh"
        }, 
        {
            "status": "READY", 
            "url": "file?path=%2Ftmp%2F4e4ceeee-0211-4f56-ae0e-66223de2cd92&name=test.data", 
            "name": "test.data"
        }
    ], 
    "id": "1", 
    "arguments": [
        "this is the pased argument app 1"
    ]
}
```

The most important output of the json above is the *task* identifier **task_id**.
For the purpose of this tutorial keep note of it executing:

```bash
TASK_ID=1
```

## View task details

It is posssible to get *task* details using the command:

```bash
curl -s\
     -H "Authorization: $TKN"\
     $FGHOST/tasks/$TASK_ID
```

which produces the output:

```json
{
    "status": "DONE", 
    "description": "testing application 1 with id: 4", 
    "creation": "2020-05-05T15:09:05Z", 
    "iosandbox": "/tmp/4e4ceeee-0211-4f56-ae0e-66223de2cd92", 
    "user": "00000000-0000-0000-0000-000000000000", 
    "id": "1", 
    "output_files": [
        {
            "url": "file?path=%2Ftmp%2F4e4ceeee-0211-4f56-ae0e-66223de2cd92%2F1tmp4e4ceeee02114f56ae0e66223de2cd92_1&name=test.out", 
            "name": "test.out"
        }, 
        {
            "url": "file?path=%2Ftmp%2F4e4ceeee-0211-4f56-ae0e-66223de2cd92%2F1tmp4e4ceeee02114f56ae0e66223de2cd92_1&name=stdout.txt", 
            "name": "stdout.txt"
        }, 
        {
            "url": "file?path=%2Ftmp%2F4e4ceeee-0211-4f56-ae0e-66223de2cd92%2F1tmp4e4ceeee02114f56ae0e66223de2cd92_1&name=stderr.txt", 
            "name": "stderr.txt"
        }
    ], 
    "application": "4", 
    "_links": [
        {
            "href": "/v1.0/tasks/1/input", 
            "rel": "input"
        }
    ], 
    "arguments": [
        "this is the pased argument app 1"
    ], 
    "runtime_data": [], 
    "input_files": [
        {
            "status": "READY", 
            "url": "file?path=%2Ftmp%2F4e4ceeee-0211-4f56-ae0e-66223de2cd92&name=test.sh", 
            "name": "test.sh"
        }, 
        {
            "status": "READY", 
            "url": "file?path=%2Ftmp%2F4e4ceeee-0211-4f56-ae0e-66223de2cd92&name=test.data", 
            "name": "test.data"
        }
    ], 
    "last_change": "2020-05-05T15:09:32Z"
}
```



## View task status

Using jq utility it is possible to retrieve directly the status of the submitted task using:

```bash
curl -s\
      -H "Authorization: $TKN"\
      $FGHOST/tasks/$TASK_ID |jq '.status'
```

Probably the output of this command will be the **"DONE"** status which means the task has successful executed on the destination infrastructure.

## Get task output files

The following shel script command, obtain all output file content

```bash
for url in $(curl -s\
                  -H "Authorization: $TKN"\
                  $FGHOST/tasks/$TASK_ID |\
                  jq '.output_files[].url'|\
                  xargs echo); do\
  echo "[url: $url (begin)]";\
  curl -s\
       -H "Authorization: $TKN"\
       "$FGHOST/$url";\
  echo "[url (end)]"
done
```

```bash
[url: file?path=%2Ftmp%2F4e4ceeee-0211-4f56-ae0e-66223de2cd92%2F1tmp4e4ceeee02114f56ae0e66223de2cd92_1&name=test.out (begin)]
This is the output file
[url (end)]
[url: file?path=%2Ftmp%2F4e4ceeee-0211-4f56-ae0e-66223de2cd92%2F1tmp4e4ceeee02114f56ae0e66223de2cd92_1&name=stdout.txt (begin)]
These are the arguments:  this is the pased argument app 1
This is the test script execution
[data content (begin)]
--- !THIS IS THE DATAFILE! ---
[data content (end)]
[url (end)]
[url: file?path=%2Ftmp%2F4e4ceeee-0211-4f56-ae0e-66223de2cd92%2F1tmp4e4ceeee02114f56ae0e66223de2cd92_1&name=stderr.txt (begin)]
[url (end)]
```


## Example with python code execution

This example uses the same infrastructure to execute **python** executing a given python **source**, use an unput **datafile** and produces and **output** file.

```bash
INFRA_ID=4
SCRIPT=test.py
DATAFILE=pytest.data
OUTFILE=pytest.out
FILES="\"$SCRIPT\", \"$DATAFILE\""
curl -s\
     -H "Authorization: $TKN"\
     -H "Content-Type: application/json"\
     -X POST\
     -d "{ \"infrastructures\": [ $INFRA_ID ],
           \"parameters\": [
           {
             \"name\": \"target_executor\",
             \"value\": \"GridEngine\",
             \"description\": \"CSGF JSAGA Based executor interface\"},
           { \"name\": \"jobdesc_executable\",
             \"value\": \"$SCRIPT\",
             \"description\": \"\"},
           { \"name\": \"jobdesc_output\",
             \"value\": \"stdout.txt\",
             \"description\": \"\"},
           { \"name\": \"jobdesc_error\",
             \"value\": \"stderr.txt\",
             \"description\": \"\"}],
             \"enabled\": true,
           \"files\": [ $FILES ],
           \"name\": \"test application\",
           \"description\": \"Tester python application\"}"\
    $FGHOST/applications
```

From returned json, keep note of the new *applciation* identifier using

```bash
APP_ID=5
```

Now create the **datafile**

```bash
for i in $(seq 1 100); do echo $i >> $DATAFILE; done
```

upload the application **datafile**

```bash
curl -s\
     -H "Authorization: $TKN"\
     -F "file[]=@$DATAFILE" $FGHOST/applications/$APP_ID/input
```

Create and upload the **script**file

```bash
cat >$SCRIPT <<EOF
#!/usr/bin/python
print("This is a python example script")
print("Datafile: ")
with open('$DATAFILE', 'r') as f:
        for l in f:
            if not l.strip():
                break
            print(l)
f=open('$OUTFILE', 'w')
f.write('This is the output file')
f.close()
EOF
chmod +x $SCRIPT

curl -s\
     -H "Authorization: $TKN"\
     -F "file[]=@$SCRIPT" $FGHOST/applications/$APP_ID/input
```

## Create the Pyhon application task

The following command will start a **task** releated to the previous created application:

```bash
curl -s\
     -H "Authorization: $TKN"\
     -H "Content-Type: application/json"\
     -X POST\
     -d "{ \"application\":\"$APP_ID\",
           \"description\":\"testing python application with id: $APP_ID\",
           \"arguments\": [\"this is the pased argument\"],
           \"output_files\": [{\"name\": \"$OUTFILE\"}]}"\
     $FGHOST/tasks
```

Again keep note of the produced task

```bash
TASK_ID=2
```

Then when the status of the task wil be **DONE** execute again the output retrieval command already seen above:

```bash
for url in $(curl -s\
                  -H "Authorization: $TKN"\
                  $FGHOST/tasks/$TASK_ID |\
                  jq '.output_files[].url'|\
                  xargs echo); do\
  echo "[url: $url (begin)]";\
  curl -s\
       -H "Authorization: $TKN"\
       "$FGHOST/$url";\
  echo "[url (end)]"
done
```

## Task related input files

As already notified, application input files can be specified not only at applciation level but also at task level.
The following example creates an application without application level input files. These will be specified after task creation.
The first step for this example is to create an application that uses the same configuration of the applications above, except for the file declaration that is missing in this case.

```bash
curl -s\
     -H "Authorization: $TKN"\
     -H "Content-Type: application/json"\
     -X POST\
     -d "{ \"infrastructures\": [ $INFRA_ID ],
           \"parameters\": [
           {
             \"name\": \"target_executor\",
             \"value\": \"GridEngine\",
             \"description\": \"CSGF JSAGA Based executor interface\"},
           { \"name\": \"jobdesc_executable\",
             \"value\": \"$SCRIPT\",
             \"description\": \"\"},
           { \"name\": \"jobdesc_output\",
             \"value\": \"stdout.txt\",
             \"description\": \"\"},
           { \"name\": \"jobdesc_error\",
             \"value\": \"stderr.txt\",
             \"description\": \"\"}],
             \"enabled\": true,
           \"name\": \"test application\",
           \"description\": \"Tester python application\"}"\
    $FGHOST/applications
```

again, once executed the command above, a json output similar to the following will be prompted:

```json
{
    "files": [], 
    "_links": [
        {
            "href": "/v1.0/application/6/input", 
            "rel": "input"
        }
    ], 
    "name": "test application", 
    "parameters": [
        {
            "name": "target_executor", 
            "value": "GridEngine", 
            "description": "CSGF JSAGA Based executor interface"
        }, 
        {
            "name": "jobdesc_executable", 
            "value": "test.py", 
            "description": ""
        }, 
        {
            "name": "jobdesc_output", 
            "value": "stdout.txt", 
            "description": ""
        }, 
        {
            "name": "jobdesc_error", 
            "value": "stderr.txt", 
            "description": ""
        }
    ], 
    "infrastructures": [
        4
    ], 
    "enabled": true, 
    "id": "6", 
    "description": "Tester python application"
}
```

Please notice the *files* key exists but is left empty.
As already seen by application installations used above, please keep note of the new application identifier using the command:

```bash
APP_ID=6
```
The second step of this example is the **task** creation, specifying 'input_files' option inside its input parameters:

```bash
curl -s\
     -H "Authorization: $TKN"\
     -H "Content-Type: application/json"\
     -X POST\
     -d "{ \"application\":\"$APP_ID\",
           \"description\":\"testing python application with id: $APP_ID\",
           \"arguments\": [\"this is the pased argument\"],
           \"output_files\": [{\"name\": \"$OUTFILE\"}],
           \"input_files\": [{\"name\": \"$SCRIPT\"},
                             {\"name\": \"$DATAFILE\"}]}"\
     $FGHOST/tasks
```

The resulting json output will be similar to:

```json
{
    "status": "WAITING", 
    "application": "6", 
    "date": "2020-05-06T07:17:17Z", 
    "description": "testing python application with id: 6", 
    "output_files": [
        {
            "url": "", 
            "name": "pytest.out"
        }, 
        {
            "url": "", 
            "name": "stdout.txt"
        }, 
        {
            "url": "", 
            "name": "stderr.txt"
        }
    ], 
    "_links": [
        {
            "href": "/v1.0/tasks/3", 
            "rel": "self"
        }, 
        {
            "href": "/v1.0/tasks/3/input", 
            "rel": "input"
        }
    ], 
    "user": "00000000-0000-0000-0000-000000000000", 
    "input_files": [
        {
            "status": "NEEDED", 
            "name": "test.py"
        }, 
        {
            "status": "NEEDED", 
            "name": "pytest.data"
        }
    ], 
    "id": "3", 
    "arguments": [
        "this is the pased argument"
    ]
}
```

Please notice the returned *input_files* key, reporting the status of the task files being specified during task submission, the status **NEEDED** informs that the file has to be specified before task execution on the targeted DCI.
As done by examples above, keep note of the new *task* identifier with:

```bash
TASK_ID=3
```

It is possible to get the status of task input files anytime using the FG API endpoint: */tasks/task_id/input*

```bash
curl -s     -H "Authorization: $TKN"     $FGHOST/tasks/$TASK_ID/input
```

Exactly like in the task submission output json, the result will be the same of its *input_files* key:

```bash
[
    {
        "status": "NEEDED", 
        "name": "test.py"
    }, 
    {
        "status": "NEEDED", 
        "name": "pytest.data"
    }
]
```

Then it is time to upload the files to use for the task submission using the command.
Please notice that the execution will not start until all input files will be available.
Now it is possible to specify the first input file:

```bash
curl -s -H "Authorization: $TKN"  -F "file[]=@$SCRIPT" $FGHOST/tasks/$TASK_ID/input
```

Returned json outptu informs about the status of the task file upload operation:

```json
{
    "files": [
        "test.py"
    ], 
    "message": "uploaded", 
    "task": "3", 
    "gestatus": "waiting"
}
```

The key *gestatus* informs about the task status, the value **waiting** informs that the task is still waiting to complete its list of task input files. The same information can be see issuing again the API command to list **task** input files:

```bash
curl -s     -H "Authorization: $TKN"     $FGHOST/tasks/$TASK_ID/input
```

Returned outptut will be:

```json
[
    {
        "status": "READY", 
        "url": "file?path=%2Ftmp%2Fee7f4124-670f-4b8e-9a75-317b115240c4&name=test.py", 
        "name": "test.py"
    }, 
    {
        "status": "NEEDED", 
        "name": "pytest.data"
    }
]
```

This time the *test.py* file just uploaded is reported as **READY** (available) and the key *url* allows to retrieve the file content anytime as it has been shown in section [Add files to the application](add-files-to-the-application).
The upload file API can accept multiple execution on the same file, the result of this kind of operation will overwrite the content of the of the targeted file.
Now it is possible to complete the list of input file uploading the remaining file with:

```bash
curl -s -H "Authorization: $TKN"  -F "file[]=@$DATAFILE" $FGHOST/tasks/$TASK_ID/input
```

This time the output json will report a different message similar to the output below:

```json
{
    "files": [
        "pytest.data"
    ], 
    "message": "uploaded", 
    "task": "3", 
    "_links": [
        {
            "href": "/v1.0/tasks/3", 
            "rel": "task"
        }
    ], 
    "gestatus": "triggered"
}
```

The key *gestatus* has the values **triggered** meaning that the task has been submitted to the DCI since all required files are now available.
As already seen by examplese above, next steps are the job status check and the output file retrieval.

```bash
curl -s\
      -H "Authorization: $TKN"\
      $FGHOST/tasks/$TASK_ID |jq '.status'

for url in $(curl -s\
                  -H "Authorization: $TKN"\
                  $FGHOST/tasks/$TASK_ID |\
                  jq '.output_files[].url'|\
                  xargs echo); do\
  echo "[url: $url (begin)]";\
  curl -s\
       -H "Authorization: $TKN"\
       "$FGHOST/$url";\
  echo "[url (end)]"
done
```

# Resources

Below several resources that may help to get more familiar with FG API usage.

* Official FutureGateway [documentation](https://github.com/FutureGatewayFramework/fgDocumentation/blob/master/usage.md)
* FutureGatewayFramework [website](https://futuregatewayframework.github.io)
* The source code [repository](https://github.com/FutureGatewayFramework)
