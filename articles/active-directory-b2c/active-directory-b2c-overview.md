---
title: Qu’est-ce qu’Azure Active Directory B2C ? | Microsoft Docs
description: Découvrez comment vous pouvez créer et gérer l’expérience de connexion à vos applications à l’aide d’Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 2f9c4acc1a2a98978e12aaf5356a5bc270b8d017
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2018
ms.locfileid: "52724024"
---
# <a name="what-is-azure-active-directory-b2c"></a>Qu’est-ce qu’Azure Active Directory B2C ?

Azure Active Directory (Azure AD) B2C est un service de gestion des identités qui vous permet de personnaliser et contrôler la façon dont les clients interagissent avec votre application. Cela comprend l’inscription, la connexion ainsi que la gestion des profils quand les clients utilisent vos applications. Vous pouvez, entre autres, choisir entre les applications pour iOS, Android et .NET. Azure AD B2C autorise ces actions tout en protégeant les identités de vos clients.

Configurez une application inscrite auprès d’Azure AD B2C pour prendre en charge de nombreuses actions de gestion des identités. Voici quelques exemples :

- Permettre à un client de s’inscrire dans le but d’utiliser votre application inscrite
- Permettre à un client inscrit de se connecter et de commencer à utiliser votre application
- Permettre à un client inscrit de modifier son profil
- Activer l’authentification multifacteur dans votre application
- Permettre à un client de s’inscrire et de se connecter avec des fournisseurs d’identité spécifiques
- Offrir une possibilité d’accès aux API que vous créez à partir de votre application
- Personnaliser l’apparence des expériences d’inscription et de connexion
- Gérer les sessions d’authentification unique pour votre application

## <a name="what-do-i-need-to-think-about-before-using-azure-ad-b2c"></a>À quoi dois-je penser avant d’utiliser Azure AD B2C ?

- Comment les clients pourront-ils interagir avec mon application ?
- Quelle expérience en matière d’interface utilisateur dois-je offrir aux clients ?
- Quel choix de fournisseurs d’identité dois-je proposer aux clients dans mon application ?
- Mon processus de connexion requiert-il l’exécution d’API supplémentaires ?

### <a name="customer-interaction"></a>Interaction avec les clients

