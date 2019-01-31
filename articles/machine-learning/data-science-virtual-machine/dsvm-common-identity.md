---
title: Configurer une identité commune pour la Data Science Virtual Machine - Azure | Microsoft Docs
description: Découvrez comment créer des comptes d’utilisateurs courants qui peuvent être utilisés sur plusieurs machines Data Science VM. Vous pouvez utiliser Azure Active Directory ou un annuaire local Active Directory pour authentifier les utilisateurs à Data Science Virtual Machine.
keywords: formation approfondie, IA, outils de science des données, machine virtuelle de science des données, analyse géospatiale, processus de science des données en équipe
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 2c981d33c8108386e0287d4c2a800f065c9dc431
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55452853"
---
# <a name="set-up-a-common-identity-on-the-data-science-virtual-machine"></a>Configurer une identité commune sur la Data Science Virtual Machine

Sur une machine virtuelle (VM) Azure, y compris les Data Science Virtual Machines (DSVM), vous allez créer des comptes d’utilisateur local tout en approvisionnant la VM. Ensuite, les utilisateurs s’authentifient sur la VM à l’aide de ces informations d’identification. Si vous disposez de plusieurs machines virtuelles auxquelles vous devez accéder, cette approche peut rapidement rendre la gestion des informations d’identification fastidieuse. Une gestion et des comptes d’utilisateurs communs utilisant un fournisseur d’identité basé sur des standards vous permettent d’utiliser un seul et même ensemble d’informations d’identification pour accéder aux différentes ressources figurant dans Azure, notamment à plusieurs machines virtuelles DSVM. 

Active Directory est un fournisseur d’identité courant qui est pris en charge dans Azure en tant que service et en local. Vous pouvez utiliser Azure Active Directory (Azure AD) ou la version d’Active Directory locale pour authentifier les utilisateurs sur un DSVM autonome ou un cluster de DSVM dans un groupe de machines virtuelles identiques Azure. Pour cela, joignez les instances DSVM à un domaine Active Directory. 

Si vous disposez déjà d’un annuaire Active Directory pour gérer les identités, vous pouvez l’utiliser en tant que fournisseur d’identité commun. Si vous n’en n’avez pas, vous pouvez exécuter une instance Active Directory gérée dans Azure via un service appelé [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/)(Azure AD DS). 

