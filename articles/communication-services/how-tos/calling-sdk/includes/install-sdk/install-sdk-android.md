---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 41f8fc2adefd29bb7cfda31e3dbaa83de599b154
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838479"
---
## <a name="install-the-sdk"></a>Installer le SDK

Recherchez votre niveau de projet build.gradle et veillez à ajouter `mavenCentral()` à la liste des référentiels sous `buildscript` et `allprojects`.
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
Ensuite, dans votre niveau de module build.gradle, ajoutez les lignes suivantes à la section dépendances.

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0'
    ...
}
```

### <a name="initialize-the-required-objects"></a>Initialiser les objets nécessaires

Pour créer une instance `CallAgent`, vous devez appeler la méthode `createCallAgent` sur une instance `CallClient`. Cette opération renvoie un objet d’instance `CallAgent` de manière asynchrone.
La méthode `createCallAgent` prend un `CommunicationUserCredential` comme argument, qui encapsule un [jeton d’accès](../../../../quickstarts/access-tokens.md).
Pour accéder au `DeviceManager`, une instance callAgent doit être créée en premier lieu, puis vous pouvez utiliser la méthode `CallClient.getDeviceManager` pour obtenir le DeviceManager.

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationTokenCredential tokenCredential = new CommunicationTokenCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgent callAgent = callClient.createCallAgent(appContext, tokenCredential).get();
DeviceManager deviceManager = callClient.getDeviceManager(appContext).get();
```
Pour définir un nom d’affichage pour l’appelant, utilisez cette autre méthode :

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationTokenCredential tokenCredential = new CommunicationTokenCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgentOptions callAgentOptions = new CallAgentOptions();
callAgentOptions.setDisplayName("Alice Bob");
DeviceManager deviceManager = callClient.getDeviceManager(appContext).get();
CallAgent callAgent = callClient.createCallAgent(appContext, tokenCredential, callAgentOptions).get();
```