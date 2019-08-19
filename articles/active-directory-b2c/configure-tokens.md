---
title: Configurer les jetons – Azure Active Directory B2C | Microsoft Docs
description: Découvrez comment configurer les paramètres de compatibilité des jetons et leur durée de vie dans Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 2ed1426bea920d1ef30fb28ff4a975d1268c12ff
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846926"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Configurer les jetons dans Azure Active Directory B2C

Dans cet article, vous allez apprendre à configurer la [durée de vie et la compatibilité d’un jeton](active-directory-b2c-reference-tokens.md) dans Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Prérequis

[Créez un flux d'utilisateurs](tutorial-create-user-flows.md) pour permettre aux utilisateurs de s'inscrire et de se connecter à votre application.

## <a name="configure-token-lifetime"></a>Configurer la durée de vie des jetons

Vous pouvez configurer la durée de vie des jetons sur n’importe quel flux utilisateur.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Vérifiez que vous utilisez bien l’annuaire qui contient votre locataire Azure AD B2C. Choisissez **Filtre de répertoire et d’abonnement** dans le menu supérieur et choisissez l’annuaire qui contient votre locataire Azure AD B2C.
3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
4. Sélectionnez **Flux utilisateur (stratégies)** .
5. Ouvrez le flux utilisateur que vous avez créé précédemment.
6. Sélectionner **Propriétés**.
7. Sous **Durée de vie du jeton**, ajustez les propriétés suivantes en fonction des besoins de votre application :

    ![Paramètres de propriété de la durée de vie du jeton sur le Portail Azure](./media/configure-tokens/token-lifetime.png)

8. Cliquez sur **Enregistrer**.

## <a name="configure-token-compatibility"></a>Configurer la compatibilité des jetons

1. Sélectionnez **Flux utilisateur (stratégies)** .
2. Ouvrez le flux utilisateur que vous avez créé précédemment.
3. Sélectionner **Propriétés**.
4. Sous **Paramètres de compatibilité de jeton**, ajustez les propriétés suivantes en fonction des besoins de votre application :

    ![Paramètres de propriété de la compatibilité du jeton sur le Portail Azure](./media/configure-tokens/token-compatibility.png)

5. Cliquez sur **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus d’informations sur [l’utilisation des jetons d’accès](active-directory-b2c-access-tokens.md).



