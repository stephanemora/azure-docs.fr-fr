---
title: Configurer le chiffrement Kerberos NFSv4.1 pour Azure NetApp Files | Microsoft Docs
description: Décrit la procédure de configuration du chiffrement Kerberos NFSv4.1 pour Azure NetApp Files et l’impact sur les performances.
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
ms.date: 05/06/2021
ms.author: b-juche
ms.openlocfilehash: 2b920b7a5794e1ee56b8ccc2c1d3aef45a1b5fe2
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109483818"
---
# <a name="configure-nfsv41-kerberos-encryption-for-azure-netapp-files"></a>Configurer le chiffrement Kerberos NFSv4.1 pour Azure NetApp Files

Azure NetApp Files prend en charge le chiffrement client NFS dans les modes Kerberos (krb5, krb5i, et krb5p) avec le chiffrement AES-256. Cet article décrit les configurations requises pour l’utilisation d’un volume NFSv4.1 avec le chiffrement Kerberos.

## <a name="requirements"></a>Spécifications

La configuration requise suivante s’applique au chiffrement client NFSv4.1 : 

* Connexion Active Directory Domain Services (AD DS) ou Azure Active Directory Domain Services (AADDS) pour faciliter la gestion des tickets Kerberos 
* Création d’un enregistrement DNS A/PTR pour les adresses IP du client et du serveur NFS Azure NetApp Files
* Un client Linux  
    Cet article propose des conseils pour les clients RHEL et Ubuntu.  Les autres clients fonctionneront avec des étapes de configuration similaires. 
* Accès au serveur NTP  
    Vous pouvez utiliser un des contrôleurs de domaine Active Directory (AD DC) les plus utilisés.

## <a name="create-an-nfs-kerberos-volume"></a>Créer un volume NFS Kerberos

1.  Suivez les étapes décrites dans [Créer un volume NFS pour Azure NetApp Files](azure-netapp-files-create-volumes.md) pour créer le volume NFSv4.1.   

    Sur la page Créer un volume, définissez la version NFS sur **NFSv4.1** et définissez Kerberos sur **Activé**.

    > [!IMPORTANT] 
    > Vous ne pouvez pas modifier la sélection d’activation Kerberos après la création du volume.

    ![Créer un volume Kerberos NFSv4.1](../media/azure-netapp-files/create-kerberos-volume.png)  

