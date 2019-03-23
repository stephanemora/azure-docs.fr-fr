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
ms.date: 03/21/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e07c53192ea2c8b792256af944c81c9c909dc55
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368118"
---
# <a name="consent-to-linkedin-account-connections-for-an-azure-active-directory-organization"></a>Donner son consentement pour les connexions de comptes LinkedIn pour une organisation Azure Active Directory

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

## <a name="use-the-azure-portal-to-enable-linkedin-account-connections"></a>Utiliser le portail Azure pour activer les connexions de comptes LinkedIn

Vous pouvez activer les connexions de comptes LinkedIn pour les utilisateurs que vous souhaitez accéder, à partir de l’ensemble de votre organisation aux seuls les utilisateurs sélectionnés dans votre organisation.

1. Se connecter à la [centre d’administration Azure AD](https://aad.portal.azure.com/) avec un compte qui est un administrateur général pour l’organisation Azure AD.
2. Sélectionnez **Utilisateurs**.
3. Dans le panneau **Utilisateurs**, sélectionnez **Paramètres utilisateur**.
4. Sous **connexions de comptes LinkedIn**, autoriser les utilisateurs à se connecter à leurs comptes pour accéder à leurs connexions LinkedIn dans certaines applications Microsoft. Aucune donnée n’est partagée jusqu'à ce que les utilisateurs de consentement pour se connecter à leurs comptes.

  * Sélectionnez **Oui** à donner son consentement au service pour tous les utilisateurs de l’organisation
  * Sélectionnez **sélectionnés** à donner son consentement pour seulement les utilisateurs sélectionnés dans l’organisation
  * Sélectionnez **non** pour retirer votre consentement pour les utilisateurs de votre organisation

    ![Intégrer des connexions de comptes LinkedIn dans l’organisation](./media/linkedin-integration/linkedin-integration.png)

5. Lorsque vous avez terminé, sélectionnez **enregistrer** pour enregistrer vos paramètres.
     
## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>Utilisez la stratégie de groupe pour activer les connexions de comptes LinkedIn

1. Télécharger les [fichiers modèles d’administration Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Extrayez les fichiers **ADMX** et copiez-les dans votre magasin central.
3. Ouvrez la gestion des stratégies de groupe.
4. Créez un objet de stratégie de groupe avec le paramètre suivant : **Configuration utilisateur** > **Modèles d’administration** > **Microsoft Office 2016** > **Divers** > **Afficher les fonctionnalités LinkedIn dans les applications Office**.
5. Sélectionnez **Activé** ou **Désactivé**.
  
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
