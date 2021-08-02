---
title: Extension de machine virtuelle Azure Monitor Dependency pour Linux
description: Déployez l’agent Azure Monitor Dependency sur une machine virtuelle Linux à l’aide d’une extension de machine virtuelle.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: mgoedtel
ms.author: magoedte
ms.collection: linux
ms.date: 06/01/2021
ms.openlocfilehash: 97f557ec45530de3f42dd61ee1cded57fd7c33a0
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110793743"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-linux"></a>Extension de machine virtuelle Azure Monitor Dependency pour Linux

La fonctionnalité Azure Monitor pour machines virtuelles Map obtient ses données à partir de l’agent Microsoft Dependency. L’extension de machine virtuelle de l’agent Azure VM Dependency pour Linux est publiée et prise en charge par Microsoft. L’extension installe l’agent Dependency sur des machines virtuelles Azure. Ce document présente les plateformes, configurations et options de déploiement prises en charge pour l’extension de machine virtuelle Azure VM Dependency pour Linux.

## <a name="prerequisites"></a>Prérequis

### <a name="operating-system"></a>Système d’exploitation

L’extension de l’agent Azure VM Dependency pour Linux peut être exécutée sur les systèmes d’exploitation pris en charge répertoriés dans la section [Systèmes d’exploitation pris en charge](../../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) de l’article portant sur le déploiement d’Azure Monitor pour machines virtuelles.

## <a name="extension-schema"></a>Schéma d’extensions

Le JSON suivant montre le schéma de l’extension de l’agent Azure VM Dependency sur une machine virtuelle Linux Azure. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux Azure VM."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="property-values"></a>Valeurs de propriétés

| Nom | Valeur/Exemple |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| publisher | Microsoft.Azure.Monitoring.DependencyAgent |
| type | DependencyAgentLinux |
| typeHandlerVersion | 9.5 |

## <a name="template-deployment"></a>Déploiement de modèle

Vous pouvez déployer les extensions de machines virtuelles Azure avec des modèles Azure Resource Manager. Vous pouvez utiliser le schéma JSON détaillé dans la section précédente dans un modèle Azure Resource Manager pour exécuter l’extension de l’agent Azure VM Dependency pendant un déploiement de modèle Azure Resource Manager.

Le JSON d’une extension de machine virtuelle peut être imbriqué à l’intérieur de la ressource de machine virtuelle. Vous pouvez également le placer à la racine ou au niveau supérieur d’un modèle JSON Resource Manager. Le positionnement du JSON affecte la valeur du nom de la ressource et son type. Pour plus d’informations, consultez [Définition du nom et du type des ressources enfants](../../azure-resource-manager/templates/child-resource-name-type.md).

L’exemple suivant suppose que l’extension de l’agent Dependency est imbriquée dans la ressource de machine virtuelle. Lors de l’imbrication de la ressource d’extension, le JSON est placé dans l’objet `"resources": []` de la machine virtuelle.


```json
{
    "type": "extensions",
    "name": "DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

Lorsque vous placez l’extension JSON à la racine du modèle, le nom de ressource inclut une référence à la machine virtuelle parente. Le type reflète la configuration imbriquée. 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="azure-cli-deployment"></a>Déploiement de l’interface de ligne de commande Azure

Vous pouvez utiliser l’interface de ligne de commande Azure pour déployer l’extension de machine virtuelle de l’agent Dependency sur une machine virtuelle existante.  

```azurecli

az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 
```

## <a name="automatic-upgrade-preview"></a>Mise à niveau automatique (préversion)
Une nouvelle fonctionnalité de mise à niveau automatique des versions mineures de l’extension Dependency est désormais disponible en préversion publique. Vous devez effectuer les modifications de configuration suivantes pour activer cette fonctionnalité.

-   Utilisez l’une des méthodes indiquées dans [Activation de l’accès en préversion](../automatic-extension-upgrade.md#enabling-preview-access) pour activer la fonctionnalité pour votre abonnement.
- Ajoutez l’attribut `enableAutomaticUpgrade` au modèle.

Le schéma de contrôle de version de l’extension Dependency Agent respecte le format suivant :

```
<MM.mm.bb.rr> where M = Major version number, m = minor version number, b = bug number, r = revision number.
```

Les attributs `enableAutomaticUpgrade` et `autoUpgradeMinorVersion` fonctionnent ensemble pour déterminer comment les mises à niveau seront gérées pour les machines virtuelles de l’abonnement.

| enableAutomaticUpgrade | autoUpgradeMinorVersion | Résultat |
|:---|:---|:---|
| true | false | Mettez à niveau l’agent Dependency si une version plus récente de bb.rr existe. Par exemple, si vous exécutez 9.6.0.1355 et que la version la plus récente est 9.6.2.1366, vos machines virtuelles dans les abonnements activés seront mises à niveau vers 9.6.2.1366. |
| true | true |  Cela mettra à niveau l’agent Dependency si une version plus récente de mm.bb.rr ou bb.rr existe. Par exemple, si vous exécutez 9.6.0.1355 et que la version la plus récente est 9.7.1.1416, vos machines virtuelles dans les abonnements activés seront mises à niveau vers 9.7.1.1416. De même, si vous exécutez 9.6.0.1355 et que la version la plus récente est 9.6.2.1366, vos machines virtuelles dans les abonnements activés seront mises à niveau vers 9.6.2.1366. |
| false | True ou False | La mise à niveau automatique est désactivée.

> [!IMPORTANT]
> Si vous ajoutez `enableAutomaticUpgrade` à votre modèle, assurez-vous d’utiliser au moins l’API version 2019-12-01.

## <a name="troubleshoot-and-support"></a>Dépannage et support technique

### <a name="troubleshoot"></a>Dépanner

Vous pouvez récupérer les données sur l’état des déploiements d’extension à partir du portail Azure et à l’aide de l’interface de ligne de commande Azure. Pour afficher l’état du déploiement des extensions pour une machine virtuelle donnée, exécutez la commande suivante à l’aide de l’interface de ligne de commande Azure :

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

La sortie de l’exécution de l’extension est enregistrée dans le fichier suivant :

```
/opt/microsoft/dependency-agent/log/install.log 
```

### <a name="support"></a>Support

Si vous avez besoin d’une aide supplémentaire à quelque étape que ce soit dans cet article, vous pouvez contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/support/forums/). Vous pouvez également signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir un support**. Pour plus d’informations sur l’utilisation du support Azure, lisez le [FAQ du support Microsoft Azure](https://azure.microsoft.com/support/faq/).
