---
title: Fichier Include
titleSuffix: Azure
description: Fichier Include
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 16909e0a5d59b0ae019d91aad00e0168b0c5f433
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75773900"
---
1. Dans la page **Créer un peering**, sous l’onglet **Configuration**, remplissez les champs comme indiqué ci-dessous.

    > [!div class="mx-imgBorder"]
    > ![Configuration de peering - Direct](../media/setup-direct-conf-tab.png)

    * Dans **Type d’appairage**, sélectionnez *Direct*.
    * Dans **Réseau Microsoft**, choisissez *AS8075*. Ne sélectionnez pas ASN 8069. Il est réservé aux applications spéciales et utilisé uniquement par [Microsoft Peering](mailto:peering@microsoft.com).
    * Sélectionnez **Gratuit de base** pour le *SKU*. Ne sélectionnez pas *Premium gratuit*, car il est réservé aux applications spéciales.
    * Choisissez l’emplacement **Metro** comme emplacement où vous souhaitez convertir le peering en ressource Azure. Si vous avez des connexions de peering avec Microsoft dans l’emplacement **Metro** sélectionné qui ne sont pas converties en ressource Azure, elles seront répertoriées dans la section **Connexions d’appairage** comme indiqué ci-dessous. Vous pouvez maintenant convertir ces connexions de peering en ressource Azure.

        > [!div class="mx-imgBorder"]
        > ![Configuration de peering - direct - connexions héritées](../media/setup-directlegacy-conf-tab.png)

1. Si vous devez mettre à jour la bande passante, cliquez sur le bouton Modifier d’une ligne, comme indiqué ci-dessous, pour modifier les paramètres de connexion.

    > [!div class="mx-imgBorder"]
    > ![Configuration de peering - modification directe](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > Si vous souhaitez ajouter des connexions de peering supplémentaires avec Microsoft dans l’emplacement **Metro** sélectionné, vous pouvez le faire en cliquant sur le bouton **Créer nouveau**. Pour plus d’informations, consultez [Créer ou modifier un peering direct en utilisant le portail](../howto-direct-portal.md).
    >

1. Cliquez sur **Vérifier + créer**. Notez que le portail exécute la validation de base des informations que vous avez entrées. *Exécution de la validation finale...* s’affiche dans un ruban en haut.

    > [!div class="mx-imgBorder"]
    > ![Onglet Validation d’appairage](../media/setup-direct-review-tab-validation.png)

1. Une fois que *Validation réussie* s’affiche, vérifiez vos informations et envoyez la requête en cliquant sur **Créer**. Si vous avez besoin de modifier votre requête, cliquez sur **Précédent** et répétez les étapes ci-dessus.

    > [!div class="mx-imgBorder"]
    > ![Envoi de peering](../media/setup-direct-review-tab-submit.png)

1. Une fois que vous avez envoyé la requête, attendez qu’elle termine le déploiement. Si le déploiement échoue, contactez le [Peering Microsoft](mailto:peering@microsoft.com). Un déploiement réussi s’affiche comme ci-dessous.

    > [!div class="mx-imgBorder"]
    > ![Réussite du peering](../media/setup-direct-success.png)
