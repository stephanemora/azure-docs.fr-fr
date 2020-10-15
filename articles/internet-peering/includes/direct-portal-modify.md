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
ms.openlocfilehash: 3894bf046ed4ee3f068e43dbc5bc5b7f2a1002b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "81681071"
---
Cette section décrit comment effectuer les opérations de modification suivantes pour le Peering direct.

### <a name="add-direct-peering-connections"></a>Ajouter les connexions de Peering direct
1. Sélectionnez le bouton **+ Ajouter des connexions** et configurez une nouvelle connexion de Peering.
    > [!div class="mx-imgBorder"]
    > ![Affichage des ressources de peering](../media/setup-direct-modify-addconnection.png)

1. Remplissez le formulaire **Connexion de Peering direct**, puis sélectionnez **Enregistrer**. Pour obtenir de l’aide sur la configuration d’une connexion de Peering, consultez les étapes de la section « Créer et approvisionner un Peering direct ».
    > [!div class="mx-imgBorder"]
    > ![Direct Peering Connection form](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Supprimer les connexions de Peering direct

Pour le moment, le Portail Azure ne prend pas en charge la suppression d’une connexion. Pour plus d’informations, contactez l’équipe [Peering Microsoft](mailto:peeringexperience@microsoft.com).

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Mettre à niveau ou rétrograder la bande passante sur les connexions actives
1. Sélectionnez une connexion de Peering que vous souhaitez modifier, puis sélectionnez **…**  > **Modifier la connexion**.
    > [!div class="mx-imgBorder"]
    > ![Modifier la connexion](../media/setup-direct-modify-editconnection.png)

1. Modifiez la bande passante en déplaçant le curseur, puis sélectionnez **Enregistrer**.
    > [!div class="mx-imgBorder"]
    > ![Modify bandwidth](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4-or-ipv6-session-information-on-active-connections"></a>Ajouter des informations de session IPv4 ou IPv6 sur des connexions actives
1. Sélectionnez une connexion de Peering que vous souhaitez modifier, puis sélectionnez **…**  > **Modifier la connexion** comme indiqué à l’étape 1.
1. Entrez les informations **Préfixe IPv4 de session** ou **Préfixe IPv6 de session**, puis sélectionnez **Enregistrer**.

### <a name="remove-ipv4-or-ipv6-session-information-on-active-connections"></a>Supprimer des informations de session IPv4 ou IPv6 sur des connexions actives
La suppression d’informations **Préfixe IPv4 de session** ou **Préfixe IPv6 de session** n’est pas actuellement prise en charge dans le portail. Pour plus d’informations, contactez l’équipe [Peering Microsoft](mailto:peeringexperience@microsoft.com).
