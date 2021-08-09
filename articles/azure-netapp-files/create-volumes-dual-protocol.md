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
ms.date: 06/14/2021
ms.author: b-juche
ms.openlocfilehash: 92ba9ea8b63671112b6f9e16a984b50439c9d45d
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112072034"
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
* Créez un compte `pcuser` dans votre instance Active Directory (AD) et assurez-vous que le compte est activé. Ce compte servira d’utilisateur par défaut. Il sera utilisé pour mapper les utilisateurs UNIX afin d’accéder à un volume à deux protocoles configuré avec le style de sécurité NTFS. Le compte `pcuser` est utilisé uniquement lorsqu’il n’y a aucun utilisateur dans l’instance AD. Si un utilisateur possède un compte dans l’instance AD avec les attributs POSIX définis, ce compte sera celui utilisé pour l’authentification et il ne sera pas mappé au compte `pcuser`. 
* Créez une zone de recherche inversée sur le serveur DNS, puis ajoutez un enregistrement pointeur (PTR) de l’ordinateur hôte AD dans cette zone de recherche inversée. Sinon, la création du volume à deux protocoles échoue.
* L’option **Autoriser les utilisateurs NFS locaux avec LDAP** dans les connexions Active Directory vise à fournir un accès occasionnel et temporaire aux utilisateurs locaux. Lorsque cette option est activée, l’authentification et la recherche d’utilisateurs à partir du serveur LDAP cessent de fonctionner. Par conséquent, vous devez laisser cette option *désactivée* sur les connexions Active Directory, sauf lorsqu’un utilisateur local a besoin d’accéder à des volumes avec LDAP. Dans ce cas, vous devez désactiver cette option dès que l’accès de l’utilisateur local n’est plus nécessaire pour le volume. Consultez [Autoriser les utilisateurs NFS locaux avec LDAP à accéder à un volume à deux protocoles](#allow-local-nfs-users-with-ldap-to-access-a-dual-protocol-volume) pour gérer l’accès des utilisateurs locaux.
* Vérifiez que le client NFS est à jour et qu’il exécute les mises à jour les plus récentes du système d’exploitation.
* Les volumes à deux protocoles prennent en charge à la fois Active Directory Domain Services (ADDS) et Azure Active Directory Domain Services (AADDS). 
* Les volumes à deux protocoles ne prennent pas en charge l’utilisation de LDAP sur TLS avec AADDS. Consultez [Considérations relatives à LDAP sur TLS](configure-ldap-over-tls.md#considerations).
* La version de NFS utilisée par un volume à deux protocoles est NFSv3. Par conséquent, les considérations suivantes s'appliquent :
    * Le double protocole ne prend pas en charge les attributs étendus des ACL Windows `set/get` provenant des clients NFS.
    * Les clients NFS ne peuvent pas modifier les autorisations pour le style de sécurité NTFS, et les clients Windows ne peuvent pas modifier les autorisations pour les volumes à deux protocoles de type UNIX.   

        Le tableau suivant décrit les styles de sécurité et leurs effets :  
        
        | Style de sécurité    | Clients pouvant modifier des autorisations   | Autorisations que les clients peuvent utiliser  | Style de sécurité effectif obtenu    | Clients pouvant accéder aux fichiers     |
        |-  |-  |-  |-  |-  |
        | `Unix`    | NFS   | Bits en mode NFSv3   | UNIX  | NFS et Windows   |
        | `Ntfs`    | Windows   | ACL NTFS     | NTFS  |NFS et Windows|

    * La direction dans laquelle le mappage de noms se produit (Windows vers UNIX ou UNIX vers Windows) dépend du protocole utilisé et du style de sécurité appliqué au volume. Un client Windows nécessite toujours un mappage de noms Windows vers UNIX. L’attribution d’autorisations de révision à un utilisateur dépend du style de sécurité. Inversement, un client NFS doit uniquement utiliser un mappage de noms UNIX vers Windows si le style de sécurité NTFS est utilisé. 

        Le tableau suivant décrit les mappages de noms et les styles de sécurité :  
    
        |     Protocol          |     Style de sécurité          |     Direction du mappage de noms          |     Autorisations appliquées          |
        |-|-|-|-|
        |  SMB  |  `Unix`  |  Windows vers UNIX  |  UNIX (listes de contrôle d’accès en mode bits ou NFSv4.x)  |
        |  SMB  |  `Ntfs`  |  Windows vers UNIX  |  Listes de contrôle d’accès NTFS (basées sur l’accès de l’identificateur de sécurité Windows au partage)  |
        |  NFSv3  |  `Unix`  |  Aucun  |  UNIX (listes de contrôle d’accès en mode bits ou NFSv4.x) <br><br>  Notez que les listes de contrôle d’accès NFSv4.x peuvent être appliquées à l’aide d’un client d’administration NFSv4.x et être honorées par les clients NFSv3.  |
        |  NFS  |  `Ntfs`  |  UNIX vers Windows  |  Listes de contrôle d’accès NTFS (basées sur l’identificateur de sécurité de l’utilisateur Windows mappé)  |

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

    * Spécifiez un **chemin d’accès au volume** unique. Ce chemin est utilisé lorsque vous créez des cibles de montage. Les exigences concernant ce chemin sont les suivantes :  

        - Il doit être unique au sein de chaque sous-réseau de la région. 
        - Il doit commencer par un caractère alphabétique.
        - Il doit contenir uniquement des lettres, des chiffres ou des tirets (`-`). 
        - La longueur totale ne doit pas dépasser 80 caractères.

    * Spécifiez le **Style de sécurité** à utiliser : NTFS (par défaut) ou UNIX.

    * Si vous souhaitez activer le chiffrement du protocole SMB3 pour le volume à double protocole, sélectionnez **Activer le chiffrement du protocole SMB3**.   

        Cette fonctionnalité active le chiffrement pour les données SMB3 à la volée uniquement. Elle ne chiffre pas les données NFSv3 à la volée. Les clients SMB qui n’utilisent pas le chiffrement SMB3 ne seront pas en mesure d’accéder à ce volume. Les données au repos sont chiffrées, indépendamment de ce paramètre. Pour plus d’informations, consultez [Chiffrement SMB](azure-netapp-files-smb-performance.md#smb-encryption). 

        La fonctionnalité **Chiffrement de protocole SMB3** est actuellement en préversion. Si vous utilisez cette fonctionnalité pour la première fois, inscrivez-la avant de l’utiliser : 

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

L’option **Autoriser les utilisateurs NFS locaux avec LDAP** dans les connexions Active Directory permet aux utilisateurs locaux du client NFS qui ne sont pas présents sur le serveur LDAP de Windows d’accéder à un volume à deux protocoles dont le protocole LDAP avec groupes étendus est activé. 

> [!NOTE] 
> Avant d’activer cette option, vous devez comprendre les [considérations](#considerations). 

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
* Tous les utilisateurs et groupes doivent avoir un numéro d’identification unique, respectivement `uidNumber` et `gidNumber`. 

### <a name="access-active-directory-attribute-editor"></a>Accéder à l’éditeur d’attributs Active Directory 

Sur un système Windows, vous pouvez accéder à l’éditeur d’attributs Active Directory comme suit :  

1. Cliquez sur **Démarrer**, accédez à **Outils d’administration Windows**, puis cliquez sur **Utilisateurs et ordinateurs Active Directory** pour ouvrir la fenêtre Utilisateurs et ordinateurs Active Directory.  
2.  Cliquez sur le nom de domaine que vous souhaitez afficher, puis développez son contenu.  
3.  Pour afficher l’éditeur d’attributs avancé, activez l’option **Fonctionnalités avancées** dans le menu **Affichage** de la fenêtre Utilisateurs et ordinateurs Active Directory.   
    ![Capture d’écran montrant comment accéder au menu des fonctionnalités avancées de l’éditeur d’attributs.](../media/azure-netapp-files/attribute-editor-advanced-features.png) 
4. Double-cliquez sur **Utilisateurs** dans le volet gauche pour voir la liste des utilisateurs.
5. Double-cliquez sur un utilisateur particulier pour voir son onglet **Éditeur d’attributs**.
 
## <a name="configure-the-nfs-client"></a>Configurer le client NFS 

Suivez les instructions de [Configurer un client NFS pour Azure NetApp Files](configure-nfs-clients.md) pour configurer le client NFS.  

## <a name="next-steps"></a>Étapes suivantes  

* [Configurer un client NFS pour Azure NetApp Files](configure-nfs-clients.md)
* [Configurer ADDS LDAP sur TLS pour Azure NetApp Files](configure-ldap-over-tls.md)
* [Résoudre les problèmes des volumes SMB ou à deux protocoles](troubleshoot-dual-protocol-volumes.md)
* [Résoudre les problèmes liés au volume LDAP](troubleshoot-ldap-volumes.md)
