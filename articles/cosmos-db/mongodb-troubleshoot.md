---
title: Résoudre des erreurs courantes dans l’API Azure Cosmos DB pour MongoDB
description: Ce document détaille les façons de résoudre les problèmes couramment rencontrés dans l’API Azure Cosmos DB pour MongoDB.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: chrande
ms.openlocfilehash: 26097408d0b83b043f4a25183146c892fc4b48ad
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2021
ms.locfileid: "98538543"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Résoudre des problèmes courants dans l’API Azure Cosmos DB pour MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

L’article suivant décrit les erreurs courantes et les solutions relatives aux déploiements utilisant l’API Azure Cosmos DB pour MongoDB.

>[!Note]
> Azure Cosmos DB n’héberge pas le moteur MongoDB. Il fournit une implémentation du protocole de transmission MongoDB. Par conséquent, certaines de ces erreurs se trouvent uniquement dans l’API Azure Cosmos DB pour MongoDB. 

## <a name="common-errors-and-solutions"></a>Erreurs courantes et solutions

| Code       | Error                | Description  | Solution  |
|------------|----------------------|--------------|-----------|
| 2 | Le chemin de l’index correspondant à l’élément order-by spécifié est exclu ou la requête order by n’a pas d’index composite correspondant à partir duquel elle peut être traitée. | La requête demande un tri sur un champ qui n’est pas indexé. | Créez un index correspondant (ou un index composite) pour la requête de tri tentée. |
| 13 | Non autorisé | La requête ne dispose pas des autorisations nécessaires pour aboutir. | Veillez à définir les autorisations appropriées pour votre base de données et votre collection.  |
| 16 | InvalidLength | La longueur de la requête spécifiée n’est pas valide. | Si vous utilisez la fonction EXPLAIN (), assurez-vous que vous ne fournissez qu’une seule opération. |
| 26 | NamespaceNotFound | La base de données ou la collection référencée dans la requête est introuvable. | Assurez-vous que le nom de votre base de données/collection correspond exactement au nom de votre requête.|
| 50 | ExceededTimeLimit | La requête a dépassé le délai d’exécution de 60 secondes. |  Cette erreur peut avoir plusieurs causes. L’une d’elles est que la capacité actuelle d’unités de requête allouées n’est pas suffisante pour traiter la requête. Ce problème peut être résolu en augmentant le nombre d’unités de requête de cette collection ou base de données. Dans d’autres cas, il est possible de contourner cette erreur en fractionnant une requête volumineuse en plusieurs requêtes plus petites. Toute nouvelle tentative d’écriture ayant reçu cette erreur peut entraîner une écriture en double.|
| 61 | ShardKeyNotFound | Le document de votre requête ne contient pas la clé de partition de la collection (clé de partition Azure Cosmos DB). | Vérifiez que la clé de partition de la collection est utilisée dans la requête.|
| 66 | ImmutableField | La requête tente de modifier un champ immuable | Les champs « ID » sont immuables. Vérifiez que votre requête ne tente pas de mettre à jour ce champ. |
| 67 | CannotCreateIndex | La requête de création d’un index ne peut pas être effectuée. | Jusqu’à 500 index de champ uniques peuvent être créés dans un conteneur. Jusqu’à huit champs peuvent être inclus dans un index composé (les index composés sont pris en charge dans la version 3.6+). |
| 115 | CommandNotSupported | La requête tentée n’est pas prise en charge. | Des détails supplémentaires doivent être fournis dans l’erreur. Si cette fonctionnalité est importante pour vos déploiements, faites-le nous savoir en créant un ticket de support dans le [portail Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). |
| 11000 | DuplicateKey | La clé de partition (clé de partition Azure Cosmos DB) du document que vous insérez existe déjà dans la collection ou une contrainte de champ d’index unique a été violée. | Utilisez la fonction update () pour mettre à jour un document existant. Si la contrainte de champ d’index unique a été violée, insérez ou mettez à jour le document avec une valeur de champ non présente dans la partition. |
| 16500 | TooManyRequests  | Le nombre total d’unités de requête consommées est supérieur au taux d’unités de requête provisionné pour la collection et a été limité. | Adaptez le débit assigné à un conteneur ou à un ensemble de conteneurs à partir du portail Azure ou renouvelez l’opération. Si vous [activez SSR](prevent-rate-limiting-errors.md) (nouvelle tentative côté serveur), Azure Cosmos DB retente automatiquement les requêtes qui échouent en raison de cette erreur. |
| 16501 | ExceededMemoryLimit | En tant que service multi-locataire, l’opération a dépassé l’allocation de mémoire du client. | Réduisez l’étendue de l’opération en fixant un critère de requête plus restrictif, ou contactez le support technique via le [portail Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Exemple : `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 40324 | Nom d’étape de pipeline non reconnu. | Le nom d’étape de votre requête de pipeline d’agrégation n’a pas été reconnu. | Assurez-vous que tous les noms de pipeline d’agrégation sont valides dans votre requête. |
| - | Problèmes relatifs à la version câblée de MongoDB | Les anciennes versions des pilotes MongoDB ne sont pas en mesure de détecter le nom du compte Azure Cosmos dans les chaînes de connexion. | Ajoutez *appName=@**accountName**@* à la fin de la chaîne de connexion de votre API Cosmos DB pour MongoDB, où ***accountName*** correspond au nom de votre compte Cosmos DB. |
| - | Problèmes de réseau du client MongoDB (tels que les exceptions socket ou endOfStream)| La requête réseau a échoué. Cela est fréquemment dû à une connexion TCP inactive que le client MongoDB tente d’utiliser. Les pilotes MongoDB utilisent souvent le regroupement de connexions, ce qui entraîne l’utilisation d’une connexion aléatoire choisie à partir du pool. En règle générale, les connexions inactives expirent au niveau d’Azure Cosmos DB après quatre minutes. | Vous pouvez retenter les requêtes ayant échoué dans votre code d’application, modifier les paramètres de votre client MongoDB (pilote) pour déconnecter les connexions TCP inactives avant la fenêtre de délai d’expiration de 4 minutes ou configurer vos paramètres KeepAlive de système d’exploitation pour maintenir les connexions TCP dans un état actif. |

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [utiliser Studio 3T](mongodb-mongochef.md) avec l’API Azure Cosmos DB pour MongoDB.
- Découvrez comment [utiliser Robo 3T](mongodb-robomongo.md) avec l’API Azure Cosmos DB pour MongoDB.
- Explorez les [exemples](mongodb-samples.md) MongoDB avec l’API Azure Cosmos DB pour MongoDB.
