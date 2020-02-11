---
title: Pool d’hôtes Windows Virtual Desktop Place de marché Azure - Azure
description: Guide pratique pour créer un pool d’hôtes Windows Virtual Desktop à l’aide de la Place de marché Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 08/30/2019
ms.author: helohr
ms.openlocfilehash: 25dd4810cf8cccab8bcbf211da4f6abbcd147056
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77020027"
---
# <a name="tutorial-create-a-host-pool-by-using-the-azure-marketplace"></a>Tutoriel : Créer un pool d’hôtes en utilisant la Place de marché Azure

Les pools d'hôtes sont des ensembles d'une ou de plusieurs machines virtuelles identiques dans des environnements de locataires Windows Virtual Desktop. Chaque pool d’hôtes peut contenir un groupe d’applications avec lequel les utilisateurs peuvent interagir comme ils le feraient sur un ordinateur de bureau physique.

Ce tutoriel explique comment créer un pool d’hôtes avec un locataire Windows Virtual Desktop à l’aide d’une offre de la Place de marché Microsoft Azure. Les tâches sont les suivantes :

> [!div class="checklist"]
> * Créez un pool d’hôtes dans Windows Virtual Desktop.
> * Créez un groupe de ressources avec des machines virtuelles dans un abonnement Azure.
> * Joignez les machines virtuelles au domaine Active Directory.
> * Inscrivez les machines virtuelles avec Windows Virtual Desktop.

Avant de commencer, si vous ne l’avez pas déjà fait, [téléchargez et importez le module PowerShell Windows Virtual Desktop](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) à utiliser dans votre session PowerShell. Exécutez ensuite l’applet de commande suivante pour vous connecter à votre compte :

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Exécuter l’offre de la Place de marché Azure pour provisionner un nouveau pool d’hôtes

Pour exécuter l’offre de la Place de marché Azure pour provisionner un nouveau pool d’hôtes :

1. Dans le menu du Portail Azure ou dans la page **Accueil**, sélectionnez **Créer une ressource**.
2. Entrez **Windows Virtual Desktop** dans la fenêtre de recherche de la Place de marché.
3. Sélectionnez **Windows Virtual Desktop - Provisionner un pool d’hôtes**, puis **Créer**.

Après cela, suivez les instructions indiquées de la section suivante pour entrer les informations relatives aux panneaux appropriés.

### <a name="basics"></a>Concepts de base

Voici la procédure à suivre pour le panneau **Informations de base** :

1. Entrez un nom pour le pool d’hôtes. Il doit être unique dans le locataire Windows Virtual Desktop.
2. Sélectionnez l’option appropriée pour un bureau personnel. Si vous sélectionnez **Oui**, chaque utilisateur qui se connecte à ce pool d’hôtes est affecté de façon permanente à une machine virtuelle.
3. Entrez une liste séparée par des virgules d’utilisateurs qui peuvent se connecter aux clients Windows Virtual Desktop et accéder à un ordinateur de bureau une fois l’offre de la Place de marché Azure terminée. Par exemple, si vous voulez autoriser l’accès à user1@contoso.com et user2@contoso.com, entrez « user1@contoso.com,user2@contoso.com ».
4. Sélectionnez **Créer**, puis indiquez un nom pour le nouveau groupe de ressources.
5. Pour **Emplacement**, sélectionnez le même emplacement que le réseau virtuel qui dispose d’une connectivité au serveur Active Directory.
6. Sélectionnez **Suivant : Configurer des machines virtuelles >** .

>[!IMPORTANT]
>Si vous utilisez une solution Azure Active Directory Domain Services et Azure Active Directory pure, veillez à déployer votre pool hôte dans la même région que votre instance Azure Active Directory Domain Services pour éviter les erreurs de jonction de domaine et d’informations d’identification.

### <a name="configure-virtual-machines"></a>Configurer des machines virtuelles

Pour le panneau **Configurer des machines virtuelles** :

1. Acceptez les valeurs par défaut ou personnalisez le nombre et la taille des machines virtuelles.
2. Entrez un préfixe pour le nom des machines virtuelles. Par exemple, si vous entrez le nom « préfixe », les machines virtuelles s’appelleront « préfixe-0 », « préfixe-1 », etc.
3. Sélectionnez **Suivant : Paramètres de la machine virtuelle**.

### <a name="virtual-machine-settings"></a>Paramètres de la machine virtuelle

Pour le panneau **Paramètres de la machine virtuelle** :

>[!NOTE]
> Si vous joignez vos machines virtuelles à un environnement Azure Active Directory Domain Services (Azure AD DS), vérifiez que votre utilisateur de jonction de domaine est membre du [groupe Administrateurs AAD DC](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).
>
> Le compte doit également faire partie du domaine managé Azure AD DS ou du locataire Azure AD : les comptes provenant d’annuaires externes associés à votre locataire Azure AD ne peuvent pas s’authentifier correctement au cours du processus de jonction de domaine. 

