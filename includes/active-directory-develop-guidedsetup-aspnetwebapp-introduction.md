---
title: Fichier Include
description: Fichier Include
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 9dd9185816670da1fdb59ef42f1f3ca37784eb35
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2018
ms.locfileid: "36204935"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Ajouter la connexion avec Microsoft à une application ASP.NET

Ce guide explique comment implémenter la connexion avec Microsoft à l’aide d’une solution ASP.NET MVC avec une application basée sur un navigateur web traditionnel avec une norme OpenID Connect. 

À la fin de ce guide, votre application pourra accepter des connexions de comptes personnels (y compris outlook.com, live.com et autres), ainsi que des comptes professionnels et scolaires de n’importe quelle société ou organisation ayant intégré Azure Active Directory. 

> Ce guide requiert Visual Studio 2015 Update 3 ou Visual Studio 2017.  Ni l’un, ni l’autre ne sont installés sur votre ordinateur ?  [Téléchargez gratuitement Visual Studio 2017](https://www.visualstudio.com/downloads/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Fonctionnement de l’exemple d’application de ce guide

![Fonctionnement de ce guide](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.png)

L’exemple d’application créé dans le cadre de ce guide est basé sur un scénario dans lequel un utilisateur se sert du navigateur pour accéder à un site web ASP.NET qui l’invite à s’authentifier via un bouton de connexion. Dans ce scénario, la majorité du travail pour afficher la page web se passe côté serveur.

## <a name="libraries"></a>Bibliothèques

Ce guide utilise les bibliothèques suivantes :

|Bibliothèque|Description|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Intergiciel qui permet à une application d’utiliser OpenIDConnect pour l’authentification.|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Intergiciel qui permet à une application de maintenir la session utilisateur à l’aide de cookies.|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Permet aux applications basées sur OWIN de s’exécuter sur IIS à l’aide du pipeline de requête ASP.NET.|

