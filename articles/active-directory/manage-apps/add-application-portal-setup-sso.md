---
title: 'Démarrage rapide : Configurer l’authentification unique (SSO) pour une application dans votre locataire Azure Active Directory (Azure AD)'
description: Ce guide de démarrage rapide vous guide tout au long du processus de configuration de l’authentification unique (SSO) pour une application dans votre locataire Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: f90edf525a8deb35376d6637090e7b424b2fd658
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86038988"
---
# <a name="quickstart-set-up-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Démarrage rapide : Configurer l’authentification unique (SSO) pour une application dans votre locataire Azure Active Directory (Azure AD)

Prenez en main les connexions utilisateur simplifiées en configurant l’authentification unique pour une application que vous avez ajoutée à votre locataire Azure AD. Une fois l’authentification unique configurée, vos utilisateurs peuvent se connecter à une application à l’aide de leurs informations d’identification Azure AD. L’authentification unique est incluse dans l’édition gratuite d’Azure AD.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’authentification unique pour une application que vous avez ajoutée à votre locataire Azure AD, vous avez besoin des éléments suivants :

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un des rôles suivants : Administrateur général, Administrateur d’application cloud, Administrateur d’application ou propriétaire du principal de service.
- Application qui prend en charge l’authentification unique et qui a déjà été préconfigurée et ajoutée à la galerie Azure AD. La plupart des applications peuvent utiliser Azure AD pour l’authentification unique. Les applications de la galerie Azure AD ont été préconfigurées. Si votre application n’est pas listée ou qu’il s’agit d’une application développée personnalisée, vous pouvez toujours l’utiliser avec Azure AD. Consultez les tutoriels et autres documents dans la table des matières. Ce guide de démarrage rapide se concentre sur les applications qui ont été préconfigurées pour l’authentification unique et ajoutées à la galerie Azure AD par les développeurs d’applications.
- (Facultatif : effectuer [Voir vos applications](view-applications-portal.md)).
- (Facultatif : effectuer [Ajouter une application](add-application-portal.md)).
- (Facultatif : effectuer [Configurer une application](add-application-portal-configure.md)).


>[!IMPORTANT]
>Nous vous recommandons d’utiliser un environnement de non-production pour tester les étapes de ce guide de démarrage rapide.


## <a name="enable-single-sign-on-for-an-app"></a>Activer l’authentification unique pour une application

Quand vous avez terminé d’ajouter une application à votre locataire Azure AD, la page de vue d’ensemble correspondante s’affiche immédiatement. Si vous configurez une application qui a déjà été ajoutée, regardez le premier guide de démarrage rapide, qui vous guide dans l’affichage des applications ajoutées à votre locataire. 

Pour configurer l’authentification unique pour une application :

1. Dans le portail Azure AD, sélectionnez **Applications d’entreprise** puis recherchez et sélectionnez l’application que vous souhaitez configurer pour l’authentification unique.
2. Dans la section Gérer, sélectionnez **Authentification unique** pour ouvrir le volet Propriétés à des fins de modification.
    :::image type="content" source="media/add-application-portal-setup-sso/configure-sso.png" alt-text="Montre la page de configuration de l’authentification unique dans le portail Azure AD.":::
3. Sélectionnez SAML pour ouvrir la page de configuration de l’authentification unique. Dans cet exemple, l’application que nous configurons pour l’authentification unique est GitHub. Une fois GitHub configuré, vos utilisateurs peuvent s’y connecter en utilisant leurs informations d’identification à partir de notre locataire Azure AD.
    :::image type="content" source="media/add-application-portal-setup-sso/github-sso.png" alt-text="Montre la page de configuration de l’authentification unique sur GitHub.":::
4. Le processus de configuration d’une application afin d’utiliser Azure AD pour l’authentification unique basée sur SAML varie d’une application à l’autre. Notez qu’il existe un lien vers de l’aide pour GitHub. Vous trouverez des guides pour d’autres applications à l’adresse suivante : https://docs.microsoft.com/azure/active-directory/saas-apps/
5. Suivez le guide afin de configurer l’authentification unique pour l’application. De nombreuses applications ont des exigences spécifiques en matière d’abonnement pour la fonctionnalité SSO. Par exemple, GitHub nécessite un abonnement Enterprise.
    :::image type="content" source="media/add-application-portal-setup-sso/github-pricing.png" alt-text="Montre l’option d’authentification unique dans l’abonnement Enterprise de la page des prix GitHub.":::


## <a name="next-steps"></a>Étapes suivantes

- [Supprimer une application](delete-application-portal.md)
