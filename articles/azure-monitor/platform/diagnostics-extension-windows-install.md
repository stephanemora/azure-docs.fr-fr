---
title: Installer et configurer l’extension Diagnostics Azure pour Windows (WAD)
description: Découvrez comment collecter les données de diagnostics Azure dans un compte Stockage Azure afin de pouvoir les afficher avec l’un des outils disponibles.
services: azure-monitor
author: bwren
ms.subservice: diagnostic-extension
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.openlocfilehash: 929ab4109eb8d0e90b6c561a2135c0b7dd4205bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672257"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>Installer et configurer l’extension Diagnostics Azure pour Windows (WAD)
L’extension Diagnostics Azure est un agent présent dans Azure Monitor qui collecte des données de supervision dans le système d’exploitation invité et des charges de travail de machines virtuelles Azure et d’autres ressources de calcul. Cet article fournit des détails sur l’installation et la configuration de l’extension de diagnostic Windows et une description de la façon dont les données sont stockées dans un compte Stockage Azure.

L’extension de diagnostic étant implémentée comme [extension de machine virtuelle](../../virtual-machines/extensions/overview.md) dans Azure, elle prend en charge les mêmes options d’installation à l’aide de modèles Resource Manager, de PowerShell et de l’interface CLI. Pour plus d’informations sur l’installation et la maintenance des extensions de machine virtuelle, consultez [Extensions et fonctionnalités de machine virtuelle pour Windows](../../virtual-machines/extensions/features-windows.md).

## <a name="install-with-azure-portal"></a>Installer avec Portail Azure
Vous pouvez installer et configurer l’extension de diagnostics sur une machine virtuelle individuelle dans le Portail Azure qui vous fournit une interface au lieu de travailler directement avec la configuration. Lorsque vous activez l’extension de diagnostic, elle utilise automatiquement une configuration par défaut avec les compteurs de performance et les événements les plus courants. Vous pouvez modifier cette configuration par défaut en fonction de vos besoins spécifiques.

> [!NOTE]
> Il existe des paramètres d’extension de diagnostic que vous ne pouvez pas configurer à l’aide du Portail Azure, notamment l’envoi de données à Azure Event Hubs. Vous devez utiliser l’une des autres méthodes de configuration pour ces paramètres.

1. Ouvrez le menu d’une machine virtuelle dans le Portail Azure.
2. Cliquez sur **Paramètres de diagnostic** dans la section **Surveillance** du menu de la machine virtuelle.
3. Cliquez sur **Activer la supervision d’invités** si l’extension de diagnostic n’a pas déjà été activée.
4. Un nouveau compte Stockage Azure sera créé pour la machine virtuelle, dont le nom sera basé sur le nom du groupe de ressources de la machine virtuelle. Vous pouvez attacher la machine virtuelle à un autre compte de stockage en sélectionnant l’onglet **Agent**.

![Paramètres de diagnostic](media/diagnostics-extension-windows-install/diagnostic-settings.png)


Vous pouvez modifier la configuration par défaut une fois l’extension d diagnostic activée. Le tableau suivant décrit les options que vous pouvez modifier dans les différents onglets. Certaines options ont une commande **personnalisée** qui vous permet de spécifier une configuration plus détaillée. Pour plus d’informations sur les différents paramètres, consultez [Schéma d’extension de diagnostic Windows](diagnostics-extension-schema-windows.md).

