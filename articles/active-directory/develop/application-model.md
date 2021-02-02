---
title: Modèle d’application | Azure
titleSuffix: Microsoft identity platform
description: En savoir plus sur le processus d’inscription de votre application afin qu’elle puisse s’intégrer à la plateforme d’identités Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/28/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 86543b961698e736b2211553b0dca367b28158ef
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98795653"
---
# <a name="application-model"></a>Modèle d'application

Les applications peuvent connecter les utilisateurs elles-mêmes, ou déléguer la connexion à un fournisseur d’identité. Cet article décrit les étapes nécessaires pour inscrire une application auprès de la plateforme d’identités Microsoft.

## <a name="register-an-application"></a>Inscrire une application

Pour qu’un fournisseur d’identité sache qu’un utilisateur a accès à une application particulière, l’utilisateur et l’application doivent tous les deux être inscrits auprès du fournisseur d’identité. Lorsque vous inscrivez votre application auprès d’Azure Active Directory (Azure AD), vous fournissez une configuration d’identité pour votre application, qui lui permet de s’intégrer à la plateforme d’identités Microsoft. L’inscription de l’application vous permet également d’effectuer les opérations suivantes :

* Personnaliser la boîte de dialogue de connexion avec la marque de votre application. Cette personnalisation est importante, car la connexion est la première expérience de l’utilisateur avec votre application.
* Décider si vous souhaitez autoriser les utilisateurs à se connecter uniquement s’ils appartiennent à votre organisation. Cette architecture est connue sous le nom d’application à locataire unique. Vous pouvez également autoriser les utilisateurs à se connecter à l’aide d’un compte professionnel ou scolaire, ce qui est connu sous le nom d’application mutualisée. Vous pouvez également autoriser les comptes Microsoft personnels ou un compte de réseau social depuis LinkedIn, Google, etc.
* Demandez des autorisations d’étendue. Par exemple, vous pouvez demander l’étendue « user.read », qui accorde l’autorisation de lire le profil de l’utilisateur connecté.
* Préciser les étendues qui définissent l’accès à votre API web. En général, lorsqu’une application souhaite accéder à votre API, elle doit demander des autorisations pour les étendues que vous définissez.
* Partager un secret avec la plateforme d’identités Microsoft qui prouve l’identité de l’application. L’utilisation d’un secret est pertinente dans le cas où l’application est une application cliente confidentielle. Une application cliente confidentielle est une application qui peut conserver les informations d’identification en toute sécurité. Un serveur principal approuvé est requis pour stocker les informations d’identification.

