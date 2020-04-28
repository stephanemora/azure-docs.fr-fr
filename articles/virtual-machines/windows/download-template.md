---
title: Télécharger le modèle d’une machine virtuelle Azure
description: Téléchargez le modèle pour une machine virtuelle à l’aide du portail ou de PowerShell.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: af6905f0ba62a9053e44134348721312ade6b9d7
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085380"
---
# <a name="download-the-template-for-a-vm"></a>Télécharger le modèle d’une machine virtuelle
Lorsque vous créez une machine virtuelle dans Azure à l’aide du portail ou de PowerShell, un modèle Resource Manager est automatiquement créé pour vous. Vous pouvez utiliser ce modèle pour dupliquer rapidement un déploiement. Le modèle contient des informations sur toutes les ressources d’un groupe de ressources. Pour une machine virtuelle, cela signifie que le modèle contient tout ce qui est créé pour prendre en charge la machine virtuelle dans ce groupe de ressources, notamment les ressources réseau.

## <a name="download-the-template-using-the-portal"></a>Télécharger le modèle à l’aide du portail
1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans le menu de gauche, sélectionnez **Machines virtuelles**.
3. Sélectionnez la machine virtuelle dans la liste.
4. Sélectionnez **Exporter le modèle**.
5. Sélectionnez **Télécharger** depuis le menu en haut et enregistrez le fichier .zip sur votre ordinateur local.
6. Ouvrez le fichier .zip et extrayez les fichiers dans un dossier. Le fichier .zip contient :
   
   * parameters.json
   * template.json

Le fichier template.json est le modèle.

## <a name="download-the-template-using-powershell"></a>Télécharger le modèle à l’aide de PowerShell
Vous pouvez également télécharger le fichier de modèle .json à l’aide de la cmdlet [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup). Vous pouvez utiliser le paramètre `-path` afin de fournir le nom et le chemin d’accès du fichier .json. Cet exemple montre comment télécharger le modèle pour le groupe de ressources nommé **myResourceGroup** vers le dossier **C:\users\public\downloads** sur votre ordinateur local.

```powershell
    Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur le déploiement des ressources à l’aide de modèles, consultez la page [Procédure pas à pas du modèle Resource Manager](../../azure-resource-manager/resource-manager-template-walkthrough.md).

