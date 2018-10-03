---
title: Run and debug Docker container
thumbnailImage: /images/uploads/screen-shot-2018-10-03-at-9.04.02-am.png
thumbnailImagePosition: left
coverImage: ''
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

{{< codeblock "main_app.xml" "html">}}

<configuration name="main_app" type="docker-deploy" factoryName="docker-image" server-name="Docker">
      <deployment type="docker-image">
        <settings>
          <option name="JSONFilePath" value="" />
          <option name="command" value="" />
          <option name="commandLineOptions" value="-v $PROJECT_DIR$/../apps/static_assets/dist:/usr/local/tomcat/webapps/static_assets/dist -v $PROJECT_DIR$/../apps/static_assets/lib:/usr/local/tomcat/webapps/static_assets/lib -v $PROJECT_DIR$/../apps/app_conf:/usr/local/main_app/conf -v $PROJECT_DIR$/../apps/main_app/build/libs:/usr/local/tomcat/webapps" />
          <option name="containerName" value="main_app" />
          <option name="entrypoint" value="" />
          <option name="envVars">
            <list>
              <DockerEnvVarImpl>
                <option name="name" value="JAVA_TOOL_OPTIONS" />
                <option name="value" value="-agentlib:jdwp=transport=dt_socket,address=8010,server=y,suspend=n" />
              </DockerEnvVarImpl>
            </list>
          </option>
          <option name="imageTag" value="tomcat:7" />
          <option name="portBindings">
            <list>
              <DockerPortBindingImpl>
                <option name="containerPort" value="8080" />
                <option name="hostPort" value="8080" />
              </DockerPortBindingImpl>
              <DockerPortBindingImpl>
                <option name="containerPort" value="8010" />
                <option name="hostPort" value="8010" />
              </DockerPortBindingImpl>
            </list>
          </option>
          <option name="startBrowserSettings">
            <browser url="http://127.0.0.1" />
          </option>
        </settings>
      </deployment>
      <method v="2">
        <option name="Gradle.BeforeRunTask" enabled="true" tasks="build" externalProjectPath="$PROJECT_DIR$/../apps/main_app" vmOptions="" scriptParameters="" />
      </method>
    </configuration>

{{< /codeblock >}}

Here I name both the configuration and the container "main_app" which is the same with the spring app itself just to distinguish them easily. Some other important information is the image name which is "tomcat:7" in this case, the command line options that include some custom docker options such as volume mappings (in this scenario I mapped several static apps to the tomcat webapps folder on the container side, as well as the main_app and a config folder), some port mappings from the container to the host ( e.g. 8080 for the http communication and 8010 as remote listener port for debugging purpose). To do a remote debug later, I also needed to add an environment variable JAVA_TOOL_OPTIONS so that we can bind the debugger to the port 8010 on Tomcat server.

As you can see, we can also hook some commands before launching the container in Intellij, and I think this is a killer feature. In the example above, I added a gradle build command to build the main app before have it served by Docker tomcat. We can even have our static content to be built with npm or angular cli as well.

And if you want to look at the run configuration visually, here is a real config I put in for one of the apps:

![null](/images/uploads/screen-shot-2018-10-03-at-10.00.31-am.png)

Next up, start the container and have it ready for debugging. That would, unfortunately, require us to create a debug configuration. There may be a way to make a single run config for docker run and debug, but I haven't dug into that yet. I think one option may be a after launch hook similar to the before launch one I mentioned above that can allow us to add the debug command.

So here is the remote debug config:

![null](/images/uploads/screen-shot-2018-10-03-at-10.07.28-am.png)

Or as a xml format:
{{< codeblock "debug_main_app.xml" "html">}}
<configuration name="debug_main_app" type="Remote" factoryName="Remote">
      <module name="main_app" />
      <option name="USE_SOCKET_TRANSPORT" value="true" />
      <option name="SERVER_MODE" value="false" />
      <option name="SHMEM_ADDRESS" />
      <option name="HOST" value="localhost" />
      <option name="PORT" value="8010" />
      <RunnerSettings RunnerId="Debug">
        <option name="DEBUG_PORT" value="8010" />
        <option name="LOCAL" value="false" />
      </RunnerSettings>
      <method v="2" />
    </configuration>
{{< /codeblock >}}

Pay attention to the port that we use to bind the debugger to, which in this case is 8010. This should match the one set in the docker container config.

That's it. Now you can start the debugger and fix your ugly bugs.

Happy coding.
