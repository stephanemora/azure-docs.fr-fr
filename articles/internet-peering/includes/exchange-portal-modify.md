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
ms.openlocfilehash: e3e87e59f5b3c95051d9ee53e4b8d87afe9d9ba8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75773744"
---
Cette section décrit comment effectuer les opérations de modification suivantes pour le peering direct :

### <a name="add-exchange-peering-connections"></a>Ajouter des connexions de peering Exchange

1. Cliquez sur le bouton **+ Ajouter des connexions** en haut et configurez une nouvelle connexion de peering.
    > [!div class="mx-imgBorder"]
    > ![Affichage des ressources de peering](../media/setup-exchange-modify-addconnection.png)
1. Remplissez le formulaire **Connexion de Peering direct**, puis cliquez sur **Enregistrer**. Pour obtenir de l’aide sur la configuration d’une connexion de Peering, consultez les étapes de la section « Créer et approvisionner un Peering direct » ci-dessus.
    > [!div class="mx-imgBorder"]
    > ![Affichage des ressources de peering](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Supprimer des connexions de peering Exchange

1. Cliquez sur une connexion de Peering que vous souhaitez supprimer, puis cliquez sur le bouton **...**  > **Supprimer la connexion**.
    > [!div class="mx-imgBorder"]
    > ![Suppression de la connexion de Peering](../media/setup-exchange-modify-deleteconnection.png)
1. Entrez l’ID de ressource dans la zone **Confirmer la suppression** comme indiqué dans les zones mises en surbrillance, puis cliquez sur **Supprimer**.
    > [!div class="mx-imgBorder"]
    > ![Connexion de Peering DeleteConfirm](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Ajouter une session IPv4/IPv6 sur les connexions actives

1. Cliquez sur une connexion de peering que vous souhaitez modifier, puis cliquez sur le bouton **...**  > **Modifier la connexion**.
    > [!div class="mx-imgBorder"]
    > ![Modification de la connexion de peering](../media/setup-exchange-modify-editconnection.png)
1. Ajoutez des informations sur l’**adresse IPv4** ou l’**adresse IPv6**, puis cliquez sur **Enregistrer**.
    > [!div class="mx-imgBorder"]
    > ![Modifier la connexion de Peering](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Supprimer la session IPv4/IPv6 sur les connexions actives

La suppression d’une session IPv4/IPv6 à partir d’une connexion existante n’est actuellement pas prise en charge sur le portail. Contactez le [Peering Microsoft](mailto:peeringexperience@microsoft.com).