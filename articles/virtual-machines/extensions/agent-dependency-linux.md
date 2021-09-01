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
ms.openlocfilehash: ce4b7ee2fea9d5b2b7c92b5ade1b3ad146382214
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562999"
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

## <a name="automatic-extension-upgrade"></a>Mise à niveau automatique des extensions
Une nouvelle fonctionnalité de [mise à niveau automatique des versions mineures](../automatic-extension-upgrade.md) de l’extension Dependency est désormais disponible.

Pour activer la mise à niveau automatique pour une extension, vous devez vous assurer que la propriété `enableAutomaticUpgrade` est définie sur `true` et ajoutée au modèle d’extension. Cette propriété doit être activée sur chaque machine virtuelle ou groupe identique de machines virtuelles individuellement. Utilisez l’une des méthodes décrites dans la section [activation](../automatic-extension-upgrade.md#enabling-automatic-extension-upgrade) pour activer la fonctionnalité pour votre machine virtuelle ou votre groupe identique de machines virtuelles.

Lorsque la mise à niveau automatique des extensions est activée sur une machine virtuelle ou un groupe identique de machines virtuelles, l’extension est automatiquement mise à niveau chaque fois que l’éditeur de l’extension publie une nouvelle version pour cette extension. La mise à niveau est appliquée en toute sécurité selon les principes de première disponibilité, comme décrit [ici](../automatic-extension-upgrade.md#how-does-automatic-extension-upgrade-work).

Le fonctionnement de l’attribut `enableAutomaticUpgrade` est différent de celui de `autoUpgradeMinorVersion`. Les attributs `autoUpgradeMinorVersion` ne déclenchent pas automatiquement une mise à jour de version mineure lorsque l’éditeur d’extension publie une nouvelle version. L’attribut `autoUpgradeMinorVersion` indique si l’extension doit utiliser une version mineure plus récente si celle-ci est disponible au moment du déploiement. Cependant, une fois déployée, l’extension ne mettra pas à jour les versions mineures à moins d’être redéployée, même si cette propriété est définie sur true.

Pour maintenir la version de votre extension à jour, nous vous recommandons d’utiliser `enableAutomaticUpgrade` pour le déploiement de votre extension.

> [!IMPORTANT]
> Si vous ajoutez `enableAutomaticUpgrade` à votre modèle, assurez-vous d’utiliser l’API version 2019-12-01 ou version ultérieure.

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
