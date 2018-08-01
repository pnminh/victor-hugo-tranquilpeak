---
title: Create and run groovy script with Intellij
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

{{< codeblock "ManagerSecretRetriever.groovy.java" "java">}}
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
The good thing about Groovy is to download dependencies we just need to add the @Grab annotation and Groovy runtime will look for and download them for us automatically from the preset repositories such as m2. Later in this post we will see how to setup the download sites. 
As Jenkins supports ephemeral docker containers, to run a language-specific script we can grab the image from Docker Hub or any docker repositories, or build the image on the fly with the provided Dockerfile. Here is an example of using a Dockerfile to create an inline image and then use it to start a container inside Jenkins pipeline and run the Groovy script
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
        <ivy pattern="${user.home}/.groovy/grapes/[organisation]/[module]/ivy-[revision].xml"/>
        <artifact pattern="${user.home}/.groovy/grapes/[organisation]/[module]/[type]s/[artifact]-[revision](-[classifier]).[ext]"/>
      </filesystem>
      <ibiblio name="localm2" root="file:${user.home}/.m2/repository/" checkmodified="true" changingPattern=".*" changingMatcher="regexp" m2compatible="true"/>
      <!-- todo add 'endorsed groovy extensions' resolver here -->
      <ibiblio name="dice-repo" root="http://artifactory.services.dicedev.dhiaws.com/artifactory/jcenter/" m2compatible="true"/>
      <ibiblio name="jcenter" root="https://jcenter.bintray.com/" m2compatible="true"/>
      <ibiblio name="ibiblio" m2compatible="true"/>
    </chain>
  </resolvers>
</ivysettings>
{{< /codeblock >}}
