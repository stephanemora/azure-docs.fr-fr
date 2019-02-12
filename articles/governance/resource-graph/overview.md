---
title: Vue d’ensemble d’Azure Resource Graph
description: Azure Resource Graph est un service Azure qui permet d’exécuter des requêtes complexes sur des ressources à grande échelle.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/06/2019
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 6b3bad4e4619f8909f5c6d71111b4fad9ddb3098
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55813277"
---
# <a name="what-is-azure-resource-graph"></a>Qu’est-ce qu’Azure Resource Graph ?

Azure Resource Graph est un service Azure conçu pour étendre Gestion des ressources Azure. Il permet non seulement d’explorer de manière efficace et performante les ressources, mais aussi de lancer des requêtes à grande échelle sur tous les abonnements et groupes d’administration pour optimiser la gestion de votre environnement. Ces requêtes vous permettent d’exécuter les fonctionnalités suivantes :

- Interroger les ressources avec des fonctions complexes de filtrage, de regroupement et de tri en fonction des propriétés des ressources.
- Explorer de manière itérative les ressources selon les exigences de gouvernance et convertir l’expression résultante en définition de stratégie.
- Évaluer l’impact de l’application de stratégies dans un environnement cloud étendu.

À travers cette documentation, nous allons examiner en détail chacune de ces fonctionnalités.

> [!NOTE]
> Azure Resource Graph fait partie de la nouvelle expérience de navigation « Toutes les ressources » du portail Azure. Il est conçu pour aider les clients qui doivent gérer des environnements à grande échelle.

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Comment Resource Graph vient compléter Azure Resource Manager

Azure Resource Manager envoie actuellement les données à un cache de ressources limité qui propose plusieurs champs de ressource, à savoir le nom de la ressource, l’ID, le type, le groupe de ressources, les abonnements et l’emplacement. Auparavant, pour travailler avec d’autres propriétés de ressource vous deviez appeler chaque fournisseur de ressources et demander des détails sur les propriétés de chaque ressource.

Avec Azure Resource Graph, vous pouvez accéder aux propriétés retournées par les fournisseurs de ressources sans appeler chaque fournisseur.

## <a name="the-query-language"></a>Langage de requête

Maintenant que vous avez une meilleure idée de ce qu’est Azure Resource Graph, examinons comment construire des requêtes.

Il est important de comprendre que le langage de requête d’Azure Resource Graph est basé sur le [langage de requête de l’Explorateur de données Azure](../../data-explorer/data-explorer-overview.md).

Tout d’abord, pour obtenir des informations sur les opérations et fonctions qui peuvent être utilisées avec Azure Resource Graph, consultez le [langage de requête Resource Graph](./concepts/query-language.md). Pour parcourir des ressources, consultez [Explorer les ressources](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Autorisations dans Azure Resource Graph

Pour utiliser Resource Graph, vous devez disposer des droits appropriés pour le [contrôle d’accès en fonction du rôle](../../role-based-access-control/overview.md) (RBAC), avec au moins un accès en lecture aux ressources à interroger. Sans au moins une `read` autorisation pour l’objet Azure ou un groupe d’objets, les résultats ne seront pas renvoyés.

## <a name="throttling"></a>Limitation

Les requêtes vers Resource Graph sont limitées afin d’offrir une excellente expérience et un temps de réponse optimal à tous les clients. Si votre organisation souhaite utiliser l’API Resource Graph pour les requêtes fréquentes et à grande échelle, utilisez l’option « Commentaires » du portail à partir de la page Resource Graph. Veillez à fournir votre cas d’entreprise et sélectionnez la case à cocher « Microsoft peut vous contacter par e-mail à propos de vos commentaires » afin que l’équipe puisse vous contacter.

## <a name="running-your-first-query"></a>Exécution de votre première requête

Resource Graph prend en charge Azure CLI et Azure PowerShell. La requête est structurée de la même manière pour les deux langages. Découvrez comment activer Resource Graph dans [Azure CLI](first-query-azurecli.md#add-the-resource-graph-extension) et [Azure PowerShell](first-query-powershell.md#add-the-resource-graph-module).

## <a name="next-steps"></a>Étapes suivantes

- Exécuter votre première requête avec [Azure CLI](first-query-azurecli.md)
- Exécuter votre première requête avec [Azure PowerShell](first-query-powershell.md)
- Commencer avec des [requêtes de démarrage](./samples/starter.md)
- Aller plus loin avec des [requêtes avancées](./samples/advanced.md)