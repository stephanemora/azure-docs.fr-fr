---
title: Vue d’ensemble d’Azure Resource Graph
description: Azure Resource Graph est un service Azure qui permet d’exécuter des requêtes complexes sur des ressources à grande échelle.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: d68183f4d0a928ac72f3f73ea5225ad174820cb7
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162098"
---
# <a name="what-is-azure-resource-graph"></a>Qu’est-ce qu’Azure Resource Graph ?

Azure Resource Graph est un service Azure conçu pour étendre Gestion des ressources Azure. Il permet non seulement d’explorer de manière efficace et performante les ressources, mais aussi de lancer des requêtes à grande échelle sur tous les abonnements et groupes d’administration pour optimiser la gestion de votre environnement. Ces requêtes vous permettent d’effectuer les tâches suivantes :

- Interroger les ressources avec des fonctions complexes de filtrage, de regroupement et de tri en fonction des propriétés des ressources.
- Explorer de manière itérative les ressources selon les exigences de gouvernance et convertir l’expression résultante en définition de stratégie.
- Évaluer l’impact de l’application de stratégies dans un environnement cloud étendu.

Dans cette documentation, nous allons examiner chacune de ces tâches en détail.

> [!NOTE]
> Azure Resource Graph fait partie de la nouvelle expérience de navigation « Toutes les ressources » du portail Azure. Il est conçu pour aider les clients qui doivent gérer des environnements à grande échelle.

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Comment Resource Graph vient compléter Azure Resource Manager

Azure Resource Manager envoie actuellement les données à un cache de ressources limité qui expose plusieurs champs de ressource, à savoir le nom de la ressource, l’ID, le type, le groupe de ressources, les abonnements et l’emplacement. Aujourd’hui, si vous souhaitez travailler avec d’autres propriétés de ressource, vous devez appeler chaque fournisseur de ressources et demander des détails sur les propriétés de chaque ressource.

Avec Azure Resource Graph, vous pouvez accéder aux propriétés retournées par les fournisseurs de ressources sans appeler chaque fournisseur.

## <a name="the-query-language"></a>Langage de requête

Maintenant que vous avez une meilleure idée de ce qu’est Azure Resource Graph, examinons comment construire des requêtes.

Il est important de comprendre que le langage de requête d’Azure Resource Graph est basé sur le [langage de requête de l’Explorateur de données Azure](../../data-explorer/data-explorer-overview.md).

Tout d’abord, pour obtenir des informations sur les opérations et fonctions qui peuvent être utilisées avec Azure Resource Graph, consultez le [langage de requête Resource Graph](./concepts/query-language.md). Pour parcourir des ressources, consultez [Explorer les ressources](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Autorisations dans Azure Resource Graph

Pour utiliser Resource Graph, vous devez disposer d’autorisations accordées par le biais du [contrôle d’accès en fonction du rôle](../../role-based-access-control/overview.md) (RBAC), avec au moins un accès en lecture aux ressources à interroger. Tout groupe d’administration, abonnement, groupe de ressources ou ressource individuelle pour lequel vous ne disposez pas d’autorisations `read` ne sera pas retourné dans les résultats d’une requête Resource Graph.

## <a name="running-your-first-query"></a>Exécution de votre première requête

Resource Graph prend en charge Azure CLI et Azure PowerShell. Le composant de requête est structuré de la même façon quel que soit le langage utilisé. La prise en charge d’Azure Resource Graph n’étant pas encore disponible par défaut dans ces SDK, vous devez charger une extension ou un module pour fournir les commandes nécessaires.
Découvrez comment activer Resource Graph dans [Azure CLI](first-query-azurecli.md#add-the-resource-graph-extension) et [Azure PowerShell](first-query-powershell.md#add-the-resource-graph-module).

## <a name="next-steps"></a>Étapes suivantes

- Exécuter votre première requête avec [Azure CLI](first-query-azurecli.md)
- Exécuter votre première requête avec [Azure PowerShell](first-query-powershell.md)
- Commencer avec des [requêtes de démarrage](./samples/starter.md)
- Aller plus loin avec des [requêtes avancées](./samples/advanced.md)