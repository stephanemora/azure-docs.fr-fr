---
title: Créer un locataire Azure Active Directory | Microsoft Docs
description: Découvrez comment créer un locataire Azure AD à utiliser pour l'inscription et la création d'applications.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 1f4b24eb-ab4d-4baa-a717-2a0e5b8d27cd
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: quickstart
ms.date: 03/12/2020
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: 1d2140ca0064e2eb6b12726bcee7bf5362de8fbe
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "79408281"
---
# <a name="quickstart-set-up-a-tenant"></a>Démarrage rapide : Configurer un locataire

La plateforme d’identité Microsoft permet aux développeurs de créer des applications qui ciblent un large éventail d’environnements Microsoft 365 personnalisés et d’identités. Pour bien commencer avec la plateforme d’identité Microsoft, vous devrez accéder à un environnement, également appelé un locataire Azure AD, qui peut inscrire et gérer des applications, accéder aux données Microsoft 365 et déployer des accès conditionnels et des restrictions personnalisés au niveau du locataire.

Le locataire est la représentation d’une organisation. Il s’agit d’une instance Azure AD dédiée, reçue par une organisation ou un développeur d’applications lorsque l’organisation ou le développeur d’applications crée une relation avec Microsoft (l’inscription à Azure, Microsoft Intune ou Microsoft 365, par exemple).

Chaque locataire Azure AD est distinct et séparé des autres locataires Azure AD. Il dispose de sa propre représentation d’identités professionnelles et scolaires, d’identités des consommateurs (dans le cas de locataire Azure AD B2C) et d’inscriptions d’application. Dans votre locataire, une inscription d’application peut autoriser des authentifications à partir de comptes situés au sein de votre locataire uniquement ou bien à partir de tous les locataires.

## <a name="determining-environment-type"></a>Déterminer le type d’environnement

Vous pouvez créer deux types d’environnements. Pour décider celui dont vous avez besoin, vous devez déterminer le type d’utilisateurs authentifiés par votre application.

* Comptes professionnels et scolaires (comptes Azure AD) ou comptes Microsoft (par exemple, outlook.com et live.com)
* Comptes sociaux et locaux (Azure AD B2C)

Le guide de démarrage rapide comporte deux scénarios selon le type d’application que vous souhaitez générer. Si vous avez besoin d’aide pour cibler le type d’identité, jetez un œil à l’article relatif à [la plateforme d’identité Microsoft](about-microsoft-identity-platform.md).

## <a name="work-and-school-accounts-or-personal-microsoft-accounts"></a>Comptes professionnels et scolaires ou comptes Microsoft personnels

### <a name="use-an-existing-tenant"></a>Utiliser un locataire existant

De nombreux développeurs disposent déjà de locataires grâce à des services ou abonnements liés à des locataires Azure AD, comme des abonnements Microsoft 365 ou Azure.

1. Pour vérifier le locataire, connectez-vous au [Portail Azure](https://portal.azure.com) avec le compte que vous souhaitez utiliser pour gérer votre application.
1. Vérifiez l’angle supérieur droit. Si vous disposez d’un locataire, vous êtes automatiquement connecté à celui-ci, et vous pouvez voir son nom directement sous le nom de votre compte.
   * Dans l’angle supérieur droit du Portail Azure, survolez le nom de votre compte avec la souris pour voir votre nom, votre adresse e-mail, votre répertoire et votre ID de locataire (un GUID), ainsi que votre domaine.
   * Si votre compte est associé à plusieurs locataires, vous pouvez sélectionner le nom de votre compte pour ouvrir un menu dans lequel vous pouvez passer d’un locataire à l’autre. Chaque locataire a son propre ID.

> [!TIP]
> Deux options s’offrent à vous si vous avez besoin de trouver l’ID du locataire :
> * Survolez le nom de votre compte avec la souris pour obtenir le répertoire/ID du locataire.
> * Sélectionnez **Azure Active Directory > Propriétés > ID de répertoire** dans le Portail Azure.

Si vous ne disposez pas de locataire associé à votre compte, vous voyez un GUID sous le nom de votre compte et vous ne pouvez pas effectuer certaines actions comme l’inscription d’applications tant que vous n’avez pas suivi les étapes de la prochaine section.

### <a name="create-a-new-azure-ad-tenant"></a>Créer un nouveau locataire Azure AD

Si vous ne disposez pas déjà d’un locataire Azure AD ou si vous souhaitez en créer un autre à des fins de développement, consultez le [guide de démarrage rapide](../fundamentals/active-directory-access-create-new-tenant.md) ou suivez simplement l’[expérience de création d’annuaire](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory). Vous devez fournir les informations suivantes pour créer un locataire :

- **Nom de l’organisation**
- **Domaine initial** : il est ajouté à *.onmicrosoft.com. Vous pourrez personnaliser le domaine ultérieurement.
- **Pays ou région**

> [!NOTE]
> Lorsque vous nommez votre locataire, utilisez des caractères alphanumériques. Les caractères spéciaux ne sont pas autorisés. Le nom ne doit pas comporter plus de 256 caractères.

## <a name="social-and-local-accounts"></a>Comptes sociaux et locaux

Pour commencer à créer des applications pour connecter des comptes sociaux et locaux, vous devez créer un locataire Azure AD B2C. Pour commencer, suivez les instructions de [création d’un locataire Azure AD B2C](../../active-directory-b2c/tutorial-create-tenant.md).

## <a name="next-steps"></a>Étapes suivantes

* [Inscrivez une application](quickstart-register-app.md) et intégrez-la à la plateforme des identités Microsoft. 
* Découvrez les [principes de base de l’authentification](authentication-scenarios.md).
* Pour plus d’informations sur la relation entre les abonnements et un client Azure AD, consultez [Association des abonnements Azure avec Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