Une fois l’application inscrite, elle reçoit un identificateur unique qu’elle partage avec la plateforme d’identités Microsoft lorsqu’elle demande des jetons. Si l’application est une [application cliente confidentielle](developer-glossary.md#client-application), elle partage également le secret ou la clé publique, selon que des certificats ou des secrets ont été utilisés.

La plateforme d’identités Microsoft représente des applications à l’aide d’un modèle qui remplit deux fonctions principales :

* Identifier l’application par les protocoles d’authentification qu’elle prend en charge.
* Fournir tous les identifiants, URL, secrets et informations connexes nécessaires à l’authentification.

La plateforme d’identités Microsoft :

* contient toutes les données nécessaires lors de l’authentification, au moment de l’exécution ;
* contient toutes les données permettant de déterminer les ressources susceptibles d’être demandées par une application pour obtenir l’accès, et les circonstances dans lesquelles une requête donnée doit être traitée.
* fournit l’infrastructure pour implémenter le provisionnement de l’application au sein du locataire du développeur, et de n’importe quel autre locataire Azure AD.
* gère le consentement de l’utilisateur au moment de la demande de jeton et facilite l’approvisionnement dynamique des applications entre les locataires.

Le *consentement* est le processus par lequel un propriétaire de ressources octroie à une application cliente l’accès à des ressources protégées, avec des autorisations spécifiques, en son nom. La plateforme d’identités Microsoft permet :

* aux utilisateurs et administrateurs d’accorder ou de refuser dynamiquement le consentement lorsque l’application souhaite accéder aux ressources en leur nom ;
* aux administrateurs d’avoir la décision finale sur ce que les applications sont autorisées à faire et quels utilisateurs peuvent utiliser des applications spécifiques, ainsi que sur la manière dont les ressources de répertoire sont consultées.

## <a name="multi-tenant-apps"></a>Applications multi-locataires

Dans la plateforme d’identités Microsoft, un [objet d’application](developer-glossary.md#application-object) décrit une application. Au moment du déploiement, la plateforme d’identités Microsoft utilise l’objet d’application en tant que blueprint pour créer un [principal de service](developer-glossary.md#service-principal-object). Ce dernier représente une instance concrète d’une application dans un annuaire ou un locataire. Le principal de service définit les actions réelles d’une application dans un répertoire cible spécifique, les utilisateurs qui peuvent s’en servir, les ressources auxquelles elle a accès, etc. La plateforme d’identités Microsoft crée un principal de service à partir d’un objet d’application, via le [consentement](developer-glossary.md#consent).

Le diagramme suivant illustre un flux de provisionnement simplifié piloté par consentement dans la plateforme d’identités Microsoft. Il montre deux locataires : *A* et *B*.

* Le *locataire A* possède l’application.
* Le *locataire B* instancie l’application via un principal de service.

![Diagramme illustrant un flux d’approvisionnement simplifié piloté par le consentement.](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

Dans ce flux d’approvisionnement :

1. Un utilisateur issu du locataire B tente de se connecter à l’application. Le point de terminaison d’autorisation demande un jeton pour l’application.
1. Les informations d’identification de l’utilisateur sont acquises et vérifiées à des fins d’authentification.
1. L’utilisateur est invité à donner son consentement pour que l’application accède au locataire B.
1. La plateforme d’identités Microsoft utilise l’objet d’application dans le locataire A en tant que blueprint pour créer un principal de service dans le locataire B.
1. L’utilisateur reçoit le jeton demandé.

Vous pouvez répéter ce processus pour d’autres locataires. Le locataire A conserve le blueprint pour l’application (objet d’application). Les utilisateurs et les administrateurs de tous les autres locataires où l’application a obtenu le consentement gardent le contrôle sur les actions de l’application par le biais de l’objet du principal de service correspondant dans chaque locataire. Pour plus d’informations, consultez [Objets d’application et de principal de service dans la plateforme d’identités Microsoft](app-objects-and-service-principals.md).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’authentification et l’autorisation dans la plateforme d’identités Microsoft, consultez les articles suivants :

* Pour en savoir plus sur les concepts de base de l’authentification et de l’autorisation, consultez [Authentification ou autorisation](authentication-vs-authorization.md).
* Pour savoir comment les jetons d’accès, les jetons d’actualisation et les jetons d’ID sont utilisés dans l’authentification et l’autorisation, consultez [Jetons de sécurité](security-tokens.md).
* Pour en savoir plus sur le flux de connexion des applications web, de bureau et mobiles, consultez [Flux de connexion des applications](app-sign-in-flow.md).

Pour plus d’informations sur le modèle d’application, consultez les articles suivants :

* Pour plus d’informations sur les objets d’application et les principaux de service dans la plateforme d’identités Microsoft, consultez [Comment et pourquoi les applications sont ajoutées à Azure AD](active-directory-how-applications-are-added.md).
* Pour plus d’informations sur les applications à locataire unique et les applications mutualisées, consultez [Location dans Azure Active Directory](single-and-multi-tenant-apps.md).
* Pour plus d’informations sur la façon dont Azure AD fournit également Azure Active Directory B2C pour permettre aux organisations de connecter des utilisateurs, généralement des clients, à l’aide d’identités de réseaux sociaux comme un compte Google, consultez la [documentation d’Azure Active Directory B2C](../../active-directory-b2c/index.yml).