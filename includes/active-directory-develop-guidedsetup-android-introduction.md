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
ms.openlocfilehash: aa21168f1e19ccddc190379b30e1b612d96b56d5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32202666"
---
# <a name="sign-in-users-and-all-the-microsoft-graph-api-from-an-android-app"></a>Connecter des utilisateurs et appeler l’API Microsoft Graph à partir d’une application Android

Ce guide explique comment une application Android native peut obtenir un jeton d’accès et appeler l’API Microsoft Graph ou d’autres API qui nécessitent des jetons d’accès provenant d’un point de terminaison Azure Active Directory v2.

À la fin de ce guide, votre application pourra accepter des connexions de comptes personnels (y compris outlook.com, live.com et autres), ainsi que des comptes professionnels et scolaires de n’importe quelle société ou organisation utilisant Azure Active Directory. L’application appelle ensuite une API qui est protégée par le point de terminaison Azure Active Directory v2.  

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Fonctionnement de l’exemple d’application de ce guide
![Fonctionnement de cet exemple](media/active-directory-develop-guidedsetup-android-intro/android-intro.png)

L’exemple d’application que vous avez créé avec ce guide est basé sur un scénario dans lequel une application Android est utilisée pour interroger une API web qui accepte des jetons provenant du point de terminaison Azure Active Directory v2 (dans ce cas l’API Microsoft Graph). Pour ce scénario, votre application ajoute le jeton obtenu aux requêtes HTTP via l’en-tête d’autorisation. La bibliothèque d’authentification Microsoft (MSAL) gère l’acquisition et le renouvellement de jetons pour vous.

## <a name="prerequisites"></a>Prérequis

* Cette installation guidée se concentre sur Android Studio, mais n’importe quel autre environnement de développement d’application Android peut être utilisé. 
* Le Kit de développement logiciel (SDK) Android 21 ou version ultérieure est requis (Kit de développement logiciel (SDK) 25 recommandé).
* Google Chrome ou un navigateur web utilisant les onglets personnalisés est requis pour cette version de MSAL pour Android.

> [!NOTE]
> Google Chrome n’est pas inclus avec l’émulateur Visual Studio pour Android. Nous vous recommandons de tester ce code sur un émulateur avec l’API 25 ou sur une image avec l’API 21 ou version ultérieure disposant de Google Chrome.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Gestion de l’acquisition de jetons pour accéder à des API web protégées

Une fois l’utilisateur authentifié, l’exemple d’application reçoit un accès au jeton qui peut être utilisé pour interroger l’API Microsoft Graph ou une API web sécurisée par Azure Active Directory v2.

Avec les API comme Microsoft Graph, vous avez besoin d’un jeton d’accès pour autoriser l’accès à des ressources spécifiques. Par exemple, un accès au jeton est nécessaire pour lire le profil d’un utilisateur, accéder au calendrier d’un utilisateur ou envoyer un e-mail. Votre application peut demander un jeton d’accès à l’aide de MSAL pour accéder à ces ressources en spécifiant les étendues d’API. Ce jeton d’accès est ensuite ajouté à l’en-tête d’autorisation HTTP pour chaque appel effectué sur la ressource protégée. 

MSAL gère la mise en cache et l’actualisation des jetons d’accès pour vous, ce qui évite à votre application d’avoir à le faire.

## <a name="libraries"></a>Bibliothèques

Ce guide utilise les bibliothèques suivantes :

|Bibliothèque|Description|
|---|---|
|[com.microsoft.identity.client](http://javadoc.io/doc/com.microsoft.identity.client/msal)|Bibliothèque d’authentification Microsoft (MSAL)|
