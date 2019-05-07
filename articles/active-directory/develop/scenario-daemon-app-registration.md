---
title: Démon application appeler des API web (inscription de l’application) - plateforme d’identité Microsoft
description: Découvrez comment créer une application démon qui appelle des API web - inscription de l’application
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79a355ab226e56a3dde1df5369deda5142d47848
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076239"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>Application démon par appels web API - inscription de l’application

Pour une application démon, voici ce que vous devez savoir lors de l’inscription de l’application.

## <a name="supported-account-types"></a>Types de comptes pris en charge

Étant donné que les applications de démon ne sont pertinentes dans le locataire Azure AD, lorsque vous créez l’application, vous devrez choisir :

- soit **comptes dans ce répertoire d’organisation uniquement**. Ce choix est le cas le plus courant, comme les applications de démon sont généralement écrites par les développeurs de line-of-business (LOB).
- ou **comptes dans n’importe quel répertoire organisation**. Vous allez faire ce choix si vous êtes un éditeur de logiciels en fournissant un outil de l’utilitaire à vos clients. Vous aurez besoin des administrateurs de locataires du client à l’approuver.

## <a name="authentication---no-reply-uri-needed"></a>Authentification - aucun URI de réponse ne nécessaire

Dans le cas où votre application cliente confidentielle utilise **uniquement** flux les informations d’identification du client, l’URL de réponse n’a pas besoin être inscrit. Elle n’a pas nécessaire pour la configuration d’application/construction. Le flux d’informations d’identification client ne l’utilise.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>Autorisations d’API - autorisations d’application et le consentement de l’administrateur

Une application démon peut demander uniquement les autorisations d’application aux API (autorisations déléguées pas). Dans le **autorisation API** page d’inscription de l’application, une fois que vous avez sélectionné **ajouter une autorisation** choisi la famille d’API, choisissez **autorisations d’Application**, puis sélectionnez vos autorisations

![Autorisations d’application et le consentement de l’administrateur](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

Applications de démon disposer un administrateur de locataires de consentement préalable à l’application appelle l’API web. Ce consentement est fourni dans le même **autorisation API** page, par un administrateur de locataire en sélectionnant **donner son consentement d’administrateur à *notre organisation***

Si vous êtes un ISV créant une application mutualisée, vous pouvez vérifier le [déploiement - cas d’applications de démon de l’architecture mutualisée](scenario-daemon-production.md#deployment---case-of-multi-tenant-daemon-apps) paragraphe.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Application démon - configuration de code d’application](./scenario-daemon-app-configuration.md)
