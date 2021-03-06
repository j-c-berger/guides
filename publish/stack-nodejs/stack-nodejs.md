---
permalink: /guides/stack-nodejs/
layout: guide-markdown
title: Developing cloud native microservice applications with the Node.js application stack
duration: 40 minutes
releasedate: 2020-01-27
description: Learn how to create, run, update, deploy, and deliver a simple cloud native application using the Node.js application stack.
tags: ['Stack', 'Node.js']
guide-category: stacks
---

<!-- Note:
> This repository contains the guide documentation source. To view
> the guide in published form, view it on the [website](https://kabanero.io/guides/{projectid}.html).
-->

<!--
//
//	Copyright 2019, 2020 IBM Corporation and others.
//
//	Licensed under the Apache License, Version 2.0 (the "License");
//	you may not use this file except in compliance with the License.
//	You may obtain a copy of the License at
//
//	http://www.apache.org/licenses/LICENSE-2.0
//
//	Unless required by applicable law or agreed to in writing, software
//	distributed under the License is distributed on an "AS IS" BASIS,
//	WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
//	See the License for the specific language governing permissions and
//	limitations under the License.
-->

<!--
// =================================================================================================
// What you'll learn
// =================================================================================================
-->

## What you will learn

Application stacks enable the development and optimization of microservice applications. With application stacks, developers don’t need to manage the full software development stack or be experts on underlying container technologies or Kubernetes. Application stacks are customized for specific enterprises to incorporate their company standards and technology choices. Developers access these stacks by configuring their development environment to point to a stack configuration.

In this guide, you’ll learn how to configure your development environment, then create and run a simple cloud native microservice based on the Node.js application stack. Finally, you’ll update the microservice that you created and deploy it to Kubernetes or serverless. Deployment to serverless is optional depending on whether you want to Scale to Zero.

Applications in this guide are built and run with Node.js, and deployed to Kubernetes through a modern DevOps toolchain that is triggered in Git.

<!--
// =================================================================================================
// Prerequisites
// =================================================================================================
-->

## Prerequisites

- [Docker](https://docs.docker.com/get-started/) must be installed. If you are using Docker Desktop, you can enable Kubernetes from the menu by selecting *Preferences* -> *Kubernetes* -> *Enable Kubernetes*. Other options include [Minishift](https://www.okd.io/minishift/) or [Minikube](https://kubernetes.io/docs/setup/learning-environment/minikube/).
- [Appsody](https://appsody.dev/docs/getting-started/installation) must be installed.

<!--
// =================================================================================================
// Getting started
// =================================================================================================
-->

## Getting started

You are going to create an application that is based on a public stack from the Kabanero project. After configuring your local development environment, you are going to initialize a new project that is based on the Node.js stack.

<!--
// =================================================================================================
// Configuring your development environment
// =================================================================================================
-->

### Configuring your development environment

To check the repositories that you can already access, run the following command:

```shell
appsody repo list
```

You see output similar to the following example:

```shell
NAME        URL
*incubator https://github.com/appsody/stacks/releases/latest/download/incubator-index.yaml
```

Next, run the following command to add the URL for the public Kabanero stack hub:

```shell
appsody repo add kabanero https://github.com/kabanero-io/kabanero-stack-hub/releases/download/0.7.0/kabanero-stack-hub-index.yaml
```

Check the repositories again by running `appsody repo list` to see that your repository was added. The output is similar to the following example:

```shell
NAME        URL
*incubator https://github.com/appsody/stacks/releases/latest/download/incubator-index.yaml
kabanero https://github.com/kabanero-io/kabanero-stack-hub/releases/download/0.7.0/kabanero-stack-hub-index.yaml
```

In this example, the asterisk (\*) shows that `incubator` is the default repository. Run the following command to set `kabanero` as the default repository:

```shell
appsody repo set-default kabanero
```

Check the available repositories again by running `appsody repo list` to see that the default is updated:

```shell
NAME        URL
incubator   https://github.com/appsody/stacks/releases/latest/download/incubator-index.yaml
*kabanero https://github.com/kabanero-io/kabanero-stack-hub/releases/download/0.7.0/kabanero-stack-hub-index.yaml
```

**Recommendation**: To avoid initializing projects that are based on the public application stacks, it's best
to remove `incubator` from the list. Run the following command to remove the `incubator` repository:

```shell
appsody repo remove incubator
```

Check the available repositories again by running `appsody repo list` to see that `incubator` is removed:

```shell
NAME        URL
*kabanero https://github.com/kabanero-io/kabanero-stack-hub/releases/download/0.7.0/kabanero-stack-hub-index.yaml
```

Your development environment is now configured to use your customized application stacks. Next, you need to initialize your project.

**Note:** If your organization has created a stack hub that contains customized application stacks, you must configure your development environment to access them. After you have completed this guide, you can step through this section again to update your configuration to point to the URL for your organization's stack hub. This configuration process is also described in [Developing microservice applications with the CLI](../use-appsody-cli).

<!--
// =================================================================================================
// Initializing your project
// =================================================================================================
-->

### Initializing your project

First, create a directory that will contain the project:

```shell
mkdir -p ~/projects/simple-nodejs
cd ~/projects/simple-nodejs
```

Run the following command to initialize your Node.js project:

```shell
appsody init nodejs
```

The output from the command is similar to the following example:

```shell
Checking stack requirements...
Docker requirements met
Appsody requirements met
Running appsody init...
Downloading nodejs template project from https://github.com/kabanero-io/collections/releases/download/0.6.3/nodejs.v0.3.3.templates.simple.tar.gz
Download complete. Extracting files from /Users/myuser/appsody/simple-nodejs/nodejs.tar.gz
Setting up the development environment
Your Appsody project name has been set to simple-nodejs
Pulling docker image docker.io/kabanero/nodejs:0.3
Running command: docker pull docker.io/kabanero/nodejs:0.3
0.3: Pulling from kabanero/nodejs
..
..
Status: Downloaded newer image for kabanero/nodejs:0.3
docker.io/kabanero/nodejs:0.3
[Warning] The stack image does not contain APPSODY_PROJECT_DIR. Using /project
Running command: docker run --rm --entrypoint /bin/bash docker.io/kabanero/nodejs:0.3 -c "find /project -type f -name .appsody-init.sh"
Successfully added your project to /Users/myuser/.appsody/project.yaml
Your Appsody project ID has been set to 20200402132709.84166800
Successfully initialized Appsody project with the nodejs stack and the default template.

```

**Note:** Some lines (..) are removed for clarity.

Your new project is created, built, and started inside a container.

<!--
// =================================================================================================
// Understanding the project layout
// =================================================================================================
-->

## Understanding the project layout

For context, the following image displays the structure of the project that you’re working on:

![Project structure](/img/guide/collection-nodejs-project-layout.png)

This project contains the following artifacts:

- `app.js`:: A sample javascript app
- `package-lock.json`:: The application's npm dependency tree
- `package.json`:: The application's package manifest

<!--
// =================================================================================================
// Running the development environment
// =================================================================================================
-->

## Running the development environment

Run the following command to start the development environment:

```shell
appsody run
```

The CLI launches a local Docker image that contains the Node.js runtime environment that hosts the microservice.
After some time, you see a message similar to the following example:

```shell
Running development environment...
Pulling docker image docker.io/kabanero/nodejs:0.3
Running command: docker pull docker.io/kabanero/nodejs:0.3
0.3: Pulling from kabanero/nodejs
Digest: sha256:c6d287713984c008bfdc7d491aa3b1123bc7ad0126be239e97a50f2e8c201e2a
Status: Image is up to date for kabanero/nodejs:0.3
docker.io/kabanero/nodejs:0.3
Running command: docker run --rm -p 3000:3000 -p 8080:8080 -p 9229:9229 --name simple-nodejs -v /Users/myuser/appsody/simple-nodejs/:/project/user-app -v appsody-simple-nodejs-20200402133141.39489100:/project/user-app/node_modules -v appsody-controller-0.3.4:/.appsody -t --entrypoint /.appsody/appsody-controller docker.io/kabanero/nodejs:0.3 "--mode=run"
[Container] Running APPSODY_PREP command: npm install && npm audit fix
up to date in 0.733s
..
..
[Container] Running command:  npm start --node-options --require=appmetrics-dash/attach
[Container]
[Container] > nodejs-simple@0.1.0 start /project/user-app
[Container] > node app.js
[Container]
[Container] [Thu Apr  2 12:31:44 2020] com.ibm.diagnostics.healthcenter.loader INFO: Node Application Metrics 5.1.1.202003102147 (Agent Core 4.0.5)
[Container] Hello from Node.js 12!
```

This message indicates that the project is started. The container exposes port 3000, which allows you to bring your own web application and use it with this stack.

You are now ready to begin developing your application.

<!--
// =================================================================================================
// Creating and updating the application
// =================================================================================================
-->

## Creating and updating the application

You are now going to create a new simple web server that listens on `http://localhost:3000/`.

Edit the `app.js` file in your project folder and update the contents to match the following code:

```shell
const http = require('http');

const hostname = '0.0.0.0';
const port = 3000;

const hander = (req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('New web server available!\n');
}

const server = http.createServer((handler)

server.listen(port, hostname, () => {
  console.log(`Server running at http://${hostname}:${port}/`);
});
```

Save the changes.

The development environment watches for file changes and automatically updates your application. Point your browser to `http://localhost:3000/` to see your new server, which displays **New web server available!**.

<!--
// =================================================================================================
// Testing the application
// =================================================================================================
-->

## Testing the application

If you are building an application that is composed of microservices, you need to test within the context of the overall system. First, test your application and perform unit testing in isolation. To test the application as part of the system, deploy the system and then the new application.

You can choose how you want to deploy the system and application. If you have adequate CPU and memory to run MiniShift, the application, and the associated services, then you can deploy the application on a local Kubernetes that is running on your computer. Alternatively, you can enable Docker Desktop for Kubernetes, which is described in the Prerequisites section of the guide.

You can also deploy the system, application, and the associated services in a private namespace on a development cluster. From this private namespace, you can commit the microservices in Git repositories and deploy them through a DevOps pipeline, not directly to Kubernetes.

<!--
// =================================================================================================
// Testing locally on Kubernetes
// =================================================================================================
-->

### Testing locally on Kubernetes

After you finish writing your application code, the CLI makes it easy to deploy directly to a Kubernetes cluster for further local testing. The ability to deploy directly to a Kubernetes cluster is valuable when you want to test multiple microservices together or test with services that the application requires.

Ensure that your `kubectl` command is configured with cluster details and run the following command to deploy the application:

```shell
appsody deploy
```

This command builds a new Docker image that is optimized for production deployment and deploys the image to your local Kubernetes cluster.
After some time you see a message similar to the following example:

```shell
Deployed project running at http://localhost:32569
```

Run the following command to check the status of the application pods:

```shell
kubectl get pods
```

In the following example output, you can see that a `simple-nodejs` pod is running:

```shell
NAME                                    READY   STATUS    RESTARTS   AGE
appsody-operator-6bbddbd455-nfhnm        1/1     Running   0          26d
simple-nodejs-775b655768-lqn6q           1/1     Running   0          3m10s
```

After the `simple-nodejs` pod starts, go to the URL that was returned when you ran the `appsody deploy` command, and you see the splash screen. To see the response from your application, point your browser to the `<URL_STRING>/example` URL, where `<URL_STRING>` is the URL that was returned. For example, `http://localhost:32569` was returned in the previous example. Go to the `http://localhost:32569/` URL to see the deployed application response.

Use the following command to stop the deployed application:

```shell
appsody deploy delete
```

After you run this command and the deployment is deleted, you see the following message:

```shell
Deployment deleted
```

<!--
// =================================================================================================
// Testing with serverless
// =================================================================================================
-->

### Testing with serverless

You can choose to test an application that is deployed with serverless to take advantage of Scale to Zero. Not all applications can be written to effectively take advantage of Scale to Zero. The Kabanero operator-based installation configures serverless on the Kubernetes cluster. Because of the resources that are required to run serverless and its dependencies, testing locally can be difficult. Publish to Kubernetes by using pipelines that are described later in the guide. Your operations team can configure the pipelines so that serverless is enabled for deployment.

<!--
// =================================================================================================
// Publishing to Kubernetes by using pipelines
// =================================================================================================
-->

## Publishing to Kubernetes by using pipelines

After you develop and test your application in your local environment, it’s time to publish it to your enterprise’s pipeline. From your enterprise’s pipeline, you can deploy the application to the appropriate Kubernetes cluster for staging or production. Complete this process in Git.

When Kabanero is installed, deploying applications to a Kubernetes cluster always occurs through the DevOps pipeline that is triggered in Git. Using DevOps pipelines to deploy applications ensures that developers can focus on application code, not on containers or Kubernetes infrastructure. From an enterprise perspective, this deployment process ensures that both the container image build and the deployment to Kubernetes or Knative happen in a secure and consistent way that meets company standards.

To deliver your application to the pipeline, push the project to the pre-configured Git repository that has a configured webhook. This configured webhook triggers the enterprise build and deploy pipeline. For more information, see [Working with pipelines](../working-with-pipelines).
