---
title: Utiliser des étiquettes de requête dans SQL Synapse
description: Cet article contient des conseils essentiels pour l’utilisation d’étiquettes de requête dans SQL Synapse.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 0b2c03c5c7ea8c65cb1cde3cbdb73b6bb838dc06
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324462"
---
# <a name="use-query-labels-in-synapse-sql"></a>Utiliser des étiquettes de requête dans SQL Synapse

Cet article contient des conseils essentiels pour l’utilisation d’étiquettes de requête dans SQL Synapse.

> [!NOTE]
> Le pool SQL serverless (préversion) ne prend pas en charge l’étiquetage des requêtes.

## <a name="what-are-query-labels"></a>Ce que sont les étiquettes de requête

Le pool SQL dédié prend en charge le concept d’étiquette de requête. Avant de poursuivre l’étude, examinons un exemple :

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

La dernière ligne balise la chaîne « Mon étiquette de requête » dans la requête. Cette étiquette est utile, car le libellé peut être interrogé via des DMV. L’interrogation d’étiquettes fournit un mécanisme de localisation des requêtes problématiques, qui permet d’identifier la progression via un processus ELT exécuté.

Les conventions de nommage sont très utiles. Par exemple, démarrer l’étiquette par PROJET, PROCÉDURE, INSTRUCTION ou COMMENTAIRE identifie une requête de manière unique dans l’ensemble du code lors du contrôle de la source.

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


