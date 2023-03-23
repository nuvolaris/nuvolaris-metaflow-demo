<!--
  ~ Licensed to the Apache Software Foundation (ASF) under one
  ~ or more contributor license agreements.  See the NOTICE file
  ~ distributed with this work for additional information
  ~ regarding copyright ownership.  The ASF licenses this file
  ~ to you under the Apache License, Version 2.0 (the
  ~ "License"); you may not use this file except in compliance
  ~ with the License.  You may obtain a copy of the License at
  ~
  ~   http://www.apache.org/licenses/LICENSE-2.0
  ~
  ~ Unless required by applicable law or agreed to in writing,
  ~ software distributed under the License is distributed on an
  ~ "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
  ~ KIND, either express or implied.  See the License for the
  ~ specific language governing permissions and limitations
  ~ under the License.
  ~
-->
# Welcome to Nuvolaris Metaflow Plugin Demo

Nuvolaris is an [Open Source](https://opensource.org/) project to build a complete and portable [Serverless](https://martinfowler.com/articles/serverless.html) environment that runs in every [Kubernetes](https://kubernetes.io/). Technically it is a distribution of [Apache OpenWhisk](https://openwhisk.apache.org) released under the [Apache Licence 2.0](LICENSE) (like OpenWhisk itself), that includes other services out of the box.

[Metaflow](https://outerbounds.com) is an open source Machine Learning framework implemented and open sourced by [Netflix](https://github.com/Netflix/metaflow).

This repository provides an overview of the Nuvolaris Metaflow plugin, which is giving the possibility to execute Metaflow steps as serverless function executed on an OpenWhisk engine.

The repository it is mainly composed of two submodules:

- `metaflow` a fork of Metaflow 2.7.14
- `nuvolaris-metaflow` which is containing the source code of the Nuvolaris plugin

The submodules `nuvolaris-metaflow` contains some Metaflow example inside `examples` folders.

We recommend to use an AWS EC2 machine to test the Nuvolaris Metaflow plugin, but alternatively it is possible to use also a local installation of Nuvolaris with docker. 

## Setup Nuvolaris and required S3 credentials

Install Nuvolaris following the instructions available at [Nuvolaris EC2 Installation](https://www.nuvolaris.io/documentation/installation/how-to-install-on-aws-ec2), executing the command

```
curl -s https://www.nuvolaris.io/scripts/ubuntu-ec2.sh | sudo bash
```
if everything is ok you should see an output similar to this one
```
You are all set! Thanks for using Nuvolaris
*** Command to connect to Nuvolaris in ec2-xxx-xxx-xxx-xx.compute-1.amazonaws.com ***
nuv wskprops --apihost=http://xxxxx.compute-1.amazonaws.com --auth=xxxx:yyyyy
```

Take note of the `apihost` and `auth` parameters as they will be required to properly configure the Nuvolaris Metaflow Plugin

Configure valid S3 credentials and setup an S3 bucket. This is required needed to define a metaflow shared storage area.

```
$ aws configure
AWS Access Key ID [None]: <your AWS Key Id>
AWS Secret Access Key [None]: <your AWS access key>
Default region name [None]: <you default region, if any>
Default output format [None]: json

# create an S3 bucket
aws s3api create-bucket --bucket <bucket-name> --region <region-name>
```

## Setup the project an prepare the execution environment

Clone the project repository

```
git clone https://github.com/nuvolaris/nuvolaris-metaflow-demo.git --recurse-submodules
```

Nuvolaris Metaflow plugin it is still a prototype, therefore it is not available as python library yet. To install it

```
cd nuvolaris-metaflow
python3 -m pip install .

# at completion this message should apper
Successfully installed metaflow-2.7.14 nuvolaris-metaflow-0.1.0
```

To complete the configuration we need to setup a configuration file to metaflow. By default the filename is `~/.metaflowconfig/config.json`

```
touch ~/.metaflowconfig/config.json
```

edit the config.json file and add the followjng lines using the values retrieved during Nuvolaris installation and your S3 bucket name.

```
{
  "NUVOLARIS_API_URL":"http://xxxxx.compute-1.amazonaws.com/api/v1/namespaces",
  "NUVOLARIS_API_USER":"xxxx",
  "NUVOLARIS_API_AUTH":"yyyyy",
  "NUVOLARIS_DATASTORE_SYSROOT_S3":"s3://<your_bucket_name>"
}
```

Be sure you have an environment variable USERNAME defined as this is required by Metaflow itslef for tracking reason
```
export USERNAME=<a username you want to use>
```

You are now ready to see the Nuvolaris operation plugin in action, by executing one the examples we provided under `nuvolaris-metaflow/examples`.

You may start from the `helloworld.py` ones for instance, using something like

```
$ python3 examples/helloworld.py run

Metaflow 2.7.14+nuvolaris executing HelloFlow for user:nuvuser
Validating your flow...
    The graph looks good!
Running pylint...
    Pylint is happy!
2023-03-23 14:35:30.853 Workflow starting (run-id 1679582130319283):
2023-03-23 14:35:30.883 [1679582130319283/start/1 (pid 2286486)] Task is starting.
2023-03-23 14:35:32.088 [1679582130319283/start/1 (pid 2286486)] HelloFlow is starting.
2023-03-23 14:35:32.637 [1679582130319283/start/1 (pid 2286486)] Task finished successfully.
2023-03-23 14:35:32.922 [1679582130319283/hello/2 (pid 2286602)] Task is starting.
2023-03-23 14:35:33.636 [1679582130319283/hello/2 (pid 2286602)] creating action mf with memory=256 and timeout=60000
2023-03-23 14:35:33.696 [1679582130319283/hello/2 (pid 2286602)] checking completion of nuvolaris activation aba3937d185349c1a3937d185309c1db
2023-03-23 14:35:35.406 [1679582130319283/hello/2 (pid 2286602)] checking completion of nuvolaris activation aba3937d185349c1a3937d185309c1db
2023-03-23 14:35:38.017 [1679582130319283/hello/2 (pid 2286602)] checking completion of nuvolaris activation aba3937d185349c1a3937d185309c1db
2023-03-23 14:35:42.622 [1679582130319283/hello/2 (pid 2286602)] checking completion of nuvolaris activation aba3937d185349c1a3937d185309c1db
2023-03-23 14:35:42.882 [1679582130319283/hello/2 (pid 2286602)] [job aba3937d185349c1a3937d185309c1db] Task status (Job is done)...
2023-03-23 14:35:43.189 [1679582130319283/hello/2 (pid 2286602)] tail_logs
2023-03-23 14:35:43.189 [1679582130319283/hello/2 (pid 2286602)] [job aba3937d185349c1a3937d185309c1db] Task finished with exit code 0.
2023-03-23 14:35:44.166 [1679582130319283/hello/2 (pid 2286602)] Task finished successfully.
2023-03-23 14:35:44.379 [1679582130319283/end/3 (pid 2287082)] Task is starting.
2023-03-23 14:35:45.547 [1679582130319283/end/3 (pid 2287082)] HelloFlow is all done.
2023-03-23 14:35:46.115 [1679582130319283/end/3 (pid 2287082)] Task finished successfully.
2023-03-23 14:35:46.192 Done!
[~/nuvolaris-metaflow-demo/nuvolaris-metaflow:main]
```

You can get detaila about the action execution `aba3937d185349c1a3937d185309c1db` invoked by the Nuvolaris Metaflow plugin by running this command
```
nuv wsk activation get aba3937d185349c1a3937d185309c1db
```

We provide a Taskfile.yaml inside the `nuvolaris-metaflow` submodules conveniently defining all the above mentioned commands for you. If it is not installed on your EC2 machine you can follow the [Taskfile installation](https://taskfile.dev/installation/) guide