| Onglet | Description |
|:---|:---|
| Vue d’ensemble | Affiche la configuration actuelle avec des liens vers les autres onglets. |
| Compteurs de performance | Sélectionnez les compteurs de performances à collecter et l’échantillonnage pour chacun d’entre eux.  |
| Journaux d’activité | Sélectionnez les données du journal à collecter. Cela comprend les journaux d’événements Windows, les journaux IIS, les journaux d’applications .NET et les événements ETW.  |
| Vidages sur incident | Activez le vidage sur incident pour différents processus. |
| Récepteurs | Activez les récepteurs de données pour envoyer des données aux destinations en plus de Stockage Azure.<br>Azure Monitor : envoie des données de performances aux mesures Azure Monitor.<br>Application Insights : envoie des données à une application Application Insights. |
| Agent | Modifiez la configuration suivante pour l’agent :<br>- Modifiez le compte de stockage.<br>- Spécifiez le disque local maximal utilisé pour l’agent.<br>- Configurer les journaux d’activité pour l’intégrité de l’agent lui-même.|


> [!NOTE]
> Bien que la configuration de l’extension de diagnostic puisse être au format JSON ou XML, toute configuration effectuée dans le Portail Azure est toujours stockée au format JSON. Si vous utilisez XML avec une autre méthode de configuration, puis modifiez votre configuration avec le Portail Azure, les paramètres seront modifiés en format JSON.

## <a name="resource-manager-template"></a>Modèle Resource Manager
Pour plus d’informations sur le déploiement de l’extension de diagnostic à l’aide de vos modèles Azure Resource Manager, consultez [Utiliser la surveillance et les diagnostics avec une machine virtuelle Windows et des modèles Azure Resource Manager](../../virtual-machines/extensions/diagnostics-template.md). 

