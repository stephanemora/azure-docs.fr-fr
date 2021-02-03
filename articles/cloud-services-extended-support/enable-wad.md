---
title: Appliquer l’extension Diagnostics Azure pour Windows dans Azure Cloud Services (support étendu)
description: Appliquer l’extension Diagnostics Azure pour Windows pour Azure Cloud Services (support étendu)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: ad2a27d1e41ba8e589aa98542c4a0cb3d92afbea
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430863"
---
# <a name="apply-the-windows-azure-diagnostics-extension-in-cloud-services-extended-support"></a>Appliquer l’extension Diagnostics Azure pour Windows dans Azure Cloud Services (support étendu) 
Vous pouvez surveiller les principales mesures de performances pour n’importe quel service cloud. Chaque rôle de service cloud collecte des données de base : utilisation du processeur, utilisation du réseau et utilisation du disque. Si le service cloud a l’extension Microsoft.Azure.Diagnostics appliquée à un rôle, ce rôle peut collecter des points de données supplémentaires. Pour plus d’informations, consultez [Présentation des extensions](extensions.md).

L’extension Diagnostics Azure pour Windows peut être activée pour Azure Cloud Services (support étendu) par le biais de [PowerShell](deploy-powershell.md) ou d’un [modèle ARM](deploy-template.md).

## <a name="apply-windows-azure-diagnostics-extension-using-powershell"></a>Appliquer l’extension Diagnostics Azure pour Windows à l’aide de PowerShell

```powershell
# Create WAD extension object
$storageAccountKey = Get-AzStorageAccountKey -ResourceGroupName "ContosOrg" -Name "contosostorageaccount"
$configFile = "<WAD public configuration file path>"
$wadExtension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -StorageAccountName "contosostorageaccount" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFile -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true 

# Get existing Cloud Service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"

# Add WAD extension to existing Cloud Service extension object
$cloudService.ExtensionProfile.Extension = $cloudService.ExtensionProfile.Extension + $wadExtension

# Update Cloud Service
$cloudService | Update-AzCloudService
```

## <a name="apply-windows-azure-diagnostics-extension-using-arm-template"></a>Appliquer l’extension Diagnostics Azure pour Windows à l’aide d’un modèle ARM
```json
"extensionProfile": { 
          "extensions": [ 
            { 
              "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1", 
              "properties": { 
                "autoUpgradeMinorVersion": true, 
                "publisher": "Microsoft.Azure.Diagnostics", 
                "type": "PaaSDiagnostics", 
                "typeHandlerVersion": "1.5", 
                "settings": "Include PublicConfig XML as a raw string", 
                "protectedSettings": "Include PrivateConfig XML as a raw string”", 
                "rolesAppliedTo": [ 
                  "WebRole1" 
                ] 
              } 
            }
          ]
        },

```

## <a name="next-steps"></a>Étapes suivantes 
- Consultez les [prérequis du déploiement](deploy-prerequisite.md) d’Azure Cloud Services (support étendu).
- Consultez la [foire aux questions (FAQ)](faq.md) relative à Azure Cloud Services (support étendu).
- Déployez une instance d’Azure Cloud Services (support étendu) avec le [portail Azure](deploy-portal.md), [PowerShell](deploy-powershell.md), un [modèle](deploy-template.md) ou [Visual Studio](deploy-visual-studio.md).
