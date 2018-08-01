---
title: Create and run groovy script with Intellij
thumbnailImage: /images/uploads/groovy-logo.png
thumbnailImagePosition: left
coverImage: /images/uploads/groovy-logo.png
metaAlignment: center
coverMeta: out
date: '2018-08-01T14:28:42-05:00'
description: ' A note on how to create and run Groovy script with Intellij'
tags:
  - blog
  - post
  - tutorial
categories:
  - blog
---
We use Jenkins to run out CI/CD pipeline. The process requires some custom extensions to the existing capability of Jenkins, such as a way to get

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
