---
title: Configurer une authentification SSO en un clic à partir de la galerie d’applications Azure AD | Microsoft Docs
description: Étapes pour configurer une authentification SSO en un clic à partir de la galerie d’applications Azure AD
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: e0416991-4b5d-4b18-89bb-91b6070ed3ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 358240823da469551e254356fc0613bea20d78c5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67064813"
---
# <a name="one-click-sso-feature-for-azure-ad-gallery-applications"></a>Fonctionnalité authentification SSO en un clic pour les applications de la galerie Azure AD

 Dans ce didacticiel, vous allez apprendre à effectuer une authentification SSO en un clic pour toutes les applications SAML qui fournissent l’interface utilisateur pour la configuration SSO.

## <a name="introduction-to-one-click-sso"></a>Introduction à l’authentification SSO en un clic

La fonctionnalité d’authentification SSO en un clic est introduite pour configurer l’authentification unique pour les applications de la galerie des applications Azure AD qui prennent en charge le protocole SAML. Nous avons fourni cette option sur la page de configuration SSO Azure AD pour permettre à nos clients de configurer automatiquement leurs métadonnées Azure AD côté application. L’objectif consiste à aider les clients à configurer rapidement le SSO avec un minimum d’efforts. 

## <a name="advantages-of-the-one-click-sso"></a>Avantages de l’authentification SSO en un clic

- Accélération de la configuration SSO des applications de la galerie là où les clients devaient procéder à une configuration manuelle côté application.
- Renforcement de l’efficacité et de la précision de la configuration
- Aucune communication de partenaire ou de support nécessaires pour l’installation, car l’application fournit l’interface utilisateur pour la configuration SAML.

## <a name="prerequisites"></a>Prérequis

- Abonnement Administrateur actif pour les applications à configurer pour l’authentification SSO en un clic.
- **Extension du navigateur My Apps Secure Sign-in** de Microsoft installée dans le navigateur. Si vous souhaitez en savoir plus sur cette extension, veuillez cliquer sur ce [lien](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="one-click-sso-feature-step-by-step-details"></a>Procédure pas à pas pour l’authentification SSO en un clic

1. Ajoutez l’application à partir de la galerie d’applications Azure AD.

2. Cliquez sur Authentification unique.

3. Cliquez sur Activer l’authentification unique.

4. Indiquez les valeurs de obligatoires pour la configuration dans la section Configuration SAML de base.

    > [!NOTE] 
    > Si l’application requiert une configuration de revendications personnalisées, configurez les avant d’effectuer l’authentification SSO en un clic.

5. Si la fonctionnalité d’authentification SSO en un clic est implémentée pour n’importe quelle application de la galerie, l’écran suivant s’affiche. Si l’extension **Extension du navigateur My Apps Secure Sign-in** n’est pas déjà installée, vous devez cliquer sur l’option **Installer l’extension**.

    ![Installer l’extension du navigateur My Apps Secure Sign-in](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. Après avoir ajouté l’extension au navigateur, cliquez sur **Setup Application Name** (Configurer le nom de l’application) qui vous redirige vers le portail d’administration de l’application. Vous devez vous connecter en tant qu’administrateur pour accéder à l’application.

    ![Configurer le nom de l’application](./media/one-click-sso-tutorial/setup-sso.png)

7. Cette extension de navigateur configure automatiquement l’application pour vous. Elle vous demande d’abord de confirmer que vous souhaitez continuer. Cliquez sur **Oui**.

    ![Enregistrement des données renseignées automatiquement](./media/one-click-sso-tutorial/save-autopopulate.png)

    > [!NOTE]
    > Si vous devez procéder à des étapes supplémentaires pour configurer votre application, des messages s’affichent pour vous indiquer quoi faire. 

8. Une fois la configuration terminée, cliquez sur **Ok** pour enregistrer les modifications.

    ![Enregistrer les données renseignées automatiquement](./media/one-click-sso-tutorial/save-data.png)

9. Un message de confirmation s’affiche et vos paramètres SSO sont correctement configurés. Vous pouvez ensuite tester l’application.

    ![SSO configuré](./media/one-click-sso-tutorial/sso-configured.png)

10. Une fois la configuration réussie, l’application se ferme, et vous revenez au portail Azure.

11. Vous pouvez cliquer sur le bouton Tester pour tester l’authentification unique.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
* [Présentation de l’extension du navigateur My Apps Secure Sign-in](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)
 