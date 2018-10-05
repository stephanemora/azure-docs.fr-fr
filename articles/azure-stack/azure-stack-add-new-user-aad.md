---
title: Ajouter un nouveau compte client Azure Stack dans Azure Active Directory | Microsoft Docs
description: Après le déploiement du Kit de développement Microsoft Azure Stack, vous devrez créer au moins un compte utilisateur client pour pouvoir explorer le portail client.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: a75d5c88-5b9e-4e9a-a6e3-48bbfa7069a7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: 9a4d7200a2bc2445fcdfefc0332d67a045b5a2e1
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47038015"
---
# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Ajouter un nouveau compte de locataire Azure Stack dans Azure Active Directory

Après le [déploiement du Kit de développement Azure Stack](azure-stack-run-powershell-script.md), vous aurez besoin d’un compte utilisateur client afin de pouvoir explorer le portail client et tester vos offres et vos plans. Vous pouvez créer un compte client [à l’aide du portail Azure](#create-an-azure-stack-tenant-account-using-the-azure-portal) ou [à l’aide de PowerShell](#create-an-azure-stack-tenant-account-using-powershell).

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Création d’un compte de locataire Azure Stack à l’aide du portail Azure

Pour utiliser le portail Azure, vous devez disposer d’un abonnement Azure.

1. Connectez-vous à [Azure](https://portal.azure.com).
2. Dans la barre de navigation de gauche, sélectionnez **Active Directory** et accédez au répertoire que vous souhaitez utiliser pour Azure Stack, ou créez-en un.
3. Sélectionnez **Azure Active Directory** > **Utilisateurs** > **Nouvel utilisateur**.

    ![Page Utilisateurs - Tous avec l’option Nouvel utilisateur mis en surbrillance](media/azure-stack-add-new-user-aad/new-user-all-users.png)

4. Dans la page **Utilisateur**, fournissez les informations requises.

    ![Ajouter un nouvel utilisateur, page Utilisateur avec les informations sur l’utilisateur](media/azure-stack-add-new-user-aad/new-user-user.png)

    - **Nom (obligatoire).** Prénom et nom du nouvel utilisateur. Par exemple, Mary Parker.
    - **Nom d’utilisateur (obligatoire).** Nom d’utilisateur du nouvel utilisateur. Par exemple : mary@contoso.com.
        Le domaine dans le nom d’utilisateur doit correspondre au nom de domaine par défaut initial, <_votrenomdedomaine_>.onmicrosoft.com, ou à un nom de domaine personnalisé, comme contoso.com. Pour plus d’informations sur la création d’un nom de domaine personnalisé, consultez [Ajouter un nom de domaine personnalisé dans Azure Active Directory](../active-directory/fundamentals/add-custom-domain.md).
    - **Profil.** Si vous le souhaitez, ajoutez des informations supplémentaires sur l’utilisateur. Vous pouvez également ajouter ces informations ultérieurement. Pour plus d’informations sur l’ajout d’informations sur l’utilisateur, consultez [Ajouter ou modifier des informations de profil utilisateur](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md).
    - **Rôle d’annuaire.**  Choisissez **Utilisateur**.

5. Cochez **Afficher le mot de passe** et copiez le mot de passe généré automatiquement dans le champ **Mot de passe**. Vous aurez besoin de ce mot de passe pour vous connecter la première fois.

6. Sélectionnez **Créer**.

    L’utilisateur est créé et ajouté à votre locataire Azure AD.

7. Connectez-vous au portail Microsoft Azure avec le nouveau compte. Modifiez le mot de passe lorsque vous y êtes invité.
8. Connectez-vous à `https://portal.local.azurestack.external` avec le nouveau compte pour afficher le portail client.

## <a name="create-an-azure-stack-tenant-account-using-powershell"></a>Création d’un compte de locataire Azure Stack à l’aide de PowerShell

Si vous n’avez pas d’abonnement Azure, vous ne pouvez pas utiliser le portail Azure pour ajouter un compte utilisateur client. Dans ce cas, vous pouvez utiliser le module Azure Active Directory pour Windows PowerShell à la place.

> [!NOTE]
> Si vous utilisez un compte Microsoft (Live ID) pour déployer le Kit de développement Azure Stack, vous ne pouvez pas utiliser AAD PowerShell pour créer le compte client. 
> 
> 

1. Installez [l’Assistant de connexion Microsoft Online Services pour les professionnels de l’informatique RTW](https://www.microsoft.com/en-us/download/details.aspx?id=41950).
2. Installez le [module Azure Active Directory pour Windows PowerShell (version 64 bits)](http://go.microsoft.com/fwlink/p/?linkid=236297) et ouvrez-le.
3. Exécutez les applets de commande suivantes :

    ```powershell
    # Provide the AAD credential you use to deploy Azure Stack Development Kit

            $msolcred = get-credential

    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. Connectez-vous à Microsoft Azure avec le nouveau compte. Modifiez le mot de passe lorsque vous y êtes invité.
2. Connectez-vous à `https://portal.local.azurestack.external` avec le nouveau compte pour afficher le portail client.

