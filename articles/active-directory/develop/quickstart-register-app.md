---
title: Inscrire une application à l’aide de la plateforme d’identités Microsoft (préversion) | Azure
description: Découvrez comment ajouter et inscrire une application à l’aide de la plateforme d’identités Microsoft.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/25/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: e4e667c9a9490d164b9fb1c90580ceb30989a7dc
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988755"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform-preview"></a>Guide de démarrage rapide : Inscrire une application à l’aide de la plateforme d’identités Microsoft (préversion)

Les développeurs et les fournisseurs de logiciels en tant que service (SaaS) peuvent développer des services cloud commerciaux ou des applications métier qui peuvent être intégrés à la plateforme d’identités Microsoft pour fournir un processus de connexion et d’autorisation sécurisé à leurs services.

Ce guide de démarrage rapide vous explique comment ajouter et inscrire une application dans le portail Azure afin de l’intégrer à la plateforme d’identités Microsoft.

## <a name="prerequisite"></a>Configuration requise

Pour démarrer, vous devez intégrer l’expérience de préversion pour les inscriptions d’applications dans le portail Azure. Les étapes figurant dans ce démarrage rapide correspondent à la nouvelle interface utilisateur et ne fonctionnent que si vous participez à l’expérience de préversion.

## <a name="register-a-new-application-using-the-azure-portal"></a>Inscrire une nouvelle application à l’aide du Portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Si votre compte vous propose un accès à plusieurs locataires, sélectionnez votre compte en haut à droite et définissez votre session de portail sur le locataire Azure AD souhaité.
1. Dans le volet de navigation gauche, sélectionnez le service **Azure Active Directory**, sélectionnez ensuite **inscriptions d’applications (préversion) > Nouvelle inscription**.
1. Lorsque la page **Inscrire une application** s’affiche, saisissez les informations d’inscription de votre application :

    - **Nom** : saisissez un nom d’application cohérent qui s’affichera pour les utilisateurs de l’application.
    - **Types de compte pris en charge** : sélectionnez les comptes à prendre en charge par l’application.

        | Types de comptes pris en charge | Description |
        |-------------------------|-------------|
        | **Comptes dans cet annuaire organisationnel uniquement** | Sélectionnez cette option si vous générez une application métier. Cette option n’est pas disponible si vous n’inscrivez pas l’application dans un répertoire.<br><br>Cette option ne mappe qu’à un compte Azure AD à locataire unique.<br><br>Il s’agit de l’option par défaut, à moins que vous n’inscriviez l’application hors d’un répertoire. Dans les cas où l’application est inscrite hors d’un répertoire, l’option par défaut est multi-locataire et comptes Microsoft personnels. |
        | **Comptes dans un annuaire organisationnel** | Sélectionnez cette option si vous voulez cibler tous les clients professionnels ou du domaine éducatif.<br><br>Cette option ne mappe qu’à un compte Azure AD multi-locataire.<br><br>Si vous avez inscrit l’application comme compte Azure AD à locataire unique seulement, vous pouvez le mettre à jour vers un compte Azure AD multi-locataire et inversement via le panneau **Authentification**. |
        | **Comptes dans un annuaire organisationnel et comptes personnels Microsoft** | Sélectionnez cette option pour cibler l’ensemble le plus large de clients.<br><br>Cette option mappe à des comptes Microsoft personnels et Azure AD multi-locataires.<br><br>Si vous avez inscrit l’application comme comptes Microsoft personnels et Azure AD multi-locataires, vous ne pouvez pas modifier cela dans l’interface utilisateur. Vous devez utiliser l’éditeur de manifeste de l’application pour modifier les types de compte pris en charge. |

    - **URI de redirection (facultatif)** : sélectionnez le type d’application que vous créez, **Web** ou **Client public (mobile et bureau)**, puis entrez l’URI de redirection (ou URL de réponse).
        - Pour les applications web, indiquez l’URL de base de votre application. Par exemple, `http://localhost:31544` peut être l’URL pour une application web en cours d’exécution sur votre ordinateur local. Les utilisateurs peuvent utiliser cette URL pour se connecter à une application web cliente.
        - Pour les applications de client public, indiquez l’URI utilisé par Azure AD pour retourner les réponses de jeton. Entrez une valeur spécifique de votre application, par exemple, `myapp://auth`.

    Pour voir des exemples spécifiques pour les applications web ou natives, consultez les [Guides de démarrage rapides](https://docs.microsoft.com/azure/active-directory/develop/#quickstarts).

1. Lorsque vous avez terminé, sélectionnez **Inscrire**.

    [![Inscrire une nouvelle application dans le portail Azure](./media/quickstart-add-azure-ad-app-preview/new-app-registration-expanded.png)](./media/quickstart-add-azure-ad-app-preview/new-app-registration-expanded.png#lightbox)

Azure AD assigne un ID d’application unique à votre application, et vous êtes redirigé vers la page **Vue d’ensemble** de votre application. Pour ajouter des fonctionnalités supplémentaires à votre application, vous pouvez sélectionner d’autres options de configuration, dont la personnalisation, les certificats et les secrets, les autorisations API, et plus encore.

[![Page Vue d’ensemble d’une application nouvellement inscrite](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les [autorisations et consentement](v2-permissions-and-consent.md).
- Pour activer des fonctionnalités de configuration supplémentaires dans l’inscription de votre application (par exemple, informations d’identification, autorisations, activation de la connexion pour les utilisateurs à partir d’autres locataires), consultez ces guides de démarrage rapide :
    - [Configurer une application cliente pour accéder aux API web](quickstart-configure-app-access-web-apis.md)
    - [Configurer une application pour exposer les API web](quickstart-configure-app-expose-web-apis.md)
    - [Modifier les comptes pris en charge par une application](quickstart-modify-supported-accounts.md)
- Choisissez un [guide de démarrage rapide](https://docs.microsoft.com/azure/active-directory/develop/#quickstarts) pour rapidement générer une application et ajouter des fonctionnalités telles que l’obtention de jetons, l’actualisation de jetons, la connexion d’un utilisateur et l’affichage des informations utilisateur et bien plus encore.
- Pour en savoir plus sur les deux objets Azure AD représentant une application inscrite et la relation entre ces objets, consultez [Objets application et principal du service dans Azure Active Directory (Azure AD)](app-objects-and-service-principals.md).
- Pour en savoir plus sur les directives de personnalisation que vous devez suivre lors du développement d’applications, consultez [Directives de personnalisation des applications](howto-add-branding-in-azure-ad-apps.md).
