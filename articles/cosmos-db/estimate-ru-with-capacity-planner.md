---
title: Estimer les coûts à l’aide du planificateur de capacité Azure Cosmos DB - API SQL
description: Le planificateur de capacité Azure Cosmos DB vous permet d’estimer le débit (unités de requête/seconde) requis et le coût de votre charge de travail. Cet article explique comment utiliser le planificateur de capacité pour estimer le débit et le coût requis lors de l’utilisation de l’API SQL.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/20/2021
ms.author: dech
ms.openlocfilehash: 6329935c780494722a78e00e82446834f72aa37b
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108209298"
---
# <a name="estimate-rus-using-the-azure-cosmos-db-capacity-planner---sql-api"></a>Estimer le nombre d’unités de requête/seconde à l’aide du planificateur de capacité Azure Cosmos DB - API SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Pour optimiser les coûts et les performances, il est essentiel de configurer vos bases de données et conteneurs Azure Cosmos avec la quantité appropriée de débit provisionné, ou d’[unités de requête par seconde](request-units.md), pour votre charge de travail. Cet article explique comment utiliser le [planificateur de capacité](https://cosmos.azure.com/capacitycalculator/) Azure Cosmos DB pour estimer le nombre requis d’unités de requête par seconde et le coût de votre charge de travail lors de l’utilisation de l’API SQL. Si vous utilisez l’API pour MongoDB, consultez l’article [Utiliser le calculateur de capacité avec MongoDB](mongodb/estimate-ru-capacity-planner.md).

[!INCLUDE [capacity planner modes](includes/capacity-planner-modes.md)]

