---
title: Résoudre des erreurs courantes dans l’API Azure Cosmos DB pour MongoDB
description: Ce document détaille les façons de résoudre les problèmes couramment rencontrés dans l’API Azure Cosmos DB pour MongoDB.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: chrande
ms.openlocfilehash: de39aee73a6f4b422af4524d3302f8858f8b060b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101692230"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Résoudre des problèmes courants dans l’API Azure Cosmos DB pour MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

L’article suivant décrit les erreurs courantes et les solutions relatives aux déploiements utilisant l’API Azure Cosmos DB pour MongoDB.

>[!Note]
> Azure Cosmos DB n’héberge pas le moteur MongoDB. Il fournit une implémentation du [protocole filaire version 4.0](mongodb-feature-support-40.md) et [3.6](mongodb-feature-support-36.md), et la prise en charge héritée pour le [protocole filaire version 3.2](mongodb-feature-support.md) de MongoDB. Par conséquent, certaines de ces erreurs se trouvent uniquement dans l’API Azure Cosmos DB pour MongoDB.

## <a name="common-errors-and-solutions"></a>Erreurs courantes et solutions

| Code       | Error                | Description  | Solution  |
|------------|----------------------|--------------|-----------|
| 2 | BadValue | L’une des causes courantes est que le chemin de l’index correspondant à l’élément order-by spécifié est exclu ou la requête order by n’a pas d’index composite correspondant à partir duquel elle peut être traitée. La requête demande un tri sur un champ qui n’est pas indexé. | Créez un index correspondant (ou un index composite) pour la requête de tri tentée. |
| 2 | La transaction n’est pas active | La transaction multidocument a dépassé la limite fixée à 5 secondes. | Faites une nouvelle tentative ou limitez l’étendue des opérations au sein de la transaction multidocument pour la terminer dans le délai de 5 secondes. |
| 13 | Non autorisé | La requête ne dispose pas des autorisations nécessaires pour aboutir. | Veillez à utiliser les bonnes clés.  |
| 26 | NamespaceNotFound | La base de données ou la collection référencée dans la requête est introuvable. | Assurez-vous que le nom de votre base de données/collection correspond exactement au nom de votre requête.|
| 50 | ExceededTimeLimit | La requête a dépassé le délai d’exécution de 60 secondes. |  Cette erreur peut avoir plusieurs causes. L’une d’elles est que la capacité actuelle d’unités de requête allouées n’est pas suffisante pour traiter la requête. Ce problème peut être résolu en augmentant le nombre d’unités de requête de cette collection ou base de données. Dans d’autres cas, il est possible de contourner cette erreur en fractionnant une requête volumineuse en plusieurs requêtes plus petites. Toute nouvelle tentative d’écriture ayant reçu cette erreur peut entraîner une écriture en double. <br><br>Si vous essayez de supprimer de grandes quantités de données sans incidence sur les unités de requête : <br>– Utilisez la TTL (basée sur l’horodatage) : [Faire expirer des données avec l’API Azure Cosmos DB pour MongoDB](mongodb-time-to-live.md) <br>– Utilisez la taille de curseur/de lot pour effectuer la suppression. Vous pouvez récupérer un seul document à la fois et le supprimer à l’aide d’une boucle. Cela vous permet de supprimer petit à petit des données sans affecter votre application de production.|
| 61 | ShardKeyNotFound | Le document de votre requête ne contient pas la clé de partition de la collection (clé de partition Azure Cosmos DB). | Vérifiez que la clé de partition de la collection est utilisée dans la requête.|
| 66 | ImmutableField | La requête tente de modifier un champ immuable | Les champs « _id » sont immuables. Vérifiez que votre requête ne tente pas de mettre à jour ce champ ou le champ de clé de partition. |
| 67 | CannotCreateIndex | La requête de création d’un index ne peut pas être effectuée. | Jusqu’à 500 index de champ uniques peuvent être créés dans un conteneur. Jusqu’à huit champs peuvent être inclus dans un index composé (les index composés sont pris en charge dans la version 3.6+). |
| 112 | WriteConflict | La transaction multidocument a échoué en raison d’une transaction multidocument en conflit | Faites une nouvelle tentative jusqu’à ce qu’elle aboutisse. |
| 115 | CommandNotSupported | La requête tentée n’est pas prise en charge. | Des détails supplémentaires doivent être fournis dans l’erreur. Si cette fonctionnalité est importante pour vos déploiements, créez un ticket de support dans le [portail Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) et l’équipe Azure Cosmos DB vous recontactera. |
| 11000 | DuplicateKey | La clé de partition (clé de partition Azure Cosmos DB) du document que vous insérez existe déjà dans la collection ou une contrainte de champ d’index unique a été violée. | Utilisez la fonction update () pour mettre à jour un document existant. Si la contrainte de champ d’index unique a été violée, insérez ou mettez à jour le document avec une valeur de champ non présente dans la partition. Une autre option consiste à utiliser un champ contenant une combinaison des champs d’ID et de clé de partition. |
| 16500 | TooManyRequests  | Le nombre total d’unités de requête consommées est supérieur au taux d’unités de requête provisionné pour la collection et a été limité. | Adaptez le débit assigné à un conteneur ou à un ensemble de conteneurs à partir du portail Azure ou renouvelez l’opération. Si vous activez SSR (nouvelle tentative côté serveur), Azure Cosmos DB retente automatiquement les requêtes qui échouent en raison de cette erreur. |
| 16501 | ExceededMemoryLimit | En tant que service multi-locataire, l’opération a dépassé l’allocation de mémoire du client. Cela s’applique uniquement à l’API Azure Cosmos DB pour MongoDB version 3.2. | Réduisez l’étendue de l’opération en fixant un critère de requête plus restrictif, ou contactez le support technique via le [portail Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Exemple : `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 40324 | Nom d’étape de pipeline non reconnu. | Le nom d’étape de votre requête de pipeline d’agrégation n’a pas été reconnu. | Assurez-vous que tous les noms de pipeline d’agrégation sont valides dans votre requête. |
| - | Problèmes relatifs à la version câblée de MongoDB | Les anciennes versions des pilotes MongoDB ne sont pas en mesure de détecter le nom du compte Azure Cosmos dans les chaînes de connexion. | Ajoutez `appName=@accountName@` à la fin de votre chaîne de connexion, où `accountName` correspond au nom de votre compte Azure Cosmos DB. |
| - | Problèmes de réseau du client MongoDB (tels que les exceptions socket ou endOfStream)| La requête réseau a échoué. Cela est fréquemment dû à une connexion TCP inactive que le client MongoDB tente d’utiliser. Les pilotes MongoDB utilisent souvent le regroupement de connexions, ce qui entraîne l’utilisation d’une connexion aléatoire choisie à partir du pool. En règle générale, les connexions inactives expirent au niveau d’Azure Cosmos DB après quatre minutes. | Vous pouvez retenter les requêtes ayant échoué dans votre code d’application, modifier les paramètres de votre client MongoDB (pilote) pour déconnecter les connexions TCP inactives avant la fenêtre de délai d’expiration de quatre minutes, ou configurer vos paramètres `keepalive` de système d’exploitation pour maintenir les connexions TCP dans un état actif.<br><br>Afin d’éviter des messages liés à la connectivité, modifiez la chaîne de connexion pour définir `maxConnectionIdleTime` sur une à deux minutes.<br>– Pilote Mongo : configurez `maxIdleTimeMS=120000` <br>– Node.JS : configurez `socketTimeoutMS=120000`, `autoReconnect` = true, `keepAlive` = true, `keepAliveInitialDelay` = 3 minutes
| - | L’interpréteur de commandes Mongo ne fonctionne pas dans le portail Azure | Quand l’utilisateur tente d’ouvrir un interpréteur de commandes Mongo, rien ne se produit et l’onglet reste vide.  | Vérifiez le pare-feu. Le pare-feu n’est pas pris en charge avec l’interpréteur de commandes Mongo dans le portail Azure. <br>- Installez l’interpréteur de commandes Mongo sur l’ordinateur local dans les règles de pare-feu <br>- Utilisez l’interpréteur de commandes Mongo existant
| - | Impossible de se connecter avec la chaîne de connexion  | La chaîne de connexion a changé lors de la mise à niveau de la version 3.2 vers la version 3.6 | Notez que lors de l’utilisation de l’API Azure Cosmos DB pour les comptes MongoDB, le point de terminaison de la version 3.6 des comptes est au format `*.mongo.cosmos.azure.com`, tandis que celui de la version 3.2 des comptes est au format `*.documents.azure.com`.  

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [utiliser Studio 3T](mongodb-mongochef.md) avec l’API Azure Cosmos DB pour MongoDB.
- Découvrez comment [utiliser Robo 3T](mongodb-robomongo.md) avec l’API Azure Cosmos DB pour MongoDB.
- Explorez les [exemples](mongodb-samples.md) MongoDB avec l’API Azure Cosmos DB pour MongoDB.
