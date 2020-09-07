---
title: Démarrage rapide - Configurer un serveur de configuration Azure Spring Cloud
description: Décrit la configuration du serveur de configuration Azure Spring Cloud pour le déploiement d’applications.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: 47f74b551919177b13f5a72d6eedeae3c77b9f14
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951660"
---
# <a name="quickstart-set-up-azure-spring-cloud-configuration-server"></a>Démarrage rapide : Configurer un serveur de configuration Azure Spring Cloud

Le serveur de configuration Azure Spring Cloud est un service de configuration centralisé pour les systèmes distribués. Il utilise une couche de dépôt enfichable qui prend actuellement en charge le stockage local, Git et Subversion.  Configurez le serveur de configuration pour déployer des applications de microservice sur Azure Spring Cloud.

## <a name="prerequisites"></a>Prérequis

* [Installez JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Souscrire à un abonnement Azure](https://azure.microsoft.com/free/)
* (Facultatif) [Installez Azure CLI version 2.0.67 ou ultérieure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) et installez l’extension Azure Spring Cloud avec la commande : `az extension add --name spring-cloud`
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

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Créer et déployer des applications](spring-cloud-quickstart-deploy-apps.md)
