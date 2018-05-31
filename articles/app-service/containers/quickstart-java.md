---
title: Un démarrage rapide pour la création d’une application web Java dans Azure App Service sur Linux
description: Dans ce démarrage rapide, vous déployez votre premier programme Java Hello World dans Azure App Service sur Linux en quelques minutes.
services: app-service\web
documentationcenter: ''
author: msangapu
manager: cfowler
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/07/2018
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: 2018f5b7051f2b6906372dad3319c763974b93b1
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34355183"
---
# <a name="quickstart-create-a-java-web-app-in-app-service-on-linux"></a>Démarrage rapide : créer une application web Java dans App Service sur Linux

App Service sur Linux fournit actuellement une fonctionnalité d’évaluation pour prendre en charge les applications web Java. Pour plus d’informations sur les préversions, veuillez consulter les [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

[App Service sur Linux](app-service-linux-intro.md) fournit un service d’hébergement web hautement scalable appliquant des mises à jour correctives automatiques à l’aide du système d’exploitation Linux. Ce démarrage rapide montre comment utiliser [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) avec le [Plug-in Maven pour les applications web Azure (préversion)](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) pour déployer une application web Java avec une image Linux intégrée.

![Exemple d’application s’exécutant dans Azure](media/quickstart-java/java-hello-world-in-browser.png)

[Le déploiement d’applications web Java sur un conteneur Linux dans le cloud à l’aide du kit de ressources Azure pour IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-hello-world-web-app-linux) est une autre approche pour déployer votre application Java sur votre propre conteneur.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Prérequis


Pour effectuer ce démarrage rapide : 

* [Azure CLI 2.0 ou ultérieure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) installé localement.
* [Apache Maven](http://maven.apache.org/).



## <a name="create-a-java-app"></a>Créer une application Java

Exécutez la commande suivante à l’aide de Maven pour créer une nouvelle application web *helloworld* :  

    mvn archetype:generate -DgroupId=example.demo -DartifactId=helloworld -DarchetypeArtifactId=maven-archetype-webapp

Passez au nouveau répertoire de projet *helloworld* et générez tous les modules à l’aide de la commande suivante :

    mvn verify

Cette commande vérifiera et créera tous les modules, y compris le fichier *helloworld.war* dans le sous-répertoire *helloworld/target*.


## <a name="deploying-the-java-app-to-app-service-on-linux"></a>Déploiement de l’application Java sur App Service sur Linux

Il existe plusieurs options de déploiement pour le déploiement de vos applications web Java vers App Service sur Linux. Ces options sont les suivantes :

* [Déploiement via le plug-in Maven pour Azure Web Apps](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin)
* [Déploiement via des archives ZIP ou WAR](https://docs.microsoft.com/azure/app-service/app-service-deploy-zip)
* [Déploiement via FTP](https://docs.microsoft.com/azure/app-service/app-service-deploy-ftp)

Dans ce guide de démarrage rapide, vous allez utiliser le plug-in Maven pour les applications web Azure. Il présente des avantages dans la mesure où il est facile à utiliser à partir de Maven et il crée les ressources Azure nécessaires pour vous (groupe de ressources, plan App Service et application web).

### <a name="deploy-with-maven"></a>Déployer avec Maven

Pour déployer à partir de Maven, ajoutez la définition de plug-in suivante à l’intérieur de l’élément `<build>` du fichier *pom.xml* :

```xml
    <plugins>
      <plugin>
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-webapp-maven-plugin</artifactId> 
        <version>1.1.0</version>
        <configuration> 
          <resourceGroup>YOUR_RESOURCE_GROUP</resourceGroup> 
          <appName>YOUR_WEB_APP</appName> 
          <linuxRuntime>tomcat 9.0-jre8</linuxRuntime>
          <deploymentType>ftp</deploymentType> 
          <resources> 
              <resource> 
                  <directory>${project.basedir}/target</directory> 
                  <targetPath>webapps</targetPath> 
                  <includes> 
                      <include>*.war</include> 
                  </includes> 
                  <excludes> 
                      <exclude>*.xml</exclude> 
                  </excludes> 
              </resource> 
          </resources> 
        </configuration>
      </plugin>
    </plugins>
```    

Mettez à jour les espaces réservés suivants dans la configuration du plug-in :

| Placeholder | Description |
| ----------- | ----------- |
| `YOUR_RESOURCE_GROUP` | Nom du nouveau groupe de ressources dans lequel créer votre application web. En plaçant toutes les ressources d’une application dans un groupe, vous pouvez les gérer ensemble. Par exemple, si vous supprimez le groupe de ressources, vous supprimez également toutes les ressources associées à l’application. Mettez à jour cette valeur avec un nouveau nom de groupe de ressources unique, par exemple, *TestResources*. Vous utiliserez ce nom de groupe de ressources pour nettoyer toutes les ressources Azure dans une section ultérieure. |
| `YOUR_WEB_APP` | Le nom d’application fera partie du nom d’hôte pour l’application web lors du déploiement vers Azure (YOUR_WEB_APP.azurewebsites.net). Mettez à jour cette valeur avec un nom unique pour la nouvelle application web Azure, qui va héberger votre application Java, par exemple *contoso*. |

L’élément `linuxRuntime` de la configuration contrôle l’image Linux intégrée utilisée avec votre application.

Exécutez la commande suivante et suivez toutes les instructions pour vous authentifier auprès de Azure CLI :

    az login

Déployez votre application Java vers l’application web à l’aide de la commande suivante :

    mvn clean package azure-webapp:deploy


Une fois le déploiement terminé, accédez à l’application déployée à l’aide de l’URL suivante dans votre navigateur web.

```bash
http://<app_name>.azurewebsites.net/helloworld
```

L’exemple de code Java s’exécute dans une application web avec une image intégrée.

![Exemple d’application s’exécutant dans Azure](media/quickstart-java/java-hello-world-in-browser-curl.png)

**Félicitations !** Vous avez déployé votre première application Java sur App Service sur Linux.


[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]


## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez utilisé Maven pour créer une application web Java, puis vous avez déployé l’application web Java vers App Service sur Linux. Pour en savoir plus sur l’utilisation de Java avec Azure, suivez le lien ci-dessous.

> [!div class="nextstepaction"]
> [Azure pour les développeurs Java](https://docs.microsoft.com/java/azure/)

