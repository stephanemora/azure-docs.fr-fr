---
title: Gestionnaire d’extensions de configuration d’état souhaité Azure | Microsoft Docs
description: Charger et appliquer une configuration DSC PowerShell sur une machine virtuelle Azure à l’aide de l’extension DSC
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: windows
ms.workload: ''
ms.date: 03/26/2018
ms.author: robreed
ms.openlocfilehash: b9e96473a6f66dcbc675da1553deaed4ad61b249
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2018
ms.locfileid: "45630923"
---
# <a name="powershell-dsc-extension"></a>Extension de configuration d’état souhaité PowerShell

## <a name="overview"></a>Vue d’ensemble

L’extension DSC PowerShell pour Windows est publiée et prise en charge par Microsoft. L’extension charge et applique une Configuration DSC PowerShell sur une machine virtuelle Azure. L’extension DSC fait appel à DSC PowerShell pour mettre en œuvre la configuration DSC reçue sur la machine virtuelle. Ce document présente les plateformes, configurations et options de déploiement qui sont prises en charge pour l’extension de machine virtuelle DSC pour Windows.

## <a name="prerequisites"></a>Prérequis

### <a name="operating-system"></a>Système d’exploitation

L’extension DSC prend en charge les systèmes d’exploitation suivants

Windows Server 2016, Windows Server 2012, Windows Server 2012 R2, Windows Server 2008 R2 SP1, Windows Client 7/8.1

### <a name="internet-connectivity"></a>Connectivité Internet

La machine virtuelle cible doit être connectée à Internet pour pouvoir utiliser l’extension DSC pour Windows. 

## <a name="extension-schema"></a>Schéma d’extensions

L’extrait JSON suivant montre le schéma de la section des paramètres de l’extension DSC dans un modèle Azure Resource Manager. 

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "Microsoft.Powershell.DSC",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "properties": {
    "publisher": "Microsoft.Powershell",
    "type": "DSC",
    "typeHandlerVersion": "2.73",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "wmfVersion": "latest",
        "configuration": {
            "url": "http://validURLToConfigLocation",
            "script": "ConfigurationScript.ps1",
            "function": "ConfigurationFunction"
        },
        "configurationArguments": {
            "argument1": "Value1",
            "argument2": "Value2"
        },
        "configurationData": {
            "url": "https://foo.psd1"
        },
        "privacy": {
            "dataCollection": "enable"
        },
        "advancedOptions": {
            "forcePullAndApply": false
            "downloadMappings": {
                "specificDependencyKey": "https://myCustomDependencyLocation"
            }
        } 
    },
    "protectedSettings": {
        "configurationArguments": {
            "parameterOfTypePSCredential1": {
                "userName": "UsernameValue1",
                "password": "PasswordValue1"
            },
            "parameterOfTypePSCredential2": {
                "userName": "UsernameValue2",
                "password": "PasswordValue2"
            }
        },
        "configurationUrlSasToken": "?g!bber1sht0k3n",
        "configurationDataUrlSasToken": "?dataAcC355T0k3N"
    }
  }
}
```

### <a name="property-values"></a>Valeurs de propriétés

| NOM | Valeur/Exemple | Type de données |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Powershell.DSC | chaîne |
| Type | DSC | chaîne |
| typeHandlerVersion | 2.73 | int |

### <a name="settings-property-values"></a>Valeurs de la propriété Settings

| NOM | Type de données | Description
| ---- | ---- | ---- |
| settings.wmfVersion | chaîne | Spécifie la version de Windows Management Framework qui doit être installée sur votre machine virtuelle. Lorsque cette propriété est définie sur « latest », la dernière version de WMF est installée. Actuellement, les seules valeurs possibles pour cette propriété sont « 4.0 », « 5.0 » et « latest ». Les valeurs possibles font l’objet de mises à jour. La valeur par défaut est « latest ». |
| settings.configuration.url | chaîne | Spécifie l’adresse URL de téléchargement de votre fichier .zip de configuration DSC. Si l’accès à l’URL fournie nécessite un jeton SAP, vous devez définir la propriété protectedSettings.configurationUrlSasToken sur la valeur du jeton SAP. Cette propriété est requise si la propriété settings.configuration.script ou settings.configuration.function est définie.
| settings.configuration.script | chaîne | Spécifie le nom de fichier du script qui contient la définition de votre configuration DSC. Ce script doit se trouver dans le dossier racine du fichier .zip téléchargé depuis l’URL spécifiée par la propriété configuration.url. Cette propriété est requise si la propriété settings.configuration.url ou settings.configuration.script est définie.
| settings.configuration.function | chaîne | Spécifie le nom de votre configuration DSC. La configuration nommée doit se trouver dans le script défini par configuration.script. Cette propriété est requise si la propriété settings.configuration.url ou settings.configuration.function est définie.
| settings.configurationArguments | Collection | Définit les paramètres à transmettre à votre configuration DSC. Cette propriété n’est pas chiffrée.
| settings.configurationData.url | chaîne | Spécifie l’URL de téléchargement de votre fichier de données de configuration (.pds1) à utiliser comme entrée pour votre configuration DSC. Si l’accès à l’URL fournie nécessite un jeton SAP, vous devez définir la propriété protectedSettings.configurationDataUrlSasToken sur la valeur du jeton SAP.
| settings.privacy.dataEnabled | chaîne | Active ou désactive la collecte télémétrique. Les seules valeurs possibles pour cette propriété sont « Enable », « Disable », '' ou $null. Le fait de laisser cette propriété vide ou de la définir comme nulle active la télémétrie
| settings.advancedOptions.forcePullAndApply | Bool | Permet à l’extension DSC de mettre à jour et de promulguer des configurations DSC lorsque le mode d’actualisation est Pull.
| settings.advancedOptions.downloadMappings | Collection | Définit d’autres emplacements pour télécharger des dépendances, telles que WMF et .NET

### <a name="protected-settings-property-values"></a>Valeurs de la propriété Protected Settings

| NOM | Type de données | Description
| ---- | ---- | ---- |
| protectedSettings.configurationArguments | chaîne | Définit les paramètres à transmettre à votre configuration DSC. Cette propriété est chiffrée. |
| protectedSettings.configurationUrlSasToken | chaîne | Spécifie le jeton SAP permettant d’accéder à l’URL définie par configuration.url. Cette propriété est chiffrée. |
| protectedSettings.configurationDataUrlSasToken | chaîne | Spécifie le jeton SAP permettant d’accéder à l’URL définie par configurationData.url. Cette propriété est chiffrée. |


## <a name="template-deployment"></a>Déploiement de modèle

Les extensions de machines virtuelles Azure peuvent être déployées avec des modèles Azure Resource Manager. Les modèles sont idéaux lorsque vous déployez une ou plusieurs machines virtuelles nécessitant une configuration post-déploiement. Vous trouverez un exemple de modèle Resource Manager qui inclut l’extension de machine virtuelle d’agent OMS dans la [galerie de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates/tree/052db5feeba11f85d57f170d8202123511f72044/dsc-extension-iis-server-windows-vm). 

La configuration JSON pour une extension de machine virtuelle peut être imbriquée à l’intérieur de la ressource de machine virtuelle ou placée à la racine ou au niveau supérieur d’un modèle de Resource Manager JSON. Le positionnement de la configuration JSON affecte la valeur du nom de la ressource et son type. 

Lors de l’imbrication de la ressource d’extension, le JSON est placé dans l’objet `"resources": []` de la machine virtuelle. Lorsque vous placez l’extension JSON à la racine du modèle, le nom de ressource inclut une référence à la machine virtuelle parente, et le type reflète la configuration imbriquée.  


## <a name="azure-cli-deployment"></a>Déploiement de l’interface de ligne de commande Azure

Vous pouvez utiliser l’interface de ligne de commande Azure pour déployer l’extension de machine virtuelle d’agent OMS sur une machine virtuelle existante. Remplacez la clé OMS et l’ID OMS par ceux de votre espace de travail OMS. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name Microsoft.Powershell.DSC \
  --publisher Microsoft.Powershell \
  --version 2.73 --protected-settings '{}' \
  --settings '{}'
```

