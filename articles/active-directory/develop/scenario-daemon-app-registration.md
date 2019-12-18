---
title: Inscrire une application démon appelant des API web - Plateforme d’identités Microsoft | Azure
description: Apprenez à générer une application démon qui appelle des API web - inscription d'application
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
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcdad9b1ea56a41613c5d8d815d1418783fea4ce
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965583"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>Application démon qui appelle des API web - inscription d'application

Voici ce que vous devez savoir lors de l'inscription d'une application démon.

## <a name="supported-account-types"></a>Types de comptes pris en charge

Dans la mesure où les applications démon ne présentent d’intérêt que dans des locataires Azure AD, lorsque vous créez l’application, vous devez choisir :

- **Comptes dans cet annuaire organisationnel uniquement** : ce choix est le plus fréquent car les applications démon sont généralement créées par des développeurs métier ;
- ou **Comptes dans un annuaire organisationnel** : ce choix s'applique aux éditeurs de logiciels indépendants qui fournissent un utilitaire à leurs clients. Les admins de locataire du client sont requis pour l'approuver.

## <a name="authentication---no-reply-uri-needed"></a>Authentification - Aucune adresse URI de réponse requise

Si votre application cliente confidentielle utilise **uniquement** le flux d’informations d’identification du client, l’URI de réponse n’a pas besoin d’être enregistré. Elle n'est pas non plus nécessaire pour la configuration/construction de l'application. Le flux d'informations d'identification du client ne l'utilise pas.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>Autorisations d'API - permissions d'application et consentement administrateur

Une application démon peut uniquement demander des permissions d'application (et non des permissions déléguées) aux APIs. Sur la page **Autorisation d'API** du processus d'inscription d'application, après avoir sélectionné **Ajouter une autorisation** et choisi la famille d'API, choisissez **Permissions d'application**, puis sélectionnez vos autorisations.

![Permissions d'application et consentement administrateur](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

> [!NOTE]
> L’API web que vous souhaitez appeler doit définir des **autorisations d’application (rôles d’application)** , et non des autorisations déléguées. Pour plus d’informations sur la façon d’exposer une telle API, voir [API web protégée : Inscription d’application – Si votre API web est appelée par une application démon](scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app)

Les applications démon ont besoin de l'accord préalable de l'administrateur du locataire pour appeler l'API web. Ce consentement est fourni sur la même page **Autorisation d'API** par un administrateur de locataire qui sélectionne **Accorder le consentement administrateur à *notre organisation*** .

Si vous êtes un éditeur de logiciels indépendant qui génère une application mutualisée, n'hésitez pas à consulter le paragraphe [Déploiement - cas d'applications démon mutualisées](scenario-daemon-production.md#deployment---case-of-multi-tenant-daemon-apps).

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Application démon - configuration du code de l'application](./scenario-daemon-app-configuration.md)
