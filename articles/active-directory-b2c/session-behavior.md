---
title: Configurer le comportement de session – Azure Active Directory B2C | Microsoft Docs
description: Configurez le comportement de session dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c47b0249824a3683b8f24cc01cc5eea5ccf32585
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87482666"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Configurer le comportement de session dans Azure Active Directory B2C

La gestion de [session d’authentification unique (SSO)](session-overview.md) dans Azure Active Directory B2C (Azure AD B2C) permet à un administrateur de contrôler l’interaction avec un utilisateur une fois celui-ci authentifié. Par exemple, l’administrateur peut contrôler si la sélection des fournisseurs d’identité s’affiche ou si des détails de compte doivent être entrés à nouveau. Cet article décrit comment configurer les paramètres d’authentification unique pour Azure AD B2C.

## <a name="session-behavior-properties"></a>Propriétés de comportement de session

Vous pouvez utiliser les propriétés suivantes pour gérer les sessions d’application web :

- **Durée de vie de session d’application web (minutes)** : la durée de vie du cookie de session Azure AD B2C stocké dans le navigateur de l’utilisateur après une authentification réussie.
    - Par défaut : 1 440 minutes.
    - Valeur minimale (inclusive) : 15 minutes.
    - Valeur maximale (inclusive) : 1 440 minutes.
- **Délai d’expiration de session d’application web** : le [type d’expiration de session](session-overview.md#session-expiry-type), *Rolling* (Propagé) ou *Absolute* (Absolu). 
- **Configuration de l’authentification unique** : l’[étendue de session](session-overview.md#session-scope) du comportement de l’authentification unique (SSO) sur plusieurs applications et flux d’utilisateurs dans votre client Azure AD B2C.


## <a name="configure-the-properties"></a>Configurer les propriétés

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en sélectionnant le filtre **Annuaire et abonnement** dans le menu supérieur et en choisissant l’annuaire qui contient votre locataire Azure AD B2C.
3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
4. Sélectionnez **Flux d’utilisateurs**.
5. Ouvrez le flux utilisateur que vous avez créé précédemment.
6. Sélectionner **Propriétés**.
7. Configurez **Durée de vie de la session de l’application web (minutes)** , **Délai d’expiration de la session de l’application web** , **Configuration de l’authentification unique** et **Exiger un jeton d’ID dans les demandes de déconnexion**, selon les besoins.

    ![Paramètres de propriété du comportement de la session sur le Portail Azure](./media/session-behavior/session-behavior.png)

8. Cliquez sur **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur une [session Azure AD B2C](session-overview.md).