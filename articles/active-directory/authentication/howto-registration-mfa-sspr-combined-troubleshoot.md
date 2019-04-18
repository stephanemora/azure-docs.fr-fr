---
title: Résoudre les problèmes d’inscription combinée pour Azure AD SSPR et une authentification multifacteur (version préliminaire) - Azure Active Directory
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
ms.openlocfilehash: 40918493071fe0dd694c43e2b087a2bf7eb197d8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59489191"
---
# <a name="troubleshooting-combined-security-information-registration-preview"></a>Résolution des problèmes combinée d’inscription des informations de sécurité (version préliminaire)

Les informations contenues dans cet article sont destinés à guider les administrateurs à la résolution des problèmes signalés par des utilisateurs de l’expérience d’inscription combinées.

|     |
| --- |
| L’inscription d’informations de sécurité combinée pour Azure multi-Factor Authentication et de réinitialisation de mot de passe libre-service d’Azure Active Directory (Azure AD) est une fonctionnalité en préversion publique d’Azure AD. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="audit-logs"></a>Journaux d’audit

Les événements consignés pour l’inscription combinée se trouvent dans la catégorie de méthodes d’authentification dans Azure AD journaux d’audit.

![D’Audit Azure AD journaux des événements d’interface montrant l’inscription](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

Le tableau suivant répertorie tous les événements d’audit générés par une inscription combinée :

| Activité | Statut | Motif | Description |
| --- | --- | --- | --- |
| L’utilisateur a inscrit toutes les informations de sécurité requis | Succès | Utilisateur a inscrit toutes les informations de sécurité requis. | Cet événement se produit lorsqu’un utilisateur a terminé l’inscription.|
| L’utilisateur a inscrit toutes les informations de sécurité requis | Échec | L’utilisateur a annulé l’inscription des informations de sécurité. | Cet événement se produit lorsqu’un utilisateur annule l’enregistrement à partir du mode d’interruption.|
| Informations de sécurité utilisateur inscrit | Succès | Utilisateur inscrit *méthode*. | Cet événement se produit lorsqu’un utilisateur s’inscrit à une méthode individuelle. *Méthode* peut être l’authentificateur app, téléphone, E-mail, sécurité questions, application mot de passe, téléphone secondaire et ainsi de suite.| 
| Utilisateur passé en revue les informations de sécurité | Succès | Utilisateur évaluation réussie des informations de sécurité. | Cet événement se produit lorsqu’un utilisateur sélectionne **semble correct** sur la page de révision des informations de sécurité.|
| Utilisateur passé en revue les informations de sécurité | Échec | Échec de l’utilisateur passer en revue les informations de sécurité. | Cet événement se produit lorsqu’un utilisateur sélectionne **semble correct** sur les informations de sécurité, consultez page mais quelque chose échoue sur le serveur principal.|
| Informations de sécurité d’utilisateur supprimé | Succès | Utilisateur supprimé *méthode*. | Cet événement se produit lorsqu’un utilisateur supprime une méthode individuelle. *Méthode* peut être l’authentificateur app, téléphone, E-mail, sécurité questions, application mot de passe, téléphone secondaire et ainsi de suite.|
| Informations de sécurité d’utilisateur supprimé | Échec | Échec de l’utilisateur à supprimer *méthode*. | Cet événement se produit lorsqu’un utilisateur tente de supprimer une méthode, mais la tentative échoue pour une raison quelconque. *Méthode* peut être l’authentificateur app, téléphone, E-mail, sécurité questions, application mot de passe, téléphone secondaire et ainsi de suite.|
| Informations de sécurité par défaut de l’utilisateur a changé | Succès | Utilisateur a modifié les informations de sécurité par défaut *méthode*. | Cet événement se produit lorsqu’un utilisateur modifie la méthode par défaut. *Méthode* peut être notification d’application Authenticator, un code de mon application d’authentification ou d’un jeton, appelez le + X XXXXXXXXXX, texte code + X XXXXXXXXX et ainsi de suite.|
| Informations de sécurité par défaut de l’utilisateur a changé | Échec | Échec de l’utilisateur modifier les informations de sécurité par défaut *méthode*. | Cet événement se produit lorsqu’un utilisateur tente de modifier la méthode par défaut, mais la tentative échoue pour une raison quelconque. *Méthode* peut être notification d’application Authenticator, un code de mon application d’authentification ou d’un jeton, appelez le + X XXXXXXXXXX, texte code + X XXXXXXXXX et ainsi de suite.|

## <a name="troubleshooting-interrupt-mode"></a>Résolution des problèmes de mode d’interruption

| Symptôme | Étapes de dépannage |
| --- | --- |
| Je ne vois pas les méthodes que je m’attendais à voir. | 1. Vérifiez si l’utilisateur possède un rôle d’administrateur Azure AD. Si Oui, afficher les différences de stratégie d’administrateur SSPR. <br> 2. Déterminer si l’utilisateur est interrompue en raison de la mise en œuvre de l’inscription de l’authentification multifacteur ou de mise en œuvre de l’inscription SSPR. Consultez le [organigramme](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes) sous « Modes de l’inscription combiné » pour déterminer quelles méthodes doivent être indiquées. <br> 3. Déterminer comment la stratégie de l’authentification multifacteur ou SSPR a été récemment. Si la modification a été récemment, il peut prendre un certain temps pour la stratégie de mise à jour se propager.|

## <a name="troubleshooting-manage-mode"></a>Résolution des problèmes de gérer le mode

| Symptôme | Étapes de dépannage |
| --- | --- |
| Je n’ai pas la possibilité d’ajouter une méthode particulière. | 1. Déterminer si la méthode est activée pour l’authentification multifacteur ou pour SSPR. <br> 2. Si la méthode est activée, réenregistrez les stratégies et patientez 1 à 2 heures avant de le tester à nouveau. <br> 3. Si la méthode est activée, assurez-vous que l’utilisateur n’a pas déjà configuré le nombre maximal de cette méthode qu’ils sont autorisés à configurer.|

## <a name="disable-combined-registration"></a>Désactiver l’inscription combinée

Quand un utilisateur enregistre un numéro de téléphone et/ou l’application mobile dans le nouveau combinés expérience, les tampons de notre service un jeu d’indicateurs (StrongAuthenticationMethods) pour ces méthodes sur cet utilisateur. Cette fonctionnalité permet à l’utilisateur d’effectuer l’authentification multifacteur avec ces méthodes chaque fois que l’authentification multifacteur est requise.

Si un administrateur Active la version préliminaire, les utilisateurs de s’inscrire via la nouvelle expérience, puis l’administrateur désactive la version préliminaire, les utilisateurs peuvent à leur insu pour l’authentification multifacteur également enregistrés.

Si un utilisateur qui a effectué l’enregistrement combiné accède à la page d’inscription de réinitialisation du mot de passe libre-service actuelle à [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup), l’utilisateur sera invité à effectuer une authentification multifacteur avant de pouvoir accéder Cette page. Cette étape est attendue à partir d’un point de vue technique, mais il est nouveau pour les utilisateurs qui ont déjà été inscrits pour SSPR uniquement. Bien que cette étape supplémentaire améliore la posture de sécurité de l’utilisateur en fournissant un autre niveau de sécurité, administrateurs souhaiterez restaurer leurs utilisateurs afin qu’ils ne sont plus en mesure d’effectuer l’authentification multifacteur.  

### <a name="how-to-roll-back-users"></a>Comment réinitialiser des utilisateurs

Si, en tant qu’administrateur, voulez-vous réinitialiser les paramètres de l’authentification multifacteur d’un utilisateur, vous pouvez utiliser le script PowerShell fourni dans la section suivante. Le script efface la propriété StrongAuthenticationMethods pour l’application mobile d’un utilisateur et/ou le numéro de téléphone. Si vous exécutez ce script pour vos utilisateurs, ils devrez réinscrire pour l’authentification multifacteur s’ils en ont besoin. Nous vous recommandons de restauration de test avec un ou deux utilisateurs avant de revenir à tous les utilisateurs concernés.

Les étapes qui suivent vous aideront à restaurer un utilisateur ou un groupe d’utilisateurs.

#### <a name="prerequisites"></a>Conditions préalables

1. Installez les modules Azure AD PowerShell appropriés. Dans une fenêtre de PowerShell, exécutez les commandes suivantes pour installer les modules :

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. Enregistrer la liste des ID d’objet utilisateur affecté à votre ordinateur comme un fichier texte avec un ID par ligne. Prenez note de l’emplacement du fichier.
1. Enregistrez le script suivant sur votre ordinateur et prenez note de l’emplacement du script :

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

Dans une fenêtre PowerShell, exécutez la commande suivante, en fournissant les emplacements de fichier de script et l’utilisateur. Entrez les informations d’identification de l’administrateur général quand vous y êtes invité. Le script génère le résultat de chaque opération de mise à jour d’utilisateur.

`<script location> -path <user file location>`

### <a name="disable-the-preview-experience"></a>Désactiver l’expérience de préversion

Pour désactiver l’expérience de préversion pour vos utilisateurs, procédez comme suit :

1. Connectez-vous au portail Azure en tant qu’utilisateur administrateur.
2. Accédez à **Azure Active Directory** > **paramètres utilisateur** > **gérer les paramètres des fonctionnalités en version préliminaire de panneau accès**.
3. Sous **les utilisateurs peuvent utiliser les fonctionnalités en version préliminaire de l’inscription et la gestion des informations de sécurité**, la valeur est le sélecteur **aucun**, puis sélectionnez **enregistrer**.

Les utilisateurs seront plus invités à enregistrer à l’aide de l’expérience de préversion.

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur la version préliminaire de combinée d’inscription pour la réinitialisation de mot de passe libre-service et de l’authentification multifacteur Azure](concept-registration-mfa-sspr-combined.md)
