---
title: Fichier include
titleSuffix: Azure
description: Fichier include
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 79cf4b2edd1a25df427cf15f9ee7f2f331ebd2df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75773768"
---
1. Cliquez sur **Créer une ressource** > **Afficher tout**.

    > [!div class="mx-imgBorder"]
    > ![Recherchez le peering](../media/setup-seeall.png)

1. Recherchez *Peering* dans la barre de recherche et saisissez *Entrer* à l’aide du clavier. À partir des résultats, cliquez sur **Appairer** la ressource.

    > [!div class="mx-imgBorder"]
    > ![Lancer le peering](../media/setup-launch.png)

1. Une fois le **peering** lancé, passez en revue la page pour en comprendre les détails. Lorsque vous êtes prêt, cliquez sur **Créer**.

    > [!div class="mx-imgBorder"]
    > ![Créer un peering](../media/setup-create.png)

1. Sur la page **Créer un peering**, sous l’onglet **Informations de base**, remplissez les champs comme indiqué ci-dessous.

    > [!div class="mx-imgBorder"]
    > ![Informations de base sur le peering](../media/setup-basics-tab.png)

    * Choisissez votre **abonnement** Azure.
    * Pour le **groupe de ressources**, sélectionnez un groupe de ressources existant dans la liste déroulante ou créez un nouveau groupe en sélectionnant **Créer un nouveau groupe**. Dans cet exemple, ,nous créons un nouveau groupe de ressources.
    * Le **nom** correspond au nom de la ressource et vous pouvez le choisir librement.
    * La **région** est sélectionnée automatiquement si vous avez choisi un groupe de ressources existant dans l’étape ci-dessus. Si vous avez choisi de créer un nouveau groupe de ressources, vous devez également choisir la région Azure dans laquelle vous souhaitez que la ressource réside. USA Est

        > [!NOTE]
        > La région dans laquelle le groupe de ressources réside est indépendante de l’emplacement où vous souhaitez créer le peering avec Microsoft. Toutefois, il est recommandé d’organiser vos ressources de peering dans des groupes de ressources qui résident dans les régions Azure les plus proches. Par exemple : pour le peering dans Ashburn, vous pouvez créer un groupe de ressources dans *USA Est* ou *USA Est 2*

    * Choisissez votre ASN dans le champ **ASN de pair**.

        > [!IMPORTANT]
        > * Vous pouvez uniquement choisir un ASN avec un état de validation « approuvé » avant de soumettre une demande de peering. Si vous venez d’envoyer votre demande PeerAsn, patientez environ 12 heures pour que l’association ASN soit « approuvée ». Si l’ASN que vous sélectionnez est en attente de validation, un message d’erreur s’affiche. 
        > * Si vous ne voyez pas l’ASN que vous devez choisir, vérifiez que vous avez bien sélectionné le bon abonnement. Si c’est le cas, vérifiez si vous avez déjà créé PeerAsn à l’aide de [l’association de l’ASN de pair à un abonnement Azure](../howto-subscription-association-portal.md).

        > [!div class="mx-imgBorder"]
        > ![Informations de base sur le peering remplies](../media/setup-direct-basics-filled-tab.png)

    * Cliquez sur **Suivant : Configuration >** pour continuer.
