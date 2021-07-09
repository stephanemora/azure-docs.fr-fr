---
title: 'Tutoriel : Déployer des sites web Nuxt.js rendus côté serveur sur Azure Static Web Apps'
description: Générez et déployez des sites dynamiques Nuxt.js avec Azure Static Web Apps.
services: static-web-apps
author: christiannwamba
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: chnwamba
ms.custom: devx-track-js
ms.openlocfilehash: 7654546ba52406aed5659cc736bb9a538a73ac7f
ms.sourcegitcommit: 0ce834cd348bb8b28a5f7f612c2807084cde8e8f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109813233"
---
# <a name="deploy-server-rendered-nuxtjs-websites-on-azure-static-web-apps"></a>Déployer des sites web Nuxt.js rendus côté serveur sur Azure Static Web Apps

Ce tutoriel permet d’apprendre à déployer un site web statique généré [Nuxt.js](https://nuxtjs.org) sur [Azure Static Web Apps](overview.md). Pour commencer, vous apprendrez à créer, configurer et déployer une application Nuxt.js. Au cours de ce processus, vous apprendrez aussi à gérer les problèmes courants rencontrés lors de la génération de pages statiques avec Nuxt.js

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/).
- Un compte GitHub. [Créez un compte gratuitement](https://github.com/join).
- [Node.js](https://nodejs.org) (déjà installé).

## <a name="set-up-a-nuxtjs-app"></a>Créer une application Nuxt.js

Pour créer un projet Nuxt.js, utilisez `create-nuxt-app`. Dans ce tutoriel, au lieu de partir à zéro, vous clonez un référentiel existant. Ce référentiel est créé de telle sorte qu’il constitue un parfait exemple de déploiement d’une application Nuxt.js dynamique en tant que site statique.

1. Créez un référentiel sous votre compte GitHub à partir d’un référentiel de modèles.
1. Accédez à [http://github.com/staticwebdev/nuxtjs-starter/generate](https://github.com/login?return_to=/staticwebdev/nuxtjs-starter/generate)
1. Nommez le référentiel **nuxtjs-starter**.
1. Ensuite, clonez le nouveau référentiel sur votre ordinateur. Veillez à remplacer <YOUR_GITHUB_ACCOUNT_NAME> par le nom de votre compte.

    ```bash
    git clone http://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/nuxtjs-starter
    ```

1. Accédez à l’application Nuxt.js nouvellement clonée :

   ```bash
   cd nuxtjs-starter
   ```

1. Installez des dépendances :

    ```bash
    npm install
    ```

1. Démarrez l’application Nuxt.js dans l’environnement de développement :

    ```bash
    npm run dev
    ```

Accédez à `http://localhost:3000` pour ouvrir l’application, où le site web suivant devrait s’ouvrir dans votre navigateur favori :

:::image type="content" source="media/deploy-nuxtjs/start-nuxtjs-app.png" alt-text="Démarrer l’application Nuxt.js":::

Lorsque vous cliquez sur une infrastructure ou une bibliothèque, vous devez voir une page détaillant des informations sur l’élément sélectionné :

:::image type="content" source="media/deploy-nuxtjs/start-nuxtjs-details.png" alt-text="Page de détails":::

## <a name="generate-a-static-website-from-nuxtjs-build"></a>Générer un site web statique à partir du build Nuxt.js

Quand vous générez un site Nuxt.js en utilisant `npm run build`, l’application est générée comme une application web traditionnelle, et non comme un site statique. Pour générer un site statique, utilisez la configuration d’application suivante.

1. Mettez à jour le script de génération du _package.json_ pour générer uniquement un site statique avec la commande `nuxt generate` :

    ```json
    "scripts": {
      "dev": "nuxt dev",
      "build": "nuxt generate",
    },
    ```

    Maintenant que cette commande est en place, Static Web Apps exécute le script `build` chaque fois que vous transmettez (push) une validation (commit).

1. Générez un site statique :

    ```bash
    npm run build
    ```

    Nuxt.js génère le site statique et le copie dans un dossier _dist_ à la racine de votre répertoire de travail.

    > [!NOTE]
    > Ce dossier est listé dans le fichier _.gitignore_, car il doit être généré par CI/CD lors du déploiement.

## <a name="push-your-static-website-to-github"></a>Transmettre votre site web statique à GitHub

Azure Static Web Apps déploie votre application à partir d’un référentiel GitHub et continue à effectuer cette opération pour chaque validation transmise vers une branche désignée. Utilisez les commandes suivantes pour synchroniser vos modifications avec GitHub.

1. Pour indexer tous les fichiers modifiés :

    ```bash
    git add .
    ```

1. Pour valider toutes les modifications :

    ```bash
    git commit -m "Update build config"
    ```

1. Pour transmettre vos modifications à GitHub :

    ```bash
    git push origin main
    ```

## <a name="deploy-your-static-website"></a>Déployer votre site web statique

La procédure suivante vous indique comment lier Azure Static Web Apps à l’application que vous venez de transmettre à GitHub. Puis, une fois dans Azure, vous pourrez déployer l’application dans un environnement de production.

### <a name="create-an-azure-static-web-apps-resource"></a>Créer une ressource Azure Static Web Apps

1. Accédez au [portail Azure](https://portal.azure.com).
1. Sélectionnez **Créer une ressource**.
1. Recherchez **Static Web Apps**.
1. Sélectionnez **Static Web Apps**.
1. Sélectionnez **Create** (Créer).
1. Sous l’onglet _Informations de base_, entrez les valeurs suivantes.

    | Propriété | Valeur |
    | --- | --- |
    | _Abonnement_ | Le nom de votre abonnement Azure. |
    | _Groupe de ressources_ | **my-nuxtjs-group**  |
    | _Nom_ | **my-nuxtjs-app** |
    | _Type de plan_ | **Gratuit** |
    | _Région de l’API Azure Functions et des environnements intermédiaires_ | Sélectionnez la région la plus proche de vous. |
    | _Source_ | **GitHub** |

1. Sélectionnez **Se connecter avec GitHub** et authentifiez-vous auprès de GitHub.

1. Entrez les valeurs GitHub suivantes.

    | Propriété | Valeur |
    | --- | --- |
    | _Organisation_ | Sélectionnez l’organisation GitHub de votre choix. |
    | _Dépôt_ | Sélectionnez le référentiel que vous avez créé précédemment. |
    | _Branche_ | Sélectionnez **principal**. |

1. Dans la section _Détails de build_, sélectionnez **Personnalisé** dans la liste déroulante _Présélections de build_ et conservez les valeurs par défaut.

1. Dans _Emplacement de l’application_, entrez **./** .
1. Laissez la zone _Emplacement de l’API_ vide.
1. Dans la zone _Emplacement de la sortie_, entrez **out**.

### <a name="review-and-create"></a>Examiner et créer

1. Pour vérifier que les informations sont correctes, sélectionnez le bouton **Vérifier + créer**.

1. Pour démarrer la création de l’application web statique App Service et le provisionnement d’une action GitHub pour le déploiement, sélectionnez **Créer**.

1. Une fois le déploiement terminé, cliquez sur **Accéder à la ressource**.

1. Dans la fenêtre _Overview_ (Présentation), cliquez sur le lien *URL* pour ouvrir l’application que vous avez déployée.

Si le site web ne se charge pas immédiatement, c’est que le workflow GitHub Actions est toujours en cours d’exécution en arrière-plan. Lorsqu’il sera terminé, actualisez votre navigateur pour afficher votre application web.

Vous pouvez vérifier l’état des workflow Actions en accédant aux actions de votre référentiel :

```url
https://github.com/<YOUR_GITHUB_USERNAME>/nuxtjs-starter/actions
```

### <a name="sync-changes"></a>Synchroniser les modifications

Une fois l’application créée, Azure Static Web Apps a créé un fichier de workflow GitHub Actions dans votre référentiel. Vous devez placer ce fichier dans votre référentiel local pour synchroniser votre historique git.

Revenez au terminal et exécutez la commande suivante : `git pull origin main`.

## <a name="configure-dynamic-routes"></a>Configurer des routages dynamiques

Accédez au site qui vient d’être déployé et cliquez sur l’un des logos de l’infrastructure ou de la bibliothèque. Au lieu d’obtenir une page détaillant des informations, vous obtenez une page d’erreur 404.

:::image type="content" source="media/deploy-nuxtjs/404-in-production.png" alt-text="Erreur 404 sur les routages dynamiques":::

En effet, Nuxt.js n’a généré que la page d'accueil du site statique. Nuxt.js peut générer des fichiers statiques `.html` équivalents pour chaque fichier de page `.vue`, mais il existe une exception. 

Si la page est une page dynamique, par exemple `_id.vue`, Nuxt.js n’aura pas assez d’informations pour générer un code HTML statique à partir de cette page dynamique. Vous devez alors fournir explicitement les chemins d’accès possibles pour les routages dynamiques.

## <a name="generate-static-pages-from-dynamic-routes"></a>Générer des pages statiques à partir de routages dynamiques

1. Mettez à jour le fichier _nuxt.config.js_, pour que Nuxt.js utilise une liste de toutes les données disponibles afin de générer des pages statiques pour chaque infrastructure/bibliothèque :

   ```javascript
     import { projects } from "./utils/projectsData";

     export default {
       mode: "universal",

       //...truncated

       generate: {
         async routes() {
           const paths = [];

           projects.forEach(project => {
             paths.push(`/project/${project.slug}`);
           });

           return paths;
         }
       }
     };
   ```

   > [!NOTE]
   > `routes` est une fonction asynchrone. Vous pouvez donc effectuer une requête vers un API dans cette fonction, puis utiliser la liste retournée pour générer les chemins d’accès.

2. Transmettez les nouvelles modifications à votre référentiel GitHub, puis patientez quelques minutes pendant que GitHub Actions génère à nouveau votre site. Une fois la génération terminée, l’erreur 404 disparaît.

   :::image type="content" source="media/deploy-nuxtjs/404-in-production-fixed.png" alt-text="Résolution de l’erreur 404 sur les routages dynamiques":::

> [!div class="nextstepaction"]
> [Configurer un nom de domaine personnalisé](custom-domain.md)
