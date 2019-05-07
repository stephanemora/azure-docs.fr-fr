---
title: Récupération accélérée de base de données - Azure SQL Database | Microsoft Docs
description: Azure SQL Database dispose d’une nouvelle fonctionnalité qui permet une récupération de base de données rapide et cohérente, l’annulation de transaction instantanée et la troncation agressive du journal pour les bases de données uniques et les bases de données mises en pool dans Azure SQL Database, et les bases de données dans Azure SQL Data Warehouse.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 77bc33747964a5f4ee1a67aba777dc3ed76b9a51
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65073468"
---
# <a name="accelerated-database-recovery"></a>Récupération de la base de données accélérée

La **récupération de base de données accélérée** est une nouvelle fonctionnalité du moteur de base de données SQL qui améliore considérablement la disponibilité des bases de données, en particulier en présence de transactions d’une durée d’exécution longue, grâce à une nouvelle conception du processus de récupération du moteur de base de données SQL. La récupération de base de données accélérée est disponible pour les bases de données uniques et les bases de données mises en pool dans Azure SQL Database, et les bases de données dans Azure SQL Data Warehouse. Les principaux avantages de la récupération de base de données accélérée sont :

- **Récupération rapide et cohérente des bases de données**

  Avec la récupération de base de données accélérée, les transactions longues n’affectent pas le temps de récupération global, permettant ainsi une rapide et cohérente des bases de données, quelle que soit le nombre ou la taille des transactions actives dans le système.

- **Annulation instantanée des transactions**

  Avec la récupération de base de données accélérée, l’annulation des transactions est instantanée, quel que soit le temps pendant lequel une transaction a été active ou le nombre de mises à jour effectuées.

- **Troncation agressive du journal**

  Avec la récupération de base de données accélérée, le journal des transactions est tronqué de façon agressive, même en présence de transactions longues actives, ce qui l’empêche de croître d’une façon hors de contrôle.

## <a name="the-current-database-recovery-process"></a>Le processus actuel de récupération de base de données

