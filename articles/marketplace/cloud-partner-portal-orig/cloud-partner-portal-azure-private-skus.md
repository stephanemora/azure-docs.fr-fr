---
title: Références SKU privées | Microsoft Docs
description: Comment utiliser des références SKU privées pour gérer la disponibilité des offres.
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
ms.openlocfilehash: 28fe85c6443701d5fb40d4f90dbec9ba445a234d
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51230238"
---
<a name="private-skus"></a>Références SKU privées
============

Les références SKU privées vous permettent de restreindre la disponibilité des références SKU à des clients spécifiques. Lorsqu’une référence SKU est marquée comme privée, elle n’est pas disponible dans les catalogues publics, y compris sur la [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com) et sur le [portail Azure](http://portal.azure.com). Sur le portail Azure, seuls les clients ayant accès à la référence SKU peuvent la voir. En outre, ils reçoivent également un message qui les informe qu'ils ont accès aux offres privées.

>[!NOTE]
>Les références SKU privées doivent disposer d'un nouvel ID de référence SKU/plan unique pour éviter tout conflit avec vos références SKU publiques.

Vous pouvez utiliser des références SKU privées pour :

1.  Publier des logiciels dont vous souhaitez réserver l'accès à certains clients
2.  Publier des variantes des logiciels publics à un prix personnalisé pour des clients spécifiques
3.  Publier des variantes des logiciels publics avec une description et des conditions personnalisées (via une nouvelle offre)

Si vous souhaitez uniquement modifier le prix, vous pouvez réutiliser les disques d’une autre référence SKU de la même offre. Avec les références SKU privées, vous n’êtes pas obligé de renvoyer les disques des différentes références SKU.

<a name="mark-a-sku-private"></a>Marquer une référence SKU comme privée
---------------------

Pour marquer une référence SKU comme privée, activez l’option correspondante :

![Marquer une référence SKU comme privée](./media/cloud-partner-portal-publish-virtual-machine/markingskuprivate.png)

Vous pouvez réutiliser les disques d'une autre référence SKU et modifier la description ou la tarification. Pour réutiliser les disques, répondez **Oui** à la question « Cette référence SKU réutilise-t-elle des images provenant d’une référence SKU publique ? ».

Si la référence SKU est marquée comme privée et que l’offre comporte d’autres références SKU dotées de disques réutilisables, vous devez impérativement indiquer que la référence SKU réutilise les disques d’une autre référence SKU. Vous devez également spécifier le public cible de la référence SKU privée.

>[!NOTE]
>À l'issue de sa publication, une référence SKU publique ne peut pas être privatisée.

<a name="select-an-image"></a>Sélectionner une image
------------------

Vous pouvez fournir de nouveaux disques à la référence SKU privée ou réutiliser les disques fournis à une autre référence SKU, en vous contentant de modifier la tarification ou la description. Pour réutiliser les disques, répondez **Oui** à la question « Cette référence SKU réutilise-t-elle des images provenant d’une référence SKU publique ? ».

![Indiquer que les images sont réutilisées](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

Après avoir confirmé que la référence SKU réutilise les images d'une autre référence SKU, vous devez identifier la source des images.

Les invites de la capture d'écran suivante montrent comment identifier que la référence SKU privée réutilise les images de la référence SKU sélectionnée :

![Sélectionner une image](./media/cloud-partner-portal-publish-virtual-machine/selectimage2.png)

Lorsque vous publiez l'offre, les images de la référence SKU sélectionnée sont mises à disposition sous l'ID de la référence SKU privée avec des tarifs/conditions personnalisés. Seul le public cible voit la référence SKU privée.

Pour les mises à jour d'images, il vous suffit de mettre à jour l'image de la référence SKU sous-jacente. L'image de la référence SKU privée est alors automatiquement mise à jour. De même, si vous supprimez l'image de la référence SKU sous-jacente, elle est également supprimée de la référence SKU privée.

<a name="restricting-the-audience"></a>Restreindre le public
------------------------

Seuls les utilisateurs ciblés peuvent accéder aux offres privées et les déployer.
Nous prenons actuellement en charge le ciblage des utilisateurs à l'aide des ID d'abonnement.

Ces abonnements peuvent être entrés via un formulaire de saisie manuelle (**10 abonnements maximum**) ou en chargeant un fichier CSV (**20 000 abonnements maximum**).

Saisie manuelle pour public restreint :

![Restreindre manuellement le public](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience1.png)

Chargement d'un fichier CSV pour public restreint :

![Utiliser le fichier CSV pour restreindre le public](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience2.png)

Exemple de contenu du fichier CSV :

            Type,Id,Description
            SubscriptionId,7738d703-3135-4e8d-8b81-1e70379abd9d,Private Customer

Lorsque vous basculez du mode de saisie manuelle vers le mode de chargement du fichier CSV, ou inversement, l'ancienne liste d'ID d'abonnement avec accès à la référence SKU n'est pas conservée. Un avertissement s'affiche et la liste n'est remplacée que lors de l'enregistrement de l'offre.

<a name="previewing-private-offers"></a>Préversion des offres privées
-------------------------

Lors de l'étape de préversion/version intermédiaire, seuls les abonnements pour préversion au niveau offre ont accès à la référence SKU. Il s'agit d'une phase de test au moment de laquelle vous pouvez valider l'apparence de l'offre pour les clients ciblés, et cette phase est standard pour tous les types de publication.

Abonnements pour préversion au niveau offre pour accéder à des offres intermédiaires :

![ID d’abonnement pour version préliminaire](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

Une fois l'offre en direct, seuls les abonnements pour public restreint (entrés via le mode de saisie manuelle ou le mode CSV) pourront voir et déployer la référence SKU privée. À des fins de validation, nous vous recommandons de **toujours inclure vos propres abonnements dans le public restreint** pour la référence SKU privée.

>[!NOTE]
>À des fins de débogage, les équipes de support et d’ingénierie de Microsoft ont également accès à ces offres privées.
