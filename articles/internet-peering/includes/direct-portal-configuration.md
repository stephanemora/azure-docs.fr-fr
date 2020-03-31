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
ms.openlocfilehash: 2a42ba8809e6895c9eea9f8762513b7fcaa9eb3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75773964"
---
1. Dans la page **Créer un peering**, sous l’onglet **Configuration**, remplissez les champs comme indiqué ci-dessous.

    * Dans **Type d’appairage**, sélectionnez *Direct*.
    * Dans **Réseau Microsoft**, choisissez *AS8075*. Ne créez pas de peering avec ASN 8069. Il est réservé aux applications spéciales et utilisé uniquement par [Microsoft Peering](mailto:peering@microsoft.com).
    * Sélectionnez **Gratuit de base** pour la *SKU*. Ne sélectionnez pas *Premium gratuit*, car il est réservé aux applications spéciales.
    * Choisissez **Métro** comme emplacement où vous souhaitez configurer le Peering.

        > [!NOTE]
        > Si vous avez déjà des connexions Peering avec Microsoft à l’emplacement **Metro** sélectionné et que vous utilisez le portail pour la première fois afin de configurer le Peering à cet emplacement, vos connexions Peering existantes sont répertoriées dans la section **Connexions Peering** comme indiqué ci-dessous. Microsoft convertira automatiquement ces connexions de peering en ressource Azure afin que vous puissiez les gérer avec les nouvelles connexions de façon centralisée. Pour plus d’informations, consultez [Convertir un peering direct existant en ressource Azure en utilisant le portail](../howto-legacy-direct-portal.md).
        >

1. Sous **Connexions Peering**, cliquez sur **Créer nouveau** afin d’ajouter une ligne pour chaque nouvelle connexion que vous souhaitez configurer.

    * Pour configurer/modifier les paramètres de connexion, cliquez sur le bouton Modifier d’une ligne.

        > [!div class="mx-imgBorder"]
        > ![Configuration de peering - modification directe](../media/setup-direct-conf-tab-edit.png)
    
    * Pour supprimer une ligne, cliquez sur le bouton **…**  > **Supprimer**.

        > [!div class="mx-imgBorder"]
        > ![Configuration de peering - modification directe](../media/setup-direct-conf-tab-delete.png)

    * Vous devez fournir tous les paramètres pour une connexion, comme indiqué ci-dessous.

         > [!div class="mx-imgBorder"]
         > ![Configuration de peering - connexion directe](../media/setup-direct-conf-tab-connection.png)

        1. Sélectionnez l’**installation Peering** où la connexion doit être configurée.
        1. **Session Address Provider** (Fournisseur d’adresses de session) est utilisé pour déterminer qui fournit le sous-réseau nécessaire à l'établissement d'une session BGP entre votre réseau et Microsoft. Si vous êtes en mesure de fournir un sous-réseau, choisissez *Homologue*. Sinon, choisissez **Microsoft**. [Peering Microsoft](mailto:peering@microsoft.com) vous contactera alors. Notez qu’avec le choix de cette option Microsoft prendra plus de temps à traiter la demande de peering. Dans certains cas, Microsoft peut ne pas être en mesure de fournir des sous-réseaux, ce qui entraînera un refus de la requête.
        1. Si vous avez choisi **Fournisseur d’adresses de session** en tant qu’*Homologue*, entrez l’adresse IPv4 et l’adresse IPv6 ainsi que le masque de préfixe dans les champs **Session IPv4 Prefix** (Préfixe IPv4 de session) et **Session IPv6 Prefix** (Préfixe IPv6 de session) respectivement.
        1. Entrez le nombre de préfixes IPv4 et IPv6 que vous publierez dans les champs **Nombre maximum d’adresses IPv4 publiées** et **Nombre maximum d’adresses IPv6 publiées** respectivement.
        1. Ajustez le curseur **Bande passante totale** pour refléter la bande passante de la connexion.
        1. Cliquez sur **OK** pour enregistrer vos paramètres de connexion.

1. Répétez l’étape ci-dessus pour ajouter d’autres connexions dans toute installation où Microsoft est colocalisée avec votre réseau, au sein de la zone **Métro** sélectionnée précédemment.

1. Après avoir ajouté toutes les connexions requises, cliquez sur **Vérifier + créer**.

    > [!div class="mx-imgBorder"]
    > ![Configuration du Peering – Onglet Final](../media/setup-direct-conf-tab-final.png)

1. Notez que le portail exécute la validation de base des informations que vous avez entrées. *Exécution de la validation finale…* s’affiche dans un ruban en haut de l’écran.

    > [!div class="mx-imgBorder"]
    > ![Onglet de validation du Peering](../media/setup-direct-review-tab-validation.png)

1. Une fois que *Validation réussie* s’affiche, vérifiez vos informations et envoyez la requête en cliquant sur **Créer**. Si vous avez besoin de modifier votre requête, cliquez sur **Précédent** et répétez les étapes ci-dessus.

    > [!div class="mx-imgBorder"]
    > ![Envoi de peering](../media/setup-direct-review-tab-submit.png)

1. Une fois que vous avez envoyé la requête, attendez qu’elle termine le déploiement. Si le déploiement échoue, contactez l’assistance [Peering Microsoft](mailto:peering@microsoft.com). Un déploiement réussi présente l’apparence suivante.

    > [!div class="mx-imgBorder"]
    > ![Réussite du peering](../media/setup-direct-success.png)
