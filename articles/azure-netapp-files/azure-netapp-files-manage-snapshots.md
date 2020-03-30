---
title: Gérer les instantanés avec Azure NetApp Files | Microsoft Docs
description: Explique comment créer des captures instantanées pour un volume ou procéder à la restauration d'une capture instantanée sur un nouveau volume avec Azure NetApp Files.
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
ms.date: 03/03/2020
ms.author: b-juche
ms.openlocfilehash: 48055a774808aea86452e8410b7e717f5019d172
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78267903"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Gérer les instantanés avec Azure NetApp Files

Vous pouvez utiliser Azure NetApp Files pour créer manuellement un instantané à la demande à partir d’un volume, ou pour restaurer un volume à partir d’un instantané. Le service Azure NetApp Files ne crée pas automatiquement des instantanés de volume.  

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Créer un instantané à la demande pour un volume

Vous pouvez créer des instantanés seulement à la demande. Actuellement, les stratégies d’instantané ne sont pas prises en charge.

1.  Dans le panneau Volume, cliquez sur **Captures instantanées**.

    ![Accéder à des captures instantanées](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  Cliquez sur **+ Ajouter une capture instantanée** afin de créer une capture instantanée à la demande pour un volume.

    ![Ajouter une capture instantanée](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  Dans la fenêtre Nouvel instantané, spécifiez un nom pour l’instantané que vous créez.   

    ![Nouvelle capture instantanée](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Cliquez sur **OK**. 

## <a name="restore-a-snapshot-to-a-new-volume"></a>Restaurer un instantané sur un nouveau volume

Actuellement, vous pouvez restaurer un instantané seulement sur un nouveau volume. 
1. Accédez au panneau **Gérer les instantanés** à partir du panneau Volume pour afficher la liste des instantanés. 
2. Sélectionnez un instantané à restaurer.  
3. Cliquez avec le bouton droit sur le nom de l’instantané, puis sélectionnez **Restaurer sur un nouveau volume** dans l’option de menu.  

    ![Restaurer un instantané sur un nouveau volume](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

4. Dans la fenêtre Nouveau Volume, spécifiez les informations pour le nouveau volume :  
    * **Nom**   
        Spécifiez le nom du volume que vous créez.  
        
        Le nom doit être unique au sein d’un groupe de ressources. Il doit comprendre au moins trois caractères.  Vous pouvez utiliser tous les caractères alphanumériques.

    * **Chemin du fichier**     
        Spécifiez le chemin du fichier à utiliser pour créer le chemin d’exportation pour le nouveau volume. Le chemin d’exportation est utilisé pour monter le volume et y accéder.   
        
        Une cible de montage est le point de terminaison de l’adresse IP du service NFS. Elle est générée automatiquement.   
        
        Le nom du chemin du fichier peut contenir seulement des lettres, des chiffres et des traits d’union (« - »). Il doit comprendre entre 16 et 40 caractères. 

    * **Quota**  
        Spécifiez la quantité de stockage logique allouée au volume.  

        Le champ **Quota disponible** indique la quantité d’espace inutilisé dans le pool de capacités choisi, que vous pouvez utiliser pour créer un volume. La taille du nouveau volume ne doit pas dépasser le quota disponible.

    *   **Réseau virtuel**  
        Spécifiez le réseau virtuel Azure à partir duquel vous voulez accéder au volume.  
        Le réseau virtuel que vous spécifiez doit avoir un sous-réseau délégué à Azure NetApp Files. Vous ne pouvez accéder à Azure NetApp Files qu'à partir du même réseau virtuel ou d'un réseau virtuel qui se trouve dans la même région que le volume via le peering de réseau virtuel. Vous pouvez accéder au volume à partir de votre réseau local via Express Route. 

    * **Sous-réseau**  
        Spécifiez le sous-réseau que vous souhaitez utiliser pour le volume.  
        Le sous-réseau que vous spécifiez doit être délégué au service Azure NetApp Files. Vous pouvez créer un sous-réseau en sélectionnant **Créer** sous le champ Sous-réseau.  
   <!--
    ![Restored new volume](../media/azure-netapp-files/azure-netapp-files-snapshot-new-volume.png) 
   -->

5. Cliquez sur **OK**.   
    Le nouveau volume sur lequel l’instantané est restauré apparaît dans le panneau Volumes.

## <a name="next-steps"></a>Étapes suivantes

[Comprendre la hiérarchie de stockage d’Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
