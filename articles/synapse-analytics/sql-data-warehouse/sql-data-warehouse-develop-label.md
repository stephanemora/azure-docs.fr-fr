---
title: Utiliser des étiquettes pour instrumenter des requêtes
description: Conseils relatifs à l’utilisation de libellés pour instrumenter les requêtes d’un pool SQL Synapse dans le cadre du développement de solutions.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5e2cd03ae878e80139a7f7a8ba67cef15b24d571
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633489"
---
# <a name="using-labels-to-instrument-queries-in-synapse-sql-pool"></a>Utiliser des étiquettes pour instrumenter des requêtes dans un pool SQL Synapse

Cet article inclut des conseils pour développer des solutions utilisant des étiquettes afin d’instrumenter les requêtes d’un pool SQL.

Conseils relatifs à l’utilisation de libellés pour instrumenter des requêtes dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions.

## <a name="what-are-labels"></a>Que sont les étiquettes ?

Un pool SQL prend en charge le concept de « libellé de requête ». Avant de poursuivre l’étude, examinons un exemple :

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

La dernière ligne balise la chaîne « Mon étiquette de requête » dans la requête. Cette balise est utile car l’étiquette peut être interrogée via des DMV.

L’interrogation d’étiquettes fournit un mécanisme de localisation des requêtes à problème qui permet d’identifier la progression via une ELT exécutée.

Une convention d’affectation de noms efficace s’avère très utile. Par exemple, démarrer l’étiquette par PROJET, PROCÉDURE, INSTRUCTION ou COMMENTAIRE identifie une requête de manière unique dans l’ensemble du code lors du contrôle de la source.

La requête suivante utilise une vue de gestion dynamique pour rechercher par étiquette :

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Vous devez impérativement placer l’étiquette du mot entre crochets ou guillemets doubles lors de l’interrogation. Étiquette est un mot réservé et provoque une erreur lorsqu’il n’est pas délimité.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des conseils supplémentaires, consultez la [vue d’ensemble du développement](sql-data-warehouse-overview-develop.md).
