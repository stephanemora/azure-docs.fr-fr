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
ms.date: 04/27/2021
ms.author: b-juche
ms.openlocfilehash: b195b1a816576ec8a065ebe83bd02fa3aeb53c5a
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108161390"
---
# <a name="create-a-dual-protocol-nfsv3-and-smb-volume-for-azure-netapp-files"></a>Créer un volume double protocole (NFSv3 et SMB) pour Azure NetApp Files

Azure NetApp Files prend en charge la création de volumes en utilisant NFS (NFSv3 et NFSv4.1), SMB3 ou le double protocole. Cet article explique la procédure de création d’un volume qui utilise le double protocole NFSv3 et SMB avec prise en charge du mappage d’utilisateur LDAP. 

Pour créer des volumes NFS, voir [Créer un volume NFS](azure-netapp-files-create-volumes.md). Pour créer des volumes SMB, voir [Créer un volume SMB](azure-netapp-files-create-volumes-smb.md). 

## <a name="before-you-begin"></a>Avant de commencer 

* Vous devez déjà avoir créé un pool de capacités.  
    Consultez [Configurer un pool de capacité](azure-netapp-files-set-up-capacity-pool.md).   
* Un sous-réseau doit être délégué à Azure NetApp Files.  
    Consultez [Déléguer un sous-réseau à Azure NetApp Files](azure-netapp-files-delegate-subnet.md).

## <a name="considerations"></a>Considérations

