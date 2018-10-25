---
title: Supprimer une offre ou référence SKU de la Place de marché Azure | Microsoft Docs
description: Procédure de suppression d’une offre ou d’une référence SKU.
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
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: cc172e35e8964fad3b1a1410d1f1f3240c423ab3
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48806205"
---
<a name="delete-an-offer-or-sku-from-azure-marketplace"></a>Supprimer une offre ou référence SKU de la Place de marché Azure
==========================================

Vous pouvez décider de supprimer votre offre de la Place de marché pour diverses raisons. La suppression de l’offre permet de faire en sorte que les nouveaux clients ne puissent plus acheter ou déployer votre offre, mais elle n’a aucun impact sur les clients existants.
L’arrêt de l’offre est le processus d’arrêt de l’accord de service et/ou de licence entre vos clients actuels et vous. Les instructions et les règles relatives au retrait et à la résiliation de l’offre sont régies par le [Contrat d’éditeur de la Place de marché Microsoft](http://go.microsoft.com/fwlink/?LinkID=699560) (voir Section
7) et les [stratégies de participation](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (voir la Section 6.2). Cet article traite des différents scénarios de suppression pris en charge et des étapes à suivre correspondantes.

<a name="delete-a-live-sku-from-azure-marketplace"></a>Supprimer une référence SKU en ligne de la Place de marché Azure
----------------------------------------

Vous pouvez supprimer une référence SKU en ligne de la Place de marché Azure en procédant comme suit :

1.  Connectez-vous au [portail Cloud Partner](https://cloudpartner.azure.com/).

2.  Sélectionnez votre offre dans l’onglet **Toutes les offres**.

3.  Dans le volet à gauche de l’écran, sélectionnez l’onglet **SKU**.

4.  Sélectionnez la référence que vous souhaitez supprimer et cliquez sur le bouton Supprimer de cette référence SKU.

5.  [Republiez](./cloud-partner-portal-make-offer-live-on-Azure-Marketplace.md) l’offre sur la Place de marché Azure.

Une fois que l’offre est en ligne sur la Place de marché Azure, la référence est supprimée de la Place de marché Azure et du Portail Azure.

<a name="roll-back-to-a-previous-sku-version"></a>Restaurer une version précédente de la référence SKU
----------------------------------

Vous pouvez supprimer la version actuelle d’une référence SKU en ligne de la Place de marché Azure en suivant les étapes ci-dessous. Une fois le processus terminé, la référence sera restaurée à sa version précédente.

1.  Connectez-vous au [portail Cloud Partner](https://cloudpartner.azure.com/).

2.  Sélectionnez votre offre dans l’onglet **Toutes les offres**.

3.  Dans le volet à gauche de l’écran, sélectionnez l’onglet **SKU**.

4.  Supprimez la dernière **Version du disque** de la liste des versions du disque publiées.

5.  [Republiez](./cloud-partner-portal-make-offer-live-on-Azure-Marketplace.md) l’offre sur la Place de marché Azure.

Une fois que l’offre est en ligne sur la Place de marché Azure, la version actuelle de la référence listée est supprimée de la Place de marché Azure et du Portail Azure.
La référence sera restaurée à sa version précédente.

<a name="delete-a-live-offer"></a>Supprimer une offre en direct
-------------------

Différents aspects doivent être pris en compte en cas de demande de suppression d’une offre en direct. Suivez les étapes ci-dessous pour obtenir de l’aide auprès de l’équipe de support afin de supprimer une offre en ligne de la Place de marché Azure :

1.  Créez un ticket de support à l’aide de ce [lien](https://go.microsoft.com/fwlink/?linkid=844975), ou en cliquant sur Support en haut à droite du portail CLoud Partner.

2.  Sélectionnez votre type d’offre spécifique dans la liste **Type de problème** et **Remove a published offer** (Supprimer une offre publiée) dans la liste **Catégorie**.

3.  Envoyez la demande.

L’équipe de support vous guidera tout au long du processus de suppression de l’offre.

>[!NOTE]
>La suppression d’une référence SKU/offre n’affecte pas les achats en cours de la référence SKU/offre. Ces références SKU/offres continuent à fonctionner comme avant. Elles ne peuvent toutefois pas être utilisées pour effectuer de nouveaux achats.
