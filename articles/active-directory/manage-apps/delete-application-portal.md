---
title: 'Démarrage rapide : Supprimer une application de votre locataire'
titleSuffix: Azure AD
description: Ce guide de démarrage rapide utilise le portail Azure pour supprimer une application de votre locataire Azure Active Directory (Azure AD).
services: active-directory
author: davidmu
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/23/2021
ms.author: davidmu
ms.reviewer: alamaral
ms.openlocfilehash: 04e4e011773426b5be7f6ef9c00f7969c35d20fd
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2021
ms.locfileid: "114668421"
---
# <a name="quickstart-delete-an-application-from-your-tenant"></a>Démarrage rapide : Supprimer une application de votre locataire

Ce guide de démarrage rapide utilise le portail Azure pour supprimer une application qui a été ajoutée à votre locataire Azure Active Directory (Azure AD).

Pour en savoir plus sur SSO et Azure, consultez [Qu’est-ce que l’authentification unique (SSO)](what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour supprimer une application de votre locataire Azure AD, vous avez besoin des éléments suivants :

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un des rôles suivants : Administrateur général, Administrateur d’application cloud, Administrateur d’application ou propriétaire du principal de service.
- Facultatif : effectuer [Voir vos applications](view-applications-portal.md).
- Facultatif : effectuer [Ajouter une application](add-application-portal.md).
- Facultatif : effectuer [Configurer une application](add-application-portal-configure.md).
- Facultatif : suivre [Affecter des utilisateurs à une application](add-application-portal-assign-users.md).
- Facultatif : effectuer [Configurer l’authentification unique](add-application-portal-setup-sso.md).

>[!IMPORTANT]
>Pour tester les étapes de ce guide de démarrage rapide, utilisez un environnement de non-production.

> [!NOTE]
>Pour supprimer une application d’Azure AD, un utilisateur doit se voir attribuer l’un des rôles suivants : administrateur général, administrateur d’application Cloud, administrateur d’application ou propriétaire du principal de service.

## <a name="delete-an-application-from-your-azure-ad-tenant"></a>Supprimer une application de votre locataire Azure AD

Pour supprimer une application de votre locataire Azure AD :

1. Dans le portail Azure AD, sélectionnez **Applications d’entreprise**. Ensuite, recherchez et sélectionnez l’application que vous souhaitez supprimer. Dans ce cas, nous voulons supprimer **360 en ligne**.
1. À la section **Gérer** dans le volet gauche, sélectionnez **Propriétés**.
1. Sélectionnez **Supprimer**, puis sélectionnez **Oui** pour confirmer que vous souhaitez supprimer l’application de votre locataire Azure AD.

:::image type="content" source="media/add-application-portal/delete-application.png" alt-text="Capture d’écran de l’écran Propriétés illustrant le changement de logo.":::

> [!TIP]
> Vous pouvez automatiser la gestion des applications à l’aide de l’API Graph. Consultez [Automatiser la gestion des applications avec l’API Microsoft Graph](/graph/application-saml-sso-configure-api).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous aurez terminé cette série de guides de démarrage rapide, supprimez l'application pour nettoyer votre locataire de test. La suppression d’application est traitée dans ce guide de démarrage rapide.

## <a name="next-steps"></a>Étapes suivantes

Vous avez terminé la série de guides de démarrage rapide. Ensuite, pour en savoir plus sur l’authentification unique (SSO), consultez [Qu’est-ce que l’authentification unique ?](what-is-single-sign-on.md) Vous pouvez aussi en apprendre davantage sur les bonnes pratiques relatives à la gestion des applications.
> [!div class="nextstepaction"]
> [Bonnes pratiques relatives à la gestion des applications](application-management-fundamentals.md)
