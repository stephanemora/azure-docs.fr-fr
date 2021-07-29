---
title: Log Analytics Workspace Insights
description: 'Présentation de Log Analytics Workspace Insights : ingestion, utilisation, intégrité, agents et bien plus encore'
services: azure-monitor
ms.topic: conceptual
author: noakup
ms.author: noakuper
ms.date: 05/06/2021
ms.openlocfilehash: 450f4f8d93261412db4963579f8b8525c2028f46
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110482117"
---
# <a name="log-analytics-workspace-insights-preview"></a>Log Analytics Workspace Insights (préversion)

Log Analytics Workspace Insights (préversion) offre une surveillance complète de vos espaces de travail grâce à une vue unifiée de l’utilisation de votre espace de travail, des performances, de l’intégrité, de l’agent, des requêtes et du journal des modifications. Cet article vous aidera à comprendre comment intégrer et utiliser Log Analytics Workspace Insights (préversion).

## <a name="overview-your-log-analytics-workspaces"></a>Superviser vos espaces de travail Log Analytics

Lors de l’accès à Log Analytics Workspace Insights par le biais du Azure Monitor Insights, le point de vue « à l’échelle » s’affiche. Ici, vous pouvez voir comment vos espaces de travail sont répartis dans le monde entier, passer en revue leurs informations de rétention, de limitation et de licence (code de couleur) et choisir un espace de travail pour afficher ses Insights.


:::image type="content" source="media/log-analytics-workspace-insights-overview/at-scale.png" alt-text="Capture d’écran montrant la liste des espaces de travail de Log Analytics" lightbox="media/log-analytics-workspace-insights-overview/at-scale-expanded.png":::


Pour lancer Log Analytics Workspace Insights à l’échelle, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Sélectionnez **Surveillance** dans le volet gauche du Portail Azure, puis dans la section Hub Insights, sélectionnez **Log Analytics Workspace Insights (préversion)** .

## <a name="view-insights-for-a-log-analytics-workspace"></a>Voir les Insights pour un espace de travail Log Analytics

Le lancement d’Insights dans le contexte d’un espace de travail spécifique affiche des données enrichies et des analyses des performances, de l’utilisation, de l’intégrité, des agents, des requêtes et du journal des modifications de l’espace de travail.


:::image type="content" source="media/log-analytics-workspace-insights-overview/at-resource.png" alt-text="Capture d’écran montrant la vue générale de Log Analytics Workspace Insights" lightbox="media/log-analytics-workspace-insights-overview/at-resource.png":::

Pour accéder à Log Analytics Workspace Insights :

1. Ouvrez Log Analytics Workspace Insights à partir d’Azure Monitor (expliqué ci-dessus)

2. Sélectionnez un espace de travail à analyser

ou 

1. Dans le Portail Azure, sélectionnez **Espaces de travail Log Analytics**

2. Choisir un espace de travail Log Analytics

3. Sélectionnez **Insights** dans le menu de l’espace de travail (sous Surveillance)

Les données sont organisées dans des onglets et l’intervalle de temps en haut (24 heures par défaut) s’applique à tous les onglets. Certains graphiques et tableaux utilisent une période différente, comme indiqué dans leurs titres.


### <a name="overview-tab"></a>Onglet Overview

Sous l’onglet **Vue d’ensemble**, vous pouvez voir :

* Paramètres et statistiques principaux
    - Le volume d’ingestion mensuel de l’espace de travail
    - Combien de machines ont envoyé des pulsations, c’est-à-dire des machines connectées à cet espace de travail (dans l’intervalle de temps sélectionné)
    - Ordinateurs qui n’ont pas envoyé de pulsations au cours de la dernière heure (dans l’intervalle de temps sélectionné)
    - Le paramètre de durée de conservation des données
    - La limite quotidienne définie et la quantité de données déjà ingérées au cours du dernier jour

* Top 5 des tables : graphiques analysant les 5 tables les plus ingérées au cours du mois dernier.
    - Volume de données ingérées dans chaque table
    - L’ingestion quotidienne à chacune d’entre elles-pour afficher visuellement des pics ou des chutes
    - Anomalies d’ingestion : liste des pics identifiés et des chutes dans l’ingestion de ces tables


### <a name="usage-tab"></a>Onglet Utilisation

#### <a name="usage-dashboard"></a>Tableau de bord de l’utilisation

Cet onglet fournit des informations sur l’utilisation de l’espace de travail. Le sous-onglet tableau de bord affiche les données d’ingestion par tables et les 5 tables les plus ingérées par défaut dans l’intervalle de temps sélectionné (les mêmes tables affichées dans la page vue d’ensemble). Vous pouvez choisir les tables à afficher dans la liste déroulante Tables de l’espace de travail. 

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-usage.png" alt-text="Capture d’écran de l’onglet d’utilisation de l’espace de travail" lightbox="media/log-analytics-workspace-insights-overview/workspace-usage.png":::

