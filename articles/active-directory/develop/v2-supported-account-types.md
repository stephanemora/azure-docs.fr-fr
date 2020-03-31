---
title: Types de comptes pris en charge - Plateforme d’identités Microsoft | Azure
description: Documentation conceptuelle sur les publics et les types de compte pris en charge dans les applications
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: de90c81f56b6017b2d53ecbfb2c400a4c9f05d81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79230485"
---
# <a name="supported-account-types"></a>Types de comptes pris en charge

Cet article présente les types de comptes (parfois appelés publics) pris en charge dans les applications.

<!-- This section can be in an include for many of the scenarios (SPA, Web App signing-in users, protecting a Web API, Desktop (depending on the flows), Mobile -->

## <a name="supported-accounts-types-in-microsoft-identity-platform-applications"></a>Types de comptes pris en charge dans les applications de la plateforme d’identité Microsoft

Dans le cloud public Microsoft Azure, la plupart des types d’applications peuvent connecter des utilisateurs avec une audience :

- Si vous écrivez une application métier (Line of Business, LOB), vous pouvez connecter les utilisateurs à votre propre organisation. Une telle application est parfois nommée **locataire unique**.
- Si vous êtes un éditeur de logiciels, vous pouvez écrire une application qui connecte les utilisateurs :

  - À n’importe quelle organisation. Une telle application est nommée application web **mutualisée**. Vous noterez parfois que ce type d’application connecte les utilisateurs à leurs comptes professionnels ou scolaires.
  - Avec leur compte Microsoft professionnel, scolaire ou personnel.
  - Avec un compte Microsoft personnel uniquement.
    > [!NOTE]
    > Actuellement, la plateforme d’identité Microsoft prend en charge les comptes Microsoft personnels uniquement par inscription d’une application pour des **comptes Microsoft professionnels ou scolaires**, puis limite la connexion dans le code de l’application en spécifiant une autorité Azure AD lors de la création de l’application, par exemple `https://login.microsoftonline.com/consumers`.

- Si vous écrivez une entreprise B2C (Business to Customer), vous pouvez également connecter les utilisateurs avec leurs identités sociales en utilisant Azure AD B2C.

## <a name="certain-authentication-flows-dont-support-all-the-account-types"></a>Certains flux d’authentification ne prennent pas en charge tous les types de compte

Certains types de compte ne peuvent pas être utilisés avec certains flux d’authentification. Par exemple, dans les applications de bureau, les applications UWP ou démon :

- Les applications de démon peuvent uniquement être utilisées avec des organisations Azure Active Directory. Il est inutile d’essayer d’utiliser des applications de démon pour gérer des comptes personnels Microsoft (l’autorisation de l’administrateur ne sera jamais accordée).  
- Vous pouvez uniquement utiliser le flux de l’authentification Windows intégrée avec des comptes professionnels ou scolaires (dans votre organisation ou une autre). En effet, l’authentification Windows intégrée fonctionne avec des comptes de domaine et nécessite que les ordinateurs soient joints à un domaine ou joints à Azure AD. Ce flux est inutile pour les comptes Microsoft personnels.
- L’[octroi des informations d'identification liées au mot de passe du propriétaire de ressource](./v2-oauth-ropc.md) (nom d’utilisateur/mot de passe) ne peut pas être utilisé avec des comptes Microsoft personnels. En effet, les comptes Microsoft personnels nécessitent que l’utilisateur donne son accord pour l’accès aux ressources personnelles à chaque session de connexion. Pour cette raison, ce comportement n’est pas compatible avec les flux non interactifs.
- Le flux de code d’appareil ne fonctionne pas encore avec les comptes Microsoft personnels.

## <a name="supported-account-types-in-national-clouds"></a>Types de compte pris en charge dans les clouds nationaux

 Les applications peuvent également connecter les utilisateurs à des [clouds nationaux](authentication-national-cloud.md). Toutefois, les comptes personnels Microsoft ne sont pas pris en charge dans ces clouds (en raison de leur définition). C’est pourquoi les types de comptes pris en charge sont réduits, pour ces clouds, à votre organisation (un seul locataire) ou à toutes les organisations (applications mutualisées).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [location dans Azure Active Directory](./single-and-multi-tenant-apps.md)
- En savoir plus sur les [clouds nationaux](./authentication-national-cloud.md)
