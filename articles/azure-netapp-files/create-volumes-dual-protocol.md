---
title: Créer un volume double protocole (NFSv3 et SMB) pour Azure NetApp Files | Microsoft Docs
description: Décrit la procédure de création d’un volume qui utilise le double protocole NFSv3 et SMB avec prise en charge du mappage d’utilisateur LDAP.
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
ms.date: 8/11/2020
ms.author: b-juche
ms.openlocfilehash: dcdb3e8ce545227bc11cc60e3885c1a985ed34f4
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89459995"
---
# <a name="create-a-dual-protocol-nfsv3-and-smb-volume-for-azure-netapp-files"></a>Créer un volume double protocole (NFSv3 et SMB) pour Azure NetApp Files

Azure NetApp Files prend en charge la création de volumes en utilisant NFS (NFSv3 et NFSv4.1), SMBv3 ou le double protocole. Cet article explique la procédure de création d’un volume qui utilise le double protocole NFSv3 et SMB avec prise en charge du mappage d’utilisateur LDAP.  


## <a name="before-you-begin"></a>Avant de commencer 

* Vous devez déjà avoir configuré un pool de capacité.  
    Consultez [Configurer un pool de capacité](azure-netapp-files-set-up-capacity-pool.md).   
* Un sous-réseau doit être délégué à Azure NetApp Files.  
    Consultez [Déléguer un sous-réseau à Azure NetApp Files](azure-netapp-files-delegate-subnet.md).

## <a name="considerations"></a>Considérations

* Veillez à respecter la [configuration requise pour les connexions Active Directory](azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections). 
* Créez une zone de recherche inversée sur le serveur DNS, puis ajoutez un enregistrement pointeur (PTR) de l’ordinateur hôte AD dans cette zone de recherche inversée. Sinon, la création du volume à deux protocoles échoue.
* Vérifiez que le client NFS est à jour et qu’il exécute les mises à jour les plus récentes du système d’exploitation.

## <a name="create-a-dual-protocol-volume"></a>Créer un volume à deux protocoles

