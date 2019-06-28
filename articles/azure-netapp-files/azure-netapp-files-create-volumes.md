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
ms.date: 6/6/2019
ms.author: b-juche
ms.openlocfilehash: 657bacc153b5721d5a9f34792eaf4796cb477755
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808883"
---
# <a name="create-a-volume-for-azure-netapp-files"></a>Créer un volume pour Azure NetApp Files

Chaque pool de capacité peut disposer d'un maximum de 500 volumes. La consommation de capacité d’un volume est comptée par rapport à la capacité configurée de son pool. Azure NetApp Files prend en charge les volumes NFS et SMBv3. 

## <a name="before-you-begin"></a>Avant de commencer 
Vous devez déjà avoir configuré un pool de capacité.   
[Configurer un pool de capacité](azure-netapp-files-set-up-capacity-pool.md)   
Un sous-réseau doit être délégué à Azure NetApp Files.  
[Déléguer un sous-réseau à Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="create-an-nfs-volume"></a>Créer un volume NFS

1.  Cliquez sur le panneau **Volumes** à partir du panneau Pools de capacités. 

    ![Accédez à Volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2.  Cliquez sur **+ Ajouter un volume** pour créer un volume.  
    La fenêtre Créer un volume s’affiche.

3.  Dans la fenêtre Créer un volume, cliquez sur **Créer** et renseignez les champs suivants :   
    * **Nom du volume**      
        Spécifiez le nom du volume que vous créez.   

        Un nom de volume doit être unique au sein de chaque pool de capacité. Il doit comprendre au moins trois caractères. Vous pouvez utiliser tous les caractères alphanumériques.

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

4. Cliquez sur **Protocole**, puis sélectionnez **NFS** comme type de protocole pour le volume.   
    * Spécifiez le **chemin du fichier** à utiliser pour créer le chemin d’exportation du nouveau volume. Le chemin d’exportation est utilisé pour monter le volume et y accéder.

        Le nom du chemin du fichier peut contenir seulement des lettres, des chiffres et des traits d’union (« - »). Il doit comprendre entre 16 et 40 caractères. 

        Le chemin d’accès du fichier doit être unique au sein de chaque abonnement et chaque région. 

    * Le cas échéant, [configurez une stratégie d’exportation pour le volume NFS](azure-netapp-files-configure-export-policy.md).

    ![Spécifier le protocole NFS](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

5. Cliquez sur **Vérifier et créer** pour passer en revue les informations du volume.  Cliquez ensuite sur **Créer** pour créer le volume NFS.

    Le volume créé s’affiche dans la page Volumes. 
 
    Un volume hérite de l’abonnement, du groupe de ressources et des attributs d’emplacement de son pool de capacité. Vous pouvez suivre l’état du déploiement du volume dans le volet des notifications.

## <a name="create-an-smb-volume"></a>Créer un volume SMB

Azure NetApp Files prend en charge les volumes SMBv3. Vous devez créer des connexions Active Directory avant d’ajouter un volume SMB. 

### <a name="requirements-for-active-directory-connections"></a>Configuration requise pour les connexions Active Directory

 La configuration requise pour les connexions Active Directory est la suivante : 

* Le compte d’administrateur utilisé doit être en mesure de créer des comptes d’ordinateur dans le chemin d’accès de l’unité d’organisation (UO) que vous spécifiez.  

* Les ports appropriés doivent être ouverts sur le serveur Windows Active Directory (AD) applicable.  
    Les ports requis sont les suivants : 

    |     de diffusion en continu           |     Port     |     Protocole     |
    |-----------------------|--------------|------------------|
    |    Services web AD    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    N/A       |    Réponse à écho    |
    |    Kerberos           |    464       |    TCP           |
    |    Kerberos           |    464       |    UDP           |
    |    Kerberos           |    88        |    TCP           |
    |    Kerberos           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    Nom NetBIOS       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    UDP           |
    |    LDAP sécurisé        |    636       |    TCP           |
    |    LDAP sécurisé        |    3269      |    TCP           |
    |    w32time            |    123       |    UDP           |

### <a name="create-an-active-directory-connection"></a>Créer une connexion Active Directory

1. À partir de votre compte NetApp, cliquez sur **Connexions Active Directory**, puis sur **Rejoindre**.  

    ![Connexions Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. Dans la fenêtre Rejoindre Active Directory, entrez les informations suivantes :

    * **DNS principal**  
        Il s’agit du serveur DNS requis pour la jonction de domaine Active Directory et les opérations d’authentification SMB. 
    * **DNS secondaire**   
        Serveur DNS secondaire garantissant la redondance des services de noms. 
    * **Domaine**  
        Nom de domaine des Active Directory Domain Services que vous souhaitez rejoindre.
    * **Préfixe de serveur SMB (compte d’ordinateur)**  
        Préfixe de nom pour le compte d’ordinateur dans Active Directory que Azure NetApp Files utilise pour la création de nouveaux comptes.

        Par exemple, si la norme d’affectation de noms utilisée par votre organisation pour les serveurs de fichiers est NAS-01, NAS-02..., NAS-045, entrez « NAS » comme préfixe. 

        Le service crée des comptes d’ordinateurs supplémentaires dans Active Directory en fonction des besoins.

    * **Chemin d’accès de l’unité d’organisation**  
        Chemin d’accès de l’unité d’organisation (UO) où les comptes de machine du serveur SMB sont créés. Autrement dit, OU=second level, OU=first level. 
    * Informations d’identification, y compris votre **nom d’utilisateur** et **mot de passe**

    ![Rejoindre Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Cliquez sur **Joindre**.  

    La connexion Active Directory créée s’affiche.

    ![Connexions Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

### <a name="add-an-smb-volume"></a>Ajouter un volume SMB

1. Cliquez sur le panneau **Volumes** à partir du panneau Pools de capacités. 

    ![Accédez à Volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Cliquez sur **+ Ajouter un volume** pour créer un volume.  
    La fenêtre Créer un volume s’affiche.

3. Dans la fenêtre Créer un volume, cliquez sur **Créer** et renseignez les champs suivants :   
    * **Nom du volume**      
        Spécifiez le nom du volume que vous créez.   

        Un nom de volume doit être unique au sein de chaque pool de capacité. Il doit comprendre au moins trois caractères. Vous pouvez utiliser tous les caractères alphanumériques.

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

4. Cliquez sur **Protocole** et renseignez les informations suivantes :  
    * Sélectionnez **SMB** comme type de protocole pour le volume. 
    * Sélectionnez votre connexion **Active Directory** dans la liste déroulante.
    * Spécifiez le nom du volume partagé dans **Nom de partage**.

    ![Spécifier le protocole SMB](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Cliquez sur **Vérifier et créer** pour passer en revue les informations du volume.  Cliquez ensuite sur **Créer** pour créer le volume SMB.

    Le volume créé s’affiche dans la page Volumes. 
 
    Un volume hérite de l’abonnement, du groupe de ressources et des attributs d’emplacement de son pool de capacité. Vous pouvez suivre l’état du déploiement du volume dans le volet des notifications.

## <a name="next-steps"></a>Étapes suivantes  

* [Monter ou démonter un volume pour des machines virtuelles Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Configurer une stratégie d’exportation pour un volume NFS](azure-netapp-files-configure-export-policy.md)
* [En savoir plus sur l’intégration d’un réseau virtuel pour les services Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
