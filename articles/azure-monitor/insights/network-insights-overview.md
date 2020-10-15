---
title: Azure Monitor pour réseaux (préversion)
description: Vue d’ensemble rapide d’Azure Monitor pour réseau, qui fournit une vue complète de l’intégrité et des métriques pour toutes les ressources réseau déployées sans aucune configuration.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/24/2020
ms.openlocfilehash: 0d0e8c4806784cf9b00712ad1bf45bca958a7eb1
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91995360"
---
# <a name="azure-monitor-for-networks-preview"></a>Azure Monitor pour réseaux (préversion)
Azure Monitor pour réseau fournit une vue complète de l’[intégrité](../../service-health/resource-health-checks-resource-types.md) et des [métriques](../platform/metrics-supported.md) pour toutes les ressources réseau déployées sans aucune configuration.  Il permet également d’accéder à toutes les fonctionnalités de surveillance du réseau, comme le [Moniteur de connexion](../../network-watcher/connection-monitor-preview.md), la [journalisation des flux pour les groupes de sécurité réseau](../../network-watcher/network-watcher-nsg-flow-logging-overview.md), [Traffic Analytics](../../network-watcher/traffic-analytics.md) et d’autres fonctionnalités de [diagnostic](../../network-watcher/network-watcher-monitoring-overview.md#diagnostics) du réseau.

Azure Monitor pour les réseaux est structuré autour des composants clés de surveillance suivants :
- [Intégrité de réseau et métriques](#networkhealth)
- [Connectivité](#connectivity)
- [Trafic](#traffic)
- [Boîte à outils de diagnostic](#diagnostictoolkit)

## <a name="network-health-and-metrics"></a><a name="networkhealth"></a>Intégrité de réseau et métriques

La page **Vue d’ensemble** d’Azure Monitor pour réseaux vous permet de visualiser facilement l’inventaire de vos ressources réseau ainsi que les alertes et l’intégrité des ressources. Elle est divisée en quatre zones fonctionnelles principales : recherche et filtrage, Intégrité des ressources et métriques, Alertes et Affichage des dépendances

![Page Vue d’ensemble](media/network-insights-overview/overview.png)

### <a name="search-and-filtering"></a>Recherche et filtrage
L’affichage des alertes et de l’intégrité des ressources peut être personnalisé à l’aide de filtres comme **Abonnement**, **Groupe de ressources** et **Type de ressource**. La zone de recherche permet d’effectuer des recherches dans les propriétés des ressources.

La zone de recherche peut être utilisée pour rechercher des ressources et des ressources associées. Par exemple, une adresse IP publique est associée à une passerelle d’application. La recherche du nom DNS des adresses IP publiques permet d’identifier l’adresse IP publique et la passerelle d’application associée.

![Azure Monitor pour réseaux - Recherche](media/network-insights-overview/search.png)


### <a name="resource-health-and-metric"></a>Intégrité des ressources et métriques
Chaque vignette représente un type de ressource, avec le nombre d’instances déployées sur tous les abonnements sélectionnés ainsi que l’état d’intégrité des ressources. Dans l’exemple ci-dessous, il existe 45 connexions ER et VPN déployées, dont 44 intègres et 1 non disponible.

![Azure Monitor pour réseaux - Intégrité des ressources](media/network-insights-overview/resource-health.png)

Le fait de cliquer sur les connexions ER et VPN non disponibles lance un affichage de métriques. 

![Azure Monitor pour réseaux - Vue Métrique](media/network-insights-overview/metric-view.png)

Vous pouvez cliquer sur chaque élément en mode grille. Cliquez sur l’icône d’intégrité pour être redirigé vers l’intégrité de ressource de la connexion concernée. Cliquez sur Alertes pour être redirigé vers la page des alertes et métriques associée à la connexion concernée. 

### <a name="alerts"></a>Alertes
La grille **Alertes** à droite fournit une vue de toutes les alertes générées pour les ressources sélectionnées pour l’ensemble des abonnements. Cliquez sur le nombre d’alertes pour accéder à la page détaillée des alertes.

### <a name="dependency-view"></a>Affichage des dépendances
L’affichage des **dépendances** permet de visualiser la façon dont la ressource est configurée. L’affichage des dépendances est maintenant pris en charge pour Application Gateway, Virtual WAN et Load Balancer. Dans le cas d’Application Gateway par exemple, vous pouvez accéder à l’affichage des dépendances en cliquant sur le nom de la ressource Application Gateway en mode grille des métriques. Cela s’applique également à Virtual WAN et à Load Balancer.

![Azure Monitor pour réseaux - Vue Application Gateway](media/network-insights-overview/application-gateway.png)

L’affichage des **dépendances** pour Application Gateway fournit une vue simplifiée de la façon dont les adresses IP front-end sont connectées aux écouteurs, aux règles et au pool back-end. Les arêtes de connexion sont codées par couleur et fournissent des détails supplémentaires en fonction de l’intégrité du pool back-end. L’affichage fournit également une vue détaillée des métriques Application Gateway et des métriques pour tous les pools back-end associés telles que les groupes de machines virtuelles identiques et les instances de machine virtuelle.

![Azure Monitor pour réseaux - Vue Dépendances](media/network-insights-overview/dependency-view.png)

Le graphique de dépendance facilite la navigation vers les paramètres de configuration. Cliquez avec le bouton droit sur un pool back-end pour accéder à d’autres fonctionnalités. Par exemple, si le pool back-end est une machine virtuelle, vous pouvez accéder directement aux insights de machine virtuelle et à la fonctionnalité de résolution des problèmes de connexion Network Watcher pour identifier les problèmes de connectivité.

![Azure Monitor pour réseaux - Menu Affichage des dépendances](media/network-insights-overview/dependency-view-menu.png)

La barre de recherche et de filtrage de l’affichage des dépendances permet d’effectuer facilement des recherches dans le graphique. Par exemple, la recherche d’*AppGWTestRule* dans l’exemple ci-dessous réduit la vue graphique à tous les nœuds connectés via *AppGWTestRule*.

![Azure Monitor pour réseaux - Exemple de recherche](media/network-insights-overview/search-example.png)

Différents filtres permettent de limiter l’accès à un chemin et à un état spécifiques. Par exemple, sélectionnez uniquement *Non sain* dans la liste déroulante **État d’intégrité** pour afficher toutes les arêtes où l’état est *Non sain*.

Cliquez sur **Afficher les métriques détaillées** afin de lancer un classeur préconfiguré avec des métriques détaillées pour Application Gateway, toutes les ressources du pool back-end et les adresses IP front-end. 

## <a name="connectivity"></a><a name="connectivity"></a>Connectivité

L’onglet **Connectivité** offre un moyen facile de visualiser tous les tests configurés à l’aide du Moniteur de connexion et du [Moniteur de connexion (préversion)](../../network-watcher/connection-monitor-preview.md) pour le jeu d’abonnements sélectionné.

![Onglet Connectivité dans Azure Monitor pour réseaux](media/network-insights-overview/azure-monitor-for-networks-connectivity-tab.png)

Les tests sont regroupés par vignettes Sources et Destinations et affichent l’état d’accessibilité pour chaque test. Les paramètres accessibles offrent un accès facile pour configurer vos critères d’accessibilité en fonction des vérifications ayant échoué (%) et du RTT (ms). Une fois les valeurs définies, l’état de chaque test est mis à jour en fonction des critères de sélection.

![Tests de connectivité dans Azure Monitor pour réseaux](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png)

Cliquer sur n’importe quelle vignette source ou destination lance une vue métrique.

![Mesures de connectivité dans Azure Monitor pour réseaux](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png)


Vous pouvez cliquer sur chaque élément en mode grille. Cliquez sur l’icône **Accessibilité** pour rediriger vers la page du portail **Moniteur de connexion** pour afficher la topologie tronçon par tronçon et les problèmes de connectivité identifiés. Cliquez sur **Alertes** pour rediriger vers les alertes et **Pourcentage de vérifications ayant échoué/temps d’aller-retour** pour rediriger vers la page de métriques du Moniteur de connexion sélectionné.

La grille **Alertes** à droite fournit une vue de toutes les alertes générées pour les tests de connectivité configurés pour l’ensemble des abonnements. Cliquez sur le nombre d’alertes pour accéder à la page détaillée des alertes.

## <a name="traffic"></a><a name="traffic"></a>Trafic
L’onglet Trafic permet d’accéder à tous les groupes configurés pour les [journaux de flux NSG](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) et [Traffic Analytics](../../network-watcher/traffic-analytics.md) pour le jeu d’abonnements sélectionné et regroupés par emplacement. La fonctionnalité de recherche fournie sous cet onglet permet d’identifier les groupes configurés pour l’adresse IP recherchée. Vous pouvez rechercher n’importe quelle adresse IP dans votre environnement, et la vue régionale en mosaïque affiche tous les groupes, ainsi que les journaux de flux NSG et l’état de configuration de Traffic Analytics.

![Affichage du trafic dans Azure Monitor pour réseaux](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png)

Cliquez sur une vignette de région pour lancer un affichage en grille qui permet d’afficher et de configurer facilement les journaux de flux NSG et Traffic Analytics.  

![Affichage régional du trafic dans Azure Monitor pour réseaux](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png)

Vous pouvez cliquer sur chaque élément en mode grille. Cliquez sur l’état de la configuration pour modifier le journal de flux NSG et la configuration de Traffic Analytics. Cliquez sur Alertes pour rediriger vers les alertes de trafic configurées pour le groupe de sécurité réseau sélectionné. De même, vous pouvez accéder à la vue Traffic Analytics en cliquant sur l’espace de travail.  

La grille **Alertes** à droite fournit une vue de toutes les alertes d’espace de travail Traffic Analytics sur tous les abonnements. Cliquez sur le nombre d’alertes pour accéder à la page détaillée des alertes.

## <a name="diagnostic-toolkit"></a><a name="diagnostictoolkit"></a> Boîte à outils de diagnostic
La boîte à outils de diagnostic fournit un accès à toutes les fonctionnalités de diagnostic disponibles pour la résolution des problèmes du réseau. À partir de cette liste déroulante, vous pouvez accéder à des fonctionnalités telles que la [capture de paquets](../../network-watcher/network-watcher-packet-capture-overview.md), la [résolution des problèmes VPN](../../network-watcher/network-watcher-troubleshoot-overview.md), la [résolution des problèmes de connexion](../../network-watcher/network-watcher-connectivity-overview.md), le [tronçon suivant](../../network-watcher/network-watcher-next-hop-overview.md) et la [vérification des flux d’IP](../../network-watcher/network-watcher-ip-flow-verify-overview.md).

![Onglet Boîte à outils de diagnostic](media/network-insights-overview/azure-monitor-for-networks-diagnostic-toolkit.png)

## <a name="troubleshooting"></a>Dépannage 

Pour obtenir des conseils généraux sur la résolution des problèmes, reportez-vous à [l’article de résolution des problèmes](troubleshoot-workbooks.md) pour les insights basés sur les workbooks.

Cette section est destinée à vous aider à diagnostiquer et résoudre certains des problèmes communs que vous êtes susceptible de rencontrer lors de l’utilisation d’Azure Monitor pour réseaux. La liste ci-dessous permet d’identifier les informations pertinentes pour un problème spécifique.

### <a name="resolving-performance-issues-or-failures"></a>Résolution des problèmes de performances ou des échecs

Pour vous aider à résoudre les problèmes liés à la mise en réseau que vous identifiez avec Azure Monitor pour réseaux, consultez la documentation sur la résolution des problèmes de la ressource défectueuse. Les liens de dépannage pour les services les plus utilisés sont répertoriés ci-dessous.
* Réseau virtuel (VNet)
* Application Gateway
* Passerelle VPN
* ExpressRoute 
* Load Balancer 

### <a name="why-dont-i-see-the-resources-from-all-the-subscriptions-i-have-selected"></a>Pourquoi ne puis-je pas voir les ressources de tous les abonnements que j’ai sélectionnés

Insights réseau ne peut afficher que les ressources de 5 abonnements à la fois. 

### <a name="i-want-to-make-changes-or-add-additional-visualizations-to-network-insights-how-do-i-do-so"></a>Je souhaite apporter des modifications ou ajouter des visualisations supplémentaires aux insights réseau, comment faire ?

Pour apporter des modifications, sélectionnez « Mode d’édition » pour modifier le classeur. Vous pouvez ensuite enregistrer votre travail sous un nouveau classeur lié à un abonnement et à un groupe de ressources désignés.

### <a name="what-is-the-time-grain-once-we-pin-any-part-of-the-workbooks"></a>Quel est le fragment de temps après avoir épinglé une partie des classeurs ?

Nous utilisons le fragment de temps « Auto » ; par conséquent, il dépend de l’intervalle de temps sélectionné.

### <a name="what-is-the-time-range-when-any-part-of-the-workbook-is-pinned"></a>Quel est l’intervalle de temps quand une partie du classeur est épinglée ?

L’intervalle de temps dépendra des paramètres du tableau de bord.

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-the-network-insights"></a>Que se passe-t-il si je souhaite voir d’autres données ou créer mes propres visualisations ? Comment puis-je apporter des modifications aux insights réseau ?

Vous pouvez modifier le classeur que vous voyez dans les affichages de volet latéral et de métriques détaillées, en utilisant le mode d’édition, puis enregistrer votre travail sous un nouveau classeur qui contiendra toutes vos nouvelles modifications.


## <a name="next-steps"></a>Étapes suivantes

- Découvrez la supervision réseau dans l’article [Présentation d’Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md).
- Découvrez les scénarios que les classeurs sont conçus pour prendre en charge, comment créer et personnaliser des rapports existants, et bien plus encore en consultant la rubrique [Créer des rapports interactifs avec les classeurs Azure Monitor](../platform/workbooks-overview.md).