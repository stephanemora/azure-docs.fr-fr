---
title: Estimer les coûts à l’aide du planificateur de capacité d’Azure Cosmos DB – API pour MongoDB
description: Le planificateur de capacité Azure Cosmos DB vous permet d’estimer le débit (unités de requête/seconde) requis et le coût de votre charge de travail. Cet article explique comment utiliser le planificateur de capacité pour estimer le débit et le coût requis lors de l’utilisation de l’API Azure Cosmos DB pour MongoDB.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 08/26/2021
ms.author: dech
ms.openlocfilehash: a6c430462185ebac38c196d5aa80fc0d676f4911
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123030354"
---
# <a name="estimate-rus-using-the-azure-cosmos-db-capacity-planner---azure-cosmos-db-api-for-mongodb"></a>Estimer les RU/s à l’aide du planificateur de capacité d’Azure Cosmos DB – API Azure Cosmos DB pour MongoDB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-mongodb-api.md)]

> [!NOTE]
> Si vous planifiez une migration de données vers Azure Cosmos DB et que tout ce que vous savez est le nombre de vCores et de serveurs dans votre cluster de bases de données partitionné et répliqué existant, lisez la section l’article sur l’[estimation des unités de requête à l’aide de vCores ou de processeurs virtuels](../convert-vcore-to-request-unit.md) 
>

