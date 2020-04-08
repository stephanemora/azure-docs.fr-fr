---
title: Déployer la solution de supervision à distance à l’aide d’Azure CLI – Azure | Microsoft Docs
description: Ce guide pratique montre comment approvisionner l’accélérateur de solution de supervision à distance à l’aide d’Azure CLI.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: 501ca51a9542229a14e98a56679837950a82891e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258292"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>Déployer l’accélérateur de solution de supervision à distance à l’aide de l’interface CLI

Ce guide pratique montre comment déployer l’accélérateur de solution de supervision à distance. Vous déployez la solution à l’aide de l’interface CLI. Vous pouvez également déployer la solution avec l’interface utilisateur basée sur le web sur azureiotsolutions.com. Pour découvrir cette option, consultez le guide de démarrage rapide [Déployer l’accélérateur de solution de supervision à distance](quickstart-remote-monitoring-deploy.md).

## <a name="prerequisites"></a>Prérequis

Pour déployer l’accélérateur de solution de supervision à distance, vous avez besoin d’un abonnement Azure actif.

Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, consultez [Essai gratuit Azure](https://azure.microsoft.com/pricing/free-trial/).

Pour pouvoir exécuter l’interface CLI, vous devez installer [Node.js](https://nodejs.org/) sur votre ordinateur local.

## <a name="install-the-cli"></a>Installer l’interface de ligne de commande

Pour installer l’interface CLI, exécutez la commande suivante dans votre environnement de ligne de commande :

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Se connecter à l’interface CLI

Avant de pouvoir déployer l’accélérateur de solution, vous devez vous connecter à votre abonnement Azure à l’aide d’Azure CLI :

```cmd/sh
pcs login
```

Suivez les instructions à l’écran pour effectuer le processus de connexion.

## <a name="deployment-options"></a>Options de déploiement

Quand vous déployez l’accélérateur de solution, vous disposez de plusieurs options pour configurer le processus de déploiement :

| Option | Valeurs | Description |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard`, `local` | Utilisez un déploiement _basic_ à des fins de test et de démonstration ; il déploie tous les microservices sur une seule machine virtuelle. Utilisez un déploiement _standard_ à des fins de production ; il déploie les microservices sur plusieurs machines virtuelles. Un déploiement _local_ configure un conteneur Docker pour exécuter les microservices sur votre ordinateur local, et il utilise des services Azure tels que Stockage et Cosmos DB. |
| Runtime | `dotnet`, `java` | Sélectionne l’implémentation de langage des microservices. |

Pour découvrir comment utiliser le déploiement local, consultez [Running the remote monitoring solution locally](iot-accelerators-remote-monitoring-deploy-local.md) (Exécution locale de la solution de supervision à distance).

## <a name="basic-and-standard-deployments"></a>Déploiements de base et standard

Cette section récapitule les principales différences entre les déploiements de base et standard.

### <a name="basic"></a>De base

Vous pouvez effectuer un déploiement de base à partir de [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) ou avec l’interface CLI.

Le déploiement de base est destiné à présenter la solution. Pour réduire les coûts, tous les microservices sont déployés dans une seule machine virtuelle. Ce déploiement n’utilise pas d’architecture prête pour la production.

Un déploiement de base crée les services suivants dans votre abonnement Azure :

| Count | Ressource                       | Type         | Utilisation |
|-------|--------------------------------|--------------|----------|
| 1     | [Machine virtuelle Linux](https://azure.microsoft.com/services/virtual-machines/) | Standard D1 V2  | Hébergement des microservices |
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                  | Niveau Standard S1 | Gestion des appareils et communication |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)              | standard        | Stockage des données de configuration, des règles, des alertes et d’autres supports de stockage à froid |  
| 1     | [Compte Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)  | standard        | Stockage des points de contrôle streaming et de machine virtuelle |
| 1     | [Application Web](https://azure.microsoft.com/services/app-service/web/)        |                 | Hébergement de l’application web frontend |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Gestion des identités d’utilisateur et de la sécurité |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | standard                | Affichage des emplacements de ressources |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 unités              | Prise en charge des analyses en temps réel |
| 1     | [Service d’approvisionnement des appareils Azure](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Approvisionnement des appareils à grande échelle |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 : 1 unité              | Stockage des données de messages et activation de l’analyse approfondie des données de télémétrie |

### <a name="standard"></a>standard

Vous pouvez effectuer un déploiement standard seulement avec l’interface CLI.

Un déploiement standard est un déploiement prêt pour la production, qu’un développeur peut personnaliser et étendre. Utilisez l’option de déploiement standard quand vous êtes prêt à personnaliser une architecture prête pour la production, créée pour la mise à l’échelle et l’extensibilité. Les microservices d’application sont générés en tant que conteneurs Docker et déployés à l’aide d’Azure Kubernetes Service. L’orchestrateur Kubernetes déploie, met à l’échelle et gère les microservices.

Un déploiement standard crée les services suivants dans votre abonnement Azure :

| Count | Ressource                                     | Référence (SKU) / Taille      | Utilisation |
|-------|----------------------------------------------|-----------------|----------|
| 1     | [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service)| Utilisez un service d’orchestration de conteneurs Kubernetes entièrement managé (3 agents par défaut).|
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                     | Niveau Standard S2 | Gestion des appareils, commande et contrôle |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)                 | standard        | Stockage des données de configuration et de télémétrie d’appareil comme les règles, les alertes et les messages |
| 5     | [Comptes de stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | standard        | 4 pour le stockage de machine virtuelle et 1 pour les points de contrôle streaming |
| 1     | [App Service](https://azure.microsoft.com/services/app-service/web/)             | S1 Standard     | Passerelle d’application via TLS |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Gestion des identités d’utilisateur et de la sécurité |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | standard                | Affichage des emplacements de ressources |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 unités              | Prise en charge des analyses en temps réel |
| 1     | [Service d’approvisionnement des appareils Azure](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Approvisionnement des appareils à grande échelle |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 : 1 unité              | Stockage des données de messages et activation de l’analyse approfondie des données de télémétrie |

> [!NOTE]
> Les informations relatives à la tarification de ces services sont disponibles à l’adresse [https://azure.microsoft.com/pricing](https://azure.microsoft.com/pricing). Des détails relatifs à l’utilisation et à la facturation de votre abonnement sont disponibles sur le [portail Azure](https://portal.azure.com/).

## <a name="deploy-the-solution-accelerator"></a>Déployer l’accélérateur de solution

Exemples de déploiements :

### <a name="example-deploy-net-version"></a>Exemple : déployer la version .NET

L’exemple suivant montre comment déployer la version .NET de base de l’accélérateur de solution de supervision à distance :

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>Exemple : déployer la version Java

L’exemple suivant montre comment déployer la version Java standard de l’accélérateur de solution de supervision à distance :

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>options de la commande pcs

Quand vous exécutez la commande `pcs` pour déployer une solution, vous êtes invité à indiquer les éléments suivants :

- Un nom pour votre solution. Ce nom doit être unique.
- Sélectionnez l’abonnement Azure à utiliser.
- Un emplacement.
- Les informations d’identification des machines virtuelles qui hébergent les microservices. Vous pouvez utiliser ces informations d’identification pour accéder aux machines virtuelles à des fins de dépannage.

Une fois la commande `pcs` exécutée, l’URL de votre nouvel accélérateur de solution s’affiche. La commande `pcs` crée également un fichier `{deployment-name}-output.json` contenant des informations telles que le nom de l’IoT Hub qui l’a créé.

Pour plus d’informations sur les paramètres de la ligne de commande, exécutez :

```cmd/sh
pcs -h
```

Pour plus d’informations sur l’interface CLI, consultez [How to use the CLI](https://github.com/Azure/pcs-cli/blob/master/README.md) (Comment utiliser l’interface CLI).

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide pratique, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurer l’accélérateur de solution
> * Déployer l’accélérateur de solution
> * Établir une connexion à l’accélérateur de solution

La solution de supervision à distance étant déployée, l’étape suivante consiste à [explorer les fonctionnalités du tableau de bord des solutions](./quickstart-remote-monitoring-deploy.md).

<!-- Next how-to guides in the sequence -->
