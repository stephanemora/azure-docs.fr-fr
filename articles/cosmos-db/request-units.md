---
title: Unités de requête et estimation du débit - Azure Cosmos DB | Microsoft Docs
description: Découvrez les besoins en unités de requête dans Azure Cosmos DB, et comment les spécifier et les estimer.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: rimman
ms.openlocfilehash: 16ccda120aef0aa892bf365403f3f0bdc1209ca3
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823721"
---
# <a name="request-units-in-azure-cosmos-db"></a>Unités de requête dans Azure Cosmos DB

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) est la base de données multi-modèle distribué mondialement de Microsoft. Avec Azure Cosmos DB, il n’est pas nécessaire de louer des machines virtuelles, de déployer des logiciels ou de surveiller les bases de données. Azure Cosmos DB est utilisé et surveillé en continu par les excellents ingénieurs Microsoft afin d’offrir une disponibilité, des performances et une protection des données optimales. Vous pouvez accéder à vos données en utilisant les API de votre choix, comme les API [SQL](documentdb-introduction.md), [MongoDB](mongodb-introduction.md) et [Table](table-introduction.md), et un graphique via [l’API Gremlin](graph-introduction.md). Toutes les API sont maintenant prises en charge en mode natif. 

La devise d’Azure Cosmos DB est *l’unité de requête (RU)*. Avec les unités de requête, vous n’avez pas besoin de réserver de capacités en lecture et en écriture, ni de configurer les ressources de processeur, de mémoire et d’E/S par seconde. Azure Cosmos DB prend en charge des API variées avec différentes opérations allant des lectures et des écritures simples aux requêtes de graphe complexes. Toutes les requêtes n’étant pas égales, la quantité normalisée d’unités de requête qui leur est affectée est fonction de la quantité de calcul requise pour traiter chaque requête. Le nombre d’unités de requête pour une opération est déterministe. Vous pouvez suivre le nombre d’unités de requête qui sont consommées par une opération dans Azure Cosmos DB via un en-tête de réponse. 

