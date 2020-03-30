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
ms.openlocfilehash: 2e8938f270ed175c687d975b0b248275ad92f8e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75773904"
---
1. Accédez à **Groupes de ressources**, puis cliquez sur le groupe de ressources que vous avez sélectionné lors de la création de la ressource **Peering**. Vous pouvez utiliser le champ *Filtre* si vous avez trop de groupes de ressources.

    > [!div class="mx-imgBorder"]
    > ![Groupe de ressources de peering](../media/setup-direct-get-resourcegroup.png)

1. Cliquez sur la ressource **Peering** que vous avez créée.

    > [!div class="mx-imgBorder"]
    > ![Affichage des ressources de peering](../media/setup-direct-get-open.png)

1. La page **Présentation** affiche des informations de haut niveau. Observez les informations mises en surbrillance ci-dessous.

    > [!div class="mx-imgBorder"]
    > ![Affichage des ressources de peering](../media/setup-direct-get-overview.png)

1. Sur la gauche, cliquez sur **Informations ASN** pour afficher les informations envoyées lors de la création de PeerAsn

    > [!div class="mx-imgBorder"]
    > ![Affichage des ressources de peering](../media/setup-direct-get-asninfo.png)

1. Sur la gauche, cliquez sur **Connexions**. Regardez en haut un résumé des connexions par peering entre votre ASN et Microsoft, sur différentes installations au sein de la métro. Vous pouvez également accéder au résumé des connexions à partir de la page **Présentation**, en cliquant sur **Connexions** dans le volet central comme indiqué ci-dessus.

    > [!div class="mx-imgBorder"]
    > ![Affichage des ressources de peering](../media/setup-direct-get-connectionssummary.png)

    * L’**état de la connexion** correspond à l’état de la configuration des connexions par Peering. Les états affichés dans ce champ suivent le diagramme d’état indiqué dans [Procédure pas à pas pour le Peering direct](../walkthrough-direct-all.md)
    * L’**état de session IPv4** et l’**état de session IPv6** correspondent respectivement aux états de session BGP IPv4 et IPv6.  
    * Lorsque vous sélectionnez une ligne en haut, la section ***Connexion*** dans la partie inférieure affiche les détails de chaque connexion. Vous pouvez cliquer sur les flèches pour développer les sous-sections ***Configuration***, ***Adresse IPv4*** et ***Adresse IPv6***.
