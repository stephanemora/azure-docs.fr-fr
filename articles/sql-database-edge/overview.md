---
title: Qu’est-ce qu’Azure SQL Database Edge ? | Microsoft Docs
description: En savoir plus sur Azure SQL Database Edge
keywords: présentation de sql database edge, qu’est-ce que sql database edge, vue d’ensemble de sql database edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 03/24/2020
ms.openlocfilehash: d5c48b6036065f6182912c21c144cab80fc3cfbf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80246702"
---
# <a name="what-is-azure-sql-database-edge-preview"></a>Qu’est-ce qu’Azure SQL Database Edge en préversion ?

Azure SQL Database Edge en préversion est un moteur de base de données relationnelle optimisé conçu pour les déploiements IoT et IoT Edge. Il offre des fonctionnalités permettant de créer une couche de traitement et de stockage des données hautes performances pour les solutions et applications IoT. Azure SQL Database Edge offre des fonctionnalités permettant de diffuser, de traiter et d’analyser des données relationnelles et non relationnelles, telles que des données JSON, graphiques et de série chronologique, ce qui en fait le choix idéal pour une variété d’applications IoT modernes.

Azure SQL Database Edge repose sur les dernières versions du [moteur de base de données Microsoft SQL Server](/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database-edge/toc.json), qui fournit des fonctionnalités de performances, de sécurité et de traitement des requêtes de pointe. Azure SQL Database Edge repose ainsi sur le même moteur que SQL Server et Azure SQL Database, il offre la même surface de programmation T-SQL qui rend le développement d’applications ou de solutions plus facile et plus rapide, et qui, dans le même temps, permet la portabilité d’application entre des appareils IoT Edge, des centres de données et le cloud.

## <a name="deployment-models"></a>Modèles de déploiement

Azure SQL Database Edge est disponible sur la Place de marché Azure et peut être déployé en tant que module pour [Azure IoT Edge](../iot-edge/about-iot-edge.md). Pour plus d’informations, consultez [Déployer Azure SQL Database Edge](deploy-portal.md).<br>

![Schéma de présentation de SQL Database Edge](media/overview/overview.png)

## <a name="editions-of-sql-database-edge"></a>Éditions de SQL Database Edge

SQL Database Edge est disponible avec trois éditions ou plans logiciels différents. Ces éditions incluent des ensembles de fonctionnalités identiques et diffèrent uniquement en termes de droits d’utilisation et de quantité d’UC/de mémoire qu’elles prennent en charge.

   |**Planification**  |**Description**  |
   |---------|---------|
   |Azure SQL Database Edge Développeur  |  Référence SKU de développement uniquement, chaque conteneur SQL Database Edge est limité à 4 cœurs et à 32 Go de mémoire.  |
   |Azure SQL Database Edge    |  Référence SKU de production, chaque conteneur SQL Database Edge est limité à 8 cœurs et à 64 Go de mémoire. |

## <a name="pricing-and-availability"></a>Tarification et disponibilité

Azure SQL Database est actuellement en préversion. Pour plus d’informations sur la tarification et la disponibilité, consultez [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).

> [!IMPORTANT]
> Pour comprendre les différences de fonctionnalités entre Azure SQL Database Edge et SQL Server, ainsi que les différences entre les diverses options d’Azure SQL Database Edge, consultez [Fonctionnalités de SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).

## <a name="streaming-capabilities"></a>Fonctionnalités de diffusion en continu  

Azure SQL Database Edge fournit des fonctionnalités de diffusion en continu intégrées pour l’analyse en temps réel et le traitement d’événements complexe. La fonctionnalité de diffusion en continu est créée à l’aide des mêmes constructions qu’[Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) et fournit des fonctionnalités similaires à celles d’[Azure Stream Analytics sur IoT Edge](../stream-analytics/stream-analytics-edge.md).

Le moteur de diffusion en continu pour Azure SQL Database Edge est conçu pour une latence faible, une résilience, une utilisation efficace de la bande passante et la conformité.

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>Fonctionnalités de Machine Learning et d’intelligence artificielle

Azure SQL Database Edge fournit des fonctionnalités de Machine Learning et d’analyse intégrées en intégrant le runtime ONNX (Open Neural Network Exchange) de format ouvert, qui permet d’échanger des modèles de Deep Learning et de réseau neuronal entre différentes infrastructures. Pour plus d’informations sur ONNX, cliquez [ici](https://onnx.ai/). Le runtime ONNX offre la possibilité de développer des modèles dans un langage ou des outils de votre choix, qui peuvent ensuite être convertis au format ONNX pour être exécutés dans SQL Database Edge. Pour plus d’informations, consultez [Machine Learning et intelligence artificielle avec ONNX dans SQL Database Edge](onnx-overview.md).

## <a name="working-with-azure-sql-database-edge"></a>Utilisation d’Azure SQL Database Edge

Azure SQL Database Edge simplifie le développement et la gestion des applications et vous fait gagner en productivité. Les utilisateurs peuvent utiliser tous les outils et compétences familiers pour créer des applications et des solutions exceptionnelles répondant à leurs besoins en IoT Edge. L’utilisateur peut développer dans SQL Database Edge à l’aide d’outils tels que les suivants :

- [Portail Azure](https://portal.azure.com/) : application web dédiée à la gestion de tous les services Azure.
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) : application cliente téléchargeable gratuitement dédiée à la gestion des infrastructures SQL, allant de SQL Server à SQL Database.
- [SQL Server Data Tools dans Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt/) : application cliente téléchargeable gratuitement, dédiée au développement de bases de données relationnelles SQL Server, de bases de données SQL, de packages Integration Services, de modèles de données Analysis Services et de rapports Reporting Services.
- [Azure Data Studio](/sql/azure-data-studio/what-is/) : outil de base de données multiplateforme téléchargeable gratuitement pour les professionnels des données utilisant la famille Microsoft de plateformes de données locales et cloud sur Windows, MacOS et Linux.
- [Visual Studio Code](https://code.visualstudio.com/docs) : éditeur de code open source téléchargeable gratuitement pour Windows, macOS et Linux. Il prend en charge les extensions, notamment l’[extension mssql](https://aka.ms/mssql-marketplace) pour l’exécution de requêtes dans Microsoft SQL Server, Azure SQL Database et Azure SQL Data Warehouse.


## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la tarification et la disponibilité, consultez [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).
- Demandez à activer Azure SQL Database Edge pour votre abonnement.
- Pour commencer, consultez les procédures suivantes :
  - [Déployer SQL Database Edge via le Portail Azure](deploy-portal.md)
  - [Machine Learning et intelligence artificielle avec SQL Database Edge](onnx-overview.md)
