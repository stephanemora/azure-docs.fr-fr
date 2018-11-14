---
title: Créer des ressources techniques d’image conteneur | Microsoft Docs
description: Créer les ressources techniques pour un conteneur Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pbutlerm
ms.openlocfilehash: 56de300a95d750957494865f9871127b2a9c8283
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2018
ms.locfileid: "50980173"
---
# <a name="prepare-your-container-technical-assets"></a>Préparer vos ressources techniques de conteneur

Cet article décrit les étapes et les conditions requises pour configurer une offre de conteneur de la Place de marché Azure.

## <a name="before-you-begin"></a>Avant de commencer

Examinez la documentation [Azure Container Instances](https://docs.microsoft.com/azure/container-instances), qui fournit des démarrages rapides, des tutoriels et des exemples.

## <a name="fundamental-technical-knowledge"></a>Connaissances techniques fondamentales

Concevoir, créer et tester ces ressources demande du temps et des connaissances techniques sur la plateforme Azure et les technologies utilisées pour créer l’offre.
 
En plus de votre domaine de solution, votre équipe d’ingénierie doit connaître les technologies Microsoft suivantes :

-   Connaissances de base des [services Azure](https://azure.microsoft.com/services/) 
-   Comment [concevoir et créer des applications Azure](https://azure.microsoft.com/solutions/architecture/)
-   Expérience de travail avec les [machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/), [Stockage Azure](https://azure.microsoft.com/services/?filter=storage) et [Mise en réseau Azure](https://azure.microsoft.com/services/?filter=networking)
-   Expérience de travail avec [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
-   Expérience de travail avec [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Outils suggérés

Choisissez un ou plusieurs des environnements de scripts suivants pour faciliter la gestion de votre image conteneur :

-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure)

En outre, nous vous recommandons d’ajouter les outils suivants à votre environnement de développement :

-   [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   Extension : [Outils Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Extension : [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Extension : [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Nous vous suggérons également d’examiner les outils disponibles sur la page [Outils de développement Azure](https://azure.microsoft.com/tools/) et, si vous utilisez Visual Studio, sur [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Créer l’image conteneur

- Créez et configurez le disque dur virtuel (VHD) pour votre machine virtuelle de conteneur. Ce disque dur virtuel contient le système d’exploitation (Windows, Linux ou Ubuntu) du conteneur. Des disques de données supplémentaires peuvent être nécessaires.
- Configurez le système d’exploitation de la machine virtuelle, la taille de la machine virtuelle, les ports à ouvrir et les disques de données attachés.
- Installez l’application et les autres logiciels requis pour votre offre. Par exemple : le logiciel de base de données, les logiciels tiers ou une application personnalisée.

## <a name="next-steps"></a>Étapes suivantes

[Créer votre offre de conteneur](./cpp-create-offer.md)