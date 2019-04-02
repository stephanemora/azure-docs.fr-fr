---
title: Créer un pool d’hôtes Windows Virtual Desktop Preview avec la Place de marché Azure - Azure
description: Comment créer un pool d’hôtes Windows Virtual Desktop Preview avec la Place de marché Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: cc404c84bf855ab6e49d13207f40b9faa32cdbb2
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58399867"
---
# <a name="tutorial-create-a-host-pool-with-azure-marketplace"></a>Didacticiel : Créer un pool d’hôtes avec la Place de marché Azure

Les pools d’hôtes sont une collection d’une ou de plusieurs machines virtuelles identiques dans des environnements de locataires Windows Virtual Desktop Preview. Chaque pool d’hôtes peut contenir un groupe d’applications avec lequel les utilisateurs peuvent interagir comme ils le feraient sur un ordinateur de bureau physique.

Cet article explique comment créer un pool d’hôtes avec un locataire Windows Virtual Desktop à l’aide d’une offre de la Place de marché Microsoft Azure. Cela inclut la création d’un pool d’hôtes dans Windows Virtual Desktop, la création d’un groupe de ressources avec des machines virtuelles dans un abonnement Azure, la jonction de ces machines virtuelles au domaine Active Directory et l’inscription des machines virtuelles auprès de Windows Virtual Desktop.

Avant de commencer, si vous ne l’avez pas déjà fait, [téléchargez et importez le module PowerShell Windows Virtual Desktop](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) à utiliser dans votre session PowerShell.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur <https://portal.azure.com>.

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Exécuter l’offre de la Place de marché Azure pour provisionner un nouveau pool d’hôtes

Pour exécuter l’offre de la Place de marché Azure pour provisionner un nouveau pool d’hôtes :

1. Sélectionnez **+** or **+ Créer une ressource**.
2. Entrez **Windows Virtual Desktop** dans la fenêtre de recherche de la Place de marché.
3. Sélectionnez **Windows Virtual Desktop - Provisionner un pool d’hôtes**, puis **Créer**.

Suivez les instructions permettant d’entrer les informations des panneaux appropriés.

### <a name="basics"></a>Concepts de base

Voici la procédure à suivre pour le panneau Informations de base :

1. Entrez un nom pour le pool d’hôtes. Il doit être unique dans le locataire Windows Virtual Desktop.
2. Sélectionnez l’option appropriée pour le bureau personnel. Si vous sélectionnez **Oui**, chaque utilisateur qui se connecte à ce pool d’hôtes est affecté de façon permanente à une machine virtuelle.
3. Entrez une liste séparée par des virgules d’utilisateurs qui peuvent se connecter aux clients Windows Virtual Desktop et accéder à un ordinateur de bureau une fois l’offre de la Place de marché Azure terminée. Par exemple, si vous voulez autoriser l’accès à user1@contoso.com et user2@contoso.com, entrez « user1@contoso.com,user2@contoso.com ».
4. Sélectionnez **Créer**, puis indiquez un nom pour le nouveau groupe de ressources.
5. Pour **Emplacement**, sélectionnez le même emplacement que le réseau virtuel qui dispose d’une connectivité au serveur Active Directory.
6. Sélectionnez **OK**.

### <a name="configure-virtual-machines"></a>Configurer des machines virtuelles

Pour le panneau Configurer des machines virtuelles :

1. Acceptez les valeurs par défaut ou personnalisez le nombre et la taille des machines virtuelles.
2. Entrez un préfixe pour le nom des machines virtuelles. Par exemple, si vous entrez le nom « préfixe », les machines virtuelles s’appelleront « préfixe-0 », « préfixe-1 », etc.
3. Sélectionnez **OK**.

### <a name="virtual-machine-settings"></a>Paramètres de la machine virtuelle

Pour le panneau Paramètres de la machine virtuelle :

1. Sélectionnez la **Source de l’image**, puis entrez les informations appropriées concernant la façon de la rechercher et de la stocker. Si vous choisissez de ne pas utiliser de disques managés, sélectionnez le compte de stockage contenant le fichier .vhd.
2. Entrez le nom d’utilisateur principal et le mot de passe pour le compte de domaine qui va joindre les machines virtuelles au domaine Active Directory. Ces mêmes nom d’utilisateur et mot de passe sont créés sur les machines virtuelles sous la forme d’un compte local. Vous pouvez réinitialiser ces comptes locaux ultérieurement.
3. Sélectionnez le réseau virtuel qui dispose d’une connectivité au serveur Active Directory, puis choisissez un sous-réseau pour héberger les machines virtuelles.
4. Sélectionnez **OK**.

