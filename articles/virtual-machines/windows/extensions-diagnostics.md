---
title: Extension Azure Diagnostics pour Windows | Microsoft Docs
description: Surveiller les machines virtuelles Windows Azure à l’aide de l’extension Azure Diagnostics
services: virtual-machines-windows
documentationcenter: ''
author: johnkemnetz
manager: ashwink
editor: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/06/2018
ms.author: johnkem
ms.openlocfilehash: db4208c8fef27d2e2085e63ba3a986456d0544bf
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49429114"
---
# <a name="azure-diagnostics-extension-for-windows-vms"></a>Extension Azure Diagnostics pour machines virtuelles Windows

## <a name="overview"></a>Vue d’ensemble

L’extension de machine virtuelle Azure Diagnostics vous permet de collecter des données de surveillance, comme des compteurs de performances et des journaux d’événements, à partir de votre machine virtuelle Windows. Vous pouvez définir de façon granulaire les données à collecter et leur emplacement de destination, par exemple un compte Stockage Azure ou un hub d’événements Azure. Vous pouvez également utiliser ces données pour générer des graphiques dans le portail Azure ou créer des alertes de métrique.

## <a name="prerequisites"></a>Prérequis

### <a name="operating-system"></a>Système d’exploitation

L’extension Azure Diagnostics peut être exécutée sur un client Windows 10, ainsi que sur Windows Server 2008 R2, 2012, 2012 R2 et 2016.

### <a name="internet-connectivity"></a>Connectivité Internet

L’extension Azure Diagnostics nécessite que la machine virtuelle cible soit connectée à Internet. 

## <a name="extension-schema"></a>Schéma d’extensions

[Le schéma et les valeurs de propriété de l’extension Azure Diagnostics sont décrits dans ce document.](../../monitoring-and-diagnostics/azure-diagnostics-schema-1dot3-and-later.md)

## <a name="template-deployment"></a>Déploiement de modèle

Les extensions de machines virtuelles Azure peuvent être déployées avec des modèles Azure Resource Manager. Le schéma JSON détaillé dans la section précédente peut être utilisé dans un modèle Azure Resource Manager pour exécuter l’extension Azure Diagnostics pendant le déploiement d’un modèle Azure Resource Manager. Pour plus d’informations, consultez [Utiliser la surveillance et les diagnostics avec une machine virtuelle Windows et des modèles Azure Resource Manager](extensions-diagnostics-template.md).

## <a name="azure-cli-deployment"></a>Déploiement de l’interface de ligne de commande Azure

Vous pouvez utiliser Azure CLI pour déployer l’extension Azure Diagnostics sur une machine virtuelle existante. Remplacez les paramètres protégés et leurs propriétés par un JSON valide issu du schéma d’extension ci-dessus. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --version 1.9.0.0 --protected-settings protected-settings.json \
  --settings public-settings.json 
```

## <a name="powershell-deployment"></a>Déploiement PowerShell

Vous pouvez utiliser la commande `Set-AzureRmVMDiagnosticsExtension` pour ajouter l’extension Azure Diagnostics à une machine virtuelle existante. Voir aussi [Utiliser PowerShell pour activer Azure Diagnostics sur une machine virtuelle exécutant Windows](ps-extensions-diagnostics.md).
```powershell
$vm_resourcegroup = "myvmresourcegroup"
$vm_name = "myvm"
$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup `
  -VMName $vm_name `
  -DiagnosticsConfigurationPath $diagnosticsconfig_path
```

## <a name="troubleshoot-and-support"></a>Dépannage et support technique

### <a name="troubleshoot"></a>Résolution des problèmes

Vous pouvez récupérer les données sur l’état des déploiements d’extension à partir du portail Azure et à l’aide de l’interface de ligne de commande Azure. Pour afficher l’état du déploiement des extensions pour une machine virtuelle donnée, exécutez la commande suivante à l’aide de l’interface de ligne de commande Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Pour plus d’informations sur la résolution des problèmes liés à l’extension Azure Diagnostics, [consultez cet article](../../monitoring-and-diagnostics/azure-diagnostics-troubleshooting.md).

### <a name="support"></a>Support

Si vous avez besoin d’une aide supplémentaire à quelque étape que ce soit dans cet article, vous pouvez contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/support/forums/). Vous pouvez également signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur Obtenir un support. Pour plus d’informations sur l’utilisation du support Azure, lisez le [FAQ du support Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus sur l’extension Azure Diagnostics](../../monitoring-and-diagnostics/azure-diagnostics.md)
* [Examinez le schéma et les versions de l’extension](../../monitoring-and-diagnostics/azure-diagnostics-schema.md)
