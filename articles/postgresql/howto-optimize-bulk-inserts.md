---
title: Optimiser les insertions en bloc - Azure Database pour PostgreSQL - Serveur unique
description: Cet article explique comment vous pouvez optimiser des insertions en bloc dans une instance Azure Database pour PostgreSQL (serveur unique).
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 4c4bac16917be0064ebb111328753d378d462a2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770133"
---
# <a name="optimize-bulk-inserts-and-use-transient-data-on-an-azure-database-for-postgresql---single-server"></a>Optimiser les insertions en bloc et utiliser les données temporaires dans une instance Azure Database pour PostgreSQL (serveur unique) 
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
