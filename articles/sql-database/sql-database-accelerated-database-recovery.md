---
title: Récupération accélérée de base de données - Azure SQL Database | Microsoft Docs
description: Azure SQL Database dispose d’une nouvelle fonctionnalité qui permet une récupération de base de données rapide et cohérente, l’annulation de transaction instantanée et la troncation agressive du journal pour les bases de données uniques, les pools élastiques et Azure SQL Data Warehouse.
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 10/11/2018
ms.openlocfilehash: 49ef31996cb9c55ed244202a85e123faf52fbd2a
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49649764"
---
# <a name="accelerated-database-recovery-preview"></a>Récupération de base de données accélérée (préversion)

La **récupération de base de données accélérée** est une nouvelle fonctionnalité du moteur de base de données SQL qui améliore considérablement la disponibilité des bases de données, en particulier en présence de transactions d’une durée d’exécution longue, grâce à une nouvelle conception du processus de récupération du moteur de base de données SQL. La récupération de base de données accélérée est actuellement disponible pour les bases de données uniques, les pools élastiques et Azure SQL Data Warehouse. Les principaux avantages de la récupération de base de données accélérée sont :

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

  Analyse vers l’avant du journal des transactions à partir du début du dernier point de contrôle réussi (ou de la page LSN la plus ancienne) jusqu’à la fin, pour déterminer l’état de chaque transaction au moment de l’arrêt de SQL Server.

- **Phase de restauration par progression**

  Analyse vers l’avant du journal des transactions depuis la transaction non validée la plus ancienne jusqu’à la fin, pour rétablir la base de données à l’état où elle était au moment du plantage en refaisant toutes les opérations.

- **Phase d’annulation**

  Pour chaque transaction qui était active au moment du plantage, parcourt le journal vers l’arrière, en annulant les opérations effectuées par cette transaction.

Avec cette conception, le temps nécessaire au moteur de base de données SQL pour récupérer à partir d’un redémarrage inattendu est (à peu près) proportionnel à la taille de la transaction active la plus longue dans le système au moment du plantage. La récupération nécessite une annulation de toutes les transactions incomplètes. La quantité de temps nécessaire est proportionnelle au travail que la transaction a effectué et au temps pendant lequel elle a été active. Par conséquent, le processus de récupération de SQL Server peut prendre beaucoup de temps en présence de transactions longues (comme des grandes opérations d’insertion en bloc ou des opérations de génération d’index sur une grande table).

De plus, annuler/défaire une grande transaction selon cette conception peut également prendre beaucoup de temps, car cela utilise la même phase d’annulation que celle décrite plus haut.

En outre, le moteur de base de données SQL ne peut pas tronquer le journal des transactions quand il y a des transactions longues, car les enregistrements correspondants du journal sont nécessaires pour les processus de récupération et d’annulation. Le résultat de cette conception du moteur de base de données SQL est que certains clients sont confrontés à ce problème que la taille du journal des transactions devient très grande et consomme de très grandes quantités d’espace pour le journal.

## <a name="the-accelerated-database-recovery-process"></a>Le processus de récupération de base de données accélérée

La récupération de base de données accélérée résout les problèmes évoqués plus haut en redéfinissant complètement le processus de récupération du moteur de base de données SQL pour :

- La rendre instantanée/avec une durée constante en évitant de devoir analyser le journal à partir de/jusqu’au début de la transaction active la plus ancienne. Avec la récupération de base de données accélérée, le journal des transactions est traité seulement à partir du dernier point de contrôle réussi (ou du numéro séquentiel dans le journal de la page endommagée la plus ancienne). Par conséquent, le temps de récupération n’est pas affecté par les transactions longues.
- Réduire au minimum l’espace du journal des transactions, car il n’est plus nécessaire de traiter le journal pour toute la transaction. Par conséquent, le journal des transactions peut être tronqué de façon agressive au fil de la réalisation des points de contrôle et des sauvegardes.

Globalement, la récupération de base de données accélérée effectue une récupération rapide d’une base de données en gérant des versions de toutes les modifications de la base de données physique et en annulant seulement les opérations logiques, qui sont limitées et peuvent être annulées quasi instantanément. Les transactions qui étaient actives au moment d’un plantage sont marquées comme étant abandonnées et par conséquent, toutes les versions générées par ces transactions peuvent être ignorées par les requêtes utilisateur simultanées.

Le processus de récupération de la récupération de base de données accélérée a les trois mêmes phases que le processus de récupération actuel. Le fonctionnement de ces phases avec la récupération de base de données accélérée est illustré dans le diagramme suivant et expliqué plus en détails après celui-ci.

![Processus de récupération de la récupération de base de données accélérée](./media/sql-database-accelerated-database-recovery/adr-recovery-process.png)

- **Phase d’analyse**

  Le processus reste identique à celui d’aujourd’hui, avec en plus la reconstruction du sLog (journal secondaire) et la copie des enregistrements du journal pour les opérations sans gestion de version.
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

  Le rétablissement logique est le processus asynchrone responsable de l’exécution des annulations basées sur la version au niveau de la ligne, qui permet l’annulation instantanée des transactions et l’annulation des opérations avec version.

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

## <a name="to-enable-adr-during-this-preview-period"></a>Pour activer la récupération de base de données accélérée pendant cette période de préversion

Pendant la période de préversion, pour activer cette fonctionnalité, envoyez un e-mail à [adr@microsoft.com](mailto:adr@microsoft.com) pour en savoir plus et essayer la récupération de base de données accélérée. Dans l’e-mail, incluez le nom de votre serveur logique (pour les bases de données uniques, les pools élastiques et Azure Data Warehouse). Cette fonctionnalité étant en préversion, votre serveur de test ne doit pas être un serveur de production.
