---
title: Réinitialiser des jetons de déploiement dans Azure Static Web Apps (préversion)
description: Réinitialiser les jetons dans un site Azure Static Web Apps
services: static-web-apps
author: webmaxru
ms.author: masalnik
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 1/31/2021
ms.openlocfilehash: fe1edb2693993d02a705039c18b04c8d1b7b9725
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101743146"
---
# <a name="reset-deployment-tokens-in-azure-static-web-apps-preview"></a>Réinitialiser des jetons de déploiement dans Azure Static Web Apps (préversion)

Lorsque vous créez un site Azure Static Web Apps, Azure génère un jeton utilisé pour identifier l’application pendant le déploiement. Pendant l’approvisionnement, ce jeton est stocké en tant que secret dans le dépôt GitHub. Cet article explique comment utiliser et gérer ce jeton.

Normalement, vous n’avez pas à vous soucier du jeton de déploiement, mais voici quelques raisons pour lesquelles vous devrez peut-être récupérer ou réinitialiser le jeton.

* **Compromission du jeton** : réinitialisez votre jeton s’il est exposé à un tiers externe.
* **Déploiement à partir d’un dépôt GitHub séparé** : si vous opérez un déploiement manuel à partir d’un dépôt GitHub séparé, vous devez définir le jeton de déploiement dans le nouveau dépôt.

## <a name="prerequisites"></a>Prérequis

- Un référentiel GitHub existant configuré avec Azure Static Web Apps.
- Consultez le guide de démarrage rapide [Générer votre première application web statique](getting-started.md) si vous ne l’avez pas déjà fait.

## <a name="reset-a-deployment-token"></a>Réinitialiser un jeton de déploiement

1. Cliquez sur le lien **Gérer un jeton de déploiement** sur la page _vue d’ensemble_ de votre site Azure Static Web Apps.

    :::image type="content" source="./media/deployment-token-management/manage-deployment-token-button.png" alt-text="Gestion du jeton de déploiement":::

1. Cliquez sur le bouton **Réinitialiser le jeton**.

    :::image type="content" source="./media/deployment-token-management/manage-deployment-token.png" alt-text="Réinitialisation du jeton de déploiement":::

1. Après avoir affiché un nouveau jeton dans le champ _Jeton de déploiement_, copiez le jeton en cliquant sur l’icône **Copier dans le presse-papiers**.


## <a name="update-a-secret-in-the-github-repository"></a>Mettre à jour un secret dans le dépôt GitHub

Pour garantir l’exécution du déploiement automatisé, après la réinitialisation d’un jeton, vous devez définir la nouvelle valeur dans le dépôt GitHub correspondant.

1. Accédez au dépôt de votre projet sur GitHub, puis cliquez sur l’onglet **Paramètres** .
1. Cliquez sur l’élément de menu **Secrets**. Vous trouverez un secret généré pendant l’approvisionnement de l’application web statique nommée _AZURE_STATIC_WEB_APPS_API_TOKEN_... dans la section _Secrets du référentiel_.

    :::image type="content" source="./media/deployment-token-management/github-repo-secrets.png" alt-text="Affichage des secrets du référentiel":::

    > [!NOTE]
    > Si vous avez créé le site Azure Static Web Apps dans plusieurs branches de ce référentiel, vous verrez plusieurs secrets _AZURE_STATIC_WEB_APPS_API_TOKEN_... dans cette liste. Sélectionnez le bon en faisant correspondre le nom de fichier indiqué dans le champ _Modifier le flux de travail_ sous l’onglet _Vue d’ensemble_ du site Azure Static Web Apps.

1. Cliquez sur le bouton **Mettre à jour** pour ouvrir l’éditeur.
1. **Collez la valeur** du jeton de déploiement dans le champ _Valeur_.
1. Cliquez sur **Mettre à jour le secret**.

    :::image type="content" source="./media/deployment-token-management/github-update-secret.png" alt-text="Mise à jour du secret du référentiel":::

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Publier à partir d’un générateur de site statique](publish-gatsby.md)
