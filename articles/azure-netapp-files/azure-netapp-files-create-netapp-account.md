---
title: Créer un compte NetApp pour accéder à Azure NetApp Files | Microsoft Docs
description: Ce document décrit comment accéder à Azure NetApp Files et créer un compte NetApp afin de pouvoir configurer un pool de capacité et créer un volume.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: bb43a75b6a221c15c8724302797d04c22e04c8d2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58113637"
---
# <a name="create-a-netapp-account"></a>Créer un compte NetApp
La création d’un compte NetApp vous permet de configurer un pool de capacité et par la suite de créer un volume. Le panneau Azure NetApp Files permet de créer un nouveau compte NetApp.

## <a name="before-you-begin"></a>Avant de commencer
Vous devez avoir inscrit votre abonnement pour l’utilisation du fournisseur de ressources NetApp et la fonctionnalité d’évaluation publique.

[S’inscrire à Azure NetApp Files](azure-netapp-files-register.md)

## <a name="steps"></a>Étapes 

1. Connectez-vous au portail Azure. 
2. Accédez à Azure NetApp Files à l’aide d’une des méthodes suivantes :  
   * Recherchez **Azure NetApp Files** dans la zone de recherche du portail Azure.  
   * Cliquez sur **Tous les services** dans la navigation, puis filtrez sur Azure NetApp Files.  

   Vous pouvez placer le panneau Azure NetApp Files dans vos Favoris en cliquant sur l’icône représentant une étoile en regard de celui-ci. 

3. Cliquez sur **+Ajouter** pour créer un nouveau compte NetApp.  
   La nouvelle fenêtre de compte NetApp s’affiche.  

4. Fournissez les informations suivantes pour votre compte NetApp : 
   * **Nom du compte**  
     Indiquez un nom unique pour l’abonnement.
   * **Abonnement**  
     Sélectionnez un abonnement à partir de vos abonnements existants.
   * **Groupe de ressources**   
     Utilisez un groupe de ressources existant ou créez-en un.
   * **Lieu**  
     Sélectionnez la région dans laquelle vous souhaitez que le compte et ses ressources enfants soient situés.  

     ![Nouveau compte NetApp](../media/azure-netapp-files/azure-netapp-files-new-netapp-account.png)


5. Cliquez sur **Créer**.     
   Le compte NetApp que vous avez créé apparaît désormais dans le panneau Azure NetApp Files. 

## <a name="next-steps"></a>Étapes suivantes  

[Configurer un pool de capacité](azure-netapp-files-set-up-capacity-pool.md)