* Grille principale : ici, vous pouvez voir les tables regroupées par solutions et les informations sur chaque table.
    - La quantité de données qui a été ingérée (pendant l’intervalle de temps sélectionné)
    - Pourcentage pris par cette table par rapport au volume total d’ingestion (pendant la période sélectionnée). Cela permet d’identifier les tables qui affectent le plus l’ingestion. Dans la capture d’écran ci-dessous, vous pouvez voir qu’AzureDiagnostics et ContainerLog représentent plus de 2 tiers (64%) des données ingérées dans cet espace de travail.
    - Quand était la dernière mise à jour des statistiques d’utilisation de chaque table, les statistiques d’utilisation doivent être actualisées toutes les heures normalement. Étant donné que l’actualisation des statistiques d’utilisation est une opération de service interne, un délai d’actualisation de ces données n’est indiqué que si vous savez que vous devez interpréter les données correctement. Aucune action n’est nécessaire (en tant qu’utilisateur).
    - Facturable : indique les tables qui sont facturées et celles qui sont gratuites.

* Détails spécifiques à la table

    En bas de la page, vous pouvez consulter des informations détaillées sur la table sélectionnée dans la grille principale.
    - Volume d’ingestion : quantité de données qui ont été ingérées dans la table à partir de chaque ressource et mode de réexécution dans le temps. Les ressources qui ingèrent plus de 30 % du volume total envoyé à cette table sont signalées par un signe d’avertissement, que vous devez noter.
    - Latence de l’ingestion : nombre de fois où l’ingestion du temps a été effectué, analysé pour le 50e, le 90e ou le 95e centile des demandes envoyées à cette table. Le graphique supérieur de cette zone représente le temps d’ingestion total des requêtes (pour le centile sélectionné) de bout en bout, à partir du moment où l’événement s’est produit et jusqu’à ce qu’il soit ingéré dans l’espace de travail.
    Le graphique ci-dessous montre la latence de l’agent (le temps nécessaire à l’agent pour envoyer le journal à l’espace de travail) et celui du pipeline (le temps nécessaire au service pour traiter les données et les envoyer à l’espace de travail).
    :::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-usage-ingestion-latency.png" alt-text="Capture d’écran du sous-onglet de latence de l’ingestion de l’utilisation de l’espace de travail" lightbox="media/log-analytics-workspace-insights-overview/workspace-usage-ingestion-latency.png":::

#### <a name="additional-usage-queries"></a>Requêtes d’utilisation supplémentaires

Le sous-onglet de requêtes supplémentaires expose les requêtes qui s’exécutent sur toutes les tables de l’espace de travail (au lieu de s’appuyer sur les métadonnées d’utilisation, actualisées toutes les heures). Étant donné que leurs requêtes sont beaucoup plus étendues et moins efficaces, elles ne sont pas exécutées automatiquement. Toutefois, elles peuvent exposer des informations intéressantes sur les ressources qui envoient la plupart des journaux à l’espace de travail et éventuellement sur la facturation.

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-usage-additional-queries.png" alt-text="Capture d’écran de l’onglet de requêtes supplémentaires de l’utilisation de l’espace de travail" lightbox="media/log-analytics-workspace-insights-overview/workspace-usage-additional-queries.png":::

L’une de ces requêtes est « Quelles ressources Azure envoient la plupart des journaux à cet espace de travail » (en affichant les 50 premiers).
Dans notre espace de travail de démonstration, vous pouvez constater clairement que 3 clusters Kuberbetes envoient beaucoup plus de données que toutes les autres ressources, et une seule d’entre elles charge l’espace de travail.

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-usage-additional-query-run.png" alt-text="Capture d’écran de l’onglet requêtes supplémentaires d’utilisation de l’espace de travail avec les résultats d’une requête supplémentaire" lightbox="media/log-analytics-workspace-insights-overview/workspace-usage-additional-query-run.png":::


### <a name="health-tab"></a>Onglet d’intégrité

Cet onglet affiche l’état d’intégrité de l’espace de travail et le moment où il a été signalé pour la dernière fois, ainsi que les erreurs et avertissements opérationnels (récupérés à partir de la table _LogOperation).

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-health.png" alt-text="Capture d’écran de l’onglet intégrité de l’espace de travail" lightbox="media/log-analytics-workspace-insights-overview/workspace-health.png":::

### <a name="agents-tab"></a>Onglet Agents

Cet onglet fournit des informations sur les agents qui envoient des journaux à cet espace de travail.
:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-agents.png" alt-text="Capture d’écran de l’onglet agents de l’espace de travail" lightbox="media/log-analytics-workspace-insights-overview/workspace-agents.png":::

