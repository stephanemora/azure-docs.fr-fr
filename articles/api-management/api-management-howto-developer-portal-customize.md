---
title: Accéder au portail managé des développeurs et le personnaliser – Gestion des API Azure | Microsoft Docs
description: Découvrez comment utiliser la version managée du portail des développeurs dans la Gestion des API.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/05/2020
ms.author: apimpm
ms.openlocfilehash: 3ceb8fd154e8ad533f4bf6bc9eb0ec3900749f8b
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92075363"
---
# <a name="access-and-customize-developer-portal"></a>Accéder au portail des développeurs et le personnaliser

Le portail des développeurs est un site web généré automatiquement et entièrement personnalisable avec la documentation de vos API. C’est là que les consommateurs d’API peuvent découvrir vos API, apprendre à les utiliser et y demander l’accès.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Accéder à la version managée du portail des développeurs
> * Parcourir son interface d’administration
> * Personnaliser le contenu
> * Publier les modifications
> * Afficher le portail publié

Pour plus d’informations sur le portail des développeurs, voir la [Vue d’ensemble du portail des développeurs Gestion des API Azure](api-management-howto-developer-portal.md).

![Portail des développeurs Gestion des API – Mode Administrateur](media/api-management-howto-developer-portal-customize/cover.png)

## <a name="prerequisites"></a>Prérequis

