---
title: Créer un volume SMB pour Azure NetApp Files | Microsoft Docs
description: Cet article explique comment créer un volume SMB3 dans Azure NetApp Files. Découvrez la configuration requise pour les services de domaine et connexions Active Directory.
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
ms.date: 02/16/2021
ms.author: b-juche
ms.openlocfilehash: 91f4f90658281282cdcb01b091bd9c9647d8d702
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100635484"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Créer un volume SMB pour Azure NetApp Files

Azure NetApp Files prend en charge la création de volumes avec NFS (NFSv3 et NFSv4.1), SMB3 ou le double protocole (NFSv3 et SMB). La consommation de capacité d’un volume est comptée par rapport à la capacité configurée de son pool. Cet article explique comment créer un volume SMB3.

## <a name="before-you-begin"></a>Avant de commencer 

* Vous devez déjà avoir configuré un pool de capacité. Consultez [Configurer un pool de capacité](azure-netapp-files-set-up-capacity-pool.md).     
* Un sous-réseau doit être délégué à Azure NetApp Files. Consultez [Déléguer un sous-réseau à Azure NetApp Files](azure-netapp-files-delegate-subnet.md).

## <a name="configure-active-directory-connections"></a>Configurer des connexions Azure Active Directory 

Avant de créer un volume SMB, vous devez créer une connexion Active Directory. Si vous n’avez pas configuré de connexions Active Directory pour des fichiers Azure NetApp, suivez les instructions décrites dans [Créer et gérer des connexions Active Directory](create-active-directory-connections.md).

## <a name="add-an-smb-volume"></a>Ajouter un volume SMB

1. Cliquez sur le panneau **Volumes** à partir du panneau Pools de capacités. 

    ![Accédez à Volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Cliquez sur **+ Ajouter un volume** pour créer un volume.  
    La fenêtre Créer un volume s’affiche.

3. Dans la fenêtre Créer un volume, cliquez sur **Créer** et renseignez les champs suivants sous l’onglet De base :   
    * **Nom du volume**      
        Spécifiez le nom du volume que vous créez.   

        Un nom de volume doit être unique au sein de chaque pool de capacité. Il doit comprendre au moins trois caractères. Vous pouvez utiliser tous les caractères alphanumériques.   

        Vous ne pouvez pas utiliser `default` ni `bin` comme nom de volume.

    * **Pool de capacités**  
        Spécifiez le pool de capacité dans lequel vous souhaitez que le volume soit créé.

    * **Quota**  
        Spécifiez la quantité de stockage logique allouée au volume.  

        Le champ **Quota disponible** indique la quantité d’espace inutilisé dans le pool de capacités choisi, que vous pouvez utiliser pour créer un volume. La taille du nouveau volume ne doit pas dépasser le quota disponible.  

    * **Débit (Mio/s)**    
        Si le volume est créé dans un pool de capacité avec Qualité de service manuelle, spécifiez le débit souhaité pour le volume.   

        Si le volume est créé dans un pool de capacité avec Qualité de service automatique, la valeur affichée dans ce champ est (quota x débit du niveau de service).   

    * **Réseau virtuel**  
        Spécifiez le réseau virtuel Azure (VNet) à partir duquel vous voulez accéder au volume.  

        Le réseau virtuel que vous spécifiez doit avoir un sous-réseau délégué à Azure NetApp Files. Le service Azure NetApp Files est accessible seulement à partir du même réseau virtuel ou d’un réseau virtuel qui figure dans la même région que le volume via le peering de réseau virtuel. Vous pouvez également accéder au volume à partir de votre réseau local via Express Route.   

    * **Sous-réseau**  
        Spécifiez le sous-réseau que vous souhaitez utiliser pour le volume.  
        Le sous-réseau que vous spécifiez doit être délégué à Azure NetApp Files. 
        
        Si vous n’avez pas délégué un sous-réseau, vous pouvez cliquer sur **Créer** dans la page Créer un volume. Ensuite, dans la page Créer un sous-réseau, fournissez les informations sur le sous-réseau, puis sélectionnez **Microsoft.NetApp/volumes** pour déléguer le sous-réseau à Azure NetApp Files. Dans chaque réseau virtuel, un seul sous-réseau peut être délégué à Azure NetApp Files.   
 
        ![Créer un volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Créer un sous-réseau](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Si vous souhaitez appliquer une stratégie d’instantané existante au volume, cliquez sur **Afficher la section avancée** pour la développer, indiquez si vous souhaitez masquer le chemin d'accès de l’instantané, puis sélectionnez une stratégie d’instantané dans le menu déroulant. 

        Pour plus d’informations sur la création d’une stratégie d’instantané, consultez [Gérer les stratégies d’instantané](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

        ![Afficher la sélection avancée](../media/azure-netapp-files/volume-create-advanced-selection.png)

4. Cliquez sur **Protocole** et renseignez les informations suivantes :  
    * Sélectionnez **SMB** comme type de protocole pour le volume. 
    * Sélectionnez votre connexion **Active Directory** dans la liste déroulante.
    * Spécifiez le nom du volume partagé dans **Nom de partage**.

    ![Spécifier le protocole SMB](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Cliquez sur **Vérifier et créer** pour passer en revue les informations du volume.  Cliquez ensuite sur **Créer** pour créer le volume SMB.

    Le volume créé s’affiche dans la page Volumes. 
 
    Un volume hérite de l’abonnement, du groupe de ressources et des attributs d’emplacement de son pool de capacité. Vous pouvez suivre l’état du déploiement du volume dans le volet des notifications.

## <a name="control-access-to-an-smb-volume"></a>Contrôler l’accès à un volume SMB  

L’accès à un volume SMB est géré par le biais d’autorisations.  

### <a name="share-permissions"></a>Autorisations de partage  

Par défaut, un nouveau volume dispose des autorisations de partage **Tout le monde / Contrôle total**. Les membres du groupe Admins du domaine peuvent modifier les autorisations de partage à l’aide de Gestion de l’ordinateur sur le compte d’ordinateur utilisé pour le volume Azure NetApp Files.

![Chemin de montage SMB](../media/azure-netapp-files/smb-mount-path.png) 
![Définir des autorisations de partage](../media/azure-netapp-files/set-share-permissions.png) 

### <a name="ntfs-file-and-folder-permissions"></a>Autorisations de fichier et de dossier NTFS  

Vous pouvez définir des autorisations pour un fichier ou un dossier à l’aide de l’onglet **Sécurité** des propriétés de l’objet dans le client SMB Windows.
 
![Définit des autorisations de fichier et de dossier](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>Étapes suivantes  

* [Monter ou démonter un volume pour des machines virtuelles Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Limites des ressources pour Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Questions fréquentes sur SMB](./azure-netapp-files-faqs.md#smb-faqs)
* [Résoudre les problèmes des volumes SMB ou à deux protocoles](troubleshoot-dual-protocol-volumes.md)
* [En savoir plus sur l’intégration d’un réseau virtuel pour les services Azure](../virtual-network/virtual-network-for-azure-services.md)
* [Installation d’une nouvelle forêt Active Directory à l’aide d’Azure CLI](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
