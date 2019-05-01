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
ms.date: 4/23/2019
ms.author: b-juche
ms.openlocfilehash: 53b2742cf92f3a3df346ba3557c718b8d7a11a4e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64719440"
---
# <a name="create-a-volume-for-azure-netapp-files"></a>Créer un volume pour Azure NetApp Files

Chaque pool de capacité peut disposer d'un maximum de 500 volumes. La consommation de capacité d’un volume est comptée par rapport à la capacité configurée de son pool. Les fichiers NetApp Azure prend en charge les volumes NFS et smbv3 fournis par. 

## <a name="before-you-begin"></a>Avant de commencer 
Vous devez déjà avoir configuré un pool de capacité.   
[Configurer un pool de capacité](azure-netapp-files-set-up-capacity-pool.md)   
Un sous-réseau doit être délégué à Azure NetApp Files.  
[Déléguer un sous-réseau à Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="create-an-nfs-volume"></a>Créer un volume NFS

1.  Cliquez sur le **Volumes** panneau à partir du Panneau de Pools de capacité. 

    ![Accédez aux Volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2.  Cliquez sur **+ Ajouter un volume** pour créer un volume.  
    Créer qu'une fenêtre de Volume s’affiche.

3.  Dans la création d’une fenêtre de Volume, cliquez sur **créer** et fournissent des informations pour les champs suivants :   
    * **Nom de volume**      
        Spécifiez le nom du volume que vous créez.   

        Un nom de volume doit être unique au sein de chaque pool de capacité. Il doit comprendre au moins trois caractères. Vous pouvez utiliser des caractères alphanumériques.

    * **Pool de capacité**  
        Spécifier le pool de capacité où vous souhaitez que le volume doit être créé.

    * **Quota**  
        Spécifiez la quantité de stockage logique allouée au volume.  

        Le champ **Quota disponible** indique la quantité d’espace inutilisé dans le pool de capacités choisi, que vous pouvez utiliser pour créer un volume. La taille du nouveau volume ne doit pas dépasser le quota disponible.  

    * **Réseau virtuel**  
        Spécifiez le réseau virtuel Azure à partir duquel vous voulez accéder au volume.  

        Le réseau virtuel que vous spécifiez doit avoir un sous-réseau délégué à Azure NetApp Files. Le service Azure NetApp Files est accessible seulement à partir du même réseau virtuel ou d’un sous-réseau qui se trouve dans la même région que le volume via le peering de réseau virtuel. Vous pouvez également accéder au volume à partir de votre réseau local via Express Route.   

    * **Sous-réseau**  
        Spécifiez le sous-réseau que vous souhaitez utiliser pour le volume.  
        Le sous-réseau que vous spécifiez doit être délégué à Azure NetApp Files. 
        
        Si vous n’avez pas délégué un sous-réseau, vous pouvez cliquer sur **Créer** sur la page Créer un volume. Ensuite, dans la page Créer un sous-réseau, fournissez les informations sur le sous-réseau, puis sélectionnez **Microsoft.NetApp/volumes** pour déléguer le sous-réseau à Azure NetApp Files. Dans chaque réseau virtuel, un seul sous-réseau peut être délégué à Azure Files de NetApp.   
 
        ![Créer un volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Créer un sous-réseau](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Cliquez sur **protocole**, puis sélectionnez **NFS** comme type de protocole pour le volume.   
    * Spécifiez le **chemin d’accès du fichier** qui sera utilisé pour créer le chemin d’exportation pour le nouveau volume. Le chemin d’exportation est utilisé pour monter le volume et y accéder.

        Le nom du chemin du fichier peut contenir seulement des lettres, des chiffres et des traits d’union (« - »). Il doit comprendre entre 16 et 40 caractères. 

        Le chemin d’accès du fichier doit être unique au sein de chaque abonnement et chaque région. 

    * Si vous le souhaitez, [configurer la stratégie d’exportation pour le volume NFS](azure-netapp-files-configure-export-policy.md)

    ![Spécifiez le protocole NFS](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

5. Cliquez sur **vérifier + créer** pour consulter les détails de volume.  Puis cliquez sur **créer** pour créer le volume NFS.

    Le volume que vous avez créé apparaît dans la page Volumes. 
 
    Un volume hérite de l’abonnement, du groupe de ressources et des attributs d’emplacement de son pool de capacité. Vous pouvez suivre l’état du déploiement du volume dans le volet des notifications.

## <a name="create-an-smb-volume"></a>Créer un volume SMB

Les fichiers NetApp Azure prend en charge les volumes de smbv3 fournis par. Vous devez créer des connexions Active Directory avant d’ajouter un volume SMB. 

### <a name="create-an-active-directory-connection"></a>Créer une connexion Active Directory

1. Vous assurer que le requiements suivant : 

    * Le compte d’administrateur que vous utilisez doit être en mesure de créer des comptes d’ordinateur dans le chemin d’accès de l’unité d’organisation (UO) que vous spécifiez.
    * Les ports appropriés doivent être ouverts sur le serveur Windows Active Directory (AD) applicables.  
        Les ports requis sont les suivantes : 

        |     de diffusion en continu           |     Port     |     Protocol     |
        |-----------------------|--------------|------------------|
        |    Services Web d’AD    |    9389      |    TCP           |
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


1. À partir de votre compte NetApp, cliquez sur **connexions Active Directory**, puis cliquez sur **joindre**.  

    ![Connexions Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. Dans la fenêtre de joindre un Active Directory, fournissez les informations suivantes :

    * **Le serveur DNS principal**   
        Il s’agit de l’adresse IP de contrôleur de domaine pour le favori Active Directory Domain Services pour une utilisation avec Azure Files de NetApp. 
    * **DNS secondaire**  
        Il s’agit de l’adresse IP de contrôleur de domaine pour le secondaire Active Directory Domain Services pour une utilisation avec Azure Files de NetApp. 
    * **Domaine**  
        Il s’agit du nom de domaine de vos Services de domaine Active Directory que vous souhaitez joindre.
    * **Préfixe de serveur (compte d’ordinateur) de SMB**  
        Il s’agit du préfixe d’affectation de noms pour le compte d’ordinateur dans Active Directory que les fichiers NetApp Azure utilisera pour la création de nouveaux comptes.

        Par exemple, si la norme d’affectation de noms que votre organisation utilise des serveurs de fichiers est NAS-01, 02 NAS..., NAS-045, puis que vous entrez « NAS » pour le préfixe. 

        Le service créera des comptes d’ordinateurs supplémentaires dans Active Directory en fonction des besoins.

    * **Chemin d’accès de l’unité d’organisation**  
        Il s’agit de l’unité d’organisation (UO) où les comptes d’ordinateurs serveurs SMB doit être créés dans le chemin d’accès LDAP. Autrement dit, unité d’organisation = de deuxième niveau, UO = de premier niveau. 
    * Informations d’identification, y compris votre **nom d’utilisateur** et **mot de passe**

    ![Joindre Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Cliquez sur **Joindre**.  

    La connexion Active Directory que vous avez créé s’affiche.

    ![Connexions Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

### <a name="add-an-smb-volume"></a>Ajouter un volume SMB

1. Cliquez sur le **Volumes** panneau à partir du Panneau de Pools de capacité. 

    ![Accédez aux Volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Cliquez sur **+ Ajouter un volume** pour créer un volume.  
    Créer qu'une fenêtre de Volume s’affiche.

3. Dans la création d’une fenêtre de Volume, cliquez sur **créer** et fournissent des informations pour les champs suivants :   
    * **Nom de volume**      
        Spécifiez le nom du volume que vous créez.   

        Un nom de volume doit être unique au sein de chaque pool de capacité. Il doit comprendre au moins trois caractères. Vous pouvez utiliser des caractères alphanumériques.

    * **Pool de capacité**  
        Spécifier le pool de capacité où vous souhaitez que le volume doit être créé.

    * **Quota**  
        Spécifiez la quantité de stockage logique allouée au volume.  

        Le champ **Quota disponible** indique la quantité d’espace inutilisé dans le pool de capacités choisi, que vous pouvez utiliser pour créer un volume. La taille du nouveau volume ne doit pas dépasser le quota disponible.  

    * **Réseau virtuel**  
        Spécifiez le réseau virtuel Azure à partir duquel vous voulez accéder au volume.  

        Le réseau virtuel que vous spécifiez doit avoir un sous-réseau délégué à Azure NetApp Files. Le service Azure NetApp Files est accessible seulement à partir du même réseau virtuel ou d’un sous-réseau qui se trouve dans la même région que le volume via le peering de réseau virtuel. Vous pouvez également accéder au volume à partir de votre réseau local via Express Route.   

    * **Sous-réseau**  
        Spécifiez le sous-réseau que vous souhaitez utiliser pour le volume.  
        Le sous-réseau que vous spécifiez doit être délégué à Azure NetApp Files. 
        
        Si vous n’avez pas délégué un sous-réseau, vous pouvez cliquer sur **Créer** sur la page Créer un volume. Ensuite, dans la page Créer un sous-réseau, fournissez les informations sur le sous-réseau, puis sélectionnez **Microsoft.NetApp/volumes** pour déléguer le sous-réseau à Azure NetApp Files. Dans chaque réseau virtuel, un seul sous-réseau peut être délégué à Azure Files de NetApp.   
 
        ![Créer un volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Créer un sous-réseau](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Cliquez sur **protocole** et complétez les informations suivantes :  
    * Sélectionnez **SMB** comme type de protocole pour le volume. 
    * Sélectionnez votre **Active Directory** connexion dans la liste déroulante.
    * Spécifiez le nom du volume partagé en **nom de partage**.

    ![Spécifiez le protocole SMB](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Cliquez sur **vérifier + créer** pour consulter les détails de volume.  Puis cliquez sur **créer** pour créer le volume SMB.

    Le volume que vous avez créé apparaît dans la page Volumes. 
 
    Un volume hérite de l’abonnement, du groupe de ressources et des attributs d’emplacement de son pool de capacité. Vous pouvez suivre l’état du déploiement du volume dans le volet des notifications.

## <a name="next-steps"></a>Étapes suivantes  

* [Monter ou démonter un volume pour les machines virtuelles Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Configurer la stratégie d’exportation pour un volume NFS](azure-netapp-files-configure-export-policy.md)
* [En savoir plus sur l’intégration d’un réseau virtuel pour les services Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
