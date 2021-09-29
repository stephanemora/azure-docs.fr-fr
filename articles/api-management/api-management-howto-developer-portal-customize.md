---
title: Tutoriel – Accéder au portail des développeurs et le personnaliser – Gestion des API Azure | Microsoft Docs
description: Suivez ce tutoriel pour savoir comment personnaliser le portail des développeurs Gestion des API, un site web généré automatiquement et entièrement personnalisable à l’aide de la documentation de vos API.
services: api-management
author: dlepow
ms.service: api-management
ms.topic: tutorial
ms.date: 08/31/2021
ms.author: danlep
ms.openlocfilehash: 9220bcfc218acaaa073090fd1b05b4648510d096
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128583023"
---
# <a name="tutorial-access-and-customize-the-developer-portal"></a>Tutoriel : Accéder et personnaliser le portail des développeurs

Le *portail des développeurs* est un site web généré automatiquement et entièrement personnalisable avec la documentation de vos API. C’est là que les consommateurs d’API peuvent découvrir vos API, apprendre à les utiliser et y demander l’accès.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Accéder à la version managée du portail des développeurs
> * Parcourir son interface d’administration
> * Personnaliser le contenu
> * Publier les modifications
> * Afficher le portail publié

Pour plus d’informations sur le portail des développeurs, voir la [Vue d’ensemble du portail des développeurs Gestion des API Azure](api-management-howto-developer-portal.md).

:::image type="content" source="media/api-management-howto-developer-portal-customize/cover.png" alt-text="Portail des développeurs Gestion des API – Mode Administrateur" border="false":::

## <a name="prerequisites"></a>Prérequis