* Erreurs d’opération et avertissements : il s’agit d’erreurs et d’avertissements liés spécifiquement aux agents. Ils sont regroupés par le titre d’erreur/d’avertissement pour vous aider à obtenir une vue plus claire des différents problèmes qui peuvent se produire, mais qui peuvent être développés pour afficher l’heure exacte et les ressources auxquelles ils font référence. Notez également que vous pouvez cliquer sur « exécuter la requête dans les journaux » pour interroger la table _LogOperation via l’expérience des journaux, voir les données brutes et l’analyser plus en détails.
* Agents de l’espace de travail : il s’agit des agents qui ont envoyé des journaux à l’espace de travail pendant l’intervalle de temps sélectionné. Vous pouvez voir les types d’agents (direct, passerelle, SCOM ou serveurs d'administration SCOM) et l’état d’intégrité. Les agents marqués comme sains ne fonctionnent pas nécessairement correctement : ils ont seulement indiqué qu’ils ont envoyé une pulsation au cours de la dernière heure. Un état d’intégrité plus détaillé est détaillé dans la grille ci-dessous.
* Activité des agents : cette grille affiche des informations sur tous les agents, les agents sains ou non sains. Ici, « sain » indique seulement que l’agent envoie une pulsation au cours de la dernière heure. Pour mieux comprendre son état, passez en revue la tendance affichée dans la grille : elle indique le nombre de pulsations envoyées par cet agent dans le temps. L’état d’intégrité réel peut être déduit uniquement si vous savez comment la ressource surveillée fonctionne, par exemple, si un ordinateur est intentionnellement arrêté à des moments particuliers, vous pouvez vous attendre à ce que les pulsations de l’agent s’affichent de façon intermittente, dans un modèle de correspondance.


### <a name="query-audit-tab"></a>Onglet d’audit de requête

L’audit de requête crée des journaux sur l’exécution des requêtes sur l’espace de travail. Si cette option est activée, ces données sont très utiles pour comprendre et améliorer les performances, l’efficacité et la charge des requêtes. Pour activer l’audit de requête sur votre espace de travail ou en savoir plus, consultez [Requêtes d’audit dans les journaux d’Azure Monitor](../logs/query-audit.md).

#### <a name="performance"></a>Performances 
Cet onglet affiche :
* Durée de la requête : 95e centile et du 50e centile (médian) de la durée en ms, au fil du temps.
* Nombre de lignes retournées : 95e centile et 50e centile (médian) du nombre de lignes, au fil du temps.
* Le volume de données traitées : 95e centile, 50e centile et le total des données traitées dans toutes les demandes, au fil du temps.
* Codes de réponse : distribution des codes de réponse à toutes les requêtes dans l’intervalle de temps sélectionné.

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-query-audit-performance.png" alt-text="Capture d’écran de l’onglet audit de requête, sous-onglet performances" lightbox="media/log-analytics-workspace-insights-overview/workspace-query-audit-performance.png":::

#### <a name="slow-and-inefficient-queries"></a>Requêtes lentes et inefficaces 
Cet onglet affiche deux grilles pour vous aider à identifier les requêtes lentes et inefficaces que vous souhaiterez peut-être repenser. Ces requêtes ne doivent pas être utilisées dans des tableaux de bord ou des alertes, car elles créeront une charge chronique inutile sur votre espace de travail.
* La plupart des requêtes gourmandes en ressources : les 10 requêtes nécessitant le plus d’UC, ainsi que le volume de données traitées (Ko), l’intervalle de temps et le texte de chaque requête.
* Requêtes les plus lentes : les 10 requêtes les plus lentes, ainsi que l’intervalle de temps et le texte de chaque requête.

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-query-audit-slow-queries.png" alt-text="Capture d’écran de l’onglet audit de requête, sous-onglet requêtes lentes" lightbox="media/log-analytics-workspace-insights-overview/workspace-query-audit-slow-queries.png":::

#### <a name="query-users"></a>Requêtes d’utilisateurs 
Cet onglet affiche l’activité des utilisateurs par rapport à cet espace de travail :
* Requêtes par utilisateur : nombre de requêtes exécutées par chaque utilisateur dans l’intervalle de temps sélectionné
* Utilisateurs limités : utilisateurs qui ont exécuté des requêtes qui ont été limitées (en raison de l’interrogation excessive de l’espace de travail)

### <a name="change-log-tab"></a>Onglet de journal des modifications

Cet onglet affiche les modifications de configuration apportées à l’espace de travail au cours des 90 derniers jours (quel que soit l’intervalle de temps sélectionné), ainsi que la personne qui les a exécutées.
Il a pour but de vous aider à surveiller qui modifie les paramètres d’espace de travail importants, tels que le plafonnement des données ou la licence d’espace de travail.

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-change-log.png" alt-text="Capture d’écran de l’onglet journal des modifications de l’espace de travail" lightbox="media/log-analytics-workspace-insights-overview/workspace-change-log.png":::


## <a name="next-steps"></a>Étapes suivantes

Découvrez les scénarios que les classeurs sont conçus pour prendre en charge, comment créer et personnaliser des rapports existants, et bien plus encore en consultant la rubrique [Créer des rapports interactifs avec les classeurs Azure Monitor](../visualize/workbooks-overview.md).
