---
title: Configurer un réseau virtuel existant pour Managed Instance
description: Cet article décrit comment configurer un réseau virtuel et un sous-réseau existants dans lesquels vous pouvez déployer Azure SQL Database Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 4e678edad2c59205e76598991b36d296404a3163
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773636"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-database-managed-instance"></a>Configurer un réseau virtuel existant pour Azure SQL Database Managed Instance

Azure SQL Database Managed Instance doit être déployé au sein d’un [réseau virtuel](../virtual-network/virtual-networks-overview.md) Azure et du sous-réseau dédié exclusivement à Managed Instances. Vous pouvez utiliser le réseau virtuel et le sous-réseau existants s’ils sont configurés conformément aux [exigences des réseaux virtuels Managed Instance](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

Si l’un des cas suivants vous concerne, vous pouvez valider et modifier votre réseau en utilisant le script expliqué dans cet article :

- Vous avez un sous-réseau qui n’est pas encore configuré.
- Vous ne savez pas si le sous-réseau est conforme aux [exigences](sql-database-managed-instance-connectivity-architecture.md#network-requirements).
- Vous souhaitez vérifier que le sous-réseau est toujours conforme aux [exigences du réseau](sql-database-managed-instance-connectivity-architecture.md#network-requirements) après des changements.

> [!Note]
> Vous pouvez uniquement créer une instance Managed Instance dans les réseaux virtuels créés par le biais du modèle de déploiement Azure Resource Manager. Les réseaux virtuels Azure créés par le biais du modèle de déploiement classique ne sont pas pris en charge. Calculez la taille du sous-réseau en suivant les instructions de l’article [Déterminer la taille du sous-réseau pour les options Managed Instance](sql-database-managed-instance-determine-size-vnet-subnet.md). Vous ne pouvez pas redimensionner le sous-réseau après avoir déployé les ressources à l’intérieur.
>
> Après la création d’une instance managée, le déplacement de l’instance managée ou du réseau virtuel vers un autre groupe de ressources ou vers un autre abonnement n’est pas pris en charge.

## <a name="validate-and-modify-an-existing-virtual-network"></a>Valider et modifier un réseau virtuel existant

Si vous souhaitez créer une instance managée à l’intérieur d’un sous-réseau existant, nous vous recommandons de préparer ce dernier à l’aide du script PowerShell suivant :

```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/delegate-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/delegateSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```

Le script prépare le sous-réseau en trois étapes :

1. Validation : valide le réseau virtuel et le sous-réseau sélectionnés par rapport aux exigences de mise en réseau de Managed Instance.
2. Confirmation : présente à l’utilisateur un ensemble de changements à apporter au sous-réseau en vue de le préparer au déploiement de Managed Instance. Invite également l’utilisateur à donner son consentement.
3. Préparation : configure correctement le réseau virtuel et le sous-réseau.

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble, consultez [Présentation de Managed Instance](sql-database-managed-instance.md).
- Pour accéder à un tutoriel montrant comment créer un réseau virtuel, créer une instance Managed Instance et restaurer une base de données à partir d’une sauvegarde, consultez [Créer une instance Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).
- Pour les problèmes liés au DNS, consultez [Configuration d’un DNS personnalisé](sql-database-managed-instance-custom-dns.md).
