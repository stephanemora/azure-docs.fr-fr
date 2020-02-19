---
title: Créer un volume SMB pour Azure NetApp Files | Microsoft Docs
description: Décrit comment créer un volume SMB pour Azure NetApp Files.
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
ms.date: 02/05/2020
ms.author: b-juche
ms.openlocfilehash: c65da771dd483b3a79785d4bec2b89cbeefca5c4
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049885"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Créer un volume SMB pour Azure NetApp Files

Azure NetApp Files prend en charge les volumes NFS et SMBv3. La consommation de capacité d’un volume est comptée par rapport à la capacité configurée de son pool. Cet article explique comment créer un volume SMBv3. Si vous souhaitez créer un volume NFS, consultez [Créer un volume NFS pour Azure NetApp Files](azure-netapp-files-create-volumes.md). 

## <a name="before-you-begin"></a>Avant de commencer 
Vous devez déjà avoir configuré un pool de capacité.   
[Configurer un pool de capacité](azure-netapp-files-set-up-capacity-pool.md)   
Un sous-réseau doit être délégué à Azure NetApp Files.  
[Déléguer un sous-réseau à Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Configuration requise pour les connexions Active Directory

 Vous devez créer des connexions Active Directory avant de créer un volume SMB. La configuration requise pour les connexions Active Directory est la suivante : 

* Le compte d’administrateur utilisé doit être en mesure de créer des comptes d’ordinateur dans le chemin d’accès de l’unité d’organisation (UO) que vous spécifiez.  

* Les ports appropriés doivent être ouverts sur le serveur Windows Active Directory (AD) applicable.  
    Les ports requis sont les suivants : 

    |     Service           |     Port     |     Protocol     |
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
    |    w32time            |    123       |    UDP           |

* La topologie de site pour les services de domaine Active Directory ciblés doit respecter les meilleures pratiques, en particulier le réseau virtuel Azure où Azure NetApp Files est déployé.  

    L’espace d’adressage du réseau virtuel où Azure NetApp Files est déployé doit être ajouté à un site Active Directory nouveau ou existant (où réside un contrôleur de domaine accessible par Azure NetApp Files). 

* Les serveurs DNS spécifiés doivent être accessibles à partir du [sous-réseau délégué](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) d’Azure NetApp Files.  

    Consultez [Consignes pour planifier un réseau Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) pour découvrir les topologies de réseau prises en charge.

    Les groupes de sécurité réseau et les pare-feu doivent avoir des règles configurées de manière appropriée pour autoriser les demandes de trafic Active Directory et DNS. 

* Le sous-réseau délégué Azure NetApp Files doit pouvoir accéder à tous les contrôleurs de domaine Active Directory Domain Services (AD DS) dans le domaine, y compris tous les contrôleurs de domaine locaux et distants. Si ce n'est pas le cas, une interruption de service peut se produire.  

    Si le sous-réseau délégué Azure NetApp Files n'est pas en mesure d'accéder à certains contrôleurs de domaine, vous pouvez envoyer une demande de support Azure afin de remplacer l’étendue **global** (par défaut) par **site**.  Azure NetApp Files doit uniquement communiquer avec les contrôleurs de domaine du site où réside l’espace d’adressage du sous-réseau délégué Azure NetApp Files.

    Consultez [Conception de la topologie du site](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology) sur les sites et services Active Directory. 
    
Pour plus d’informations sur AD, consultez les [Questions fréquentes sur SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs) pour Azure NetApp Files. 

## <a name="create-an-active-directory-connection"></a>Créer une connexion Active Directory

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

        Si vous utilisez Azure NetApp Files avec Azure Active Directory Domain Services, le chemin d’accès de l’unité d’organisation est `OU=AADDC Computers` lorsque vous configurez Active Directory pour votre compte NetApp.
        
    * Informations d’identification, y compris votre **nom d’utilisateur** et **mot de passe**

    ![Rejoindre Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Cliquez sur **Joindre**.  

    La connexion Active Directory créée s’affiche.

    ![Connexions Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

> [!NOTE] 
> Vous pouvez modifier les champs de nom d’utilisateur et de mot de passe après avoir enregistré la connexion Active Directory. Aucune autre valeur ne peut être modifiée après l’enregistrement de la connexion. Si vous avez besoin de modifier d’autres valeurs, vous devez d’abord supprimer tous les volumes SMB déployés, puis supprimer et recréer la connexion Active Directory.

## <a name="add-an-smb-volume"></a>Ajouter un volume SMB

1. Cliquez sur le panneau **Volumes** à partir du panneau Pools de capacités. 

    ![Accédez à Volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Cliquez sur **+ Ajouter un volume** pour créer un volume.  
    La fenêtre Créer un volume s’affiche.

3. Dans la fenêtre Créer un volume, cliquez sur **Créer** et renseignez les champs suivants :   
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

        Le réseau virtuel que vous spécifiez doit avoir un sous-réseau délégué à Azure NetApp Files. Le service Azure NetApp Files est accessible seulement à partir du même réseau virtuel ou d’un réseau virtuel qui figure dans la même région que le volume via le peering de réseau virtuel. Vous pouvez également accéder au volume à partir de votre réseau local via Express Route.   

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
* [Limites des ressources pour Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Questions fréquentes sur SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [En savoir plus sur l’intégration d’un réseau virtuel pour les services Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [Installation d’une nouvelle forêt Active Directory à l’aide d’Azure CLI](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
