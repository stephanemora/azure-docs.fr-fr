---
title: Attribution de rôle de principal du service Windows Virtual Desktop - Azure
description: Guide pratique pour créer des principaux de service et attribuer des rôles à l’aide de PowerShell dans Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ad78fe2ce66511cb75e23a28a30c44b173da9020
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117724"
---
# <a name="tutorial-create-service-principals-and-role-assignments-by-using-powershell"></a>Tutoriel : Créer des principaux de service et des attributions de rôles avec PowerShell

>[!IMPORTANT]
>Ce contenu s’applique à la version Automne 2019 qui ne prend pas en charge les objets Azure Resource Manager Windows Virtual Desktop.

Les principaux de service sont des identités, que vous pouvez créer dans Azure Active Directory pour attribuer des rôles et des autorisations dans un but précis. Dans Windows Virtual Desktop, vous pouvez créer un principal de service pour :

- Automatisez des tâches de gestion Windows Virtual Desktop spécifiques.
- L’utiliser comme informations d’identification à la place des utilisateurs avec MFA (authentification multifacteur) obligatoire lors de l’exécution de tout modèle Azure Resource Manager pour Windows Virtual Desktop.

Dans ce tutoriel, vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créez un principal du service dans Azure Active Directory.
> * Créez une attribution de rôle dans Windows Virtual Desktop.
> * Se connecter à Windows Virtual Desktop avec le principal de service.

## <a name="prerequisites"></a>Prérequis

Avant de pouvoir créer des principaux de service et des attributions de rôles, vous devez effectuer trois opérations :

1. Installez le module AzureAD. Pour ce faire, exécutez PowerShell en tant qu’administrateur, puis exécutez l’applet de commande suivante :

    ```powershell
    Install-Module AzureAD
    ```

2. [Téléchargez et importez le module PowerShell Windows Virtual Desktop](/powershell/windows-virtual-desktop/overview/).

3. Suivez toutes les instructions de cet article dans la même session PowerShell. Le processus risque de ne pas fonctionner si vous interrompez votre session PowerShell en fermant la fenêtre et en la rouvrant ultérieurement.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Création d’un principal du service dans Azure Active Directory

Quand vous avez rempli les prérequis dans votre session PowerShell, exécutez les applets de commande PowerShell suivantes pour créer un principal de service multilocataire dans Azure.

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```
## <a name="view-your-credentials-in-powershell"></a>Afficher vos informations d’identification dans PowerShell

Avant de créer l’attribution de rôle pour votre principal du service, consultez vos informations d’identification et notez-les pour vous y référer ultérieurement. Le mot de passe est particulièrement important parce que vous ne pouvez pas le récupérer après avoir fermé cette session PowerShell.

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

## <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Créer une attribution de rôle dans Windows Virtual Desktop

Ensuite, vous devez créer une attribution de rôle afin que le principal du service puisse se connecter à Windows Virtual Desktop. Veillez à vous connecter avec un compte qui dispose des autorisations nécessaires pour créer des attributions de rôle.

Tout d’abord, si vous ne l’avez pas déjà fait, [téléchargez et importez le module PowerShell Windows Virtual Desktop](/powershell/windows-virtual-desktop/overview/) à utiliser dans votre session PowerShell.

Exécutez les applets de commande PowerShell suivantes pour vous connecter à Windows Virtual Desktop et afficher vos locataires.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Get-RdsTenant
```

Quand vous trouvez le nom du locataire pour lequel vous souhaitez créer une attribution de rôle, utilisez ce nom dans l’applet de commande suivante :

```powershell
$myTenantName = "<Windows Virtual Desktop Tenant Name>"
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>Se connecter avec le principal de service

Après avoir créé une attribution de rôle pour le principal de service, vérifiez que le principal de service peut se connecter à Windows Virtual Desktop en exécutant l’applet de commande suivante :

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

Une fois que vous êtes connecté, vérifiez que tout fonctionne en testant quelques applets de commande PowerShell Windows Virtual Desktop avec le principal de service.

## <a name="next-steps"></a>Étapes suivantes

Après avoir créé le principal du service et l’avoir affecté à un rôle dans votre locataire Windows Virtual Desktop, vous pouvez l’utiliser pour créer un pool d’hôtes. Pour en savoir plus sur les pools d’hôtes, passez au tutoriel concernant la création d’un pool d’hôtes dans Windows Virtual Desktop.

 > [!div class="nextstepaction"]
 > [Créer un pool d’hôtes avec la Place de marché Azure](create-host-pools-azure-marketplace-2019.md)
