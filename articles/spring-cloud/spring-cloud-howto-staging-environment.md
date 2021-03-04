---
title: Configurer un environnement de préproduction dans Azure Spring Cloud | Microsoft Docs
description: Découvrir comment utiliser le déploiement bleu-vert avec Azure Spring Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: d610e79773dabb9942352747f1f032b17730ffca
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738750"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Configurer un environnement intermédiaire dans Azure Spring Cloud

**Cet article s’applique à :** ✔️ Java

Cet article explique comment configurer un déploiement intermédiaire à l’aide du modèle de déploiement bleu-vert dans Azure Spring Cloud. Le déploiement bleu-vert est un modèle de livraison continue Azure DevOps qui consiste à garder en service une version existante (bleue) pendant le déploiement d’une nouvelle version (verte). Cet article vous montre également comment faire passer ce déploiement intermédiaire en production sans modifier le déploiement de production.

## <a name="prerequisites"></a>Prérequis

* Instance d’Azure Spring Cloud au **niveau tarifaire** *Standard*.
* [Extension Azure Spring Cloud](/cli/azure/azure-cli-extensions-overview) d’Azure CLI.

Cet article utilise une application générée à partir de Spring Initializr. Si vous voulez utiliser une autre application pour cet exemple, vous devrez effectuer un changement simple dans une partie publique de l’application afin de différencier votre déploiement intermédiaire de la production.

>[!TIP]
> Azure Cloud Shell est un interpréteur de commandes interactif gratuit, qui vous permet d’exécuter les instructions de cet article.  Les outils Azure les plus courants sont préinstallés, notamment les dernières versions de Git, JDK, Maven et Azure CLI. Si vous êtes connecté à votre abonnement Azure, lancez votre [Azure Cloud Shell](https://shell.azure.com).  Pour en savoir plus, consultez [Vue d’ensemble d’Azure Cloud Shell](../cloud-shell/overview.md).

Pour configurer des déploiements bleus-verts dans Azure Spring Cloud, suivez les instructions des sections suivantes.

## <a name="install-the-azure-cli-extension"></a>Installer l’extension Azure CLI

Installez l’extension Azure Spring Cloud pour Azure CLI à l’aide de la commande suivante :

```azurecli
az extension add --name spring-cloud
```
## <a name="prepare-app-and-deployments"></a>Préparer l’application et les déploiements
Pour générer l’application, procédez comme suit :
1. Générez le code de l’exemple d’application à l’aide de Spring Initializr avec [cette configuration](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client).

2. Téléchargez le code.
3. Ajoutez le fichier source HelloController.java suivant au dossier `\src\main\java\com\example\hellospring\`.
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
az spring-cloud app create -n demo -g <resourceGroup> -s <Azure Spring Cloud instance> --is-public
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

Affichez les applications déployées à l’aide des procédures suivantes.

1. Accédez à votre instance Azure Spring Cloud dans le portail Azure.

1. Dans le volet de navigation gauche, ouvrez le panneau « Applications » pour afficher les applications de votre instance de service.

    [ ![Applications - Tableau de bord](media/spring-cloud-blue-green-staging/app-dashboard.png)](media/spring-cloud-blue-green-staging/app-dashboard.png)

1. Vous pouvez cliquer sur une application et afficher les détails.

    [ ![Applications - Vue d’ensemble](media/spring-cloud-blue-green-staging/app-overview.png)](media/spring-cloud-blue-green-staging/app-overview.png)

1. Ouvrez **Déploiements** pour voir tous les déploiements de l’application. La grille affiche le déploiement de production et le déploiement intermédiaire.

    [ ![Tableau de bord des applications/déploiements](media/spring-cloud-blue-green-staging/deployments-dashboard.png)](media/spring-cloud-blue-green-staging/deployments-dashboard.png)

1. Cliquez sur l’URL pour ouvrir l’application actuellement déployée.
    ![URL déployée](media/spring-cloud-blue-green-staging/running-blue-app.png)
1. Cliquez sur **Production** dans la colonne **État** pour afficher l’application par défaut.
    ![Exécution par défaut](media/spring-cloud-blue-green-staging/running-default-app.png)
1. Cliquez sur **Intermédiaire** dans la colonne **État** pour afficher l’application intermédiaire.
    ![Exécution intermédiaire](media/spring-cloud-blue-green-staging/running-staging-app.png)

>[!TIP]
> * Vérifiez que votre point de terminaison de test se termine par une barre oblique (/) pour garantir le bon chargement du fichier CSS.  
> * Si votre navigateur vous demande d’entrer des informations d’identification de connexion pour afficher la page, utilisez [URL Decode](https://www.urldecoder.org/) pour décoder votre point de terminaison de test. URL Decode retourne une URL au format « https://\<username>:\<password>@\<cluster-name>.test.azureapps.io/gateway/green ».  Utilisez-le pour accéder à votre point de terminaison.

>[!NOTE]    
> Les paramètres du serveur de configuration s’appliquent à votre environnement intermédiaire et à la production. Par exemple, si vous définissez *somepath* comme chemin du contexte (`server.servlet.context-path`) de votre passerelle d’application dans le serveur de configuration, le chemin de votre déploiement vert devient : « https://\<username>:\<password>@\<cluster-name>.test.azureapps.io/gateway/green/somepath/... ».
 
 Si vous visitez votre passerelle d’application publique à ce stade, vous devriez voir l’ancienne page sans votre nouveau changement.

## <a name="set-the-green-deployment-as-the-production-environment"></a>Définir le déploiement vert comme environnement de production

1. Après avoir vérifié votre changement dans votre environnement intermédiaire, vous pouvez l’envoyer (push) en production. Dans la page **Applications**/**Déploiements**, sélectionnez l’application qui se trouve actuellement dans `Production`.

1. Cliquez sur le bouton de sélection après l’**état d’inscription** du déploiement vert et définissez la build intermédiaire sur production. 

   [ ![Définir production sur intermédiaire](media/spring-cloud-blue-green-staging/set-staging-deployment.png)](media/spring-cloud-blue-green-staging/set-staging-deployment.png)

1. L’URL de l’application doit maintenant afficher vos modifications.

   ![Déploiement intermédiaire maintenant en production](media/spring-cloud-blue-green-staging/new-production-deployment.png)

>[!NOTE]
> Une fois le déploiement vert défini comme environnement de production, le déploiement précédent devient le déploiement intermédiaire.

## <a name="modify-the-staging-deployment"></a>Modifier le déploiement de préproduction

Si vous n’êtes pas satisfait de votre changement, vous pouvez modifier le code de votre application, générer un nouveau package jar et le charger dans votre déploiement Vert à l’aide d’Azure CLI.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>Supprimer le déploiement intermédiaire

Pour supprimer votre déploiement intermédiaire du port Azure, accédez à la page de votre déploiement intermédiaire et sélectionnez le bouton **Supprimer**.

Vous pouvez également supprimer votre déploiement intermédiaire à partir d’Azure CLI en exécutant la commande suivante :

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```

## <a name="next-steps"></a>Étapes suivantes

* [CI/CD pour Azure Spring Cloud](https://review.docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-cicd?branch=pr-en-us-142929&pivots=programming-language-java)