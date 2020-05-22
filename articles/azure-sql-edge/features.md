---
title: Fonctionnalités prises en charge par Azure SQL Edge (préversion)
description: En savoir plus sur les fonctionnalités prises en charge par Azure SQL Edge (préversion)
keywords: présentation de SQL Edge, qu'est-ce que SQL Edge, vue d'ensemble de SQL Edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 7d33c2bef1cd0f7bfab4ec3c09f16c049881d1cd
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594618"
---
# <a name="supported-features-of-azure-sql-edge-preview"></a>Fonctionnalités prises en charge par Azure SQL Edge (préversion) 

Cet article présente les fonctionnalités prises en charge par Azure SQL Edge. Azure SQL Edge repose sur la dernière version du moteur de base de données Microsoft SQL Server pour Linux, et prend en charge une partie des fonctionnalités prises en charge par SQL Server 2019 pour Linux, en plus de certaines fonctionnalités qui ne sont actuellement pas prises en charge ou disponibles dans SQL Server 2019 pour Linux ou Windows. Pour obtenir la liste complète des fonctionnalités prises en charge dans SQL Server pour Linux, consultez [Éditions et fonctionnalités de SQL Server 2019 pour Linux prises en charge](https://docs.microsoft.com/sql/linux/sql-server-linux-editions-and-components-2019). Pour obtenir la liste des éditions et fonctionnalités de SQL Server pour Windows prises en charge, consultez [Éditions et fonctionnalités de SQL Server 2019 (15.x) prises en charge](https://docs.microsoft.com/sql/sql-server/editions-and-components-of-sql-server-version-15).

> [!NOTE]
> Azure SQL Edge est actuellement disponible en préversion et, en tant que tel, ne doit PAS être utilisé dans des environnements de production. Microsoft peut recommander l'exécution d'Azure SQL Edge dans des environnements de production après validation du déploiement et des scénarios de cas d'usage.

## <a name="azure-sql-edge-editions"></a>Éditions d'Azure SQL Edge

Deux éditions ou plans logiciels d'Azure SQL Edge sont disponibles. Ces éditions disposent des mêmes fonctionnalités et ne diffèrent que par leurs droits d'utilisation et par la capacité d'UC/de mémoire à laquelle elles peuvent accéder sur le système hôte.

   |**Planification**  |**Description**  |
   |---------|---------|
   |Azure SQL Edge Développeur  |  Référence SKU de développement uniquement, chaque conteneur Azure SQL Edge Développeur est limité à 4 cœurs et à 32 Go de mémoire.  |
   |Azure SQL Edge    |  Référence SKU de production, chaque conteneur Azure SQL Edge est limité à 8 cœurs et à 64 Go de mémoire.  |

## <a name="operating-system"></a>Système d’exploitation

Les conteneurs Azure SQL Edge reposent actuellement sur Ubuntu (16.04 et 18.04) et, en tant que tels, sont uniquement pris en charge par les hôtes Docker exécutant Ubuntu 16.04 et 18.04. Azure SQL Edge peut également s'exécuter sur d'autres hôtes de système d'exploitation, par exemple, d'autres distributions de Linux ou sous Windows (via Docker CE ou Docker EE), mais ces configurations ne sont pas testées de manière approfondie par Microsoft.

Azure SQL Edge n'est actuellement pris en charge que pour les déploiements via Azure IoT Edge. Pour plus d'informations sur les systèmes pris en charge par Azure IoT Edge, consultez [Systèmes pris en charge par Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/support).

La configuration recommandée pour exécuter Azure SQL Edge sous Windows consiste à configurer une machine virtuelle Ubuntu sur l'hôte Windows, puis à exécuter SQL Edge sur la machine virtuelle Linux.

## <a name="hardware-support"></a>Prise en charge matérielle

Azure SQL Edge requiert un processeur 64 bits, qui peut être de marque Intel, AMD ou ARM, avec au moins 1 processeur et 1 Go de RAM sur l'hôte. L'empreinte mémoire au démarrage d'Azure SQL Edge est proche de 500 Mo. Par conséquent, un supplément de mémoire est nécessaire pour les autres modules IoT Edge exécutés sur le périphérique de périmètre.

## <a name="azure-sql-edge-components"></a>Composants Azure SQL Edge

Azure SQL Edge prend en charge le moteur de base de données, mais pas les autres composants disponibles avec SQL Server 2019 pour Windows ou SQL Server 2019 pour Linux. Plus précisément, Azure SQL Edge ne prend pas en charge les composants SQL Server tels que Analysis Services, Reporting Services, Integration Services, Master Data Services, Machine Learning Services (dans la base de données) et Machine Learning Server (autonome).

## <a name="supported-features"></a>Fonctionnalités prises en charge

Outre la prise en charge d'un sous-ensemble de fonctionnalités de SQL Server pour Linux, Azure SQL Edge prend en charge les nouvelles fonctionnalités suivantes. 

- SQL Streaming, basé sur le même moteur qu'Azure Stream Analytics, fournit des fonctionnalités de diffusion de données en temps réel dans Azure SQL Edge. 
- Nouvelle fonction T-SQL appelée Date_Bucket pour l'analyse des données de série chronologique.
- Fonctionnalités de Machine Learning via le runtime ONNX inclus avec le moteur SQL.

## <a name="sql-server-on-linux-features-not-supported-in-azure-sql-edge"></a>Fonctionnalités de SQL Server pour Linux non prises en charge dans Azure SQL Edge

La liste ci-dessous répertorie les fonctionnalités de SQL Server 2019 pour Linux qui ne sont actuellement pas prises en charge par Azure SQL Edge.

| Domaine | Fonctionnalité ou service non pris en charge |
|-----|-----|
| **Création de bases de données** | OLTP en mémoire + commandes DDL et fonctions Transact-SQL associées, affichages catalogue et vues de gestion dynamique |
| &nbsp; | Type de données HierarchyID + commandes DDL et fonctions Transact-SQL associées, affichages catalogue et vues de gestion dynamique |
| &nbsp; | Type de données spatiales + commandes DDL et fonctions Transact-SQL associées, affichages catalogue et vues de gestion dynamique |
| &nbsp; | Stretch DB + commandes DDL et fonctions Transact-SQL associées, affichages catalogue et vues de gestion dynamique |
| &nbsp; | Index de recherche en texte intégral + commandes DDL et fonctions Transact-SQL associées, affichages catalogue et vues de gestion dynamique|
| &nbsp; | FileTable, FILESTREAM + commandes DDL et fonctions Transact-SQL associées, affichages catalogue et vues de gestion dynamique|
| **Moteur de base de données** | Réplication. Toutefois, Azure SQL Edge peut être configuré en tant qu'abonné push d'une topologie de réplication. |
| &nbsp; | PolyBase. Toutefois, Azure SQL Edge peut être configuré comme cible pour les tables externes dans Polybase. |
| &nbsp; | Extensibilité du langage avec Java et Spark |
| &nbsp; | Intégration d’Active Directory |
| &nbsp; | Instantanés de base de données |
| &nbsp; | Prise en charge de la mémoire persistante |
| &nbsp; | Microsoft Distributed Transaction Coordinator |
| &nbsp; | Resource Governor et gouvernance des ressources d'E/S |
| &nbsp; | Buffer Pool Extension |
| &nbsp; | Requête distribuée avec connexions tierces |
| &nbsp; | Serveurs liés |
| &nbsp; | Procédures stockées étendues système (XP_CMDSHELL, etc.) |
| &nbsp; | Assemblys CLR + commandes DDL et fonctions Transact-SQL associées, affichages catalogue et vues de gestion dynamique |
| &nbsp; | Fonctions T-SQL dépendant du CLR comme ASSEMBLYPROPERTY, FORMAT, PARSE, TRY_PARSE |
| &nbsp; | Fonctions, clauses de requête et affichages catalogue de dates et d'heures dépendant du CLR |
| &nbsp; | Buffer Pool Extension |
| &nbsp; | Messagerie de base de données |
| **SQL Server Agent** |  Sous-systèmes : CmdExec, PowerShell, lecture de la file d’attente, SSIS, SSAS, SSRS |
| &nbsp; | Alertes |
| &nbsp; | Sauvegarde managée |
| **Haute disponibilité** | Groupes de disponibilité Always On  |
| &nbsp; | Groupes de disponibilité de base |
| &nbsp; | Instance de cluster de basculement Always On |
| &nbsp; | Mise en miroir de bases de données |
| &nbsp; | Ajout de mémoire et de processeur à chaud |
| **Sécurité** | Gestion de clés extensible |
| &nbsp; | Intégration d'Active Directory|
| &nbsp; | Prise en charge des enclaves sécurisées|
| **Services** | SQL Server Browser |
| &nbsp; | Machine Learning via R et Python |
| &nbsp; | StreamInsight |
| &nbsp; | Analysis Services |
| &nbsp; | Reporting Services |
| &nbsp; | Data Quality Services |
| &nbsp; | Master Data Services |
| &nbsp; | Distributed Replay |
| **Facilité de gestion** | Point de contrôle de l’utilitaire SQL Server |

## <a name="next-steps"></a>Étapes suivantes

- [Déployer Azure SQL Edge](deploy-portal.md)
- [Configurer Azure SQL Edge](configure.md)
- [Se connecter à une instance d'Azure SQL Edge à l'aide des outils du client SQL Server](connect.md)
- [Sauvegarder et restaurer des bases de données dans Azure SQL Edge](backup-restore.md)
