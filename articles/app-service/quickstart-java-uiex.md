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
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 64e4c05e9439c164329dede5d714bec160bc5ae2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102050373"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>Démarrage rapide : Créer une application Java dans Azure App Service

[Azure App Service](overview.md) offre un service d’hébergement web hautement évolutif appliquant des mises à jour correctives automatiques.  Ce guide de démarrage rapide montre comment utiliser [Azure CLI](/cli/azure/get-started-with-azure-cli) avec le [plug-in Azure Web App pour Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) afin de déployer un fichier .jar ou .war. 

Il existe également des versions IDE de cet article. Consultez [Guide de démarrage d’Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app) ou le [Guide de démarrage rapide d’Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app).

## <a name="1-prepare-your-environment"></a>1. Préparation de votre environnement

Avant de commencer la lecture cet article, vous devez disposer des éléments suivants :

+ An <abbr title="Profil qui gère les informations de facturation pour l’utilisation d’Azure.">Compte Azure</abbr> avec un abonnement actif <abbr title="Structure organisationnelle de base dans laquelle vous gérez les ressources dans Azure, généralement associée à une personne ou à un service au sein d’une organisation.">abonnement</abbr>. [Créez un compte gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Interface de ligne de commande Azure](/cli/azure/install-azure-cli).

+ [Java Developer Kit (JDK)](/azure/developer/java/fundamentals/java-jdk-long-term-support) version 8 ou 11.

