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
ms.openlocfilehash: cd51eca0ea4563e1b56f74677df0829669d9e177
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75773724"
---
1. Dans la page **Créer un Peering**, sous l’onglet **Configuration**, remplissez les champs comme indiqué ci-dessous.

    > [!div class="mx-imgBorder"]
    > ![Configuration du Peering – Exchange](../media/setup-exchange-conf-tab.png)

    * Dans **Type de Peering**, sélectionnez *Exchange*.
    * Sélectionnez **Gratuit de base** pour la *SKU*.
    * Choisissez **Métro** comme emplacement où vous souhaitez configurer le Peering.

        > [!NOTE]
        > Si vous avez déjà des connexions Peering avec Microsoft à l’emplacement **Metro** sélectionné et que vous utilisez le portail pour la première fois afin de configurer le Peering à cet emplacement, vos connexions Peering existantes sont répertoriées dans la section **Connexions Peering** comme indiqué ci-dessous. Microsoft convertira automatiquement ces connexions Peering en ressource Azure afin que vous puissiez toutes les gérer avec les nouvelles connexions de façon centralisée. Pour plus d’informations, consultez [Convertir un Peering Exchange existant en ressource Azure à l’aide du portail](../howto-legacy-exchange-portal.md).
        >

1. Sous **Connexions Peering**, cliquez sur **Créer nouveau** afin d’ajouter une ligne pour chaque nouvelle connexion que vous souhaitez configurer.

    * Pour configurer/modifier les paramètres de connexion, cliquez sur le bouton Modifier d’une ligne.

        > [!div class="mx-imgBorder"]
        > ![Modification de la configuration du Peering – Exchange](../media/setup-exchange-conf-tab-edit.png)

    * Pour supprimer une ligne, cliquez sur le bouton **…**  > **Supprimer**.

        > [!div class="mx-imgBorder"]
        > ![Modification de la configuration du Peering – Exchange](../media/setup-exchange-conf-tab-delete.png)

    * Vous devez fournir tous les paramètres pour une connexion, comme indiqué ci-dessous.

         > [!div class="mx-imgBorder"]
         > ![Configuration du Peering – Connexion Exchange](../media/setup-exchange-conf-tab-connection.png)

        1. Sélectionnez l’**installation Peering** où la connexion doit être configurée.
        1. Dans les champs **adresse IPv4** et **adresse IPv6**, entrez respectivement l’adresse IPv4 et IPv6 qui serait configurée dans les routeurs Microsoft à l’aide de la commande voisin.
        1. Entrez le nombre de préfixes IPv4 et IPv6 que vous publierez dans les champs **Nombre maximum d’adresses IPv4 publiées** et **Nombre maximum d’adresses IPv6 publiées** respectivement.
        1. Cliquez sur **OK** pour enregistrer vos paramètres de connexion.

1. Répétez l’étape ci-dessus pour ajouter d’autres connexions dans toute installation où Microsoft est colocalisé avec votre réseau, au sein de la zone **Métro** sélectionnée précédemment.

1. Après avoir ajouté toutes les connexions requises, cliquez sur **Vérifier + créer**.

    > [!div class="mx-imgBorder"]
    > ![Configuration du Peering – Onglet Final](../media/setup-exchange-conf-tab-final.png)

1. Notez que le portail exécute la validation de base des informations que vous avez entrées. *Exécution de la validation finale…* s’affiche dans un ruban en haut de l’écran.

    > [!div class="mx-imgBorder"]
    > ![Onglet de validation du Peering](../media/setup-direct-review-tab-validation.png)

1. Une fois que *Validation réussie* s’affiche, vérifiez vos informations et envoyez la requête en cliquant sur **Créer**. Si vous avez besoin de modifier votre requête, cliquez sur **Précédent** et répétez les étapes ci-dessus.

    > [!div class="mx-imgBorder"]
    > ![Envoi du Peering](../media/setup-exchange-review-tab-submit.png)

1. Une fois que vous avez envoyé la requête, attendez qu’elle termine le déploiement. Si le déploiement échoue, contactez l’assistance [Peering Microsoft](mailto:peering@microsoft.com). Un déploiement réussi s’affiche comme ci-dessous.

    > [!div class="mx-imgBorder"]
    > ![Peering réussi](../media/setup-direct-success.png)