## <a name="troubleshoot-and-support"></a>Dépannage et support technique

### <a name="troubleshoot"></a>Résolution des problèmes

Vous pouvez récupérer les données sur l’état des déploiements d’extension à partir du portail Azure et à l’aide de l’interface de ligne de commande Azure. Pour afficher l’état du déploiement des extensions pour une machine virtuelle donnée, exécutez la commande suivante à l’aide de l’interface de ligne de commande Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Le package d’extension est téléchargé et déployé à cet emplacement sur la machine virtuelle Azure
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}
```

Le fichier d’état de l’extension contient le statut secondaire et les codes de réussite ou d’erreur de l’état ainsi que l’erreur détaillée et la description de chaque exécution de l’extension.
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}\Status\{0}.Status  -> {0} being the sequence number
```

Les journaux de sortie de l’extension sont enregistrés dans le répertoire suivant :

```
C:\WindowsAzure\Logs\Plugins\{Extension_Name}\{Extension_Version}
```

### <a name="error-codes-and-their-meanings"></a>Codes d’erreur et signification

| Code d'erreur | Signification | Action possible |
| :---: | --- | --- |
| 1 000 | Erreur générique | Le message de cette erreur est fourni par l’exception spécifique dans les journaux de l’extension |
| 52 | Erreur d’installation de l’extension | Le message de cette erreur est fourni par l’exception spécifique |
| 1002 | Erreur d’installation de WMF | Erreur durant l'installation de WMF. |
| 1004 | Package Zip non valide | Zip non valide ; Erreur lors de la décompression du fichier zip |
| 1100 | Erreur d'argument | Indique un problème dans l’entrée fournie par l’utilisateur. Le message de l’erreur est fourni par l’exception spécifique|


### <a name="support"></a>Support

Si vous avez besoin d’une aide supplémentaire à quelque étape que ce soit dans cet article, vous pouvez contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/support/forums/). Vous pouvez également signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur Obtenir un support. Pour plus d’informations sur l’utilisation du support Azure, lisez le [FAQ du support Microsoft Azure](https://azure.microsoft.com/support/faq/).