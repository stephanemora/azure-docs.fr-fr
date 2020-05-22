---
title: 'Démarrage rapide : Création de votre première application web statique avec Azure Static Web Apps'
description: Apprenez à créer une instance Azure Static Web Apps avec votre framework frontal favori.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 14fd237b6437c15ede2da7c0694004f6c22736cc
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599130"
---
# <a name="quickstart-building-your-first-static-web-app"></a>Démarrage rapide : Création de votre première application web statique

Azure Static Web Apps publie les sites web dans un environnement de production en compilant les applications à partir d’un référentiel GitHub. Dans ce guide de démarrage rapide, vous créez une application web avec votre framework frontal par défaut à partir d’un référentiel GitHub.

Si vous n’avez pas d’abonnement Azure, [créez un compte d’essai gratuit](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Prérequis

- [GitHub](https://github.com)
- Compte [Azure](https://portal.azure.com)

## <a name="create-a-repository"></a>Créer un référentiel

Cet article utilise les référentiels de modèles GitHub pour vous permettre de créer facilement un nouveau référentiel. Les modèles sont des applications créées avec différents frameworks frontaux.

# <a name="angular"></a>[Angular](#tab/angular)

- Accédez à l’emplacement suivant pour créer un nouveau référentiel.
  - https://github.com/staticwebdev/angular-basic/generate
- Nommez votre référentiel **my-first-static-web-app**

# <a name="react"></a>[React](#tab/react)

- Accédez à l’emplacement suivant pour créer un nouveau référentiel.
  - https://github.com/staticwebdev/react-basic/generate
- Nommez votre référentiel **my-first-static-web-app**

# <a name="vue"></a>[Vue](#tab/vue)

- Accédez à l’emplacement suivant pour créer un nouveau référentiel.
  - https://github.com/staticwebdev/vue-basic/generate
- Nommez votre référentiel **my-first-static-web-app**

# <a name="no-framework"></a>[Pas de framework](#tab/vanilla-javascript)

- Accédez à l’emplacement suivant pour créer un nouveau référentiel.
  - https://github.com/staticwebdev/vanilla-basic/generate
- Nommez votre référentiel **my-first-static-web-app**

> [!NOTE]
> Azure Static Web Apps nécessite au moins un fichier HTML pour pouvoir créer une application web. Le référentiel que vous créez lors de cette étape comprend un seul _fichier index.html_.

---

Cliquez sur le bouton **Créer un référentiel à partir du modèle**.

:::image type="content" source="media/getting-started/create-template.png" alt-text="Créer un référentiel à partir du modèle":::

## <a name="create-a-static-web-app"></a>Créer une application web statique

Maintenant que le référentiel est créé, vous pouvez créer une application web statique à partir du Portail Azure.

- Accéder au [portail Azure](https://portal.azure.com)
- Cliquer sur **Créer une ressource**
- Rechercher **Static Web Apps**
- Cliquer sur **Static Web Apps (préversion)**
- Cliquez sur **Créer**

### <a name="basics"></a>Concepts de base

Commencez par configurer votre nouvelle application et liez-la à un référentiel GitHub.

:::image type="content" source="media/getting-started/basics-tab.png" alt-text="Onglet Informations de base":::

- Sélectionnez votre _abonnement Azure_.
- Sélectionnez ou créez un _groupe de ressources_.
- Nommez l’application **my-first-static-web-app**.
  - Les caractères valides sont `a-z` (insensible à la casse), `0-9`et `_`.
- Sélectionnez la _région_ la plus proche de vous.
- Sélectionnez **SKU** _gratuite_.
- Cliquez sur le bouton **Se connecter à GitHub** et authentifiez-vous auprès de GitHub.

Une fois que vous êtes connecté avec GitHub, entrez les informations relatives au référentiel.

:::image type="content" source="media/getting-started/repository-details.png" alt-text="Détails du référentiel":::

- Sélectionnez votre _organisation_ préférée.
- Sélectionnez **my-first-web-static-app** dans la liste déroulante _Référentiel_.
- Sélectionner **maître** dans la liste déroulante _Branche_.
- Cliquez sur le bouton **Suivant : Compiler >** pour modifier la configuration de build.

:::image type="content" source="media/getting-started/next-build-button.png" alt-text="Bouton Compilation suivante":::

### <a name="build"></a>Build

Ensuite, ajoutez les détails de configuration spécifiques à votre framework frontal favori.

# <a name="angular"></a>[Angular](#tab/angular)

- Entrez **/** dans la zone _emplacement de l’application_.
- Désactivez la valeur par défaut de la zone _Emplacement de l’API_.
- Entrez **dist/angular-basic** dans la boîte _Emplacement de l’artefact d’application_.

# <a name="react"></a>[React](#tab/react)

- Entrez **/** dans la zone _emplacement de l’application_.
- Désactivez la valeur par défaut de la zone _Emplacement de l’API_.
- Entrez **build** dans la zone _Emplacement de l’artefact d’application_.

# <a name="vue"></a>[Vue](#tab/vue)

- Entrez **/** dans la zone _emplacement de l’application_.
- Désactivez la valeur par défaut de la zone _Emplacement de l’API_.
- Entrez **dist** dans la zone _Emplacement de l’artefact d’application_.

# <a name="no-framework"></a>[Pas de framework](#tab/vanilla-javascript)

- Entrez **/** dans la zone _emplacement de l’application_.
- Désactivez la valeur par défaut de la zone _Emplacement de l’API_.
- Désactivez la valeur par défaut de la zone _Emplacement de l’artefact d’application_.

---

Cliquez sur le bouton **Vérifier + créer**.

:::image type="content" source="media/getting-started/review-create.png" alt-text="Bouton Vérifier + créer":::

### <a name="review--create"></a>Vérifier + créer

Une fois la demande validée, vous pouvez continuer à créer l’application.

Cliquez sur le bouton **Créer**

:::image type="content" source="media/getting-started/create-button.png" alt-text="Bouton Créer":::

Une fois la ressource créée, cliquez sur le bouton **Accéder à la ressource**.

:::image type="content" source="media/getting-started/resource-button.png" alt-text="Accédez au bouton de ressources":::

## <a name="view-the-website"></a>Voir le site web

Le déploiement d’une application statique comporte deux aspects. Le premier provisionne les ressources Azure sous-jacentes qui composent votre application. Le second est un flux de travail GitHub Actions qui génère et publie votre application.

Avant de pouvoir accéder à votre nouveau site statique, la build de déploiement doit d’abord finir de s’exécuter.

La fenêtre de vue d’ensemble de Static Web Apps présente des liens qui vous permettent d’interagir avec votre application web.

:::image type="content" source="media/getting-started/overview-window.png" alt-text="Fenêtre Vue d’ensemble":::

1. Cliquez sur la bannière intitulée « Cliquez ici pour vérifier l’état de vos GitHub Actions » pour accéder aux actions GitHub en cours d’exécution sur votre référentiel. Une fois que vous avez vérifié que le travail de déploiement est terminé, vous pouvez accéder à votre site web à l’aide de l’URL générée.

2. Une fois le flux de travail GitHub Actions terminé, vous pouvez cliquer sur l’_URL_ pour ouvrir le site web dans un nouvel onglet.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne prévoyez pas de continuer à utiliser cette application, vous pouvez supprimer l’instance Azure Static Web Apps en effectuant les étapes suivantes :

1. Ouvrez le [portail Azure](https://portal.azure.com)
1. Recherchez **my-first-web-static-app** dans la barre de recherche supérieure.
1. Cliquez sur le nom de l’application.
1. Cliquez sur le bouton **Supprimer**.
1. Cliquez sur **Oui** pour confirmer l’action de suppression.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter une API](add-api.md)
