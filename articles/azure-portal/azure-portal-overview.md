---
title: Vue d’ensemble du portail Azure | Microsoft Docs
description: Découvrez comment naviguer dans le portail Azure et de l’utiliser pour gérer des services
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 05/24/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: de04b461d8e2d2dce9a88052a86fbe5d163fafec
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244195"
---
# <a name="azure-portal-overview"></a>Présentation du portail Azure

Cet article présente le portail Azure, identifie les éléments de la page du portail et vous aide à vous familiariser avec l’expérience de gestion via le portail Azure.

## <a name="what-is-the-azure-portal"></a>Qu’est le portail Azure ?

Le portail Azure est une console unifiée basée sur le web qui fournit une alternative aux outils de ligne de commande. Avec le portail Azure, vous pouvez gérer votre abonnement Azure à l’aide d’une interface utilisateur graphique. Vous pouvez créer, gérer et surveiller tout ce que de simples applications web pour les déploiements de cloud complexes, créer des tableaux de bord personnalisés pour une vue organisée des ressources et configurer les options d’accessibilité pour une expérience optimale.

Le portail Azure est conçu pour la résilience et une disponibilité continue. Il a une présence dans chaque centre de données Azure rendant résistant aux défaillances de centre de données individuel et évite également les ralentissements du réseau s’ils sont proches des utilisateurs. Le portail Azure met à jour en permanence et ne requiert aucun temps d’arrêt pour les activités de maintenance.

## <a name="azure-home"></a>Page d’accueil Azure

Tant que nouvel abonné aux services Azure, la première chose que vous voyez après avoir [connectez-vous au portail](https://portal.azure.com) est **page d’accueil Azure**. Cette compile page ressources qui vous aident à tirer le meilleur parti de votre abonnement Azure. Nous avons inclus des liens pour libérer des cours en ligne, de documentation, de services principaux, et des sites utiles pour rester en cours et la gestion des changer pour votre organisation. Pour accéder rapidement et facilement au travail en cours, nous montrons également une liste de vos ressources récemment consultés. Vous ne pouvez pas personnaliser cette page, mais vous pouvez choisir si vous voulez afficher **page d’accueil Azure** ou **tableau de bord Azure** en tant que vue par défaut. La première fois que vous vous connecterez, il existe une invite de commandes en haut de la page où vous pouvez enregistrer vos préférences.

![Sélecteur de vue de capture d’écran montrant par défaut](./media/azure-portal-overview/azure-portal-default-view.png)

## <a name="azure-dashboard"></a>Tableau de bord Azure

Tableaux de bord fournissent une vue ciblée des ressources dans votre abonnement qui vous intéressent le plus à vous. Nous avons donné un tableau de bord par défaut pour vous aider à démarrer. Vous pouvez personnaliser ce tableau de bord pour afficher les ressources que vous utilisez plus dans une vue unique. Toute modification apportée à la vue par défaut affecte uniquement votre expérience. Toutefois, créer des tableaux de bord pour votre usage personnel ou publier vos tableaux de bord personnalisés et les partager avec d’autres utilisateurs de votre organisation. Pour plus d’informations, consultez [créer et partager des tableaux de bord dans le portail Azure](../azure-portal/azure-portal-dashboards.md).

## <a name="getting-around-the-portal"></a>Pour contourner le portail

Il est utile de comprendre la disposition de portail de base et comment interagir avec lui. Ici, nous allons présenter les composants de l’interface utilisateur et la terminologie que nous utilisons pour donner des instructions. Pour une visite guidée plus détaillée du portail, consultez la leçon cours [accédez au portail](https://docs.microsoft.com/learn/modules/tour-azure-portal/3-navigate-the-portal).

La barre latérale de portail Azure et l’en-tête de page sont les éléments globaux qui sont toujours présents. Ces fonctionnalités persistantes sont le « shell » pour l’interface utilisateur associé à chaque service ou fonctionnalité et l’en-tête fournit l’accès à des contrôles globaux. La page de configuration (parfois appelée un « panneau ») pour une ressource peut également avoir un volet de gauche pour vous aider à déplacer entre les fonctionnalités.

La figure ci-dessous présente les éléments de base du portail Azure, chacun d'entre eux est décrites dans le tableau suivant.

![Capture d’écran montrant l’affichage plein écran du portail et une clé pour les éléments d’interface utilisateur](./media/azure-portal-overview/azure-portal-fullscreen-map.png)

|Clé|Description
|:---:|---|
|1|En-tête de page. Apparaît en haut de chaque page du portail et contient les éléments globaux.|
|2| Recherche globale. Utilisez la barre de recherche pour trouver rapidement une ressource spécifique, un service ou la documentation.|
|3|Contrôles globaux. Comme tous les éléments globaux, ces fonctionnalités conserver entre le portail et incluent : Cloud Shell, filtre d’abonnement, notifications, paramètres du portail, aide et support et envoyez-nous vos commentaires.|
|4|Votre compte. Afficher les informations relatives à votre compte, changer les annuaires, se déconnecter ou connectez-vous avec un autre compte.|
|5.|Barre latérale. La barre latérale est un élément global qui vous aide à naviguer entre les services. La barre latérale peut être réduite pour donner le focus plus dans le volet de travail.|
|6.|Contrôle maître pour créer une nouvelle ressource dans l’abonnement actuel. Rechercher ou parcourir la place de marché Azure pour le type de ressource que vous souhaitez créer.|
|7|Votre liste de favoris. Ajouter ou supprimer des favoris à partir de la **tous les services** page.|
|8|Volet gauche. De nombreux services incluent un menu de volet de gauche pour vous aider à gérer le service.|
|9|Barre de commandes. Les contrôles sur la barre de commandes sont contextuelles pour votre focus actuel.|
|10|Barre de navigation. Vous pouvez utiliser les liens de navigation pour vous déplacer dans un niveau dans votre flux de travail.|
|11|Volet de travail.  Affiche les détails sur la ressource qui est actuellement active.|

## <a name="get-started-with-services"></a>Bien démarrer avec les services

Si vous êtes un nouvel abonné, vous devrez créer une ressource avant rien à gérer. Sélectionnez **+ créer une ressource** pour afficher les services disponibles dans la place de marché Azure. Vous trouverez des applications et des services provenant de centaines de fournisseurs ici, tous certifiés pour s’exécuter sur Azure.

Nous les préremplies vos favoris dans la barre latérale avec des liens vers des services couramment utilisés.  Pour afficher tous les services disponibles, sélectionnez **tous les services** à partir de la barre latérale.

> [!TIP]
> Le moyen le plus rapide pour rechercher une ressource, un service ou une documentation consiste à utiliser *recherche* dans l’en-tête global. Utilisez les liens de navigation pour revenir aux pages précédentes.
>

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur l’emplacement d’exécution du portail Azure [pris en charge des navigateurs et appareils](../azure-portal/azure-portal-supported-browsers-devices.md)

* Restez connecté à la volée avec [application mobile Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
