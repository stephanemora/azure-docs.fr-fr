---
title: Configurer un environnement de préproduction dans Azure Spring Cloud | Microsoft Docs
description: Découvrir comment utiliser le déploiement bleu-vert avec Azure Spring Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 9b3a5659e91ca90d31500b10526e3e2179d4e7da
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105046111"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Configurer un environnement intermédiaire dans Azure Spring Cloud

**Cet article s’applique à :** ✔️ Java

Cet article explique comment configurer un déploiement intermédiaire à l'aide du modèle de déploiement bleu-vert dans Azure Spring Cloud. Le déploiement bleu-vert est un modèle de livraison continue Azure DevOps qui consiste à garder en service une version existante (bleue) pendant le déploiement d'une nouvelle version (verte). Cet article vous montre également comment faire passer ce déploiement intermédiaire en production sans modifier le déploiement de production.

## <a name="prerequisites"></a>Prérequis

* Instance d'Azure Spring Cloud au niveau tarifaire Standard
* [Extension Azure Spring Cloud](/cli/azure/azure-cli-extensions-overview) pour Azure CLI

Cet article utilise une application générée à partir de Spring Initializr. Si vous souhaitez utiliser une autre application pour cet exemple, vous devrez apporter une modification simple à une partie publique de l'application afin de différencier votre déploiement intermédiaire du déploiement de production.

