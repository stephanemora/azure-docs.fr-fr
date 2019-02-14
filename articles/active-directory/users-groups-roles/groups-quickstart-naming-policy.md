---
title: Démarrage rapide en matière d’ajout d’une stratégie d’affectation de noms pour les groupes Office 365 – Azure Active Directory | Microsoft Docs
description: Explique comment ajouter de nouveaux utilisateurs ou supprimer des utilisateurs existants dans Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9905e3d8b2e0760fca82e9d2c0bfb0164d8fc9fa
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56210422"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>Démarrage rapide : Stratégie d’affectation de noms pour les groupes dans Azure Active Directory

Dans le cadre de ce démarrage rapide, vous allez configurer une stratégie d’affectation de noms dans votre locataire Azure Active Directory (Azure AD) pour les groupes Office 365 créés par l’utilisateur, afin de faciliter le tri et la recherche des groupes de votre locataire. Par exemple, vous pouvez utiliser la stratégie d’affectation de noms pour :

* communiquer la fonction d’un groupe, l’appartenance, la région géographique ou le nom de la personne qui a créé le groupe ;
* faciliter le classement des groupes dans le carnet d’adresses ;
* empêcher l’utilisation de certains mots dans les noms et alias de groupe.

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="install-powershell-cmdlets"></a>Installer les applets de commande PowerShell

Veillez à désinstaller toute version ancienne du module Azure Active Directory PowerShell for Graph pour Windows PowerShell et à installer [Azure Active Directory PowerShell for Graph - Public Preview Release 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) avant d’exécuter les commandes PowerShell. 

1. Ouvrez l’application Windows PowerShell en tant qu’administrateur.
2. Désinstallez toute version précédente d’AzureADPreview.
  
  ```
  Uninstall-Module AzureADPreview
  ```
3. Installez la dernière version d’AzureADPreview.
  
  ```
  Install-Module AzureADPreview
  ```
Si vous êtes invité à accéder à un référentiel non approuvé, tapez **Y**. L’installation du nouveau module peut prendre quelques minutes.

## <a name="set-up-naming-policy"></a>Configurer la stratégie d’affectation de noms

### <a name="step-1-sign-in-using-powershell-cmdlets"></a>Étape 1 : Se connecter à l’aide d’applets de commande PowerShell

1. Ouvrez l’application Windows PowerShell. Vous n’avez pas besoin de privilèges élevés.

2. Exécutez les commandes suivantes pour préparer l’exécution des applets de commande.
  
  ```
  Import-Module AzureADPreview
  Connect-AzureAD
  ```
  Dans l’écran **Connectez-vous à votre compte** qui s’ouvre, entrez votre compte d’administrateur et votre mot de passe pour vous connecter à votre service, puis sélectionnez **Se connecter**.

3. Suivez les étapes de [Configuration des paramètres de groupe avec les applets de commande Azure Active Directory](groups-settings-cmdlets.md) pour créer des paramètres de groupe pour ce locataire.

### <a name="step-2-view-the-current-settings"></a>Étape 2 : Afficher les paramètres actuels

1. Visualisez les paramètres de stratégie d’affectation de noms actuels.
  
  ```
  $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
  ```
  
2. Affichez les paramètres de groupe actuels.
  
  ```
  $Setting.Values
  ```
  
### <a name="step-3-set-the-naming-policy-and-any-custom-blocked-words"></a>Étape 3 : Définir la stratégie d’affectation de noms et les éventuels mots bloqués personnalisés

1. Définissez les préfixes et les suffixes de nom de groupe dans Azure AD PowerShell. Pour que la fonctionnalité fonctionne correctement, [GroupName] doit être inclus dans le paramètre.
  
  ```
  $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
  ```
  
2. Définissez les mots bloqués personnalisés sur lesquels vous voulez imposer des restrictions. L’exemple suivant illustre la façon dont vous pouvez ajouter vos propres mots personnalisés.
  
  ```
  $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
  ```
  
3. Enregistrez les paramètres de la nouvelle stratégie pour qu’elle prenne effet, comme dans l’exemple suivant.
  
  ```
  Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
  ```
  
Vous avez terminé. Vous avez défini votre stratégie d’affectation de noms et ajouté vos mots bloqués personnalisés.

## <a name="clean-up-resources"></a>Supprimer des ressources

1. Supprimez les préfixes et les suffixes de nom de groupe dans Azure AD PowerShell.
  
  ```
  $Setting["PrefixSuffixNamingRequirement"] =""
  ```
  
2. Supprimez les mots bloqués personnalisés.
  
  ```
  $Setting["CustomBlockedWordsList"]=""
  ```
  
3. Enregistrez les paramètres.
  
  ```
  Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
  ```

## <a name="next-steps"></a>Étapes suivantes

Dans le cadre de ce démarrage rapide, vous avez appris à utiliser des applets de commande PowerShell afin de définir la stratégie d’affectation de noms pour votre locataire Azure AD.

Pour plus d’informations sur les stratégies d’affectation de noms, et notamment sur les contraintes techniques, sur l’ajout d’une liste de mots bloqués personnalisés et sur les expériences d’utilisateur final pour l’ensemble des applications Office 365, consultez l’article suivant.
> [!div class="nextstepaction"]
> [Présentation exhaustive des stratégies d’affectation de noms](groups-naming-policy.md)
