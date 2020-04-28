---
title: Créer une ressource technique de machine virtuelle Azure
description: Cet article décrit les étapes et les conditions requises pour configurer une offre de conteneur sur la Place de marché Azure.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: e51b8c705533fd74a5e46eaa2570563fef485309
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730636"
---
# <a name="create-an-azure-vm-technical-asset"></a>Créer une ressource technique de machine virtuelle Azure

> [!IMPORTANT]
> Nous allons déplacer la gestion de vos offres Azure Container du Portail Cloud Partner vers l'Espace partenaires. Tant que vos offres ne sont pas migrées, suivez les instructions de la section [Préparer vos ressources techniques Container](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-create-technical-assets) pour que le Portail Cloud Partner gère vos offres.

Cet article décrit les étapes et les conditions requises pour configurer une offre Container sur la Place de marché Azure.

## <a name="before-you-begin"></a>Avant de commencer

Consultez [Azure Container Instances](https://docs.microsoft.com/azure/container-instances) pour accéder aux guides de démarrage rapide, tutoriels et exemples disponibles.

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

## <a name="next-step"></a>Étape suivante

- [Créez votre offre Container](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer).
