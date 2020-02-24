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
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 3cf759294a31fcf90c5a3f4a6cdc68e3c35882e0
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425345"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-windows"></a>Démarrage rapide : Créer une application Java dans Azure App Service sur Windows

> [!NOTE]
> Cet article explique comment déployer une application sur App Service sous Windows. Pour déployer une application App Service sur _Linux_, consultez [Créer une application web Java sur Linux](./containers/quickstart-java.md).
>

[Azure App Service](overview.md) offre un service d’hébergement web hautement évolutif appliquant des mises à jour correctives automatiques.  Ce guide de démarrage rapide montre comment utiliser [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) avec le [plug-in Azure Web App pour Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) afin de déployer un fichier WAR (archive web) Java.

> [!NOTE]
> Vous pouvez également le faire à l’aide d’IDE couramment utilisés comme IntelliJ et Eclipse. Consultez nos documents similaires dans le [Guide de démarrage d’Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app) ou le [Guide de démarrage rapide d’Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app).
>
![Exemple d’application en cours d’exécution dans Azure App Service](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

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
    <!-- Deploy to Tomcat in App Service Windows         -->
    <!--*************************************************-->
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.8.0</version>
        <configuration>
            <!-- Specify v2 schema -->
            <schemaVersion>v2</schemaVersion>
            <!-- App information -->
            <subscriptionId>SUBSCRIPTION_ID</subscriptionId>
            <resourceGroup>RESOURCEGROUP_NAME</resourceGroup>
            <appName>WEBAPP_NAME</appName>
            <region>REGION</region>
            <!-- Java Runtime Stack for App Service on Windows-->
            <runtime>
                <os>windows</os>
                <javaVersion>1.8</javaVersion>
                <webContainer>tomcat 9.0</webContainer>
            </runtime>
            <deployment>
                <resources>
                    <resource>
                        <directory>${project.basedir}/target</directory>
                        <includes>
                            <include>*.war</include>
                        </includes>
                    </resource>
                </resources>
            </deployment>
        </configuration>
    </plugin>
</plugins>
```

> [!NOTE]
> Dans cet article, nous travaillons uniquement avec des applications Java empaquetées dans des fichiers WAR. Le plug-in prend également en charge les applications web JAR. Accédez à la section [Déployer un fichier JAR SE Java sur App Service dans Linux](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) pour l’essayer.


Mettez à jour les espaces réservés suivants dans la configuration du plug-in :

| Espace réservé | Description |
| ----------- | ----------- |
| `SUBSCRIPTION_ID` | ID unique de l’abonnement dans lequel vous voulez déployer votre application. Vous pouvez trouver l’ID de l’abonnement par défaut dans Cloud Shell ou dans l’interface CLI à l’aide de la commande `az account show`. Pour connaître tous les abonnements disponibles, utilisez la commande `az account list`.|
| `RESOURCEGROUP_NAME` | Nom du nouveau groupe de ressources dans lequel créer votre application. En plaçant toutes les ressources d’une application dans un groupe, vous pouvez les gérer ensemble. Par exemple, si vous supprimez le groupe de ressources, vous supprimez également toutes les ressources associées à l’application. Mettez à jour cette valeur avec un nouveau nom de groupe de ressources unique, par exemple, *myResourceGroup*. Vous utiliserez ce nom de groupe de ressources pour nettoyer toutes les ressources Azure dans une section ultérieure. |
| `WEBAPP_NAME` | Le nom d’application fera partie du nom d’hôte pour l’application lors du déploiement vers Azure (WEBAPP_NAME.azurewebsites.net). Mettez à jour cette valeur avec un nom unique pour la nouvelle application App Service, qui va héberger votre application Java (par exemple, *contoso*). |
| `REGION` | Une région Azure où l’application est hébergée, par exemple *westus2*. Vous pouvez obtenir une liste de régions à partir du Cloud Shell ou de l’interface CLI à l’aide de la commande `az account list-locations`. |

## <a name="deploy-the-app"></a>Déployer l’application

Déployez votre application Java sur Azure à l’aide de la commande suivante :

```bash
mvn package azure-webapp:deploy
```

Une fois le déploiement terminé, accédez à l’application déployée à l’aide de l’URL suivante dans votre navigateur web. Par exemple : `http://<webapp>.azurewebsites.net/`.

![Exemple d’application en cours d’exécution dans Azure App Service](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

**Félicitations !** Vous avez déployé votre première application Java sur App Service sur Windows.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ressources Azure pour les développeurs Java](/java/azure/)

> [!div class="nextstepaction"]
> [Mapper un domaine personnalisé](app-service-web-tutorial-custom-domain.md)