2. Sélectionnez **Exporter la stratégie** pour correspondre à l’option (Kerberos 5, Kerberos 5i ou Kerberos 5p) de niveau d’accès et de sécurité voulu pour le volume.   

    Pour connaitre l’impact de Kerberos sur les performances, consultez [Impact de Kerberos sur les performances sur NFSv4.1](#kerberos_performance).  

    Vous pouvez également modifier les méthodes de sécurité Kerberos concernant le volume en cliquant sur Exporter la stratégie dans le volet de navigation Azure NetApp Files.

3.  Cliquez sur **Vérifier + Créer** pour créer le volume NFSv4.1.

## <a name="configure-the-azure-portal"></a>Configurer le portail Azure 

1.  Suivez les instructions dans [Créer une connexion Active Directory](create-active-directory-connections.md).  

    Kerberos requiert la création d’au moins un compte d’ordinateur dans Active Directory. Les informations de compte que vous fournissez sont utilisées pour créer les comptes pour les volumes SMB *et* Kerberos NFSv4.1. Cet ordinateur est automatiquement créé lors de la création du volume.

2.  Sous **Domaine Kerberos**, entrez le **nom du serveur AD** et l’adresse **IP KDC**.

    Le serveur AD et l’adresse IP du KDC peuvent être le même serveur. Ces informations permettent de créer le compte d’ordinateur SPN utilisé par Azure NetApp Files. Une fois le compte d’ordinateur créé, Azure NetApp Files utilisera les enregistrements du serveur DNS pour localiser des serveurs KDC supplémentaires si nécessaire. 

    ![Domaine Kerberos](../media/azure-netapp-files/kerberos-realm.png)
 
3.  Cliquez sur **Joindre** pour enregistrer la configuration.

## <a name="configure-active-directory-connection"></a>Configurer la connexion Azure Active Directory 

La configuration de Kerberos NFSv4.1 crée deux comptes d’ordinateur dans Active Directory :
* Un compte d’ordinateur pour les partages SMB
* Un compte d’ordinateur pour NFSv4.1 : vous pouvez identifier ce compte par le biais du préfixe `NFS-`. 

Après la création du premier volume Kerberos NFSv4.1, définissez le type de chiffrement pour le compte d’ordinateur à l’aide de la commande PowerShell suivante :

`Set-ADComputer $NFSCOMPUTERACCOUNT -KerberosEncryptionType AES256`

## <a name="configure-the-nfs-client"></a>Configurer le client NFS 

Suivez les instructions dans [Configurer un client NFS pour Azure NetApp Files](configure-nfs-clients.md) pour configurer le client NFS.  

## <a name="mount-the-nfs-kerberos-volume"></a><a name="kerberos_mount"></a>Monter un volume NFS Kerberos

1. Sur la page **Volumes**, sélectionnez le volume NFS que vous souhaitez monter.

2. Sélectionnez **Instructions de montage** à partir du volume pour afficher les instructions.

    Par exemple : 

    ![Instructions de montage pour les volumes Kerberos](../media/azure-netapp-files/mount-instructions-kerberos-volume.png)  

3. Créez le répertoire (point de montage) pour le nouveau volume.  

4. Définissez le type de chiffrement par défaut sur AES 256 pour le compte d’ordinateur :  
    `Set-ADComputer $NFSCOMPUTERACCOUNT -KerberosEncryptionType AES256 -Credential $ANFSERVICEACCOUNT`

    * Vous ne devez exécuter cette commande qu’une seule fois pour chaque compte d’ordinateur.
    * Vous pouvez exécuter cette commande à partir d’un contrôleur de domaine ou d’un PC sur lequel [RSAT](https://support.microsoft.com/help/2693643/remote-server-administration-tools-rsat-for-windows-operating-systems) est installé. 
    * La variable `$NFSCOMPUTERACCOUNT` est le compte d’ordinateur créé dans Active Directory lorsque vous déployez le volume Kerberos. Il s’agit du compte avec le préfixe `NFS-`. 
    * La variable `$ANFSERVICEACCOUNT` est un compte d'utilisateur Active Directory non privilégié avec contrôles délégués sur l’unité d'organisation dans laquelle le compte d'ordinateur a été créé. 

5. Montez le volume sur l’hôte : 

    `sudo mount -t nfs -o sec=krb5p,rw,hard,rsize=1048576,wsize=1048576,vers=4.1,tcp $ANFEXPORT $ANFMOUNTPOINT`

    * La variable `$ANFEXPORT` est le chemin d’accès `host:/export` trouvé dans les instructions de montage.
    * La variable `$ANFMOUNTPOINT` est le dossier créé par l’utilisateur sur l’hôte Linux.

## <a name="performance-impact-of-kerberos-on-nfsv41"></a><a name="kerberos_performance"></a>Impact de Kerberos sur les performances de NFSv4.1 

Vous devez comprendre les options de sécurité disponibles pour les volumes NFSv4.1, les vecteurs de performances testés et l’impact attendu de Kerberos sur les performances. Pour plus d’informations, consultez [Impact de Kerberos sur les performances des volumes NFSv4.1](performance-impact-kerberos.md).  

## <a name="next-steps"></a>Étapes suivantes  

* [Impact de Kerberos sur les performances des volumes NFSv4.1](performance-impact-kerberos.md)
* [Résoudre les problèmes de volume Kerberos NFSv4.1](troubleshoot-nfsv41-kerberos-volumes.md)
* [Questions fréquentes (FAQ) sur Azure NetApp Files](azure-netapp-files-faqs.md)
* [Créer un volume NFS pour Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Créer une connexion Active Directory](create-active-directory-connections.md)
* [Configurer un client NFS pour Azure NetApp Files](configure-nfs-clients.md) 
