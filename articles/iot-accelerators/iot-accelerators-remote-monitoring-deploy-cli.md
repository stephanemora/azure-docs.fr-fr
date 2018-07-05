---
title: Déployer la solution de surveillance à distance Java - Azure | Microsoft Docs
description: Ce tutoriel montre comment provisionner l’accélérateur de solution de surveillance à distance à l’aide de l’interface CLI.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: 736d0394b61bd2830a155d6ad714a2a8d19af82b
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37017507"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>Déployer l’accélérateur de solution de surveillance à distance à l’aide de l’interface CLI

Ce tutoriel montre comment provisionner l’accélérateur de solution de surveillance à distance. Vous déployez la solution à l’aide de l’interface CLI. Vous pouvez également déployer la solution à l’aide de l’interface utilisateur basée sur le web à partir d’azureiotsuite.com. Pour en savoir plus sur cette option, consultez [Déployer l’accélérateur de solution de surveillance à distance](iot-accelerators-remote-monitoring-deploy.md).

## <a name="prerequisites"></a>Prérequis

Pour déployer l’accélérateur de solution de surveillance à distance, vous avez besoin d’un abonnement Azure actif.

Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/).

Pour pouvoir exécuter l’interface CLI, vous devez installer [Node.js](https://nodejs.org/) sur votre ordinateur local.

## <a name="install-the-cli"></a>Installer l’interface de ligne de commande

Pour installer l’interface CLI, exécutez la commande suivante dans votre environnement de ligne de commande :

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Se connecter à l’interface CLI

Avant de pouvoir déployer l’accélérateur de solution, vous devez vous connecter à votre abonnement Azure à l’aide de l’interface CLI comme suit :

```cmd/sh
pcs login
```

Suivez les instructions à l’écran pour effectuer le processus de connexion.

## <a name="deployment-options"></a>Options de déploiement

Quand vous déployez l’accélérateur de solution, vous disposez de plusieurs options pour configurer le processus de déploiement :

| Option | Valeurs | Description |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard`, `local` | Utilisez un déploiement _basic_ à des fins de test et de démonstration ; il déploie tous les microservices sur une seule machine virtuelle. Utilisez un déploiement _standard_ à des fins de production ; il déploie les microservices sur plusieurs machines virtuelles. Un déploiement _local_ configure un conteneur Docker pour exécuter les microservices sur votre ordinateur local, et il utilise des services Azure tels que Stockage et Cosmos DB dans le cloud. |
| Runtime | `dotnet`, `java` | Sélectionne l’implémentation de langage des microservices. |

Pour découvrir comment utiliser le déploiement local, consultez [Running the remote monitoring solution locally (Exécution locale de la solution de surveillance à distance)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Running-the-Remote-Monitoring-Solution-Locally#deploy-azure-services-and-set-environment-variables).

## <a name="basic-vs-standard-deployments"></a>Déploiements de base et standard

### <a name="basic"></a>De base
Le déploiement de base est destiné à présenter la solution. Pour réduire le coût de cette démonstration, tous les microservices sont déployés sur une seule machine virtuelle ; il ne s’agit pas d’une architecture prête pour la production.

Notre option de déploiement standard doit être utilisée quand vous êtes prêt à personnaliser une architecture prête pour la production, créée pour la scalabilité et l’extensibilité.

La création d’une solution de base entraîne le provisionnement des services Azure suivants dans votre abonnement Azure au coût ci-après : 

| Count | Ressource                       | type         | Utilisation |
|-------|--------------------------------|--------------|----------|
| 1     | [Machine virtuelle Linux](https://azure.microsoft.com/services/virtual-machines/) | Standard D1 V2  | Hébergement des microservices |
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                  | Niveau Standard S1 | Gestion des appareils et communication |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)              | standard        | Stockage des données de configuration et de télémétrie d’appareil comme les règles, les alarmes et les messages |  
| 1     | [Compte Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)  | standard        | Stockage des points de contrôle streaming et de machine virtuelle |
| 1     | [Application Web](https://azure.microsoft.com/services/app-service/web/)        |                 | Hébergement de l’application web frontend |

### <a name="standard"></a>standard
Le déploiement standard est un déploiement prêt pour la production, qu’un développeur peut personnaliser et étendre pour répondre à ses besoins. Pour des raisons de fiabilité et de scalabilité, les microservices de l’application sont créés en tant que conteneurs Docker et déployés en utilisant un orchestrateur ([Kubernetes](https://kubernetes.io/) est choisi par défaut). L’orchestrateur est responsable du déploiement, de la mise à l’échelle et de la gestion de l’application.

La création d’une solution standard entraîne le provisionnement des services Azure suivants dans votre abonnement Azure au coût ci-après :

| Count | Ressource                                     | Référence (SKU) / Taille      | Utilisation |
|-------|----------------------------------------------|-----------------|----------|
| 4     | [Machines virtuelles Linux](https://azure.microsoft.com/services/virtual-machines/)   | Standard D2 V2  | 1 maître et 3 agents pour l’hébergement des microservices avec redondance |
| 1     | [Azure Container Service](https://azure.microsoft.com/services/container-service/) |                 | Orchestrateur [Kubernetes](https://kubernetes.io) |
| 1     | [Azure IoT Hub][https://azure.microsoft.com/services/iot-hub/]                     | Niveau Standard S2 | Gestion des appareils, commande et contrôle |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)                 | standard        | Stockage des données de configuration et de télémétrie d’appareil comme les règles, les alarmes et les messages |
| 5     | [Comptes de stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | standard        | 4 pour le stockage de machine virtuelle et 1 pour les points de contrôle streaming |
| 1     | [App Service](https://azure.microsoft.com/services/app-service/web/)             | S1 Standard     | Application Gateway via SSL |

> Les tarifs de ces services sont disponibles [ici](https://azure.microsoft.com/pricing). Les quantités utilisées et la facturation détaillée de votre abonnement sont disponibles dans le [portail Azure](https://portal.azure.com/).

## <a name="deploy-the-solution-accelerator"></a>Déployer l’accélérateur de solution

### <a name="example-deploy-net-version"></a>Exemple : déployer la version .NET

L’exemple suivant montre comment déployer la version .NET de base de l’accélérateur de solution de surveillance à distance :

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>Exemple : déployer la version Java

L’exemple suivant montre comment déployer la version Java standard de l’accélérateur de solution de surveillance à distance :

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>options de la commande pcs

Quand vous exécutez la commande `pcs` pour déployer une solution, vous êtes invité à indiquer les éléments suivants :

- Un nom pour votre solution. Ce nom doit être unique.
- Sélectionnez l’abonnement Azure à utiliser.
- Un emplacement.
- Les informations d’identification des machines virtuelles qui hébergent les microservices. Vous pouvez utiliser ces informations d’identification pour accéder aux machines virtuelles à des fins de dépannage.

Quand la commande `pcs` se termine, elle affiche l’URL du nouveau déploiement de l’accélérateur de solution. La commande `pcs` crée également un fichier `{deployment-name}-output.json` avec des informations supplémentaires telles que le nom du hub IoT qui a été automatiquement provisionné.

Pour plus d’informations sur les paramètres de la ligne de commande, exécutez :

```cmd/sh
pcs -h
```

Pour plus d’informations sur l’interface CLI, consultez [How to use the CLI](https://github.com/Azure/pcs-cli/blob/master/README.md) (Comment utiliser l’interface CLI).

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Configurer l’accélérateur de solution
> * Déployer l’accélérateur de solution
> * Établir une connexion à l’accélérateur de solution

La solution de surveillance à distance étant déployée, l’étape suivante consiste à [explorer les fonctionnalités du tableau de bord des solutions](./iot-accelerators-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->
