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
ms.openlocfilehash: 000971878e24c46892aaef1fa0c65237a4219883
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678511"
---
Une fois que la ressource **Peering** est déployée, vous pouvez l’afficher en procédant comme suit.

1. Accédez à **Groupes de ressources** et sélectionnez le groupe de ressources que vous avez sélectionné lors de la création de la ressource **Peering**. Utilisez la case **Filtre** si la liste des groupes de ressources est trop longue.

    > [!div class="mx-imgBorder"]
    > ![Groupes de ressources](../media/setup-direct-get-resourcegroup.png)

1. Sélectionnez la ressource **Peering** que vous avez créée.

    > [!div class="mx-imgBorder"]
    > ![Affichage des ressources de peering](../media/setup-direct-get-open.png)

1. La page **Vue d’ensemble** affiche des informations générales, comme illustré ici.

    > [!div class="mx-imgBorder"]
    > ![Peering resource Overview pane](../media/setup-exchange-get-overview.png)

1. Sur la gauche, sélectionnez **Informations ASN** pour afficher les informations envoyées lorsque vous avez créé PeerAsn.

    > [!div class="mx-imgBorder"]
    > ![Peering resource ASN information](../media/setup-direct-get-asninfo.png)

1. Sur la gauche, sélectionnez **Connexions**. En haut de l’écran, vous voyez un résumé des connexions par Peering entre votre ASN et Microsoft, sur différentes installations au sein de la zone urbaine. Vous pouvez également accéder au résumé des connexions à partir de la page **Présentation** en sélectionnant **Connexions** dans le volet central, comme illustré.

    > [!div class="mx-imgBorder"]
    > ![Peering resource Connections](../media/setup-exchange-get-connectionssummary.png)

    * **L’état de la connexion** correspond à l’état de la configuration de la connexion de Peering. Les états affichés dans ce champ suivent le diagramme d’état indiqué dans la [procédure pas à pas pour le Peering Exchange](../walkthrough-exchange-all.md).
    * L’**état de session IPv4** et l’**état de session IPv6** correspondent respectivement aux états de session BGP IPv4 et IPv6.  
    * Lorsque vous sélectionnez une ligne en haut de l’écran, la section **Connexion** dans la partie inférieure affiche les détails de chaque connexion. Sélectionnez les flèches pour développer **Configuration**, **Adresse IPv4** et **Adresse IPv6**.

    > [!div class="mx-imgBorder"]
    > ![Affichage des ressources de peering](../media/setup-exchange-get-connectionsipv4.png)