- Suivez ce guide de démarrage rapide : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md)
- Importez et publiez une instance Gestion des API Azure. Pour plus d’informations, voir [Importer et publier](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>Accéder au portail en tant qu’administrateur

Suivez les étapes ci-dessous pour accéder à la version managée du portail.

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre instance APIM.
1. Sélectionnez le bouton **Portail des développeurs** dans la barre de navigation supérieure. Un nouvel onglet de navigateur s’ouvre, en affichant une version d’administration du portail.


## <a name="developer-portal-architectural-concepts"></a>Concepts architecturaux du portail des développeurs

Les composants du portail se divisent en deux catégories logiques : le *code* et le *contenu*.

### <a name="code"></a>Code

Le code, qui est conservé dans le [dépôt GitHub](https://github.com/Azure/api-management-developer-portal) du portail des développeurs Gestion des API comprend les éléments suivants :

- **Widgets** : représentent des éléments visuels et associent du code HTML, du code JavaScript, des possibilités d’application de styles, des paramètres et un mappage du contenu. (exemples : image, paragraphe de texte, formulaire, liste d’API, etc.) ;
- **Définitions de styles** : spécifient comment des styles peuvent être appliqués aux widgets
- **Moteur** : génère des pages web statiques à partir du contenu du portail, écrit en JavaScript
- **Éditeur visuel** : offre une expérience de personnalisation et de création dans le navigateur

### <a name="content"></a>Contenu

Le contenu est divisé en deux sous-catégories : le *contenu du portail* et le *contenu de la Gestion des API*.

Le *contenu du portail*, propre au portail, comprend les éléments suivants :

- **Pages** : par exemples, la page de destination, les tutoriels sur les API, les billets de blog
- **Contenus multimédias** : images, animations et autres contenus basés sur des fichiers
- **Dispositions** : modèles qui sont mis en correspondance avec une URL et qui définissent le mode d’affichage des pages
- **Styles** : valeurs des définitions de styles, comme les polices, les couleurs, les bordures
- **Paramètres** : configurations comme favicon, les métadonnées de site web

    Le contenu du portail, à l’exception des contenus multimédias, est exprimé sous la forme de documents JSON.

Le *contenu Gestion des API* comprend des entités telles que les API, les Opérations, les Produits et les Abonnements.
## <a name="understand-the-portals-administrative-interface"></a>Comprendre l’interface d’administration du portail

### <a name="default-content"></a>Contenu par défaut 

Si vous accédez au portail pour la première fois, le contenu par défaut est automatiquement provisionné en arrière-plan. Il a été conçu pour présenter les fonctionnalités du portail et réduire les personnalisations nécessaires à la particularisation de votre portail. Pour plus d’informations sur le contenu du portail, voir la [Vue d’ensemble du portail des développeurs Gestion des API Azure](api-management-howto-developer-portal.md).

### <a name="visual-editor"></a>Éditeur visuel

Il est possible de personnaliser le contenu du portail avec l’éditeur visuel. 
* Les sections de menu de gauche permettent de créer ou de modifier des pages, des médias, des dispositions, des menus, des styles ou des paramètres de site web. 
* Les éléments de menu du bas permettent de basculer entre les fenêtres d’affichage (par exemple, mobile ou ordinateur de bureau), d’afficher les éléments du portail visibles par les utilisateurs authentifiés et anonymes, et d’enregistrer ou d’annuler des actions.
* Ajoutez des lignes à une page en cliquant sur une icône bleue dotée d’un signe plus. 
* Pour ajouter des widgets (par exemple, du texte, des images ou une liste d’API), appuyez sur une icône grise accompagnée d’un signe plus.
* Réorganisez les éléments d’une page avec l’interaction de type glisser-déplacer. 

### <a name="layouts-and-pages"></a>Dispositions et pages

:::image type="content" source="media/api-management-howto-developer-portal-customize/pages-layouts.png" alt-text="Pages et dispositions" border="false":::

Les dispositions définissent la façon dont s’affichent les pages. Par exemple, dans le contenu par défaut, il existe deux dispositions : l’une s’applique à la page d’accueil et l’autre à toutes les autres pages.

Pour appliquer une disposition à une page, faites correspondre son modèle d’URL à son URL. Par exemple, une disposition associée au modèle d’URL `/wiki/*` s’appliquera à chacune des pages dont l’URL comporte le segment `/wiki/` : `/wiki/getting-started`, `/wiki/styles`, etc.

Dans l’image précédente, le contenu appartenant à la disposition est marqué en bleu, tandis que la page est marquée en rouge. Les sections de menu sont marquées de la même manière.

### <a name="styling-guide"></a>Guide de style

:::image type="content" source="media/api-management-howto-developer-portal-customize/styling-guide.png" alt-text="Guide de style" border="false":::

Le guide de style est un panneau destiné aux concepteurs. Il permet de contrôler et de styliser tous les éléments visuels du portail. Le style est hiérarchique : de nombreux éléments héritent des propriétés d’autres éléments. Par exemple, les éléments de type bouton utilisent des couleurs du texte et de l’arrière-plan. Pour modifier la couleur d’un bouton, il faut modifier la variante de couleur d’origine.

Pour modifier une variante, sélectionnez-la, puis sélectionnez l’icône de crayon qui apparaît au-dessus. Après avoir effectué les modifications dans la fenêtre contextuelle, fermez-la.

### <a name="save-button"></a>Bouton Enregistrer

:::image type="content" source="media/api-management-howto-developer-portal-customize/save-button.png" alt-text="Bouton Enregistrer" border="false":::

Chaque fois que vous apportez une modification au portail, vous devez l’enregistrer manuellement en sélectionnant le bouton **Enregistrer** dans le menu du bas, ou en appuyant sur [Ctrl]+[S]. Le contenu modifié est alors automatiquement chargé dans votre service Gestion des API.

## <a name="customize-the-portals-content"></a>Personnaliser le contenu du portail

Avant de mettre le portail à la disposition des visiteurs, il est préférable de personnaliser le contenu généré automatiquement. Sont recommandées les modifications suivantes : dispositions, styles et contenu de la page d’accueil.

> [!NOTE]
> Pour des raisons d’intégration, les pages suivantes ne sont ni supprimables ni déplaçables sous une autre URL : `/404`, `/500`, `/captcha`, `/change-password`, `/config.json`, `/confirm/invitation`, `/confirm-v2/identities/basic/signup`, `/confirm-v2/password`, `/internal-status-0123456789abcdef`, `/publish`, `/signin`, `/signin-sso` et `/signup`.

### <a name="home-page"></a>page d'accueil

La page d’**Accueil** par défaut comporte du contenu d’espace réservé. Vous pouvez soit supprimer des sections entières comportant ce contenu, soit conserver la structure et ajuster les éléments un par un. Remplacez le texte et les images générés par les vôtres en veillant à ce que les liens pointent vers les emplacements souhaités. Vous pouvez modifier la structure et le contenu de la page d’accueil de la façon suivante :
* Glisser-déplacer des éléments de page vers l’emplacement souhaité sur le site.
* Sélection des éléments de texte et d’en-tête pour modifier et mettre en forme le contenu. 
* La vérification de vos boutons pointe vers les emplacements appropriés.

### <a name="layouts"></a>Mises en forme

Remplacez le logo généré automatiquement dans la barre de navigation par votre propre image.

1. Dans le portail des développeurs, sélectionnez le logo par défaut **Contoso** en haut à gauche de la barre de navigation. 
1. Sélectionnez l’icône **Éditer**. 
1. Sous la section **Principal**, sélectionnez **Source**.
1. Dans la fenêtre contextuelle **Média**, sélectionnez soit :
    * une image déjà téléchargée dans votre bibliothèque, soit
    * **Charger un fichier** pour charger un nouveau fichier d’image à utiliser, soit
    * Sélectionnez **Aucun** pour renoncer à utiliser un logo.
1. Le logo est mis à jour en temps réel.
1. Cliquez en dehors des fenêtres contextuelles pour quitter la bibliothèque multimédia.
1. Cliquez sur **Save**.

### <a name="styling"></a>Style

Même si vous n’avez pas besoin de modifier les styles, vous pouvez éventuellement ajuster des éléments spécifiques. Par exemple, choisissez la couleur principale en fonction de la couleur de votre marque. Il existe deux méthodes pour le faire :

#### <a name="overall-site-style"></a>Style de site général

1. Dans le portail des développeurs, sélectionnez l’icône **Styles** dans la barre d’outils de gauche.
1. Sous la section **Couleurs**, sélectionnez l’élément du style de couleur que vous voulez modifier.
1. Cliquez sur l’icône **Modifier** pour cet élément de style.
1. Sélectionnez la couleur dans le sélecteur de couleurs ou entrez le code de couleur hexadécimal.
1. Ajoutez et nommez un autre élément de couleur en cliquant sur **Ajouter une couleur**.  
1. Cliquez sur **Save**.

#### <a name="container-style"></a>Style de conteneur

1. Sur la page principale du portail des développeurs, sélectionnez l’arrière-plan du conteneur.
1. Sélectionnez l’icône **Modifier**.
1. Dans la fenêtre contextuelle, définissez :
    * l’arrière-plan à effacer, une image, une couleur spécifique ou à un dégradé
    * la taille du conteneur, la marge et marge intérieure.
    * la position et la hauteur du conteneur.
1. Cliquez en dehors des fenêtres contextuelles pour quitter les paramètres du conteneur.
1. Cliquez sur **Save**.

### <a name="customization-example"></a>Exemple de personnalisation

Dans la vidéo suivante, nous expliquons comment modifier le contenu du portail, personnaliser l’aspect du site web et publier les modifications.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a><a name="publish"></a>Publier le portail

Pour que votre portail et ses dernières modifications soient accessibles aux visiteurs, vous devez le *publier*. Vous pouvez publier le portail dans l’interface d’administration du portail ou à partir du portail Azure.

### <a name="publish-from-the-administrative-interface"></a>Publier à partir de l’interface d’administration

1. Veillez à enregistrer vos modifications en sélectionnant l’icône **Enregistrer**.
1. Dans la section **Opérations** du menu, sélectionnez **Publier le site web**. Cette opération peut prendre quelques minutes.  

    :::image type="content" source="media/api-management-howto-developer-portal-customize/publish-portal.png" alt-text="Publier le portail" border="false":::

### <a name="publish-from-the-azure-portal"></a>Publier à partir du portail Azure

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre instance APIM.
1. Dans le menu de gauche, sous **Portail des développeurs**, sélectionnez **Vue d’ensemble du portail**.
1. Dans la fenêtre **Vue d’ensemble du portail**, sélectionnez **Publier**.

    :::image type="content" source="media/api-management-howto-developer-portal-customize/pubish-portal-azure-portal.png" alt-text="Publier le portail depuis le portail Azure":::

> [!NOTE]
> Le portail doit être publié de nouveau après des modifications de configuration du service Gestion des API. Par exemple, vous publiez de nouveau le portail après l’attribution d’un domaine personnalisé, la mise à jour des fournisseurs d’identité, la définition de la délégation ou la spécification de la connexion et des conditions d’utilisation du produit.


## <a name="visit-the-published-portal"></a>Consulter le portail publié

Une fois publié, le portail est accessible à la même URL que le panneau d’administration, par exemple `https://contoso-api.developer.azure-api.net`. Affichez-le dans une session de navigateur distincte (par le biais du mode de navigation incognito ou privée) en tant que visiteur externe.

## <a name="apply-the-cors-policy-on-apis"></a>Appliquer la stratégie CORS sur les API

Pour permettre aux visiteurs de votre portail de tester les API via la console interactive intégrée, activez CORS (cross-origin resource sharing) sur vos API. Pour plus d’informations, consultez les [Questions fréquentes du portail des développeurs Gestion des API Azure](developer-portal-faq.md#cors).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le portail des développeurs :

- [Vue d’ensemble du portail des développeurs Gestion des API Azure](api-management-howto-developer-portal.md)
- [Migrer vers le nouveau portail des développeurs](developer-portal-deprecated-migration.md) à partir du portail hérité déprécié.