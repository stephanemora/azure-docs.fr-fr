---
title: Utiliser des étiquettes de requête dans SQL Synapse
description: Cet article contient des conseils essentiels pour l’utilisation d’étiquettes de requête dans SQL Synapse.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 47b476cbc6997ca5ec63968bdc269e2273662100
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427077"
---
# <a name="use-query-labels-in-synapse-sql"></a>Utiliser des étiquettes de requête dans SQL Synapse
Cet article contient des conseils essentiels pour l’utilisation d’étiquettes de requête dans SQL Synapse.

> [!NOTE]
> SQL à la demande (préversion) ne prend pas en charge l’étiquetage des requêtes.

## <a name="what-are-query-labels"></a>Ce que sont les étiquettes de requête
Un pool SQL prend en charge le concept d’étiquette de requête. Avant de poursuivre l’étude, examinons un exemple :

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

La dernière ligne balise la chaîne « Mon étiquette de requête » dans la requête. Cette balise est particulièrement utile, car cette étiquette peut être interrogée via des DMV. L’interrogation d’étiquettes fournit un mécanisme de localisation des requêtes problématiques, qui permet d’identifier la progression via un processus ELT exécuté.

Une bonne convention d’affectation de noms est très utile. Par exemple, démarrer l’étiquette par PROJET, PROCÉDURE, INSTRUCTION ou COMMENTAIRE identifie une requête de manière unique dans l’ensemble du code lors du contrôle de la source.

La requête suivante utilise une vue de gestion dynamique pour rechercher par étiquette :

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Vous devez impérativement placer l’étiquette du mot entre crochets ou guillemets doubles lors de l’interrogation. Étiquette est un mot réservé et provoque une erreur lorsqu’il n’est pas délimité. 
> 
> 

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des conseils supplémentaires, consultez la [vue d’ensemble du développement](develop-overview.md).


