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
ms.openlocfilehash: d9ff01cf0180dae7f75d9753ba889d0caddad937
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75773984"
---
Cette section décrit comment effectuer les opérations de modification suivantes pour le peering direct :

### <a name="add-direct-peering-connections"></a>Ajouter les connexions de Peering direct
1. Cliquez sur le bouton **+ Ajouter des connexions** en haut et configurez une nouvelle connexion de peering.
    > [!div class="mx-imgBorder"]
    > ![Affichage des ressources de peering](../media/setup-direct-modify-addconnection.png)
1. Remplissez le formulaire **Connexion de peering direct**, puis cliquez sur **Enregistrer**. Pour obtenir de l’aide sur la configuration d’une connexion de Peering, consultez les étapes de la section « Créer et approvisionner un Peering direct » ci-dessus.
    > [!div class="mx-imgBorder"]
    > ![Affichage des ressources de peering](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Supprimer les connexions de Peering direct

Pour le moment, le portail ne prend pas en charge la suppression d’une connexion. Contactez le [Peering Microsoft](mailto:peeringexperience@microsoft.com).

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Mettre à niveau ou rétrograder la bande passante sur les connexions actives
1. Cliquez sur une connexion de peering que vous souhaitez modifier, puis cliquez sur le bouton **...**  > **Modifier la connexion**.
    > [!div class="mx-imgBorder"]
    > ![Modification de la connexion de peering](../media/setup-direct-modify-editconnection.png)
1. Modifiez la bande passante comme indiqué ci-dessous, puis cliquez sur **Enregistrer**.
    > [!div class="mx-imgBorder"]
    > ![Connexion de peering - Modifier la bande passante](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Ajoutez une session IPv4/IPv6 sur les connexions actives.
1. Cliquez sur une connexion de peering que vous souhaitez activer pour Microsoft Azure Peering Service, puis cliquez sur le bouton **...**  > **Modifier la connexion** comme indiqué ci-avant.
1. Ajoutez les informations **Session IPv4 prefix** (Préfixe IPv4 de session) ou **Session IPv6 prefix** (Préfixe IPv6 de session), puis cliquez sur **Enregistrer**.

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Supprimez la session IPv4/IPv6 sur les connexions actives.
    Removing a **Session IPv4 prefix** or **Session IPv6 prefix** info is not currently supported on portal. Contact [Microsoft peering](mailto:peeringexperience@microsoft.com).
