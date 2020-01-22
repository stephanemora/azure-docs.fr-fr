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
ms.openlocfilehash: 7d7b9f847cdcc4ab4b1ff065425eebe07fb4d888
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75773944"
---
1. Dans la page **Créer un peering**, sous l’onglet **Configuration**, remplissez les champs comme indiqué ci-dessous.

    > [!div class="mx-imgBorder"]
    > ![Configuration de peering - Exchange](../media/setup-exchange-conf-tab.png)

    * Dans **Type d’appairage**, sélectionnez *Exchange*.
    * Sélectionnez **Gratuit de base** pour le *SKU*.
    * Choisissez l’emplacement **Metro** comme emplacement où vous souhaitez convertir le peering en ressource Azure. Si vous avez des connexions de peering avec Microsoft dans l’emplacement **Metro** sélectionné qui ne sont pas converties en ressource Azure, elles seront répertoriées dans la section **Connexions de peering** comme indiqué ci-dessous. Vous pouvez maintenant convertir ces connexions de peering en ressource Azure.

        > [!div class="mx-imgBorder"]
        > ![Configuration du peering - Exchange - Connexions héritées](../media/setup-exchange-legacy-conf-tab.png)

        > [!NOTE]
        > Vous ne pouvez pas modifier les paramètres pour les connexions de peering héritées. Si vous souhaitez ajouter des connexions de peering supplémentaires avec Microsoft dans l’emplacement **Metro** sélectionné, vous pouvez le faire en cliquant sur le bouton **Créer nouveau**. Pour plus d’informations, consultez [Créer ou modifier un peering Exchange en utilisant le portail](../howto-exchange-portal.md).
        >

1. Cliquez sur **Vérifier + créer**. Notez que le portail exécute la validation de base des informations que vous avez entrées. *Exécution de la validation finale...* s’affiche dans un ruban en haut.

    > [!div class="mx-imgBorder"]
    > ![Onglet Validation d’appairage](../media/setup-direct-review-tab-validation.png)

1. Une fois que *Validation réussie* s’affiche, vérifiez vos informations et envoyez la requête en cliquant sur **Créer**. Si vous avez besoin de modifier votre requête, cliquez sur **Précédent** et répétez les étapes ci-dessus.

    > [!div class="mx-imgBorder"]
    > ![Envoi de peering](../media/setup-exchange-review-tab-submit.png)

1. Une fois que vous avez envoyé la requête, attendez qu’elle termine le déploiement. Si le déploiement échoue, contactez le [Peering Microsoft](mailto:peering@microsoft.com). Un déploiement réussi s’affiche comme ci-dessous.

    > [!div class="mx-imgBorder"]
    > ![Réussite du peering](../media/setup-direct-success.png)
