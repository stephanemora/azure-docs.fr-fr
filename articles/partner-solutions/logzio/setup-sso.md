---
title: Authentification unique pour l’intégration d’Azure à Logz.io – Solutions des partenaires Azure
description: Découvrez comment configurer l’authentification unique pour l’intégration d’Azure à Logz.io.
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 10/25/2021
ms.author: tomfitz
ms.openlocfilehash: 86ef7f781e3093b2daa78a393963b9053c9f5982
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057568"
---
# <a name="set-up-logzio-single-sign-on"></a>Configurer l’authentification unique de Logz.io

Cet article explique comment configurer l’authentification unique dans Azure Active Directory. L’intégration de l’authentification unique pour Logz.io est facultative.

## <a name="configure-single-sign-on"></a>Configurer l’authentification unique

Pour utiliser la fonctionnalité d’authentification unique Security Assertion Markup Language (SAML) dans la ressource Logz.io, vous devez configurer une application d’entreprise.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Dans le menu du portail, sélectionnez **Azure Active Directory** ou recherchez _Azure Active Directory_.
1. Accédez à **Gérer** > **Applications d’entreprise** et sélectionnez le bouton **Nouvelle application**.
1. Recherchez _Logz.io_ et sélectionnez l’application SAML nommée **Logz.io - Azure AD Integration**, puis sélectionnez **Créer**.

   :::image type="content" source="./media/sso-setup/gallery.png" alt-text="Parcourez la galerie Azure Active Directory.":::

1. Dans **Vue d’ensemble**, copiez l’**ID d’application** de l’application d’authentification unique.

   Notez l’_ID d’application_, car vous l’utiliserez dans une étape ultérieure pour configurer SAML.

   :::image type="content" source="./media/sso-setup/app-id.png" alt-text="Copiez l’ID d’application.":::

1. Sélectionnez la vignette nommée **2. Configurer l’authentification unique**.

   :::image type="content" source="./media/sso-setup/setup.png" alt-text="Configurez l’authentification unique.":::

1. Sélectionnez le protocole **SAML** comme méthode d’authentification unique.

   :::image type="content" source="./media/sso-setup/saml.png" alt-text="Authentification unique SAML.":::

1. Dans la configuration SAML, sélectionnez des valeurs existantes ou utilisez les zones de texte pour entrer les champs obligatoires **Identificateur** et **URL de réponse**. Pour créer de nouvelles entrées, utilisez l’_ID d’application_ qui a été copié dans une étape précédente.

   Utilisez les modèles suivants pour ajouter de nouvelles valeurs :

   - **Identificateur** : `urn:auth0:logzio:<Application ID>`
   - **URL de réponse** : `https://logzio.auth0.com/login/callback?connection=<Application ID>`

   :::image type="content" source="./media/sso-setup/saml-config.png" alt-text="Configuration SAML de base.":::

1. À l’invite **Enregistrer les paramètres de l’authentification unique**, sélectionnez **Oui**, puis sélectionnez **Enregistrer**.

   À l’invite de l’authentification unique, sélectionnez **Non, je testerai plus tard**.

1. Une fois la configuration SAML enregistrée, accédez à **Gérer** > **Propriétés** et définissez l’option **Affectation d’utilisateurs requise ?** sur **Non**. Sélectionnez **Enregistrer**.

   :::image type="content" source="./media/sso-setup/properties.png" alt-text="Propriétés de l’authentification unique.":::

## <a name="next-steps"></a>Étapes suivantes

- Pour résoudre les problèmes liés à l’authentification unique, consultez [Résolution des problèmes](troubleshoot.md).
- Pour créer une intégration Logz.io, consultez [Démarrage rapide : Créer une ressource Logz.io dans le portail Azure](create.md).
