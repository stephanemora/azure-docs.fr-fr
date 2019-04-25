---
title: Optimiser les insertions en bloc sur un serveur Azure Database pour PostgreSQL
description: Cet article explique comment vous pouvez optimiser des insertions en bloc dans un serveur Azure Database pour PostgreSQL.
author: WenJason
ms.author: v-jay
ms.service: postgresql
ms.topic: conceptual
origin.date: 10/22/2018
ms.date: 03/04/2019
ms.openlocfilehash: a82984ce4c2a2e44306abaa63265e0c25cc6ace4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60422115"
---
# <a name="optimize-bulk-inserts-and-use-transient-data-on-an-azure-database-for-postgresql-server"></a>Optimiser les insertions en bloc et utiliser les données temporaires sur un serveur Azure Database pour PostgreSQL 
Cet article explique comment vous pouvez optimiser des insertions en bloc et utiliser les données temporaires dans un serveur Azure Database pour PostgreSQL.

## <a name="use-unlogged-tables"></a>Utiliser des tables non journalisées
Si vos opérations de charge de travail impliquent des données temporaires ou insèrent des jeux de données volumineux en bloc, envisagez d’utiliser des tables non journalisées.

Les tables non journalisées constituent une fonctionnalité PostgreSQL qui peut être utilisée efficacement pour optimiser les insertions en bloc. PostgreSQL utilise la journalisation Write-Ahead Logging (WAL). Elle offre l’atomicité et la durabilité, par défaut. L’atomicité, la cohérence, l’isolation et la durabilité sont les propriétés d’ACID. 

L’insertion dans une table non journalisée signifie que PostgreSQL effectue des insertions sans écrire dans le journal des transactions, qui est lui-même une opération d’E/S. Par conséquent, ces tables sont nettement plus rapides que des tables ordinaires.

Utilisez les options suivantes pour créer une table non journalisée :
- Créez une nouvelle table non journalisée en utilisant la syntaxe `CREATE UNLOGGED TABLE <tableName>`.
- Convertissez une table journalisée existante en table non journalisée en utilisant la syntaxe `ALTER TABLE <tableName> SET UNLOGGED`.  

Pour inverser le processus, utilisez la syntaxe `ALTER TABLE <tableName> SET LOGGED`.

## <a name="unlogged-table-tradeoff"></a>Compromis de table non journalisée
Les tables non journalisées ne sont pas sécurisées en cas d’incident. Une table non journalisée est automatiquement tronquée après un incident ou un arrêt brutal. Le contenu d’une table non journalisée n’est pas non plus répliqué vers les serveurs de secours. Tous les index créés sur une table non journalisée ne sont pas journalisés automatiquement non plus. Une fois l’opération d’insertion terminée, convertissez la table en table journalisée pour que l’insertion soit durable.

Certaines charges de travail de clients ont connu une amélioration des performances d’environ 15 à 20 % lors de l’utilisation de tables non journalisées.

## <a name="next-steps"></a>Étapes suivantes
Passez en revue votre charge de travail pour les utilisations de données temporaires et les insertions en bloc. Consultez la documentation PostgreSQL suivante :
 
- [Créer des commandes de Table SQL](https://www.postgresql.org/docs/current/static/sql-createtable.html)