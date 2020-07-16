---
title: Fonctionnalités prises en charge par Azure SQL Edge (préversion)
description: Découvrez les fonctionnalités prises en charge par Azure SQL Edge (préversion).
keywords: présentation de SQL Edge, qu'est-ce que SQL Edge, vue d'ensemble de SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 34d2ba05b00ab92066bc7fa3ccd0b7b2aa59e15c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84669635"
---
# <a name="supported-features-of-azure-sql-edge-preview"></a>Fonctionnalités prises en charge par Azure SQL Edge (préversion) 

Azure SQL Edge repose sur la dernière version du moteur de base de données SQL Server sur Linux. Il prend en charge une partie des fonctionnalités prises en charge par SQL Server 2019 sur Linux, en plus de certaines fonctionnalités qui ne sont pas prises en charge ou disponibles dans SQL Server 2019 sur Linux (ou dans SQL Server sur Windows).

Pour obtenir la liste complète des fonctionnalités prises en charge dans SQL Server pour Linux, consultez [Éditions et fonctionnalités de SQL Server 2019 pour Linux prises en charge](https://docs.microsoft.com/sql/linux/sql-server-linux-editions-and-components-2019). Pour obtenir la liste des éditions et fonctionnalités de SQL Server pour Windows prises en charge, consultez [Éditions et fonctionnalités de SQL Server 2019 (15.x) prises en charge](https://docs.microsoft.com/sql/sql-server/editions-and-components-of-sql-server-version-15).

> [!NOTE]
> Azure SQL Edge est disponible en préversion et, en tant que tel, ne doit pas être utilisé dans des environnements de production. Microsoft peut recommander l’exécution d’Azure SQL Edge dans des environnements de production, suivant la validation du déploiement et de vos scénarios de cas d’usage.

## <a name="azure-sql-edge-editions"></a>Éditions d’Azure SQL Edge

Deux éditions ou plans logiciels d'Azure SQL Edge sont disponibles. Ces éditions disposent des mêmes fonctionnalités et ne diffèrent que par leurs droits d’utilisation et par la capacité de mémoire et de cœurs à laquelle elles peuvent accéder sur le système hôte.

   |**Planification**  |**Description**  |
   |---------|---------|
   |Azure SQL Edge Développeur  |  Pour le développement uniquement. Chaque conteneur Azure SQL Edge Développeur est limité à 4 cœurs et à 32 Go de mémoire.  |
   |Azure SQL Edge    |  Pour la production. Chaque conteneur Azure SQL Edge est limité à 8 cœurs et à 64 Go de mémoire.  |

## <a name="operating-system"></a>Système d’exploitation

Les conteneurs Azure SQL Edge reposent actuellement sur Ubuntu 16.04 et, en tant que tels, sont uniquement pris en charge par les hôtes Docker exécutant Ubuntu 16.04 (recommandé) ou 18.04. Azure SQL Edge peut également s’exécuter sur d’autres hôtes de système d’exploitation. Par exemple, il peut s’exécuter sur d’autres distributions de Linux ou sur Windows (à l’aide de Docker CE ou Docker EE). Notez, toutefois, que Microsoft n’a pas testé ces configurations de manière intensive.

Azure SQL Edge n'est actuellement pris en charge que pour les déploiements via Azure IoT Edge. Pour plus d’informations, consultez [Systèmes pris en charge par Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/support).

La configuration recommandée pour exécuter Azure SQL Edge sur Windows consiste à configurer une machine virtuelle Ubuntu sur l’hôte Windows, puis à exécuter Azure SQL Edge sur la machine virtuelle Linux.

## <a name="hardware-support"></a>Prise en charge matérielle

Azure SQL Edge nécessite un processeur 64 bits, qui peut être de marque Intel, AMD ou ARM, avec au moins 1 processeur et 1 Go de RAM sur l’hôte. L'empreinte mémoire au démarrage d'Azure SQL Edge est proche de 500 Mo. Par conséquent, un supplément de mémoire est nécessaire pour les autres modules IoT Edge exécutés sur le périphérique de périmètre.

## <a name="azure-sql-edge-components"></a>Composants Azure SQL Edge

Azure SQL Edge ne prend en charge que le moteur de base de données. Il ne prend pas en charge les autres composants disponibles avec SQL Server 2019 pour Windows ou SQL Server 2019 pour Linux. Plus précisément, Azure SQL Edge ne prend pas en charge les composants SQL Server tels qu’Analysis Services, Reporting Services, Integration Services, Master Data Services, Machine Learning Services (dans la base de données) et Machine Learning Server (autonome).

## <a name="supported-features"></a>Fonctionnalités prises en charge

Outre la prise en charge d’un sous-ensemble de fonctionnalités de SQL Server sur Linux, Azure SQL Edge prend en charge les nouvelles fonctionnalités suivantes : 

- Le streaming SQL, basé sur le même moteur qu’Azure Stream Analytics, fournit des fonctionnalités de streaming de données en temps réel dans Azure SQL Edge. 
- La fonction T-SQL appelée `Date_Bucket` pour l’analytique données de série chronologique.
- Fonctionnalités de machine learning via le runtime ONNX inclus avec le moteur SQL.

## <a name="unsupported-features"></a>Fonctionnalités non prises en charge

La liste suivante présente les fonctionnalités de SQL Server 2019 pour Linux qui ne sont pas prises en charge par Azure SQL Edge.

| Domaine | Fonctionnalité ou service non pris en charge |
|-----|-----|
| **Création de bases de données** | OLTP en mémoire + commandes DDL et fonctions Transact-SQL associées, affichages catalogue et vues de gestion dynamique. |
| &nbsp; | Type de données `HierarchyID` + commandes DDL et fonctions Transact-SQL associées, affichages catalogue et vues de gestion dynamique. |
| &nbsp; | Type de données `Spatial` + commandes DDL et fonctions Transact-SQL associées, affichages catalogue et vues de gestion dynamique. |
| &nbsp; | Stretch DB + commandes DDL et fonctions Transact-SQL associées, affichages catalogue et vues de gestion dynamique. |
| &nbsp; | Index de recherche en texte intégral + commandes DDL et fonctions Transact-SQL associées, affichages catalogue et vues de gestion dynamique.|
| &nbsp; | `FileTable`, `FILESTREAM` + commandes DDL et fonctions Transact-SQL associées, affichages catalogue et vues de gestion dynamique.|
| **Moteur de base de données** | Réplication. Notez que vous pouvez configurer Azure SQL Edge en tant qu’abonné push d’une topologie de réplication. |
| &nbsp; | PolyBase. Notez que vous pouvez configurer Azure SQL Edge comme cible pour les tables externes dans Polybase. |
| &nbsp; | Extensibilité du langage avec Java et Spark. |
| &nbsp; | Intégration d’Active Directory. |
| &nbsp; | Instantanés de base de données. |
| &nbsp; | Prise en charge de la mémoire persistante. |
| &nbsp; | Microsoft Distributed Transaction Coordinator. |
| &nbsp; | Gouverneur de ressources et gouvernance des ressources d’E/S. |
| &nbsp; | Extension du pool de mémoires tampons. |
| &nbsp; | Requête distribuée avec connexions tierces. |
| &nbsp; | Serveurs liés. |
| &nbsp; | Procédures stockées étendues système (par exemple, `XP_CMDSHELL`). |
| &nbsp; | Assemblys CLR + commandes DDL et fonctions Transact-SQL associées, affichages catalogue et vues de gestion dynamique. |
| &nbsp; | Fonctions T-SQL dépendant du CLR, comme `ASSEMBLYPROPERTY`, `FORMAT`, `PARSE` et `TRY_PARSE`. |
| &nbsp; | Fonctions, clauses de requête et affichages catalogue de dates et d’heures dépendant du CLR. |
| &nbsp; | Extension du pool de mémoires tampons. |
| &nbsp; | Messagerie de base de données. |
| **SQL Server Agent** |  Sous-systèmes : CmdExec, PowerShell, lecture de la file d’attente, SSIS, SSAS et SSRS. |
| &nbsp; | Alertes. |
| &nbsp; | Sauvegarde managée. |
| **Haute disponibilité** | Groupes de disponibilité Always On.  |
| &nbsp; | Groupes de disponibilité de base. |
| &nbsp; | Instance de cluster de basculement Always On. |
| &nbsp; | Mise en miroir de bases de données. |
| &nbsp; | Ajout de mémoire et de processeur à chaud. |
| **Sécurité** | Gestion de clés extensible. |
| &nbsp; | Intégration d’Active Directory.|
| &nbsp; | Prise en charge des enclaves sécurisées.|
| **Services** | SQL Server Browser. |
| &nbsp; | Machine Learning via R et Python. |
| &nbsp; | StreamInsight. |
| &nbsp; | Analysis Services. |
| &nbsp; | Reporting Services. |
| &nbsp; | Data Quality Services. |
| &nbsp; | Master Data Services. |
| &nbsp; | Distributed Replay. |
| **Facilité de gestion** | Point de contrôle de l’utilitaire SQL Server. |

## <a name="next-steps"></a>Étapes suivantes

- [Déployer Azure SQL Edge](deploy-portal.md)
- [Configurer Azure SQL Edge](configure.md)
- [Se connecter à une instance d'Azure SQL Edge à l'aide des outils du client SQL Server](connect.md)
- [Sauvegarder et restaurer des bases de données dans Azure SQL Edge](backup-restore.md)