Pour optimiser les coûts et les performances, il est essentiel de configurer vos bases de données et collections avec la quantité appropriée de débit approvisionné, ou [unités de requête (RU/s)](../request-units.md), pour votre charge de travail. Cet article explique comment utiliser le [planificateur de capacité](https://cosmos.azure.com/capacitycalculator/) d’Azure Cosmos DB pour estimer le nombre requis de RU/s et le coût de votre charge de travail lors de l’utilisation de l’API Azure Cosmos DB pour MongoDB. Si vous utilisez l’API SQL, consultez l’article [Utiliser le calculateur de capacité avec l’API SQL](../estimate-ru-with-capacity-planner.md).

[!INCLUDE [capacity planner modes](../includes/capacity-planner-modes.md)]

## <a name="estimate-provisioned-throughput-and-cost-using-basic-mode"></a><a id="basic-mode"></a>Estimer le débit provisionné et le coût à l’aide du mode De base
Pour obtenir une estimation rapide de votre charge de travail à l’aide du mode De base,accédez au [planificateur de capacité](https://cosmos.azure.com/capacitycalculator/). Entrez les paramètres suivants en fonction de votre charge de travail :

|**Input**  |**Description**  |
|---------|---------|
| API |Choisissez l’API MongoDB. |
|Nombre de régions|L’API Azure Cosmos DB pour MongoDB est disponible dans toutes les régions Azure. Sélectionnez le nombre de régions requises pour votre charge de travail. Vous pouvez associer un nombre quelconque de régions à votre compte. Pour plus d’informations, consultez [Distribution mondiale](../distribute-data-globally.md).|
|Écritures multirégions|Si vous activez les [écritures dans plusieurs régions](../distribute-data-globally.md#key-benefits-of-global-distribution), votre application peut lire et écrire dans n’importe quelle région Azure. Si vous désactivez les écritures dans plusieurs régions, votre application peut écrire des données dans une seule région. <br/><br/> Activez les écritures dans plusieurs régions si vous prévoyez d’avoir une charge de travail active-active qui nécessite des écritures à faible latence dans différentes régions. Par exemple, une charge de travail IoT qui écrit des données dans la base de données à des volumes élevés dans différentes régions. <br/><br/> Les écritures dans plusieurs régions garantissent une disponibilité en lecture et en écriture de 99,999 %. Les écritures dans plusieurs régions requièrent davantage de débit par rapport aux écritures dans une seule région. Pour plus d’informations, découvrez [en quoi les unités de requêtes se distinguent entre les écritures dans une région et les écritures dans plusieurs régions](../optimize-cost-regions.md).|
|Total des données stockées dans le magasin transactionnel |Total des données estimées stockées (Go) dans le magasin transactionnel dans une seule région.|
|Utiliser le magasin analytique| Choisissez **Activé** si vous souhaitez utiliser le [magasin analytique Synapse](../synapse-link.md). Entrez le **nombre total de données stockées dans le magasin analytique**, qui représente l’estimation des données stockées (Go) dans le magasin analytique dans une seule région.  |
|Taille de l’élément|Taille estimée des documents, comprise entre 1 ko et 2 Mo. |
|Recherches/s |Nombre d’opérations de recherche attendues par seconde par région. |
|Insertions/s |Nombre d’opérations d’insertion attendues par seconde par région. |
|Mises à jour/s |Nombre d’opérations de mise à jour attendues par seconde par région. Lorsque vous choisissez l’indexation automatique, le nombre estimé de RU/s pour l’opération de mise à jour est calculé comme une propriété modifiée par une mise à jour. |
|Suppressions/s |Nombre d’opérations de suppression attendues par seconde par région. |

Une fois que vous avez rempli les informations requises, sélectionnez **Calculer**. L’onglet **Estimation du coût** affiche le coût total du stockage et le débit provisionné. Vous pouvez développer le lien **Afficher les détails** sous cet onglet pour obtenir la décomposition du débit requise pour les différents CRUD et demandes de requête. **Chaque fois que vous modifiez la valeur d’un champ, sélectionnez Calculer pour recalculer le coût estimé.**

:::image type="content" source="./media/estimate-ru-capacity-planner/basic-mode-mongodb-api.png" alt-text="Mode De base du planificateur de capacité" border="true":::

## <a name="estimate-provisioned-throughput-and-cost-using-advanced-mode"></a><a id="advanced-mode"></a>Estimer le débit provisionné et le coût à l’aide du mode Avancé

Le mode Avancé vous permet de fournir des paramètres supplémentaires qui ont un impact sur l’estimation des unités de requête/seconde. Pour utiliser cette option, accédez au [planificateur de capacité](https://cosmos.azure.com/capacitycalculator/) et **connectez-vous** à l’outil avec un compte que vous utilisez pour Azure. L’option de connexion est disponible dans l’angle droit.

Une fois connecté, vous pouvez voir des champs supplémentaires par rapport aux champs du mode De base. Entrez les autres paramètres en fonction de votre charge de travail.

|**Input**  |**Description**  |
|---------|---------|
|API|Azure Cosmos DB est un service multi-modèle et multi-API. Choisissez l’API MongoDB. |
|Nombre de régions|L’API Azure Cosmos DB pour MongoDB est disponible dans toutes les régions Azure. Sélectionnez le nombre de régions requises pour votre charge de travail. Vous pouvez associer un nombre quelconque de régions à votre compte Cosmos. Pour plus d’informations, consultez [Distribution mondiale](../distribute-data-globally.md).|
|Écritures multirégions|Si vous activez les [écritures dans plusieurs régions](../distribute-data-globally.md#key-benefits-of-global-distribution), votre application peut lire et écrire dans n’importe quelle région Azure. Si vous désactivez les écritures dans plusieurs régions, votre application peut écrire des données dans une seule région. <br/><br/> Activez les écritures dans plusieurs régions si vous prévoyez d’avoir une charge de travail active-active qui nécessite des écritures à faible latence dans différentes régions. Par exemple, une charge de travail IoT qui écrit des données dans la base de données à des volumes élevés dans différentes régions. <br/><br/> Les écritures dans plusieurs régions garantissent une disponibilité en lecture et en écriture de 99,999 %. Les écritures dans plusieurs régions requièrent davantage de débit par rapport aux écritures dans une seule région. Pour plus d’informations, découvrez [en quoi les unités de requêtes se distinguent entre les écritures dans une région et les écritures dans plusieurs régions](../optimize-cost-regions.md).|
|Cohérence par défaut|L’API Azure Cosmos DB pour MongoDB prend en charge cinq niveaux de cohérence afin de permettre aux développeurs d’équilibrer le compromis entre cohérence, disponibilité et latence. Pour en savoir plus, consultez l’article [Niveaux de cohérence](../consistency-levels.md). <br/><br/> Par défaut, l’API pour MongoDB utilise la cohérence de session, qui garantit la capacité de lire vos propres écritures dans une session. <br/><br/> Le choix du niveau Obsolescence limitée ou Cohérence forte nécessite le double d’unités de requête/seconde requis pour les lectures, par rapport aux niveaux Session, Préfixe cohérent et Cohérence éventuelle. Une cohérence forte avec les écritures dans plusieurs régions n’est pas prise en charge et prend automatiquement par défaut les écritures dans une seule région avec une cohérence forte. |
|Stratégie d’indexation| Si vous choisissez l’option **Désactivé**, aucune des propriétés n’est indexée. Cela entraîne un coût d’unité de requête inférieur pour les écritures. Désactivez la stratégie d’indexation si vous envisagez uniquement d’effectuer des requêtes à l’aide du champ _id et de la clé de partition pour chaque requête (les deux par requête).<br/><br/> Si vous choisissez l’option **Automatique**, les versions 3.6 et ultérieures de l’API pour MongoDB indexent automatiquement le champ _id. Lorsque vous choisissez l’indexation automatique, cela revient à définir un index de caractère générique (où chaque propriété est indexée automatiquement). Utilisez des index de caractère générique pour tous les champs afin d’obtenir des requêtes flexibles et efficaces.<br/><br/>Si vous choisissez l’option **Personnaliser**, vous pouvez définir le nombre de propriétés indexées à l’aide d’index composés de plusieurs clés ou d’index composés. Vous pouvez entrer le nombre de propriétés indexées plus tard dans le formulaire. Pour plus d’informations, consultez [Gestion des index](../mongodb-indexing.md) dans l’API pour MongoDB.|
|Total des données stockées dans le magasin transactionnel |Total estimé des données stockées (Go) dans le magasin transactionnel dans une seule région.|
|Utiliser le magasin analytique| Choisissez **Activé** si vous souhaitez utiliser le [magasin analytique Synapse](../synapse-link.md). Entrez le **nombre total de données stockées dans le magasin analytique**, qui représente l’estimation des données stockées (Go) dans le magasin analytique dans une seule région.  |
|Mode Charge de travail|Sélectionnez l’option **Stable** si le volume de votre charge de travail est constant. <br/><br/> Sélectionnez l’option **Variable** si le volume de votre charge de travail change au fil du temps.  Par exemple, pendant un jour ou un mois spécifique. Le paramètre suivant est disponible si vous choisissez l’option de charge de travail variable :<ul><li>Percentage of time at peak (Pourcentage de temps lors des pics) : Pourcentage de temps dans un mois où votre charge de travail requiert un débit maximal (le plus élevé). </li></ul> <br/><br/> Par exemple, si vous avez une charge de travail qui a une activité élevée pendant les heures de bureau de 9 h 00 à 18 h 00, le pourcentage de temps à atteindre est le suivant : 45 heures lors du pic/730 heures/mois = ~ 6 %.<br/><br/>Avec les intervalles prenant en compte les heures creuses et les pics, vous pouvez optimiser vos coûts en [mettant à l’échelle votre débit provisionné par programmation](../set-throughput.md#update-throughput-on-a-database-or-a-container) en conséquence.|
|Taille de l’élément|Taille des documents, comprise entre 1 ko et 2 Mo. Vous pouvez ajouter des estimations pour plusieurs exemples d’éléments. <br/><br/>Vous pouvez également **charger un exemple de document (JSON)** pour obtenir une estimation plus précise.<br/><br/>Si votre charge de travail comporte plusieurs types d’éléments (avec un contenu JSON différent) dans le même conteneur, vous pouvez télécharger plusieurs documents JSON et récupérer l’estimation. Utilisez le bouton **Add new item (Ajouter un nouvel élément)** pour ajouter plusieurs exemples de documents JSON.|
| Type d'opération | Type d’opération, par exemple **Rechercher**, **Agréger**, **Modifier**, etc.  |
| Frais d’unités de requête (RU) par appel | Estimation des frais de RU/s pour exécuter le type d’opération sélectionné. |
| Appels/s par région | Nombre de types d’opération sélectionnés exécutés par seconde par région. |

Vous pouvez également utiliser le bouton **Save Estimate (Enregistrer l’estimation)** pour télécharger un fichier .csv contenant l’estimation actuelle.

:::image type="content" source="./media/estimate-ru-capacity-planner/advanced-mode-mongodb-api.png" alt-text="Mode Avancé du planificateur de capacité" border="true":::

Les prix indiqués dans le planificateur de capacité sont des estimations basées sur les tarifs publics pour le débit et le stockage. Tous les prix sont affichés en dollars américains. Reportez -vous à la [page de tarification Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) pour afficher tous les tarifs par région.  

## <a name="next-steps"></a>Étapes suivantes

* Si vous ne connaissez que le nombre de vCore et de serveurs présents dans votre cluster de bases de données existant, lisez l’article sur l’[estimation des unités de requête à l’aide de vCore ou de processeurs virtuels](../convert-vcore-to-request-unit.md) 
* Apprenez-en davantage sur le [modèle de tarification Azure Cosmos DB](../how-pricing-works.md).
* Créez un [compte, une base de données et un conteneur Cosmos](../create-cosmosdb-resources-portal.md).
* Découvrez comment [optimiser le coût du débit provisionné](../optimize-cost-throughput.md).
* Découvrez comment [optimiser le coût avec une capacité réservée](../cosmos-db-reserved-capacity.md).
* Vous tentez d’effectuer une planification de la capacité pour une migration vers Azure Cosmos DB ? Vous pouvez utiliser les informations sur votre cluster de bases de données existantes pour la planification de la capacité.
