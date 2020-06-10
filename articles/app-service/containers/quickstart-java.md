---
title: 'Démarrage rapide : Créer une application Linux Java'
description: Découvrez comment bien démarrer avec les applications Linux sur Azure App Service en déployant votre première application Java sur un conteneur Linux dans App Service.
keywords: azure, app service, application web, linux, java, maven, démarrage rapide
author: msangapu-msft
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/27/2019
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 1bff1e22df1583491bf8d249cdb8752e9a1e4f57
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2020
ms.locfileid: "84485380"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-linux"></a>Démarrage rapide : Créer une application Java dans Azure App Service sur Linux

[App Service sur Linux](app-service-linux-intro.md) fournit un service d’hébergement web hautement scalable appliquant des mises à jour correctives automatiques à l’aide du système d’exploitation Linux. Ce guide de démarrage rapide montre comment utiliser [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) avec le [plug-in Azure Web App pour Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) afin de déployer un fichier d’archive web (WAR) Java sur le système d’exploitation Linux.

> [!NOTE]
>
> Vous pouvez également le faire à l’aide d’IDE couramment utilisés comme IntelliJ, Eclipse et VS Code. Consultez nos documents similaires dans le [Guide de démarrage d’Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app), le [Guide de démarrage rapide d’Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app) ou le [Guide de démarrage rapide de VS Code](https://code.visualstudio.com/docs/java/java-webapp).
>
![Exemple d’application en cours d’exécution dans Azure App Service](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Créer une application Java

Exécutez la commande Maven suivante dans l’invite Cloud Shell pour créer une application nommée `helloworld` :

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" -Dversion=1.0-SNAPSHOT
```
Ensuite, changez votre répertoire de travail vers le dossier du projet :

```bash
cd helloworld
```

## <a name="configure-the-maven-plugin"></a>Configurer le plug-in Maven

Le processus de déploiement sur Azure App Service peut récupérer vos informations d’identification Azure dans Azure CLI automatiquement. Si Azure CLI n’est pas installé, le plug-in Maven vous connecte avec OAuth ou la connexion de l’appareil. Consultez les détails sur l’[authentification avec les plug-ins Maven](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication), si nécessaire.

Pour configurer le déploiement, exécutez la commande maven dans l’invite de commandes et utilisez les configurations par défaut en appuyant sur **Entrée** jusqu’à ce que l’invite **Confirm (Y/N)** s’affiche et appuyez sur **« y »** pour terminer la configuration. 
```cmd
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
```
Voici un exemple de processus :

```cmd
~@Azure:~/helloworld$ mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< example.demo:helloworld >-----------------------
[INFO] Building helloworld Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.9.1:config (default-cli) @ helloworld ---
[WARNING] The plugin may not work if you change the os of an existing webapp.
Define value for OS(Default: Linux):
1. linux [*]
2. windows
3. docker
Enter index to use:
Define value for javaVersion(Default: jre8):
1. Java 11
2. Java 8 [*]
Enter index to use:
Define value for runtimeStack(Default: TOMCAT 8.5):
1. TOMCAT 9.0
2. TOMCAT 8.5 [*]
Enter index to use:
Please confirm webapp properties
AppName : helloworld-1558400876966
ResourceGroup : helloworld-1558400876966-rg
Region : westeurope
PricingTier : Premium_P1V2
OS : Linux
RuntimeStack : TOMCAT 8.5-jre8
Deploy to slot : false
Confirm (Y/N)? : Y
```

> [!NOTE]
> Dans cet article, nous travaillons uniquement avec des applications Java empaquetées dans des fichiers WAR. Le plug-in prend également en charge les applications web JAR. Accédez à la section [Déployer un fichier JAR SE Java sur App Service dans Linux](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) pour l’essayer.

Ouvrez sur `pom.xml` pour voir la configuration mise à jour.

```bash
code pom.xml
```

Vous pouvez modifier les configurations d’App Service directement dans votre fichier pom si nécessaire. Voici les plus courantes :

 Propriété | Obligatoire | Description | Version
---|---|---|---
`<schemaVersion>` | false | Spécifiez la version du schéma de configuration. Les valeurs prises en charge sont les suivantes : `v1`, `v2`. | 1.5.2
`<resourceGroup>` | true | Groupe de ressources Azure pour votre application web. | 0.1.0+
`<appName>` | true | Le nom de votre application web. | 0.1.0+
`<region>` | true | Spécifie la région où votre application web sera hébergée ; la valeur par défaut est **westeurope**. Toutes les régions valides dans la section [Régions prises en charge](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | 0.1.0+
`<pricingTier>` | false | Le niveau tarifaire pour votre application web. La valeur par défaut est **P1V2**.| 0.1.0+
`<runtime>` | true | La configuration de l’environnement de runtime, dont vous pouvez afficher les détails [ici](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | 0.1.0+
`<deployment>` | true | La configuration de déploiement, dont vous pouvez afficher les détails [ici](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | 0.1.0+

> [!div class="nextstepaction"]
> [J’ai rencontré un problème](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=config)

## <a name="deploy-the-app"></a>Déployer l’application

Déployez votre application Java sur Azure à l’aide de la commande suivante :

```bash
mvn package azure-webapp:deploy
```

Une fois le déploiement terminé, accédez à l’application déployée à l’aide de l’URL suivante dans votre navigateur web. Par exemple : `http://<webapp>.azurewebsites.net`. 

![Exemple d’application en cours d’exécution dans Azure App Service](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Félicitations !** Vous avez déployé votre première application Java sur App Service sur Linux.

> [!div class="nextstepaction"]
> [J’ai rencontré un problème](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Au cours des étapes précédentes, vous avez créé des ressources Azure au sein d’un groupe de ressources. Si vous ne pensez pas avoir besoin de ces ressources plus tard, supprimez le groupe de ressources du portail en exécutant la commande suivante dans Cloud Shell :

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

L’exécution de cette commande peut prendre une minute.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Se connecter à Azure SQL Database à l’aide de Java](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
> [Se connecter à Azure DB pour MySQL à l’aide de Java](/azure/mysql/connect-java)

> [!div class="nextstepaction"]
> [Se connecter à Azure DB pour PostgreSQL à l’aide de Java](/azure/postgresql/connect-java)

> [!div class="nextstepaction"]
> [Configurer une application Java](configure-language-java.md)

> [!div class="nextstepaction"]
> [Intégration continue/déploiement continu avec Jenkins](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [Autres ressources Azure pour les développeurs Java](/java/azure/)

> [!div class="nextstepaction"]
> [En savoir plus sur les plug-ins Maven pour Azure](https://github.com/microsoft/azure-maven-plugins)
