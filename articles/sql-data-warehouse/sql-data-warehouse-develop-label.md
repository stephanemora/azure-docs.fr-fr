---
title: Utilisation des étiquettes pour instrumenter des requêtes dans SQL Data Warehouse | Microsoft Docs
description: Conseils relatifs à l’utilisation de libellés pour instrumenter des requêtes dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: ee991fdfcd93ea064d1205d61d07adf377cce667
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480033"
---
# <a name="using-labels-to-instrument-queries-in-azure-sql-data-warehouse"></a>Utilisation des libellés pour instrumenter des requêtes dans SQL Data Warehouse
Conseils relatifs à l’utilisation de libellés pour instrumenter des requêtes dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions.


## <a name="what-are-labels"></a>Que sont les étiquettes ?
SQL Data Warehouse prend en charge le concept de « libellé de requête ». Avant de poursuivre l’étude, examinons un exemple :

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

La dernière ligne balise la chaîne « Mon étiquette de requête » dans la requête. Cette balise est particulièrement utile, car cette étiquette peut être interrogée via des DMV. L’interrogation d’étiquettes fournit un mécanisme de localisation des requêtes à problème qui permet d’identifier la progression via une ELT exécutée.

Une convention d’affectation de noms efficace s’avère très utile. Par exemple, démarrer l’étiquette par PROJET, PROCÉDURE, INSTRUCTION ou COMMENTAIRE permet d’identifier une requête de manière unique dans l’ensemble du code lors du contrôle de la source.

La requête suivante utilise une vue de gestion dynamique pour rechercher par étiquette.

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
Pour obtenir des conseils supplémentaires, consultez la [vue d’ensemble du développement](sql-data-warehouse-overview-develop.md).