La récupération de base de données dans SQL Server suit le modèle de récupération [ARIES](https://people.eecs.berkeley.edu/~brewer/cs262/Aries.pdf) et est constitué de trois phases, qui sont illustrées dans le diagramme suivant et expliquées plus loin de façon plus détaillée.

![processus de récupération actuel](./media/sql-database-accelerated-database-recovery/current-recovery-process.png)

- **Phase d’analyse**

  Transférer l’analyse du journal des transactions à partir du début du dernier point de contrôle réussi (ou la page de modifications plus ancien LSN) jusqu'à la fin, pour déterminer l’état de chaque transaction à l’heure de qu'arrêt de SQL Server.

- **Phase de restauration par progression**

  Transférer l’analyse du journal des transactions à partir de la plus ancienne transaction non validée jusqu'à la fin, pour rétablir la base de données à l’état qu’il était au moment de l’incident en effectuant les opérations validées de nouveau.

- **Phase d’annulation**

  Pour chaque transaction qui était active au moment du plantage, parcourt le journal vers l’arrière, en annulant les opérations effectuées par cette transaction.

Avec cette conception, le temps nécessaire au moteur de base de données SQL pour récupérer à partir d’un redémarrage inattendu est (à peu près) proportionnel à la taille de la transaction active la plus longue dans le système au moment du plantage. La récupération nécessite une annulation de toutes les transactions incomplètes. La quantité de temps nécessaire est proportionnelle au travail que la transaction a effectué et au temps pendant lequel elle a été active. Par conséquent, le processus de récupération de SQL Server peut prendre beaucoup de temps en présence de transactions longues (comme des grandes opérations d’insertion en bloc ou des opérations de génération d’index sur une grande table).

De plus, annuler/défaire une grande transaction selon cette conception peut également prendre beaucoup de temps, car cela utilise la même phase d’annulation que celle décrite plus haut.

En outre, le moteur de base de données SQL ne peut pas tronquer le journal des transactions quand il y a des transactions longues, car les enregistrements correspondants du journal sont nécessaires pour les processus de récupération et d’annulation. Grâce à cette conception du moteur de base de données SQL, certains clients confronté au problème que la taille du journal des transactions devient très volumineux et consomme de grandes quantités d’espace disque.

## <a name="the-accelerated-database-recovery-process"></a>Le processus de récupération de base de données accélérée

La récupération de base de données accélérée résout les problèmes évoqués plus haut en redéfinissant complètement le processus de récupération du moteur de base de données SQL pour :

- La rendre instantanée/avec une durée constante en évitant de devoir analyser le journal à partir de/jusqu’au début de la transaction active la plus ancienne. Avec la règle ADR, le journal des transactions est traité seulement à partir du dernier point de contrôle réussi (ou de la page de modifications plus ancien numéro de séquence de journal (LSN)). Par conséquent, le temps de récupération n’est pas affecté par les transactions longues.
- Réduire au minimum l’espace du journal des transactions, car il n’est plus nécessaire de traiter le journal pour toute la transaction. Par conséquent, le journal des transactions peut être tronqué de façon agressive au fil de la réalisation des points de contrôle et des sauvegardes.

À un niveau élevé, ADR réalise récupération rapide de base de données par le contrôle de version, toutes les modifications de base de données physique et seules opérations logiques annulation, qui sont limités et ne peuvent être annulée presque instantanément. Les transactions qui étaient actives au moment d’un plantage sont marquées comme étant abandonnées et par conséquent, toutes les versions générées par ces transactions peuvent être ignorées par les requêtes utilisateur simultanées.

Le processus de récupération de la récupération de base de données accélérée a les trois mêmes phases que le processus de récupération actuel. Le fonctionnement de ces phases avec la récupération de base de données accélérée est illustré dans le diagramme suivant et expliqué plus en détails après celui-ci.

![Processus de récupération de la récupération de base de données accélérée](./media/sql-database-accelerated-database-recovery/adr-recovery-process.png)

- **Phase d’analyse**

  Le processus reste identique à aujourd'hui avec l’ajout de reconstruction sLog et la copie des enregistrements de journal pour les opérations sans version.
  
- Phase de **restauration par progression**

  Divisée en deux phases (P)
  - Phase 1

      Restauration par progression à partir du sLog (de la transaction non validée la plus ancienne jusqu’au dernier point de contrôle). La restauration par progression est une opération rapide, car elle doit traiter seulement quelques enregistrements provenant du sLog.
      
  - Phase 2

     La restauration par progression à partir du journal des transactions démarre à partir du dernier point de contrôle (au lieu de la transaction non validée la plus ancienne)
     
- **Phase d’annulation**

   La phase d’annulation avec la récupération de base de données accélérée se termine quasi instantanément, en utilisant le sLog pour annuler les opérations sans version et le magasin de versions persistantes (PVS, Persisted Version Store) avec le rétablissement logique pour effectuer l’annulation basée sur la version au niveau de la ligne.

## <a name="adr-recovery-components"></a>Composants de la récupération de base de données accélérée

Les quatre composants clés de la récupération de base de données accélérée sont :

- **Magasin de versions persistantes**

  Le magasin de versions persistantes est un nouveau mécanisme du moteur de base de données SQL pour conserver les versions des lignes générées dans la base de données elle-même, au lieu du magasin de versions `tempdb` traditionnel. Le magasin de versions persistantes permet l’isolement des ressources et améliore la disponibilité des bases de données secondaires accessibles en lecture.

- **Rétablissement logique**

  Rétablissement logique n’est responsable de l’exécution de niveau ligne basée sur la version annulation - fournissant une annulation de la transaction d’instantané et l’annulation pour toutes les opérations avec version gérée par le processus asynchrone.

  - Effectue le suivi des transactions abandonnées
  - Effectue l’annulation avec le magasin de versions persistantes pour toutes les transactions utilisateur
  - Libère tous les verrous immédiatement après l’abandon de la transaction

- **sLog**

  sLog est un flux secondaire de journalisation en mémoire qui stocke des enregistrements du journal pour les opérations sans version (comme l’invalidation du cache de métadonnées, les acquisitions de verrous, etc.). Le sLog a les caractéristiques suivantes :

  - Volume faible et en mémoire
  - Stocké sur disque via une sérialisation pendant le processus du point de contrôle
  - Tronqué périodiquement au fil de la validation des transactions
  - Accélère la restauration par progression et l’annulation en traitant seulement les opérations sans version  
  - Permet la troncation agressive du journal des transactions en conservant seulement les enregistrements nécessaires dans le journal

- **Nettoyeur**

  Le nettoyeur est le processus asynchrone qui se réveille périodiquement et nettoie les versions des pages qui ne sont pas nécessaires.

## <a name="who-should-consider-accelerated-database-recovery"></a>Cas d’utilisation de la récupération de base de données accélérée

Les types suivants de clients doivent envisager l’utilisation de la récupération de base de données accélérée :

- Les clients qui ont des charges de travail avec des transactions longues.
- Les clients qui ont rencontré des cas où des transactions actives provoquent un accroissement significatif du journal des transactions.  
- Les clients ayant subi de longues périodes d’indisponibilité de la base de données en raison de la longueur de la récupération de SQL Server (comme un redémarrage manuel de SQL Server ou l’annulation manuelle de transactions).

