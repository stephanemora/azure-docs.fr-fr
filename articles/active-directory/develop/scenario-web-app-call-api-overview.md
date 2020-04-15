---
title: Générer une application web appelant des API web – Plateforme d’identités Microsoft | Azure
description: Apprenez à générer une application web qui appelle des API web (vue d’ensemble)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5af9e34baf6115e801fbfe35e6e3895e48b360e7
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881721"
---
# <a name="scenario-a-web-app-that-calls-web-apis"></a>Scénario : Application web qui appelle des API web

Découvrez comment générer une application web qui connecte des utilisateurs à la plateforme d’identités Microsoft et qui appelle des API web au nom de l’utilisateur connecté.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

Ce scénario suppose que vous avez déjà parcouru le scénario suivant :

> [!div class="nextstepaction"]
> [Application web qui connecte les utilisateurs](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Vue d’ensemble

Vous ajoutez l’authentification à votre application web pour qu’elle puisse connecter des utilisateurs et appeler une API web au nom de l’utilisateur connecté.

![Application web qui appelle des API web](./media/scenario-webapp/web-app.svg)

Les applications web qui appellent des API web sont des applications clientes confidentielles.
C’est pourquoi elles inscrivent un secret (un mot de passe ou un certificat d’application) auprès d’Azure Active Directory (Azure AD). Ce secret est transmis lors de l’appel à Azure AD pour l’obtention d’un jeton.

## <a name="specifics"></a>Spécificités

> [!NOTE]
> L’ajout de la connexion à une application web vise à protéger l’application web elle-même. Cette protection est obtenue à l’aide de bibliothèques *intergicielles*, et non de la bibliothèque d’authentification Microsoft (MSAL). Le scénario précédent, [Application web qui connecte les utilisateurs](scenario-web-app-sign-user-overview.md), a traité ce sujet.
>
> Ce scénario explique comment appeler des API web à partir d’une application web. Vous devez obtenir des jetons d’accès pour ces API web. Vous obtenez ces jetons à l’aide des bibliothèques MSAL.

Le développement de ce scénario implique ces tâches spécifiques :

- Pendant l’[inscription de l’application](scenario-web-app-call-api-app-registration.md), vous devez fournir un URI de réponse, un secret ou un certificat à partager avec Azure AD. Si vous déployez votre application dans plusieurs emplacements, vous devrez fournir ces informations pour chaque emplacement.
- La [configuration de l’application](scenario-web-app-call-api-app-configuration.md) doit fournir les informations d’identification du client qui ont été partagées avec Azure AD lors de l’inscription de l’application.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Application web qui appelle des API web : inscription d’application](scenario-web-app-call-api-app-registration.md)
