---
title: Application à page unique (inscription de l’application) - plateforme d’identité Microsoft
description: Découvrez comment créer une application à page unique (inscription de l’application)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1faf4669dca2aaaf3f873e66f859473ccd99f10
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074829"
---
# <a name="single-page-application---app-registration"></a>Inscription d’application - application à page unique

Cette page explique les spécificités de l’inscription d’application pour une application à page unique (SPA).

Suivez les étapes pour [inscrire une nouvelle application avec la plateforme d’identité Microsoft](quickstart-register-app.md), puis sélectionnez les comptes pris en charge pour votre application. Le scénario d’application à page unique peut prendre en charge l’authentification avec des comptes dans votre organisation ou n’importe quelle organisation et les comptes Microsoft personnels.

Ensuite, Découvrez les aspects spécifiques de l’inscription d’application qui s’appliquent aux applications à page unique.

## <a name="register-a-redirect-uri"></a>Inscrire un URI de redirection

Le flux implicite envoie les jetons dans une redirection vers l’application à page unique en cours d’exécution dans un navigateur web. Par conséquent, il est une exigence importante pour inscrire un URI où votre application peut recevoir les jetons de redirection. Vérifiez que l’URI correspondant exactement à l’URI de votre application de redirection.

Dans le [portail Azure](https://go.microsoft.com/fwlink/?linkid=2083908), accédez à votre application inscrite, sur le **authentification** page de l’application, sélectionnez le **Web** plateforme et entrez la valeur de la URI de redirection pour votre application dans le **URI de redirection** champ.

## <a name="enable-the-implicit-flow"></a>Activer le flux implicit

Sur le même **authentification** page sous **paramètres avancés**, vous devez également activer le **octroi implicite**. Si votre application s’exécute uniquement des utilisateurs et obtenir des jetons d’ID de connexion, il suffit d’activer **les jetons d’ID** case à cocher.

Si votre application doit également obtenir des jetons d’accès pour appeler des API, veillez à activer la **de jetons d’accès** ainsi case à cocher. Pour plus d’informations, consultez [les jetons d’ID](./id-tokens.md) et [de jetons d’accès](./access-tokens.md).

## <a name="api-permissions"></a>Autorisations des API

Applications à page unique peuvent appeler des API pour le compte de l’utilisateur connecté. Ils doivent demander des autorisations déléguées. Pour plus d’informations, consultez [ajouter des autorisations pour accéder aux API web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configuration de code de l’application](scenario-spa-app-configuration.md)
