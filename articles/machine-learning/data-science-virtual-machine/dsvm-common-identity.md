---
title: Configurer une identité commune pour la machine virtuelle DSVM - Azure | Microsoft Docs
description: Configurez une identité commune dans les environnements DSVM en équipe d’entreprise.
keywords: formation approfondie, IA, outils de science des données, machine virtuelle de science des données, analyse géospatiale, processus de science des données en équipe
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 431d3079c0d942c15ccbc8352261ccfe1f5f6e47
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2018
---
# <a name="setup-common-identity-on-the-data-science-vm"></a>Configurer une identité commune sur la machine virtuelle DSVM

Par défaut, sur une machine virtuelle Azure comprenant la machine virtuelle de science des données (DSVM), des comptes d’utilisateurs locaux sont créés lors de l’approvisionnement de la machine virtuelle, et les utilisateurs s’authentifient sur la machine virtuelle avec ces informations d’identification. Si vous disposez de plusieurs machines virtuelles auxquelles vous devez accéder, cette approche peut rapidement rendre la gestion des informations d’identification fastidieuse. Une gestion et des comptes d’utilisateurs communs utilisant un fournisseur d’identité basé sur des standards vous permettent d’utiliser un seul et même ensemble d’informations d’identification pour accéder aux différentes ressources figurant dans Azure, notamment à plusieurs machines virtuelles DSVM. 

Active Directory (AD) est un fournisseur d’identité courant qui est pris en charge à la fois dans Azure en tant que service, et en local. Vous pouvez tirer parti d’Active Directory ou d’Azure Active Directory pour authentifier les utilisateurs sur une machine virtuelle DSVM autonome ou dans un cluster de machines virtuelles DSVM d’un groupe de machines virtuelles identiques Azure. Pour ce faire, vous devez joindre les instances DSVM à un domaine Active Directory. Si vous disposez déjà d’un annuaire Active Directory pour gérer les identités, vous pouvez l’utiliser en tant que fournisseur d’identité commun. Si vous n’en n’avez pas, vous pouvez exécuter une instance AD gérée dans Azure via un service appelé [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/)(Azure AD DS). 