La documentation sur [Azure AD](https://docs.microsoft.com/azure/active-directory/) fournit des [instructions de gestion](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution#synchronized-identity) détaillées, notamment pour connecter Azure AD à votre répertoire local si vous en avez un. 

Cet article décrit les étapes pour configurer un service de domaine Active Directory entièrement géré sur Azure à l’aide d’Azure AD DS. Vous pouvez ensuite joindre vos DSVM au domaine Active Directory géré pour permettre aux utilisateurs d’accéder à un pool de DSVM (et autres ressources Azure) à l’aide d’un compte d’utilisateur et d’informations d’identification communs. 

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Configurer un domaine Active Directory entièrement managé dans Azure

Azure AD DS facilite la gestion des identités en fournissant un service entièrement géré dans Azure. Pour ce domaine Active Directory, vous gérez les utilisateurs et les groupes. Étapes de configuration des comptes d’utilisateurs et d’un domaine Active Directory hébergé dans Azure dans votre répertoire :

1. Dans le portail Azure, ajoutez l’utilisateur à Active Directory : 

   a. Connectez-vous au [centre d’administration Azure Active Directory](https://aad.portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.
    
   b. Sélectionnez **Azure Active Directory**, puis **Utilisateurs et groupes**.
    
   c. Dans **Utilisateurs et groupes**, sélectionnez **Tous les utilisateurs**, puis **Nouvel utilisateur**.
   
      Le volet **Utilisateur** s’ouvre.
      
      ![Volet « Utilisateur »](./media/add-user.png)
    
   d. Entrez les détails de l’utilisateur, dont son **nom** et son **nom d’utilisateur**. La partie du nom de domaine du nom d’utilisateur doit être le nom de domaine initial par défaut, « [nom de domaine].onmicrosoft.com », ou un [nom de domaine personnalisé](../../active-directory/add-custom-domain.md) vérifié, non fédéré, comme « contoso.com ».
    
   e. Copiez ou notez d’une autre façon le mot de passe généré de sorte à pouvoir le fournir à l’utilisateur une fois ce processus terminé.
    
   f. Si vous le souhaitez, vous pouvez ouvrir et renseigner les informations dans **Profil**, **Groupes** ou **Rôle d’annuaire** pour l’utilisateur. 
    
   g. Dans **Utilisateur**, sélectionnez **Créer**.
    
   h. Distribuez de manière sécurisée le mot de passe généré au nouvel utilisateur afin qu’il puisse se connecter.

1. Créez une instance Azure AD DS. Suivez les instructions de l’article [Activer Azure Active Directory Domain Services à l’aide du portail Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) (Tâches 1 à 5). Il est important de mettre à jour les mots de passe existants de l’utilisateur dans Active Directory afin que le mot de passe dans Azure AD DS soit synchronisé. Il est également important d’ajouter le service DNS à Azure AD DS, comme indiqué dans la tâche n°4 de l’article. 

1. Créez un sous-réseau DSVM distinct dans le réseau virtuel créé lors de la tâche n°2 de l’étape précédente.
1. Créez une ou plusieurs instances de Data Science VM dans le sous-réseau DSVM. 
1. Suivez les [instructions](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) pour ajouter DSVM à Active Directory. 
1. Montez un partage Azure Files pour héberger votre répertoire de base ou de blocs-notes afin de pouvoir monter votre espace de travail sur n’importe quelle machine. (Si vous avez besoin d’autorisations limitées au niveau des fichiers, vous avez besoin d’un système NFS s’exécutant sur une ou plusieurs machines virtuelles.)

   a. [Créez un partage Azure Files](../../storage/files/storage-how-to-create-file-share.md).
    
   b. Montez-le sur la machine virtuelle DSVM Linux. Lorsque vous cliquez sur le bouton **Connexion** dans le partage Azure Files de votre compte de stockage sur le portail Azure, la commande à exécuter dans un interpréteur de commandes Bash s’affiche sur la DSVM Linux. La commande se présente comme suit :
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. Supposons que vous avez monté votre partage Azure Files dans /data/workspace, par exemple. Créez maintenant les répertoires pour chacun de vos utilisateurs dans le partage : /data/workspace/user1, /data/workspace/user2 et ainsi de suite. Créez un répertoire `notebooks` dans l’espace de travail de chaque utilisateur. 
1. Créez des liens symboliques pour `notebooks` dans `$HOME/userx/notebooks/remote`.   

Maintenant, les utilisateurs de votre instance Active Directory sont hébergés dans Azure. En utilisant les informations d’identification Active Directory, les utilisateurs peuvent se connecter à n’importe quelle DSVM (SSH ou JupyterHub) qui est jointe à Azure AD DS. Étant donné que l’espace de travail utilisateur se trouve dans le partage Azure Files, les utilisateurs ont accès à leurs blocs-notes et aux autres travaux à partir de n’importe quelle DSVM lorsqu’ils utilisent JupyterHub. 

Pour la mise à l’échelle automatique, vous pouvez utiliser le groupe de machines virtuelles identiques pour créer un pool de VM qui sont toutes jointes au domaine de cette manière et avec le disque partagé monté. Les utilisateurs peuvent se connecter à n’importe quelle machine disponible dans le groupe de machines virtuelles identiques et avoir accès au disque partagé sur lequel leurs blocs-notes sont enregistrés. 

## <a name="next-steps"></a>Étapes suivantes

* [Stocker en toute sécurité les informations d’identification pour accéder aux ressources cloud](dsvm-secure-access-keys.md)