* Veillez à respecter la [configuration requise pour les connexions Active Directory](create-active-directory-connections.md#requirements-for-active-directory-connections). 
* Créez une zone de recherche inversée sur le serveur DNS, puis ajoutez un enregistrement pointeur (PTR) de l’ordinateur hôte AD dans cette zone de recherche inversée. Sinon, la création du volume à deux protocoles échoue.
* Vérifiez que le client NFS est à jour et qu’il exécute les mises à jour les plus récentes du système d’exploitation.
* Pour le moment, les volumes à deux protocoles ne prennent pas en charge Azure Active Directory Domain Services (AADDS). LDAP sur TLS ne doit pas être activé si vous utilisez AADDS.
* La version de NFS utilisée par un volume à deux protocoles est NFSv3. Par conséquent, les considérations suivantes s'appliquent :
    * Le double protocole ne prend pas en charge les attributs étendus des ACL Windows `set/get` provenant des clients NFS.
    * Les clients NFS ne peuvent pas modifier les autorisations pour le style de sécurité NTFS, et les clients Windows ne peuvent pas modifier les autorisations pour les volumes à deux protocoles de type UNIX.   

    Le tableau suivant décrit les styles de sécurité et leurs effets :  
    
    | Style de sécurité    | Clients pouvant modifier des autorisations   | Autorisations que les clients peuvent utiliser  | Style de sécurité effectif obtenu    | Clients pouvant accéder aux fichiers     |
    |-  |-  |-  |-  |-  |
    | `Unix`    | NFS   | Bits en mode NFSv3   | UNIX  | NFS et Windows   |
    | `Ntfs`    | Windows   | ACL NTFS     | NTFS  |NFS et Windows|
* Les utilisateurs d'UNIX qui montent le volume de style de sécurité NTFS via NFS sont authentifiés en tant qu'utilisateur Windows `root` pour UNIX `root` et `pcuser` pour tous les autres utilisateurs. Vérifiez que ces comptes d'utilisateur sont disponibles dans votre instance d'Active Directory avant de monter le volume lorsque vous utilisez NFS. 
* Si vous avez de grandes topologies et que vous utilisez le style de sécurité `Unix` avec un volume à double protocole ou LDAP avec des groupes étendus, Azure NetApp Files peut ne pas être en mesure d’accéder à tous les serveurs de vos topologies.  Si cette situation se produit, contactez l’équipe chargée de votre compte pour obtenir de l’aide.  <!-- NFSAAS-15123 --> 
* Vous n’avez pas besoin d’un certificat d’autorité de certification racine de serveur pour créer un volume à deux protocoles. Il n’est obligatoire que si le protocole LDAP sur TLS est activé.


## <a name="create-a-dual-protocol-volume"></a>Créer un volume à deux protocoles

1.  Cliquez sur le panneau **Volumes** à partir du panneau Pools de capacités. Cliquez sur **+ Ajouter un volume** pour créer un volume. 

    ![Accédez à Volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  Dans la fenêtre Créer un volume, cliquez sur **Créer** et fournissez les informations pour les champs suivants sous l’onglet De base :   
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

    * Spécifiez le **Chemin d’accès du volume** pour le volume.   
    Ce chemin d'accès de volume est le nom du volume partagé. Le nom doit commencer par un caractère alphabétique et doit être unique dans chaque abonnement et chaque région.  

    * Spécifiez le **Style de sécurité** à utiliser : NTFS (par défaut) ou UNIX.

    * Si vous souhaitez activer le chiffrement du protocole SMB3 pour le volume à double protocole, sélectionnez **Activer le chiffrement du protocole SMB3**.   

        Cette fonctionnalité active le chiffrement pour les données SMB3 à la volée uniquement. Elle ne chiffre pas les données NFSv3 à la volée. Les clients SMB qui n’utilisent pas le chiffrement SMB3 ne seront pas en mesure d’accéder à ce volume. Les données au repos sont chiffrées, indépendamment de ce paramètre. Pour plus d’informations, consultez le [Forum aux questions sur le chiffrement SMB](azure-netapp-files-faqs.md#smb-encryption-faqs). 

        La fonctionnalité de **Chiffrement de protocole SMB3** est actuellement en préversion. Si vous utilisez cette fonctionnalité pour la première fois, inscrivez-la avant de l’utiliser : 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBEncryption
        ```

        Vérifiez l’état d’inscription de la fonctionnalité : 

        > [!NOTE]
        > **RegistrationState** peut être à l’état `Registering` pendant plusieurs minutes, et jusqu’à 60 minutes, avant de passer à l’état `Registered`. Avant de continuer, attendez que l’état soit `Registered`.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBEncryption
        ```
        
        Vous pouvez également utiliser les [commandes Azure CLI](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` et `az feature show` pour inscrire la fonctionnalité et afficher l’état de l’inscription.  

    * En option, [configurez une stratégie d’exportation pour le volume](azure-netapp-files-configure-export-policy.md).

    ![Spécifier le double protocole](../media/azure-netapp-files/create-volume-protocol-dual.png)

4. Cliquez sur **Vérifier et créer** pour passer en revue les informations du volume. Cliquez ensuite sur **Créer** pour créer le volume.

    Le volume créé s’affiche dans la page Volumes. 
 
    Un volume hérite de l’abonnement, du groupe de ressources et des attributs d’emplacement de son pool de capacité. Vous pouvez suivre l’état du déploiement du volume dans le volet des notifications.

## <a name="allow-local-nfs-users-with-ldap-to-access-a-dual-protocol-volume"></a>Autoriser les utilisateurs NFS locaux avec LDAP à accéder à un volume à deux protocoles 

Vous pouvez autoriser les utilisateurs du client NFS local qui ne sont pas présents sur le serveur LDAP Windows à accéder à un volume à deux protocoles avec la fonctionnalité LDAP avec groupes étendus activés. Pour ce faire, activez l’option **Autoriser les utilisateurs NFS locaux avec LDAP**, comme suit :

1. Cliquez sur **Connexions Active Directory**.  Sur une connexion Active Directory existante, cliquez sur le menu contextuel (les trois points `…`), puis sélectionnez **Modifier**.  

2. Dans la fenêtre **Modifier les paramètres Active Directory** qui s’affiche, sélectionnez l’option **Autoriser les utilisateurs NFS locaux avec LDAP**.  

    ![Capture d’écran montrant l’option Autoriser les utilisateurs NFS locaux avec LDAP](../media/azure-netapp-files/allow-local-nfs-users-with-ldap.png)  


## <a name="manage-ldap-posix-attributes"></a>Gérer les attributs LDAP POSIX

Vous pouvez gérer les attributs POSIX, tels que l’identificateur d’utilisateur, le répertoire de base et d’autres valeurs à l’aide du composant logiciel enfichable MMC Utilisateurs et ordinateurs Active Directory.  L'exemple suivant illustre l’Éditeur d’attributs Active Directory :  

![Éditeur d’attributs Active Directory](../media/azure-netapp-files/active-directory-attribute-editor.png) 

Vous devez définir les attributs suivants pour les utilisateurs LDAP et les groupes LDAP : 
* Attributs requis pour les utilisateurs LDAP :   
    `uid: Alice`, `uidNumber: 139`, `gidNumber: 555`, `objectClass: posixAccount`
* Attributs requis pour les groupes LDAP :   
    `objectClass: posixGroup`, `gidNumber: 555`

## <a name="configure-the-nfs-client"></a>Configurer le client NFS 

Suivez les instructions de [Configurer un client NFS pour Azure NetApp Files](configure-nfs-clients.md) pour configurer le client NFS.  

## <a name="next-steps"></a>Étapes suivantes  

* [Configurer un client NFS pour Azure NetApp Files](configure-nfs-clients.md)
* [Résoudre les problèmes des volumes SMB ou à deux protocoles](troubleshoot-dual-protocol-volumes.md)
* [Résoudre les problèmes liés au volume LDAP](troubleshoot-ldap-volumes.md)
