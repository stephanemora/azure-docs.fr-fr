---
title: Supprimer des offres d’une place de marché - Place de marché Microsoft Azure | Microsoft Docs
description: Supprimer des offres des places de marché Microsoft Azure et AppSource à l’aide du Portail Cloud Partner
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: pbutlerm
ms.openlocfilehash: 1d5d02d65dd3dcf5978639818fba4ebe36ffaaff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60825147"
---
# <a name="delete-azure-marketplace-and-appsource-offers-or-skus"></a>Supprimer des références SKU ou des offres de la Place de marché Microsoft Azure et d’AppSource

Pour diverses raisons, vous pouvez décider de retirer votre offre de sa place de marché Microsoft, et ce sous deux formes :

- La *suppression de l’offre* permet de faire en sorte que les nouveaux clients ne puissent plus acheter ou déployer votre offre, mais elle n’a aucun impact sur les clients existants, que vous devez prendre en charge en fonction de votre contrat de licence et de la législation applicable. 
- L’*arrêt de l’offre* est le processus d’arrêt de l’accord de service et/ou de licence entre vos clients actuels et vous. 

Des conseils et des stratégies relatives à le pour offre de suppression et arrêt sont régies par [contrat d’éditeur place de marché Microsoft](https://go.microsoft.com/fwlink/?LinkID=699560) et [politiques](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (section [offre suspension et retrait](https://docs.microsoft.com/en-us/legal/marketplace/participation-policy#offering-suspension-and-removal)). 

Cet article traite des différents scénarios de suppression pris en charge et des étapes à suivre correspondantes.  

> [!NOTE]
> Vous pouvez supprimer une offre qui n’a pas été publiée en sélectionnant simplement le bouton **Delete** dans la barre d’outils de l’onglet **Editor**.


## <a name="delete-a-published-sku-from-the-azure-marketplace"></a>Supprimer une référence SKU publiée de la Place de marché Microsoft Azure

Vous pouvez supprimer une référence SKU publiée de la Place de marché Microsoft Azure en effectuant les étapes suivantes :

1.  Connectez-vous au [portail Cloud Partner](https://cloudpartner.azure.com/).
2.  Dans la page **All offers**, sélectionnez votre offre.  Votre offre doit apparaître sous l’onglet **Editor**.
3.  Dans la barre d’outils de gauche, sélectionnez l’onglet **SKUs** (Références SKU). 
4.  Sélectionnez la référence à supprimer, puis cliquez sur le bouton **Delete**.
5.  [Republiez](./cpp-publish-offer.md) l’offre sur la Place de marché Azure.

Une fois que l’offre modifiée est publiée sur la Place de marché Microsoft Azure, la référence SKU sélectionnée n’apparaît plus dans celle-ci, ni dans le portail Azure.


## <a name="roll-back-to-a-previous-sku-version"></a>Restaurer une version précédente de la référence SKU

Vous pouvez supprimer la version actuelle d’une référence SKU publiée de la Place de marché Azure en effectuant les étapes suivantes. Une fois le processus terminé, la référence est restaurée à sa version précédente.

1. Connectez-vous au [Portail Cloud Partner](https://cloudpartner.azure.com/).
2. Dans la page **All offers**, sélectionnez votre offre.  Votre offre doit apparaître sous l’onglet **Editor**.
3. Dans la barre d’outils de gauche, sélectionnez l’onglet **SKUs**. 
4. Supprimez la dernière version de la ressource de solution associée de la liste des versions disque.  Selon le type d’offre, ce champ peut être **Disk Version**, **Package Versions** ou une ressource similaire. 
5. [Republiez](./cpp-publish-offer.md) l’offre sur la Place de marché Azure.

Une fois que l’offre modifiée est publiée sur la Place de marché Microsoft Azure, la version actuelle de la référence SKU listée n’apparaît plus dans celle-ci, ni dans le portail Azure.  La version précédente de la référence SKU est restaurée.


## <a name="delete-a-live-offer"></a>Supprimer une offre en direct

Différents aspects d’ordre procédural, métier et juridique régissent la suppression d’une offre en ligne. Suivez les étapes suivantes pour obtenir de l’aide auprès de l’équipe de support afin de supprimer une offre en ligne de la Place de marché Microsoft Azure :

1.  Créez un ticket de support à l’aide de la page [Create an incident](https://go.microsoft.com/fwlink/?linkid=844975) ou en cliquant sur **Support** en haut à droite du [Portail Cloud Partner](https://cloudpartner.azure.com/).

2.  Sélectionnez votre type d’offre spécifique dans la liste **Problem type** et sélectionnez **Remove a published offer** (Supprimer une offre publiée) dans la liste **Category**.

3.  Envoyez la demande.

L’équipe de support vous guide tout au long du processus de suppression de l’offre.

> [!NOTE]
> La suppression d’une offre (ou référence SKU) n’a aucune répercussion sur les acquisitions actuelles de cette offre (ou référence SKU). Ces acquisitions continueront à fonctionner comme avant. Toutefois, les offres ou références SKU supprimées ne sont pas disponibles pour des acquisitions futures.


## <a name="next-steps"></a>Étapes suivantes

Une fois que vous êtes familiarisé avec les opérations de base utilisées pour gérer les offres, vous êtes prêt à créer une instance d’une [offre de place de marché](../cpp-marketplace-offers.md) Microsoft.
