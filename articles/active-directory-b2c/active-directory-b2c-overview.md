---
title: Qu’est-ce qu’Azure Active Directory B2C ? | Microsoft Docs
description: Découvrez comment créer et gérer des expériences d’identité, comme l’inscription, la connexion et la gestion de profils, dans votre application avec Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 02/20/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 33c6f9e06439ebeb17608aa374532ac4f7a020cf
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64701349"
---
# <a name="what-is-azure-active-directory-b2c"></a>Qu’est-ce qu’Azure Active Directory B2C ?

Azure Active Directory (Azure AD) B2C est un service de gestion d’identités entreprise-client. Ce service vous permet de personnaliser et de contrôler la façon dont les utilisateurs interagissent de façon sécurisée avec vos applications web, de bureau, mobiles ou monopages. Avec Azure AD B2C, les utilisateurs peuvent s’inscrire, se connecter, réinitialiser les mots de passe et modifier des profils. Azure AD B2C implémente une forme des protocoles OpenID Connect et OAuth 2.0. Les jetons de sécurité et leurs revendications sont ce qu’il y a de plus important dans l’implémentation de ces protocoles : ils vous permettent de fournir un accès sécurisé aux ressources.

Un *parcours utilisateur* est une demande qui spécifie une stratégie, qui contrôle le comportement de l’interaction entre l’utilisateur et votre application avec Azure AD B2C. Vous pouvez définir les parcours utilisateur dans Azure AD B2C de deux façons. 

Si vous êtes développeur d’applications avec ou sans maîtrise des identités, vous pouvez choisir de définir des flux utilisateur d’identité courants avec le portail Azure. Si vous êtes un professionnel des identités, un intégrateur de systèmes, un consultant ou membre d’une équipe d’identité interne, si vous connaissez bien les flux OpenID Connect, et que vous comprenez bien les fournisseurs d’identité et l’authentification basée sur les revendications, vous pouvez choisir des stratégies personnalisées basées sur XML.

Avant de commencer à définir un parcours utilisateur, vous devez créer un locataire Azure AD B2C, et inscrire votre application et l’API dans ce locataire. Une fois que vous avez effectué ces tâches, vous pouvez commencer la définition d’un parcours utilisateur avec des flux utilisateur ou des stratégies personnalisées. Vous pouvez aussi ajouter ou changer des fournisseurs d’identité, ou personnaliser la façon dont l’utilisateur effectue le parcours.

## <a name="protocols-and-tokens"></a>Protocoles et jetons

Azure AD B2C prend en charge les [protocoles OpenID Connect et OAuth 2.0](active-directory-b2c-reference-protocols.md) pour les parcours utilisateur. Dans l’implémentation d’OpenID Connect par Azure AD B2C, votre application entame le parcours utilisateur en émettant des demandes d’authentification vers Azure AD B2C. 

Le résultat d’une demande à Azure AD B2C est un jeton de sécurité, comme un [jeton d’ID ou un jeton d’accès](active-directory-b2c-reference-tokens.md). Ce jeton de sécurité définit l’identité de l’utilisateur. Les jetons sont reçus de points de terminaison Azure AD B2C, comme un point de terminaison`/token` ou `/authorize`. Avec ces jetons, vous pouvez accéder à des revendications qui peuvent être utilisées pour valider une identité et autoriser l’accès à des ressources sécurisées.

## <a name="tenants-and-applications"></a>Locataires et applications

Dans Azure AD B2C, un *locataire* représente votre organisation et est un annuaire d’utilisateurs. Chaque locataire Azure AD B2C est distinct et indépendant des autres locataires Azure AD B2C. Vous disposez peut-être déjà d’un locataire Azure Active Directory : le locataire Azure AD B2C est un autre locataire. Un locataire contient des informations sur les utilisateurs qui se sont inscrits pour utiliser votre application. Il s’agit notamment des mots de passe, des données de profil et des autorisations. Pour plus d’informations, consultez [Tutoriel : Créer un locataire Azure Active Directory B2C](tutorial-create-tenant.md).

Avant de configurer votre application pour l’utilisation d’Azure AD B2C, vous devez d’abord l’inscrire dans le locataire avec le portail Azure. Le processus d’inscription collecte et attribue des valeurs à votre application. Ces valeurs comprennent un ID d’application, qui identifie de l’application de façon univoque, et un URI de redirection, qui est utilisé pour rediriger les réponses vers l’application.

Le mode d’interaction de chaque application suit un modèle général similaire :

1. L’application invite l’utilisateur à exécuter une stratégie.
2. L'utilisateur exécute la stratégie en fonction de la définition de celle-ci.
3. L’application reçoit un jeton.
4. L’application utilise le jeton pour essayer d’accéder à une ressource.
5. Le serveur de ressources valide le jeton pour confirmer que l’accès peut être accordé.
6. L’application actualise régulièrement le jeton.

