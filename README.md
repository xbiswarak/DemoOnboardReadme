Modular Pipeline Library
========================

Shared jenkins library with modular structure allows you to write simple pipeline modules, test it properly and use in any kind of pipelines.

## Usage ![HowTo](https://img.shields.io/badge/How-To-1234ff?logo=react&style=plastic)

The [`AwsPipeline.groovy`](https://github.com/fortum/jenkins-mpl/blob/master/vars/AwsPipeline.groovy) file has a template structure with the default values pre-loaded in the file for reference, which can either be inherited or overridden while using the Modular Pipeline Library in your Jenkinsfile. To start using the Library, you can follow the below mentioned steps:

* [**Common Parameters**](https://github.com/fortum/jenkins-mpl/blob/master/vars/AwsPipeline.groovy#L14): The default set of parameters which can be used in multiple stages of your pipeline. 
```
    agent_label: 'central',                                             //Jenkins agent label
    environment: 'AWS',                                                 //Ex. 'AWS' or 'Azure'
    buildType: 'Maven',                                                 //Ex. 'Gradle' or 'Maven'
    appName: 'testrds',                                                 //Your application name
    businessUnit: 'devops-demo',                                        //Business Unit name
    region: 'eu-west-1',                                                //AWS region
    ecrRegistry: 'XXXXXXXXXXXX.dkr.ecr.eu-west-1.amazonaws.com',        //ECR registry URL
    dynamodbTable: 'terraform_lock',                                    //Terraform State Locking
    environmentType: 'test',                                            //Ex. 'test' or 'prod'
    awsAccountID: 'XXXXXXXXXXXX'                                        //AWS Account ID
```
* [**Build Params**](https://github.com/fortum/jenkins-mpl/blob/master/vars/AwsPipeline.groovy#L29): Parameters which can be used during the runtime of your pipeline. 
```
    dockerBuildMemory: '1024m'                                          //Docker runtime option with memory
```
* [**ECS**](https://github.com/fortum/jenkins-mpl/blob/master/vars/AwsPipeline.groovy#L35): Parameters which are used specifically for deploying your application on ECS.
```
    fargateCPU: '1024',                                                 //Ex. '1024' (1 vCPU)
    fargateMemory: '2048',                                              //Ex. '2048' (2 GiB)
    appPort: '8091',                                                    //Application port
    appCount: '1',                                                      //Replica Set
    ecsClusterName: 'dod-test-cluster',                                 //ECS Cluster name
    lbName: 'demodynamo',                                               //Load Balancer name
    appPattern: '/health'                                               //App Path; Ex. <url>/health
```
* [**EKS**](https://github.com/fortum/jenkins-mpl/blob/master/vars/AwsPipeline.groovy#L46): Parameters which are used specifically for deploying your application on EKS.
```
    containerCpuLimit: '500m',                                          //Ex. '100m' or '500m'
    containerMemoryLimit: '512M',                                       //Ex. '256M' or '512M'
    containerCpuRequest: '250m',                                        //Ex. '100m' or '250m'
    containerMemoryRequest: '256M'                                      //Ex. '256M' or '512M'
```
* [**SonarQube**](https://github.com/fortum/jenkins-mpl/blob/master/vars/AwsPipeline.groovy#L54): Parameters which are used for scanning your Application and displaying the results on the Sonar Dashboard.
```
    sonarqubeUrl: 'https://sonarqube.fortum.tech',                      //The SonarQube endpoint
    sonarProjectName: 'DynamoTest',                                     //Sonar Project name property
    sonarProjectKey: 'DYNMO',                                           //Sonar Project key property
    sonarSources: 'src/main/java',                                      //Project sources to scan
    sonarLanguage: 'java',                                              //Project Language
    sonarEncoding: 'UTF-8'                                              //Encoding format
```
* [**DynamoDB**](https://github.com/fortum/jenkins-mpl/blob/master/vars/AwsPipeline.groovy#L66): Parameters which can be used for spinning up your DynamoDB instance.
```
    enableDynamoDB: 'true',                                             //Ex. 'true' or 'false'
    billingMode: 'PROVISIONED',                                         //Ex. PROVISIONED
    readCapacity: '10',                                                 //Ex. numeric value
    writeCapacity: '10',                                                //Ex. numeric value
    hashKey: 'AppName',                                                 //The header name of the Table
    rangeKey: ''                                                        //The range key
```
* [**Gauntlt**](https://github.com/fortum/jenkins-mpl/blob/master/vars/AwsPipeline.groovy#L77): Parameters which can be used by the security testing tool - Gauntlt.
```
    gauntltImage: 'XXXXXXXXXXXX.amazonaws.com/gaunt:latest',            //The Gauntlt docker image url
    gauntltReportDir: '',                                               //Directory to output the report
    applicationEndpointToScan: '',                                      //Hostname of application or the Load Balancer address
    nmapField: "80/tcp\\s+open\\s+http",                                //Ports expected to be open at the application endpoint
    failBuildOnFailure: 'false',                                        //Ex. 'true' or 'false'
    gauntltTags: '',                                                    //Any specific tag to run; Ex. '@regression'
    gauntltReportFormat: 'html',                                        //Ex. 'html' or 'json'
    applicationProtocol: 'http'                                         //Ex. 'http' or 'https'
```
* [**RDS**](https://github.com/fortum/jenkins-mpl/blob/master/vars/AwsPipeline.groovy#L89): Parameters which can be used for spinning up your RDS instance.
```
    enableRDS: 'false',                                                 //Ex. 'true' or 'false'
    dbEngine: 'mysql',                                                  //The DB Engine
    dbEngineVersion: '5.7.26',                                          //The DB Engine version
    dbUsername: 'admin',                                                //The DB username
    dbPassword: 'admin123',                                             //The DB password
    dbPort: '3306',                                                     //The DB port
    dbFamily: 'mysql5.7'                                                //The DB family
```
* [**Redis**](https://github.com/fortum/jenkins-mpl/blob/master/vars/AwsPipeline.groovy#L102): Parameters which can be used for spinning up your Redis instance.
```
    enableRedis: 'false',                                               //Ex. 'true' or 'false'
    nodeType: 'cache.m3.medium',                                        //Node type - small | medium | large
    elastiCacheType: 'redis',                                           //'redis' or 'memcache'
    redisPort: '6379',                                                  //Redis port
    redisEngineVer: '5.0.6',                                            //Redis Engine version
    redisFamily: 'redis5.0'                                             //Redis family
```

## Running the Pipeline ![RunIT](https://img.shields.io/badge/Run-IT-3eeac?logo=pluralsight&style=plastic)

To make use of the above variables, you can add them in your `Jenkinsfile` which should be residing in your Application code repository. A sample `Jenkinsfile` will look like the following code snippet:

```
@Library('mpl') _                                                       //Referring the master branch of mpl
// AwsPipeline {}                                                       //Will be invoked with the default values mentioned in the library
AwsPipeline {                                                       
  appName = 'mpldynamotest'                                             //Overridding the App name
  appPort = '8882'                                                      //Overridding the App port
  enableDynamoDB = 'true'                                               //Toggle the boolean switch
  buildType = 'Gradle'                                                  //Expect a 'Gradle' or 'Maven' build in the runtime
  region = 'eu-west-1'                                                  //Set the region in the runtime
  modules.Build.command = 'chmod +x gradlew && ./gradlew clean build'   //Pass commands for running the application
  modules.Deploy.target = 'ECS'                                         //Set the deployment target
}
```

* Using the above example, we have added a [`Jenkinsfile`](Jenkinsfile) for your Application code repository.
* Use the same [`Jenkinsfile`](Jenkinsfile) by adding new parameters or by overriding the values as per your application needs to trigger the job in Jenkins.
* Observe the Pipeline running with multiple Stages which gets invoked as part of the Modular Pipeline Library.

<p align="center"><img src="https://github.com/fortum/jenkins-mpl/blob/mpl-rds-redis/resources/JenkinsMPL.gif" width="600"/></p>
