---
title: Références SKU et plans privés | Place de marché Azure
description: Comment utiliser des références SKU privées pour gérer la disponibilité des offres.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: dsindona
ms.openlocfilehash: ee3ab7be4d15b13a3c0bb014a3ca4d4096299b4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280386"
---
<a name="private-skus-and-plans"></a>Références SKU et plans privés
============

Les références SKU privées vous permettent de restreindre la disponibilité des références SKU à des clients spécifiques. Lorsqu’une référence SKU est marquée comme privée, elle n’est pas disponible dans les catalogues publics, y compris sur la [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com) et sur le [portail Azure](https://portal.azure.com). Sur le portail Azure, seuls les clients ayant accès à la référence SKU peuvent la voir. En outre, ils reçoivent également un message qui les informe qu'ils ont accès aux offres privées.

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

Vous pouvez fournir de nouveaux disques à la référence SKU privée ou réutiliser les disques fournis à une autre référence SKU, en vous contentant de modifier la tarification ou la description. Pour réutiliser les disques, répondez **Oui** à la question « Cette référence SKU réutilise-t-elle une image provenant d’une référence SKU publique ? ».

![Indiquer la réutilisation des images](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

Après avoir confirmé que la référence SKU réutilise les images, sélectionnez la référence SKU source ou *de base* pour les images :

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

<a name="managing-private-audiences"></a>Gestion des audiences privées
-------------------------

**Pour mettre à jour l’audience sans avoir à republier la totalité de l’offre, vous devez apporter les modifications souhaitées à l’audience (à l’aide de l’interface utilisateur ou de l’API), puis lancer l’action « Synchroniser les audiences privées ».**

Si votre audience est composée de 10 abonnements ou moins, vous pouvez la gérer entièrement à l’aide de l’interface utilisateur du portail Cloud Partner (CPP).

Si votre audience contient plus de 10 abonnements, vous pouvez la gérer à l’aide d’un fichier CSV que vous pouvez charger sur l’interface utilisateur du portail CPP ou à l’aide de l’API.

Si vous utilisez l’API et que vous ne souhaitez pas conserver un fichier CSV, vous pouvez gérer l’audience directement à l’aide de l’API conformément aux instructions ci-dessous.

> [!NOTE]
> Utilisez l’ID d’abonnement Azure (plans et références SKU) ou l’ID de locataire (plans uniquement) pour ajouter une audience à votre offre privée.

###  <a name="managing-subscriptions-with-the-api"></a>Gestion des abonnements avec l’API

Vous pouvez utiliser l’API pour charger un fichier CSV ou gérer votre audience directement (sans utiliser de fichier CSV). En général, il vous suffit de récupérer votre offre, de mettre à jour l’objet `restrictedAudience`, puis de soumettre ces modifications à votre offre afin d’ajouter ou de supprimer des membres de l’audience.

Voici comment mettre à jour par programmation votre liste d’audiences :

1. [Récupérez les données de votre offre](cloud-partner-portal-api-retrieve-specific-offer.md) :

    ```
    GET https://cloudpartner.azure.com/api/publishers//offers/?api-version=2017-10-31&includeAllPricing=true
    ```

2. Recherchez des objets d’audience restreinte dans chaque référence SKU de l’offre à l’aide de cette requête JPath :

    ```
    $.definition.plans[*].restrictedAudience
    ```
3. Mettez à jour les objets d’audience restreinte pour votre offre.

    **Si vous avez initialement chargé la liste d’abonnements de votre offre privée à partir du fichier CSV :**

    Vos objets *restrictedAudience* ressemblent à ce qui suit.
    ```
    "restrictedAudience": {
                  "uploadedCsvUri": "{SasUrl}"
    }
    ```

    Pour chaque objet d’audience restreinte :

    a. Téléchargez le contenu de `restrictedAudience.uploadedCsvUri`. Le contenu est simplement un fichier CSV avec des en-têtes. Par exemple :

        type,id,description
        subscriptionId,541a269f-3df2-486e-8fe3-c8f9dcf28205,sub1
        subscriptionId,c0da499c-25ec-4e4b-a42a-6e75635253b9,sub2

    b. Ajoutez ou supprimez des abonnements dans le fichier CSV téléchargé en fonction des besoins.

    c. Chargez le fichier CSV mis à jour à un emplacement, comme [Stockage Blob Azure](../../storage/blobs/storage-blobs-overview.md) ou [OneDrive](https://onedrive.live.com), et créez un lien en lecture seule vers votre fichier. Il s’agit de votre nouvelle URL *SasUrl*.

    d. Mettez à jour la clé `restrictedAudience.uploadedCsvUri` avec votre nouvelle URL *SasUrl*.

    **Si vous avez entré manuellement la liste d’origine des abonnements pour votre offre privée à partir du portail Cloud Partner :**

    Vos objets *restrictedAudience* ressemblent à ce qui suit :

    ```
    "restrictedAudience": {
        "manualEntries": [{
            "type": "subscriptionId",
            "id": "541a269f-3df2-486e-8fe3-c8f9dcf28205",
            "description": "sub1"
            }, {
            "type": "subscriptionId",
            "id": "c0da499c-25ec-4e4b-a42a-6e75635253b9",
            "description": "sub2"
            }
        ]}
    ```

    a. Pour chaque objet d’audience restreinte, ajoutez ou supprimez des entrées dans la liste `restrictedAudience.manualEntries` en fonction des besoins.

4. Lorsque vous avez terminé la mise à jour de tous les objets *restrictedAudience* pour chaque référence SKU de votre offre privée, [mettez à jour l’offre](cloud-partner-portal-api-creating-offer.md) :

    ```
    PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31
    ```
    Ainsi, votre liste d’audiences mise à jour est désormais en vigueur.

<a name="previewing-private-offers"></a>Aperçu des offres privées
-------------------------

Lors de l'étape de préversion/version intermédiaire, seuls les abonnements pour préversion au niveau offre ont accès à la référence SKU. Au cours de cette phase de test, vous pouvez afficher un aperçu de l’offre telle qu’elle se présenterait à vos clients cibles.

Abonnements pour préversion au niveau offre pour accéder à des offres intermédiaires :

![ID d’abonnement pour version préliminaire](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

Une fois l'offre en direct, seuls les abonnements pour public restreint (entrés via le mode de saisie manuelle ou le mode CSV) pourront voir et déployer la référence SKU privée. À des fins de validation, nous vous recommandons de **toujours inclure vos propres abonnements dans le public restreint** pour la référence SKU privée.

>[!NOTE]
>À des fins de débogage, les équipes de support et d’ingénierie de Microsoft ont également accès à ces offres privées.
