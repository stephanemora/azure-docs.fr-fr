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
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: b-juche
ms.openlocfilehash: 0d4629651e17c917720196c1e5f6152c50c2a33a
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57770264"
---
# <a name="create-a-volume-for-azure-netapp-files"></a>Créer un volume pour Azure NetApp Files

La consommation de capacité d’un volume est calculée par rapport à la capacité configurée de son pool.  Vous pouvez créer plusieurs volumes dans un pool de capacité, mais la consommation de capacité totale des volumes ne doit pas dépasser la taille du pool. 

## <a name="before-you-begin"></a>Avant de commencer 
Vous devez déjà avoir configuré un pool de capacité.   
[Configurer un pool de capacité](azure-netapp-files-set-up-capacity-pool.md)   
Un sous-réseau doit être délégué à Azure NetApp Files.  
[Déléguer un sous-réseau à Azure NetApp Files](azure-netapp-files-delegate-subnet.md)


## <a name="steps"></a>Étapes 
1.  Cliquez sur le panneau **Volumes** à partir du panneau Gérer les pools de capacités. 
2.  Cliquez sur **+ Ajouter un volume** pour créer un volume.  
    La fenêtre Nouveau volume apparaît.

3.  Dans la fenêtre Nouveau volume, cliquez sur **Créer** et renseignez les champs suivants :   
    * **Nom**      
        Spécifiez le nom du volume que vous créez.   

        Le nom doit être unique au sein d’un groupe de ressources. Il doit comprendre au moins trois caractères.  Vous pouvez utiliser tous les caractères alphanumériques.

    * **Chemin de fichier**  
        Spécifiez le chemin du fichier à utiliser pour créer le chemin d’exportation pour le nouveau volume. Le chemin d’exportation est utilisé pour monter le volume et y accéder.   
     
        Le nom du chemin du fichier peut contenir seulement des lettres, des chiffres et des traits d’union (« - »). Il doit comprendre entre 16 et 40 caractères.  

    * **Quota**  
        Spécifiez la quantité de stockage logique allouée au volume.  

        Le champ **Quota disponible** indique la quantité d’espace inutilisé dans le pool de capacités choisi, que vous pouvez utiliser pour créer un volume. La taille du nouveau volume ne doit pas dépasser le quota disponible.  

    * **Réseau virtuel**  
        Spécifiez le réseau virtuel Azure à partir duquel vous voulez accéder au volume.  

        Le réseau virtuel que vous spécifiez doit avoir un sous-réseau délégué à Azure NetApp Files. Le service Azure NetApp Files est accessible seulement à partir du même réseau virtuel ou d’un sous-réseau qui se trouve dans la même région que le volume via le peering de réseau virtuel. Vous pouvez également accéder au volume à partir de votre réseau local via Express Route.   

    * **Sous-réseau**  
        Spécifiez le sous-réseau que vous souhaitez utiliser pour le volume.  
        Le sous-réseau que vous spécifiez doit être délégué à Azure NetApp Files. 
        
        Si vous n’avez pas délégué un sous-réseau, vous pouvez cliquer sur **Créer** sur la page Créer un volume. Ensuite, dans la page Créer un sous-réseau, fournissez les informations sur le sous-réseau, puis sélectionnez **Microsoft.NetApp/volumes** pour déléguer le sous-réseau à Azure NetApp Files. Notez que dans chaque réseau virtuel, un seul sous-réseau peut être délégué à Azure NetApp Files.   
 
        ![Nouveau volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Créer un sous-réseau](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)


4.  Cliquez sur **OK**. 
 
Un volume hérite de l’abonnement, du groupe de ressources et des attributs d’emplacement de son pool de capacité. Vous pouvez suivre l’état du déploiement du volume dans le volet des notifications.

## <a name="next-steps"></a>Étapes suivantes  
* [Configurer une stratégie d’exportation pour un volume (facultatif)](azure-netapp-files-configure-export-policy.md)
* [En savoir plus sur l’intégration d’un réseau virtuel pour les services Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)