Pour inscrire une application web, suivez les étapes du [Tutoriel : Inscrire une application pour activer l’abonnement et la connexion à l’aide d’Azure AD B2C](tutorial-register-applications.md). Vous pouvez également [ajouter une application d’API web à votre locataire Azure Active Directory B2C](add-web-application.md) ou [ajouter une application cliente native à votre locataire Azure Active Directory B2C](add-native-application.md).

## <a name="user-journeys"></a>Parcours utilisateur

La stratégie dans un parcours utilisateur peut être définie comme un [flux utilisateur](active-directory-b2c-reference-policies.md) ou une [stratégie personnalisée](active-directory-b2c-overview-custom.md). Des flux utilisateur prédéfinis pour les tâches d’identité les plus courantes, comme l’inscription, la connexion et la modification de profil, sont disponibles dans le portail Azure.

Les parcours utilisateur vous permettent de contrôler les comportements en configurant les paramètres suivants :

- Comptes sociaux que l’utilisateur utilise pour s’inscrire à l’application
- Données collectées auprès de l’utilisateur, comme le prénom ou le code postal
- Authentification multifacteur
- Apparence des pages
- Informations renvoyées à l’application

Les stratégies personnalisées sont des fichiers de configuration qui définissent le comportement d’[Identity Experience Framework](trustframeworkpolicy.md) dans votre locataire Azure AD B2C. Identity Experience Framework est la plateforme sous-jacente qui établit la confiance mutuelle et qui exécute les étapes d’un parcours utilisateur. 

Vous pouvez changer entièrement les stratégies personnalisées afin d’effectuer un grand nombre de tâches. Une stratégie personnalisée c’est un ou plusieurs fichiers au format XML qui se font mutuellement référence dans une chaîne hiérarchique. Un [pack de démarrage](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) est disponible pour les stratégies personnalisées et permet d’activer des tâches d’identité courantes. 

