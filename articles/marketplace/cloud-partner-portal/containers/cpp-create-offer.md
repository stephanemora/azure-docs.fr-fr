---
title: Créer une offre Azure Container | Microsoft Docs
description: Guide pratique pour publier une nouvelle offre de conteneur sur la Place de marché.
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
ms.openlocfilehash: 3b2a34a5ed2c6b57bfce0b9a4cf35cdf14cff9c9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57900078"
---
# <a name="create-a-new-container-offer-with-the-cloud-partner-portal"></a>Créer une offre de conteneur sur le Portail Cloud Partner

Cet article explique comment créer et publier une entrée d’offre de conteneur sur la Place de marché Azure. Les offres apparaissent sous la forme d’une entité distincte dans la Place de marché Microsoft Azure et sont associées à un ou plusieurs références SKU.  Une offre de conteneur se compose des regroupements de ressources et de services d’appui suivants :

|  **Groupe de ressources**   |  **Description**  |
|  ---------------   |  ---------------  |
|    Références (SKU)            |  Plus petite unité d’une offre pouvant être déployée. Plusieurs références (SKU) peuvent être associées à l’offre unique (classe de produit) . Vous pouvez utiliser des références (SKU) pour faire la distinction entre les fonctionnalités prises en charge et les modèles de facturation. |
|  Marketplace       | Contient des ressources et des spécifications marketing, juridique et de gestion des prospects.  <ul><li> Les ressources marketing incluent le nom, la description et les logos de l’offre</li> <li> Les ressources juridiques incluent une politique de confidentialité, des conditions d’utilisation et d’autres documents juridiques</li>  <li> La stratégie de gestion des prospects vous permet de spécifier comment gérer les prospects à partir du portail de l’utilisateur final de la Place de marché Microsoft Azure.</li> </ul> |
| Support            | Contient les informations de contact et de stratégie du support |


## <a name="new-offer-form"></a>Formulaire de nouvelle offre 

Connectez-vous au [Portail Cloud Partner](https://cloudpartner.azure.com/), puis sélectionnez **+ Nouvelle offre** dans la barre de menus de gauche. Dans le menu Nouvelle offre, sélectionnez **Conteneurs** afin d’afficher le formulaire **Nouvelle offre** et démarrer le processus de définition des ressources pour une nouvelle offre de conteneur.

![Sélection de l’option Conteneurs pour une nouvelle offre](./media/azure-container-offer.png)

## <a name="next-steps"></a>Étapes suivantes

La page **Nouvelle offre** correspondant aux offres de type conteneur comporte un ensemble d’onglets et de champs de formulaire permettant de créer une offre. Chacun des articles suivants explique comment utiliser l’onglet correspondant pour définir les groupes de ressources et les services d’appui de votre nouvelle offre de conteneur.

- [Onglet des paramètres de l’offre](./cpp-offer-settings-tab.md)
- [Onglet des références SKU](./cpp-skus-tab.md)
- [Onglet de la Place de marché](./cpp-marketplace-tab.md)
- [Onglet du support](./cpp-support-tab.md)
