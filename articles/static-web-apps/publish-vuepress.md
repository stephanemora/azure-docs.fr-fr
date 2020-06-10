---
title: 'Tutoriel : Publier un site VuePress dans Azure Static Web Apps'
description: Ce tutoriel vous montre comment déployer une application VuePress dans Azure Static Web Apps.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.openlocfilehash: cc1bf52190cb47bc4ffd6efe159ed88cac560e02
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2020
ms.locfileid: "84297992"
---
# <a name="tutorial-publish-a-vuepress-site-to-azure-static-web-apps-preview"></a>Tutoriel : Publier un site VuePress dans Azure Static Web Apps en préversion

Cet article montre comment créer et déployer une application web [VuePress](https://vuepress.vuejs.org/) dans [Azure Static Web Apps](overview.md). Le résultat est une nouvelle application Azure Static Web Apps avec les actions GitHub associées qui vous permettent de contrôler la manière dont l’application est générée et publiée.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
>
> - Créer une application VuePress
> - Configurer une application Azure Static Web Apps
> - Déployer l’application VuePress dans Azure

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. Si vous n’en avez pas, vous pouvez créer un [compte gratuitement](https://azure.microsoft.com/free/).
- Un compte GitHub. Si vous n’en avez pas, vous pouvez créer un [compte gratuitement](https://github.com/join).
- [Node.js](https://nodejs.org) (déjà installé).

## <a name="create-a-vuepress-app"></a>Créer une application VuePress

Créer une application VuePress à partir de l’interface CLI :

1. Créez un nouveau dossier pour l’application VuePress.

   ```bash
   mkdir static-site
   ```

1. Ajoutez un fichier _README.md_.

   ```bash
   echo '# Hello From VuePress' > README.md
   ```

1. Initialisez le fichier _package.json_.

   ```bash
   npm init -y
   ```

1. Ajoutez VuePress en tant que `devDependency`.

   ```bash
   npm install --save-dev vuepress
   ```

1. Ouvrez le fichier _package.json_ dans un éditeur de texte et ajoutez une commande de génération à la section [`scripts`](https://docs.npmjs.com/cli-commands/run-script.html).

   ```json
   ...
   "scripts": {
       "build": "vuepress build"
   }
   ...
   ```

1. Créez un fichier _.gitignore_ pour exclure le dossier _node\_modules_.

    ```bash
    echo 'node_modules' > .gitignore
    ```

1. Initialisez un référentiel Git.

   ```bash
    git init
    git add -A
    git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Transmettre votre application à GitHub

Vous devez avoir un référentiel sur GitHub pour vous connecter à Azure Static Web Apps. Les étapes suivantes vous montrent comment créer un référentiel pour votre site.

1. Créez un référentiel GitHub (ne créez pas de fichier README) sur [https://github.com/new](https://github.com/new) appelé **vuepress-static-app**.

1. Ajoutez le référentiel GitHub en tant que référentiel distant pour votre référentiel local. Veillez à remplacer l’espace réservé `<YOUR_USER_NAME>` par votre nom d’utilisateur GitHub dans la commande suivante.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/vuepress-static-app
   ```

1. Transmettez votre référentiel local à GitHub.

   ```bash
   git push --set-upstream origin master
   ```

## <a name="deploy-your-web-app"></a>Déployez votre application web

Les étapes suivantes vous montrent comment créer une nouvelle application Static Web Apps et la déployer dans un environnement de production.

### <a name="create-the-application"></a>Création de l'application

1. Accéder au [portail Azure](https://portal.azure.com)
1. Cliquez sur **Create a Resource** (Créer une ressource).
1. Recherchez **Static Web Apps**.
1. Cliquez sur **Static Web Apps (Preview)** (Static Web Apps (préversion))
1. Cliquez sur **Créer**

   :::image type="content" source="./media/publish-vuepress/create-in-portal.png" alt-text="Création d’une application Static Web Apps (préversion) dans le portail":::

1. Pour **Abonnement**, acceptez l’abonnement qui est listé ou sélectionnez-en un nouveau dans la liste déroulante.

1. Dans _Groupe de ressources_, sélectionnez **Nouveau**. Dans _Nom du nouveau groupe de ressources_, entrez **vuepress-static-app**, puis sélectionnez **OK**.

1. Ensuite, entrez un nom pour votre application dans la zone **Nom**. Les caractères valides sont `a-z`, `A-Z`, `0-9` et `-`.

1. Dans _Region_ (Région), sélectionnez une région disponible proche de vous.

1. Pour _SKU_, sélectionnez **Free** (Gratuit).

   :::image type="content" source="./media/publish-vuepress/basic-app-details.png" alt-text="Champs renseignés":::

1. Cliquez sur le bouton **Sign in with GitHub** (Se connecter avec GitHub).

1. Sélectionnez l’**organisation** sous laquelle vous avez créé le référentiel.

1. Sélectionnez **vuepress-static-app** comme _référentiel_ .

1. Pour _Branche_, sélectionnez **maître**.

   :::image type="content" source="./media/publish-vuepress/completed-github-info.png" alt-text="Champs GitHub renseignés":::

### <a name="build"></a>Build

Vous ajoutez ensuite les paramètres de configuration utilisés par le processus de génération pour générer votre application. Les paramètres suivants configurent le fichier de workflow d’action GitHub.

1. Cliquez sur le bouton **Next: Build >** (Suivant : générer >) pour modifier la configuration de la génération.

1. Définissez _Emplacement de l’application_ sur **/** .

1. Définissez _Emplacement de l’artefact de l’application_ sur **.vuepress/dist**.

Il n’est pas nécessaire de fournir une valeur pour _Emplacement de l’API_ puisque vous ne déployez pas d’API pour le moment.

   :::image type="content" source="./media/publish-vuepress/build-details.png" alt-text="Paramètres de génération":::

### <a name="review-and-create"></a>Examiner et créer

1. Pour vérifier que les informations sont correctes, cliquez sur le bouton **Review + Create** (Vérifier + créer).

1. Cliquez sur **Créer** pour commencer la création de l’application Azure Static Web Apps et approvisionner une action GitHub pour le déploiement.

1. Une fois le déploiement terminé, cliquez sur **Accéder à la ressource**.

1. Sur l’écran de la ressource, cliquez sur le lien _URL_ pour ouvrir l’application que vous avez déployée. Vous devrez peut-être patienter une ou deux minutes, le temps que l’action GitHub soit terminée.

   :::image type="content" source="./media/publish-vuepress/deployed-app.png" alt-text="Application déployée":::

### <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter un domaine personnalisé](custom-domain.md)
