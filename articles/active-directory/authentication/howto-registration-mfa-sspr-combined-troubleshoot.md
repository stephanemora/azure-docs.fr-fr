---
title: Résoudre les problèmes d’inscription combinée pour Azure AD SSPR et MFA (version préliminaire) - Azure Active Directory
description: Résoudre les problèmes de l’authentification multifacteur Azure AD et l’inscription de combinées de réinitialisation de mot de passe libre-service (version préliminaire)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f799b671e6216b402aa988aa66da4c2cfc44693
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317525"
---
# <a name="troubleshooting-combined-security-information-registration-preview"></a>Résolution des problèmes combinée d’inscription des informations de sécurité (version préliminaire)

Les informations fournies dans cet article peuvent guider les administrateurs à la résolution des problèmes avec l’expérience d’inscription combiné signalé par les utilisateurs finaux.

|     |
| --- |
| L’enregistrement d’informations de sécurité combinée pour la réinitialisation du mot de passe libre-service Azure multi-Factor Authentication et Azure AD est une fonctionnalité en préversion publique d’Azure Active Directory. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="audit-logs"></a>Journaux d’audit

Les événements consignés pour l’inscription combinée sont sous la catégorie « Méthodes d’authentification » dans Azure AD journaux d’audit.

![Interface montrant une certaine sécurité événements d’informations d’inscription d’un nouvel utilisateur dans le répertoire des journaux de Azure AD d’Audit](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

Vous trouverez ci-dessous tous les événements d’audit générés par une inscription combinée :

| Activité | Statut | Motif | Description |
| --- | --- | --- | --- |
| L’utilisateur a inscrit toutes les informations de sécurité requis | Succès | Utilisateur a inscrit toutes les informations de sécurité requis. | Cet événement se produit lorsqu’un utilisateur a terminé l’inscription.|
| L’utilisateur a inscrit toutes les informations de sécurité requis | Échec | L’utilisateur a annulé l’inscription des informations de sécurité. | Cet événement se produit lorsqu’un utilisateur annule l’enregistrement à partir du mode d’interruption.|
| Informations de sécurité utilisateur inscrit | Succès | Inscrit par l’utilisateur « méthode ». | Cet événement se produit lorsqu’un utilisateur s’inscrit à une méthode individuelle. « Méthode » peut être l’application d’authentification, téléphone, E-mail, questions de sécurité, mot de passe, téléphone secondaire, etc.| 
| Utilisateur passé en revue les informations de sécurité | Succès | Utilisateur évaluation réussie des informations de sécurité. | Cet événement se produit lorsqu’un utilisateur clique sur « Looks good » sur la page de révision des informations de sécurité.|
| Utilisateur passé en revue les informations de sécurité | Échec | Échec de l’utilisateur passer en revue les informations de sécurité. | Cet événement se produit lorsqu’un utilisateur clique sur « Recherche de bon » sur les informations de sécurité de la page vérifier, mais quelque chose échoue sur le serveur principal.|
| Informations de sécurité d’utilisateur supprimé | Succès | Utilisateur supprimé « méthode ». | Cet événement se produit lorsqu’un utilisateur supprime une méthode individuelle. « Méthode » peut être l’application d’authentification, téléphone, E-mail, questions de sécurité, mot de passe, téléphone secondaire, etc.|
| Informations de sécurité d’utilisateur supprimé | Échec | Échec de l’utilisateur à supprimer la « méthode ». | Cet événement se produit lorsqu’un utilisateur tente de supprimer une méthode, mais elle échoue pour une raison quelconque. « Méthode » peut être l’application d’authentification, téléphone, E-mail, questions de sécurité, mot de passe, téléphone secondaire, etc.|
| Informations de sécurité par défaut de l’utilisateur a changé | Succès | Utilisateur a modifié les informations de sécurité par défaut à la « méthode ». | Cet événement se produit lorsqu’un utilisateur modifie sa méthode par défaut. « Méthode » peut être la notification d’application Authenticator, de code de mon application d’authentification ou d’un jeton, appelez le + X XXXXXXXXXX, texte un code à + X XXXXXXXXX, etc.|
| Informations de sécurité par défaut de l’utilisateur a changé | Échec | Échec de l’utilisateur modifier les informations de sécurité par défaut à la « méthode ». | Cet événement se produit lorsqu’un utilisateur tente de modifier leur méthode par défaut, mais elle échoue pour une raison quelconque. « Méthode » peut être notification d’application Authenticator, un code de mon application d’authentification ou d’un jeton, appel + X XXXXXXXXXX, un code à + X XXXXXXXXX, etc. de texte.|

## <a name="troubleshooting-interrupt-mode"></a>Résolution des problèmes de mode d’interruption

| Symptôme | Étapes de dépannage |
| --- | --- |
| Je ne vois pas les méthodes que je m’attendais à voir. | 1. Vérifiez si l’utilisateur possède un rôle d’administrateur Azure AD. Si Oui, passez en revue les différences de stratégie d’administrateur SSPR. <br> 2. Déterminer si l’utilisateur est interrompue en raison d’une mise en œuvre de MFA d’inscription ou de mise en œuvre de l’inscription SSPR. Passez en revue l’organigramme sous modes combinée d’inscription pour déterminer quelles méthodes doivent être indiquées. <br> 3. Déterminer comment la stratégie MFA ou SSPR a été récemment. Si la modification a été récemment, il peut prendre un certain temps pour la stratégie de mise à jour se propager.|

## <a name="troubleshooting-manage-mode"></a>Résolution des problèmes de gérer le mode

| Symptôme | Étapes de dépannage |
| --- | --- |
| Je n’ai pas la possibilité d’ajouter une méthode particulière. | 1. Déterminer si la méthode est activée pour l’authentification Multifacteur ou pour SSPR. <br> 2. Si la méthode est activée, réenregistrez les stratégies et patientez 1 à 2 heures avant de le tester à nouveau. <br> 3. Si la méthode est activée, assurez-vous que l’utilisateur n’a pas déjà configuré le nombre maximal de cette méthode qu’ils sont autorisés à configurer.|

## <a name="disable-combined-registration"></a>Désactiver l’inscription combinée

Quand un utilisateur a inscrit son numéro de téléphone et/ou l’application mobile dans le nouveau combinés expérience, les tampons de notre service un jeu d’indicateurs (StrongAuthenticationMethods) pour ces méthodes sur cet utilisateur. Cette fonctionnalité permet à l’utilisateur effectuer une authentification multifacteur (MFA) Azure avec ces méthodes chaque fois qu’une telle authentification est requise.

La propriété StrongAuthenticationMethods est définie pour les méthodes que les utilisateurs inscrivent via la nouvelle expérience. Si un administrateur active la préversion, les utilisateurs s’inscrivent via la nouvelle expérience, puis quand l’administrateur désactive la préversion, les utilisateurs peuvent également être inscrits pour l’authentification multifacteur à leur insu.

Si un utilisateur qui a effectué combiné d’inscription accède à la page d’inscription de réinitialisation du mot de passe libre-service en cours, à [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup), il seront invité à effectuer une MFA avant de pouvoir accéder à cette page. Cette étape est un comportement attendu d’un point de vue technique mais, pour les utilisateurs qui se sont précédemment inscrits pour SSPR uniquement, il s’agit d’un nouveau comportement. Bien que cette étape supplémentaire améliore la situation de sécurité de l’utilisateur en fournissant un niveau supplémentaire de sécurité, il arrive que les administrateurs souhaitent réinitialiser leurs utilisateurs de façon à ce qu’ils ne puissent plus effectuer d’authentification multifacteur.  

### <a name="how-to-roll-back-users"></a>Comment réinitialiser des utilisateurs

Si, en tant qu’administrateur, vous souhaitez réinitialiser les paramètres d’authentification multifacteur d’un utilisateur, nous avons créé un script PowerShell qui efface la propriété StrongAuthenticationMethods pour l’application mobile et/ou le numéro de téléphone d’un utilisateur. Si vous exécutez ce script pour vos utilisateurs, ceux-ci devront se réinscrire pour l’authentification multifacteur si nécessaire. Nous vous recommandons de tester la réinitialisation avec un ou deux utilisateurs avant de réinitialiser tous les utilisateurs affectés.

Les étapes ci-après vous aideront à restaurer un utilisateur ou un groupe d’utilisateurs :

#### <a name="prerequisites"></a>Conditions préalables

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

#### <a name="rollback"></a>Restauration

Dans une fenêtre PowerShell, exécutez la commande suivante après la mise à jour des emplacements en surbrillance. Entrez les informations d’identification de l’administrateur général quand vous y êtes invité. Le script génère le résultat de chaque opération de mise à jour d’utilisateur.

`<script location> -path <user file location>`

### <a name="disable-preview-experience"></a>Désactiver l’expérience de préversion

Pour désactiver l’expérience de préversion pour vos utilisateurs, procédez comme suit :

1. Connectez-vous au portail Azure en tant qu’administrateur général ou administrateur d’utilisateurs.
2. Accédez à **Azure Active Directory**, **Paramètres utilisateur**, **Gérer les paramètres des fonctionnalités préliminaires du volet d’accès**.
3. Sous **Users can use preview features for registering and managing security info** (les utilisateurs peuvent utiliser les fonctionnalités en préversion pour inscrire et gérer les informations de sécurité), positionnez le sélecteur sur **None** (Aucune), puis cliquez sur **Enregistrer**.

Les utilisateurs ne seront plus invités à s’inscrire à l’aide de l’expérience en préversion.

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur la version préliminaire de combinée d’inscription pour la réinitialisation de mot de passe libre-service et de l’authentification multifacteur Azure](concept-registration-mfa-sspr-combined.md)
