---
title: Créer un volume pour Azure NetApp Files | Microsoft Docs
description: Décrit comment créer un volume pour Azure NetApp Files.
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
ms.topic: get-started-article
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 42e475f4da2102bb8b010daec6e6451ba7f13848
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011090"
---
# <a name="create-a-volume-for-azure-netapp-files"></a>Créer un volume pour Azure NetApp Files

La consommation de capacité d’un volume est calculée par rapport à la capacité configurée de son pool.  Vous pouvez créer plusieurs volumes dans un pool de capacité, mais la consommation de capacité totale des volumes ne doit pas dépasser la taille du pool. 

## <a name="before-you-begin"></a>Avant de commencer 
Vous devez déjà avoir configuré un pool de capacité.  

[Configurer un pool de capacité](azure-netapp-files-set-up-capacity-pool.md)

## <a name="steps"></a>Étapes 
1.  Cliquez sur le panneau **Volumes** à partir du panneau Gérer les pools de capacités. 
2.  Cliquez sur **+ Ajouter un volume** pour créer un volume.  
    La fenêtre Nouveau volume apparaît.

3.  Dans la fenêtre Nouveau volume, cliquez sur **Créer** et renseignez les champs suivants :   
    * **Nom**      
        Spécifiez le nom du volume que vous créez.   
        Le nom doit être unique au sein d’un groupe de ressources. Il doit comprendre au moins 3 caractères.  Vous pouvez utiliser tous les caractères alphanumériques.

    * **Chemin de fichier**  
        Spécifiez le chemin du fichier à utiliser pour créer le chemin d’exportation pour le nouveau volume. Le chemin d’exportation est utilisé pour monter le volume et y accéder.   
        Une cible de montage est le point de terminaison de l’adresse IP du service NFS. Elle est générée automatiquement.    
        Le nom du chemin du fichier peut contenir seulement des lettres, des chiffres et des traits d’union (« - »). Il doit comprendre entre 16 et 40 caractères.  

    * **Quota**  
        Spécifiez la quantité de stockage logique allouée au volume.  
        Le champ **Quota disponible** indique la quantité d’espace inutilisé dans le pool de capacités choisi, que vous pouvez utiliser pour créer un volume. La taille du nouveau volume ne doit pas dépasser le quota disponible.  

    * **Réseau virtuel**  
        Spécifiez le réseau virtuel Azure à partir duquel vous voulez accéder au volume. Azure NetApp Files doit être configuré sur le réseau virtuel que vous spécifiez. Le service Azure NetApp Files est accessible seulement à partir d’un réseau virtuel qui se trouve au même emplacement que le volume.   

    ![Nouveau volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)

4.  Cliquez sur **OK**. 
 
Un volume hérite de l’abonnement, du groupe de ressources et des attributs d’emplacement de son pool de capacité. Vous pouvez suivre l’état du déploiement du volume dans le volet des notifications.

## <a name="next-steps"></a>Étapes suivantes  
[Configurer une stratégie d’exportation pour un volume (facultatif)](azure-netapp-files-configure-export-policy.md)

