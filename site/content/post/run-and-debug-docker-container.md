---
title: Run and debug Docker container
thumbnailImage: /images/uploads/screen-shot-2018-10-03-at-9.04.02-am.png
thumbnailImagePosition: left
coverImage: /images/uploads/screen-shot-2018-10-03-at-9.04.02-am.png
metaAlignment: center
coverMeta: out
date: '2018-10-03T08:52:42-05:00'
tags:
  - blog
  - post
categories:
  - blog
---
Recently I talked to a colleague to see how he setup his IntelliJ workspace to run and debug a tomcat application. I had followed a traditional way when coming to Java application development by spinning up a local tomcat server with some pre-defined run commands to build and deploy artifacts to the server,  all with IntelliJ build configurations. However the idea of using IntelliJ to debug a Docker application is very convenient. Let me show you about my steps to fulfill that job here.

First we need to create a run configuration to run docker container. Here is the one I used to run my Spring Application with Tomcat:

{{< codeblock "grapeConfig.xml" "html">}}

<configuration name="customer" type="docker-deploy" factoryName="docker-image" server-name="Docker">

\    <deployment type="docker-image">

\    <settings>

\    <option name="JSONFilePath" value="" />

\    <option name="command" value="" />

\    <option name="commandLineOptions" value="-v $PROJECT_DIR$/../batch/customer_assets/dist:/usr/local/tomcat/webapps/customer_assets/dist -v $PROJECT_DIR$/../batch/customer_assets/lib:/usr/local/tomcat/webapps/customer_assets/lib -v $PROJECT_DIR$/../batch/customer-apps/dist:/usr/local/tomcat/webapps/customer-apps/dist -v $PROJECT_DIR$/../batch/customer-apps/lib:/usr/local/tomcat/webapps/customer-apps/lib -v $PROJECT_DIR$/../batch/customer_conf:/usr/local/customer/conf -v $PROJECT_DIR$/../batch/customer/build/libs:/usr/local/tomcat/webapps" />

\    <option name="containerName" value="customer" />

\    <option name="entrypoint" value="" />

\    <option name="envVars">

\    <list>

\    <DockerEnvVarImpl>

\    <option name="name" value="JAVA_TOOL_OPTIONS" />

\    <option name="value" value="-agentlib:jdwp=transport=dt_socket,address=8010,server=y,suspend=n" />

\    </DockerEnvVarImpl>

\    </list>

\    </option>

\    <option name="imageTag" value="tomcat:7" />

\    <option name="portBindings">

\    <list>

\    <DockerPortBindingImpl>

\    <option name="containerPort" value="8080" />

\    <option name="hostPort" value="8080" />

\    </DockerPortBindingImpl>

\    <DockerPortBindingImpl>

\    <option name="containerPort" value="8010" />

\    <option name="hostPort" value="8010" />

\    </DockerPortBindingImpl>

\    </list>

\    </option>

\    <option name="startBrowserSettings">

\    <browser url="http://127.0.0.1" />

\    </option>

\    </settings>

\    </deployment>

\    <method v="2">

\    <option name="Gradle.BeforeRunTask" enabled="true" tasks="build" externalProjectPath="$PROJECT_DIR$/../batch/customer" vmOptions="" scriptParameters="" />

\    </method>

\    </configuration>

{{< /codeblock >}}
