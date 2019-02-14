---
title: Créer une application web Java sur Linux - Azure App Service
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
ms.date: 12/10/2018
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: 6cce9bbdaea10ffc1c8294d206a87955b13adb58
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55893950"
---
# <a name="quickstart-create-a-java-app-in-app-service-on-linux"></a>Démarrage rapide : créer une application Java dans App Service sous Linux

[App Service sur Linux](app-service-linux-intro.md) fournit un service d’hébergement web hautement scalable appliquant des mises à jour correctives automatiques à l’aide du système d’exploitation Linux. Ce guide de démarrage rapide montre comment utiliser l’interface [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) avec le [plug-in Maven pour les applications web Azure (préversion)](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) afin de déployer un fichier WAR (archive web) Java.

![Exemple d’application s’exécutant dans Azure](media/quickstart-java/java-hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Créer une application Java

Exécutez la commande Maven suivante dans l’invite Cloud Shell pour créer une application nommée `helloworld` :

```bash
mvn archetype:generate -DgroupId=example.demo -DartifactId=helloworld -DarchetypeArtifactId=maven-archetype-webapp
```

## <a name="configure-the-maven-plugin"></a>Configurer le plug-in Maven

Pour un déploiement à partir de Maven, utilisez l’éditeur de code dans Cloud Shell pour ouvrir le fichier de projet `pom.xml` dans le répertoire `helloworld`. 

```bash
code pom.xml
```

Ajoutez ensuite la définition de plug-in suivante dans l’élément `<build>` du fichier `pom.xml`.

```xml
<plugins>
    <!--*************************************************-->
    <!-- Deploy to Tomcat in App Service Linux           -->
    <!--*************************************************-->
      
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.5.3</version>
        <configuration>
   
            <!-- App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>
   
            <!-- Java Runtime Stack for App on Linux-->
            <linuxRuntime>tomcat 8.5-jre8</linuxRuntime>
   
        </configuration>
    </plugin>
</plugins>
```    


> [!NOTE] 
> Dans cet article, nous travaillons uniquement avec des applications Java empaquetées dans des fichiers WAR. Le plug-in prend également en charge les applications web JAR. Accédez à la section [Déployer un fichier JAR SE Java sur App Service dans Linux](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) pour l’essayer.


Mettez à jour les espaces réservés suivants dans la configuration du plug-in :

| Placeholder | Description |
| ----------- | ----------- |
| `RESOURCEGROUP_NAME` | Nom du nouveau groupe de ressources dans lequel créer votre application. En plaçant toutes les ressources d’une application dans un groupe, vous pouvez les gérer ensemble. Par exemple, si vous supprimez le groupe de ressources, vous supprimez également toutes les ressources associées à l’application. Mettez à jour cette valeur avec un nouveau nom de groupe de ressources unique, par exemple, *TestResources*. Vous utiliserez ce nom de groupe de ressources pour nettoyer toutes les ressources Azure dans une section ultérieure. |
| `WEBAPP_NAME` | Le nom d’application fera partie du nom d’hôte pour l’application lors du déploiement vers Azure (WEBAPP_NAME.azurewebsites.net). Mettez à jour cette valeur avec un nom unique pour la nouvelle application App Service, qui va héberger votre application Java (par exemple, *contoso*). |
| `REGION` | Une région Azure où l’application est hébergée, par exemple `westus2`. Vous pouvez obtenir une liste de régions à partir du Cloud Shell ou de l’interface CLI à l’aide de la commande `az account list-locations`. |

## <a name="deploy-the-app"></a>Déployer l’application

Déployez votre application Java sur Azure à l’aide de la commande suivante :

```bash
mvn package azure-webapp:deploy
```

Une fois le déploiement terminé, accédez à l’application déployée à l’aide de l’URL suivante dans votre navigateur web. Par exemple : `http://<webapp>.azurewebsites.net/helloworld`. 

![Exemple d’application s’exécutant dans Azure](media/quickstart-java/java-hello-world-in-browser-curl.png)

**Félicitations !** Vous avez déployé votre première application Java sur App Service sur Linux.


[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]


## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez utilisé Maven pour créer une application Java, configuré le [plug-in Maven pour Azure Web Apps ](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin), puis déployé une application web Java packagée dans une archive web sur App Service sous Linux. Consultez les tutoriels et les articles pratiques suivants pour obtenir plus d’informations sur l’hébergement des applications Java sur App Service sur Linux.

- [Tutoriel : Déployer une application Java d’entreprise avec PostgreSQL](tutorial-java-enterprise-postgresql-app.md)
- [Configurer une source de données Tomcat](app-service-linux-java.md#connecting-to-data-sources)
- [Intégration continue/déploiement continu avec Jenkins](/azure/jenkins/deploy-jenkins-app-service-plugin)
- [Configurer les outils de surveillance des performances des applications](how-to-java-apm-monitoring.md)

