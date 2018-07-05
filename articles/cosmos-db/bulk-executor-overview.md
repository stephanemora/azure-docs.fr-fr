---
title: Vue d’ensemble de la bibliothèque d’exécuteur en bloc Azure Cosmos DB | Microsoft Docs
description: Découvrez la bibliothèque d’exécuteur en bloc (Bulk Executor) Azure Cosmos DB, ses avantages et son architecture.
keywords: Exécuteur en bloc Java
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: ramkris
ms.openlocfilehash: 893888904b6afc583c3c20b94d08eb3255a98cad
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36303990"
---
# <a name="azure-cosmos-db-bulk-executor-library-overview"></a>Vue d’ensemble de la bibliothèque d’exécuteur en bloc Azure Cosmos DB
 
Azure Cosmos DB est un service de base de données rapide, flexible et distribué à l’échelle mondiale, conçu pour une montée en puissance élastique afin de prendre en charge : 

* Les grands débits de lecture et d’écriture (plusieurs millions d’opérations par seconde)  
* Le stockage de volumes élevés (plusieurs centaines de téraoctets ou plus) de données transactionnelles et opérationnelles avec une latence prévisible de l’ordre de la milliseconde  

La bibliothèque d’exécuteur en bloc permet de tirer parti de ces formidables capacités de débit et de stockage, et d’effectuer des opérations en bloc dans Azure Cosmos DB par le biais d’API de mise à jour en bloc et d’importation en bloc. Les sections qui suivent décrivent en détail les fonctionnalités de la bibliothèque d’exécuteur en bloc. 

> [!NOTE] 
> Actuellement, la bibliothèque d’exécuteur en bloc gère les opérations d’importation et de mise à jour. Seuls les comptes d’API SQL Azure Cosmos DB la prennent en charge. Pour toute information sur les mises à jour de cette bibliothèque, consultez les notes de publication [.NET](sql-api-sdk-bulk-executor-dot-net.md) et [Java](sql-api-sdk-bulk-executor-java.md).
 
## <a name="key-features-of-the-bulk-executor-library"></a>Principales fonctionnalités de la bibliothèque d’exécuteur en bloc  
 
* Elle réduit considérablement la quantité de ressources de calcul côté client nécessaires pour saturer le débit alloué à un conteneur. Une application monothread qui écrit des données à l’aide de l’API d’importation en bloc atteint un débit d’écriture 10 fois supérieur à celui d’une application multithread qui écrit des données en parallèle tout en saturant l’UC de l’ordinateur client.  

* Elle élimine les tâches fastidieuses liées à l’écriture de la logique d’application qui gère la limitation des requêtes, les délais d’attente de requêtes et autres exceptions temporaires en les traitant efficacement au sein de la bibliothèque.  

* Elle fournit un mécanisme simplifié pour la montée en puissance des applications effectuant des opérations en bloc. Une seule instance d’exécuteur en bloc sur une machine virtuelle Azure peut consommer plus de 500 000 RU/s. Vous pouvez atteindre un débit plus élevé en ajoutant des instances supplémentaires à chacune des machines virtuelles clientes.  
 
* Elle peut importer en bloc plus d’un téraoctet de données par heure en faisant appel à une architecture de montée en puissance.  

* Elle peut mettre à jour en bloc des données existantes dans des conteneurs Azure Cosmos DB sous forme de correctifs. 
 
## <a name="how-does-the-bulk-executor-operate"></a>Quel est le fonctionnement de BulkExecutor ? 

Quand une opération en bloc visant à importer ou mettre à jour des documents est déclenchée avec un lot d’entités, elles sont initialement mélangées dans des compartiments correspondant à leur plage de clés de partition Azure Cosmos DB. Dans chaque compartiment qui correspond à une plage de clés de partition, elles sont divisées en mini-lots jouant chacun le rôle de charge utile validée côté serveur. La bibliothèque d’exécuteur en bloc comporte des optimisations intégrées permettant l’exécution simultanée de ces mini-lots entre les groupes de clés de partition et au sein d’un même groupe. L’image suivante montre comment l’exécuteur en bloc traite les données par lots dans différentes clés de partition :  

![Architecture de BulkExecutor](./media/bulk-executor-overview/bulk-executor-architecture.png)

La bibliothèque BulkExecutor utilise de manière optimale le débit alloué à une collection. Elle utilise un  [mécanisme de contrôle de congestion de style AIMD](https://tools.ietf.org/html/rfc5681) pour chaque plage de clés de partition Azure Cosmos DB afin de gérer efficacement la limitation et les délais d’attente. 

## <a name="next-steps"></a>Étapes suivantes 
  
* Pour en savoir plus, essayez les exemples d’applications consommant la bibliothèque BulkExecutor dans [.NET](bulk-executor-dot-net.md) et [Java](bulk-executor-java.md).  
* Consultez les notes de publication et les informations sur le SDK de l’exécuteur en bloc en [.NET](sql-api-sdk-bulk-executor-dot-net.md) et en [Java](sql-api-sdk-bulk-executor-java.md).
* La bibliothèque BulkExecutor est intégrée au connecteur Spark Cosmos DB. Pour plus d’informations, consultez l’article sur le [connecteur Spark Azure Cosmos DB](spark-connector.md).  
* La bibliothèque d’exécuteur en bloc est également intégrée à une nouvelle version du [connecteur Azure Cosmos DB](https://aka.ms/bulkexecutor-adf-v2) pour permettre à Azure Data Factory de copier des données.
