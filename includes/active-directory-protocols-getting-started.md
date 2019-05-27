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
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121528"
---
## <a name="register-your-application-with-your-ad-tenant"></a>Inscrire votre application avec votre client AD
Tout d’abord, vous devez inscrire votre application avec votre client Azure Active Directory (Azure AD). Ceci vous fournira un ID d’application pour votre application et lui permettra de recevoir des jetons.

* Connectez-vous au [Portail Azure](https://portal.azure.com).
* Choisissez votre locataire Azure AD en cliquant sur votre compte dans le coin supérieur droit de la page, puis en cliquant sur la barre de navigation **Changer de répertoire** et en sélectionnant ensuite le locataire souhaité. 
  * Ignorez cette étape si vous n’avez qu’un locataire Azure AD sous votre compte ou si vous avez en déjà sélectionné un.
* Dans le volet de navigation gauche, cliquez sur **Azure Active Directory**.
* Cliquez sur **inscriptions** , puis cliquez sur **nouvelle inscription**.
* Suivez les invites et créez une application. Il n’a pas d’importance s’il s’agit d’une application web ou une application de client public (mobile et bureau) pour ce didacticiel, mais si vous souhaitez que des exemples spécifiques pour les applications web ou applications de client public, consultez notre [Démarrages rapides](../articles/active-directory/develop/v1-overview.md).
  * Le **nom** de l’application donne une description de votre application aux utilisateurs finaux.
  * Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un annuaire organisationnel et comptes personnels Microsoft**.
  * Fournir le **l’URI de redirection**. Pour les Applications Web, voici l’URL de base de votre application où les utilisateurs peuvent se connecter.  Par exemple : `http://localhost:12345`. Pour un client public (mobile et bureau), Azure AD utilise pour retourner les réponses de jeton. Entrez une valeur spécifique à votre application.  Par exemple : `http://MyFirstAADApp`.
    <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
* Une fois l’inscription terminée, Azure AD affecte à votre application un identificateur client unique (le **ID d’Application**). Copiez cette valeur à partir de la page de l’application, car vous en aurez besoin dans les sections suivantes.
* Pour trouver votre application dans le portail Azure, cliquez sur **Inscriptions des applications**, puis sur **Afficher toutes les applications**.
