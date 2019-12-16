---
title: Inscrire une application auprès de la plateforme d’identités Microsoft | Azure
description: Découvrez comment ajouter et inscrire une application à l’aide de la plateforme d’identités Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 05/09/2019
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: aragra, lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58a68b89ce38664c0d0ec87b509fe5f872cfd78c
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74921413"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Démarrage rapide : Inscrire une application avec la plateforme d’identités Microsoft

Les développeurs et les fournisseurs de logiciels en tant que service (SaaS) peuvent développer des services cloud commerciaux ou des applications métier qui peuvent être intégrés à la plateforme d’identités Microsoft pour fournir un processus de connexion et d’autorisation sécurisé à leurs services.

Ce guide de démarrage rapide vous explique comment ajouter et inscrire une application à l’aide de l’expérience **Inscriptions d’applications** dans le portail Azure afin de l’intégrer à la plateforme d’identités Microsoft. Pour en savoir plus sur les nouvelles fonctionnalités et les améliorations de la nouvelle expérience permettant d’inscrire des applications, consultez [ce billet de blog](https://developer.microsoft.com/graph/blogs/new-app-registration/).

## <a name="register-a-new-application-using-the-azure-portal"></a>Inscrire une nouvelle application à l’aide du Portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Si votre compte vous propose un accès à plusieurs locataires, sélectionnez votre compte en haut à droite et définissez votre session de portail sur le locataire Azure AD souhaité.
1. Recherchez et sélectionnez **Azure Active Directory**. Sur la plage **Active Directory**, sélectionnez **Inscriptions d’applications**, puis **Nouvelle inscription**.
1. Lorsque la page **Inscrire une application** s’affiche, saisissez les informations d’inscription de votre application :

   - **Nom** : saisissez un nom d’application cohérent qui s’affichera pour les utilisateurs de l’application.
   - **Types de compte pris en charge** : sélectionnez les comptes à prendre en charge par l’application.

       | Types de comptes pris en charge | Description |
       |-------------------------|-------------|
       | **Comptes dans cet annuaire organisationnel uniquement** | Sélectionnez cette option si vous générez une application métier. Cette option n’est pas disponible si vous n’inscrivez pas l’application dans un répertoire.<br><br>Cette option ne mappe qu’à un compte Azure AD à locataire unique.<br><br>Il s’agit de l’option par défaut, à moins que vous n’inscriviez l’application hors d’un répertoire. Dans les cas où l’application est inscrite hors d’un répertoire, l’option par défaut est multi-locataire et comptes Microsoft personnels. |
       | **Comptes dans un annuaire organisationnel** | Sélectionnez cette option si vous voulez cibler tous les clients professionnels ou du domaine éducatif.<br><br>Cette option ne mappe qu’à un compte Azure AD multi-locataire.<br><br>Si vous avez inscrit l’application comme compte Azure AD à locataire unique seulement, vous pouvez le mettre à jour vers un compte Azure AD multi-locataire et inversement via le panneau **Authentification**. |
       | **Comptes dans un annuaire organisationnel et comptes personnels Microsoft** | Sélectionnez cette option pour cibler l’ensemble le plus large de clients.<br><br>Cette option mappe à des comptes Microsoft personnels et Azure AD multi-locataires.<br><br>Si vous avez inscrit l’application comme comptes Microsoft personnels et Azure AD multi-locataires, vous ne pouvez pas modifier cela dans l’interface utilisateur. Vous devez utiliser l’éditeur de manifeste de l’application pour modifier les types de compte pris en charge. |

   - **URI de redirection (facultatif)** : sélectionnez le type d’application que vous créez, **Web** ou **Client public (mobile et bureau)** , puis entrez l’URI de redirection (ou URL de réponse).
       - Pour les applications web, indiquez l’URL de base de votre application. Par exemple, `https://localhost:31544` peut être l’URL pour une application web en cours d’exécution sur votre ordinateur local. Les utilisateurs peuvent utiliser cette URL pour se connecter à une application web cliente.
       - Pour les applications de client public, indiquez l’URI utilisé par Azure AD pour retourner les réponses de jeton. Entrez une valeur spécifique de votre application, par exemple, `myapp://auth`.

     Pour voir des exemples spécifiques pour les applications web ou natives, consultez les [Guides de démarrage rapides](https://docs.microsoft.com/azure/active-directory/develop).

1. Lorsque vous avez terminé, sélectionnez **Inscrire**.

    [![Affiche l’écran d’enregistrement d’une nouvelle application dans le portail Azure](./media/quickstart-add-azure-ad-app-preview/new-app-registration-expanded.png)](./media/quickstart-add-azure-ad-app-preview/new-app-registration-expanded.png#lightbox)

Azure AD assigne un ID d’application unique (client) à votre application, et vous êtes redirigé vers la page **Vue d’ensemble** de votre application. Pour ajouter des fonctionnalités supplémentaires à votre application, vous pouvez sélectionner d’autres options de configuration, dont la personnalisation, les certificats et les secrets, les autorisations API, et plus encore.

[![Exemple de page de vue d’ensemble d’application nouvellement inscrite](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les [autorisations et consentement](v2-permissions-and-consent.md).
- Pour activer des fonctionnalités de configuration supplémentaires dans l’inscription de votre application (par exemple, informations d’identification, autorisations, activation de la connexion pour les utilisateurs à partir d’autres locataires), consultez ces guides de démarrage rapide :
    - [Configurer une application cliente pour accéder aux API web](quickstart-configure-app-access-web-apis.md)
    - [Configurer une application pour exposer les API web](quickstart-configure-app-expose-web-apis.md)
    - [Modifier les comptes pris en charge par une application](quickstart-modify-supported-accounts.md)
- Choisissez un [guide de démarrage rapide](https://docs.microsoft.com/azure/active-directory/develop) pour rapidement générer une application et ajouter des fonctionnalités telles que l’obtention de jetons, l’actualisation de jetons, la connexion d’un utilisateur et l’affichage des informations utilisateur et bien plus encore.
- Pour en savoir plus sur les deux objets Azure AD représentant une application inscrite et la relation entre ces objets, consultez [Objets application et principal du service dans Azure Active Directory (Azure AD)](app-objects-and-service-principals.md).
- Pour en savoir plus sur les directives de personnalisation que vous devez suivre lors du développement d’applications, consultez [Directives de personnalisation des applications](howto-add-branding-in-azure-ad-apps.md).
