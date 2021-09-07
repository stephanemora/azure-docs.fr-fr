---
title: Présentation du portail Azure
description: Le portail Azure est une interface utilisateur graphique que vous pouvez utiliser pour gérer vos services Azure. Découvrez comment explorer et rechercher des ressources dans le portail Azure.
keywords: portail
ms.date: 08/30/2021
ms.topic: overview
ms.openlocfilehash: 32ca1f0032ab5ced2157bb73ccf5c3f40df86707
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224467"
---
# <a name="azure-portal-overview"></a>Présentation du portail Azure

Cet article présente le portail Azure, identifie les éléments des pages du portail et vous aide à vous familiariser avec l’expérience de gestion sur le portail Azure.

## <a name="what-is-the-azure-portal"></a>Qu’est-ce que le portail Azure ?

Le portail Azure est une console web unifiée qui offre une alternative aux outils en ligne de commande. Avec le portail Azure, vous pouvez gérer votre abonnement Azure via une interface graphique utilisateur. Vous pouvez créer, gérer et superviser tout ce que vous voulez, de simples applications web à des déploiements cloud complexes. Créez des tableaux de bord personnalisés pour une vue organisée des ressources. Configurez les options d’accessibilité pour une expérience optimale.

Le portail Azure est conçu pour assurer résilience et disponibilité continue. Il dispose d’une présence dans chaque centre de données Azure. Cette configuration rend le portail Azure résistant aux défaillances individuelles de centres de données et évite les ralentissements du réseau en étant proche des utilisateurs. Le portail Azure est mis à jour en permanence et les activités de maintenance ne nécessitent aucun temps d’arrêt.

## <a name="azure-portal-menu"></a>Menu du portail Azure

