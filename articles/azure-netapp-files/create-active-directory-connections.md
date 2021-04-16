---
title: Créer et gérer des connexions Active Directory pour Azure NetApp Files | Microsoft Docs
description: Cet article explique comment créer et gérer des connexions Active Directory pour Azure NetApp Files.
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
ms.date: 03/24/2021
ms.author: b-juche
ms.openlocfilehash: d238b566c1286b9b765fb574cd72ee68ccf4b4a7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048372"
---
# <a name="create-and-manage-active-directory-connections-for-azure-netapp-files"></a>Créer et gérer des connexions Active Directory pour Azure NetApp Files

Plusieurs fonctionnalités d’Azure NetApp Files requièrent une connexion Active Directory.  Par exemple, vous devez disposer d’une connexion Active Directory pour pouvoir créer un [volume SMB](azure-netapp-files-create-volumes-smb.md) ou un [volume à double protocole](create-volumes-dual-protocol.md).  Cet article explique comment créer et gérer des connexions Active Directory pour Azure NetApp Files.

## <a name="before-you-begin"></a>Avant de commencer  

Vous devez déjà avoir configuré un pool de capacité.   
[Configurer un pool de capacité](azure-netapp-files-set-up-capacity-pool.md)   
Un sous-réseau doit être délégué à Azure NetApp Files.  
[Déléguer un sous-réseau à Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Configuration requise pour les connexions Active Directory

 La configuration requise pour les connexions Active Directory est la suivante : 

* Le compte d’administrateur que vous utilisez doit avoir la capacité de créer des comptes d’ordinateur dans le chemin de l’unité d’organisation (UO) que vous allez spécifier.  

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

* La topologie de site pour les services Active Directory Domain Services ciblés doit respecter les instructions, en particulier le réseau virtuel Azure où Azure NetApp Files est déployé.  

    L’espace d’adressage du réseau virtuel où Azure NetApp Files est déployé doit être ajouté à un site Active Directory nouveau ou existant (où se trouve un contrôleur de domaine accessible par Azure NetApp Files). 

* Les serveurs DNS spécifiés doivent être accessibles à partir du [sous-réseau délégué](./azure-netapp-files-delegate-subnet.md) d’Azure NetApp Files.  

    Consultez [Consignes pour planifier un réseau Azure NetApp Files](./azure-netapp-files-network-topologies.md) pour découvrir les topologies de réseau prises en charge.

    Les groupes de sécurité réseau et les pare-feu doivent avoir des règles configurées de manière appropriée pour autoriser les demandes de trafic Active Directory et DNS. 

* Le sous-réseau délégué Azure NetApp Files doit pouvoir accéder à tous les contrôleurs de domaine Active Directory Domain Services (AD DS) dans le domaine, y compris tous les contrôleurs de domaine locaux et distants. Si ce n'est pas le cas, une interruption de service peut se produire.  

    Si vous avez des contrôleurs de domaine que le sous-réseau délégué Azure NetApp Files ne peut pas atteindre, vous pouvez spécifier un site Active Directory au moment de créer la connexion Active Directory.  Azure NetApp Files doit communiquer uniquement avec les contrôleurs de domaine du site où se trouve l’espace d’adressage du sous-réseau délégué Azure NetApp Files.

    Consultez [Conception de la topologie du site](/windows-server/identity/ad-ds/plan/designing-the-site-topology) sur les sites et services Active Directory. 
    
* Vous pouvez activer le chiffrement AES une authentification AD en cochant la case **Chiffrement AES** dans la fenêtre [Rejoindre Active Directory](#create-an-active-directory-connection). Azure NetApp Files prend en charge les types de chiffrement DES, Kerberos AES 128 et Kerberos AES 256 (du moins sécurisé au plus sécurisé). Si vous activez le chiffrement AES, les informations d’identification de l’utilisateur utilisées pour rejoindre Active Directory doivent avoir l’option de compte correspondante la plus élevée activée correspondant aux fonctionnalités activées pour votre instance Active Directory.    

    Par exemple, si votre instance Active Directory n’a que la fonction AES-128, vous devez activer l’option de compte AES-128 pour les informations d’identification de l’utilisateur. Si votre instance Active Directory possède la fonction AES-256, vous devez activer l’option de compte AES-256 (qui prend également en charge AES-128). Si votre instance Active Directory n’a pas de fonctionnalité de chiffrement Kerberos, Azure NetApp Files utilise DES par défaut.  

    Vous pouvez activer les options de compte dans les propriétés des utilisateurs et ordinateurs Active Directory dans la console de gestion Microsoft (MMC) :   

    ![MMC des utilisateurs et ordinateurs Active Directory](../media/azure-netapp-files/ad-users-computers-mmc.png)

* Azure NetApp Files prend en charge la [signature LDAP](/troubleshoot/windows-server/identity/enable-ldap-signing-in-windows-server), qui permet la transmission sécurisée du trafic LDAP entre le service Azure NetApp Files et les [contrôleurs de domaine Active Directory](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) ciblés. Si vous suivez les conseils de l’avis de sécurite Microsoft [ADV190023](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023) pour la signature LDAP, vous devez activer la fonctionnalité de signature LDAP dans Azure NetApp Files en activant la case à cocher **Signature LDAP** dans la fenêtre [Rejoindre Active Directory](#create-an-active-directory-connection). 

    La seule configuration de [Liaison de canal LDAP](https://support.microsoft.com/help/4034879/how-to-add-the-ldapenforcechannelbinding-registry-entry) n’a aucun effet sur le service Azure NetApp Files. Toutefois, si vous utilisez à la fois la liaison de canal LDAP et le LDAP sécurisé (par exemple, LDAPS ou `start_tls`), la création du volume SMB échouera.

* Pour les DNS non intégrés avec Active Directory, vous devez ajouter un enregistrement DNS A/PTR pour permettre à Azure NetApp Files de fonctionner à l’aide d’un « nom convivial ». 

## <a name="decide-which-domain-services-to-use"></a>Déterminer quels services de domaine utiliser 

Azure NetApp Files prend en charge à la fois [Active Directory Domain Services](/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) (ADDS) et Azure Active Directory Domain Services (AADDS) pour les connexions AD.  Avant de créer une connexion AD, vous devez choisir entre ADDS et AADDS.  

Pour plus d’informations, consultez [Comparer les services Active Directory Domain Services auto-gérés, Azure Active Directory et Azure Active Directory Domain Services managés](../active-directory-domain-services/compare-identity-solutions.md). 

### <a name="active-directory-domain-services"></a>Active Directory Domain Services

Vous pouvez utiliser l’étendue [Sites et services Active Directory](/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) préférée pour Azure NetApp Files. Cette option autorise les accès en lecture et écriture aux contrôleurs de domaine Active Directory Domain Services (ADDS) [accessibles à Azure NetApp Files](azure-netapp-files-network-topologies.md). Par ailleurs, elle empêche le service de communiquer avec les contrôleurs de domaine qui ne se trouvent pas dans le site Sites et services Active Directory spécifié. 

Si vous utilisez ADDS, vous pouvez obtenir le nom de votre site auprès du groupe d’administration de votre organisation qui est en charge d’Active Directory Domain Services. L’exemple ci-dessous illustre le plug-in Sites et services Active Directory dans lequel figure le nom du site : 

![Sites et services Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-services.png)

Quand vous configurez une connexion AD pour Azure NetApp Files, vous devez spécifier le nom du site compris dans l’étendue dans le champ **Nom du site AD**.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 

Pour obtenir des recommandations et des informations sur la configuration d’Azure Active Directory Domain Services (AADDS), consultez la [documentation Azure AD Domain Services](../active-directory-domain-services/index.yml).

Des considérations supplémentaires liées à AADDS s’appliquent à Azure NetApp Files : 

* Vérifiez que le réseau virtuel ou le sous-réseau dans lequel AADDS est déployé se trouve dans la même région Azure que le déploiement Azure NetApp Files.
* Si vous utilisez un autre réseau virtuel dans la région où Azure NetApp Files est déployé, vous devez créer un appairage entre les deux réseaux virtuels.
* Azure NetApp Files prend en charge les types `user` et `resource forest`.
* Pour le type de synchronisation, vous pouvez sélectionner `All` ou `Scoped`.   
    Si vous sélectionnez `Scoped`, vérifiez que le groupe Azure AD sélectionné pour accéder aux partages SMB convient.  Si vous n’en êtes pas certain, vous pouvez utiliser le type de synchronisation `All`.
* L’utilisation de la référence SKU Entreprise ou Premium est obligatoire. La référence SKU Standard n’est pas prise en charge.

Quand vous créez une connexion Active Directory, notez les détails suivants concernant AADDS :

* Les informations **DNS principal**, **DNS secondaire** et **Nom de domaine AD DNS** se trouvent dans le menu AADDS.  
Pour les serveurs DNS, deux adresses IP sont utilisées pour configurer la connexion Active Directory. 
* Le **chemin de l’unité d’organisation** est `OU=AADDC Computers`.  
Ce paramètre est configuré dans **Active Directory Connections** (Connexions Active Directory) en dessous de **NetApp Account** (Compte NetApp) :

  ![Chemin de l’unité d’organisation](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* Les informations d’identification de **Username** (Nom d’utilisateur) peuvent être celles de n’importe quel utilisateur membre du groupe Azure AD **Azure AD DC Administrators** (Administrateurs Azure AD DC).


## <a name="create-an-active-directory-connection"></a>Créer une connexion Active Directory

1. À partir de votre compte NetApp, cliquez sur **Connexions Active Directory**, puis sur **Rejoindre**.  

    ![Connexions Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. Dans la fenêtre Joindre Active Directory, entrez les informations suivantes, en fonction des services de domaine que vous voulez utiliser :  

    Pour obtenir des informations propres aux services de domaine que vous utilisez, consultez [Déterminer quels services de domaine utiliser](#decide-which-domain-services-to-use). 

    * **DNS principal**  
        Il s’agit du serveur DNS requis pour la jonction de domaine Active Directory et les opérations d’authentification SMB. 
    * **DNS secondaire**   
        Serveur DNS secondaire garantissant la redondance des services de noms. 
    * **Nom de domaine DNS AD**  
        Nom de domaine des Active Directory Domain Services que vous souhaitez rejoindre.
    * **Nom de site Active Directory**  
        Il s’agit du nom du site auquel se limitera la détection du contrôleur de domaine. Celui-ci doit correspondre au nom du site dans Sites et services Active Directory.
    * **Préfixe de serveur SMB (compte d’ordinateur)**  
        Préfixe de nom pour le compte d’ordinateur dans Active Directory que Azure NetApp Files utilise pour la création de nouveaux comptes.

        Par exemple, si la convention de nommage utilisée par votre organisation pour les serveurs de fichiers est NAS-01, NAS-02..., NAS-045, entrez « NAS » comme préfixe. 

        Le service crée des comptes d’ordinateurs supplémentaires dans Active Directory en fonction des besoins.

        > [!IMPORTANT] 
        > Le fait de renommer le préfixe de serveur SMB après la création de la connexion Active Directory entraîne une interruption. Vous devrez remonter les partages SMB existants après avoir renommé le préfixe de serveur SMB.

    * **Chemin d’accès de l’unité d’organisation**  
        Chemin d’accès de l’unité d’organisation (UO) où les comptes de machine du serveur SMB sont créés. Autrement dit, OU=second level, OU=first level. 

        Si vous utilisez Azure NetApp Files avec Azure Active Directory Domain Services, le chemin d’accès de l’unité d’organisation est `OU=AADDC Computers` lorsque vous configurez Active Directory pour votre compte NetApp.

        ![Rejoindre Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

    * **Chiffrement AES**   
        Sélectionnez cette case à cocher si vous voulez activer le chiffrement AES pour un volume SMB. Voir [Configuration requise pour les connexions Active Directory](#requirements-for-active-directory-connections) pour les exigences. 

        ![Chiffrement AES Active Directory](../media/azure-netapp-files/active-directory-aes-encryption.png)

        La fonctionnalité de **Chiffrement AES** est actuellement en préversion. Si vous utilisez cette fonctionnalité pour la première fois, inscrivez-la avant de l’utiliser : 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```

        Vérifiez l’état d’inscription de la fonctionnalité : 

        > [!NOTE]
        > **RegistrationState** peut être à l’état `Registering` pendant plusieurs minutes, et jusqu’à 60 minutes, avant de passer à l’état `Registered`. Avant de continuer, attendez que l’état soit `Registered`.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```
        
        Vous pouvez également utiliser les [commandes Azure CLI](/cli/azure/feature) `az feature register` et `az feature show` pour inscrire la fonctionnalité et afficher l’état de l’inscription. 

    * **Signature LDAP**   
        Cochez cette case pour activer la signature LDAP. Cette fonctionnalité permet de sécuriser les recherches LDAP entre le service Azure NetApp Files et les [contrôleurs de domaine Active Directory Domain Services](/windows/win32/ad/active-directory-domain-services) spécifiés par l’utilisateur. Pour plus d’informations, consultez [ADV190023 | Instructions de Microsoft pour activer la liaison de canal LDAP et la signature LDAP](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).  

        ![Signature LDAP Active Directory](../media/azure-netapp-files/active-directory-ldap-signing.png) 

        Actuellement, la fonctionnalité **Signature LDAP** est uniquement disponible en tant que préversion. Si vous utilisez cette fonctionnalité pour la première fois, inscrivez-la avant de l’utiliser : 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```

        Vérifiez l’état d’inscription de la fonctionnalité : 

        > [!NOTE]
        > **RegistrationState** peut être à l’état `Registering` pendant plusieurs minutes, et jusqu’à 60 minutes, avant de passer à l’état `Registered`. Avant de continuer, attendez que l’état soit `Registered`.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```
        
        Vous pouvez également utiliser les [commandes Azure CLI](/cli/azure/feature) `az feature register` et `az feature show` pour inscrire la fonctionnalité et afficher l’état de l’inscription. 

     * **Utilisateurs des privilèges de sécurité**   <!-- SMB CA share feature -->   
        Vous pouvez accorder des privilèges de sécurité ( `SeSecurityPrivilege` ) aux utilisateurs qui requièrent des privilèges élevés pour accéder aux volumes de Azure NetApp Files. Les comptes utilisateurs spécifiés auront l’autorisation d’exécuter certaines actions sur les partages SMB Azure NetApp Files qui requièrent que les privilèges de sécurité ne soient pas attribués par défaut aux utilisateurs de domaine.   

        Par exemple, les comptes d’utilisateur utilisés pour installer SQL Server dans certains scénarios doivent disposer d’un privilège de sécurité élevé. Si vous utilisez un compte non-administrateur (domaine) pour installer SQL Server et que le compte ne dispose pas du privilège de sécurité attribué, vous devez ajouter des privilèges de sécurité au compte.  

        > [!IMPORTANT]
        > Le compte de domaine utilisé pour l’installation de SQL Server doit déjà exister avant que vous ne l’ajoutiez au champ **Utilisateurs du privilège de sécurité**. Lorsque vous ajoutez le compte du programme d’installation SQL Server aux **Utilisateurs des privilèges de sécurité**, le service Azure NetApp Files peut valider le compte en contactant le contrôleur de domaine. La commande peut échouer si elle ne parvient pas à contacter le contrôleur de domaine.  

        Pour plus d’informations sur `SeSecurityPrivilege` et SQL Server, consultez [L’installation SQL Server échoue si le compte d’installation ne dispose pas de certains droits d’utilisateur](/troubleshoot/sql/install/installation-fails-if-remove-user-right).

        ![Capture d’écran montrant la boîte Utilisateurs de privilèges de sécurité de la fenêtre de connexions Active Directory.](../media/azure-netapp-files/security-privilege-users.png) 

     * **Utilisateurs de stratégie de sauvegarde**  
        Vous pouvez inclure des comptes supplémentaires qui requièrent des privilèges élevés sur le compte d’ordinateur créé pour une utilisation avec Azure NetApp Files. Les comptes spécifiés seront autorisés à modifier les autorisations NTFS au niveau du fichier ou du dossier. Par exemple, vous pouvez spécifier un compte de service non privilégié utilisé pour la migration des données vers un partage de fichiers SMB dans Azure NetApp Files.  

        ![Utilisateurs de stratégie de sauvegarde Active Directory](../media/azure-netapp-files/active-directory-backup-policy-users.png)

        La fonctionnalité **Utilisateurs de stratégie de sauvegarde** est actuellement en préversion. Si vous utilisez cette fonctionnalité pour la première fois, inscrivez-la avant de l’utiliser : 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```

        Vérifiez l’état d’inscription de la fonctionnalité : 

        > [!NOTE]
        > **RegistrationState** peut être à l’état `Registering` pendant plusieurs minutes, et jusqu’à 60 minutes, avant de passer à l’état `Registered`. Avant de continuer, attendez que l’état soit `Registered`.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```
        
        Vous pouvez également utiliser les [commandes Azure CLI](/cli/azure/feature) `az feature register` et `az feature show` pour inscrire la fonctionnalité et afficher l’état de l’inscription. 

    * Informations d’identification, y compris votre **nom d’utilisateur** et **mot de passe**

        ![Informations d’identification Active Directory](../media/azure-netapp-files/active-directory-credentials.png)

3. Cliquez sur **Joindre**.  

    La connexion Active Directory créée s’affiche.

    ![Connexions Active Directory créées](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

## <a name="next-steps"></a>Étapes suivantes  

* [Créer un volume SMB](azure-netapp-files-create-volumes-smb.md)
* [Créer un volume à deux protocoles](create-volumes-dual-protocol.md)
* [Configurer le chiffrement Kerberos NFSv4.1](configure-kerberos-encryption.md)
* [Installation d’une nouvelle forêt Active Directory à l’aide d’Azure CLI](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm) 
