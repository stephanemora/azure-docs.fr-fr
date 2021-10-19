---
title: Solution Infosys de traçabilité de la chaîne logistique utilisant l’API Graph Azure Cosmos DB
description: La solution graphique de traçabilité de la chaîne logistique implémentée par Infosys utilise l’API Azure Cosmos DB Gremlin et d’autres services Azure. Elle offre une capacité de suivi et de traçabilité de la chaîne logistique mondiale pour les produits finis.
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/07/2021
author: manishmsfte
ms.author: mansha
ms.openlocfilehash: 4ec236a57e9c8f24625d22f4af3f39299d118804
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716515"
---
# <a name="supply-chain-traceability-solution-using-azure-cosmos-db-graph-api"></a>Solution de traçabilité de la chaîne logistique utilisant l’API Graph Azure Cosmos DB

[!INCLUDE[appliesto-gremlin-api](../includes/appliesto-gremlin-api.md)]

Cet article fournit une vue d’ensemble des [solutions graphiques de traçabilité implémentées par Infosys](https://azuremarketplace.microsoft.com/marketplace/apps/infosysltd.infosys-traceability-knowledge-graph?tab=Overview). Ces solutions utilisent l’API Azure Cosmos DB Gremlin et d’autres capacités Azure pour fournir une capacité de suivi et de traçabilité de la chaîne logistique mondiale pour les produits finis.

Cet article portera sur les éléments suivants :

* Qu’est-ce que la traçabilité dans le contexte d’une chaîne logistique ?
* L’architecture d’une solution de traçabilité mondiale fournie à l’aide des capacités Azure.  
* Comment la base de données graphique Azure Cosmos DB aide-t-elle à établir des relations complexes entre les matières premières et les produits finis dans une chaîne logistique mondiale ?
* Comment les services de la plateforme d’intégration Azure, tels que Gestion des API et Event Hub, vous aident-ils à intégrer divers écosystèmes d’applications de chaîne logistique ?
* Comment pouvez-vous obtenir l’aide d’Infosys pour utiliser cette solution pour votre besoin de traçabilité ?

## <a name="overview"></a>Vue d’ensemble

Dans la chaîne logistique alimentaire, la traçabilité des produits est la capacité « à les suivre et à les retrouver » tout au long de la chaîne logistique, pendant tout le cycle de vie du produit. La chaîne logistique comprend l’approvisionnement, la fabrication et la distribution. La traçabilité est essentielle pour la sécurité des denrées alimentaires, la marque et l’exposition aux réglementations. Par le passé, certaines organisations n’ont pas réussi à suivre et à retrouver efficacement les produits dans leur chaîne logistique, ce qui a entraîné des rappels coûteux, des amendes et des problèmes de santé chez les consommateurs.

La solution de traçabilité d’Infosys, développée avec les capacités d’Azure telles que les services d’application, les services d’intégration et les services de base de données, offre des capacités essentielles pour :

* Se connecter aux usines, aux entrepôts/centres de distribution.
* Ingérer/traiter des événements parallèles de mouvements de stock.
* Un graphique de connaissances, qui montre les connexions entre les matières premières, les lots et les palettes de produits finis, la relation parent/enfant à plusieurs niveaux des palettes, le mouvement des marchandises.
* Un portail avec une capacité de recherche pour suivre et retrouver les palettes.
* Identifier les impacts d’un incident de qualité tels que le lot de matières premières concerné, les palettes concernées, l’emplacement des palettes.

## <a name="solution-architecture"></a>Architecture de solution

La traçabilité de la chaîne logistique partage généralement des schémas dans l’ingestion des mouvements de palettes, le traitement des incidents de qualité et le suivi/l’analyse des données des magasins. Tout d’abord, ces systèmes doivent ingérer des rafales de données provenant de systèmes de gestion d’usine et d’entrepôt qui traversent les frontières géographiques. Ensuite, ces systèmes traitent et analysent les données en continu pour en déduire des relations complexes entre les matières premières, les lots de production, les palettes de produits finis et les relations complexes parent/enfant (conditionnement commun/nouveau conditionnement). Puis, le système doit stocker les relations complexes entre les matières premières, les produits finis et les palettes nécessaires à la traçabilité. Un portail utilisateur doté de capacités de recherche permet aux utilisateurs de suivre et de retrouver les produits dans le réseau de la chaîne logistique.

Microsoft Azure offre de nombreux services qui peuvent être appliqués aux cas d’usage de la traçabilité, notamment Azure Cosmos DB, Azure Event Hubs, Gestion des API Azure, Azure App Service, Azure SignalR, Azure Synapse Analytics et Power BI.

La solution de traçabilité d’Infosys fournit une solution prédéfinie que vous pouvez utiliser pour améliorer la capacité de suivi et de traçabilité. L’image suivante explique l’architecture utilisée pour cette solution de traçabilité :

:::image type="content" source="./media/supply-chain-traceability-solution/solution-architecture.png" alt-text="Architecture de la solution de traçabilité de la chaîne logistique" lightbox="./media/supply-chain-traceability-solution/solution-architecture.png" border="false":::

Les différents services Azure utilisés dans cette architecture aident à réaliser les tâches suivantes :

* Azure Cosmos DB permet d’effectuer un scale-up ou un scale-down des performances de manière flexible. L’API Gremlin vous permet de créer et d’interroger des relations complexes entre les matières premières, les produits finis et les entrepôts.
* Gestion des API Azure fournit des API pour les événements de mouvement de stock aux prestataires 3PL (prestataires logistiques tiers) et aux systèmes de gestion des entrepôts (WMS).  
* Azure Event Hub permet de rassembler un grand nombre d’événements simultanés provenant des systèmes WMS et des prestataires 3PL en vue d’un traitement ultérieur.
* Les applications Azure Function traitent les événements et ingèrent des données dans Azure Cosmos DB à l’aide de l’API Graph.
* Le service Azure Search permet aux utilisateurs d’effectuer des recherches complexes et de filtrer les informations sur les palettes.
* Azure Databricks lit les flux de modification et crée des modèles dans Synapse Analytics pour la création de rapports en libre-service pour les utilisateurs de Power BI.
* Azure Web App et le plan App Service permettent de déployer le portail utilisateur.
* Le compte Stockage Azure stocke les données archivées pour les besoins réglementaires à long terme.

## <a name="graph-db-and-its-data-design"></a>Graph BD et sa conception des données

La production et la distribution de biens nécessitent le maintien d’un ensemble complexe et dynamique de relations.  Un modèle de données adaptatif de notre graphique de traçabilité permet de stocker ces relations de la réception des matières premières jusqu’au transfert vers l’entrepôt du client, en passant par la fabrication des produits finis dans une usine et le transfert vers différents entrepôts au cours de la chaîne logistique. Une visualisation élémentaire du processus ressemble à l’image suivante :

:::image type="content" source="./media/supply-chain-traceability-solution/data-design-visualization.png" alt-text="Visualisation de la conception des données de la chaîne logistique" lightbox="./media/supply-chain-traceability-solution/data-design-visualization.png" border="true":::

Le diagramme ci-dessus donne une vue simplifiée et élémentaire d’un processus complexe de la chaîne logistique. Toutefois, en obtenant en temps réel les informations vitales sur les mouvements de stock des usines et des entrepôts, il est possible de créer un graphique élaboré qui relie tous ces éléments d’information disparates.

1. Le processus de traçabilité commence lorsque le fournisseur envoie des matières premières aux usines et que les nœuds initiaux (sommets) du graphique et des relations (arêtes) sont créés.

1. Les produits finis (Éléments) sont fabriqués à partir des matières premières et conditionnés sur des palettes.

1. Les palettes sont ensuite déplacées vers les entrepôts des usines ou les entrepôts du marché en fonction des demandes/commandes des clients.

1. L’entrepôt peut appartenir à la société ou à un prestataire 3PL (prestataires logistiques tiers). Les palettes sont ensuite expédiées vers d’autres entrepôts en fonction des commandes des clients. En fonction des demandes des clients, des palettes enfants ou des palettes enfant à enfant sont créées pour répondre à la quantité commandée. Parfois, un tout nouvel élément est créé en combinant plusieurs éléments. Par exemple, dans un scénario de conditionnement commun produisant un emballage varié, il arrive que le même élément soit reconditionné en plus petite ou plus grande quantité sur une palette différente dans le cadre d’une commande client.

   :::image type="content" source="./media/supply-chain-traceability-solution/pallet-relationship.png" alt-text="Relation entre les palettes dans la solution de traçabilité de la chaîne logistique" lightbox="./media/supply-chain-traceability-solution/pallet-relationship.png" border="true":::

1. Les palettes voyagent ensuite à travers le réseau de la chaîne logistique et atteignent finalement l’entrepôt du client. Au cours de ce processus, les palettes peuvent être décomposées davantage ou combinées avec d’autres palettes pour produire de nouvelles palettes afin de répondre aux commandes des clients.

1. Finalement, le système crée un graphique complexe qui contient des informations relationnelles vitales pour la gestion des incidents de qualité, que nous aborderons bientôt.

   :::image type="content" source="./media/supply-chain-traceability-solution/supply-chain-object-relationship.png" alt-text="Architecture complète des relations entre objets de la chaîne logistique" lightbox="./media/supply-chain-traceability-solution/supply-chain-object-relationship.png" border="true":::

1. Ces relations complexes sont essentielles en cas d’incident de qualité, lorsque le système peut suivre et retrouver les palettes tout au long de la chaîne logistique. Le graphique et ses traversées fournissent les informations nécessaires à cet effet. Par exemple, en cas de problème avec une matière première, le graphique peut montrer les palettes concernées et leur emplacement actuel.

## <a name="next-steps"></a>Étapes suivantes

* [Solution de graphique de traçabilité d’Infosys](https://azuremarketplace.microsoft.com/marketplace/apps/infosysltd.infosys-traceability-knowledge-graph?tab=Overview)
* Pour visualiser les données des graphiques, consultez les [solutions de visualisation de l’API Gremlin](graph-visualization-partners.md).
* Pour modéliser vos données graphiques, consultez les [solutions de modélisation de l’API Gremlin](graph-modeling-tools.md).