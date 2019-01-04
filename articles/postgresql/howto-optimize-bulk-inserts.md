---
title: Optimiser les insertions en bloc sur un serveur Azure Database pour PostgreSQL
description: Cet article explique comment vous pouvez optimiser des insertions en bloc dans un serveur Azure Database pour PostgreSQL.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: 9d2bfcddc649e4fff68bdba49df0945e88067036
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53545228"
---
# <a name="optimizing-bulk-inserts-and-use-of-transient-data-on-azure-database-for-postgresql-server"></a>Optimisation d’insertions en bloc et utilisation de données temporaires sur un serveur Azure Database pour PostgreSQL 
Cet article explique comment vous pouvez optimiser des insertions en bloc et décrit l’utilisation de données temporaires dans un serveur Azure Database pour PostgreSQL.

## <a name="using-unlogged-tables"></a>Utilisation de tables non journalisées
Pour les clients dont les opérations de charge de travail impliquent des données temporaires ou qui insèrent des jeux de données volumineux en bloc, envisagez d’utiliser des tables non journalisées.

Les tables non journalisées constituent une fonctionnalité PostgreSQL qui peut être utilisée efficacement pour optimiser les insertions en bloc. PostgreSQL utilise Write-Ahead Logging (WAL), qui fournit l’atomicité et la durabilité de deux des propriétés ACID par défaut. L’insertion dans une table non journalisée signifierait que PostgreSQL ferait des insertions sans écrire dans le journal des transactions, ce qui constitue en soi une opération d’E-S, rendant ces tables considérablement plus rapides que les tables ordinaires.

Vous trouverez ci-dessous les options de création d’une table non journalisée :
- Créez une nouvelle table non journalisée en utilisant la syntaxe : `CREATE UNLOGGED TABLE <tableName>`
- Convertissez une table journalisée existante en table non journalisée en utilisant la syntaxe : `ALTER <tableName> SET UNLOGGED`.  Cette opération peut être inversée en utilisant la syntaxe : `ALTER <tableName> SET LOGGED`

## <a name="unlogged-table-tradeoff"></a>Compromis de table non journalisée
Les tables non journalisées ne sont pas sécurisées en cas d’incident. Une table non journalisée est automatiquement tronquée après un incident ou un arrêt brutal. Le contenu d’une table non journalisée n’est pas non plus répliqué vers les serveurs de secours. Tous les index créés sur une table non journalisée ne sont pas journalisés automatiquement non plus.  Une fois l’opération d’insertion terminée, vous pouvez convertir la table en table journalisée pour que l’insertion soit durable.

Cependant, sur certaines charges de travail de nos clients, nous avons constaté une amélioration des performances d’environ 15 à 20 % lorsque nous utilisons des tables non journalisées.

## <a name="next-steps"></a>Étapes suivantes
Passez en revue votre charge de travail pour les utilisations de données temporaires et les insertions en bloc.  

Consultez la documentation PostgreSQL suivante - [Commandes SQL de création de table](https://www.postgresql.org/docs/current/static/sql-createtable.html).