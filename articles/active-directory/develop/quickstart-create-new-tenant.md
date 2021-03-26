---
title: 'Démarrage rapide : Créer un locataire Azure Active Directory'
titleSuffix: Microsoft identity platform
description: Dans ce guide de démarrage rapide, vous allez apprendre à créer un locataire Azure Active Directory à utiliser dans le développement d’applications qui utilisent la plateforme d’identités Microsoft pour l’authentification et l’autorisation.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: quickstart
ms.date: 02/15/2021
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: cb4995c0c71d0bcebb3cdd8ec471a92b7820cb9d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100535159"
---
# <a name="quickstart-set-up-a-tenant"></a>Démarrage rapide : Configurer un locataire

Si vous souhaitez créer des applications qui utilisent la plateforme d’identités Microsoft pour la gestion des identités et des accès, vous devez accéder à un *locataire* Azure Active Directory (Azure AD). C’est dans le locataire Azure AD que vous inscrivez et gérez vos applications, configurez leur accès aux données dans Microsoft 365 et d’autres API web, et activez des fonctionnalités telles que l’accès conditionnel.

Un locataire représente une organisation. Il s’agit d’une instance dédiée d’Azure AD qu’une organisation ou un développeur d’applications reçoit au début d’une relation avec Microsoft. Cette relation peut commencer par l’inscription à Azure, Microsoft Intune ou Microsoft 365, par exemple.

Chaque locataire Azure AD est distinct et indépendant des autres locataires Azure AD. Il dispose de sa propre représentation d’identités professionnelles et scolaires, d’identités de consommateurs (dans le cas d’un locataire Azure AD B2C) et d’inscriptions d’applications. Dans votre locataire, une inscription d’application peut autoriser des authentifications uniquement à partir de comptes situés dans votre locataire ou dans tous les locataires.

## <a name="prerequisites"></a>Prérequis

Un compte Azure disposant d’un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="determining-the-environment-type"></a>Déterminer le type d’environnement

Vous pouvez créer deux types d’environnements. L’environnement dépend uniquement des types d’utilisateurs authentifiés par votre application. 

Ce guide de démarrage rapide développe deux scénarios pour le type d’application que vous souhaitez créer :

* Comptes professionnels et scolaires (Azure AD) ou comptes Microsoft (par exemple, Outlook.com et Live.com)
* Comptes sociaux et locaux (Azure AD B2C)

## <a name="work-and-school-accounts-or-personal-microsoft-accounts"></a>Comptes professionnels et scolaires ou comptes Microsoft personnels

Si vous voulez créer un environnement pour les comptes professionnels et scolaires, ou les comptes Microsoft personnels, vous pouvez utiliser un locataire Azure AD existant ou en créer un.
### <a name="use-an-existing-azure-ad-tenant"></a>Utiliser un locataire Azure AD existant

Nombreux sont les développeurs qui disposent déjà de locataires par le biais de services ou d’abonnements liés à des locataires Azure AD, comme les abonnements Microsoft 365 ou Azure.

Pour vérifier l’existence du locataire :

1. Connectez-vous au <a href="https://portal.azure.com/" target="_blank">portail Azure</a>. Utilisez le compte dont vous vous servirez pour gérer votre application.
1. Regardez dans le coin supérieur droit. Si vous disposez d’un locataire, vous êtes automatiquement connecté. Le nom du locataire s’affiche directement sous le nom de votre compte.
   * Pointez sur le nom de votre compte pour voir votre nom, votre adresse e-mail, votre ID d’annuaire ou de locataire (un GUID) et votre domaine.
   * Si votre compte est associé à plusieurs locataires, vous pouvez sélectionner le nom de votre compte pour ouvrir un menu dans lequel vous pouvez passer d’un locataire à l’autre. Chaque locataire a son propre ID.

> [!TIP]
> Pour trouver l’ID du locataire, vous avez le choix entre :
> * Survoler le nom de votre compte avec la souris pour obtenir l’ID d’annuaire ou de locataire.
> * Recherchez et sélectionnez **Azure Active Directory** > **Propriétés** > **ID de locataire** dans le portail Azure.

Si aucun locataire n’est associé à votre compte, un GUID s’affiche sous le nom de votre compte. Vous ne pourrez pas effectuer des actions telles que l’inscription d’applications tant que vous n’aurez pas créé un locataire Azure AD.

### <a name="create-a-new-azure-ad-tenant"></a>Créer un nouveau locataire Azure AD

Si vous ne disposez pas déjà d’un locataire Azure AD, ou si vous souhaitez en créer un autre à des fins de développement, consultez [Créer un locataire dans Azure AD](../fundamentals/active-directory-access-create-new-tenant.md). Sinon, utilisez [l’expérience de création d’un annuaire](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) dans le portail Azure. 

Vous devez fournir les informations suivantes pour créer un locataire :

- **Nom de l’organisation**
- **Domaine initial** : ce domaine fait partie de *.onmicrosoft.com. Vous pourrez personnaliser le domaine ultérieurement.
- **Pays ou région**

> [!NOTE]
> Lorsque vous nommez votre locataire, utilisez des caractères alphanumériques. Les caractères spéciaux ne sont pas autorisés. Le nom ne doit pas comporter plus de 256 caractères.

## <a name="social-and-local-accounts"></a>Comptes sociaux et locaux

Pour commencer à créer des applications qui se connectent à des comptes sociaux et locaux, créez un locataire Azure AD B2C. Pour commencer, consultez [Créer un locataire Azure AD B2C](../../active-directory-b2c/tutorial-create-tenant.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Inscrivez une application](quickstart-register-app.md) à intégrer à la plateforme d’identités Microsoft.
