---
title: Afficher plusieurs ressources dans Azure Metrics Explorer
description: Découvrez comment visualiser plusieurs ressources dans Azure Metrics Explorer.
author: ritaroloff
services: azure-monitor
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: riroloff
ms.openlocfilehash: 85fa252f999407d5cd7f75e954554e3b14357071
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037317"
---
# <a name="view-multiple-resources-in-the-azure-metrics-explorer"></a>Afficher plusieurs ressources dans Azure Metrics Explorer

Le sélecteur d’étendue de ressource vous permet d’afficher les mesures sur plusieurs ressources qui se trouvent dans le même abonnement et la même région. Cet article explique comment afficher plusieurs ressources à l’aide de la fonctionnalité Azure Metrics Explorer d’Azure Monitor. 

## <a name="select-a-resource"></a>Sélectionner une ressource 

Sélectionnez **Métriques** dans le menu **Azure Monitor** ou dans la section **Supervision** du menu d’une ressource. Choisissez ensuite **Sélectionner une étendue** pour ouvrir le sélecteur d’étendue. 

Utilisez le sélecteur d’étendue pour sélectionner les ressources dont vous souhaitez afficher les métriques. L’étendue doit être remplie si vous avez ouvert Azure Metrics Explorer à partir du menu d’une ressource. 

![Capture d’écran montrant comment ouvrir le sélecteur d’étendue des ressources.](./media/metrics-dynamic-scope/019.png)

## <a name="select-multiple-resources"></a>Sélectionner plusieurs ressources 

Certains types de ressources peuvent interroger des métriques sur plusieurs ressources. Les métriques doivent se trouver dans le même abonnement et au même emplacement. Recherchez ces types de ressources en haut du menu **Types de ressources**.

![Capture d’écran montrant un menu de ressources compatibles avec plusieurs ressources.](./media/metrics-dynamic-scope/020.png)

> [!WARNING] 
> Vous devez disposer de l’autorisation Lecteur d’analyse au niveau de l’abonnement pour visualiser les métriques sur plusieurs ressources ou groupes de ressources, ou sur un abonnement. Pour plus d’informations, consultez [Attribuer des rôles Azure en utilisant le portail Azure](../../role-based-access-control/role-assignments-portal.md).

Pour visualiser les métriques sur plusieurs ressources, commencez par sélectionner plusieurs ressources dans le sélecteur d’étendue des ressources. 

![Capture d’écran montrant comment sélectionner plusieurs ressources.](./media/metrics-dynamic-scope/021.png)

> [!NOTE]
> Les ressources que vous sélectionnez doivent se trouver dans les mêmes type de ressource, emplacement et abonnement. Les ressources qui ne correspondent pas à ces critères ne sont pas sélectionnables. 

Lorsque vous avez terminé, choisissez **Appliquer** pour enregistrer vos sélections. 

## <a name="select-a-resource-group-or-subscription"></a>Sélectionner un groupe de ressources ou un abonnement 

> [!WARNING]
> Vous devez disposer de l’autorisation Lecteur d’analyse au niveau de l’abonnement pour visualiser les métriques sur plusieurs ressources ou groupes de ressources, ou sur un abonnement. 

Pour les types qui sont compatibles avec plusieurs ressources, vous pouvez interroger les métriques sur un abonnement ou plusieurs groupes de ressources. Commencez par sélectionner un abonnement ou un ou plusieurs groupes de ressources : 

![Capture d’écran montrant comment interroger sur plusieurs groupes de ressources.](./media/metrics-dynamic-scope/022.png)

Sélectionnez un type de ressource et un emplacement. 

![Capture d’écran montrant les groupes de ressources sélectionnés.](./media/metrics-dynamic-scope/023.png)

Vous pouvez développer les étendues sélectionnées pour vérifier les ressources auxquelles vos sélections s’appliquent.

![Capture d’écran montrant les ressources sélectionnées au sein des groupes.](./media/metrics-dynamic-scope/024.png)

Une fois les étendues sélectionnées, choisissez **OK**. 

## <a name="split-and-filter-by-resource-group-or-resources"></a>Fractionner et filtrer par groupe de ressources ou ressources

Après avoir effectué le traçage de vos ressources, vous pouvez utiliser un fractionnement et un filtrage pour obtenir plus d’insights sur vos données. 

La division vous permet de comparer les différents segments de la métrique. Par exemple, quand tracez une métrique pour plusieurs ressources, vous pouvez choisir d’**Appliquer un fractionnement** afin de fractionner par ID de ressource ou groupe de ressources. Le fractionnement vous permet de comparer une métrique unique sur plusieurs ressources ou groupes de ressources.  

Par exemple, le graphique suivant montre le pourcentage d’UC sur neuf machines virtuelles. Lorsque vous fractionnez par ID de ressource, vous voyez en quoi le pourcentage d’UC diffère par machine virtuelle. 

![Capture d’écran montrant comment vous utiliser un fractionnement pour voir le pourcentage d’UC des machines virtuelles.](./media/metrics-dynamic-scope/026.png)

En plus du fractionnement, vous pouvez utiliser un filtrage pour afficher uniquement les groupes de ressources que vous voulez voir.  Par exemple, pour afficher le pourcentage d’utilisation d’UC des machines virtuelles d’un groupe de ressources spécifique, vous pouvez choisir d’**Ajouter un filtre** pour filtrer par groupe de ressources. 

Dans cet exemple, nous filtrons sur TailspinToysDemo. Ici, le filtre supprime les métriques associées aux ressources dans TailspinToys. 

![Capture d’écran montrant comment filtrer par groupe de ressources.](./media/metrics-dynamic-scope/027.png)

## <a name="pin-multiple-resource-charts"></a>Épingler des graphiques de ressources multiples 

Les graphiques de ressources multiples permettant de visualiser les métriques de plusieurs groupes de ressources et abonnements requièrent que l’utilisateur dispose de l’autorisation *Lecteur de surveillance* au niveau de l’abonnement. Assurez-vous que tous les utilisateurs des tableaux de bord auxquels vous épinglez des graphiques de ressources multiples disposent d’autorisations suffisantes. Pour plus d’informations, consultez [Attribuer des rôles Azure en utilisant le portail Azure](../../role-based-access-control/role-assignments-portal.md).

Pour épingler votre graphique de ressources multiples à un tableau de bord, consultez [Épinglage à des tableaux de bord](../essentials/metrics-charts.md#pinning-to-dashboards). 

## <a name="next-steps"></a>Étapes suivantes

* [Résoudre des problèmes de Metrics Explorer](../essentials/metrics-troubleshoot.md)
* [Consulter la liste des métriques disponibles pour les services Azure](./metrics-supported.md)
* [Consulter des exemples de graphiques configurés](../essentials/metric-chart-samples.md)