+ [Apache Maven](https://maven.apache.org) version 3.0 ou ultérieure.

Le plug-in Maven utilise les informations d’identification du compte provenant d’Azure CLI pour effectuer le déploiement sur App Services. [Connectez-vous à Azure CLI](/cli/azure/authenticate-azure-cli) avant de continuer. Pour plus d’informations, consultez [Authentification avec les plug-ins Maven](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication).

```azurecli
az login
```

<br>
<hr/>

## <a name="2-create-a-java-app"></a>2. Créer une application Java

# <a name="java-se"></a>[Java SE](#tab/javase)

Clonez l’exemple de projet [Spring Boot Getting Started](https://github.com/spring-guides/gs-spring-boot).

```bash
git clone https://github.com/spring-guides/gs-spring-boot
```

Accédez au répertoire du projet terminé.

```bash
cd gs-spring-boot/complete
```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

Exécutez la commande Maven suivante dans l’invite Cloud Shell pour créer une application nommée `helloworld` :

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" "-Dversion=1.0-SNAPSHOT"
```

Ensuite, changez votre répertoire de travail vers le dossier du projet :

```bash
cd helloworld
```

---

<br>
<hr/>

## <a name="3-configure-the-maven-plugin"></a>3. Configurer le plug-in Maven

Exécutez la commande Maven ci-dessous pour configurer le déploiement. Cette commande vous permet de configurer le système d’exploitation App Service, la version de Java et la version de Tomcat.

```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.12.0:config
```

::: zone pivot="platform-windows"

# <a name="java-se"></a>[Java SE](#tab/javase)

1. Quand vous êtes invité à choisir l’option **Subscription**, sélectionnez l’abonnement (`Subscription`) approprié en entrant le numéro indiqué au début de la ligne.
1. Quand vous êtes invité à choisir l’option **Application web**, acceptez l’option par défaut `<create>` en appuyant sur Entrée ou bien sélectionnez une application existante.
1. Quand vous êtes invité à choisir l’option **Système d’exploitation**, sélectionnez **Windows** en entrant `3`.
1. Quand vous êtes invité à choisir l’option **Niveau tarifaire**, sélectionnez **B2** en entrant `2`.
1. Utilisez la version de Java par défaut, **Java 8**, en appuyant sur Entrée.
1. Enfin, appuyez sur Entrée dans la dernière invite pour confirmer vos sélections.

    Le résumé généré ressemble à l’extrait de code ci-dessous.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007390755
    ResourceGroup : spring-boot-1599007390755-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Windows
    Java : 1.8
    WebContainer : java 8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 41.118 s
    [INFO] Finished at: 2020-09-01T17:43:45-07:00
    [INFO] ------------------------------------------------------------------------
    </pre>

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. Quand vous êtes invité à choisir l’option **Subscription**, sélectionnez l’abonnement (`Subscription`) approprié en entrant le numéro indiqué au début de la ligne.
1. Quand vous êtes invité à choisir l’option **Application web**, acceptez l’option par défaut `<create>` en appuyant sur Entrée ou bien sélectionnez une application existante.
1. Quand vous êtes invité à choisir l’option **Système d’exploitation**, sélectionnez **Windows** en entrant `3`.
1. Quand vous êtes invité à choisir l’option **Niveau tarifaire**, sélectionnez **B2** en entrant `2`.
1. Utilisez la version de Java par défaut, **Java 8**, en appuyant sur Entrée.
1. Utilisez le conteneur web par défaut, **Tomcat 8.5**, en appuyant sur Entrée.
1. Enfin, appuyez sur Entrée dans la dernière invite pour confirmer vos sélections.

    Le résumé généré ressemble à l’extrait de code ci-dessous.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003152123
    ResourceGroup : helloworld-1599003152123-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Windows
    Java : 1.8
    WebContainer : tomcat 8.5
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 03:03 min
    [INFO] Finished at: 2020-09-01T16:35:30-07:00
    [INFO] ------------------------------------------------------------------------
    </pre>

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="java-se"></a>[Java SE](#tab/javase)

1. Quand vous êtes invité à choisir l’option **Subscription**, sélectionnez l’abonnement (`Subscription`) approprié en entrant le numéro indiqué au début de la ligne.
1. Quand vous êtes invité à choisir l’option **Application web**, acceptez l’option par défaut `<create>` en appuyant sur Entrée ou bien sélectionnez une application existante.
1. Quand vous êtes invité à choisir l’option **Système d’exploitation**, sélectionnez **Linux** en appuyant sur Entrée.
1. Quand vous êtes invité à choisir l’option **Niveau tarifaire**, sélectionnez **B2** en entrant `2`.
1. Utilisez la version de Java par défaut, **Java 8**, en appuyant sur Entrée.
1. Enfin, appuyez sur Entrée dans la dernière invite pour confirmer vos sélections.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007116351
    ResourceGroup : spring-boot-1599007116351-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Linux
    RuntimeStack : JAVA 8-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 20.925 s
    [INFO] Finished at: 2020-09-01T17:38:51-07:00
    [INFO] ------------------------------------------------------------------------
    </pre>

### <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. Quand vous êtes invité à choisir l’option **Subscription**, sélectionnez l’abonnement (`Subscription`) approprié en entrant le numéro indiqué au début de la ligne.
1. Quand vous êtes invité à choisir l’option **Application web**, acceptez l’option par défaut `<create>` en appuyant sur Entrée ou bien sélectionnez une application existante.
1. Quand vous êtes invité à choisir l’option **Système d’exploitation**, sélectionnez **Linux** en appuyant sur Entrée.
1. Quand vous êtes invité à choisir l’option **Niveau tarifaire**, sélectionnez **B2** en entrant `2`.
1. Utilisez la version de Java par défaut, **Java 8**, en appuyant sur Entrée.
1. Utilisez le conteneur web par défaut, **Tomcat 8.5**, en appuyant sur Entrée.
1. Enfin, appuyez sur Entrée dans la dernière invite pour confirmer vos sélections.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003744223
    ResourceGroup : helloworld-1599003744223-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Linux
    RuntimeStack : TOMCAT 8.5-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 50.785 s
    [INFO] Finished at: 2020-09-01T16:43:09-07:00
    [INFO] ------------------------------------------------------------------------
    </pre>

---

::: zone-end

Vous pouvez modifier les configurations d’App Service directement dans votre `pom.xml` si nécessaire. Voici les plus courantes :

Propriété | Obligatoire | Description | Version
---|---|---|---
`<schemaVersion>` | false | Spécifiez la version du schéma de configuration. Les valeurs prises en charge sont les suivantes : `v1`, `v2`. | 1.5.2
`<subscriptionId>` | false | Spécifiez l’ID d’abonnement. | 0.1.0+
`<resourceGroup>` | true | Azure <abbr title="Conteneur logique pour des ressources Azure associées que vous pouvez gérer en tant qu’unité.">groupe de ressources</abbr> pour votre application web. | 0.1.0+
`<appName>` | true | Le nom de votre application web. | 0.1.0+
`<region>` | true | Spécifie la région où votre application web sera hébergée ; la valeur par défaut est **westeurope**. Toutes les régions valides dans la section [Régions prises en charge](https://azure.microsoft.com/global-infrastructure/services/?products=app-service). | 0.1.0+
`<pricingTier>` | false | Le niveau tarifaire pour votre application web. La valeur par défaut est **P1V2** pour une charge de travail de production, tandis que **B2** est la valeur minimale recommandée pour le développement/test Java. [En savoir plus](https://azure.microsoft.com/pricing/details/app-service/linux/)| 0.1.0+
`<runtime>` | true | La configuration de l’environnement de runtime, dont vous pouvez afficher les détails [ici](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details). | 0.1.0+
`<deployment>` | true | La configuration de déploiement, dont vous pouvez afficher les détails [ici](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details). | 0.1.0+

Soyez attentif aux valeurs de `<appName>` et de `<resourceGroup>` (`helloworld-1590394316693` et `helloworld-1590394316693-rg` dans l’exemple de sortie), car elles seront utilisées ultérieurement.

[Signaler un problème](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

<br>
<hr/>

## <a name="4-deploy-the-app"></a>4. Déployer l’application

Déployez votre application Java sur Azure en utilisant la commande suivante.

```bash
mvn package azure-webapp:deploy
```

Une fois le déploiement terminé, votre application est prête sur `http://<appName>.azurewebsites.net/`. Ouvrez l’URL avec votre navigateur web local. Voici ce que vous devez normalement voir :

![Exemple d’application en cours d’exécution dans Azure App Service](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Félicitations !** Vous avez déployé votre première application Java sur App Service.

[Signaler un problème](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

<br>
<hr/>

## <a name="5-clean-up-resources"></a>5. Nettoyer les ressources

Supprimez l’application Java et ses ressources associées afin d’éviter des coûts supplémentaires.

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

L’exécution de cette commande peut prendre une minute.

<br>
<hr/>

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Se connecter à Azure DB pour PostgreSQL à l’aide de Java](../postgresql/connect-java.md)

> [!div class="nextstepaction"]
> [Configurer CI/CD](deploy-continuous-deployment.md)

> [!div class="nextstepaction"]
> [Informations sur la tarification](https://azure.microsoft.com/pricing/details/app-service/linux/)

> [!div class="nextstepaction"]
> [Agréger les journaux et les métriques](troubleshoot-diagnostic-logs.md)

> [!div class="nextstepaction"]
> [Monter en puissance](manage-scale-up.md)

> [!div class="nextstepaction"]
> [Ressources Azure pour les développeurs Java](/java/azure/)

> [!div class="nextstepaction"]
> [Configurer votre application Java](configure-language-java.md)