Selon vos besoins, différents types de stratégies personnalisées ou de flux utilisateur sont utilisés dans votre locataire Azure AD B2C et peuvent être réutilisés dans les applications. Cette flexibilité vous permet de définir et de modifier les expériences relatives à l’identité utilisateur avec peu ou pas de modification du code. Les stratégies sont utilisées en ajoutant un paramètre de requête spécial dans les requêtes d’authentification HTTP. Pour créer votre propre stratégie personnalisée, consultez [Bien démarrer avec les stratégies personnalisées dans Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="identity-providers"></a>Fournisseurs d’identité 

Dans vos applications, vous souhaiterez peut-être permettre aux utilisateurs de se connecter avec différents fournisseurs d’identité. Un *fournisseur d’identité* crée, entretient et gère les informations d’identité tout en fournissant des services d’authentification pour les applications. Vous pouvez ajouter des fournisseurs d’identité qui sont pris en charge par Azure Active Directory B2C avec le portail Azure. 

Vous utilisez généralement un seul fournisseur d’identité dans votre application, mais vous pouvez en ajouter d’autres. Pour configurer un fournisseur d’identité dans votre locataire Azure AD B2C, vous devez d’abord créer une application sur le site de développeur du fournisseur d’identité, puis vous enregistrez l’identificateur de l’application ou l’identificateur du client, et le mot de passe ou le secret du client provenant de l’application de fournisseur d’identité que vous créez. Cet identificateur et ce mot de passe sont ensuite utilisés pour configurer votre application. 

Les articles suivants décrivent les étapes nécessaires pour ajouter certains des fournisseurs d’identité communs aux flux utilisateur :

- [Amazon](active-directory-b2c-setup-amzn-app.md)
- [Facebook](active-directory-b2c-setup-fb-app.md)
- [Compte Microsoft](active-directory-b2c-setup-msa-app.md)

Les articles suivants décrivent les étapes nécessaires pour ajouter certains des fournisseurs d’identité communs aux stratégies personnalisées :
- [Amazon](setup-amazon-custom.md)
- [Google](active-directory-b2c-custom-setup-goog-idp.md)
- [Compte Microsoft](active-directory-b2c-custom-setup-msa-idp.md)

Pour plus d’informations, consultez [Tutoriel : Ajouter des fournisseurs d’identité à vos applications dans Azure Active Directory B2C](tutorial-add-identity-providers.md).


## <a name="page-customization"></a>Personnalisation des pages

La majeure partie du contenu HTML et CSS qui est présenté aux clients dans un parcours utilisateur peut être contrôlée. Avec la personnalisation des pages, vous pouvez personnaliser l’apparence d’une stratégie personnalisée ou d’un flux utilisateur. Vous conservez également la cohérence visuelle et de la marque entre votre application et Azure AD B2C en utilisant la fonctionnalité de personnalisation. 

Azure AD B2C exécute le code dans le navigateur de l’utilisateur et utilise une approche moderne appelée CORS (Cross-Origin Resource Sharing, Partage des ressources cross-origin). Tout d’abord, vous spécifiez une URL dans une stratégie avec du contenu HTML personnalisé. Azure AD B2C fusionne des éléments d’interface utilisateur avec le contenu HTML chargé à partir de votre URL, puis affiche la page à l’utilisateur.

Vous envoyez des paramètres à Azure AD B2C dans une chaîne de requête. En transmettant le paramètre à votre point de terminaison HTML, le contenu de la page est modifié de façon dynamique. Par exemple, vous changez l’image d’arrière-plan dans la page de connexion ou d’inscription en fonction d’un paramètre que vous passez depuis votre application web ou mobile.

Pour personnaliser des pages dans un flux utilisateur, consultez [Tutoriel : Personnaliser l’interface des expériences utilisateur dans Azure Active Directory B2C](tutorial-customize-ui.md). Pour personnaliser des pages dans une stratégie personnalisée, consultez [Personnaliser l’interface utilisateur de votre application à l’aide d’une stratégie personnalisée dans Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md) ou [Configurer l’interface utilisateur avec du contenu dynamique à l’aide de stratégies personnalisées dans Azure Active Directory B2C](active-directory-b2c-ui-customization-custom-dynamic.md).

## <a name="developer-resources"></a>Ressources pour les développeurs

### <a name="client-applications"></a>Applications clientes

Vous pouvez choisir entre des applications pour [iOS](active-directory-b2c-devquickstarts-ios.md), [Android](active-directory-b2c-devquickstarts-android.md) et .NET, entre autres. Azure AD B2C autorise ces actions tout en protégeant les identités de vos utilisateurs.

Si vous êtes développeur d’applications web ASP.NET, configurez votre application pour l’authentification des comptes en suivant les étapes de [Tutoriel : Autoriser une application web à effectuer l’authentification avec des comptes à l’aide d’Azure Active Directory B2C](active-directory-b2c-tutorials-web-app.md).

Si vous êtes développeur d’applications de bureau, configurez votre application pour l’authentification des comptes en suivant les étapes de [Tutoriel : Autoriser une application de bureau à effectuer l’authentification avec des comptes à l’aide d’Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app.md).

Si vous êtes développeur d’applications monopages Node.js, configurez votre application pour l’authentification des comptes en suivant les étapes de [Tutoriel : Autoriser une application monopage à effectuer l’authentification avec des comptes à l’aide d’Azure Active Directory B2C](active-directory-b2c-tutorials-spa.md).

### <a name="apis"></a>API
Si vos applications web ou clientes doivent appeler des API, vous pouvez configurer un accès sécurisé à ces ressources dans Azure AD B2C.

Si vous êtes développeur d’applications web ASP.NET, configurez votre application pour l’appel d’une API protégée en suivant les étapes de [Tutoriel : Accorder l’accès à une API web ASP.NET à l’aide d’Azure Active Directory B2C](active-directory-b2c-tutorials-web-api.md).

Si vous êtes développeur d’applications de bureau, configurez votre application pour l’appel d’une API protégée en suivant les étapes de [Tutoriel : Accorder l’accès à une API web Node.js à partir d’une application de bureau avec Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app-webapi.md).

Si vous êtes développeur d’applications monopages Node.js, configurez votre application pour l’authentification des comptes en suivant la procédure décrite dans [Tutoriel : Accorder l’accès à une API web ASP.NET Core à partir d’une application monopage à l’aide d’Azure Active Directory B2C](active-directory-b2c-tutorials-spa-webapi.md).

### <a name="javascript"></a>JavaScript

Vous pouvez ajouter votre propre code côté client JavaScript à vos applications Azure Active Directory B2C. Pour configurer JavaScript dans votre application, vous définissez un [contrat de page](page-contract.md) et vous activez [JavaScript](javascript-samples.md) dans vos flux utilisateur ou vos stratégies personnalisées.

### <a name="user-accounts"></a>Comptes d'utilisateurs

De nombreuses tâches courantes de gestion des locataires doivent être effectuées par programmation. La gestion des utilisateurs en est un parfait exemple. Il peut être nécessaire de migrer un magasin d’utilisateurs existant vers un locataire Azure AD B2C. ou que vous souhaitiez héberger l’inscription des utilisateurs sur votre page et créer des comptes d’utilisateur dans votre répertoire Azure AD B2C en arrière-plan. Pour effectuer ces types de tâches, vous devez être en mesure de créer, de lire, de mettre à jour et de supprimer des comptes d’utilisateur. Vous pouvez effectuer ces tâches avec l’[API Graph d’Azure AD](active-directory-b2c-devquickstarts-graph-dotnet.md).

## <a name="next-steps"></a>Étapes suivantes

Commencez à configurer votre application pour les expériences d’inscription et de connexion en poursuivant le didacticiel.

> [!div class="nextstepaction"]
> [Tutoriel : Créer un locataire Azure Active Directory B2C](tutorial-create-tenant.md)
