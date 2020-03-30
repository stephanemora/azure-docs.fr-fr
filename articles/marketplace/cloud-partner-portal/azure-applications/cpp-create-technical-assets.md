---
title: Créer des ressources techniques d’application Azure | Place de marché  Azure
description: Créez les ressources techniques pour une offre d’application Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: dsindona
ms.openlocfilehash: 041b2133ed63a906d3fea3ab67890a0057151b9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285263"
---
# <a name="prepare-your-azure-application-technical-assets"></a>Préparer les ressources techniques de votre application Azure

Cet article décrit les ressources nécessaires à la préparation des ressources techniques pour votre offre d’application Azure.

## <a name="before-you-begin"></a>Avant de commencer

Regardez la vidéo suivante, [Création de modèles de solutions et d’applications managées pour la Place de marché Azure](https://channel9.msdn.com/Events/Build/2018/BRK3603), un aperçu de la façon de créer un modèle Azure Resource Manager pour définir une solution d'application Azure, puis de publier l'offre d'application sur la Place de marché Azure Marketplace.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


Examinez la documentation suivante relative aux applications Azure, qui fournit des démarrages rapides, des tutoriels et des exemples.

- [Comprendre les modèles Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
- Guides de démarrage rapide :

  - [Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/)
  - [Modèles de démarrage rapide Azure sur GitHub](https://github.com/azure/azure-quickstart-templates)
  - [Publier une définition d’application](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
  - [Déployer une application de catalogue de services](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

  
- Tutoriels :

  - [Créer les fichiers de définition](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
  - [Publier l’application de la Place de marché](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

  - Exemples :

    - [Azure CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    - [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    - [Solutions d’applications managées](https://docs.microsoft.com/azure/managed-applications/sample-projects)

## <a name="fundamental-technical-knowledge"></a>Connaissances techniques fondamentales

Concevoir, créer et tester ces ressources demande du temps et des connaissances techniques sur la plateforme Azure et les technologies utilisées pour créer l’offre.

Votre équipe d’ingénierie doit connaître les technologies Microsoft suivantes :

- Connaissances de base des [services Azure](https://azure.microsoft.com/services/)
- Comment [concevoir et créer des applications Azure](https://azure.microsoft.com/solutions/architecture/)
- Expérience de travail avec les [machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/), [Stockage Azure](https://azure.microsoft.com/services/?filter=storage) et [Mise en réseau Azure](https://azure.microsoft.com/services/?filter=networking)
- Expérience de travail avec [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Expérience de travail avec [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Outils suggérés

Choisissez un ou plusieurs des environnements de scripts suivants pour faciliter la gestion de votre application Azure :

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [Azure CLI](https://docs.microsoft.com/cli/azure)

Nous vous recommandons d’ajouter les outils suivants à votre environnement de développement :

- [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Visual Studio Code](https://code.visualstudio.com/) avec les extensions suivantes :

  - Extension : [Outils Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Extension : [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Extension : [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Nous vous suggérons également d’examiner les outils disponibles sur la page [Outils de développement Azure](https://azure.microsoft.com/tools/) et, si vous utilisez Visual Studio, sur [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="next-steps"></a>Étapes suivantes

[Créer une offre d’application Azure](./cpp-create-offer.md)