La documentation sur [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/) fournit des [instructions](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution#synchronized-identity) détaillées sur l’instance Active Directory gérée, notamment pour connecter Azure Active Directory à votre répertoire local si vous en avez un. 

Le reste de cet article décrit les étapes pour configurer un service de domaine Active Directory entièrement managé dans Azure à l’aide d’Azure AD DS, et joindre vos machines virtuelles DSVM au domaine AD managé afin de permettre aux utilisateurs d’accéder à un pool de machines virtuelles DSVM (et autres ressources Azure) à l’aide d’un compte d’utilisateur et d’informations d’identification communs. 

##  <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Configurer un domaine Active Directory entièrement managé dans Azure

Azure AD DS facilite la gestion des identités en fournissant un service entièrement géré dans Azure. Dans ce domaine Active Directory, les utilisateurs et les groupes sont gérés.  Étapes de configuration des comptes d’utilisateurs et d’un domaine AD hébergé dans Azure dans votre répertoire :

1. Ajoutez un ou des utilisateurs à Active Directory sur le portail. 

    a. Connectez-vous au [centre d’administration Azure Active Directory](https://aad.portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.
    
    b. Sélectionnez **Azure Active Directory**, puis **Utilisateurs et groupes**.
    
    c. Dans **Utilisateurs et groupes**, sélectionnez **Tous les utilisateurs**, puis **Nouvel utilisateur**.
   
   ![Sélection de la commande Ajouter](./media/add-user.png)
    
    d. Entrez les détails de l’utilisateur, dont son **nom** et son **nom d’utilisateur**. La partie du nom de domaine du nom d’utilisateur doit être le nom de domaine initial par défaut, « [nom de domaine].onmicrosoft.com », ou un [nom de domaine personnalisé](../../active-directory/add-custom-domain.md) vérifié, non fédéré, comme « contoso.com ».
    
    e. Copiez ou notez d’une autre façon le mot de passe généré de sorte à pouvoir le fournir à l’utilisateur une fois ce processus terminé.
    
    f. Si vous le souhaitez, vous pouvez ouvrir et renseigner les informations dans **Profil**, **Groupes** ou **Rôle d’annuaire** pour l’utilisateur. 
    
    g. Dans **Utilisateur**, sélectionnez **Créer**.
    
    h. Distribuez de manière sécurisée le mot de passe généré au nouvel utilisateur afin qu’il puisse se connecter.

2.  Créez une instance Azure AD Domain Services.

    Pour plus d’informations sur la création d’une instance Azure ADDS, suivez les instructions de l’article [Activer Azure Active Directory Domain Services à l’aide du portail Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) (Tâches 1 à 5). Il est important que les mots de passe existants de l’utilisateur dans Active Directory soient mis à jour afin que le mot de passe dans Azure AD DS soit synchronisé. Il est également important d’ajouter le service DNS à Azure AD DS, comme indiqué dans la tâche n°4 de l’article mentionné ci-dessus. 

3.  Créez un sous-réseau DSVM distinct dans le réseau virtuel créé dans la tâche n°2 de l’étape précédente.
4.  Créez une ou plusieurs instances de machine virtuelle de science des données dans le sous-réseau DSVM. 
5.  Suivez les [instructions](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) pour ajouter DSVM à Active Directory. 
6.  Ensuite, montez un partage Azure Files pour héberger votre répertoire de base ou de blocs-notes pour activer le montage de votre espace de travail sur n’importe quelle machine. (Si vous avez besoin d’autorisations limitées au niveau des fichiers, vous avez besoin d’un système NFS s’exécutant sur une ou plusieurs machines virtuelles.)

    a. [Créez un partage Azure Files](../../storage/files/storage-how-to-create-file-share.md).
    
    b. Montez-le sur la machine virtuelle DSVM Linux. Lorsque vous cliquez sur le bouton Connexion d’Azure Files dans votre compte de stockage sur le portail Azure, la commande à exécuter dans un interpréteur de commandes Bash s’affiche sur la machine virtuelle DSVM Linux. La commande ressemble à ceci :
```
sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
```
7.  Disons par exemple que vous avez monté votre instance Azure Files dans /data/workspace. Vous allez créer maintenant des répertoires pour chacun de vos utilisateurs dans le partage : /data/workspace/user1, /data/workspace/user2, etc. Créez un répertoire ```notebooks``` dans l’espace de travail de chaque utilisateur. 
8. Créez des liens symboliques pour les ```notebooks``` dans ```$HOME/userx/notebooks/remote```.   

À présent, les utilisateurs de votre annuaire Active Directory hébergé dans Azure peuvent se connecter à n’importe quelle machine virtuelle DSVM (SSH et JupyterHub) jointe à Azure AD DS à l’aide des informations d’identification Active Directory. Étant donné que l’espace de travail utilisateur se trouve dans le partage Azure Files, les utilisateurs ont accès à leurs blocs-notes et aux autres travaux à partir de n’importe quelle machine virtuelle DSVM lors de l’utilisation de JupyterHub. 

Pour la mise à l’échelle automatique, vous pouvez utiliser le groupe de machines virtuelles identiques pour créer un pool de machines virtuelles qui sont toutes jointes au domaine de cette manière et avec le disque partagé monté. Les utilisateurs peuvent se connecter à n’importe quelle machine disponible dans le groupe de machines virtuelles identiques et avoir accès au disque partagé sur lequel leurs blocs-notes sont enregistrés. 

## <a name="next-steps"></a>Étapes suivantes

* [Stocker en toute sécurité les informations d’identification pour accéder aux ressources cloud](dsvm-secure-access-keys.md)



