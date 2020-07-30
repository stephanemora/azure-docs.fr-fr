---
title: Concepts techniques pour les offres Azure Container – Place de marché commerciale Microsoft
description: Ressources et conseils techniques pour vous aider à configurer une offre de conteneur sur la Place de marché Azure.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 04/09/2020
ms.openlocfilehash: 46102305e99c94fc71ad0934a2c0063fdcbeec4e
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87317788"
---
# <a name="create-an-azure-container-offer"></a>Créer une offre de conteneur Azure

Cet article fournit des ressources et recommandations techniques pour vous aider à créer une offre de conteneur sur la Place de marché Azure.

## <a name="before-you-begin"></a>Avant de commencer

Pour accéder à des guides de démarrage rapide, didacticiels et autres exemples, consultez la [Documentation sur Azure Container Instances](../../container-instances/index.yml).

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

- [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - Extension : [Outils Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Extension : [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Extension : [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json).

Examinez les outils disponibles sur la page [Outils de développement Azure](https://azure.microsoft.com/). Si vous utilisez Visual Studio, passez en revue les outils disponibles sur [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Créer l’image conteneur

Pour plus d'informations, reportez-vous aux tutoriels suivants :

- [Tutoriel : Créer une image conteneur à déployer sur Azure Container Instances](../../container-instances/container-instances-tutorial-prepare-app.md)
- [Tutoriel : Générer et déployer des images conteneurs dans le cloud avec Azure Container Registry Tasks](../../container-registry/container-registry-tutorial-quick-task.md).

## <a name="next-steps"></a>Étapes suivantes

- [Créez votre offre de conteneur](create-azure-container-offer.md).