## <a name="estimate-provisioned-throughput-and-cost-using-basic-mode"></a><a id="basic-mode"></a>Estimer le débit provisionné et le coût à l’aide du mode De base
Pour obtenir une estimation rapide de votre charge de travail à l’aide du mode De base,accédez au [planificateur de capacité](https://cosmos.azure.com/capacitycalculator/). Entrez les paramètres suivants en fonction de votre charge de travail :

|**Input**  |**Description**  |
|---------|---------|
| API |Choisir l’API SQL (Core) |
|Nombre de régions|Azure Cosmos DB est disponible dans toutes les régions Azure. Sélectionnez le nombre de régions requises pour votre charge de travail. Vous pouvez associer un nombre quelconque de régions à votre compte Cosmos. Pour plus d’informations, consultez [Distribution de données mondiale avec Azure Cosmos DB](distribute-data-globally.md).|
|Écritures multirégions|Si vous activez les [écritures dans plusieurs régions](distribute-data-globally.md#key-benefits-of-global-distribution), votre application peut lire et écrire dans n’importe quelle région Azure. Si vous désactivez les écritures dans plusieurs régions, votre application peut écrire des données dans une seule région. <br/><br/> Activez les écritures dans plusieurs régions si vous prévoyez d’avoir une charge de travail active-active qui nécessite des écritures à faible latence dans différentes régions. Par exemple, une charge de travail IoT qui écrit des données dans la base de données à des volumes élevés dans différentes régions. <br/><br/> Les écritures dans plusieurs régions garantissent une disponibilité en lecture et en écriture de 99,999 %. Les écritures dans plusieurs régions requièrent davantage de débit par rapport aux écritures dans une seule région. Pour plus d’informations, découvrez [en quoi les unités de requêtes se distinguent entre les écritures dans une région et les écritures dans plusieurs régions](optimize-cost-regions.md).|
|Total des données stockées dans le magasin transactionnel |Total des données estimées stockées (Go) dans le magasin transactionnel dans une seule région.|
|Utiliser le magasin analytique| Sélectionnez **Activé** si vous souhaitez utiliser le magasin analytique. Entrez le **nombre total de données stockées dans le magasin analytique**, qui représente l’estimation des données stockées (Go) dans le magasin analytique dans une seule région.  |
|Taille de l’élément|Taille estimée de l’élément de données (un document, par exemple), comprise entre 1 Ko et 2 Mo. |
|Requêtes/s |Nombre de requêtes attendues par seconde par région. Les frais en RU moyens pour exécuter une requête sont estimés à 10 RU. |
|Lectures de points/s |Nombre d’opérations de lecture de points attendues par seconde par région. Les lectures de points correspondent à la recherche de clé/valeur sur un ID d’élément unique et une clé de partition. Pour plus d’informations sur les lectures de points, consultez l’article [Options de lecture des données](optimize-cost-reads-writes.md#reading-data-point-reads-and-queries). |
|Créations/s |Nombre d’opérations de création attendues par seconde par région. |
|Mises à jour/s |Nombre d’opérations de mise à jour attendues par seconde par région. Lorsque vous choisissez l’indexation automatique, le nombre estimé de RU/s pour l’opération de mise à jour est calculé comme une propriété modifiée par une mise à jour. |
|Suppressions/s |Nombre d’opérations de suppression attendues par seconde par région. |

Une fois que vous avez rempli les informations requises, sélectionnez **Calculer**. L’onglet **Estimation du coût** affiche le coût total du stockage et le débit provisionné. Vous pouvez développer le lien **Afficher les détails** sous cet onglet pour obtenir la décomposition du débit requise pour les différents CRUD et demandes de requête. Chaque fois que vous modifiez la valeur d’un champ, sélectionnez **Calculer** pour recalculer le coût estimé.

:::image type="content" source="./media/estimate-ru-with-capacity-planner/basic-mode-sql-api.png" alt-text="Mode De base du planificateur de capacité" border="true":::

## <a name="estimate-provisioned-throughput-and-cost-using-advanced-mode"></a><a id="advanced-mode"></a>Estimer le débit provisionné et le coût à l’aide du mode Avancé

Le mode Avancé vous permet de fournir des paramètres supplémentaires qui ont un impact sur l’estimation des unités de requête/seconde. Pour utiliser cette option, accédez au [planificateur de capacité](https://cosmos.azure.com/capacitycalculator/) et **connectez-vous** à l’outil avec un compte que vous utilisez pour Azure. L’option de connexion est disponible dans l’angle droit.

Une fois connecté, vous pouvez voir des champs supplémentaires par rapport aux champs du mode De base. Entrez les autres paramètres en fonction de votre charge de travail.

|**Input**  |**Description**  |
|---------|---------|
|API|Azure Cosmos DB est un service multi-modèle et multi-API. Choisissez l’API SQL (Core). |
|Nombre de régions|Azure Cosmos DB est disponible dans toutes les régions Azure. Sélectionnez le nombre de régions requises pour votre charge de travail. Vous pouvez associer un nombre quelconque de régions à votre compte Cosmos. Pour plus d’informations, consultez [Distribution de données mondiale avec Azure Cosmos DB](distribute-data-globally.md).|
|Écritures multirégions|Si vous activez les [écritures dans plusieurs régions](distribute-data-globally.md#key-benefits-of-global-distribution), votre application peut lire et écrire dans n’importe quelle région Azure. Si vous désactivez les écritures dans plusieurs régions, votre application peut écrire des données dans une seule région. <br/><br/> Activez les écritures dans plusieurs régions si vous prévoyez d’avoir une charge de travail active-active qui nécessite des écritures à faible latence dans différentes régions. Par exemple, une charge de travail IoT qui écrit des données dans la base de données à des volumes élevés dans différentes régions. <br/><br/> Les écritures dans plusieurs régions garantissent une disponibilité en lecture et en écriture de 99,999 %. Les écritures dans plusieurs régions requièrent davantage de débit par rapport aux écritures dans une seule région. Pour plus d’informations, découvrez [en quoi les unités de requêtes se distinguent entre les écritures dans une région et les écritures dans plusieurs régions](optimize-cost-regions.md).|
|Cohérence par défaut|Azure Cosmos DB prend en charge 5 niveaux de cohérence, afin de permettre aux développeurs d’avoir un compromis entre cohérence, disponibilité et latence. Pour en savoir plus, consultez l’article [Niveaux de cohérence](consistency-levels.md). <br/><br/> Par défaut, Azure Cosmos DB utilise la cohérence de session, qui garantit la capacité de lire vos propres écritures dans une session. <br/><br/> Le choix du niveau Obsolescence limitée ou Cohérence forte nécessite le double d’unités de requête/seconde requis pour les lectures, par rapport aux niveaux Session, Préfixe cohérent et Cohérence éventuelle. Une cohérence forte avec les écritures dans plusieurs régions n’est pas prise en charge et prend automatiquement par défaut les écritures dans une seule région avec une cohérence forte. |
|Stratégie d’indexation|Par défaut, Azure Cosmos DB [indexe toutes les propriétés](index-policy.md) de tous les éléments pour des requêtes flexibles et efficaces (en respectant la stratégie d’indexation **automatique**). <br/><br/> Si vous choisissez **désactivé**, aucune des propriétés n’est indexée. Cela entraîne un coût d’unité de requête inférieur pour les écritures. Choisissez de **désactiver** la stratégie si vous prévoyez uniquement des [lectures de point](/dotnet/api/microsoft.azure.cosmos.container.readitemasync) (recherches de valeurs de clés) et/ou des écritures, et aucune requête. <br/><br/> Si vous choisissez **Automatique**, Azure CosmosDB indexe automatiquement tous les éléments au fur et à mesure qu’ils sont écrits. <br/><br/> La stratégie d’indexation **personnalisée** vous permet d’inclure ou d’exclure des propriétés spécifiques de l’index pour réduire le débit d’écriture et le stockage. Pour plus d’informations, consultez les articles sur la [stratégie d’indexation](index-overview.md) et les [exemples de stratégies d’indexation](how-to-manage-indexing-policy.md#indexing-policy-examples).|
|Total des données stockées dans le magasin transactionnel |Total des données estimées stockées (Go) dans le magasin transactionnel dans une seule région.|
|Utiliser le magasin analytique| Sélectionnez **Activé** si vous souhaitez utiliser le magasin analytique. Entrez le **nombre total de données stockées dans le magasin analytique**, qui représente l’estimation des données stockées (Go) dans le magasin analytique au sein d’une seule région.  |
|Mode Charge de travail|Sélectionnez l’option **Stable** si le volume de votre charge de travail est constant. <br/><br/> Sélectionnez l’option **Variable** si le volume de votre charge de travail change au fil du temps.  Par exemple, pendant un jour ou un mois spécifique. Le paramètre suivant est disponible si vous choisissez l’option de charge de travail variable :<ul><li>Percentage of time at peak (Pourcentage de temps lors des pics) : Pourcentage de temps dans un mois où votre charge de travail requiert un débit maximal (le plus élevé). </li></ul> <br/><br/> Par exemple, si vous avez une charge de travail qui a une activité élevée pendant les heures de bureau de 9 h 00 à 18 h 00, le pourcentage de temps à atteindre est le suivant : 45 heures lors du pic/730 heures/mois = ~ 6 %.<br/><br/>Avec les intervalles prenant en compte les heures creuses et les pics, vous pouvez optimiser vos coûts en [mettant à l’échelle votre débit provisionné par programmation](set-throughput.md#update-throughput-on-a-database-or-a-container) en conséquence.|
|Taille de l’élément|Taille de l’élément de données (un document, par exemple), comprise entre 1 Ko et 2 Mo. Vous pouvez ajouter des estimations pour plusieurs exemples d’éléments. <br/><br/>Vous pouvez également **charger un exemple de document (JSON)** pour obtenir une estimation plus précise.<br/><br/>Si votre charge de travail comporte plusieurs types d’éléments (avec un contenu JSON différent) dans le même conteneur, vous pouvez télécharger plusieurs documents JSON et récupérer l’estimation. Utilisez le bouton **Add new item (Ajouter un nouvel élément)** pour ajouter plusieurs exemples de documents JSON.|
| Nombre de propriétés | Nombre moyen de propriétés par élément. |
|Lectures de points/s |Nombre d’opérations de lecture de points attendues par seconde par région. Les lectures de points correspondent à la recherche de clé/valeur sur un ID d’élément unique et une clé de partition. Les opérations de lecture de points sont différentes des opérations de lecture de requêtes. Pour plus d’informations sur les lectures de points, consultez l’article [Options de lecture des données](optimize-cost-reads-writes.md#reading-data-point-reads-and-queries). Si votre mode de charge de travail est **Variable**, vous pouvez fournir le nombre attendu d’opérations de lecture de points pour les heures de pointe et les heures creuses. |
|Créations/s |Nombre d’opérations de création attendues par seconde par région. |
|Mises à jour/s |Nombre d’opérations de mise à jour attendues par seconde par région. |
|Suppressions/s |Nombre d’opérations de suppression attendues par seconde par région. |
|Requêtes/s |Nombre de requêtes attendues par seconde par région. Pour obtenir une estimation précise, utilisez le coût moyen des requêtes ou entrez les RU/s que vos requêtes utilisent à partir des statistiques des requêtes dans le portail Azure. |
| Frais moyens des RU/s par requête | Par défaut, le coût moyen des requêtes/s par région est estimé à 10 RU/s. Vous pouvez l’augmenter ou le diminuer selon les frais de RU/s basés sur vos frais de requêtes estimés.|

Vous pouvez également utiliser le bouton **Save Estimate (Enregistrer l’estimation)** pour télécharger un fichier .csv contenant l’estimation actuelle.

:::image type="content" source="./media/estimate-ru-with-capacity-planner/advanced-mode-sql-api.png" alt-text="Mode Avancé du planificateur de capacité" border="true":::

Les prix indiqués dans le planificateur de capacité Azure Cosmos DB sont des estimations basées sur les tarifs publics pour le débit et le stockage. Tous les prix sont affichés en dollars américains. Reportez -vous à la [page de tarification Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) pour afficher tous les tarifs par région.  

## <a name="next-steps"></a>Étapes suivantes

* Apprenez-en davantage sur le [modèle de tarification Azure Cosmos DB](how-pricing-works.md).
* Créez un [compte, une base de données et un conteneur Cosmos](create-cosmosdb-resources-portal.md).
* Découvrez comment [optimiser le coût du débit provisionné](optimize-cost-throughput.md).
* Découvrez comment [optimiser le coût avec une capacité réservée](cosmos-db-reserved-capacity.md).
