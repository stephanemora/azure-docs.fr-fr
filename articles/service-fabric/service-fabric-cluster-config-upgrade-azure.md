---
title: Mettre à niveau la configuration d’un cluster Azure Service Fabric | Microsoft Docs
description: Découvrez comment mettre à niveau la configuration qui exécute un cluster Service Fabric dans Azure à l’aide d’un modèle Resource Manager.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 818136f24eb063e2bd7217d5441bda19bf141317
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666594"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>Mettre à niveau la configuration d’un cluster dans Azure 

Cet article décrit comment personnaliser les différents paramètres de structure pour votre cluster Service Fabric. Pour des clusters hébergés dans Azure, vous pouvez personnaliser les paramètres via le [portail Azure](https://portal.azure.com) ou en utilisant un modèle Azure Resource Manager.

> [!NOTE]
> Pas tous les paramètres sont disponibles dans le portail, et il est un [meilleures pratiques pour le personnaliser à l’aide d’un modèle Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code); Portail concerne uniquement de Service Fabric Dev\Test scénario.
> 

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Personnaliser les paramètres de cluster à l’aide de modèles Resource Manager
Les clusters Azure peuvent être configurés via le modèle Resource Manager JSON. Pour en savoir plus sur les différents paramètres, consultez [Paramètres de configuration pour les clusters](service-fabric-cluster-fabric-settings.md). À titre d’exemple, la procédure ci-dessous montre comment ajouter un nouveau paramètre *MaxDiskQuotaInMB* dans la section *Diagnostics* à l’aide d’Azure Resource Explorer.

1. Accédez à https://resources.azure.com
2. Accédez à votre abonnement en développant **subscriptions** -> **\<Votre abonnement >** -> **resourceGroups** -> **\<Votre groupe de ressources >** -> **providers** -> **Microsoft.ServiceFabric** -> **clusters** -> **\<Nom de votre cluster >**
3. Dans l’angle supérieur droit, sélectionnez **Lecture/écriture**.
4. Sélectionnez **Modifier**, mettez à jour l’élément JSON `fabricSettings` et ajoutez un nouvel élément :

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

Vous pouvez également personnaliser des paramètres de cluster de l’une des manières suivantes avec Azure Resource Manager :

- Utiliser le [portail Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template) pour exporter et mettre à jour le modèle Resource Manager.
- Utiliser [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell) pour exporter et mettre à jour le modèle Resource Manager.
- Utiliser le [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli) pour exporter et mettre à jour le modèle Resource Manager.
- Utiliser les commandes PowerShell d’Azure Resource Manager [Set-AzureRmServiceFabricSetting](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/Set-AzureRmServiceFabricSetting) et [Remove-AzureRmServiceFabricSetting](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/Remove-AzureRmServiceFabricSetting) pour modifier le paramètre directement.
- Utiliser les commandes de [paramétrage de cluster az sf](https://docs.microsoft.com/cli/azure/sf/cluster/setting) d’Azure CLI pour modifier le paramètre directement.

## <a name="next-steps"></a>Étapes suivantes
* Apprenez-en davantage sur les [paramètres de cluster Service Fabric](service-fabric-cluster-fabric-settings.md).
* Découvrez comment [diminuer et augmenter la taille de votre cluster](service-fabric-cluster-scale-up-down.md).