Azure AD B2C prend en charge [OpenID Connect](https://openid.net/connect/) pour toutes les expériences utilisateur. Dans l’implémentation d’OpenID Connect par Azure AD B2C, votre application entame le parcours utilisateur en émettant des demandes d’authentification vers Azure AD B2C. Le résultat de la demande est un élément `id_token`. Ce jeton de sécurité définit l’identité du client.

Chaque application qui utilise Azure AD B2C doit être inscrite dans un locataire Azure AD B2C à l’aide du Portail Azure. Le processus d’inscription collecte et attribue des valeurs à votre application. Ces valeurs incluent un ID d’application qui identifie de manière unique l’application. Un URI de redirection qui peut être utilisé pour rediriger les réponses vers l’application est défini.

Le mode d’interaction de chaque application suit un modèle général similaire :

1. L’application invite le client à exécuter une stratégie.
2. Le client exécute la stratégie en fonction de la définition de cette dernière.
3. L’application reçoit un jeton de sécurité.
4. L’application utilise le jeton de sécurité pour essayer d’accéder à une ressource protégée.
5. Le serveur de ressources valide le jeton de sécurité afin de garantir l’octroi de l’accès.
6. L’application actualise régulièrement le jeton de sécurité.

Ces étapes varient légèrement selon le type d’application que vous créez.

Azure AD B2C interagit consécutivement avec les fournisseurs d’identité, les clients, d’autres systèmes et le répertoire local afin d’accomplir une tâche d’identité (par exemple, connexion d’un client, inscription d’un nouveau client ou réinitialisation d’un mot de passe). La plateforme sous-jacente qui établit la confiance mutuelle et qui exécute cette procédure est appelée « Infrastructure d’expérience d’identité ». Cette infrastructure et une stratégie (également appelée « parcours utilisateur » ou « stratégie d’infrastructure de confiance ») définissent explicitement les acteurs, les actions, les protocoles et la séquence d’étapes à effectuer.

Azure AD B2C protège vos applications contre les attaques par déni de service et les attaques de mot de passe. Azure AD B2C utilise des techniques de détection et d’atténuation, telles que les cookies SYN, ainsi que les limites de débit et de connexion, pour protéger les ressources contre les attaques par déni de service. L’atténuation couvre à la fois les attaques de mot de passe par force brute et celles basées sur un dictionnaire.

#### <a name="user-flows"></a>Flux d’utilisateurs

Chaque requête envoyée à Azure AD B2C spécifie un flux utilisateur, qui est une stratégie contrôlant la manière dont votre application interagit avec Azure AD B2C. Des flux utilisateur prédéfinis pour les tâches d’identité les plus courantes, telles que l’inscription, la connexion et la modification de profil, sont disponibles dans le portail Azure AD B2C.  Par exemple, un flux utilisateur d'inscription vous permet de contrôler les comportements en configurant les paramètres suivants :

- Comptes sociaux que le client utilise pour s’inscrire à l’application
- Données collectées auprès du client, telles que le nom ou le code postal
- Authentification multifacteur
- Apparence de toutes les pages d’inscription
- Informations renvoyées à l’application

#### <a name="custom-policies"></a>Stratégies personnalisées 

Les [stratégies personnalisées](active-directory-b2c-overview-custom.md) sont des fichiers config qui définissent le comportement de l’[infrastructure d’expérience d’identité](trustframeworkpolicy.md) dans votre locataire Azure AD B2C. Vous pouvez changer entièrement les stratégies personnalisées afin d’effectuer un grand nombre de tâches. Une stratégie personnalisée c’est un ou plusieurs fichiers au format XML qui se font mutuellement référence dans une chaîne hiérarchique. 

Selon vos besoins, différents types de stratégies personnalisées sont utilisées dans votre locataire Azure AD B2C et peuvent être réutilisées dans l’ensemble de vos applications. Cette flexibilité vous permet de définir et de modifier les expériences relatives à l’identité du client avec peu ou pas de modification du code. Les stratégies sont utilisées en ajoutant un paramètre de requête spécial dans les requêtes d’authentification HTTP.

Les stratégies personnalisées contrôlent les parcours utilisateur des manières suivantes :

- Définition des interactions avec les API pour capturer des informations supplémentaires, vérifier les revendications fournies par le client ou déclencher des processus externes.
- Modification du comportement en fonction des revendications à partir des API ou des revendications dans le répertoire comme *migrationStatus*.
- Tout workflow non couvert par les stratégies intégrées. Par exemple, collecte d’informations supplémentaires auprès d’un client lors d’une expérience de connexion et exécution d’une vérification d’autorisation concernant l’accès à une ressource.

### <a name="identity-providers"></a>Fournisseurs d’identité

Un fournisseur d’identité est un service qui authentifie les identités des clients et émet des jetons de sécurité. Dans Azure AD B2C, configurez un certain nombre de fournisseurs d’identité dans votre locataire, notamment un [compte Microsoft](active-directory-b2c-setup-msa-app.md), [Facebook](active-directory-b2c-setup-fb-app.md) ou [Amazon](active-directory-b2c-setup-amzn-app.md). 

Pour configurer un fournisseur d’identité dans votre locataire Azure AD B2C, vous devez enregistrer l’identificateur d’application ou l’identificateur client et le mot de passe ou la clé secrète client à partir de l’application de fournisseur d’identité que vous créez. Cet identificateur et ce mot de passe sont ensuite utilisés pour configurer votre application.

### <a name="user-interface-experience"></a>Expérience en matière d’interface utilisateur

La majeure partie du contenu HTML et CSS qui est présenté aux clients peut être contrôlée. La fonctionnalité de personnalisation de l’interface utilisateur de page vous permet de personnaliser l’apparence de toute stratégie. Vous conservez également la cohérence visuelle et de la marque entre votre application et Azure AD B2C en utilisant la fonctionnalité de personnalisation.

Azure AD B2C exécute le code dans le navigateur du client et utilise une approche moderne appelée partage des ressources cross-origin (CORS). Tout d’abord, vous spécifiez une URL dans une stratégie avec du contenu HTML personnalisé. Azure AD B2C fusionne des éléments de l’interface utilisateur avec le contenu HTML chargé depuis votre URL, puis affiche la page au client.

Vous envoyez des paramètres à Azure AD B2C dans une chaîne de requête. En transmettant le paramètre à votre point de terminaison HTML, le contenu de la page est modifié de façon dynamique. Par exemple, si vous changez l’image d’arrière-plan dans la page de connexion ou d’inscription Azure AD B2C en fonction d’un paramètre que vous transmettez depuis votre application web ou mobile.

## <a name="how-do-i-get-started-with-azure-ad-b2c"></a>Comment prendre en main Azure AD B2C ?

Dans Azure AD B2C, un locataire représente votre organisation et constitue un répertoire d’utilisateurs. Chaque locataire Azure AD B2C est distinct et indépendant des autres locataires Azure AD B2C. Un locataire contient des informations relatives aux clients qui se sont inscrits pour utiliser votre application. Il s’agit notamment des mots de passe, des données de profil et des autorisations.

Liez votre locataire Azure AD B2C à votre abonnement Azure pour activer l’ensemble des fonctionnalités et payer les frais d’utilisation. Pour permettre à vos clients de se connecter à votre application, vous devez l’inscrire dans un locataire Azure AD B2C.

Avant de configurer votre application pour l’utilisation d’Azure AD B2C, vous devez commencer par créer un locataire Azure AD B2C et inscrire votre application. Pour inscrire votre application, suivez la procédure décrite dans [Didacticiel : enregistrer une application pour activer l’inscription et la connexion à l’aide d’Azure Active Directory B2C](tutorial-register-applications.md).
  
Si vous êtes un développeur d’applications web ASP.NET, configurez votre application pour l’authentification des comptes en suivant la procédure décrite dans [Didacticiel : authentifier les utilisateurs avec Azure Active Directory B2C dans une application web ASP.NET](active-directory-b2c-tutorials-web-app.md).

Si vous êtes un développeur d’applications de bureau, configurez votre application pour l’authentification des comptes en suivant la procédure décrite dans [Tutorial: Enable a desktop application to authenticate with accounts using Azure AD B2C](active-directory-b2c-tutorials-desktop-app.md) (Didacticiel : authentifier les utilisateurs avec Azure Active Directory B2C dans une application de bureau).

Si vous êtes un développeur d’applications monopage à l’aide de Node.js, configurez votre application pour l’authentification des comptes en suivant la procédure décrite dans [Tutorial: Enable a single-page application to authenticate with accounts using Azure AD B2C](active-directory-b2c-tutorials-spa.md) (Didacticiel : authentifier les utilisateurs avec Azure Active Directory B2C dans une application monopage).

## <a name="next-steps"></a>Étapes suivantes

Commencez à configurer votre application pour les expériences d’inscription et de connexion en poursuivant le didacticiel.

> [!div class="nextstepaction"]
> [Didacticiel : créer un locataire Azure Active Directory B2C](tutorial-create-tenant.md)
