---
title: Créer des principaux de service et des attributions de rôles Windows Virtual Desktop Preview avec PowerShell - Azure
description: Comment créer des principaux de service et attribuer des rôles avec PowerShell dans Windows Virtual Desktop Preview.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/12/2019
ms.author: helohr
ms.openlocfilehash: 1e53f76f564c0970ac1f291d2125807441500de6
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523310"
---
# <a name="tutorial-create-service-principals-and-role-assignments-with-powershell"></a>Didacticiel : Créer des principaux de service et des attributions de rôles avec PowerShell

Les principaux de service sont des identités, que vous pouvez créer dans Azure Active Directory pour attribuer des rôles et des autorisations dans un but précis. Dans Windows Virtual Desktop Preview, vous pouvez créer un principal de service pour :

- Automatiser des tâches de gestion Windows Virtual Desktop spécifiques
- L’utiliser comme informations d’identification à la place des utilisateurs avec MFA (authentification multifacteur) obligatoire lors de l’exécution d’un modèle Azure Resource Manager de Windows Virtual Desktop

Dans ce tutoriel, vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Création d’un principal du service dans Azure Active Directory
> * Créer une attribution de rôle dans Windows Virtual Desktop
> * Se connecter à Windows Virtual Desktop avec le principal de service

## <a name="prerequisites"></a>Prérequis

Avant de pouvoir créer des principaux de service et des attributions de rôles, vous devez effectuer trois opérations :

1. Installez le module AzureAD. Pour ce faire, exécutez PowerShell en tant qu’administrateur, puis exécutez l’applet de commande suivante :

    ```powershell
    Install-Module AzureAD
    ```

2. Exécutez les applets de commande suivantes en remplaçant les valeurs entre guillemets par les valeurs appropriées pour votre session.

    ```powershell
    $myTenantName = "<my-tenant-name>"
    ```

3. Suivez toutes les instructions de cet article dans la même session PowerShell. La procédure peut ne pas fonctionner si vous fermez la fenêtre et que vous y revenez ultérieurement.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Création d’un principal du service dans Azure Active Directory

Une fois que vous avez rempli les prérequis dans votre session PowerShell, exécutez les applets de commande PowerShell suivantes pour créer un principal de service multilocataire dans Azure.

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```

## <a name="create-a-role-assignment-in-windows-virtual-desktop-preview"></a>Créer une attribution de rôle dans Windows Virtual Desktop Preview

Maintenant que vous avez créé un principal de service, vous pouvez l’utiliser pour vous connecter à Windows Virtual Desktop. Veillez à vous connecter avec un compte qui dispose des autorisations nécessaires pour créer l’attribution de rôle.

Tout d’abord, si vous ne l’avez pas déjà fait, [téléchargez et importez le module PowerShell Windows Virtual Desktop](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) à utiliser dans votre session PowerShell.

Exécutez les applets de commande PowerShell suivantes pour vous connecter à Windows Virtual Desktop et créer une attribution de rôle pour le principal de service.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>Se connecter avec le principal de service

Après avoir créé une attribution de rôle pour le principal de service, vous devez maintenant vérifier que le principal de service peut se connecter à Windows Virtual Desktop en exécutant l’applet de commande suivante :

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

Une fois que vous êtes connecté, vérifiez que tout fonctionne en testant quelques applets de commande PowerShell Windows Virtual Desktop avec le principal de service.

## <a name="view-your-credentials-in-powershell"></a>Afficher vos informations d’identification dans PowerShell

Avant de terminer votre session PowerShell, vous devez afficher vos informations d’identification et les noter pour vous y référer ultérieurement. Le mot de passe est particulièrement important car vous ne pouvez pas le récupérer une fois que vous avez fermé cette session PowerShell.

Voici les trois informations d’identification que vous devez noter et les applets de commande que vous devez exécuter pour les obtenir :

- Mot de passe :

    ```powershell
    $svcPrincipalCreds.Value
    ```

- ID de locataire :

    ```powershell
    $aadContext.TenantId.Guid
    ```

- ID d’application :

    ```powershell
    $svcPrincipal.AppId
    ```

## <a name="next-steps"></a>Étapes suivantes

Après avoir créé le principal du service et l’avoir affecté à un rôle dans votre locataire Windows Virtual Desktop, vous pouvez l’utiliser pour créer un pool d’hôtes. Pour en savoir plus sur les pools d’hôtes, passez au tutoriel concernant la création d’un pool d’hôtes dans Windows Virtual Desktop.

 > [!div class="nextstepaction"]
 > [Tutoriel sur le pool d’hôtes Windows Virtual Desktop](./create-host-pools-azure-marketplace.md)
