---
title: Consentement de l’administrateur pour les connexions de comptes LinkedIn - Azure Active Directory | Microsoft Docs
description: Explique comment activer ou désactiver les connexions de compte d’intégration LinkedIn dans les applications Microsoft Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/29/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1130885cc3168582935264ffaad9fd7a8ba3c60b
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64920260"
---
# <a name="integrate-linkedin-account-connections-in-azure-active-directory"></a>Intégrer des connexions de comptes LinkedIn dans Azure Active Directory

Vous pouvez autoriser les utilisateurs de votre organisation à accéder à leurs connexions LinkedIn dans certaines applications Microsoft. Aucune donnée n’est partagée jusqu'à ce que les utilisateurs de consentement pour se connecter à leurs comptes. Vous pouvez intégrer votre organisation dans Azure Active Directory (Azure AD) [centre d’administration](https://aad.portal.azure.com).

> [!IMPORTANT]
> Les connexions de comptes LinkedIn définissant est actuellement déployée pour les organisations Azure AD. Lorsqu’elle est transférée à votre organisation, il est activé par défaut.
> 
> Exceptions :
> * Le paramètre n’est pas disponible pour les clients qui utilisent Microsoft Cloud for US Government, Microsoft Cloud Germany ou Azure et Office 365 gérés par 21Vianet en Chine.
> * Le paramètre est désactivé par défaut pour les locataires approvisionnés en Allemagne. Notez que le paramètre n’est pas disponible pour les clients qui utilisent Microsoft Cloud Germany.
> * Le paramètre est désactivé par défaut pour les locataires approvisionnés en France.
>
> Une fois que les connexions de comptes LinkedIn sont activées pour votre organisation, les connexions de compte fonctionner une fois que les utilisateurs autoriser les applications qui accèdent aux données d’entreprise en leur nom. Pour plus d’informations sur le paramètre de consentement de l’utilisateur, consultez [comment supprimer l’accès d’un utilisateur à une application](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="enable-linkedin-account-connections-in-the-azure-portal"></a>Activer les connexions de comptes LinkedIn dans le portail Azure

Vous pouvez activer les connexions de comptes LinkedIn pour les utilisateurs que vous souhaitez accéder, à partir de l’ensemble de votre organisation aux seuls les utilisateurs sélectionnés dans votre organisation.

1. Se connecter à la [centre d’administration Azure AD](https://aad.portal.azure.com/) avec un compte qui est un administrateur général pour l’organisation Azure AD.
1. Sélectionnez **Utilisateurs**.
1. Dans le panneau **Utilisateurs**, sélectionnez **Paramètres utilisateur**.
1. Sous **connexions de comptes LinkedIn**, autoriser les utilisateurs à se connecter à leurs comptes pour accéder à leurs connexions LinkedIn dans certaines applications Microsoft. Aucune donnée n’est partagée jusqu'à ce que les utilisateurs de consentement pour se connecter à leurs comptes.

    * Sélectionnez **Oui** pour activer le service pour tous les utilisateurs de votre organisation
    * Sélectionnez **sélectionnés** pour activer un groupe d’utilisateurs sélectionnés dans votre organisation
    * Sélectionnez **non** pour retirer votre consentement à partir de tous les utilisateurs de votre organisation

    ![Intégrer des connexions de comptes LinkedIn dans l’organisation](./media/linkedin-integration/linkedin-integration.png)

1. Lorsque vous avez terminé, sélectionnez **enregistrer** pour enregistrer vos paramètres.

> [!Important]
> L’intégration LinkedIn n’est pas entièrement activée pour vos utilisateurs jusqu'à ce qu’ils donnent leur consentement pour se connecter à leurs comptes. Aucune donnée n’est partagée lorsque vous activez les connexions de comptes pour vos utilisateurs.

### <a name="assign-selected-users-with-a-group"></a>Affecter des utilisateurs sélectionnés à un groupe
Nous avons remplacé l’option « Selected » qui spécifie une liste d’utilisateurs avec l’option pour sélectionner un groupe d’utilisateurs afin que vous pouvez activer la capacité à connecter des comptes LinkedIn et Microsoft pour un seul groupe plutôt que de nombreux utilisateurs individuels. Si vous n’avez pas les connexions de comptes LinkedIn est activées pour les utilisateurs individuels sélectionnés, vous n’avez pas besoin de faire quoi que ce soit. Si vous avez activé les connexions de comptes LinkedIn pour les utilisateurs individuels sélectionnés, vous devez :

1. Obtenir la liste actuelle des utilisateurs individuels
1. Déplacer des utilisateurs individuels est actuellement activées pour un groupe
1. Utilisez le groupe de la précédente que le groupe sélectionné dans les connexions de comptes LinkedIn définissant dans le centre d’administration Azure AD.

> [!NOTE]
> Même si vous ne déplacez pas les utilisateurs individuels actuellement sélectionnés à un groupe, ils peuvent toujours voir les informations LinkedIn dans les applications Microsoft.

### <a name="get-the-current-list-of-selected-users"></a>Obtenir la liste actuelle des utilisateurs sélectionnés

1. Connectez-vous à Microsoft 365 avec votre compte d’administrateur.
1. Accédez à https://linkedinselectedusermigration.azurewebsites.net/ Vous verrez la liste des utilisateurs qui sont sélectionnés pour les connexions de comptes LinkedIn.
1. Exporter la liste vers un fichier CSV.

### <a name="move-the-currently-selected-individual-users-to-a-group"></a>Déplacer des utilisateurs individuels actuellement sélectionnés à un groupe

1. Lancement de PowerShell
1. Installez le module Azure AD en exécutant `Install-Module AzureAD`
1. Exécutez le script qui suit :

  ``` PowerShell
  $groupId = "GUID of the target group"
  
  $users = Get-Content 
  Path to the CSV file
  
  $i = 1
  foreach($user in $users} { Add-AzureADGroupMember -ObjectId $groupId -RefObjectId $user ; Write-Host $i Added $user ; $i++ ; Start-Sleep -Milliseconds 10 }
  ```

Pour utiliser le groupe à partir de l’étape 2 en tant que le groupe sélectionné dans les connexions de comptes LinkedIn définissant dans le centre d’administration Azure AD, consultez [LinkedIn activer les connexions de comptes dans le portail Azure](#enable-linkedin-account-connections-in-the-azure-portal).

## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>Utilisez la stratégie de groupe pour activer les connexions de comptes LinkedIn

1. Télécharger les [fichiers modèles d’administration Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
1. Extrayez les fichiers **ADMX** et copiez-les dans votre magasin central.
1. Ouvrez la gestion des stratégies de groupe.
1. Créez un objet de stratégie de groupe avec le paramètre suivant : **Configuration utilisateur** > **Modèles d’administration** > **Microsoft Office 2016** > **Divers** > **Afficher les fonctionnalités LinkedIn dans les applications Office**.
1. Sélectionnez **Activé** ou **Désactivé**.
  
   État | Résultat
   ------ | ------
   **Activé** | Le paramètre **Afficher les fonctionnalités LinkedIn dans les applications Office** dans les options d’Office 2016 est activé. Les utilisateurs de votre organisation peuvent utiliser les fonctionnalités LinkedIn dans leurs applications Office 2016.
   **Désactivé** | Le paramètre **Afficher les fonctionnalités LinkedIn dans les applications Office** dans les options d’Office 2016 est désactivé et les utilisateurs finaux ne peuvent pas le modifier. Les utilisateurs de votre organisation ne peuvent pas utiliser les fonctionnalités LinkedIn dans leurs applications Office 2016.

Cette stratégie de groupe affecte uniquement les applications Office 2016 installées sur un ordinateur local. Si les utilisateurs désactivent LinkedIn dans leurs applications Office 2016, ils peuvent toujours voir les fonctionnalités LinkedIn dans Office 365.

## <a name="next-steps"></a>Étapes suivantes

* [Consentement de l’utilisateur et le partage de LinkedIn des données](linkedin-user-consent.md)

* [Informations et fonctionnalités LinkedIn dans les applications Microsoft](https://go.microsoft.com/fwlink/?linkid=850740)

* [Centre d’aide LinkedIn](https://www.linkedin.com/help/linkedin)

* [Afficher vos paramètres d’intégration LinkedIn actuels dans le portail Azure](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