Vous pouvez [choisir le mode par défaut pour le menu du portail](set-preferences.md#set-menu-behavior). Il peut être ancré ou agir comme un panneau volant.

Lorsque le menu du portail est en mode volant, il est masqué jusqu’à ce que vous en ayez besoin. Sélectionnez l’icône de menu pour ouvrir ou fermer le menu.

:::image type="content" source="media/azure-portal-overview/azure-portal-overview-portal-menu-flyout.png" alt-text="Capture d’écran du menu du portail Azure en mode volant.":::

Si vous choisissez le mode ancré pour le menu du portail, il sera toujours visible. Vous pouvez réduire le menu pour libérer davantage d’espace de travail.

:::image type="content" source="media/azure-portal-overview/azure-portal-overview-portal-menu-expandcollapse.png" alt-text="Capture d’écran du menu du portail Azure en mode ancré.":::

## <a name="azure-home"></a>Accueil Azure

En tant que nouvel abonné aux services Azure, la première chose que vous voyez après vous être [connecté au portail](https://portal.azure.com) est la page **Accueil Azure**. Cette page compile les ressources qui vous aideront à tirer le meilleur parti de votre abonnement Azure. Nous incluons des liens vers des cours en ligne gratuits, de la documentation, des services essentiels et des sites utiles pour rester à jour et gérer les changements pour votre organisation. Pour un accès simple et rapide au travail en cours, nous montrons également la liste des ressources consultées en dernier.

Vous ne pouvez pas personnaliser la page d’accueil, mais vous pouvez choisir de voir la page **Accueil** ou **Tableau de bord** comme vue par défaut. La première fois que vous vous connectez, une invite en haut de la page vous permet d’enregistrer votre préférence. Vous pouvez [changer la sélection de la page de démarrage à tout moment dans les **paramètres du portail**](set-preferences.md#startup-page).

:::image type="content" source="media/azure-portal-overview/azure-portal-overview-portal-settings-menu-home.png" alt-text="Capture d’écran montrant les options de la page de démarrage dans le portail Azure.":::

## <a name="azure-dashboard"></a>Tableau de bord Azure

Les tableaux de bord fournissent une vue ciblée des ressources de votre abonnement qui vous intéressent le plus. Nous vous fournissons un tableau de bord par défaut pour vous aider à démarrer. Vous pouvez personnaliser ce tableau de bord pour avoir dans une même vue les ressources que vous utilisez fréquemment. Toutes les modifications que vous apportez à la vue par défaut affectent uniquement votre expérience. Toutefois, vous pouvez créer des tableaux de bord supplémentaires pour votre usage personnel, publier vos tableaux de bord personnalisés et les partager avec d’autres utilisateurs de votre organisation. Pour plus d’informations, consultez [Créer et partager des tableaux de bord dans le portail Azure](../azure-portal/azure-portal-dashboards.md).

Comme indiqué plus haut, vous pouvez [définir votre page de démarrage sur Tableau de bord](set-preferences.md#startup-page) si vous souhaitez voir le dernier [tableau de bord](azure-portal-dashboards.md) utilisé quand vous vous connectez au portail Azure.

## <a name="getting-around-the-portal"></a>Visite guidée du portail

Il est utile de comprendre la disposition de base du portail et comment interagir avec lui. Ici, nous allons présenter les composants de l’interface utilisateur et certains éléments de la terminologie que nous utilisons pour donner des instructions. Pour une visite guidée plus détaillée du portail, consultez la leçon [Parcourir le portail](/learn/modules/tour-azure-portal/3-navigate-the-portal).

Le menu et l’en-tête de page du portail Azure sont des éléments globaux qui sont toujours présents. Ces fonctionnalités persistantes sont « l’interpréteur de commandes » de l’interface utilisateur associée à chaque service ou fonctionnalité, et l’en-tête permet d’accéder à des contrôles globaux. La page de configuration (parfois appelée « panneau ») d’une ressource peut également avoir un menu de ressources qui vous aidera à vous déplacer entre les fonctionnalités.

La figure ci-dessous étiquette les éléments de base du portail Azure, qui sont décrits individuellement dans le tableau suivant. Cet exemple se concentre sur une machine virtuelle, mais les mêmes éléments s’appliquent quel que soit le type de ressource ou de service que vous utilisez.

:::image type="content" source="media/azure-portal-overview/azure-portal-overview-portal-callouts.png" alt-text="Capture d’écran montrant une vue plein écran du portail et des clés d’éléments d’interface utilisateur.":::

:::image type="content" source="media/azure-portal-overview/azure-portal-overview-portal-menu-callouts.png" alt-text="Capture d’écran montrant le menu du portail développé et des clés d’éléments d’interface utilisateur.":::

|Clé|Description
|:---:|---|
|1|En-tête de la page. Apparaît en haut de chaque page du portail et contient les éléments globaux.|
|2|Recherche globale. Utilisez la barre de recherche pour trouver rapidement une ressource spécifique, un service ou de la documentation.|
|3|Contrôles globaux. Comme tous les éléments globaux, ces fonctionnalités sont persistantes sur le portail et incluent : Cloud Shell, filtre d’abonnement, notifications, paramètres du portail, aide et support, et envoyez-nous vos commentaires.|
|4|Votre compte. Consultez les informations sur votre compte, changez d’annuaire, déconnectez-vous ou connectez-vous avec un autre compte.|
|5|Menu du portail Azure. Cet élément global peut vous aider à naviguer entre les services. Parfois appelé barre latérale. (Les éléments 9 et 10 de cette liste apparaissent dans ce menu.)|
|6|Le menu de ressources. De nombreux services incluent un menu dans le menu de ressources pour vous aider à gérer le service. Cet élément peut être désigné sous le nom de volet gauche. Ici apparaissent les commandes contextuelles liées à vos actions.|
|7|Barre de commandes. Ces contrôles sont contextuels et dépendent de vos actions.|
|8|Volet de travail. Affiche des détails sur la ressource à laquelle vous vous intéressez.|
|9|Barre de navigation. Vous pouvez utiliser les liens de la barre de navigation pour remonter d’un niveau dans votre workflow.|
|10|Contrôle principal pour créer une ressource dans l’abonnement actuel. Développez ou ouvrez le menu du portail Azure pour rechercher **+ Créer une ressource**. Cette option se trouve également dans la page d’**accueil**. Ensuite, recherchez ou parcourez la Place de marché Azure pour trouver le type de ressource que vous souhaitez créer.|
|11|Votre liste de favoris dans le menu du portail Azure. Pour apprendre à personnaliser cette liste, consultez [Ajouter, supprimer et trier des favoris](../azure-portal/azure-portal-add-remove-sort-favorites.md).|

## <a name="get-started-with-services"></a>Prise en main des services

Si vous êtes un nouvel abonné, vous devez créer une ressource avant de pouvoir la gérer. Sélectionnez **+ Créer une ressource** pour voir les services disponibles dans la Place de marché Azure. Vous y trouverez des centaines d’applications et de services provenant de nombreux fournisseurs, tous certifiés pour s’exécuter sur Azure.

Nous préremplissons vos [favoris](../azure-portal/azure-portal-add-remove-sort-favorites.md) dans la barre latérale avec des liens vers des services couramment utilisés.  Pour voir tous les services disponibles, sélectionnez **Tous les services** dans la barre latérale.

> [!TIP]
> La façon la plus rapide de rechercher une ressource, un service ou de la documentation consiste à utiliser *Recherche* dans l’en-tête global.

Regardez cette vidéo pour avoir une démonstration de l’utilisation de la recherche globale dans le portail Azure.

> [!VIDEO https://www.youtube.com/embed/nZ7WwTZcQbo]

[Guide pratique pour utiliser la recherche globale dans le portail Azure](https://www.youtube.com/watch?v=nZ7WwTZcQbo)

## <a name="next-steps"></a>Étapes suivantes

* Découvrez plus en détail où exécuter le portail Azure dans [Navigateurs et appareils pris en charge](../azure-portal/azure-portal-supported-browsers-devices.md).
* Restez connecté en déplacement avec [Azure mobile app](https://azure.microsoft.com/features/azure-portal/mobile-app/).
* Intégrez et configurez votre environnement cloud avec le [Centre de démarrage rapide Azure](../azure-portal/azure-portal-quickstart-center.md).