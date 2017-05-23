---
layout: doc
title: Usage Control
permalink: /docs/usage_control/

---

In this part you will learn how to control data flows within the Connector using LUCON policies.

## Introduction to LUCON

LUCON (_Logic based Usage CONtrol_) is a policy language for controlling data flows between endpoints. The Connector uses [Apache Camel](http://camel.apache.org/) to route messages between services (such as MQTT, REST, or OPC-UA endpoints). Message flows are controlled by LUCON policies, a simple policy language for data labelling and taint tracking.

LUCON is a simple policy language and comes with an Eclipse plugin for syntax highlighting, code completion and compilation into a format that is understood by the policy decision point within the Connector. Thus the typical workflow is as follows:

1. Write LUCON policies in Eclipse ([Install Eclipse Plugin](#install-eclipse-plugin)) and find compiled policies in folder `compiled-policies`
1. Load compiled policies into Connector
1. Activate policies

### Data Flow Rules

Consider the following example rules.

##### Private data must be anonymized before leaving the Connector

```
flow_rule {
  id anonymized                       // Rule id
  if service publicEndpoint           // Target identifier
  receives private                    // Received message labels
  then drop                           // Drop message
}
```

In this example, a rule `anonymized` declares that if any service matching the `publicEndpoint` description receives a message that contains a label `private`, the message must be dropped and not sent to the service.

##### Data must be deleted after 30 days

```
flow_rule {
  id deleteAfterOneMonth              // Rule id
  if service hadoopCluster            // Target identifier
  receives *                          // Received message labels
  require delete_after_days(X), X<30  // Obligation 
    otherwise drop                    // Alternative
}
```
In this example, a rule `deleteAfterOneMonth` declares that all messages (indicated by `receives *`) sent into a service matching the `hadoopCluster` description must be deleted after 30 days. If the service does not support deletion, the message must be dropped.

#### Service Descriptions

Flow rules refer to _service_ descriptions. A service description declares a set of services to which rule applies. It consists of the following elements:

* `id` (required) A unique identifier to which any `flow_rule` may refer to.
* `endpoint` (required) A description of the endpoints included in this service description
* `capabilities` (optional) A description of actions, the service can execute. If a rule requires actions which are not supported, the `otherwise` clause is applied
* `properties` (optional) Further descriptive properties of the service
* `removes_label` (optional) Comma-separated list of labels which will be removed from outgoing messages of the service
* `creates_label`(optional) Comma-separated list of label which will be added to outgoing messages of the service

The following example defines a set of database services that are able to run a `delete_after_days` action and add a label `persisted` to messages.

```groovy
service {
  id hadoopCluster
  
  // Defines the Camel endpoints for which this service description applies, using a regular expression.
  // Everything starting with hdfs:// or sql:// applies.
  endpoint regex("^[hdfs://.+]|[sql://.+]")

  
  // Capabilities can be required by a flow_rule. If not required, nothing will happen
  capabilities
    deletion: delete_after_days(X)
  
  // Properties describe the service's behavior. This one blinds fields "surname" and "name"
  creates_label persisted
}
```

## Install Eclipse Plugin

Download any Eclipse installation from the [Eclipse web site](https://www.eclipse.org/downloads/). We recommend using the [Eclipse Installer](https://www.eclipse.org/downloads/eclipse-packages/).

In Eclipse, click on _Help_ -> _Install New Software_

<img src="../../assets/img/eclipse_install_new_software.png" width="500"/>

Click on _Add..._ and choose _Archive..._.

<img src="../../assets/img/eclipse-install-from-archive.png" width="500"/>

Choose the LUCON plugin repository archive `de.fhg.aisec.lucon.repository-[VERSION].zip` and select the LUCON feature.

<img src="../../assets/img/eclipse-install-lucon-feature.png" width="500"/>