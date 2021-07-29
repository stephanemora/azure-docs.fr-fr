---
title: Sécurisation des secrets d’authentification dans Azure Key Vault
description: Utilisez l’identité gérée pour sécuriser les secrets d’authentification dans Azure Key Vault.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/17/2021
ms.author: cshoe
ms.openlocfilehash: cc0ced1a6c91bf2e7960e638c295d33a45db135e
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110073339"
---
# <a name="securing-authentication-secrets-in-azure-key-vault"></a>Sécurisation des secrets d’authentification dans Azure Key Vault

Quand vous configurez des fournisseurs d’authentification personnalisés, vous pouvez stocker les secrets de connexion dans Azure Key Vault. Cet article montre comment utiliser une identité gérée pour accorder à Azure Static Web Apps l’accès à Key Vault pour les secrets d’authentification personnalisés.

Les secrets de sécurité nécessitent que les éléments suivants soient en place.

- Créez une identité affectée par le système dans l’instance Static Web Apps.
- Accordez l’accès au secret Key Vault à l’identité.
- Référencez le secret Key Vault dans les paramètres de l’application Static Web Apps.

Cet article explique comment configurer chacun de ces éléments dans votre application.

## <a name="prerequisites"></a>Prérequis

- Un site Azure Static Web Apps existant.
- Ressource Key Vault existante avec une valeur secrète.

## <a name="create-identity"></a>Créer une identité

1. Sur le portail Azure, ouvrez votre site Static Web Apps.

1. Dans le menu _Paramètres_, sélectionnez **Identité**.

1. Sélectionnez l’onglet **Affectée par le système**.

1. Sous l’étiquette _État_, sélectionnez le bouton **Activé**.

1. Sélectionnez le bouton **Enregistrer**.

    :::image type="content" source="media/key-vault-secrets/azure-static-web-apps-enable-managed-identity.png" alt-text="Ajout d’une identité affectée au système":::

1. Quand la boîte de dialogue de confirmation s’affiche, sélectionnez le bouton **Oui**.

    :::image type="content" source="media/key-vault-secrets/azure-static-web-apps-enable-managed-identity-confirmation.png" alt-text="Confirmez l’attribution d’identité.":::

Vous pouvez maintenant ajouter une stratégie d’accès pour permettre à votre application Static Web Apps de lire des secrets Key Vault.

## <a name="add-a-key-vault-access-policy"></a>Ajouter une stratégie d’accès Key Vault

1. Ouvrez votre ressource Key Vault dans le portail Azure.

1. Dans le menu _Paramètres_, sélectionnez **Stratégies d’accès**.

1. Sélectionnez le lien **Ajouter une stratégie d’accès**.

1. Dans la liste déroulante _Autorisations de secret_, sélectionnez **Récupérer**.

1. En regard de l’étiquette _Sélectionner un principal_, sélectionnez le lien **Aucun sélectionné**.

1. Dans la zone de recherche, recherchez le nom de votre application Static Web Apps.

1. Sélectionnez l’élément de liste qui correspond au nom de votre application.

1. Sélectionnez le bouton **Sélectionner**.

1. Sélectionnez le bouton **Ajouter**.

1. Sélectionnez le bouton **Enregistrer**.

    :::image type="content" source="media/key-vault-secrets/azure-static-web-apps-key-vault-save-policy.png" alt-text="Enregistrer la stratégie d’accès Azure Key Vault":::

La stratégie d’accès est maintenant enregistrée dans Key Vault. Ensuite, accédez à l’URI du secret à utiliser lors de l’association de votre application Static Web Apps à la ressource Key Vault.

1. Dans le menu _Paramètres_, sélectionnez **Secrets**.

1. Sélectionnez le secret souhaité dans la liste.

1. Sélectionnez la version de secret de votre choix dans la liste.

1. Sélectionnez le **bouton Copier** situé à la fin de la zone de texte _Identificateur du secret_ pour copier la valeur de l’URI du secret dans le presse-papiers.

1. Collez cette valeur dans un éditeur de texte dans le but de l’utiliser ultérieurement.

## <a name="add-application-setting"></a>Ajouter un paramètre d’application

1. Sur le portail Azure, ouvrez votre site Static Web Apps.

1. Dans le menu _Paramètres_, sélectionnez **Configuration**.

1. Dans la section _Paramètres de l’application_, cliquez sur le bouton **Ajouter**.

1. Entrez un nom dans la zone de texte pour le champ _Nom_.

1. Déterminez la valeur du secret dans la zone de texte pour le champ _Valeur_.

    La valeur de secret est composée de plusieurs valeurs différentes. Le modèle suivant montre comment la dernière chaîne est générée.

    ```text
    @Microsoft.KeyVault(SecretUri=<YOUR-KEY-VAULT-SECRET-URI>)
    ```

    Utilisez les étapes suivantes pour générer la valeur de secret complète.

1. Copiez le modèle ci-dessus et collez-le dans un éditeur de texte.

1. Remplacez `<YOUR-KEY-VAULT-SECRET-URI>` par la valeur d’URI Key Vault que vous aviez notée précédemment.

1. Copiez la nouvelle valeur de chaîne complète.

1. Collez la valeur dans la zone de texte du champ _Valeur_.

1. Cliquez sur le bouton **OK**.

1. Sélectionnez le bouton **Enregistrer** en haut de la barre d’outils _Paramètres de l’application_.

    :::image type="content" source="media/key-vault-secrets/azure-static-web-apps-application-settings-save.png" alt-text="Enregistrement des paramètres de l’application":::

Désormais, lorsque votre configuration d’authentification personnalisée fait référence au paramètre d’application que vous venez de créer, la valeur est extraite d’Azure Key Vault à l’aide de l’identité de votre application Static Web Apps.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Authentification personnalisée](./authentication-custom.md)
