---
title: 'Démarrage rapide : Créer une application Java sur Windows'
description: Déployez votre premier programme Hello World Java dans Azure App Service sur Windows en quelques minutes. Le plug-in Azure Web App pour Maven facilite le déploiement d’applications Java.
keywords: azure, app service, application web, windows, java, maven, démarrage rapide
author: msangapu-msft
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 05/29/2019
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: ca3c7d6bc6621c4b82a44431ae313384c1653f79
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87324231"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-windows"></a>Démarrage rapide : Créer une application Java dans Azure App Service sur Windows

> [!NOTE]
> Cet article explique comment déployer une application sur App Service sous Windows. Pour déployer une application App Service sur _Linux_, consultez [Créer une application web Java sur Linux](./containers/quickstart-java.md).
>

[Azure App Service](overview.md) offre un service d’hébergement web hautement évolutif appliquant des mises à jour correctives automatiques.  Ce guide de démarrage rapide montre comment utiliser [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) avec le [plug-in Azure Web App pour Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) afin de déployer un fichier WAR (archive web) Java.

> [!NOTE]
> Vous pouvez également le faire à l’aide d’IDE couramment utilisés comme IntelliJ et Eclipse. Consultez nos documents similaires dans le [Guide de démarrage d’Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app) ou le [Guide de démarrage rapide d’Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app).
>
![Exemple d’application en cours d’exécution dans Azure App Service](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Créer une application Java

Exécutez la commande Maven suivante dans l’invite Cloud Shell pour créer une application nommée `helloworld` :

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" "-Dversion=1.0-SNAPSHOT"
```

Ensuite, changez votre répertoire de travail vers le dossier du projet :

```bash
cd helloworld
```

## <a name="configure-the-maven-plugin"></a>Configurer le plug-in Maven

Le processus de déploiement sur Azure App Service peut récupérer vos informations d’identification Azure dans Azure CLI automatiquement. Si Azure CLI n’est pas installé, le plug-in Maven vous connecte avec OAuth ou la connexion de l’appareil. Consultez les détails sur l’[authentification avec les plug-ins Maven](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication), si nécessaire.

Vous pouvez exécuter la commande maven suivante dans l’invite de commandes pour configurer le déploiement, choisissez **« 2 »** pour l’OS **Windows** dans la première étape, puis acceptez les configurations par défaut en appuyant sur **Entrée** jusqu’à ce que l’invite **Confirm (Y/N)** s’affiche, puis appuyez sur **« y »** pour terminer la configuration. 

```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
```

Voici un exemple de processus :

```console
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
Enter index to use: 2
Define value for javaVersion(Default: 1.8): 
1. 1.7
2. 1.7.0_191_ZULU
3. 1.7.0_51
4. 1.7.0_71
5. 1.7.0_80
6. 1.8 [*]
7. 1.8.0_102
8. 1.8.0_111
9. 1.8.0_144
10. 1.8.0_172
11. 1.8.0_172_ZULU
12. 1.8.0_181
13. 1.8.0_181_ZULU
14. 1.8.0_202
15. 1.8.0_202_ZULU
16. 1.8.0_25
17. 1.8.0_60
18. 1.8.0_73
19. 1.8.0_92
20. 11
21. 11.0.2_ZULU
Enter index to use:
Define value for webContainer(Default: tomcat 8.5): 
1. jetty 9.1
2. jetty 9.1.0.20131115
3. jetty 9.3
4. jetty 9.3.13.20161014
5. tomcat 7.0
6. tomcat 7.0.50
7. tomcat 7.0.62
8. tomcat 8.0
9. tomcat 8.0.23
10. tomcat 8.5 [*]
11. tomcat 8.5.20
12. tomcat 8.5.31
13. tomcat 8.5.34
14. tomcat 8.5.37
15. tomcat 8.5.6
16. tomcat 9.0
17. tomcat 9.0.0
18. tomcat 9.0.12
19. tomcat 9.0.14
20. tomcat 9.0.8
Enter index to use:
Please confirm webapp properties
AppName : helloworld-1590394316693
ResourceGroup : helloworld-1590394316693-rg
Region : westeurope
PricingTier : PremiumV2_P1v2
OS : Windows
Java : 1.8
WebContainer : tomcat 8.5
Deploy to slot : false
Confirm (Y/N)? :
[INFO] Saving configuration to pom.
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
> [J’ai rencontré un problème](https://www.research.net/r/javae2e?tutorial=app-service-web-get-started-java&step=config)

## <a name="deploy-the-app"></a>Déployer l’application

Le processus de déploiement sur Azure App Service utilise les informations d’identification du compte provenant d’Azure CLI. [Connectez-vous à Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) avant de continuer.

```azurecli
az login
```
Ensuite, vous pouvez déployer votre application Java sur Azure à l’aide de la commande suivante :

```bash
mvn package azure-webapp:deploy
```

Une fois le déploiement terminé, accédez à l’application déployée à l’aide de l’URL suivante dans votre navigateur web. Par exemple : `http://<webapp>.azurewebsites.net/`.

![Exemple d’application en cours d’exécution dans Azure App Service](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

**Félicitations !** Vous avez déployé votre première application Java sur App Service sur Windows.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Se connecter à Azure SQL Database à l’aide de Java](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
> [Se connecter à Azure DB pour MySQL à l’aide de Java](/azure/mysql/connect-java)

> [!div class="nextstepaction"]
> [Se connecter à Azure DB pour PostgreSQL à l’aide de Java](/azure/postgresql/connect-java)

> [!div class="nextstepaction"]
> [Ressources Azure pour les développeurs Java](/java/azure/)

> [!div class="nextstepaction"]
> [Mapper un domaine personnalisé](app-service-web-tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [En savoir plus sur les plug-ins Maven pour Azure](https://github.com/microsoft/azure-maven-plugins)
