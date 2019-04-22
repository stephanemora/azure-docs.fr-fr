---
title: Créer un locataire dans Windows Virtual Desktop Preview - Azure
description: Explique comment configurer des locataires Windows Virtual Desktop Preview dans Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 1c66b3de9e18cb74c43f20499e4065c7ec7ae5ca
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58801677"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop-preview"></a>Didacticiel : Créer un locataire dans Windows Virtual Desktop Preview

La création d’un locataire dans Windows Virtual Desktop Preview est la première étape de la génération de votre solution de virtualisation de bureau. Un locataire est un groupe d’un ou de plusieurs pools d’hôtes. Chaque pool d’hôtes se compose de plusieurs hôtes de session s’exécutant en tant que machines virtuelles dans Azure et inscrits auprès du service Windows Virtual Desktop. Chaque pool d’hôtes comprend également un ou plusieurs groupes d’applications qui sont utilisés pour publier des ressources du Bureau à distance et d’application à distance pour les utilisateurs. Avec un locataire, vous pouvez générer des pools d’hôtes, créer des groupes d’applications, affecter des utilisateurs et établir des connexions par l’intermédiaire du service.

Dans ce tutoriel, vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Accorder des autorisations Azure Active Directory au service Windows Virtual Desktop.
> * Attribuer le rôle d’application TenantCreator à un utilisateur dans votre locataire Azure Active Directory.
> * Créer un locataire Windows Virtual Desktop.

Voici ce dont vous avez besoin pour configurer votre locataire Windows Virtual Desktop :

* L’ID de locataire [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) pour les utilisateurs Windows Virtual Desktop.
* Un compte d’administrateur général dans le locataire Azure Active Directory.
   * Cela s’applique également aux organisations de fournisseurs de solutions cloud (CSP) créant un locataire Windows Virtual Desktop pour leurs clients. Si vous êtes une organisation CSP, vous devez être en mesure de vous connecter en tant qu’administrateur général de l’instance Azure Active Directory du client.
   * Le compte d’administrateur doit provenir du locataire Azure Active Directory dans lequel vous tentez de créer le locataire Windows Virtual Desktop. Ce processus ne prend pas en charge les comptes (invités) Azure Active Directory B2B.
   * Le compte d’administrateur doit être un compte professionnel ou scolaire.
* Un ID d’abonnement Azure

## <a name="grant-azure-active-directory-permissions-to-the-windows-virtual-desktop-preview-service"></a>Accorder des autorisations Azure Active Directory au service Windows Virtual Desktop Preview

Si vous avez déjà accordé des autorisations à Windows Virtual Desktop pour cette instance Azure Active Directory, ignorez cette section.

L’octroi d’autorisations au service Windows Virtual Desktop lui permet d’interroger votre instance Azure Active Directory concernant les tâches d’administration et les tâches d’utilisateur final.

Pour accorder des autorisations au service :

1. Ouvrez un navigateur, puis connectez-vous à la [page de consentement Windows Virtual Desktop](https://rdweb.wvd.microsoft.com).
2. Pour **Option de consentement** > **Application serveur**, entrez le nom ou l’ID de répertoire du locataire Azure Active Directory, puis sélectionnez **Envoyer**.
        Pour les clients de fournisseurs de solutions cloud, l’ID est l’ID Microsoft du client disponible dans le portail partenaires. Pour les clients Entreprise, l’ID se trouve sous **Azure Active Directory** > **Propriétés** > **ID de répertoire**.
3. Connectez-vous à la page de consentement Windows Virtual Desktop avec un compte d’administrateur général. Par exemple, si vous faites partie de l’organisation Contoso, votre compte peut être admin@contoso.com ou admin@contoso.onmicrosoft.com.  
4. Sélectionnez **Accepter**.
5. Patientez une minute.
6. Revenez à la [page de consentement Windows Virtual Desktop](https://rdweb.wvd.microsoft.com).
7. Accédez à **Option de consentement** > **Application cliente**, entrez le même nom de locataire ou ID de répertoire Azure AD, puis sélectionnez **Envoyer**.
8. Connectez-vous à la page de consentement Windows Virtual Desktop en tant qu’administrateur général comme vous l’avez fait à l’étape 3.
9. Sélectionnez **Accepter**.

## <a name="assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant"></a>Attribuer le rôle d’application TenantCreator à un utilisateur dans votre locataire Azure Active Directory

L’attribution du rôle d’application TenantCreator à un utilisateur Azure Active Directory permet à ce dernier de créer un locataire Windows Virtual Desktop associé à Azure Active Directory. Vous devez utiliser votre compte d’administrateur général pour attribuer le rôle TenantCreator.

Pour attribuer le rôle d’application TenantCreator avec votre compte d’administrateur général :

1. Ouvrez un navigateur, puis connectez-vous au [portail Azure Active Directory](https://aad.portal.azure.com) avec votre compte d’administrateur général.
   - Si vous travaillez avec plusieurs locataires Azure AD, il est recommandé d’ouvrir une session de navigation privée, puis de copier et de coller des URL dans l’adresse.
2. Sélectionnez **Applications d’entreprise**, puis recherchez **Windows Virtual Desktop**. Les deux applications pour lesquelles vous avez donné votre consentement dans la section précédente s’affichent. Entre ces deux applications, sélectionnez **Windows Virtual Desktop**.
3. Sélectionnez **Utilisateurs et groupes**, puis **Ajouter un utilisateur**.
4. Sélectionnez Utilisateurs et groupes dans le panneau **Ajouter une attribution**.
5. Recherchez un compte d’utilisateur qui va créer votre locataire Windows Virtual Desktop.
   - Pou rester simple, ce peut être le compte d’administrateur général.
6. Sélectionnez le compte d’utilisateur, cliquez sur le bouton **Sélectionner**, puis sélectionnez **Attribuer**.

## <a name="create-a-windows-virtual-desktop-preview-tenant"></a>Créer un locataire Windows Virtual Desktop Preview

Maintenant que vous avez accordé des autorisations pour interroger l’instance Azure Active Directory au service Windows Virtual Desktop et attribué le rôle TenantCreator à un compte d’utilisateur, vous pouvez créer un locataire Windows Virtual Desktop.

Tout d’abord, si vous ne l’avez pas déjà fait, [téléchargez et importez le module Windows Virtual Desktop](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) à utiliser dans votre session PowerShell.

Connectez-vous à Windows Virtual Desktop à l’aide du compte d’utilisateur TenantCreator avec cette applet de commande :

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Après cela, créez un locataire Windows Virtual Desktop associé au locataire Azure Active Directory :

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

Les valeurs entre crochets doivent être remplacées par des valeurs appropriées à votre organisation et votre locataire. Par exemple, supposons que vous êtes l’utilisateur TenantCreator Windows Virtual Desktop de l’organisation Contoso. L’applet de commande que vous devez exécuter peut ressembler à ceci :

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez créé votre locataire, vous allez devoir créer un pool d’hôtes. Pour en savoir plus sur les pools d’hôtes, passez au tutoriel concernant la création d’un pool d’hôtes dans Windows Virtual Desktop.

> [!div class="nextstepaction"]
> [Tutoriel sur le pool d’hôtes Windows Virtual Desktop](./create-host-pools-azure-marketplace.md)