Pour fournir des performances prévisibles, réservez le débit par unité de 100 RU/seconde. Vous pouvez [estimer les besoins de votre débit](request-units.md#estimating-throughput-needs) à l’aide de la [calculatrice des unités de requête](https://www.documentdb.com/capacityplanner) Azure Cosmos DB.

![Calculatrice de débit][5]

Après avoir lu cet article, vous serez en mesure de répondre aux questions suivantes :

* Que sont les unités de requête et les frais de requête dans Azure Cosmos DB ?
* Comment spécifier la capacité d’unités de requête d’un conteneur ou d’un ensemble de conteneurs dans Azure Cosmos DB ?
* Comment estimer les besoins en unités de requête de mon application ?
* Que se passe-t-il si je dépasse la capacité d’unités de requête d’un conteneur ou d’un ensemble de conteneurs dans Azure Cosmos DB ?

La base de données Azure Cosmos étant une base de données multimodèle, il est important de noter que cet article s’applique à tous les modèles de données et API dans Azure Cosmos DB. Cet article utilise des termes génériques comme *conteneur* pour désigner de manière générale une collection ou un graphique et *élément* pour désigner de manière générale une table, un document, un nœud ou une entité.

## <a name="request-units-and-request-charges"></a>Unités de requête et frais de requête

Azure Cosmos DB offre des performances élevées et prévisibles en réservant des ressources pour répondre aux besoins de débit de votre application. Les modèles de chargement des applications et d’accès changent au fil du temps. Avec Azure Cosmos DB, vous pouvez facilement augmenter ou diminuer la quantité de débit réservé disponible pour votre application.

Avec Azure Cosmos DB, un débit réservé est spécifié en termes de traitement d’unités de requête par seconde. On peut considérer les unités de requête un peu comme une devise de débit. Vous réservez une quantité d’unités de requête garantie accessible par seconde à votre application. Chaque opération dans Azure Cosmos DB (y compris l’écriture d’un document, l’exécution d’une requête, la mise à jour d’un document) consomme des ressources de processeur, de mémoire et d’E/S par seconde. Autrement dit, chaque opération entraîne des frais de requête, exprimés en unités de requête. Comprendre les facteurs qui ont un impact sur les frais d’unités de requête et cerner les demandes de débit de votre application vous permettent d’exécuter votre application de la manière la plus rentable possible. 

Pour démarrer, regardez la vidéo suivante, où le gestionnaire de programmes Azure Cosmos DB, Andrew Liu, traite des unités de requête : <br /><br />

> [!VIDEO https://www.youtube.com/embed/stk5WSp5uX0]
> 
> 

## <a name="throughput-isolation-in-globally-distributed-databases"></a>Isolement de débit dans des bases de données distribués à l’échelle mondiale

Si vous répliquez votre base de données dans plusieurs régions, Azure Cosmos DB fournit une isolation de débit pour garantir que l’utilisation des unités de requête dans une région n’a aucune incidence sur l’utilisation des unités de requête dans une autre région. Par exemple, si vous écrivez des données dans une région et lisez des données à partir d’une autre région, les unités de requête permettant d’effectuer l’opération d’écriture dans la région A ne lèsent pas les unités de requête utilisées pour l’opération de lecture dans la région B. Les unités de requête ne sont pas fractionnées sur les différentes régions où vous avez effectué le déploiement de votre base de données. Chaque région dans laquelle la base de données est répliquée a le nombre total d’unités de requête provisionnées. Pour en savoir plus sur la réplication à l’échelle mondiale, voir [Comment distribuer des données mondialement avec Azure Cosmos DB](distribute-data-globally.md).

## <a name="request-unit-considerations"></a>Considérations relatives aux unités de requête
Quand vous évaluez le nombre d’unités de requête à provisionner, vous devez impérativement tenir compte des variables suivantes :

* **Taille de l’élément**. Plus la taille est grande, plus le nombre d’unités de requête consommées pour lire ou écrire des données augmente.
* **Nombre de propriétés de l’élément**. En supposant que toutes les propriétés sont indexées par défaut, le nombre d’unités consommées pour écrire un document/nœud/une entité augmente parallèlement au nombre de propriétés.
* **Cohérence des données**. Quand vous utilisez des modèles de cohérence des données, comme Fort ou Obsolescence limitée, des unités de requête supplémentaires sont consommées pour lire les éléments.
* **Propriétés indexées**. Une stratégie d’indexation sur chaque conteneur détermine quelles propriétés sont indexées par défaut. Vous pouvez réduire la consommation d’unités de requête pour les opérations d’écriture en limitant le nombre de propriétés indexées ou en activant l’indexation différée.
* **Indexation des documents**. Par défaut, chaque élément est indexé automatiquement. Vous consommez moins d’unités de requête si vous choisissez de ne pas indexer certains de vos éléments.
* **Modèles de requête**. La complexité d’une requête a une incidence sur le nombre d’unités de requête consommées pour une opération. Le nombre de résultats de requêtes, le nombre de prédicats, la nature des prédicats, le nombre de fonctions définies par l’utilisateur, la taille des données sources et les projections ont tous une influence sur le coût des opérations de requête.
* **Utilisation des scripts**. Comme avec les requêtes, les procédures stockées et les déclencheurs consomment plus ou moins d’unités de requête en fonction de la complexité des opérations effectuées. Pendant le développement de votre application, inspectez l’en-tête des frais de requêtes pour mieux comprendre de quelle façon chaque opération consomme la capacité des unités de requête.

## <a name="estimating-throughput-needs"></a>Estimation des besoins de débit
Une unité de requête est une mesure normalisée du coût de traitement de la requête. Une unité de requête représente la capacité de traitement nécessaire pour lire (par le biais d’un lien réflexif ou d’un ID) un seul élément de 1 Ko composé de 10 valeurs de propriété uniques (à l’exclusion des propriétés système). Une demande de création (insertion), de remplacement ou de suppression du même élément nécessite un plus grand traitement de la part du service et consomme donc plus d’unités de requête. 

> [!NOTE]
> La ligne de base d’1 unité de requête pour un élément de 1 Ko correspond à une opération GET simple par le lien réflexif ou l’ID de l’élément.
> 
> 

Par exemple, voici un tableau qui indique le nombre d’unités de requête à fournir pour des éléments de trois tailles différentes (1 Ko, 4 Ko et 64 Ko) et à deux niveaux de performances (500 lectures par seconde + 100 écritures par seconde et 500 lectures par seconde + 500 écritures par seconde). Dans cet exemple, la cohérence des données est configurée au niveau de la **session** et la stratégie d’indexation est définie sur **None**.

| Taille de l’élément | Lectures par seconde | Écritures par seconde | Unités de demande
| --- | --- | --- | --- |
| 1 Ko | 500 | 100 | (500 * 1) + (100 * 5) = 1 000 UR/s
| 1 Ko | 500 | 500 | (500 * 1) + (500 * 5) = 3 000 UR/s
| 4 Ko | 500 | 100 | (500 * 1.3) + (100 * 7) = 1 350 UR/s
| 4 Ko | 500 | 500 | (500 * 1.3) + (500 * 7) = 4 150 UR/s
| 64 Ko | 500 | 100 | (500 * 10) + (100 * 48) = 9 800 UR/s
| 64 Ko | 500 | 500 | (500 * 10) + (500 * 48) = 29 000 UR/s


### <a name="use-the-request-unit-calculator"></a>Utiliser la calculatrice d’unités de demande
Pour optimiser vos estimations de débit, vous pouvez utiliser une [calculatrice d’unités de requête](https://www.documentdb.com/capacityplanner) sur le web. La calculatrice vous permet d’estimer les besoins en unités de requête des opérations courantes, notamment :

* Créations (écritures) d’éléments
* Lectures d’éléments
* Suppressions d’éléments
* Mises à jour d’éléments

L’outil inclut également la prise en charge de l’estimation des besoins de stockage de données en fonction des exemples d’éléments que vous fournissez.

Pour utiliser l’outil :

1. Chargez un ou plusieurs éléments représentatifs (par exemple un exemple de document JSON).
   
    ![Charger les éléments dans la calculatrice d’unités de requête][2]
2. Pour estimer les besoins de stockage de données, entrez le nombre total d’éléments (par exemple les documents, lignes ou sommets) que vous pensez stocker.
3. Entrez le nombre d’opérations de création, de lecture, de mise à jour et de suppression dont vous avez besoin (par seconde). Pour estimer les frais d’unités de requête des opérations de mise à jour d’éléments, chargez une copie de l’exemple d’élément de l’étape 1 comprenant des mises à jour de champs types. Par exemple, si les mises à jour d’éléments comprennent généralement la modification de deux propriétés nommées *lastLogin* et *userVisits*, copiez un exemple d’élément, mettez à jour les valeurs de ces deux propriétés, puis chargez l’élément copié.
   
    ![Entrez les exigences de débit dans la calculatrice d’unités de demande][3]
4. Cliquez sur **Calculer** et examinez les résultats.
   
    ![Résultats de la calculatrice d’unités de demande][4]

> [!NOTE]
> Si vous avez des types d’éléments qui varient considérablement en termes de taille et de nombre de propriétés indexées, chargez un exemple de chaque *type* d’élément standard dans l’outil, puis calculez les résultats.
> 
> 

### <a name="use-the-azure-cosmos-db-request-charge-response-header"></a>Utiliser l’en-tête de réponse de frais de requête Azure Cosmos DB
Chaque réponse du service Azure Cosmos DB inclut un en-tête personnalisé (`x-ms-request-charge`) qui contient le nombre d’unités de requête consommées pour une requête donnée. Vous pouvez également à cet en-tête via les kits SDK Azure Cosmos DB. Dans le Kit de développement logiciel (SDK) .NET, **RequestCharge** est une propriété de l’objet **ResourceResponse**. Pour les requêtes, l’Explorateur de données Azure Cosmos DB dans le portail Azure fournit des informations sur les frais de requête pour les requêtes exécutées.

Une méthode permettant d’estimer la quantité de débit réservé requis par votre application consiste à enregistrer les frais d’unité de requête associés à l’exécution des opérations courantes sur un élément représentatif utilisé par votre application. Ensuite, vous évaluez le nombre d’opérations que vous prévoyez d’effectuer chaque seconde. Veillez également à mesurer et à inclure les requêtes courantes et l’utilisation des scripts Azure Cosmos DB.

> [!NOTE]
> Si vous avez des types d’éléments qui varient considérablement en termes de taille et de nombre de propriétés indexées, enregistrez les frais d’unités de requête d’opérations applicables associés à chaque *type* d’élément standard.
> 
> 

Par exemple, voici une procédure à suivre :

1. Enregistrez les frais d’unités de requête de création (insertion) d’un élément standard. 
2. Enregistrez les frais d’unités de requête de lecture d’un élément standard.
3. Enregistrez les frais d’unités de requête de mise à jour d’un élément standard.
4. Enregistrez les frais d’unités de requête des requêtes d’élément standard courantes.
5. Enregistrez les frais d’unités de requête des scripts personnalisés (procédures stockées, déclencheurs, fonctions définies par l’utilisateur) utilisés par l’application.
6. Calculer les unités de requête nécessaires étant donné l’estimation du nombre d’exécutions d’opérations prévues chaque seconde.

## <a name="a-request-unit-estimate-example"></a>Exemple d’estimation d’unités de requête
Prenons l’exemple du document suivant, dont la taille est d’environ 1 Ko :

```json
{
 "id": "08259",
  "description": "Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX",
  "tags": [
    {
      "name": "cereals ready-to-eat"
    },
    {
      "name": "kellogg"
    },
    {
      "name": "kellogg's crispix"
    }
  ],
  "version": 1,
  "commonName": "Includes USDA Commodity B855",
  "manufacturerName": "Kellogg, Co.",
  "isFromSurvey": false,
  "foodGroup": "Breakfast Cereals",
  "nutrients": [
    {
      "id": "262",
      "description": "Caffeine",
      "nutritionValue": 0,
      "units": "mg"
    },
    {
      "id": "307",
      "description": "Sodium, Na",
      "nutritionValue": 611,
      "units": "mg"
    },
    {
      "id": "309",
      "description": "Zinc, Zn",
      "nutritionValue": 5.2,
      "units": "mg"
    }
  ],
  "servings": [
    {
      "amount": 1,
      "description": "cup (1 NLEA serving)",
      "weightInGrams": 29
    }
  ]
}
```

> [!NOTE]
> Les documents étant minimisés dans Azure Cosmos DB, la taille du document ci-dessus calculée par le système est légèrement inférieure à 1 Ko.
> 
> 

Le tableau suivant présente les frais d’unités de requête approximatifs pour les opérations courantes sur cet élément  (les frais d’unités de requête approximatifs partent du principe que le niveau de cohérence du compte est défini sur **Session** et que tous les éléments sont indexés automatiquement).

| Opération | Frais d’unités de requête |
| --- | --- |
| Créer un élément |~15 unités de requête |
| Lire un élément |~1 unité de requête |
| Interroger un élément par ID |~2,5 unités de requête |

Le tableau suivant présente les frais d’unités de requête approximatifs des requêtes courantes utilisées dans l’application :

| Requête | Frais d’unités de requête | Nombre d’éléments renvoyés |
| --- | --- | --- |
| Sélectionner un aliment par ID |~2,5 unités de requête |1 |
| Sélectionner un aliment par fabricant |~7 unités de requête |7 |
| Sélectionner par groupe d’aliments et trier par poids |~70 unités de requête |100 |
| Sélectionner les 10 premiers aliments dans un groupe d’aliments |~10 unités de requête |10 |

> [!NOTE]
> Les frais d’unités de requête varient en fonction du nombre d’éléments renvoyés.
> 
> 

Avec ces informations, vous pouvez estimer les besoins en unités de requête pour cette application étant donné le nombre d’opérations et de requêtes attendues par seconde :

| Opération/Requête | Nombre estimé par seconde | Unités de requête requises |
| --- | --- | --- |
| Créer un élément |10 |150 |
| Lire un élément |100 |100 |
| Sélectionner un aliment par fabricant |25 |175 |
| Sélectionner par groupe d’aliments |10 |700 |
| Sélectionner les 10 premiers |15 |150 Total |

Dans ce cas, vous estimez le besoin de débit moyen à 1 275 unités de requête par seconde. En arrondissant à la centaine la plus proche, vous devez provisionner 1 300 unités de requête par seconde pour le conteneur (ou ensemble de conteneurs) de cette application.

## <a id="RequestRateTooLarge"></a> Dépassement des limites de débit réservé dans Azure Cosmos DB
La consommation d’unités de requête est évaluée sous la forme d’un taux par seconde. Les applications qui dépassent le taux d’unités de requête approvisionné voient ce taux limité jusqu’à ce qu’il soit inférieur au niveau de débit approvisionné. En cas de limitation du taux de requête, le serveur met fin à la requête de manière anticipée avec `RequestRateTooLargeException` (code d’état HTTP 429) et retourne l’en-tête `x-ms-retry-after-ms`. L’en-tête indique le délai, en millisecondes, pendant lequel l’utilisateur doit patienter avant de réessayer d’effectuer la requête.

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Si vous utilisez des requêtes LINQ et le SDK .NET Client, la plupart du temps vous ne devez jamais traiter cette exception. En effet, la version actuelle du SDK .NET Client intercepte implicitement cette réponse, respecte l’en-tête retry-after spécifié par le serveur et réessaie d’effectuer la requête. La tentative suivante réussira toujours, sauf si plusieurs clients accèdent simultanément à votre compte.

Si vous avez plusieurs clients qui opèrent en même temps au-delà du taux de requêtes, le comportement par défaut peut ne pas suffire, et le client génère une `DocumentClientException` avec le code d’état 429 dans l’application. Dans des cas semblables, vous pouvez gérer le comportement et la logique de nouvelles tentatives dans les routines de gestion d’erreurs de votre application, ou accroître le débit provisionné pour le conteneur (ou l’ensemble de conteneurs).

## <a name="next-steps"></a>Étapes suivantes
 
- Découvrez comment [définir et obtenir du débit dans Azure Cossmos DB](set-throughput.md) via le portail et le Kit de développement logiciel (SDK) Azure.
- Apprenez-en plus sur le [test des performances et de la mise à l’échelle avec Azure Cosmos DB](performance-testing.md).
- Pour en savoir plus sur le débit réservé avec les bases de données Azure Cosmos DB, consultez [Tarification de Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) et [Partitionnement, clés de partition et mise à l’échelle dans Azure Cosmos DB](partition-data.md).
- Pour en savoir plus sur Azure Cosmos DB, consultez la [documentation](https://azure.microsoft.com/documentation/services/cosmos-db/) relative à Azure Cosmos DB. 

[2]: ./media/request-units/RUEstimatorUpload.png
[3]: ./media/request-units/RUEstimatorDocuments.png
[4]: ./media/request-units/RUEstimatorResults.png
[5]: ./media/request-units/RUCalculator2.png

