---
title: Exemple de script Azure PowerShell - Mettre à jour le nom d’utilisateur et le mot de passe RDP | Microsoft Docs
description: Exemple de script Azure PowerShell - Mettre à jour le nom d’utilisateur et le mot de passe RDP pour tous les nœuds de cluster Service Fabric d’un type de nœud spécifique.
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 03/19/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 1528ce03f548c70d20d8f7af0238019305f01f2e
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58660814"
---
# <a name="update-the-admin-username-and-password-of-the-vms-in-a-cluster"></a>Mettre à jour le nom d’utilisateur et le mot de passe administrateur des machines virtuelles dans un cluster

Chaque [type de nœud](../service-fabric-cluster-nodetypes.md) d’un cluster Service Fabric est un groupe de machines virtuelles identiques. Cet exemple de script met à jour le nom d’utilisateur et le mot de passe administrateur pour les machines virtuelles de cluster d’un type de nœud spécifique.  Ajoutez l’extension VMAccessAgent au groupe identique, car le mot de passe administrateur n’est pas une propriété de groupe identique modifiable.  Les changements de nom d’utilisateur et de mot de passe s’appliquent à tous les nœuds du groupe identique. Personnalisez les paramètres selon vos besoins.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Si nécessaire, installez Azure PowerShell à l’aide des instructions figurant dans le [Guide Azure PowerShell](/powershell/azure/overview). 

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-user-and-pw/change-rdp-user-and-pw.ps1 "Updates a RDP username and password for cluster nodes")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes : Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | Obtient les propriétés d’un type de nœud de cluster (dans un groupe de machines virtuelles identiques).   |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension)| Ajoute une extension au groupe de machines virtuelles identiques.|
| [Update-AzVmss](/powershell/module/az.compute/update-azvmss)|Met à jour l’état d’un groupe de machines virtuelles identiques sur l’état d’un objet VMSS local.|

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts Azure PowerShell pour Azure Service Fabric sur la page [Azure PowerShell Samples](../service-fabric-powershell-samples.md) (Exemples Azure PowerShell).
