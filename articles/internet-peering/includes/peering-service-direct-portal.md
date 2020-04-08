---
title: Fichier include
titleSuffix: Azure
description: Fichier include
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: include
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: 60752cf1b3c05ab7817083e70310ba7b40227dec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129940"
---
1. Cliquez sur une connexion de peering que vous souhaitez activer pour Microsoft Azure Peering Service, puis cliquez sur le bouton **...**  > **Modifier la connexion**.
    > [!div class="mx-imgBorder"]
    > ![Modification de la connexion de peering](../media/setup-direct-modify-editconnection.png)
1. Sous la section ***Use for Peering Service*** (Utiliser Peering Service), cliquez sur **Activer** et **Enregistrer**.
    > [!div class="mx-imgBorder"]
    > ![Connexion de peering - Activer Peering Service](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. L’écran Vue d’ensemble comporte les détails du déploiement. Une fois votre déploiement terminé, cliquez sur **Accéder à la ressource**.
    > [!div class="mx-imgBorder"]
    > ![Votre déploiement a été effectué](../media/setup-direct-modify-overview-deployment-complete.png)

1. **Préfixes inscrits** apparaît alors sous Paramètres. Cliquez sur **Ajouter un préfixe inscrit**.
    > [!div class="mx-imgBorder"]
    > ![Préfixes et connexions inscrits](../media/setup-direct-modify-add-registered-prefix.png)
1. Pour inscrire un préfixe, sélectionnez un **Nom** et un **Préfixe**, puis cliquez sur **Enregistrer**.
    > [!div class="mx-imgBorder"]
    >  ![Inscrire un préfixe](../media/setup-direct-modify-register-a-prefix.png) 

1. Une fois qu’un préfixe est créé, il apparaît dans la liste des préfixes inscrits. Cliquez sur le **Nom** du préfixe pour afficher plus de détails.
    > [!div class="mx-imgBorder"]
    > ![Préfixes et connexions inscrits](../media/setup-direct-modify-registered-prefixes.png)
1. Dans la page des préfixes inscrits, les informations détaillées complètes vous permettent d’inclure la **clé de préfixe** pour chaque préfixe. Cette clé devra être fournie au client à qui ce préfixe aura été attribué par son fournisseur d’accès Internet. Le client peut ensuite inscrire son préfixe dans son abonnement avec cette clé.
    > [!div class="mx-imgBorder"]
    > ![Préfixe avec clé de préfixe](../media/setup-direct-modify-registered-prefix-detail.png)