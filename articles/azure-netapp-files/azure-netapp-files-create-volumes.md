---
title: Créer un volume NFS pour Azure NetApp Files | Microsoft Docs
description: Décrit comment créer un volume NFS pour Azure NetApp Files.
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
ms.date: 12/01/2019
ms.author: b-juche
ms.openlocfilehash: 9e8817f802ca1d73ca0f6bfa2b32b1b14b37d7da
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74773549"
---
# <a name="create-an-nfs-volume-for-azure-netapp-files"></a>Créer un volume NFS pour Azure NetApp Files

Azure NetApp Files prend en charge les volumes NFS (NFSv3 et NFSv4.1) et SMBv3. La consommation de capacité d’un volume est comptée par rapport à la capacité configurée de son pool. Cet article explique comment créer un volume NFS. Si vous souhaitez créer un volume SMB, consultez [Créer un volume SMB pour Azure NetApp Files](azure-netapp-files-create-volumes-smb.md). 

## <a name="before-you-begin"></a>Avant de commencer 
Vous devez déjà avoir configuré un pool de capacité.   
[Configurer un pool de capacité](azure-netapp-files-set-up-capacity-pool.md)   
Un sous-réseau doit être délégué à Azure NetApp Files.  
[Déléguer un sous-réseau à Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="considerations"></a>Considérations 

* Choix de la version de NFS à utiliser  
  NFSv3 peut gérer de nombreux cas d’usage et est couramment déployé dans la plupart des applications d’entreprise. Vous devez valider la version (NFSv3 ou NFSv 4.1) requise par votre application et créer votre volume à l’aide de la version appropriée. Par exemple, si vous utilisez [Apache ActiveMQ](https://activemq.apache.org/shared-file-system-master-slave), le verrouillage de fichiers avec NFSv 4.1 est recommandé par rapport à NFSv3. 

* Sécurité  
  La prise en charge des bits en mode UNIX (lecture, écriture et exécution) est disponible pour NFSv3 et NFSv 4.1. Un accès de niveau racine est requis sur le client NFS pour monter des volumes NFS.

* Prise en charge des utilisateurs/groupes locaux et de LDAP pour NFSv 4.1  
  Actuellement, NFSv 4.1 prend en charge l’accès racine aux volumes uniquement. Consultez [Configurer le domaine par défaut NFSv4.1 pour Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md). 

## <a name="best-practice"></a>Bonne pratique

* Vous devez vous assurer que vous utilisez les instructions de montage appropriées pour le volume.  Voir [Monter ou démonter un volume pour des machines virtuelles Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).

* Le client NFS doit se trouver sur le même réseau virtuel ou réseau virtuel avec peering que le volume Azure NetApp Files. La connexion depuis l’extérieur du réseau virtuel est prise en charge ; toutefois, cela introduira une latence supplémentaire et réduira les performances globales.

* Vous devez vous assurer que le client NFS est à jour et qu’il exécute les dernières mises à jour du système d’exploitation.

## <a name="create-an-nfs-volume"></a>Créer un volume NFS

1.  Cliquez sur le panneau **Volumes** à partir du panneau Pools de capacités. 

    ![Accédez à Volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2.  Cliquez sur **+ Ajouter un volume** pour créer un volume.  
    La fenêtre Créer un volume s’affiche.

3.  Dans la fenêtre Créer un volume, cliquez sur **Créer** et renseignez les champs suivants :   
    * **Nom du volume**      
        Spécifiez le nom du volume que vous créez.   

        Un nom de volume doit être unique au sein de chaque pool de capacité. Il doit comprendre au moins trois caractères. Vous pouvez utiliser tous les caractères alphanumériques.   

        Vous ne pouvez pas utiliser `default` comme nom de volume.

    * **Pool de capacités**  
        Spécifiez le pool de capacité dans lequel vous souhaitez que le volume soit créé.

    * **Quota**  
        Spécifiez la quantité de stockage logique allouée au volume.  

        Le champ **Quota disponible** indique la quantité d’espace inutilisé dans le pool de capacités choisi, que vous pouvez utiliser pour créer un volume. La taille du nouveau volume ne doit pas dépasser le quota disponible.  

    * **Réseau virtuel**  
        Spécifiez le réseau virtuel Azure à partir duquel vous voulez accéder au volume.  

        Le réseau virtuel que vous spécifiez doit avoir un sous-réseau délégué à Azure NetApp Files. Le service Azure NetApp Files est accessible seulement à partir du même réseau virtuel ou d’un sous-réseau qui se trouve dans la même région que le volume via le peering de réseau virtuel. Vous pouvez également accéder au volume à partir de votre réseau local via Express Route.   

    * **Sous-réseau**  
        Spécifiez le sous-réseau que vous souhaitez utiliser pour le volume.  
        Le sous-réseau que vous spécifiez doit être délégué à Azure NetApp Files. 
        
        Si vous n’avez pas délégué un sous-réseau, vous pouvez cliquer sur **Créer** sur la page Créer un volume. Ensuite, dans la page Créer un sous-réseau, fournissez les informations sur le sous-réseau, puis sélectionnez **Microsoft.NetApp/volumes** pour déléguer le sous-réseau à Azure NetApp Files. Dans chaque réseau virtuel, un seul sous-réseau peut être délégué à Azure NetApp Files.   
 
        ![Créer un volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Créer un sous-réseau](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Cliquez sur **Protocole**, puis effectuez les actions suivantes :  
    * Sélectionnez **NFS** comme type de protocole pour le volume.   
    * Spécifiez le **chemin du fichier** à utiliser pour créer le chemin d’exportation du nouveau volume. Le chemin d’exportation est utilisé pour monter le volume et y accéder.

        Le nom du chemin du fichier peut contenir seulement des lettres, des chiffres et des traits d’union (« - »). Il doit comprendre entre 16 et 40 caractères. 

        Le chemin d’accès du fichier doit être unique au sein de chaque abonnement et chaque région. 

    * Sélectionnez la version de NFS (**NFSv3**  ou **NFSv4.1** ) du volume.  
    * Le cas échéant, [configurez une stratégie d’exportation pour le volume NFS](azure-netapp-files-configure-export-policy.md).

    ![Spécifier le protocole NFS](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

5. Cliquez sur **Vérifier et créer** pour passer en revue les informations du volume.  Cliquez ensuite sur **Créer** pour créer le volume NFS.

    Le volume créé s’affiche dans la page Volumes. 
 
    Un volume hérite de l’abonnement, du groupe de ressources et des attributs d’emplacement de son pool de capacité. Vous pouvez suivre l’état du déploiement du volume dans le volet des notifications.


## <a name="next-steps"></a>Étapes suivantes  

* [Configurer le domaine par défaut NFSv4.1 pour Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Monter ou démonter un volume pour des machines virtuelles Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Configurer une stratégie d’exportation pour un volume NFS](azure-netapp-files-configure-export-policy.md)
* [Limites des ressources pour Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [En savoir plus sur l’intégration d’un réseau virtuel pour les services Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