1.  Cliquez sur le panneau **Volumes** à partir du panneau Pools de capacités. Cliquez sur **+ Ajouter un volume** pour créer un volume. 

    ![Accédez à Volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  Dans la fenêtre Créer un volume, cliquez sur **Créer** et fournissez les informations pour les champs suivants sous l’onglet De base :   
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
        Spécifiez le réseau virtuel Azure (VNet) à partir duquel vous voulez accéder au volume.  

        Le réseau virtuel que vous spécifiez doit avoir un sous-réseau délégué à Azure NetApp Files. Le service Azure NetApp Files est accessible seulement à partir du même réseau virtuel ou d’un sous-réseau qui se trouve dans la même région que le volume via le peering de réseau virtuel. Vous pouvez également accéder au volume à partir de votre réseau local via Express Route.   

    * **Sous-réseau**  
        Spécifiez le sous-réseau que vous souhaitez utiliser pour le volume.  
        Le sous-réseau que vous spécifiez doit être délégué à Azure NetApp Files. 
        
        Si vous n’avez pas délégué un sous-réseau, vous pouvez cliquer sur **Créer** sur la page Créer un volume. Ensuite, dans la page Créer un sous-réseau, fournissez les informations sur le sous-réseau, puis sélectionnez **Microsoft.NetApp/volumes** pour déléguer le sous-réseau à Azure NetApp Files. Dans chaque réseau virtuel, un seul sous-réseau peut être délégué à Azure NetApp Files.   
 
        ![Créer un volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Créer un sous-réseau](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Si vous souhaitez appliquer une stratégie d’instantané existante au volume, cliquez sur **Afficher la section avancée** pour la développer, indiquez si vous souhaitez masquer le chemin d'accès de l’instantané, puis sélectionnez une stratégie d’instantané dans le menu déroulant. 

        Pour plus d’informations sur la création d’une stratégie d’instantané, consultez [Gérer les stratégies d’instantané](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

        ![Afficher la sélection avancée](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. Cliquez sur **Protocole**, puis effectuez les actions suivantes :  
    * Sélectionnez le type de protocole **double protocole (NFSv3 et SMB)** pour le volume.   

    * Sélectionnez la connexion **Active Directory** dans la liste déroulante.  
    Le répertoire Active Directory que vous utilisez doit avoir un certificat d’autorité de certification racine de serveur. 

    * Spécifiez le **Chemin d’accès du volume** pour le volume.   
    Ce chemin d'accès de volume est le nom du volume partagé. Le nom doit commencer par un caractère alphabétique et doit être unique dans chaque abonnement et chaque région.  

    * Spécifiez le **Style de sécurité** à utiliser : NTFS (par défaut) ou UNIX.

    * En option, [configurez une stratégie d’exportation pour le volume](azure-netapp-files-configure-export-policy.md).

    ![Spécifier le double protocole](../media/azure-netapp-files/create-volume-protocol-dual.png)

4. Cliquez sur **Vérifier et créer** pour passer en revue les informations du volume. Cliquez ensuite sur **Créer** pour créer le volume.

    Le volume créé s’affiche dans la page Volumes. 
 
    Un volume hérite de l’abonnement, du groupe de ressources et des attributs d’emplacement de son pool de capacité. Vous pouvez suivre l’état du déploiement du volume dans le volet des notifications.

## <a name="upload-active-directory-certificate-authority-public-root-certificate"></a>Charger le certificat racine public de l’autorité de certification Active Directory  

1.  Suivez [Installer l’autorité de certification](https://docs.microsoft.com/windows-server/networking/core-network-guide/cncg/server-certs/install-the-certification-authority) pour installer et configurer l’autorité de certification AD DS. 

2.  Suivez [Afficher les certificats avec le composant logiciel enfichable MMC](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) pour utiliser le composant logiciel enfichable MMC et l’outil Gestionnaire de certificats.  
    Utilisez le composant logiciel enfichable Gestionnaire de certificats pour localiser le certificat racine ou d’émission pour l’appareil local. Vous devez exécuter les commandes du composant logiciel enfichable Gestion des certificats à partir de l’un des paramètres suivants :  
    * Un client Windows joint au domaine et sur lequel le certificat racine est installé 
    * un autre ordinateur du domaine contenant le certificat racine.  

3. Exportez le certificat racine.  
    Assurez-vous que le certificat est exporté au format X.509 encodé en base 64 (.CER) : 

    ![Assistant Exportation de certificat](../media/azure-netapp-files/certificate-export-wizard.png)

4. Accédez au compte NetApp du volume double protocole, cliquez sur **Connexions Active Directory**, puis chargez le certificat d’autorité de certification racine en utilisant la fenêtre **Joindre Active Directory** :  

    ![Certificat d’autorité de certification racine de serveur](../media/azure-netapp-files/server-root-ca-certificate.png)

    Assurez-vous que le nom de l’autorité de certification peut être résolu par DNS. Ce nom figure dans le champ « Délivré par » ou « Émetteur » sur le certificat :  

    ![Informations sur le certificat](../media/azure-netapp-files/certificate-information.png)

## <a name="manage-ldap-posix-attributes"></a>Gérer les attributs LDAP POSIX

Vous pouvez gérer les attributs POSIX, tels que l’identificateur d’utilisateur, le répertoire de base et d’autres valeurs à l’aide du composant logiciel enfichable MMC Utilisateurs et ordinateurs Active Directory.  L'exemple suivant illustre l’Éditeur d’attributs Active Directory :  

![Éditeur d’attributs Active Directory](../media/azure-netapp-files/active-directory-attribute-editor.png) 


## <a name="configure-the-nfs-client"></a>Configurer le client NFS 

Suivez les instructions de [Configurer un client NFS pour Azure NetApp Files](configure-nfs-clients.md) pour configurer le client NFS.  

## <a name="next-steps"></a>Étapes suivantes  

* [FAQ sur le double protocole](azure-netapp-files-faqs.md#dual-protocol-faqs)
* [Configurer un client NFS pour Azure NetApp Files](configure-nfs-clients.md) 
