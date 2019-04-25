---
title: Fichier Include
description: Fichier Include
services: active-directory-b2c
author: davidmu1
ms.service: active-directory-b2c
ms.topic: include
ms.date: 04/09/2018
ms.author: davidmu
ms.custom: include file
ms.openlocfilehash: adeaed3625ce42087d874e67cd6cb91d406e8fae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60455670"
---
[!INCLUDE [active-directory-b2c-portal-add-application](active-directory-b2c-portal-add-application.md)]

Pour inscrire votre application mobile ou native, utilisez les paramètres spécifiés dans la table.

![Exemples de paramètres d’inscription pour une nouvelle application mobile ou native](./media/active-directory-b2c-register-mobile-native-app/b2c-new-mobile-native-app-settings.png)

| Paramètre      | Exemple de valeur  | Description                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Name** | Application de Contoso B2C | Entrez un **Nom** pour l’application qui décrira votre application aux consommateurs. |
| **Client natif** | Oui | Sélectionnez **Oui** pour une application mobile ou native. |
| **Obtenir un URI de redirection personnalisé** | `com.onmicrosoft.contoso.appname://redirect/path` | Entrez un URI de redirection avec un schéma personnalisé. Prenez soin de choisir un [URI de redirection approprié](../articles/active-directory-b2c/active-directory-b2c-app-registration.md) et de ne pas y inclure de caractères spéciaux tels que des traits de soulignement. |

Cliquez sur **Créer** pour inscrire votre application.

Votre application qui vient d’être enregistrée s’affiche dans la liste des applications du client B2C. Dans la liste, sélectionnez votre application mobile ou native. Le volet de propriétés de l’application s’affiche.

![Propriétés de l’application](./media/active-directory-b2c-register-mobile-native-app/b2c-mobile-native-app-properties.png)

Prenez note de l’**ID client de l’application** global unique. Vous utilisez l’ID dans le code de votre application.

Si votre application native appelle une API web sécurisée par Azure AD B2C, procédez comme suit :
   1. Créez une clé secrète d’application en accédant au panneau **Clés** et en cliquant sur le bouton **Générer la clé**. Prenez note de la valeur **Clé d’application** . Vous utilisez la valeur en tant que secret d’application dans le code de votre application.
   2. Cliquez sur **Accès d’API**, cliquez sur **Ajouter**, puis sélectionnez votre API web et les étendues (autorisations).

> [!NOTE]
> Une **Clé secrète d’application** est une information d’identification de sécurité importante, qui doit être correctement sécurisée.
> 
