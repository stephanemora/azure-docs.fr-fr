---
title: 'Tutoriel : Publier un site Gatsby dans Azure Static Web Apps'
description: Ce tutoriel vous montre comment déployer une application Gatsby dans Azure Static Web Apps.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/10/2021
ms.author: aapowell
ms.custom: devx-track-js
ms.openlocfilehash: 4c6a68b8db40aa07c251cabab28217143105aab1
ms.sourcegitcommit: 0ce834cd348bb8b28a5f7f612c2807084cde8e8f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109814511"
---
# <a name="tutorial-publish-a-gatsby-site-to-azure-static-web-apps"></a>Tutoriel : Publier un site Gatsby dans Azure Static Web Apps

Cet article montre comment créer et déployer une application web [Gatsby](https://gatsbyjs.org) dans [Azure Static Web Apps](overview.md). Vous obtenez ainsi un nouveau site Static Web Apps (avec la fonctionnalité GitHub Actions associée) qui vous permet de contrôler la manière dont l’application est générée et publiée.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
>
> - Créer une application Gatsby
> - Configurer un site Azure Static Web Apps
> - Déployer l’application Gatsby sur Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. Si vous n’en avez pas, vous pouvez créer un [compte gratuitement](https://azure.microsoft.com/free/).
- Un compte GitHub. Si vous n’en avez pas, vous pouvez créer un [compte gratuitement](https://github.com/join).
- [Node.js](https://nodejs.org) (déjà installé).

## <a name="create-a-gatsby-app"></a>Créer une application Gatsby

Créez une application Gatsby à l’aide de l’interface de ligne de commande (CLI) Gatsby :

1. Ouvrez un terminal.
1. Utilisez l’outil [npx](https://www.npmjs.com/package/npx) pour créer une application avec l’interface CLI Gatsby. Cela peut prendre quelques minutes.

   ```bash
   npx gatsby new static-web-app
   ```

1. Accédez à l’application nouvellement créée.

   ```bash
   cd static-web-app
   ```

1. Initialiser un dépôt Git

   ```bash
   git init
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Transmettre votre application à GitHub

Vous devez avoir un référentiel sur GitHub pour créer une ressource Azure Static Web Apps.

1. Créez un référentiel GitHub vide (ne créez pas de fichier README) à partir de [https://github.com/new](https://github.com/new) nommé **gatsby-static-web-app**.

1. Ensuite, ajoutez le référentiel GitHub que vous venez de créer en tant que référentiel distant à votre référentiel local. Veillez à remplacer l’espace réservé `<YOUR_USER_NAME>` par votre nom d’utilisateur GitHub dans la commande suivante.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/gatsby-static-web-app
   ```

1. Transmettez votre référentiel local à GitHub.

   ```bash
   git push --set-upstream origin main
   ```

## <a name="deploy-your-web-app"></a>Déployez votre application web

Les étapes suivantes vous montrent comment créer une application Static Web Apps et la déployer dans un environnement de production.

### <a name="create-the-application"></a>Création de l'application

1. Accéder au [portail Azure](https://portal.azure.com)
1. Sélectionnez **Créer une ressource**
1. Recherchez **Applications web statiques**
1. Sélectionnez **Static Web Apps**
1. Sélectionnez **Créer**
1. Sous l’onglet _Informations de base_, entrez les valeurs suivantes.

    | Propriété | Valeur |
    | --- | --- |
    | _Abonnement_ | Le nom de votre abonnement Azure. |
    | _Groupe de ressources_ | **my-gatsby-group**  |
    | _Nom_ | **my-gatsby-app** |
    | _Type de plan_ | **Gratuit** |
    | _Région de l’API Azure Functions et des environnements intermédiaires_ | Sélectionnez la région la plus proche de vous. |
    | _Source_ | **GitHub** |

1. Sélectionnez **Se connecter avec GitHub** et authentifiez-vous auprès de GitHub.

1. Entrez les valeurs GitHub suivantes.

    | Propriété | Valeur |
    | --- | --- |
    | _Organisation_ | Sélectionnez l’organisation GitHub de votre choix. |
    | _Dépôt_ | Sélectionnez **gatsby-static-web-app**. |
    | _Branche_ | Sélectionnez **principal**. |

1. Dans la section _Détails de build_, sélectionnez **Gatsby** dans la liste déroulante _Présélections de build_ et conservez les valeurs par défaut.

### <a name="review-and-create"></a>Examiner et créer

1. Pour vérifier que les informations sont correctes, sélectionnez le bouton **Vérifier + créer**.

1. Pour démarrer la création de l’application web statique App Service et le provisionnement d’une action GitHub pour le déploiement, sélectionnez **Créer**.

1. Une fois le déploiement terminé, cliquez sur **Accéder à la ressource**.

1. Sur l’écran de la ressource, cliquez sur le lien _URL_ pour ouvrir l’application que vous avez déployée. Vous devrez peut-être patienter une ou deux minutes, le temps que l’action GitHub soit terminée.

   :::image type="content" source="./media/publish-gatsby/deployed-app.png" alt-text="Application déployée":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter un domaine personnalisé](custom-domain.md)
