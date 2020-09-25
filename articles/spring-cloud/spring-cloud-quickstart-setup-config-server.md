---
title: Démarrage rapide - Configurer un serveur de configuration Azure Spring Cloud
description: Décrit la configuration du serveur de configuration Azure Spring Cloud pour le déploiement d’applications.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 639672bdeff2f833c280a041e497197286c9ff24
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90885700"
---
# <a name="quickstart-set-up-azure-spring-cloud-configuration-server"></a>Démarrage rapide : Configurer un serveur de configuration Azure Spring Cloud

Le serveur de configuration Azure Spring Cloud est un service de configuration centralisé pour les systèmes distribués. Il utilise une couche de dépôt enfichable qui prend actuellement en charge le stockage local, Git et Subversion. Dans ce guide de démarrage rapide, vous allez configurer le serveur de configuration pour obtenir des données à partir d’un dépôt Git.

::: zone pivot="programming-language-csharp"

## <a name="prerequisites"></a>Prérequis

* Suivez ce guide de démarrage rapide précédent de la série : [Provisionner le service Azure Spring Cloud](spring-cloud-quickstart-provision-service-instance.md).

## <a name="azure-spring-cloud-config-server-procedures"></a>Procédures pour le serveur de configuration Azure Spring Cloud

Configurez votre serveur de configuration avec l’emplacement du dépôt Git du projet en exécutant la commande suivante. Remplacez `<service instance name>` par le nom du service que vous avez créé. La valeur par défaut du nom de l’instance de service que vous avez définie dans le guide de démarrage rapide précédent ne fonctionne pas avec cette commande.

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples --search-paths steeltoe-sample/config
```

Cette commande demande au serveur de configuration de trouver les données de configuration dans le dossier [steeltoe-sample/config](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample/config) du dépôt de l’exemple d’application. Étant donné que le nom de l’application qui obtiendra les données de configuration est `planet-weather-provider`, le fichier qui sera utilisé est [planet-weather-provider.yml](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/steeltoe-sample/config/planet-weather-provider.yml).

::: zone-end

::: zone pivot="programming-language-java"
Le serveur de configuration Azure Spring Cloud est un service de configuration centralisé pour les systèmes distribués. Il utilise une couche de dépôt enfichable qui prend actuellement en charge le stockage local, Git et Subversion.  Configurez le serveur de configuration pour déployer des applications de microservice sur Azure Spring Cloud.

## <a name="prerequisites"></a>Prérequis

* [Installez JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true)
* [Souscrire à un abonnement Azure](https://azure.microsoft.com/free/)
* (Facultatif) [Installez Azure CLI version 2.0.67 ou ultérieure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) et installez l’extension Azure Spring Cloud avec la commande : `az extension add --name spring-cloud`
* (Facultatif) [Installez Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) et [connectez-vous](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

## <a name="azure-spring-cloud-config-server-procedures"></a>Procédures pour le serveur de configuration Azure Spring Cloud

#### <a name="portal"></a>[Portail](#tab/Azure-portal)

La procédure suivante permet de configurer le serveur de configuration en utilisant le portail Azure pour déployer l’[exemple Piggymetrics](spring-cloud-quickstart-sample-app-introduction.md).

1. Accédez à la page **Vue d’ensemble** du service, puis sélectionnez **Serveur de configuration**.

2. Dans la section **Dépôt par défaut**, définissez **URI** sur « https://github.com/Azure-Samples/piggymetrics-config  ».

3. Sélectionnez **Appliquer** pour enregistrer vos modifications.

    ![Capture d’écran du portail ASC](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

#### <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/Azure-CLI)

La procédure suivante utilise Azure CLI pour configurer le serveur de configuration afin de déployer l’[exemple Piggymetrics](spring-cloud-quickstart-sample-app-introduction.md).

Configurez votre serveur de configuration avec l’emplacement du dépôt Git pour le projet :

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/piggymetrics-config
```

---
::: zone-end

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous prévoyez de passer au prochain guide de démarrage rapide de cette série, vous pouvez ignorer cette étape.

Dans ces guides de démarrage rapide, vous avez créé des ressources Azure qui continueront de générer des frais tant qu’elles resteront dans votre abonnement. Si vous ne prévoyez pas de passer au guide de démarrage suivant, et si vous pensez que vous n’aurez pas besoin de ces ressources, supprimez le groupe de ressources du portail en exécutant la commande suivante dans Cloud Shell :

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Dans un guide de démarrage rapide précédent, vous avez aussi défini le nom du groupe de ressources par défaut. Si vous ne prévoyez pas de passer au guide de démarrage rapide suivant, supprimez cette valeur par défaut en exécutant la commande CLI suivante :

```azurecli
az configure --defaults group=
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer et déployer des applications](spring-cloud-quickstart-deploy-apps.md)
