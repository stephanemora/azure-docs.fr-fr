---
title: Se connecter à Azure Media Services v3 API - Java
description: Découvrez comment vous connecter à Media Services v3 API avec Java.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2019
ms.author: juliako
ms.openlocfilehash: 27a4a30dd0eb449726a99d02f2409632aa327567
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59683424"
---
# <a name="connect-to-media-services-v3-api---java"></a>Se connecter à l’API de Media Services v3 - Java

Cet article vous montre comment se connecter au SDK Java Azure Media Services v3 à l’aide de la connexion du principal du service dans la méthode.

Dans cet article, Visual Studio Code est utilisé pour développer l’exemple d’application.

## <a name="prerequisites"></a>Conditions préalables

- Suivez [Java d’écriture avec Visual Studio Code](https://code.visualstudio.com/docs/java/java-tutorial) à installer :

   - JDK
   - Apache Maven
   - Pack d’Extension Java
- Veillez à définir `JAVA_HOME` et `PATH` variables d’environnement.
- [Créer un compte Media Services](create-account-cli-how-to.md). Veillez à mémoriser le nom de groupe de ressources et le nom du compte Media Services.
- Suivez les étapes de la [accéder aux API](access-api-cli-how-to.md) rubrique. Enregistrez l’ID d’abonnement, ID d’application (ID client), la clé d’authentification (secret) et l’ID de client dont vous avez besoin dans une étape ultérieure.

Consultez également :

- [Java dans Visual Studio Code](https://code.visualstudio.com/docs/languages/java)
- [Gestion de projet Java dans VS Code](https://code.visualstudio.com/docs/java/java-project)

## <a name="create-a-maven-project"></a>Création d’un projet Maven

Ouvrez un outil de ligne de commande et `cd` dans un répertoire dans lequel vous souhaitez créer le projet.
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

Lorsque vous exécutez la commande, le `pom.xml`, `App.java`, et d’autres fichiers sont créés. 

## <a name="add-dependencies"></a>Ajout de dépendances

1. Dans Visual Studio Code, ouvrez le dossier où votre projet est
1. Recherchez et ouvrez le `pom.xml`
1. Ajoutez les dépendances nécessaires

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.mediaservices.v2018_07_01</groupId>
      <artifactId>azure-mgmt-media</artifactId>
      <version>1.0.0-beta</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.rest</groupId>
      <artifactId>client-runtime</artifactId>
      <version>1.6.5</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-client-authentication</artifactId>
      <version>1.6.5</version>
    </dependency>
    ```

## <a name="connect-to-the-java-client"></a>Se connecter au client Java

1. Ouvrez le `App.java` de fichiers sous `src\main\java\com\azure\ams` et assurez-vous que votre package est inclus dans la partie supérieure :

    ```java
    package com.azure.ams;
    ```
1. Sous l’instruction package, ajoutez ces instructions import :
   
   ```java
   import com.microsoft.azure.AzureEnvironment;
   import com.microsoft.azure.credentials.ApplicationTokenCredentials;
   import com.microsoft.azure.management.mediaservices.v2018_07_01.implementation.MediaManager;
   import com.microsoft.rest.LogLevel;
   ```
1. Pour créer les informations d’identification Active Directory dont vous avez besoin pour effectuer des demandes, ajoutez le code suivant à la méthode principale de la classe d’application et définissez les valeurs que vous avez obtenu à partir de [accéder aux API](access-api-cli-how-to.md):
   
   ```java
   final String clientId = "00000000-0000-0000-0000-000000000000";
   final String tenantId = "00000000-0000-0000-0000-000000000000";
   final String clientSecret = "00000000-0000-0000-0000-000000000000";
   final String subscriptionId = "00000000-0000-0000-0000-000000000000";

   try {
      ApplicationTokenCredentials credentials = new ApplicationTokenCredentials(clientId, tenantId, clientSecret, AzureEnvironment.AZURE);
      credentials.withDefaultSubscriptionId(subscriptionId);

      MediaManager manager = MediaManager
              .configure()
              .withLogLevel(LogLevel.BODY_AND_HEADERS)
              .authenticate(credentials, credentials.defaultSubscriptionId());
      System.out.println("signed in");
   }
   catch (Exception e) {
      System.out.println("Exception encountered.");
      System.out.println(e.toString());
   }
   ```
1. Exécutez l'application.

## <a name="see-also"></a>Voir aussi

- [Concepts Media Services](concepts-overview.md)
- [Kit SDK Java](https://aka.ms/ams-v3-java-sdk)
- [Référence Java](https://aka.ms/ams-v3-java-ref)
- [com.microsoft.azure.mediaservices.v2018_07_01:azure-mgmt-media](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez désormais inclure `import com.microsoft.azure.management.mediaservices.v2018_07_01.*;` et manipuler des entités.
