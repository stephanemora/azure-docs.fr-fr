---
title: Utiliser la mise à l’échelle automatique Azure avec des mesures invitées dans un modèle de groupe identique Linux | Microsoft Docs
description: Découvrez comment effectuer la mise à l’échelle automatique en utilisant des mesures invitées dans un modèle de groupe de machines virtuelles identiques Linux
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: na
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: manayar
ms.openlocfilehash: 8cd665ffd82547c4f554eb4a515a8da7dc5b3f5f
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64868987"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Mise à l’échelle automatique en utilisant des mesures invitées dans un modèle de groupe identique Linux

Il existe deux grands types de mesures dans Azure qui sont rassemblées à partir de machines virtuelles et des groupes identiques : Métriques de l’hôte et les mesures invitées. À un niveau élevé, si vous souhaitez utiliser les UC standard, de disque et de mesures de réseau, puis métriques de l’hôte sont une solution parfaitement adaptée. Si, toutefois, vous avez besoin d’un plus large éventail de mesures, puis les mesures invitées doivent être examinées.

Métriques de l’hôte ne nécessitent pas une configuration supplémentaire, car elles sont collectées par l’hôte de machine virtuelle, tandis que les mesures invitées nécessitent que vous installiez le [extension Windows Azure Diagnostics](../virtual-machines/windows/extensions-diagnostics-template.md) ou le [extension Linux Azure Diagnostics ](../virtual-machines/linux/diagnostic-extension.md) dans la machine virtuelle invitée. L’utilisation des mesures invitées à la place des mesures hôtes est courante, car les premières sont plus variées que les dernières. Les mesures de consommation de la mémoire, notamment, ne sont disponibles que via les mesures invitées. Les mesures hôtes prises en charge sont répertoriées [ici](../azure-monitor/platform/metrics-supported.md), et les mesures invitées couramment utilisées [ici](../azure-monitor/platform/autoscale-common-metrics.md). Cet article explique comment modifier le [base identique viable modèle](virtual-machine-scale-sets-mvss-start.md) à utiliser des règles de mise à l’échelle en fonction des mesures invitées des groupes identiques Linux.

## <a name="change-the-template-definition"></a>Modifier la définition du modèle

Dans un [article précédent](virtual-machine-scale-sets-mvss-start.md) nous avions créé un modèle de groupe identique de base. Maintenant nous utiliser ce modèle antérieures et modifiez-le pour créer un modèle qui déploie un groupe identique Linux avec l’échelle automatique basée sur invité métrique.

Ajoutez tout d’abord les paramètres de `storageAccountName` et `storageAccountSasToken`. L’agent de diagnostics stocke les données métriques dans une [table](../cosmos-db/table-storage-how-to-use-dotnet.md) de ce compte de stockage. À compter de la version 3.0 de l’agent de diagnostic Linux, l’utilisation d’une clé d’accès de stockage n’est plus prise en charge. Utilisez plutôt un [jeton SAP](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "storageAccountName": {
+      "type": "string"
+    },
+    "storageAccountSasToken": {
+      "type": "securestring"
     }
   },
```

Ensuite, modifiez le groupe identique `extensionProfile` pour y inclure l’extension Diagnostics. Dans cette configuration, spécifiez l’ID de ressource du groupe identique à partir duquel collecter les métriques, ainsi que le compte de stockage et le jeton SAP à utiliser pour les stocker. Indiquez la fréquence d’agrégation des métriques (dans ce cas, chaque minute) et les métriques à suivre (dans ce cas, le pourcentage de mémoire utilisée). Pour plus d’informations sur cette configuration et les mesures autres que le pourcentage de mémoire utilisée, consultez [cette documentation](../virtual-machines/linux/diagnostic-extension.md).

```diff
                 }
               }
             ]
+          },
+          "extensionProfile": {
+            "extensions": [
+              {
+                "name": "LinuxDiagnosticExtension",
+                "properties": {
+                  "publisher": "Microsoft.Azure.Diagnostics",
+                  "type": "LinuxDiagnostic",
+                  "typeHandlerVersion": "3.0",
+                  "settings": {
+                    "StorageAccount": "[parameters('storageAccountName')]",
+                    "ladCfg": {
+                      "diagnosticMonitorConfiguration": {
+                        "performanceCounters": {
+                          "sinks": "WADMetricJsonBlob",
+                          "performanceCounterConfiguration": [
+                            {
+                              "unit": "percent",
+                              "type": "builtin",
+                              "class": "memory",
+                              "counter": "percentUsedMemory",
+                              "counterSpecifier": "/builtin/memory/percentUsedMemory",
+                              "condition": "IsAggregate=TRUE"
+                            }
+                          ]
+                        },
+                        "metrics": {
+                          "metricAggregation": [
+                            {
+                              "scheduledTransferPeriod": "PT1M"
+                            }
+                          ],
+                          "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]"
+                        }
+                      }
+                    }
+                  },
+                  "protectedSettings": {
+                    "storageAccountName": "[parameters('storageAccountName')]",
+                    "storageAccountSasToken": "[parameters('storageAccountSasToken')]",
+                    "sinksConfig": {
+                      "sink": [
+                        {
+                          "name": "WADMetricJsonBlob",
+                          "type": "JsonBlob"
+                        }
+                      ]
+                    }
+                  }
+                }
+              }
+            ]
           }
         }
       }
```

Enfin, ajoutez une ressource `autoscaleSettings` pour configurer la mise à l’échelle automatique en fonction de ces métriques. Cette ressource contient une clause `dependsOn` qui fait référence au groupe identique pour s’assurer que ce groupe existe avant d’essayer de le soumettre à une mise à l’échelle automatique. Pour baser la mise à l’échelle automatique sur une autre métrique, utilisez la propriété `counterSpecifier` de la configuration de l’extension Diagnostics en tant que propriété `metricName` dans la configuration de la mise à l’échelle automatique. Pour plus d’informations sur la configuration de la mise à l’échelle automatique, consultez les [meilleures pratiques en matière de mise à l’échelle automatique](../azure-monitor/platform/autoscale-best-practices.md) et la [documentation de référence de l’API REST Azure Monitor](/rest/api/monitor/autoscalesettings).

```diff
+    },
+    {
+      "type": "Microsoft.Insights/autoscaleSettings",
+      "apiVersion": "2015-04-01",
+      "name": "guestMetricsAutoscale",
+      "location": "[resourceGroup().location]",
+      "dependsOn": [
+        "Microsoft.Compute/virtualMachineScaleSets/myScaleSet"
+      ],
+      "properties": {
+        "name": "guestMetricsAutoscale",
+        "targetResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+        "enabled": true,
+        "profiles": [
+          {
+            "name": "Profile1",
+            "capacity": {
+              "minimum": "1",
+              "maximum": "10",
+              "default": "3"
+            },
+            "rules": [
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "GreaterThan",
+                  "threshold": 60
+                },
+                "scaleAction": {
+                  "direction": "Increase",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              },
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "LessThan",
+                  "threshold": 30
+                },
+                "scaleAction": {
+                  "direction": "Decrease",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              }
+            ]
+          }
+        ]
+      }
     }
   ]
 }
```





## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
