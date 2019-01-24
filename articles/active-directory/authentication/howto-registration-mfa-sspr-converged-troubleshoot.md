---
title: Désactiver l’inscription convergée pour la SSPR et la MFA Azure AD (préversion publique)
description: Désactiver l’authentification multifacteur et l’inscription à la réinitialisation de mot de passe en libre-service Azure AD (préversion publique)
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 08/02/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, michmcla
ms.openlocfilehash: 6a51b1a3050b37fdcc822006f9e25d6662c65fb2
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54426350"
---
# <a name="disable-azure-ad-converged-registration-public-preview"></a>Désactiver l’inscription convergée Azure AD (préversion pubique)

Quand un utilisateur inscrit son numéro de téléphone ou son application mobile dans la nouvelle expérience convergée, notre service estampille un jeu d’indicateurs (StrongAuthenticationMethods) pour ces méthodes sur cet utilisateur. Cette fonctionnalité permet à l’utilisateur effectuer une authentification multifacteur (MFA) Azure avec ces méthodes chaque fois qu’une telle authentification est requise.

La propriété StrongAuthenticationMethods est définie pour les méthodes que les utilisateurs inscrivent via la nouvelle expérience. Ce comportement se produit également une fois la préversion publique disponible. Si un administrateur active la préversion, les utilisateurs s’inscrivent via la nouvelle expérience, puis quand l’administrateur désactive la préversion, les utilisateurs peuvent également être inscrits pour l’authentification multifacteur à leur insu.

Si un utilisateur ayant effectué une inscription convergée accède à la page d’inscription SSPR en cours, à l’URL [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup), il est invité à effectuer une authentification multifacteur avant de pouvoir accéder à cette page. Cette étape est un comportement attendu d’un point de vue technique mais, pour les utilisateurs qui se sont précédemment inscrits pour SSPR uniquement, il s’agit d’un nouveau comportement. Bien que cette étape supplémentaire améliore la situation de sécurité de l’utilisateur en fournissant un niveau supplémentaire de sécurité, il arrive que les administrateurs souhaitent réinitialiser leurs utilisateurs de façon à ce qu’ils ne puissent plus effectuer d’authentification multifacteur.  

## <a name="how-to-roll-back-users"></a>Comment réinitialiser des utilisateurs

Si, en tant qu’administrateur, vous souhaitez réinitialiser les paramètres d’authentification multifacteur d’un utilisateur, nous avons créé un script PowerShell qui efface la propriété StrongAuthenticationMethods pour l’application mobile et/ou le numéro de téléphone d’un utilisateur. Si vous exécutez ce script pour vos utilisateurs, ceux-ci devront se réinscrire pour l’authentification multifacteur si nécessaire. Nous vous recommandons de tester la réinitialisation avec un ou deux utilisateurs avant de réinitialiser tous les utilisateurs affectés.

Les étapes ci-après vous aideront à restaurer un utilisateur ou un groupe d’utilisateurs :

### <a name="pre-requisites"></a>Conditions préalables

1. Vous devez installer les modules Azure AD PowerShell appropriés. Dans une fenêtre de PowerShell, exécutez les commandes suivantes pour installer les modules :

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. Enregistrez la liste des ID/ID d’objet de l’utilisateur affecté sur votre ordinateur en tant que fichier texte contenant un ID par ligne. Prenez note de l’emplacement du fichier.
1. Enregistrez le script suivant sur votre ordinateur, et prenez note de l’emplacement du script :

```powershell
<# 
//********************************************************
//*                                                      *
//*   Copyright (C) Microsoft. All rights reserved.      *
//*                                                      *
//********************************************************
#>

param($path)

# Define Remediation Fn
function RemediateUser {

    param  
    (
        $ObjectId
    )

    $user = Get-MsolUser -ObjectId $ObjectId

    Write-Host "Checking if user is eligible for rollback: UPN: "  $user.UserPrincipalName  " ObjectId: "  $user.ObjectId -ForegroundColor Yellow

    $hasMfaRelyingParty = $false
    foreach($p in $user.StrongAuthenticationRequirements)
    {
        if ($p.RelyingParty -eq "*")
        {
            $hasMfaRelyingParty = $true
            Write-Host "User was enabled for per-user MFA." -ForegroundColor Yellow
        }
    }

    if ($user.StrongAuthenticationMethods.Count -gt 0 -and -not $hasMfaRelyingParty)
    {
        Write-Host $user.UserPrincipalName " is eligible for rollback" -ForegroundColor Yellow
        Write-Host "Rolling back user ..." -ForegroundColor Yellow
        Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName $user.UserPrincipalName
        Write-Host "Successfully rolled back user " $user.UserPrincipalName -ForegroundColor Green
    }
    else
    {
        Write-Host $user.UserPrincipalName " is not eligible for rollback. No action required."
    }

    Write-Host ""
    Start-Sleep -Milliseconds 750
}

# Connect
Import-Module MSOnline
Connect-MsolService

foreach($line in Get-Content $path)
{
    RemediateUser -ObjectId $line
}
```

### <a name="rollback"></a>Restauration

Dans une fenêtre PowerShell, exécutez la commande suivante après la mise à jour des emplacements en surbrillance. Entrez les informations d’identification de l’administrateur général quand vous y êtes invité. Le script génère le résultat de chaque opération de mise à jour d’utilisateur.

`<script location> -path <user file location>`

## <a name="disable-preview-experience"></a>Désactiver l’expérience de préversion

Pour désactiver l’expérience de préversion pour vos utilisateurs, procédez comme suit :

1. Connectez-vous au portail Azure en tant qu’administrateur général ou administrateur d’utilisateurs.
2. Accédez à **Azure Active Directory**, **Paramètres utilisateur**, **Gérer les paramètres des fonctionnalités préliminaires du volet d’accès**.
3. Sous **Users can use preview features for registering and managing security info** (les utilisateurs peuvent utiliser les fonctionnalités en préversion pour inscrire et gérer les informations de sécurité), positionnez le sélecteur sur **None** (Aucune), puis cliquez sur **Enregistrer**.

Les utilisateurs ne seront plus invités à s’inscrire à l’aide de l’expérience en préversion.

## <a name="next-steps"></a>Étapes suivantes

[Apprenez-en davantage sur la préversion publique d’inscription convergée pour la réinitialisation de mot de passe et l’authentification multifacteur Azure en libre-service](concept-registration-mfa-sspr-converged.md)