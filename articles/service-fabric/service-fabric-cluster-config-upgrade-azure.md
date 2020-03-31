---
title: Mettre à niveau la configuration d’un cluster Azure Service Fabric
description: Découvrez comment mettre à niveau la configuration qui exécute un cluster Service Fabric dans Azure à l’aide d’un modèle Resource Manager.
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 476a2d910b916ea29132b108478d06f756454813
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463290"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>Mettre à niveau la configuration d’un cluster dans Azure 

Cet article décrit comment personnaliser les différents paramètres de structure pour votre cluster Service Fabric. Pour des clusters hébergés dans Azure, vous pouvez personnaliser les paramètres via le [portail Azure](https://portal.azure.com) ou en utilisant un modèle Azure Resource Manager.

> [!NOTE]
> Certains paramètres ne sont pas disponibles dans le portail, et c'est une [bonne pratique de le personnaliser en utilisant un modèle Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code) ; le portail est réservé aux scénarios Service Fabric Dev\Test.
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Personnaliser les paramètres de cluster à l’aide de modèles Resource Manager
Les clusters Azure peuvent être configurés via le modèle Resource Manager JSON. Pour en savoir plus sur les différents paramètres, consultez [Paramètres de configuration pour les clusters](service-fabric-cluster-fabric-settings.md). À titre d’exemple, la procédure ci-dessous montre comment ajouter un nouveau paramètre *MaxDiskQuotaInMB* dans la section *Diagnostics* à l’aide d’Azure Resource Explorer.

1. Accédez à https://resources.azure.com
2. Accédez à votre abonnement en développant **subscriptions** ->  **\<Votre abonnement >**  -> **resourceGroups** ->  **\<Votre groupe de ressources >**  -> **providers** -> **Microsoft.ServiceFabric** -> **clusters** ->  **\<Nom de votre cluster >**
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
- Utiliser les commandes Azure PowerShell [Set-AzServiceFabricSetting](https://docs.microsoft.com/powershell/module/az.servicefabric/Set-azServiceFabricSetting) et [Remove-AzServiceFabricSetting](https://docs.microsoft.com/powershell/module/az.servicefabric/Remove-azServiceFabricSetting) pour modifier le paramètre directement.
- Utiliser les commandes de [paramétrage de cluster az sf](https://docs.microsoft.com/cli/azure/sf/cluster/setting) d’Azure CLI pour modifier le paramètre directement.

## <a name="next-steps"></a>Étapes suivantes
* Apprenez-en davantage sur les [paramètres de cluster Service Fabric](service-fabric-cluster-fabric-settings.md).
* Découvrez comment [diminuer et augmenter la taille de votre cluster](service-fabric-cluster-scale-up-down.md).
