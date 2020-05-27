---
title: Concepts techniques pour les offres Azure Container – Place de marché commerciale Microsoft
description: Ressources et conseils techniques pour vous aider à configurer une offre de conteneur sur la Place de marché Azure.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 87c915330fdc0d59d0d32c0747c7dc7647c342ba
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83700983"
---
# <a name="create-an-azure-container-offer"></a>Créer une offre de conteneur Azure

Cet article fournit des ressources et recommandations techniques pour vous aider à créer une offre de conteneur sur la Place de marché Azure.

## <a name="before-you-begin"></a>Avant de commencer

Pour accéder à des guides de démarrage rapide, didacticiels et autres exemples, consultez la [Documentation sur Azure Container Instances](https://docs.microsoft.com/azure/container-instances).

## <a name="fundamental-technical-knowledge"></a>Connaissances techniques fondamentales

Concevoir, créer et tester ces ressources demande du temps et des connaissances techniques sur la plateforme Azure et les technologies utilisées pour créer l’offre.

En plus du domaine de votre solution, votre équipe d'ingénierie doit connaître les technologies Microsoft suivantes :

- Connaissances de base des [services Azure](https://azure.microsoft.com/services/)
- Comment [concevoir et créer des applications Azure](https://azure.microsoft.com/solutions/architecture/)
- Expérience de travail avec les [machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/), [Stockage Azure](https://azure.microsoft.com/services/?filter=storage) et [Mise en réseau Azure](https://azure.microsoft.com/services/?filter=networking)
- Expérience de travail avec [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Expérience de travail avec [JSON](https://www.json.org/).

## <a name="suggested-tools"></a>Outils suggérés

Choisissez l'un des environnements de script suivants, ou les deux, pour vous aider à gérer votre image conteneur :

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-3.7.0&viewFallbackFrom=azps-3.6.1)
- [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

Nous vous recommandons d'ajouter les outils suivants à votre environnement de développement :

- [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - Extension : [Outils Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Extension : [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Extension : [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json).

Examinez les outils disponibles sur la page [Outils de développement Azure](https://azure.microsoft.com/). Si vous utilisez Visual Studio, passez en revue les outils disponibles sur [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Créer l’image conteneur

Pour plus d'informations, reportez-vous aux tutoriels suivants :

- [Tutoriel : Créer une image conteneur à déployer sur Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
- [Tutoriel : Générer et déployer des images conteneurs dans le cloud avec Azure Container Registry Tasks](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task).

## <a name="next-steps"></a>Étapes suivantes

- [Créez votre offre de conteneur](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer).