### <a name="windows-virtual-desktop-preview-tenant-information"></a>Informations sur le locataire Windows Virtual Desktop Preview

Pour le panneau Informations sur le locataire Windows Virtual Desktop :

1. Entrez le **Nom du groupe de locataires Windows Virtual Desktop** pour le groupe de locataires qui contient votre locataire. Si vous n’avez pas de nom de groupe de locataires spécifique planifié, conservez la valeur par défaut.
2. Entrez le **Nom du locataire Windows Virtual Desktop** pour le locataire que vous allez créer dans ce pool d’hôtes.
3. Spécifiez le type d’informations d’identification que vous voulez utiliser pour vous authentifier comme propriétaire des services Bureau à distance (RDS) du locataire Windows Virtual Desktop. Si vous sélectionnez **Principal du service**, vous devez également fournir l’**ID de locataire Azure AD** associé au principal de service.
4. Entrez les informations d’identification du compte administrateur de locataire. Seuls les principaux de service avec des informations d’identification de mot de passe sont pris en charge.
5. Sélectionnez **OK**.

## <a name="complete-setup-and-create-the-virtual-machine"></a>Terminer la configuration et la création de la machine virtuelle

Pour les deux derniers panneaux :

1. Dans le panneau **Synthèse**, passez en revue les informations de configuration. Si vous devez effectuer une modification, revenez au panneau approprié, puis apportez votre changement avant de continuer. Si les informations sont correctes, sélectionnez **OK**.
2. Dans le panneau **Acheter**, passez en revue les informations supplémentaires concernant votre achat sur la Place de marché Azure.
3. Sélectionnez **Créer** pour déployer votre pool d’hôtes.

En fonction du nombre de machines virtuelles que vous créez, ce processus peut prendre 30 minutes, voire plus.

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>(Facultatif) Affecter des utilisateurs supplémentaires au groupe d’applications de bureau

Une fois l’offre de la Place de marché Azure terminée, vous pouvez affecter des utilisateurs supplémentaires au groupe d’applications de bureau avant de commencer à tester les bureaux de session complète sur vos machines virtuelles. Si vous avez déjà ajouté les utilisateurs par défaut dans l’offre de la Place de marché Azure et que vous ne voulez pas en ajouter d’autres, vous pouvez ignorer cette section.

Pour affecter des utilisateurs au groupe d’applications de bureau, vous devez d’abord ouvrir une fenêtre PowerShell. Ensuite, vous devez entrer les deux applets de commande suivantes.

Exécutez l’applet de commande suivante pour vous connecter à l’environnement Windows Virtual Desktop :

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Définissez le contexte sur le groupe de locataires Windows Virtual Desktop que vous avez spécifié dans l’offre de la Place de marché Azure avec l’applet de commande suivante. Si vous avez laissé la valeur par défaut comme valeur de groupe de locataires Windows Virtual Desktop dans l’offre de la Place de marché Azure, vous pouvez ignorer cette étape.

```powershell
Set-RdsContext -TenantGroupName <tenantgroupname>
```

Une fois ces deux opérations terminées, vous pouvez ajouter des utilisateurs au groupe d’applications de bureau avec cette applet de commande :

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

L’UPN (nom d’utilisateur principal) de l’utilisateur doit correspondre à l’identité de l’utilisateur dans Azure Active Directory (par exemple, user1@contoso.com). Si vous voulez ajouter plusieurs utilisateurs, vous devez exécuter cette applet de commande pour chaque utilisateur.

Une fois ces étapes terminées, les utilisateurs ajoutés au groupe d’applications de bureau peuvent se connecter à Windows Virtual Desktop avec les clients Bureau à distance pris en charge et voir une ressource pour le bureau d’une session.

Voici les clients actuellement pris en charge :

- [Client Bureau à distance pour Windows 7 et Windows 10](connect-windows-7-and-10.md)
- [Client web Windows Virtual Desktop](connect-web.md)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé un pool d’hôtes et affecté des utilisateurs pour accéder à son bureau, vous pouvez également remplir votre pool d’hôtes avec des applications RemoteApp. Pour en savoir plus sur la façon de gérer des applications dans Windows Virtual Desktop, consultez le tutoriel Gérer les groupes d’applications.

> [!div class="nextstepaction"]
> [Gérer les groupes d’applications](./manage-app-groups.md)
