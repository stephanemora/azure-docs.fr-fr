---
title: 'Tutoriel : Déployer des sites web Next.js au rendu statique sur Azure Static Web Apps'
description: Générez et déployez des sites dynamiques Next.js avec Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 08/05/2021
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: 1f8ef3146ce7ef1b1767c04284ddbdb191b50d81
ms.sourcegitcommit: d01c2b2719e363178720003b67b968ac2a640204
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122455901"
---
# <a name="deploy-static-rendered-nextjs-websites-on-azure-static-web-apps"></a>Déployer des sites web Next.js au rendu statique sur Azure Static Web Apps

Ce tutoriel permet d’apprendre à déployer un site web statique généré [Next.js](https://nextjs.org) sur [Azure Static Web Apps](overview.md). Pour plus d’informations sur les Next.js spécifiques, consultez le [fichier Lisez-moi du modèle de démarrage](https://github.com/staticwebdev/nextjs-starter#readme).

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/).
- Un compte GitHub. [Créez un compte gratuitement](https://github.com/join).
- [Node.js](https://nodejs.org) (déjà installé).

## <a name="set-up-a-nextjs-app"></a>Créer une application Next.js

Au lieu d’utiliser l’interface CLI Next.js pour créer une application, vous pouvez utiliser un référentiel de démarrage. Le référentiel de démarrage contient une application Next.js existante qui prend en charge les itinéraires dynamiques.

Pour commencer, créez un référentiel sous votre compte GitHub à partir d’un référentiel de modèles.

1. Accédez à [https://github.com/staticwebdev/nextjs-starter/generate](https://github.com/login?return_to=/staticwebdev/nextjs-starter/generate)
1. Nommez le référentiel **nextjs-starter**
1. Ensuite, clonez le nouveau référentiel sur votre ordinateur. Remplacez `<YOUR_GITHUB_ACCOUNT_NAME>` par le nom de votre compte.

    ```bash
    git clone http://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/nextjs-starter
    ```

1. Accédez à l’application Next.js qui vient d’être clonée :

   ```bash
   cd nextjs-starter
   ```

1. Installez des dépendances :

    ```bash
    npm install
    ```

1. Démarrez l’application Next.js dans l’environnement de développement :

    ```bash
    npm run dev
    ```

Accédez à `http://localhost:3000` pour ouvrir l’application, où le site web suivant devrait s’ouvrir dans votre navigateur favori :

:::image type="content" source="media/deploy-nextjs/start-nextjs-app.png" alt-text="Démarrer l’application Next.js":::

Lorsque vous sélectionnez une infrastructure ou une bibliothèque, vous devez voir une page détaillant des informations sur l’élément sélectionné :

:::image type="content" source="media/deploy-nextjs/start-nextjs-details.png" alt-text="Page de détails":::

## <a name="deploy-your-static-website"></a>Déployer votre site web statique

La procédure suivante vous indique comment lier votre application Azure Static Web Apps. Puis, une fois dans Azure, vous pourrez déployer l’application dans un environnement de production.

### <a name="create-a-static-app"></a>Créer une application statique

1. Accédez au [portail Azure](https://portal.azure.com).
1. Sélectionnez **Créer une ressource**.
1. Recherchez **Static Web Apps**.
1. Sélectionnez **Static Web Apps**.
1. Sélectionnez **Create** (Créer).
1. Sous l’onglet _Informations de base_, entrez les valeurs suivantes.

    | Propriété | Valeur |
    | --- | --- |
    | _Abonnement_ | Le nom de votre abonnement Azure. |
    | _Groupe de ressources_ | **my-nextjs-group**  |
    | _Nom_ | **my-nextjs-app** |
    | _Type de plan_ | **Gratuit** |
    | _Région de l’API Azure Functions et des environnements intermédiaires_ | Sélectionnez la région la plus proche de vous. |
    | _Source_ | **GitHub** |

1. Sélectionnez **Se connecter avec GitHub** et authentifiez-vous auprès de GitHub.

1. Entrez les valeurs GitHub suivantes.

    | Propriété | Valeur |
    | --- | --- |
    | _Organisation_ | Sélectionnez l’organisation GitHub appropriée. |
    | _Dépôt_ | Sélectionnez **nextjs-starter**. |
    | _Branche_ | Sélectionnez **principal**. |

1. Dans la section _Détails de build_, sélectionnez **Personnalisé** dans _Présélections de build_. Ajoutez les valeurs suivantes comme pour la configuration de build.

    | Propriété | Valeur |
    | --- | --- |
    | _Emplacement de l’application_ | Entrez **/** dans la zone. |
    | _Emplacement de l’API_ | Laissez cette boîte vide. |
    | _Emplacement de sortie_ | Entrez **sortie** dans la boîte. |

### <a name="review-and-create"></a>Examiner et créer

1. Pour vérifier que les informations sont correctes, sélectionnez le bouton **Vérifier + créer**.

1. Pour démarrer la création de l’application web statique App Service et le provisionnement d’une action GitHub pour le déploiement, sélectionnez **Créer**.

1. Une fois le déploiement terminé, sélectionnez **Accéder à la ressource**.

1. Dans la fenêtre _Vue d’ensemble_, cliquez sur le lien *URL* pour ouvrir l’application que vous avez déployée.

Si le site web n’est pas chargé immédiatement, la build est toujours en cours d’exécution. Une fois le workflow terminé, actualisez votre navigateur pour afficher votre application web.

Pour vérifier l’état du workflow Actions, accédez au tableau de bord Actions pour votre référentiel :

```url
https://github.com/<YOUR_GITHUB_USERNAME>/nextjs-starter/actions
```

Désormais, toutes les modifications apportées à la branche `main` démarrent une nouvelle build et un nouveau déploiement de votre site web.

### <a name="sync-changes"></a>Synchroniser les modifications

Une fois l’application créée, Azure Static Web Apps a créé un fichier GitHub Actions dans votre référentiel. Synchronisez avec le serveur en extrayant la dernière version de votre référentiel local.

Revenez au terminal et exécutez la commande suivante : `git pull origin main`.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne prévoyez pas de continuer à utiliser cette application, vous pouvez supprimer l’instance Azure Static Web Apps en effectuant les étapes suivantes :

1. Ouvrez le [portail Azure](https://portal.azure.com).
1. Recherchez **my-nextjs-group** dans la barre de recherche supérieure.
1. Sélectionnez le nom du groupe.
1. Sélectionnez le bouton **Supprimer**.
1. Sélectionnez **Oui** pour confirmer la suppression.

> [!div class="nextstepaction"]
> [Configurer un nom de domaine personnalisé](custom-domain.md)