1. Pour **Source de l’image**, sélectionnez la source, puis entrez les informations appropriées concernant la façon de la rechercher et de la stocker. Si vous choisissez de ne pas utiliser de disques managés, sélectionnez le compte de stockage qui contient le fichier .vhd.
2. Entrez le nom d’utilisateur principal et le mot de passe pour le compte de domaine qui va joindre les machines virtuelles au domaine Active Directory. Ces mêmes nom d’utilisateur et mot de passe sont créés sur les machines virtuelles sous la forme d’un compte local. Vous pouvez réinitialiser ces comptes locaux ultérieurement.
3. Sélectionnez le réseau virtuel qui dispose d’une connectivité au serveur Active Directory, puis choisissez un sous-réseau pour héberger les machines virtuelles.
4. Sélectionnez **Suivant : Informations sur Windows Virtual Desktop**.

### <a name="windows-virtual-desktop-tenant-information"></a>Informations sur le locataire Windows Virtual Desktop

Pour le panneau **Informations sur le locataire Windows Virtual Desktop** :

1. Dans **Nom du groupe de locataires Windows Virtual Desktop**, entrez le nom du groupe de locataires qui contient votre locataire. Conservez la valeur par défaut, sauf si un nom de groupe de locataires spécifique vous a été fourni.
2. Dans **Nom du locataire Windows Virtual Desktop**, entrez le nom du locataire que vous allez créer dans ce pool d’hôtes.
3. Spécifiez le type d’informations d’identification que vous voulez utiliser pour vous authentifier comme propriétaire des services Bureau à distance (RDS) du locataire Windows Virtual Desktop. Si vous avez suivi le tutoriel [Créer des principaux de service et des attributions de rôles avec PowerShell](./create-service-principal-role-powershell.md), sélectionnez **Principal de service**. Lorsque **l’ID locataire Azure AD** apparaît, entrez l’ID de l’instance Azure Active Directory qui contient le principal de service.
4. Entrez les informations d’identification du compte administrateur de locataire. Seuls les principaux de service avec des informations d’identification de mot de passe sont pris en charge.
5. Sélectionnez **Suivant : Vérifier + créer**.

## <a name="complete-setup-and-create-the-virtual-machine"></a>Terminer la configuration et la création de la machine virtuelle

Pour les deux derniers panneaux :

1. Dans le panneau **Vérifier et créer**, passez en revue les informations de configuration. Si vous devez effectuer une modification, revenez au panneau approprié, puis apportez votre changement avant de continuer. Si les informations sont correctes, sélectionnez **OK**.
2. Sélectionnez **Créer** pour déployer votre pool d’hôtes.

En fonction du nombre de machines virtuelles que vous créez, ce processus peut prendre 30 minutes, voire plus.

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>(Facultatif) Affecter des utilisateurs supplémentaires au groupe d’applications de bureau

Une fois l’offre de la Place de marché Azure terminée, vous pouvez affecter des utilisateurs supplémentaires au groupe d’applications de bureau avant de commencer à tester les bureaux de session complète sur vos machines virtuelles. Si vous avez déjà ajouté les utilisateurs par défaut dans l’offre de la Place de marché Azure et que vous ne voulez pas en ajouter d’autres, vous pouvez ignorer cette section.

Pour affecter des utilisateurs au groupe d’applications de bureau, vous devez d’abord ouvrir une fenêtre PowerShell. Ensuite, vous devez entrer les deux applets de commande suivantes.

Exécutez l’applet de commande suivante pour vous connecter à l’environnement Windows Virtual Desktop :

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Ajoutez des utilisateurs au groupe d’applications de bureau en exécutant cette cmdlet :

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

L’UPN (nom d’utilisateur principal) de l’utilisateur doit correspondre à l’identité de l’utilisateur dans Azure Active Directory (par exemple, user1@contoso.com). Si vous voulez ajouter plusieurs utilisateurs, vous devez exécuter cette applet de commande pour chaque utilisateur.

Une fois ces étapes terminées, les utilisateurs ajoutés au groupe d’applications de bureau peuvent se connecter à Windows Virtual Desktop avec les clients Bureau à distance pris en charge et voir une ressource pour le bureau d’une session.

Voici les clients actuellement pris en charge :

- [Client Bureau à distance pour Windows 7 et Windows 10](connect-windows-7-and-10.md)
- [Client web Windows Virtual Desktop](connect-web.md)

>[!IMPORTANT]
>Pour contribuer à sécuriser votre environnement Windows Virtual Desktop dans Azure, nous vous recommandons de ne pas ouvrir le port entrant 3389 sur vos machines virtuelles. Windows Virtual Desktop ne nécessite pas l’ouverture du port entrant 3389 pour permettre aux utilisateurs d’accéder aux machines virtuelles du pool hôte. Si vous devez ouvrir le port 3389 pour résoudre des problèmes, nous vous recommandons d’utiliser un [accès à la machine virtuelle juste-à-temps](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé un pool d’hôtes et affecté des utilisateurs pour accéder à son bureau, vous pouvez remplir votre pool d’hôtes avec des programmes RemoteApp. Pour en savoir plus sur la façon de gérer des applications dans Windows Virtual Desktop, consultez ce tutoriel :

> [!div class="nextstepaction"]
> [Gérer les groupes d’applications](./manage-app-groups.md)
