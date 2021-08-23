---
title: 'Démarrage rapide : Configurer l’authentification unique SAML pour une application'
titleSuffix: Azure AD
description: Ce guide de démarrage rapide vous présente en détail le processus de configuration de l’authentification unique (SSO) basée sur SAML d’une application dans votre locataire Azure Active Directory (Azure AD).
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/23/2020
ms.author: davidmu
ms.reviewer: ergleenl
ms.openlocfilehash: cded1a874c48cab43e24a19acc6707aa8de7d1e7
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2021
ms.locfileid: "114669400"
---
# <a name="quickstart-set-up-saml-based-single-sign-on-for-an-application"></a>Démarrage rapide : Configurer l’authentification unique SAML pour une application

Familiarisez-vous avec les connexions utilisateur simplifiées en configurant l’authentification unique d’une application que vous avez ajoutée à votre locataire Azure Active Directory (Azure AD). Dès l’authentification unique configurée, vos utilisateurs peuvent se connecter à une application avec leurs informations d’identification Azure AD. L’authentification unique est incluse dans l’édition gratuite d’Azure AD.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’authentification unique d’une application que vous avez ajoutée à votre locataire Azure AD, vous avez besoin des éléments suivants :

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un des rôles suivants : Administrateur général, Administrateur d’application cloud, Administrateur d’application ou propriétaire du principal de service.
- Une application prenant en charge SSO et qui a déjà été préconfigurée puis ajoutée à la galerie Azure AD. La plupart des applications peuvent utiliser Azure AD pour SSO. Les applications de la galerie Azure AD sont préconfigurées. Si votre application n’est pas listée ou qu’il s’agit d’une application personnalisée, vous pouvez toujours l’utiliser avec Azure AD. Consultez les tutoriels et autres documents dans la table des matières. Ce guide de démarrage rapide porte sur les applications qui ont été préconfigurées pour l’authentification unique puis ajoutées à la galerie Azure AD par les développeurs d’applications.
- Facultatif : effectuer [Voir vos applications](view-applications-portal.md).
- Facultatif : effectuer [Ajouter une application](add-application-portal.md).
- Facultatif : effectuer [Configurer une application](add-application-portal-configure.md).
- Facultatif : suivre [Affecter des utilisateurs à une application](add-application-portal-assign-users.md).

>[!IMPORTANT]
>Pour tester les étapes de ce guide de démarrage rapide, utilisez un environnement de non-production.

## <a name="enable-single-sign-on-for-an-app"></a>Activer l’authentification unique pour une application

Dès que vous avez terminé l’ajout d’une application à votre locataire Azure AD, la page de vue d’ensemble correspondante s’affiche. Si vous configurez une application qui a déjà été ajoutée, consultez le premier guide de démarrage rapide. Il vous explique étape par étape comment afficher les applications ajoutées à votre locataire.

Pour configurer l’authentification unique pour une application :

1. Dans le portail Azure AD, sélectionnez **Applications d’entreprise**. Ensuite, recherchez et sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.
1. Dans la section **Gérer**, sélectionnez **Authentification unique** pour ouvrir le volet **Authentification unique** à des fins de modification.

    > [!IMPORTANT]
    > Si l’application utilise la norme OpenID Connect (OIDC) pour l’authentification unique, vous ne verrez pas d’option d’authentification unique dans la navigation. Reportez-vous au Guide de démarrage rapide sur l’authentification unique basée sur OIDC pour savoir comment la configurer.

1. Sélectionnez **SAML** pour ouvrir la page de configuration de SSO. Dans cet exemple, l’application que nous configurons pour SSO est GitHub. Une fois que l’application GitHub est configurée, vos utilisateurs peuvent s’y connecter en utilisant leurs informations d’identification à partir de votre locataire Azure AD.

    :::image type="content" source="media/add-application-portal-setup-sso/github-sso.png" alt-text="Capture d’écran montrant la page de configuration de l’authentification unique sur GitHub.":::

1. Le processus de configuration d’une application afin d’utiliser Azure AD pour l’authentification unique basée sur SAML varie d’une application à l’autre. Il existe un lien d’aide pour GitHub. Pour trouver d’autres guides d’applications, consultez [Tutoriels pour l’intégration d’applications SaaS avec Azure Active Directory](/azure/active-directory/saas-apps/).
1. Suivez le guide afin de configurer l’authentification unique pour l’application. De nombreuses applications ont des exigences spécifiques en matière d’abonnement pour la fonctionnalité SSO. Par exemple, GitHub nécessite un abonnement Enterprise.
    > [!TIP]
    > Pour en savoir plus sur les options de configuration SAML, consultez [Configurer l’authentification unique SAML](configure-saml-single-sign-on.md).

> [!TIP]
> Vous pouvez automatiser la gestion des applications à l’aide de l’API Graph. Consultez [Automatiser la gestion des applications avec l’API Microsoft Graph](/graph/application-saml-sso-configure-api).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous aurez terminé cette série de guides de démarrage rapide, supprimez l’application pour nettoyer votre locataire de test. La suppression de l’application est traitée dans le dernier guide de démarrage rapide de cette série : consultez [Supprimer une application](delete-application-portal.md).

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour découvrir comment supprimer une application.
> [!div class="nextstepaction"]
> [Supprimer une application](delete-application-portal.md)
