---
title: 'Démarrage rapide : Créer une application Java dans Azure App Service'
description: Déployez votre premier programme Hello World Java dans Azure App Service en quelques minutes. Le plug-in Azure Web App pour Maven facilite le déploiement d’applications Java.
keywords: azure, app service, application web, windows, linux, java, maven, démarrage rapide
author: jasonfreeberg
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 08/01/2020
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 274228ea5aa9ac9de9725176c8b6221ee9e9542e
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88182695"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>Démarrage rapide : Créer une application Java dans Azure App Service

[Azure App Service](overview.md) offre un service d’hébergement web hautement évolutif appliquant des mises à jour correctives automatiques.  Ce guide de démarrage rapide montre comment utiliser [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) avec le [plug-in Azure Web App pour Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) afin de déployer un fichier WAR (archive web) Java.

> [!NOTE]
> Dans cet article, nous travaillons uniquement avec des applications Java empaquetées dans des fichiers WAR. Le plug-in prend également en charge les applications web JAR. Accédez à la section [Déployer un fichier JAR SE Java sur App Service dans Linux](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) pour l’essayer.

> [!NOTE]
> Vous pouvez également le faire à l'aide d'IDE couramment utilisés comme IntelliJ et Eclipse. Consultez nos documents similaires dans le [Guide de démarrage d’Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app) ou le [Guide de démarrage rapide d’Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app).
>
![Exemple d’application en cours d’exécution dans Azure App Service](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

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

Le processus de déploiement sur Azure App Service peut récupérer vos informations d’identification Azure dans Azure CLI automatiquement. Si Azure CLI n’est pas installé localement, le plug-in Maven vous connecte avec OAuth ou la connexion de l’appareil. Consultez les détails sur l’[authentification avec les plug-ins Maven](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication), si nécessaire.

Vous pouvez exécuter la commande Maven ci-dessous pour configurer le déploiement.
```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
```

::: zone pivot="platform-windows" 
Vous serez invité à sélectionner les éléments suivants : 
* **Système d’exploitation (par défaut : `linux`)**
* **Version de Java (par défaut : `1.8`)**
* **Conteneur web (par défaut : `tomcat 8.5`)** 
 
Veillez à entrer **`2`** pour choisir le système d’exploitation **Windows** à la première étape. Vous pouvez conserver les autres paramètres par défaut en appuyant sur **Entrée**. Enfin, tapez **`Y`** quand l’invite **Confirm (Y/N)** s’affiche pour terminer la configuration.

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
::: zone-end
::: zone pivot="platform-linux"  

Vous serez invité à sélectionner les éléments suivants : 
* **Système d’exploitation (par défaut : `linux`)**
* **Version de Java (par défaut : `Java 8`)**
* **Conteneur web (par défaut : `Tomcat 8.5`)** 

Vous pouvez conserver tous paramètres par défaut en appuyant sur **Entrée**. Enfin, tapez **`Y`** quand l’invite **Confirm (Y/N)** s’affiche pour terminer la configuration.
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
::: zone-end

Vous pouvez modifier les configurations d’App Service directement dans votre `pom.xml` si nécessaire. Voici les plus courantes :

 Propriété | Obligatoire | Description | Version
---|---|---|---
`<schemaVersion>` | false | Spécifiez la version du schéma de configuration. Les valeurs prises en charge sont les suivantes : `v1`, `v2`. | 1.5.2
`<resourceGroup>` | true | Groupe de ressources Azure pour votre application web. | 0.1.0+
`<appName>` | true | Le nom de votre application web. | 0.1.0+
`<region>` | true | Spécifie la région où votre application web sera hébergée ; la valeur par défaut est **westeurope**. Toutes les régions valides dans la section [Régions prises en charge](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | 0.1.0+
`<pricingTier>` | false | Le niveau tarifaire pour votre application web. La valeur par défaut est **P1V2**.| 0.1.0+
`<runtime>` | true | La configuration de l’environnement de runtime, dont vous pouvez afficher les détails [ici](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | 0.1.0+
`<deployment>` | true | La configuration de déploiement, dont vous pouvez afficher les détails [ici](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | 0.1.0+

Soyez attentif aux valeurs `<appName>` et `<resourceGroup>` (`helloworld-1590394316693` et `helloworld-1590394316693-rg` dans la démonstration). Elles seront utilisées ultérieurement.

> [!div class="nextstepaction"]
> [J’ai rencontré un problème](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

## <a name="deploy-the-app"></a>Déployer l’application

Le processus de déploiement sur Azure App Service utilise les informations d’identification du compte provenant d’Azure CLI. [Connectez-vous à Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) avant de continuer.

```azurecli
az login
```
Ensuite, vous pouvez déployer votre application Java sur Azure à l’aide de la commande suivante :

```bash
mvn package azure-webapp:deploy
```

Une fois le déploiement terminé, votre application est prête à l’adresse `http://<appName>.azurewebsites.net/` (`http://helloworld-1590394316693.azurewebsites.net` dans la démonstration). Ouvrez l’URL avec votre navigateur web local. Vous devez voir l’écran suivant :

![Exemple d’application en cours d’exécution dans Azure App Service](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Félicitations !** Vous avez déployé votre première application Java sur App Service.

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
> [Ressources Azure pour les développeurs Java](/java/azure/)

> [!div class="nextstepaction"]
> [Configurer une application Java](configure-language-java.md)

> [!div class="nextstepaction"]
> [Intégration continue/déploiement continu avec Jenkins](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [Mapper un domaine personnalisé](app-service-web-tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [En savoir plus sur les plug-ins Maven pour Azure](https://github.com/microsoft/azure-maven-plugins)
