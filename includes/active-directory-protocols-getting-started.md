---
title: Vue d’ensemble du protocole Azure AD .NET | Documents Microsoft
description: Comment utiliser des messages HTTP pour autoriser l’accès aux applications web et API web dans votre client à l’aide d’Azure AD.
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/18/2018
ms.author: priyamo
ms.openlocfilehash: 44db736d5312b6850bb4fd8f47af5cd6b22535a7
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49960180"
---
## <a name="register-your-application-with-your-ad-tenant"></a>Inscrire votre application avec votre client AD
Tout d’abord, vous devez inscrire votre application avec votre client Azure Active Directory (Azure AD). Ceci vous fournira un ID d’application pour votre application et lui permettra de recevoir des jetons.

* Connectez-vous au [Portail Azure](https://portal.azure.com).
* Choisissez votre locataire Azure AD en cliquant sur votre compte dans le coin supérieur droit de la page, puis en cliquant sur la barre de navigation **Changer de répertoire** et en sélectionnant ensuite le locataire souhaité. 
  * Ignorez cette étape si vous n’avez qu’un locataire Azure AD sous votre compte ou si vous avez en déjà sélectionné un.
* Dans le volet de navigation gauche, cliquez sur **Azure Active Directory**.
* Cliquez sur **Inscriptions des applications**, puis sur **Nouvelle inscription d’application**.
* Suivez les invites et créez une application. Pour ce didacticiel, il peut s’agir d’une application web ou d’une application native. Cependant, si vous souhaitez obtenir des exemples spécifiques pour les applications web ou les applications natives, consultez nos rubriques de [démarrage rapide](../articles/active-directory/develop/v1-overview.md).
  * Pour les applications web, indiquez l’**URL de connexion**, c’est-à-dire l’URL de base de votre application, à laquelle les utilisateurs peuvent se connecter, par exemple `http://localhost:12345`.
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * Pour les applications natives, indiquez un **URI de redirection**, qui sera utilisé par Azure AD pour retourner les réponses de jeton. Entrez une valeur spécifique à votre application, par exemple, `http://MyFirstAADApp`
* Une fois l’inscription terminée, Azure AD affecte un identificateur client unique à votre application, **l’ID d’application**. Copiez cette valeur à partir de la page de l’application, car vous en aurez besoin dans les sections suivantes.
* Pour trouver votre application dans le portail Azure, cliquez sur **Inscriptions des applications**, puis sur **Afficher toutes les applications**.
