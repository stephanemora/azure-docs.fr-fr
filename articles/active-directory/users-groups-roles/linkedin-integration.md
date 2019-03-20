---
title: Donner son consentement pour les services LinkedIn pour votre organisation - Azure Active Directory | Microsoft Docs
description: Explique comment activer et désactiver l’intégration LinkedIn aux applications Microsoft dans Azure Active Director
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: abcb1696efe44293d01153aa37a9835ba5f43370
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58199696"
---
# <a name="consent-to-linkedin-integration-for-your-azure-active-directory-organization"></a>Donner son consentement pour l’intégration LinkedIn pour votre organisation Azure Active Directory

Dans cet article, vous pouvez apprendre à activer ou désactiver l’intégration LinkedIn pour votre organisation dans le centre d’administration Azure Active Directory (Azure AD).

> [!IMPORTANT]
> Le paramètre d’intégration de LinkedIn est actuellement déployé pour les organisations Azure AD. Lorsqu’elle est transférée à votre organisation, il est activé par défaut.
> 
> Exceptions :
> * Le paramètre n’est pas disponible pour les clients qui utilisent Microsoft Cloud for US Government, Microsoft Cloud Germany ou Azure et Office 365 gérés par 21Vianet en Chine.
> * Le paramètre est désactivé par défaut pour les locataires approvisionnés en Allemagne. Notez que le paramètre n’est pas disponible pour les clients qui utilisent Microsoft Cloud Germany.
> * Le paramètre est désactivé par défaut pour les locataires approvisionnés en France.
>
> L’intégration fonctionne uniquement si vous avez activé *et* une fois que les utilisateurs autoriser les applications qui accèdent aux données d’entreprise en leur nom. Pour plus d’informations sur le paramètre de consentement de l’utilisateur, consultez [comment supprimer l’accès d’un utilisateur à une application](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="enable-or-disable-linkedin-integration-for-your-users-in-the-azure-portal"></a>Activer ou désactiver l’intégration de LinkedIn pour vos utilisateurs dans le portail Azure

Vous pouvez activer ou désactiver l’intégration de LinkedIn pour votre locataire entier ou seulement pour les utilisateurs sélectionnés dans votre locataire.

1. Connectez-vous au [centre d’administration Azure Active Directory](https://aad.portal.azure.com/) en utilisant un compte d’administrateur général pour le locataire Azure AD.
2. Sélectionnez **Utilisateurs**.
3. Dans le panneau **Utilisateurs**, sélectionnez **Paramètres utilisateur**.
4. Sous **LinkedIn account connections (Connexions de comptes LinkedIn)**  :

   * Sélectionnez **Oui** à donner son consentement pour tous les utilisateurs de l’organisation pour se connecter à leurs comptes pour accéder à leurs contacts LinkedIn dans certaines applications Microsoft.
   * Sélectionnez **sélectionnés** à donner son consentement pour les utilisateurs de l’organisation à se connecter à leurs comptes pour accéder à leurs contacts LinkedIn dans certaines applications Microsoft.
   * Sélectionnez **non** pour retirer votre consentement pour les utilisateurs de votre organisation pour se connecter à leurs comptes pour accéder à leurs contacts LinkedIn dans certaines applications Microsoft.
    ![Activation de l’intégration LinkedIn dans l’organisation](./media/linkedin-integration/linkedin-integration.png)
5. Enregistrer vos paramètres lorsque vous avez terminé en sélectionnant **Enregistrer**.

## <a name="enable-or-disable-linkedin-integration-for-your-users-in-group-policy"></a>Activer ou désactiver l’intégration de LinkedIn pour vos utilisateurs dans une stratégie de groupe

1. Télécharger les [fichiers modèles d’administration Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Extrayez les fichiers **ADMX** et copiez-les dans votre magasin central.
3. Ouvrez la gestion des stratégies de groupe.
4. Créez un objet de stratégie de groupe avec le paramètre suivant : **Configuration utilisateur** > **Modèles d’administration** > **Microsoft Office 2016** > **Divers** > **Afficher les fonctionnalités LinkedIn dans les applications Office**.
5. Sélectionnez **Activé** ou **Désactivé**.
  
   État | Résultat
   ------ | ------
   **Activé** | Le paramètre **Afficher les fonctionnalités LinkedIn dans les applications Office** dans les options d’Office 2016 est activé. Les utilisateurs de votre organisation peuvent utiliser les fonctionnalités LinkedIn dans leurs applications Office.
   **Désactivé** | Le paramètre **Afficher les fonctionnalités LinkedIn dans les applications Office** dans les options d’Office 2016 est désactivé et les utilisateurs finaux ne peuvent pas le modifier. Les utilisateurs de votre organisation ne peuvent pas utiliser les fonctionnalités LinkedIn dans leurs applications Office 2016.

Cette stratégie de groupe affecte uniquement les applications Office 2016 installées sur un ordinateur local. Les utilisateurs peuvent voir les fonctionnalités LinkedIn dans les cartes de profil Office 365 même s’ils désactivent LinkedIn dans leurs applications Office 2016.

## <a name="learn-more"></a>En savoir plus

* [Intégrer LinkedIn à votre organisation](linkedin-user-consent.md)

* [Informations et fonctionnalités LinkedIn dans les applications Microsoft](https://go.microsoft.com/fwlink/?linkid=850740)

* [Centre d’aide LinkedIn](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>Étapes suivantes

Utilisez le lien suivant pour afficher vos paramètres d’intégration LinkedIn actuels dans le portail Azure :

[Afficher vos paramètres d’intégration LinkedIn actuels dans le portail Azure](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