>[!TIP]
> [Azure Cloud Shell](https://shell.azure.com) est un interpréteur de commandes interactif gratuit que vous pouvez utiliser pour exécuter les instructions de cet article.  Les outils Azure les plus courants sont préinstallés, notamment les dernières versions de Git, JDK, Maven et Azure CLI. Si vous êtes connecté à votre abonnement Azure, lancez votre instance de Cloud Shell. Pour en savoir plus, consultez [Vue d’ensemble d’Azure Cloud Shell](../cloud-shell/overview.md).

Pour configurer un déploiement bleu-vert dans Azure Spring Cloud, suivez les instructions des sections ci-dessous.

## <a name="install-the-azure-cli-extension"></a>Installer l’extension Azure CLI

Installez l’extension Azure Spring Cloud pour Azure CLI à l’aide de la commande suivante :

```azurecli
az extension add --name spring-cloud
```
## <a name="prepare-the-app-and-deployments"></a>Préparer l'application et les déploiements
Pour générer l'application, procédez comme suit :

1. Générez le code de l'exemple d'application à l'aide de Spring Initializr avec [cette configuration](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client).

2. Téléchargez le code.
3. Ajoutez le fichier source HelloController.java suivant au dossier `\src\main\java\com\example\hellospring\` :

   ```java
   package com.example.hellospring; 
   import org.springframework.web.bind.annotation.RestController; 
   import org.springframework.web.bind.annotation.RequestMapping; 

   @RestController 

   public class HelloController { 

   @RequestMapping("/") 

     public String index() { 

         return "Greetings from Azure Spring Cloud!"; 
     } 

   } 
   ```
4. Générez le fichier .jar :

   ```azurecli
   mvn clean packge -DskipTests
   ```
5. Créez l’application dans votre instance d’Azure Spring Cloud :

   ```azurecli
   az spring-cloud app create -n demo -g <resourceGroup> -s <Azure Spring Cloud instance> --assign-endpoint
   ```
6. Déployez l’application sur Azure Spring Cloud :

   ```azurecli
   az spring-cloud app deploy -n demo -g <resourceGroup> -s <Azure Spring Cloud instance> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar
   ```
7. Modifiez le code de votre déploiement intermédiaire :

   ```java
   package com.example.hellospring; 
   import org.springframework.web.bind.annotation.RestController; 
   import org.springframework.web.bind.annotation.RequestMapping; 

   @RestController 

   public class HelloController { 

   @RequestMapping("/") 

     public String index() { 

         return "Greetings from Azure Spring Cloud! THIS IS THE GREEN DEPLOYMENT"; 
     } 

   } 
   ```
8. Régénérez le fichier .jar :

   ```azurecli
   mvn clean packge -DskipTests
   ```
9. Créez le déploiement vert : 

   ```azurecli
   az spring-cloud app deployment create -n green --app demo -g <resourceGroup> -s <Azure Spring Cloud instance> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar 
   ```

## <a name="view-apps-and-deployments"></a>Afficher les applications et les déploiements

Affichez les applications déployées en procédant comme suit :

1. Accédez à votre instance Azure Spring Cloud dans le portail Azure.

1. Dans le volet gauche, ouvrez le volet **Applications** pour afficher les applications de votre instance de service.

   ![Capture d'écran du volet Applications ouvert.](media/spring-cloud-blue-green-staging/app-dashboard.png)

1. Vous pouvez sélectionner une application et consulter les détails de celle-ci.

   ![Capture d'écran des détails d'une application.](media/spring-cloud-blue-green-staging/app-overview.png)

1. Ouvrez **Déploiements** pour voir tous les déploiements de l’application. La grille affiche le déploiement de production et le déploiement intermédiaire.

   ![Capture d'écran affichant la liste des déploiements d'applications.](media/spring-cloud-blue-green-staging/deployments-dashboard.png)

1. Sélectionnez l'URL pour ouvrir l'application actuellement déployée.
    
   ![Capture d'écran illustrant l'URL de l'application déployée.](media/spring-cloud-blue-green-staging/running-blue-app.png)

1. Sélectionnez **Production** dans la colonne **État** pour afficher l'application par défaut.
    
   ![Capture d'écran illustrant l'URL de l'application par défaut.](media/spring-cloud-blue-green-staging/running-default-app.png)

1. Sélectionnez **Intermédiaire** dans la colonne **État** pour afficher l'application intermédiaire.
    
   ![Capture d'écran illustrant l'URL de l'application intermédiaire.](media/spring-cloud-blue-green-staging/running-staging-app.png)

>[!TIP]
> * Vérifiez que votre point de terminaison de test se termine par une barre oblique (/) pour garantir le bon chargement du fichier CSS.  
> * Si votre navigateur vous demande d’entrer des informations d’identification de connexion pour afficher la page, utilisez [URL Decode](https://www.urldecoder.org/) pour décoder votre point de terminaison de test. URL Decode renvoie une URL au format *https://\<username>:\<password>@\<cluster-name>.test.azureapps.io/gateway/green*. Utilisez ce format pour accéder à votre point de terminaison.

>[!NOTE]    
> Les paramètres du serveur de configuration s'appliquent à la fois à votre environnement intermédiaire et à votre environnement de production. Par exemple, si vous définissez *somepath* comme chemin du contexte (*server.servlet.context-path*) de votre passerelle d'application dans le serveur de configuration, le chemin de votre déploiement vert devient *https://\<username>:\<password>@\<cluster-name>.test.azureapps.io/gateway/green/somepath/...* .
 
Si vous visitez votre passerelle d’application publique à ce stade, vous devriez voir l’ancienne page sans votre nouveau changement.

## <a name="set-the-green-deployment-as-the-production-environment"></a>Définir le déploiement vert comme environnement de production

1. Après avoir vérifié votre changement dans votre environnement intermédiaire, vous pouvez l’envoyer (push) en production. Sur la page **Applications** > **Déploiements**, sélectionnez l'application qui se trouve actuellement en **Production**.

1. Sélectionnez les points de suspension situés après l'**État de l'inscription** du déploiement vert, puis sélectionnez **Définir en tant que déploiement de production**. 

   ![Capture d'écran illustrant les sélections nécessaires pour définir la build intermédiaire en tant que build de production.](media/spring-cloud-blue-green-staging/set-staging-deployment.png)

1. Vérifiez que l'URL de l'application affiche vos modifications.

   ![Capture d'écran illustrant l'URL de l'application désormais en production.](media/spring-cloud-blue-green-staging/new-production-deployment.png)

>[!NOTE]
> Une fois le déploiement vert défini comme environnement de production, le déploiement précédent devient le déploiement intermédiaire.

## <a name="modify-the-staging-deployment"></a>Modifier le déploiement de préproduction

Si vous n'êtes pas satisfait de votre changement, vous pouvez modifier le code de votre application, générer un nouveau package .jar et le charger dans votre déploiement vert à l'aide d'Azure CLI :

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>Supprimer le déploiement intermédiaire

Pour supprimer votre déploiement intermédiaire du portail Azure, accédez à la page correspondante et sélectionnez le bouton **Supprimer**.

Vous pouvez également supprimer votre déploiement intermédiaire à partir d’Azure CLI en exécutant la commande suivante :

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```

## <a name="next-steps"></a>Étapes suivantes

* [CI/CD pour Azure Spring Cloud](./spring-cloud-howto-cicd.md?pivots=programming-language-java)