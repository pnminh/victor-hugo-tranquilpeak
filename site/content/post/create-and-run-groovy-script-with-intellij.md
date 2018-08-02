---
title: Integrate groovy script in the software development life cycle
thumbnailImage: /images/uploads/groovy-logo.png
thumbnailImagePosition: left
coverImage: /images/uploads/groovy-logo-dark.png
metaAlignment: center
coverMeta: in
date: '2018-08-01T14:28:42-05:00'
description: ' A note on how to create and run Groovy script with Intellij'
tags:
  - blog
  - post
  - tutorial
categories:
  - blog
---
We use Jenkins to run out CI/CD pipeline. The process requires some custom extensions to the existing capability of Jenkins. Below is a Groovy script to get secrets from the AWS SecretManager service.

{{< codeblock "ManagerSecretRetriever.groovy" "java">}}
@Grab('com.amazonaws:aws-java-sdk-secretsmanager:1.11.358')
/**
*
* @param region
* @param secretName
* @return secret data as json string
*/
  import com.amazonaws.client.builder.AwsClientBuilder

import com.amazonaws.services.secretsmanager.AWSSecretsManager

import com.amazonaws.services.secretsmanager.AWSSecretsManagerClientBuilder

import com.amazonaws.services.secretsmanager.model.GetSecretValueRequest

import com.amazonaws.services.secretsmanager.model.GetSecretValueResult

import java.nio.ByteBuffer

import java.nio.charset.Charset

String getSecret(region,secretName) {

String endpoint = "secretsmanager.${region}.amazonaws.com"

AwsClientBuilder.EndpointConfiguration config = new AwsClientBuilder.EndpointConfiguration(endpoint, region);

AWSSecretsManagerClientBuilder clientBuilder = AWSSecretsManagerClientBuilder.standard();

clientBuilder.setEndpointConfiguration(config);

AWSSecretsManager client = clientBuilder.build();



ByteBuffer binarySecretData;

GetSecretValueRequest getSecretValueRequest = new GetSecretValueRequest();

getSecretValueRequest.setSecretId(secretName);

GetSecretValueResult getSecretValueResponse = client.getSecretValue(getSecretValueRequest);

if(getSecretValueResponse == null) {

return null

}

// Decrypted secret using the associated KMS CMK
// Depending on whether the secret was a string or binary, one of these fields will be populated

if(getSecretValueResponse.getSecretString() != null) {

return getSecretValueResponse.getSecretString()

}

else {

binarySecretData = getSecretValueResponse.getSecretBinary();

return new String( binarySecretData.array(), Charset.forName("UTF-8") )

}


}
/**
* This requires first argument as region and 2nd as secret name
*/
  String s = getSecret(args\[0],args\[1])

print s
{{< /codeblock >}}
The good thing about Groovy is to download dependencies we just need to add the @Grab annotation and Groovy runtime will look for and download them for us automatically from the preset repositories such as m2. Later in this post we will see how to setup the download sites.<br/> 
As Jenkins supports ephemeral docker containers, to run a language-specific script we can grab the image from Docker Hub or any docker repositories, or build the image on the fly with the provided Dockerfile. Here is an example of using a Dockerfile to create a custom inline image and then use it to start a container inside Jenkins pipeline and run the Groovy script
{{< codeblock "Dockerfile" "bash">}}
FROM groovy:2.4.15-jdk8-alpine
COPY grapeConfig.xml /home/groovy/.groovy/grapeConfig.xml
{{< /codeblock >}}
{{< codeblock "grapeConfig.xml" "html">}}

<?xml version="1.0"?>

<ivysettings>
  <settings defaultResolver="downloadGrapes"/>
  <resolvers>
    <chain name="downloadGrapes" returnFirst="true">
      <filesystem name="cachedGrapes">
        <ivy pattern="${user.home}/.groovy/grapes/\\[organisation]/\\[module]/ivy-\\[revision].xml"/>
        <artifact pattern="${user.home}/.groovy/grapes/\\[organisation]/\\[module]/\\[type]s/\\[artifact]-\\[revision](-\\[classifier]).\\[ext]"/>
      </filesystem>
      <ibiblio name="localm2" root="file:${user.home}/.m2/repository/" checkmodified="true" changingPattern=".*" changingMatcher="regexp" m2compatible="true"/>
      <!-- todo add 'endorsed groovy extensions' resolver here -->
      <ibiblio name="company-repo" root="http://artifactory.services.company.com/artifactory/jcenter/" m2compatible="true"/>
      <ibiblio name="jcenter" root="https://jcenter.bintray.com/" m2compatible="true"/>
      <ibiblio name="ibiblio" m2compatible="true"/>
    </chain>
  </resolvers>
</ivysettings>
{{< /codeblock >}}
{{< codeblock "JenkinsSharedLibUtil.groovy" "java">}}
...
//dockerPath: path that contain Dockerfile
dockerPath.inside{
        print "run groovy script"
        return sh(
                script: "groovy ${groovyScript} ${inputArgs.join(" ")}",
                returnStdout: true).trim()
...
{{< /codeblock >}}
The Dockerfile tells docker to get the groovy image from Docker Hub, and also copies over the grapeConfig.xml to the image. The grapeConfig.xml configures the sites where Groovy can go and "grab" dependencies. The runtime can first look for the local caches (.groovy/grapes and .m2/repository) to find the dependencies, and if they are not found there, the company  and then jcenter repos will be checked next. The Jenkins pipeline then can start the groovy container and run the script and get back the String response.

**Run Groovy script with IntelliJ**
Running script with Jenkins pipeline takes quite a few steps. What if we want to test the script before checking into source control, such as running it right inside an IDE. At Dice, we use IntelliJ to build Java and Python projects. Having it run the Groovy script on the fly should be a very convenient way as part of the development process. In this section I will show you how to make that happen.<br/>
First we need to create a Groovy module. To make it simple, we will use the folder that contains all the Groovy scripts as module name

![choose Groovy as new module](/images/uploads/screen-shot-2018-08-01-at-4.30.19-pm.png)

![select folder as Groovy module](/images/uploads/screen-shot-2018-08-01-at-4.32.06-pm.png)

IntelliJ automatically creates a src folder as a convention to keep source codes, but since we want to run the scripts right in the root directory (groovy folder), we can set this as the source root instead and just remove the src folder. Remember to Unmark the folder before deleting it.

![mark root dir as source](/images/uploads/screen-shot-2018-08-01-at-4.36.32-pm.png)

We also need to add Ivy jar as a dependency to the module as IntelliJ will use it to manage the dependencies from the @Grab annotation in each script. We can add gradle or pom file here to manage the module dependencies for us but to make it simple we will just add Ivy manually. Go to Project Structure, then select the groovy module. On the Dependencies tab click on the + button and select Library

![add dependencies to module](/images/uploads/screen-shot-2018-08-01-at-4.43.35-pm.png)

Look for the ivy dependency. In my case since I already have a project with lots of modules, the dependency is already there. You may need to search for ivy using the New Library button if you don't see it in the list.

![add ivy jar to dependency list](/images/uploads/screen-shot-2018-08-01-at-4.44.52-pm.png)

Now we can right click on the module and choose Build. This should download all dependencies listed with @Grab. However in some cases dependencies are not resolved, so one trick is to move the cursor at the @Grab annotation, use Alt+ Enter ( or Option + Return in Mac) and select Grab the artifacts. 

![grab the artifacts](/images/uploads/screen-shot-2018-08-01-at-6.53.37-pm.png)

That is it for today.
