---
title: Configurer des jetons - Azure Active Directory B2C | Microsoft Docs
description: Découvrez comment configurer les paramètres de compatibilité et de durée de vie des jetons dans Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 2455b8c12ed042af3a06a158a4e5c60a0aee748b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60384010"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Configurer des jetons dans Azure Active Directory B2C

Dans cet article, vous allez apprendre à configurer le [durée de vie et de compatibilité d’un jeton](active-directory-b2c-reference-tokens.md) dans Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Conditions préalables

[Créez un flux d'utilisateurs](tutorial-create-user-flows.md) pour permettre aux utilisateurs de s'inscrire et de se connecter à votre application.

## <a name="configure-token-lifetime"></a>Configurer la durée de vie

Vous pouvez configurer la durée de vie de jeton sur n’importe quel flux utilisateur.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Vérifiez que vous utilisez le répertoire qui contient votre locataire Azure AD B2C. Sélectionnez le **filtre Directory et abonnement** dans le menu supérieur et sélectionnez le répertoire qui contient votre locataire Azure AD B2C.
3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
4. Sélectionnez **flux d’utilisateurs (stratégies)**.
5. Ouvrir le flux d’utilisateur que vous avez créé précédemment. 
6. Sélectionner **Propriétés**.
7. Sous **durée de vie du jeton**, ajustez les propriétés suivantes en fonction des besoins de votre application :

    ![Configurer la durée de vie](./media/configure-tokens/token-lifetime.png)

8. Cliquez sur **Enregistrer**.

## <a name="configure-token-compatibility"></a>Configurer la compatibilité des jetons

1. Sélectionnez **flux d’utilisateurs (stratégies)**.
2. Ouvrir le flux d’utilisateur que vous avez créé précédemment. 
3. Sélectionner **Propriétés**.
4. Sous **les paramètres de compatibilité de jeton**, ajustez les propriétés suivantes en fonction des besoins de votre application :

    ![Configurer la compatibilité des jetons](./media/configure-tokens/token-compatibility.png)

5. Cliquez sur **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus d’informations sur [l’utilisation des jetons d’accès](active-directory-b2c-access-tokens.md).



