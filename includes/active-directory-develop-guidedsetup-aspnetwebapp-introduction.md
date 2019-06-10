---
title: Fichier Include
description: Fichier Include
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 46247d42837f8ac181d33216d2b93d28e2533c09
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66482361"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Ajouter la connexion avec Microsoft à une application ASP.NET

Ce guide explique comment implémenter la connexion avec Microsoft à l’aide d’une solution ASP.NET MVC avec une application basée sur un navigateur web traditionnel avec une norme OpenID Connect.

À la fin de ce guide, votre application sera en mesure d’accepter les connexions à l’aide de comptes personnels, comme les comptes outlook.com, live.com et autres. Cela comprend également les comptes professionnels et scolaires de toute entreprise ou organisation ayant intégré Azure Active Directory.

> Pour suivre ce guide, vous avez besoin de Visual Studio 2019.  Ni l’un, ni l’autre ne sont installés sur votre ordinateur ?  [Télécharger Visual Studio 2019 gratuitement](https://www.visualstudio.com/downloads/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Fonctionnement de l’exemple d’application de ce guide

![Fonctionnement de l’exemple d’application généré par ce tutoriel](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

L’exemple d’application que vous créez est basé sur un scénario dans lequel vous utilisez le navigateur pour accéder à un site web ASP.NET qui vous invite à vous authentifier via un bouton de connexion. Dans ce scénario, la majorité du travail pour afficher la page web se passe côté serveur.

## <a name="libraries"></a>Bibliothèques

Ce guide utilise les bibliothèques suivantes :

|Bibliothèque|Description|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Intergiciel qui permet à une application d’utiliser OpenIDConnect pour l’authentification.|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Intergiciel qui permet à une application de maintenir la session utilisateur à l’aide de cookies.|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Permet aux applications basées sur OWIN de s’exécuter sur IIS à l’aide du pipeline de requête ASP.NET.|
