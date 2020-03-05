---
title: Azure Monitor pour réseaux (préversion)
description: Vue d’ensemble rapide d’Azure Monitor pour réseau, qui fournit une vue complète de l’intégrité et des métriques pour toutes les ressources réseau déployées sans aucune configuration.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/07/2019
ms.openlocfilehash: 0f5b2fbd13cb9658e255fde727e115df748aaed9
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77654866"
---
# <a name="azure-monitor-for-networks-preview"></a>Azure Monitor pour réseaux (préversion)
Azure Monitor pour réseau fournit une vue complète de l’intégrité et des métriques pour toutes les ressources réseau déployées sans aucune configuration. La fonctionnalité de recherche avancée aide à identifier les dépendances de ressources, par l’activation de scénarios tels que l’identification des ressources qui hébergent votre site web, en recherchant simplement le nom du site web hébergé.

La page **Vue d’ensemble** d’Azure Monitor pour réseaux vous permet de visualiser facilement l’inventaire de vos ressources réseau ainsi que les alertes et l’intégrité des ressources. Elle est divisée en quatre zones fonctionnelles clés :

- Recherche et filtrage
- Intégrité des ressources et métriques
- Alertes 
- Affichage des dépendances

![Page Vue d’ensemble](media/network-insights-overview/overview.png)

## <a name="search-and-filtering"></a>Recherche et filtrage
L’affichage des alertes et de l’intégrité des ressources peut être personnalisé à l’aide de filtres comme **Abonnement**, **Groupe de ressources** et **Type de ressource**. La zone de recherche permet d’effectuer des recherches dans les propriétés des ressources.

La zone de recherche peut être utilisée pour rechercher des ressources et des ressources associées. Par exemple, une adresse IP publique est associée à une passerelle d’application. La recherche du nom DNS des adresses IP publiques permet d’identifier l’adresse IP publique et la passerelle d’application associée.

![Recherche](media/network-insights-overview/search.png)


## <a name="resource-health-and-metric"></a>Intégrité des ressources et métriques
Chaque vignette représente un type de ressource, avec le nombre d’instances déployées sur tous les abonnements sélectionnés ainsi que l’état d’intégrité des ressources. Dans l’exemple ci-dessous, il existe 105 connexions ER et VPN déployées, dont 103 intègres et 2 non disponibles.

![Intégrité des ressources](media/network-insights-overview/resource-health.png)

Le fait de cliquer sur les deux connexions ER et VPN non disponibles lance un affichage de métriques. 

![Affichage des métriques](media/network-insights-overview/metric-view.png)

Vous pouvez cliquer sur chaque élément en mode grille. Cliquez sur l’icône d’intégrité pour être redirigé vers l’intégrité de ressource de la connexion concernée. Cliquez sur Alertes pour être redirigé vers la page des alertes et métriques associée à la connexion concernée. 

## <a name="alerts"></a>Alertes
La grille **Alertes** à droite fournit une vue de toutes les alertes générées pour les ressources sélectionnées pour l’ensemble des abonnements. Cliquez sur le nombre d’alertes pour accéder à la page détaillée des alertes.

## <a name="dependency-view"></a>Affichage des dépendances
L’affichage des **dépendances** permet de visualiser la façon dont la ressource est configurée. L’affichage des dépendances est uniquement pris en charge pour Application Gateway. Vous pouvez accéder à l’affichage des dépendances en cliquant sur le nom de la ressource Application Gateway en mode grille des métriques.

![Affichage Application Gateway](media/network-insights-overview/application-gateway.png)

L’affichage des **dépendances** pour Application Gateway fournit une vue simplifiée de la façon dont les adresses IP front-end sont connectées aux écouteurs, aux règles et au pool back-end. Les arêtes de connexion sont codées par couleur et fournissent des détails supplémentaires en fonction de l’intégrité du pool back-end. L’affichage fournit également une vue détaillée des métriques Application Gateway et des métriques pour tous les pools back-end associés telles que VMSS et les instances de machine virtuelle.

![Affichage des dépendances](media/network-insights-overview/dependency-view.png)

Le graphique de dépendance facilite la navigation vers les paramètres de configuration. Cliquez avec le bouton droit sur un pool back-end pour accéder à d’autres fonctionnalités. Par exemple, si le pool back-end est une machine virtuelle, vous pouvez accéder directement aux insights de machine virtuelle et à la fonctionnalité de résolution des problèmes de connexion Network Watcher pour identifier les problèmes de connectivité.

![Menu de l’affichage des dépendances](media/network-insights-overview/dependency-view-menu.png)

La barre de recherche et de filtrage de l’affichage des dépendances permet d’effectuer facilement des recherches dans le graphique. Par exemple, la recherche d’*AppGWTestRule* dans l’exemple ci-dessous réduit la vue graphique à tous les nœuds connectés via *AppGWTestRule*. 

![Exemple de recherche](media/network-insights-overview/search-example.png)

Différents filtres permettent de limiter l’accès à un chemin et à un état spécifiques. Par exemple, sélectionnez uniquement *Non sain* dans la liste déroulante **État d’intégrité** pour afficher toutes les arêtes où l’état est *Non sain*.

Cliquez sur **Afficher les métriques détaillées** afin de lancer un classeur préconfiguré avec des métriques détaillées pour la passerelle d’application, toutes les ressources du pool back-end et les adresses IP front-end. 

## <a name="next-steps"></a>Étapes suivantes 

- Découvrez la supervision réseau dans l’article [Présentation d’Azure Network Watcher](/azure/network-watcher/network-watcher-monitoring-overview).
