---
title: Analyse d’Azure Cosmos DB à l’aide d’outils de d’analyse tiers
description: Cet article décrit des outils d’analyse tiers pour vous aider à analyser Cosmos DB.
author: manishmsfte
ms.author: mansha
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/28/2021
ms.openlocfilehash: b78d6a4c92ad96c15d55332aac320bad09c58c1e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562158"
---
# <a name="monitoring-azure-cosmos-db-using-third-party-solutions"></a>Analyse d’Azure Cosmos DB à l’aide d’outils d’analyse tiers
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Outre Azure Monitor, vous pouvez utiliser des solutions d’analyse tierces pour analyser vos instances Cosmos DB.

> [!IMPORTANT] 
> Les solutions mentionnées dans cet article sont fournies à titre d’information uniquement, la propriété appartient au propriétaire de la solution. Nous recommandons aux utilisateurs d’effectuer une évaluation minutieuse, puis de sélectionner celles qui vous conviennent le mieux.

## <a name="datadog"></a>Datadog
{Prend en charge : API SQL, API Azure Cosmos DB pour MongoDB, API Gremlin, API Cassandra et API Table}

[Datadog](https://www.datadoghq.com/) est une plateforme entièrement unifiée englobant l’analyser d’infrastructure, l’analyse de performance des applications, la gestion des journaux d’activité, l’analyse de l’expérience utilisateur et bien plus encore. En rassemblant les données de tous les outils et services de la pile de votre entreprise, Datadog fournit une seule source fiable pour le dépannage, l’optimisation des performances et la collaboration entre les équipes.
Tout ce qui se trouve dans Datadog est organisé sous le même ensemble d’étiquettes, de sorte que toutes les données pertinentes pour un problème particulier sont corrélées automatiquement. En éliminant les points morts, Datadog réduit le risque d’erreurs non repérées, limite la charge de maintenance continue des services et accélère les transformations numériques.

Datadog collecte plus de 40 métriques de jauge et de nombre différentes de CosmosDB, y compris le stockage total disponible par région, le nombre de bases de données SQL créées, et bien plus encore. Ces métriques sont collectées par le biais de l’intégration Azure Datadog et s’affichent dans la plateforme 40 % plus rapidement que le reste du secteur. Datadog fournit également un tableau de bord prêt à l’emploi pour CosmosDB, qui fournit un aperçu immédiat des performances des instances CosmosDB. Les utilisateurs peuvent visualiser les métriques au niveau de la plateforme, telles que le nombre total d’unités de requête consommées, ainsi que les métriques au niveau de l’API, comme le nombre d’espaces de clés Cassandra créés pour mieux comprendre leur utilisation de CosmosDB.

Datadog est utilisé par plusieurs clients Cosmos DB, qui incluent
- Maersk
- PWC 
- PayScale 
- Allscripts 
- Hearst



:::image type="content" source="./media/monitor-solutions/datadog-demo.gif" alt-text="Démo Datadog" border="false":::
**Figure :** Datadog en action

Liens utiles :
- [Détails de la tarification](https://www.datadoghq.com/pricing/)
- [Prise en main avec un essai pendant 14 jours](https://www.datadoghq.com/free-datadog-trial/)


## <a name="dynatrace"></a>Dynatrace
{Prend en charge : API SQL et API Azure Cosmos DB pour MongoDB}

[DynaTrace](https://www.dynatrace.com/platform/) fournit une intelligence logicielle pour le cloud afin de maîtriser la complexité du cloud et d’accélérer la transformation numérique. Grâce à une observation automatique et intelligente à l’échelle, la plateforme d’intelligence logicielle DynaTrace tout-en-un fournit des réponses précises sur les performances et la sécurité des applications, l’infrastructure sous-jacente et l’expérience de tous les utilisateurs, afin que les équipes puissent automatiser les opérations cloud, mettre en place des logiciels plus rapides et fournir des expériences numériques inégalées  
À l’aide de l’API Mongo, DynaTrace collecte et fournit des métriques CosmosDB, qui incluent le nombre d’appels et le temps de réponse, tous visualisés en fonction de l’agrégation, des commandes, des opérations de lecture et d’écriture.  Il indique également les instructions de base de données exactes exécutées dans votre environnement.  Enfin, avec la puissance du [moteur d’IA Davis](https://www.dynatrace.com/davis), il peut détecter exactement quelle instruction de base de données est la cause racine de la dégradation et peut voir la base de données identifiée comme étant la cause racine.

:::image type="content" source="./media/monitor-solutions/dynatrace-demo.gif" alt-text="Écran des différents Dynatrace pour fournir des informations d’analyse de Cosmos DB" border="false":::
**Figure :** Dynatrace en action

### <a name="useful-links"></a>Liens utiles

- [Essayez DynaTrace avec un essai gratuit pendant 15 jours](https://www.dynatrace.com/trial)
- [Déployer à partir de la Place de marché Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/dynatrace.dynatrace-managed)
- [Documentation sur Cosmos DB avec Azure Monitor](https://www.dynatrace.com/support/help/technology-support/cloud-platforms/microsoft-azure-services/set-up-integration-with-azure-monitor/?_ga=2.184080354.559899881.1623174355-748416177.1603817475)
- [Cosmos DB – Détails de l’intégration DynaTrace](https://www.dynatrace.com/news/blog/azure-services-explained-part-4-azure-cosmos-db/?_ga=2.185016301.559899881.1623174355-748416177.1603817475)
- [Analyse DynaTrace pour les bases de données Azure](https://www.dynatrace.com/technologies/azure-monitoring/azure-database-performance/)
- [Vue d’ensemble de la solution DynaTrace pour Azure](https://www.dynatrace.com/technologies/azure-monitoring/)
- [Partenaires de solutions](https://www.dynatrace.com/partners/solution-partners/)

## <a name="next-steps"></a>Étapes suivantes
- [Informations de référence sur l’analyse des données Cosmos DB](./monitor-cosmos-db-reference.md)