- Suivez ce guide de démarrage rapide : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md)
- Importez et publiez une instance Gestion des API Azure. Pour plus d’informations, voir [Importer et publier](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>Accéder au portail en tant qu’administrateur

Suivez les étapes ci-dessous pour accéder à la version managée du portail.

1. Accédez à votre instance du service Gestion des API dans le Portail Microsoft Azure.
1. Cliquez sur le bouton **Portail des développeurs** dans la barre de navigation supérieure. Un nouvel onglet de navigateur s’ouvre, en affichant une version d’administration du portail.

## <a name="understand-the-portals-administrative-interface"></a>Comprendre l’interface d’administration du portail

### <a name="default-content"></a>Contenu par défaut 

Si vous accédez au portail pour la première fois, le contenu par défaut est automatiquement configuré en arrière-plan. Il a été conçu pour présenter les fonctionnalités du portail et réduire le degré de personnalisation nécessaire du portail. Pour plus d’informations sur le contenu du portail, voir la [Vue d’ensemble du portail des développeurs Gestion des API Azure](api-management-howto-developer-portal.md).

### <a name="visual-editor"></a>Éditeur visuel

Il est possible de personnaliser le contenu du portail avec l’éditeur visuel. Les sections de menu de gauche permettent de créer ou de modifier des pages, des médias, des dispositions, des menus, des styles ou des paramètres de site web. Les éléments de menu du bas permettent de basculer entre les fenêtres d’affichage (par exemple, mobile ou ordinateur de bureau), d’afficher les éléments du portail visibles par les utilisateurs authentifiés et anonymes, et d’enregistrer ou d’annuler des actions.

Vous pouvez ajouter des lignes à une page en cliquant sur une icône bleue accompagnée d’un signe plus. Pour ajouter des widgets (par exemple, du texte, des images ou une liste d’API), appuyez sur une icône grise accompagnée d’un signe plus. Vous pouvez réorganiser les éléments d’une page avec l’interaction de type glisser-déplacer. 

### <a name="layouts-and-pages"></a>Dispositions et pages

![Pages et dispositions](media/api-management-howto-developer-portal-customize/pages-layouts.png)

Les dispositions définissent la façon dont s’affichent les pages. Par exemple, dans le contenu par défaut, il existe deux dispositions : l’une s’applique à la page d’accueil et l’autre à toutes les autres pages.

Pour appliquer une disposition à une page, faites correspondre son modèle d’URL à son URL. Par exemple, la disposition associée au modèle d’URL `/wiki/*` s’appliquera à chacune des pages dont l’URL comporte le segment `/wiki/` : `/wiki/getting-started`, `/wiki/styles`, etc.

Dans l’image ci-dessus, le contenu appartenant à la disposition est marqué en bleu, tandis que la page est marquée en rouge. Les sections de menu sont marquées de la même manière.

### <a name="styling-guide"></a>Guide de style

![Guide de style](media/api-management-howto-developer-portal-customize/styling-guide.png)

Le guide de style est un panneau destiné aux concepteurs. Il permet de contrôler et de styliser tous les éléments visuels du portail. Le style est hiérarchique : de nombreux éléments héritent des propriétés d’autres éléments. Par exemple, les éléments de type bouton utilisent des couleurs du texte et de l’arrière-plan. Pour modifier la couleur d’un bouton, il faut modifier la variante de couleur d’origine.

Pour modifier une variante, cliquez dessus et sélectionnez l’icône de pinceau qui apparaît au-dessus. Une fois que vous avez effectué les modifications dans la fenêtre contextuelle, fermez-la.

### <a name="save-button"></a>Bouton Enregistrer

![Bouton Enregistrer](media/api-management-howto-developer-portal-customize/save-button.png)

Chaque fois que vous apportez une modification au portail, vous devez l’enregistrer manuellement en appuyant sur le bouton **Enregistrer** dans le menu du bas. Le contenu modifié est alors automatiquement chargé dans votre service Gestion des API.

## <a name="customize-the-portals-content"></a>Personnaliser le contenu du portail

Avant de mettre le portail à la disposition des visiteurs, il est préférable de personnaliser le contenu généré automatiquement. Sont recommandées les modifications suivantes : dispositions, styles et contenu de la page d’accueil.

> [!NOTE]
> Pour des raisons d’intégration, les pages suivantes ne sont ni supprimables ni déplaçables sous une autre URL : `/404`, `/500`, `/captcha`, `/change-password`, `/config.json`, `/confirm/invitation`, `/confirm-v2/identities/basic/signup`, `/confirm-v2/password`, `/internal-status-0123456789abcdef`, `/publish`, `/signin`, `/signin-sso` et `/signup`.

### <a name="home-page"></a>page d'accueil

La page **d’Accueil** par défaut comporte du contenu factice. Vous pouvez soit supprimer la totalité des sections comportant du contenu, soit conserver la structure et ajuster les éléments un par un. Remplacez le texte et les images générés par les vôtres en veillant à ce que les liens pointent vers les emplacements souhaités.

### <a name="layouts"></a>Mises en forme

Remplacez le logo généré automatiquement dans la barre de navigation par votre propre image.

### <a name="styling"></a>Style

Même si vous n’avez pas besoin de modifier les styles, vous pouvez éventuellement ajuster des éléments spécifiques. Par exemple, choisissez la couleur principale en fonction de la couleur de votre marque.

### <a name="customization-example"></a>Exemple de personnalisation

Dans la vidéo ci-dessous, nous expliquons comment modifier le contenu du portail, personnaliser l’aspect du site web et publier les modifications.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a><a name="publish"> </a>Publier le portail

Pour que votre portail et ses dernières modifications soient accessibles aux visiteurs, vous devez le publier.

1. Veillez à enregistrer vos modifications en cliquant sur l’icône **Enregistrer**.
1. Cliquez sur **Publier le site web** dans la section **Opérations** du menu. Cette opération peut prendre quelques minutes.  
    ![Publier le portail](media/api-management-howto-developer-portal-customize/publish-portal.png)

> [!NOTE]
> Le portail doit être republié après que la configuration du service Gestion des API ait été modifiée, comme l'attribution d'un domaine personnalisé, la mise à jour des fournisseurs d'identité, le paramétrage de la délégation, la spécification des conditions de connexion et de produit, et plus encore.

## <a name="visit-the-published-portal"></a>Consulter le portail publié

Une fois publié, le portail est accessible à la même URL que le panneau d’administration, par exemple `https://contoso-api.developer.azure-api.net`. Affichez-le dans une session de navigateur distincte (mode de navigation incognito ou privée) en tant que visiteur externe.

## <a name="apply-the-cors-policy-on-apis"></a>Appliquer la stratégie CORS sur les API

Vous devez activer CORS (cross-origin resource sharing) sur vos API pour permettre aux visiteurs de votre portail de tester les API via la console interactive intégrée. Pour plus d’informations, consultez [cet article de la documentation](api-management-howto-developer-portal.md#cors).

## <a name="next-steps"></a>Étapes suivantes
- [Optimiser et réduire les dépenses liées au cloud](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

Pour plus d’informations sur le portail des développeurs :

- [Vue d’ensemble du portail des développeurs Gestion des API Azure](api-management-howto-developer-portal.md)