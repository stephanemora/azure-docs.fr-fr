---
title: Azure SQL Database Managed Instance - Configuration d’un réseau virtuel/sous-réseau existant | Microsoft Docs
description: Cette rubrique explique comment configurer un réseau virtuel (VNet) et un sous-réseau existants dans lesquels vous pouvez déployer Azure SQL Database Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 1718177a0902bc7049eb6986e5a1d128eeb3f233
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54040956"
---
# <a name="configure-an-existing-vnet-for-azure-sql-database-managed-instance"></a>Configurer un réseau virtuel existant pour Azure SQL Database Managed Instance

Azure SQL Database Managed Instance doit être déployé au sein d’un [réseau virtuel (VNet)](../virtual-network/virtual-networks-overview.md) Azure et du sous-réseau dédié exclusivement à des instances managées. Vous pouvez utiliser le réseau virtuel et le sous-réseau existants s’ils sont configuré conformément aux [spécifications des réseaux virtuels Managed Instance](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

Si vous disposez d’un nouveau sous-réseau pas encore configuré, si vous n’êtes pas certain qu’il est conforme aux [spécifications](sql-database-managed-instance-connectivity-architecture.md#network-requirements), ou si vous souhaitez vérifier que le sous-réseau est toujours conforme aux [spécifications des réseaux](sql-database-managed-instance-connectivity-architecture.md#network-requirements) après y avoir apporté des modifications, vous pouvez valider et modifier votre réseau en utilisant le script décrit dans cette section.

  > [!Note]
  > Vous pouvez uniquement créer une option Managed Instance dans des réseaux virtuels Resource Manager. Les réseaux virtuels Azure déployés selon le modèle de déploiement Classic ne sont pas pris en charge. Veillez à calculer la taille du sous-réseau en suivant les recommandations de l’article [Déterminer la taille du sous-réseau pour des instances managées](sql-database-managed-instance-determine-size-vnet-subnet.md), car le sous-réseau ne peut pas être redimensionné une fois que vous y avez déployé les ressources.

## <a name="validate-and-modify-an-existing-virtual-network"></a>Valider et modifier un réseau virtuel existant

Si vous souhaitez créer une instance managée à l’intérieur d’un sous-réseau existant, nous vous recommandons de préparer ce dernier à l’aide du script PowerShell suivant :

```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/prepare-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/prepareSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```

La préparation du sous-réseau s’effectue en trois étapes simples :

1. Validation : le réseau virtuel et le sous-réseau sélectionnés sont validés par rapport aux spécifications de mise en réseau Managed Instance.
2. Confirmation : un ensemble de modifications à apporter au sous-réseau en vue de le préparer au déploiement de Managed Instance est présenté à l’utilisateur, lequel est invité à donner son consentement.
3. Préparation : le réseau virtuel et le sous-réseau sont configurés correctement.

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble, consultez [Présentation de l’option Managed Instance](sql-database-managed-instance.md)
- Pour un tutoriel qui montre comment créer un réseau virtuel, créer une option Managed Instance et restaurer une base de données à partir d’une sauvegarde, consultez [Création d’une option Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).
- Pour les problèmes de DNS, consultez [Configuration d’un DNS personnalisé](sql-database-managed-instance-custom-dns.md).
