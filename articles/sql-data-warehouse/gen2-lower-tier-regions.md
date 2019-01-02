---
title: Azure SQL Data Warehouse Gen2 prend en charge les niveaux inférieurs de calcul | Microsoft Docs
description: Azure SQL Data Warehouse Gen2 prend désormais en charge les niveaux inférieurs de calcul.
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 12/03/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: d3c42c13e58156013167b8570a8845ce9de08b3b
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52884887"
---
# <a name="azure-sql-data-warehouse-gen2-support-for-lower-compute-tiers"></a>Prise en charge des niveaux inférieurs de calcul par Azure SQL Data Warehouse Gen2

Microsoft aide à réduire le coût d’entrée de gamme de l’exécution d’un entrepôt de données capable de gérer des requêtes exigeantes en ajoutant des niveaux inférieurs de calcul pour Azure SQL Data Warehouse Gen2. Les clients peuvent bénéficier des fonctionnalités de performances, de flexibilité et de sécurité exceptionnelles d’Azure SQL Data Warehouse à partir de 100 cDWU (Data Warehouse Units) et mettre à l’échelle jusqu’à 30 000 cDWU en quelques minutes. Les clients peuvent tirer parti des performances et de la flexibilité de Gen2 avec des niveaux inférieurs de calcul. 

En supprimant le point d’entrée pour l’entreposage de données nouvelle génération, Microsoft ouvre les portes aux clients qui souhaitent évaluer tous les avantages offerts par un entrepôt de données sécurisé et à hautes performances sans avoir à deviner quel environnement d’essai est le mieux adapté.  Les clients pourront dorénavant commencer à 100 cDWU, alors que le point d’entrée précédant était de 500 cDWU.  SQL Data Warehouse Gen2 continue à prendre en charge les opérations de suspension et de reprise, et va bien au-delà de la simple flexibilité de calcul.  Gen2 prend également en charge une capacité illimité de stockage columnstore, 2,5 fois plus de mémoire par requête, jusqu’à 128 requêtes simultanées et des fonctionnalités de mise en cache adaptative, offrant en moyenne des performances multipliées par cinq par rapport à la même Data Warehouse Unit sur Gen1 au même prix.  Les sauvegardes géoredondantes sont standard pour Gen2, avec protection des données garantie intégrée. Azure SQL Data Warehouse Gen2 est prêt pour une mise à l’échelle à tout moment.

Actuellement, le portail ne prend pas en charge le déploiement ou la mise à l’échelle vers les instances Gen2 de niveau inférieur. Nous travaillons dur afin d’activer cette fonctionnalité. En attendant, veuillez [soumettre un ticket](sql-data-warehouse-get-started-create-support-ticket.md) si vous souhaitez profiter de ce nouveau niveau.

## <a name="getting-started-with-azure-sql-data-warehouse-gen2"></a>Bien démarrer avec Azure SQL Data Warehouse Gen2 

Les clients peuvent choisir de déployer une nouvelle instance Gen2 ou de mettre à niveau une instance d’entrepôt de données Gen1 existante afin de profiter de la flexibilité et des performances offertes par l’entreposage de données de nouvelle génération. 

Essayez le [Niveau optimisé pour le calcul Gen2 Azure SQL Data Warehouse.](https://azure.microsoft.com/services/sql-data-warehouse/?v=17.44)
Mettez à niveau [Azure SQL Warehouse optimisé pour le calcul Gen1 vers Gen2](https://docs.microsoft.com/azure/sql-data-warehouse/upgrade-to-latest-generation) Observez Azure SQL Data Warehouse Gen2 en action dans cette [vidéo Microsoft Mechanics.](https://www.youtube.com/watch?v=Ap8I3UZonzI&feature=youtu.be)


## <a name="supported-regions-for-lower-compute-tiers"></a>Régions prises en charge pour les niveaux inférieurs de calcul

- USA Est 1 
- USA Est 2
- Europe Ouest
- Europe Nord
- USA Ouest 2
- Asie Sud-Est
- USA Centre Sud
- USA Centre 
- Asie Est
- Japon Est
- Inde Centre
- Australie Est
- Centre du Canada
- Japon Ouest 
- Centre du Canada

## <a name="next-steps"></a>Étapes suivantes

- [Apprenez-en davantage](upgrade-to-latest-generation.md) sur l’optimisation des performances en mettant à niveau SQL Data Warehouse. 
