---
title: Fichier Include
description: Fichier Include
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2019
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: e054456662af3ef9df0760af163ec5d09df18b21
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502931"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>Connecter des utilisateurs et appeler Microsoft Graph à partir d’une application Android

Dans ce didacticiel, vous allez apprendre à créer une application Android et à l’intégrer à la plateforme d’identité Microsoft. Plus précisément, cette application doit connecter un utilisateur, obtenir un jeton d’accès pour appeler l’API Microsoft Graph et adresser une requête de base à l’API Microsoft Graph.  

À la fin de ce guide, votre application acceptera les connexions de comptes Microsoft personnels (y compris outlook.com, live.com et d’autres) et de comptes professionnels ou scolaires de n’importe quelle entreprise ou organisation utilisant Azure Active Directory.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Fonctionnement de l’exemple d’application de ce guide

![Fonctionnement de l’exemple d’application généré par ce tutoriel](media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

L’application utilisée dans cet exemple est destinée à connecter des utilisateurs et à obtenir des données au nom de ces derniers.  Ces données sont accessibles par le biais d’une API distante (l’API Microsoft Graph dans ce cas précis) qui nécessite une autorisation et est également protégée par la plateforme d’identité Microsoft.

Plus précisément :

* Votre application lance une page web pour connecter l’utilisateur.
* Votre application émet un jeton d’accès pour l’API Microsoft Graph.
* Le jeton d’accès est inclus dans la requête HTTP adressée à l’API web.
* La réponse Microsoft Graph est traitée.

Cet exemple utilise la bibliothèque d’authentification Microsoft pour Android (MSAL) pour coordonner et faciliter l’authentification. MSAL renouvelle automatiquement les jetons, assure l’authentification unique entre les autres applications sur l’appareil, facilite la gestion des comptes et prend en charge la plupart des cas d’accès conditionnel.

## <a name="prerequisites"></a>Prérequis

* Ce guide de configuration utilise Android Studio 3.0.
* L’utilisation d’Android 21 ou d’une version ultérieure est requise (version 25 ou ultérieure recommandée).
* Cette version de MSAL pour Android requiert Google Chrome ou un navigateur web utilisant les onglets personnalisés.

## <a name="library"></a>Bibliothèque

Ce guide utilise la bibliothèque d’authentification suivante :

|Bibliothèque|Description|
|---|---|
|[com.microsoft.identity.client](http://javadoc.io/doc/com.microsoft.identity.client/msal)|Bibliothèque d’authentification Microsoft (MSAL)|