## <a name="azure-cli-deployment"></a>Déploiement de l’interface de ligne de commande Azure
Vous pouvez utiliser Azure CLI pour déployer l’extension Diagnostics Azure sur une machine virtuelle existante à l’aide de la commande [az vm extension set](https://docs.microsoft.com/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-set), comme indiqué dans l’exemple suivant. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

Les paramètres protégés sont définis dans l’[élément PrivateConfig](diagnostics-extension-schema-windows.md#privateconfig-element) du schéma de configuration. Voici l’exemple minimal d’un fichier de paramètres protégés qui définit le compte de stockage. Pour plus d’informations sur les paramètres privés, consultez [Exemple de configuration](diagnostics-extension-schema-windows.md#privateconfig-element).

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```
Les paramètres publics sont définis dans l’[élément PublicConfig](diagnostics-extension-schema-windows.md#publicconfig-element) du schéma de configuration. Voici l’exemple minimal d’un fichier de paramètres publics qui permet la collecte de journaux d’infrastructure de diagnostic, d’un seul compteur de performances et d’un seul journal des événements. Pour plus d’informations sur les paramètres publics, consultez [Exemple de configuration](diagnostics-extension-schema-windows.md#publicconfig-element).

```JSON
{
  "StorageAccount": "mystorageaccount",
  "WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": 5120,
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": [
          {
            "counterSpecifier": "\\Processor Information(_Total)\\% Processor Time",
            "unit": "Percent",
            "sampleRate": "PT60S"
          }
        ]
      },
      "WindowsEventLog": {
        "scheduledTransferPeriod": "PT1M",
        "DataSource": [
          {
            "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
          }
        ]
      }
    }
  }
}
```



## <a name="powershell-deployment"></a>Déploiement PowerShell
Vous pouvez utiliser PowerShell pour déployer l’extension Diagnostics Azure sur une machine virtuelle existante à l’aide de la commande [Set-AzVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension), comme indiqué dans l’exemple suivant. 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

Les paramètres privés sont définis dans l’[élément PrivateConfig](diagnostics-extension-schema-windows.md#privateconfig-element), tandis que les paramètres publics sont définis dans l’[élément PublicConfig](diagnostics-extension-schema-windows.md#publicconfig-element) du schéma de configuration. Vous pouvez également choisir de spécifier les détails du compte de stockage en tant que paramètres de la cmdlet Set-AzVMDiagnosticsExtension au lieu de les inclure dans les paramètres privés.

Voici l’exemple minimal d’un fichier config qui permet la collecte de journaux d’infrastructure de diagnostic, d’un seul compteur de performances et d’un seul journal des événements. Pour plus d’informations sur les paramètres privés et publics, consultez [Exemple de configuration](diagnostics-extension-schema-windows.md#publicconfig-element). 

```JSON
{
    "PublicConfig": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                "overallQuotaInMB": 10000
            },
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT3M",
                        "unit": "percent"
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT1M",
                    "DataSource": [
                    {
                        "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                    }
                ]
            }
        },
        "StorageAccount": "mystorageaccount",
        "StorageType": "TableAndBlob"
    },
    "PrivateConfig": {
        "storageAccountName": "mystorageaccount",
        "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
    }
}
```

Voir aussi [Utiliser PowerShell pour activer Diagnostics Azure sur une machine virtuelle exécutant Windows](../../virtual-machines/extensions/diagnostics-windows.md).

## <a name="data-storage"></a>Stockage des données
Le tableau suivant répertorie les différents types de données collectées à partir de l’extension de diagnostic et indique si elles sont stockées sous la forme d’une table ou d’un BLOB. Les données stockées dans des tableaux peuvent également être stockées dans des blobs en fonction du [paramètre StorageType](diagnostics-extension-schema-windows.md#publicconfig-element) de votre configuration publique.


| Données | Type de stockage | Description |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | Table de charge de travail | Moniteur de diagnostic et modifications de la configuration. |
| WADDirectoriesTable | Table de charge de travail | Répertoires que le moniteur de diagnostic surveille.  Cela inclut les journaux d’activité IIS, les journaux d’activité de requêtes ayant échoué et les répertoires personnalisés IIS.  L’emplacement du fichier journal blob est spécifié dans le champ de conteneur et le nom de l’objet blob se trouve dans le champ RelativePath.  Le champ AbsolutePath indique l’emplacement et le nom du fichier tel qu’il existait sur la machine virtuelle Azure. |
| WadLogsTable | Table de charge de travail | Journaux d’activité rédigés dans le code à l’aide de l’écouteur de suivi. |
| WADPerformanceCountersTable | Table de charge de travail | Compteurs de performance. |
| WADWindowsEventLogsTable | Table de charge de travail | Journaux des événements Windows. |
| wad-iis-failedreqlogfiles | Objet blob | Contient des informations issues des journaux d’activité de requêtes IIS ayant échoué. |
| wad-iis-logfiles | Objet blob | Contient des informations sur les journaux IIS. |
| « personnalisé » | Objet blob | Conteneur personnalisé basé sur la configuration des répertoires analysés par le moniteur de diagnostic.  Le nom de ce conteneur d’objets blobs est spécifié dans WADDirectoriesTable. |

## <a name="tools-to-view-diagnostic-data"></a>Outils permettant d’afficher les données de diagnostic
Plusieurs outils sont disponibles pour afficher les données après leur transfert vers le stockage. Par exemple :

* Explorateur de serveurs dans Visual Studio : si vous avez installé Microsoft Azure Tools pour Microsoft Visual Studio, vous pouvez utiliser le nœud de stockage Azure dans l’Explorateur de serveurs pour afficher des objets blobs en lecture seule et les données du tableau depuis vos comptes de stockage Azure. Vous pouvez afficher des données à partir de votre compte d’émulateur de stockage local et de comptes de stockage que vous avez créés pour Azure. Pour plus d’informations, consultez [Consultation et gestion des ressources de stockage avec l’Explorateur de serveurs](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* [L’explorateur de stockage Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md) est une application autonome qui vous permet d’utiliser facilement les données Azure Storage sur Windows, OSX et Linux.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) inclut Diagnostics Azure Manager qui vous permet d’afficher, de télécharger et de gérer les données de diagnostic collectées par les applications s’exécutant dans Azure.

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur le transfert des données d’analyse à Azure Event Hubs, consultez la page [Envoyer les données de l’extension Diagnostics Azure pour Windows à Event Hubs](diagnostics-extension-stream-event-hubs.md).
