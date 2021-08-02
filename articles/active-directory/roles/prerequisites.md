---
title: Conditions préalables à l’utilisation de PowerShell ou d’Afficheur Graph pour les rôles Azure AD – Azure Active Directory
description: Conditions préalables à l’utilisation de PowerShell ou d’Afficheur Graph pour les rôles Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 05/13/2021
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2b67f3514246bcac1185370393ac469f8c96cf3
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111890615"
---
# <a name="prerequisites-to-use-powershell-or-graph-explorer-for-azure-ad-roles"></a>Conditions préalables à l’utilisation de PowerShell ou d’Afficheur Graph pour les rôles Azure AD

Si vous souhaitez gérer des rôles Azure Active Directory (Azure AD) à l’aide de PowerShell ou d’Afficheur Graph, vous devez remplir les conditions préalables requises. Cet article décrit les conditions préalables à l’utilisation de PowerShell et d’Afficheur Graph pour les différentes fonctionnalités de rôle Azure AD.

## <a name="azuread-module"></a>Module AzureAD

Pour utiliser les commandes PowerShell afin d’effectuer les actions suivantes :

- Répertorier les attributions de rôles
- Créer un groupe pouvant se voir attribuer des rôles
- Gérer des unités administratives

Le module suivant doit être installé :

- [AzureAD](https://www.powershellgallery.com/packages/AzureAD) version 2.0.2.130 ou ultérieure


#### <a name="check-azuread-version"></a>Vérifier la version d’AzureAD

Pour vérifier la version d’AzureAD que vous avez installée, utilisez la commande [Get-InstalledModule](/powershell/module/powershellget/get-installedmodule).

```powershell
Get-InstalledModule -Name AzureAD
```

Vous devez obtenir une sortie similaire à la suivante :

```powershell
Version    Name                                Repository           Description
-------    ----                                ----------           -----------
2.0.2.130  AzureAD                             PSGallery            Azure Active Directory V2 General Availability M...
```

#### <a name="install-azuread"></a>Installer AzureAD

Si AzureAD n’est pas installé, utilisez la commande [Install-Module](/powershell/module/powershellget/install-module) pour installer AzureAD.

```powershell
Install-Module -Name AzureAD
```

#### <a name="update-azuread"></a>Mettre à jour AzureAD

Pour mettre à jour AzureAD avec la version la plus récente, exécutez à nouveau la commande [Install-Module](/powershell/module/powershellget/install-module).

```powershell
Install-Module -Name AzureAD
```

#### <a name="use-azuread"></a>Utiliser AzureAD

Pour utiliser AzureAD, procédez comme suit pour vous assurer qu’il est importé dans la session en cours.

1. Utilisez la commande [Get-Module](/powershell/module/microsoft.powershell.core/get-module) pour vérifier si AzureAD est chargé en mémoire.

    ```powershell
    Get-Module -Name AzureAD
    ```

1. Si vous ne voyez aucune sortie à l’étape précédente, utilisez la commande [Import-Module](/powershell/module/microsoft.powershell.core/import-module) pour importer AzureAD. Le paramètre `-Force` supprime le module chargé, puis le réimporte.

    ```powershell
    Import-Module -Name AzureAD -Force
    ```

1. Exécutez [Get-Module](/powershell/module/microsoft.powershell.core/get-module) à nouveau.

    ```powershell
    Get-Module -Name AzureAD
    ```

    Vous devez obtenir une sortie similaire à la suivante :
    
    ```powershell
    ModuleType Version    Name                                ExportedCommands
    ---------- -------    ----                                ----------------
    Binary     2.0.2.130  AzureAD                             {Add-AzureADApplicationOwner, Add-AzureADDeviceRegisteredO...
    ```

## <a name="azureadpreview-module"></a>Module AzureADPreview

Pour utiliser les commandes PowerShell afin d’effectuer les actions suivantes :

- Attribuer des rôles aux utilisateurs ou aux groupes
- Supprimer une attribution de rôle
- Rendre un groupe éligible à un rôle à l’aide de Privileged Identity Management
- Créer des rôles personnalisées

Le module suivant doit être installé :

- [AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview) version 2.0.2.129 ou ultérieure


#### <a name="check-azureadpreview-version"></a>Vérifier la version d’AzureADPreview

Pour vérifier la version d’AzureADPreview que vous avez installée, utilisez la commande [Get-InstalledModule](/powershell/module/powershellget/get-installedmodule).

```powershell
Get-InstalledModule -Name AzureADPreview
```

Vous devez obtenir une sortie similaire à la suivante :

```powershell
Version    Name                                Repository           Description
-------    ----                                ----------           -----------
2.0.2.129  AzureADPreview                      PSGallery            Azure Active Directory V2 Preview Module. ...
```

#### <a name="install-azureadpreview"></a>Installer AzureADPreview

Si AzureADPreview n’est pas installé, utilisez la commande [Install-Module](/powershell/module/powershellget/install-module) pour installer AzureADPreview.

```powershell
Install-Module -Name AzureADPreview
```

#### <a name="update-azureadpreview"></a>Mettre à jour AzureADPreview

Pour mettre à jour AzureADPreview avec la version la plus récente, exécutez à nouveau la commande [Install-Module](/powershell/module/powershellget/install-module).

```powershell
Install-Module -Name AzureADPreview
```

#### <a name="use-azureadpreview"></a>Utiliser AzureADPreview

Pour utiliser AzureADPreview, procédez comme suit pour vous assurer qu’il est importé dans la session en cours.

1. Utilisez la commande [Get-Module](/powershell/module/microsoft.powershell.core/get-module) pour vérifier si AzureADPreview est chargé en mémoire.

    ```powershell
    Get-Module -Name AzureADPreview
    ```

1. Si vous ne voyez aucune sortie à l’étape précédente, utilisez la commande [Import-Module](/powershell/module/powershellget/import-module) pour importer AzureADPreview. Le paramètre `-Force` supprime le module chargé, puis le réimporte.

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

1. Exécutez [Get-Module](/powershell/module/microsoft.powershell.core/get-module) à nouveau.

    ```powershell
    Get-Module -Name AzureADPreview
    ```

    Vous devez obtenir une sortie similaire à la suivante :
    
    ```powershell
    ModuleType Version    Name                                ExportedCommands
    ---------- -------    ----                                ----------------
    Binary     2.0.2.129  AzureADPreview                      {Add-AzureADAdministrativeUnitMember, Add-AzureADApplicati...
    ```

## <a name="graph-explorer"></a>Explorateur graphique

Pour gérer les rôles Azure AD à l’aide de l’[API Microsoft Graph](/graph/overview) et d’[Afficheur Graph](/graph/graph-explorer/graph-explorer-overview), vous devez effectuer les opérations suivantes :

1. Dans le portail Azure, ouvrez **Azure Active Directory**.

1. Cliquez sur **Applications d’entreprise**.

1. Dans la liste des applications, recherchez et cliquez sur **Afficheur Graph**.

1. Cliquez sur **Autorisations**.

1. Cliquez sur **Accorder un consentement administrateur pour Afficheur Graph**.

    ![Capture d’écran montrant le lien « Accorder un consentement administrateur pour Afficheur Graph ».](./media/prerequisites/select-graph-explorer.png)

1. Utilisez l’[outil Afficheur Graph](https://aka.ms/ge).

## <a name="next-steps"></a>Étapes suivantes

- [Installer Azure Active Directory PowerShell pour Graph](/powershell/azure/active-directory/install-adv2)
- [Documentation relative au module AzureAD](/powershell/module/azuread/)
- [Explorateur graphique](/graph/graph-explorer/graph-explorer-overview)
