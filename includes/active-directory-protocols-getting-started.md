---
title: Vue d’ensemble du protocole Azure AD .NET | Documents Microsoft
description: Comment utiliser des messages HTTP pour autoriser l’accès aux applications web et API web dans votre client à l’aide d’Azure AD.
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/22/2019
ms.author: priyamo
ms.openlocfilehash: b6dd4cd55755ae2c92afd327ad72ffe6966b9a07
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177236"
---
## <a name="register-your-application-with-your-ad-tenant"></a>Inscrire votre application avec votre client AD
Tout d’abord, vous devez inscrire votre application avec votre client Azure Active Directory (Azure AD). Ceci vous fournira un ID d’application pour votre application et lui permettra de recevoir des jetons.

* Connectez-vous au [Portail Azure](https://portal.azure.com).
* Choisissez votre locataire Azure AD en cliquant sur votre compte dans le coin supérieur droit de la page, puis en cliquant sur la barre de navigation **Changer de répertoire** et en sélectionnant ensuite le locataire souhaité. 
  * Ignorez cette étape si vous n’avez qu’un locataire Azure AD sous votre compte ou si vous avez en déjà sélectionné un.
* Dans le volet de navigation gauche, cliquez sur **Azure Active Directory**.
* Cliquez sur **Inscriptions des applications**, puis sur **Nouvelle inscription**.
* Suivez les invites et créez une application. Pour ce didacticiel, il peut s’agir d’une application web ou d’une application client publique (mobile et bureau). Cependant, si vous souhaitez obtenir des exemples spécifiques pour les applications web ou les applications clientes publiques, consultez nos rubriques de [démarrage rapide](../articles/active-directory/develop/v1-overview.md).
  * Le **nom** de l’application donne une description de votre application aux utilisateurs finaux.
  * Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un annuaire organisationnel et comptes personnels Microsoft**.
  * Fournissez **l’URI de redirection**. Pour les applications web, il s’agit de l’URL de base de votre application, à laquelle les utilisateurs peuvent se connecter.  Par exemple : `http://localhost:12345`. Pour une application cliente publique (mobile et bureau), Azure AD l’utilise pour renvoyer les réponses de jeton. Entrez une valeur spécifique à votre application.  Par exemple : `http://MyFirstAADApp`.
    <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
* Une fois l’inscription terminée, Azure AD attribue un identificateur client unique à votre application (**l’ID d’application**). Copiez cette valeur à partir de la page de l’application, car vous en aurez besoin dans les sections suivantes.
* Pour trouver votre application dans le portail Azure, cliquez sur **Inscriptions des applications**, puis sur **Afficher toutes les applications**.
