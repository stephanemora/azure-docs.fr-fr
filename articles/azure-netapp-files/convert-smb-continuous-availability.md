---
title: Convertir des volumes SMB Azure NetApp Files existants pour utiliser la disponibilité continue SMB | Microsoft Docs
description: Décrit l’activation de l’autorité de certification SMB en convertissant un volume SMB Azure NetApp Files existant.
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
ms.topic: how-to
ms.date: 05/10/2021
ms.author: b-juche
ms.openlocfilehash: b55b7e5632ad852b199121179772fa078129cdb5
ms.sourcegitcommit: 1b19b8d303b3abe4d4d08bfde0fee441159771e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109755085"
---
# <a name="convert-existing-smb-volumes-to-use-continuous-availability"></a>Convertir des volumes SMB existants pour utiliser la disponibilité continue 

Vous pouvez activer la fonctionnalité de disponibilité continue SMB lors de la [création d’un volume SMB](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume). Vous pouvez également convertir un volume SMB existant pour activer la fonctionnalité de disponibilité continue SMB.  Cet article explique comment activer la fonctionnalité de disponibilité continue SMB en convertissant un volume existant.  

> [!IMPORTANT]
> Cette procédure comprend un basculement du volume d’origine vers le nouveau volume activé pour les partages de disponibilité continue. Dès lors, vous devez planifier une fenêtre de maintenance pour ce processus. 

## <a name="steps"></a>Étapes

1. Assurez-vous que vous êtes [inscrit à la fonctionnalité de partages de disponibilité continue SMB](https://aka.ms/anfsmbcasharespreviewsignup) .  
2. Arrêtez l’application qui utilise le volume SMB. 
3. [Créez une capture instantanée à la demande](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume) du volume existant. 
4. Depuis le volume existant, sélectionnez **Captures instantanées** pour afficher la liste des captures instantanées.
5. Cliquez avec le bouton droit sur la capture instantanée, puis sélectionnez l’option de menu **Restaurer sur un nouveau volume**.
    
    ![Capture instantanée affichant l’option Restaurer sur un nouveau volume.](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

6. Dans la fenêtre Créer un volume qui s’affiche, spécifiez les informations concernant le nouveau volume :  

    * **Nom du volume**    
    Spécifiez le nom du volume que vous créez.   
    Le nom doit être unique au sein d’un groupe de ressources. Il doit comprendre au moins trois caractères. Vous pouvez utiliser tous les caractères alphanumériques.

    * **Quota**   
    Spécifiez la quantité de stockage logique à allouer au volume.

    ![Capture instantanée affichant la fenêtre Créer un volume.](../media/azure-netapp-files/snapshot-restore-new-volume.png) 

7. Sous la section Protocole de la fenêtre Créer un volume, veillez à bien sélectionner l’option **Activer la disponibilité continue**.

    ![Capture instantanée affichant l’option Activer la disponibilité continue.](../media/azure-netapp-files/enable-continuous-availability-option.png) 

8. Cliquez sur **Vérifier + créer**. Cliquez sur **Créer**.   
    Le nouveau volume utilise le même protocole que celui qu’utilise la capture instantanée.
   Le nouveau volume sur lequel l’instantané est restauré apparaît dans la vue Volumes.

9. Une fois le nouveau volume créé, cliquez sur **Instructions de montage** dans le panneau du volume sélectionné. Suivez ensuite ces instructions pour monter le nouveau volume.    

10. Reconfigurez votre application pour utiliser le point de montage du nouveau volume.   

11. Redémarrez l’application que vous avez arrêtée à l’étape 2. 

12. Lorsque l’application commence à utiliser le nouveau volume et que le processus de restauration vers le nouveau volume est terminé, vous pouvez supprimer le volume d’origine.  

## <a name="next-steps"></a>Étapes suivantes  

* [Créer un volume SMB pour Azure NetApp Files](azure-netapp-files-create-volumes-smb.md)
* [Monter ou démonter un volume pour des machines virtuelles Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)