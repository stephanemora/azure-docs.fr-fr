---
title: Exemples Azure PowerShell pour Azure Data Factory
description: Exemples Azure PowerShell - Scripts pour vous aider à créer et à gérer des fabriques de données.
ms.service: data-factory
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 21ab661dd19d76526cd97b75660b9749e1342e09
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104783232"
---
# <a name="azure-powershell-samples-for-azure-data-factory"></a>Exemples Azure PowerShell pour Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Le tableau suivant comprend des liens vers des exemples de scripts Azure PowerShell pour Azure Data Factory.

| Script | Description  |
|---|---|
|**Copie de données**||
|[Copier des objets blob à partir d’un dossier vers un autre dossier dans un stockage Blob Azure](scripts/copy-azure-blob-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ce script PowerShell copie des objets blob depuis un dossier du Stockage Blob Azure vers un autre dossier de ce même stockage. |
|[Copier des données d’une base de données SQL Server vers le Stockage Blob Azure](scripts/hybrid-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ce script PowerShell copie des données à partir d’une base de données SQL Server vers un stockage Blob Azure. |
|[Copie en bloc](scripts/bulk-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cet exemple de script PowerShell copie les données de plusieurs tables d’une base de données dans Azure SQL Database vers Azure Synapse Analytics. |
|[Copie incrémentielle](scripts/incremental-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cet exemple de script PowerShell charge uniquement les enregistrements nouveaux ou mis à jour à partir d’un magasin de données source vers un magasin de données récepteur, après la copie initiale complète des données de la source vers le récepteur. |
|**Transformer des données**||
|[Transformer des données à l’aide d’un cluster Spark](scripts/transform-data-spark-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ce script PowerShell transforme les données en exécutant un programme sur un cluster Spark. |
|**Intégrer par « lift-and-shift » des packages SSIS sur Azure**||
|[Créer un runtime d’intégration Azure-SSIS](scripts/deploy-azure-ssis-integration-runtime-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ce script PowerShell provisionne un runtime d’intégration SSIS-Azure qui exécute les packages SSIS (SQL Server Integration Services) dans Azure. |



