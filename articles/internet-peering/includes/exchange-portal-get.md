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
ms.openlocfilehash: b967c844ab145074490e931122cbe092d67de0c1
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75773740"
---
Une fois la ressource **Peering** déployée avec succès, vous pouvez l’afficher en suivant les étapes ci-dessous.

1. Accédez à **Groupes de ressources**, puis cliquez sur le groupe de ressources que vous avez sélectionné lors de la création de la ressource **Peering**. Vous pouvez utiliser le champ *Filtre* si vous avez trop de groupes de ressources.

    > [!div class="mx-imgBorder"]
    > ![Groupe de ressources de peering](../media/setup-direct-get-resourcegroup.png)

1. Cliquez sur la ressource **Peering** que vous avez créée.

    > [!div class="mx-imgBorder"]
    > ![Affichage des ressources de peering](../media/setup-direct-get-open.png)

1. La page **Présentation** affiche des informations de haut niveau. Observez les informations mises en surbrillance ci-dessous.

    > [!div class="mx-imgBorder"]
    > ![Affichage des ressources de peering](../media/setup-exchange-get-overview.png)

1. Sur la gauche, cliquez sur **Informations ASN** pour afficher les informations envoyées lors de la création de PeerAsn

    > [!div class="mx-imgBorder"]
    > ![Affichage des ressources de peering](../media/setup-direct-get-asninfo.png)

1. Sur la gauche, cliquez sur **Connexions**. Regardez en haut un résumé des connexions par peering entre votre ASN et Microsoft, sur différentes installations au sein de la métro. Vous pouvez également accéder au résumé des connexions à partir de la page **Vue d’ensemble**, en cliquant sur **Connexions** dans le volet central comme indiqué ci-dessus.

    > [!div class="mx-imgBorder"]
    > ![Affichage des ressources de peering](../media/setup-exchange-get-connectionssummary.png)

    * **L’état de la connexion** correspond à l’état de la configuration de la connexion de peering. Les états affichés dans ce champ suivent le diagramme d’état indiqué dans [Procédure pas à pas pour le Peering Exchange](../walkthrough-exchange-all.md)
    * L’**état de session IPv4** et l’**état de session IPv6** correspondent respectivement aux états de session BGP IPv4 et IPv6.  
    * Lorsque vous sélectionnez une ligne en haut, la section ***Connexion*** dans la partie inférieure affiche les détails de chaque connexion. Vous pouvez cliquer sur les flèches pour développer les sous-sections ***Configuration***, ***Adresse IPv4*** et ***Adresse IPv6***.

    > [!div class="mx-imgBorder"]
    > ![Affichage des ressources de Peering](../media/setup-exchange-get-connectionsipv4.png)
