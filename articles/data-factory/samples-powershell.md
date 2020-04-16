---
title: Exemples Azure PowerShell pour Azure Data Factory
description: Exemples Azure PowerShell - Scripts pour vous aider à créer et à gérer des fabriques de données.
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 01/16/2018
ms.openlocfilehash: 705b2464285b747ea7999b112733b7e99f761f22
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419083"
---
# <a name="azure-powershell-samples-for-azure-data-factory"></a>Exemples Azure PowerShell pour Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Le tableau suivant comprend des liens vers des exemples de scripts Azure PowerShell pour Azure Data Factory.

| |  |
|---|---|
|**Copie de données**||
|[Copier des objets blob à partir d’un dossier vers un autre dossier dans un stockage Blob Azure](scripts/copy-azure-blob-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ce script PowerShell copie des objets blob depuis un dossier du Stockage Blob Azure vers un autre dossier de ce même stockage. |
|[Copier des données d’une base de données SQL Server locale vers le Stockage Blob Azure](scripts/hybrid-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ce script PowerShell copie des données depuis une base de données SQL Server locale vers un stockage Blob Azure. |
|[Copie en bloc](scripts/bulk-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cet exemple de script PowerShell copie les données de plusieurs tables dans une base de données Azure SQL vers un entrepôt de données Azure SQL. |
|[Copie incrémentielle](scripts/incremental-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cet exemple de script PowerShell charge uniquement les enregistrements nouveaux ou mis à jour à partir d’un magasin de données source vers un magasin de données récepteur, après la copie initiale complète des données de la source vers le récepteur. |
|**Transformer des données**||
|[Transformer des données à l’aide d’un cluster Spark](scripts/transform-data-spark-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ce script PowerShell transforme les données en exécutant un programme sur un cluster Spark. |
|**Intégrer par « lift-and-shift » des packages SSIS sur Azure**||
|[Créer un runtime d’intégration Azure-SSIS](scripts/deploy-azure-ssis-integration-runtime-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ce script PowerShell provisionne un runtime d’intégration SSIS-Azure qui exécute les packages SSIS (SQL Server Integration Services) dans Azure. |



