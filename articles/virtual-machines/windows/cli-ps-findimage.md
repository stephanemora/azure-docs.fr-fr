---
title: Sélectionner des images de machine virtuelle Windows dans Azure | Microsoft Docs
description: Utilisez Azure PowerShell pour identifier l’éditeur, l’offre, la référence SKU et la version d’images de machine virtuelle de la Place de marché.
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 188b8974-fabd-4cd3-b7dc-559cbb86b98a
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/08/2018
ms.author: danlep
ms.openlocfilehash: 5934e955d2a18d111c625670bced134df37ef045
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2018
ms.locfileid: "49409592"
---
# <a name="find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>Rechercher des images de machine virtuelle Windows sur la Place de marché Azure avec Azure PowerShell

Cet article décrit comment utiliser Azure PowerShell pour rechercher des images de machine virtuelle sur la Place de marché Microsoft Azure. Vous pouvez ensuite spécifier une image de la Place de marché lorsque vous créez une machine virtuelle par programmation avec PowerShell, des modèles Resource Manager ou d’autres outils.

Vous pouvez également parcourir les offres et images disponibles à l’aide de la vitrine [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com/), du [Portail Azure](https://portal.azure.com) ou d’[Azure CLI](../linux/cli-ps-findimage.md). 

Vérifiez que le dernier [module Azure PowerShell](/powershell/azure/install-azurerm-ps) est installé et configuré.

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="table-of-commonly-used-windows-images"></a>Tableau des images système Windows couramment utilisées
| Publisher | Offre | Sku |
|:--- |:--- |:--- |:--- |
| MicrosoftWindowsServer |WindowsServer |2016-centre-de-données |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-Server-Core |
| MicrosoftWindowsServer |WindowsServer |2016-centre-de-données-avec-conteneurs |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2012-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2008-R2-SP1 |
| MicrosoftDynamicsNAV |DynamicsNAV |2017 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2016 |
| MicrosoftSQLServer |SQL2017-WS2016 |Entreprise |
| MicrosoftRServer |RServer-WS2016 |Entreprise |

## <a name="navigate-the-images"></a>Parcourir les images

Une autre façon de trouver une image dans un emplacement consiste à exécuter les cmdlets [Get-AzureRMVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher), [Get-AzureRMVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer) et [Get-AzureRMVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) dans cet ordre :

1. en répertoriant les éditeurs d’images ;
2. pour un éditeur donné, en répertoriant ses offres ;
3. pour une offre donnée, en répertoriant ses références SKU.

Ensuite, pour une référence SKU sélectionnée, exécutez la cmdlet [Get-AzureRMVMImage](/powershell/module/azurerm.compute/get-azurermvmimage) pour dresser la liste des versions à déployer.

1. Répertoriez les éditeurs d’images :

    ```powershell
    $locName="<Azure location, such as West US>"
    Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
    ```

2. Indiquez le nom de l’éditeur choisi et répertoriez les offres :

    ```powershell
    $pubName="<publisher>"
    Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
    ```

3. Indiquez le nom de l’offre choisie et répertoriez les références SKU :

    ```powershell
    $offerName="<offer>"
    Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
    ```
    
4. Indiquez le nom de la référence choisie et obtenez la version de l’image :

    ```powershell
    $skuName="<SKU>"
    Get-AzureRMVMImage -Location $locName -Publisher $pubName -Offer $offerName -Sku $skuName | Select Version
    ```
    
Dans la sortie de la commande `Get-AzureRMVMImage`, vous pouvez sélectionner une image de version pour déployer une nouvelle machine virtuelle.

L’exemple suivant présente l’intégralité de la séquence des commandes et leurs sorties :

```powershell
$locName="West US"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
```

Résultat partiel :

```
PublisherName
-------------
...
a10networks
aiscaler-cache-control-ddos-and-url-rewriting-
alertlogic
AlertLogic.Extension
Barracuda.Azure.ConnectivityAgent
barracudanetworks
basho
boxless
bssw
Canonical
...
```

Pour l’éditeur *MicrosoftWindowsServer* :

```powershell
$pubName="MicrosoftWindowsServer"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

Sortie :

```
Offer
-----
Windows-HUB
WindowsServer
WindowsServerSemiAnnual
```

Pour l’offre *WindowsServer* :

```powershell
$offerName="WindowsServer"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

Sortie :

```
Skus
----
2008-R2-SP1
2008-R2-SP1-smalldisk
2012-Datacenter
2012-Datacenter-smalldisk
2012-R2-Datacenter
2012-R2-Datacenter-smalldisk
2016-Datacenter
2016-Datacenter-Server-Core
2016-Datacenter-Server-Core-smalldisk
2016-Datacenter-smalldisk
2016-Datacenter-with-Containers
2016-Datacenter-with-RDSH
```

Ensuite, pour la référence SKU *2016-centre-de-données* :

```powershell
$skuName="2016-Datacenter"
Get-AzureRMVMImage -Location $locName -Publisher $pubName -Offer $offerName -Sku $skuName | Select Version
```

Maintenant, vous pouvez combiner l’éditeur, l’offre, la référence SKU et la version sélectionnés en un schéma URN (valeurs séparées par :). Transmettez cet URN avec le paramètre `--image` lorsque vous créez une machine virtuelle avec la cmdlet [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Vous pouvez également remplacer le numéro de version dans l’URN par « latest » (dernière) afin d’obtenir la dernière version de l’image.

Si vous déployez une machine virtuelle avec un modèle Resource Manager, vous définissez alors les paramètres d’image de manière individuelle dans les propriétés `imageReference`. Consultez la [référence de modèle](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Afficher les propriétés du plan

Pour afficher les informations du plan d’achat d’une image, exécutez la cmdlet `Get-AzureRMVMImage`. Si la propriété `PurchasePlan` dans la sortie n’est pas `null`, l’image a des conditions que vous devez accepter avant le déploiement par programmation.  

Par exemple, l’image *Windows Server 2016 Datacenter* ne possède pas de conditions supplémentaires, car l’information `PurchasePlan` est `null` :

```powershell
$version = "2016.127.20170406"
Get-AzureRMVMImage -Location $locName -Publisher $pubName -Offer $offerName -Skus $skuName -Version $version
```

Sortie :

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/
                   Versions/2016.127.20170406
Location         : westus
PublisherName    : MicrosoftWindowsServer
Offer            : WindowsServer
Skus             : 2016-Datacenter
Version          : 2016.127.20170406
FilterExpression :
Name             : 2016.127.20170406
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : null
DataDiskImages   : []

```

L’exemple ci-dessous montre une commande similaire pour l’image *Data Science Virtual Machine - Windows 2016*, ayant les propriétés `PurchasePlan` suivantes : `name`, `product` et `publisher`. Certaines images ont également une propriété `promotion code`. Pour déployer cette image, consultez les sections suivantes pour accepter les conditions et activer le déploiement par programmation.

```powershell
Get-AzureRMVMImage -Location "westus" -Publisher "microsoft-ads" -Offer "windows-data-science-vm" -Skus "windows2016" -Version "0.2.02"
```

Sortie :

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/microsoft-ads/ArtifactTypes/VMIma
                   ge/Offers/windows-data-science-vm/Skus/windows2016/Versions/0.2.02
Location         : westus
PublisherName    : microsoft-ads
Offer            : windows-data-science-vm
Skus             : windows2016
Version          : 0.2.02
FilterExpression :
Name             : 0.2.02
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : {
                     "publisher": "microsoft-ads",
                     "name": "windows2016",
                     "product": "windows-data-science-vm"
                   }
DataDiskImages   : []

```

### <a name="accept-the-terms"></a>Accepter les conditions

Pour afficher les termes du contrat de licence, utilisez la cmdlet [Get-AzureRmMarketplaceterms](/powershell/module/azurerm.marketplaceordering/get-azurermmarketplaceterms) et transmettez les paramètres de plan d’achat. La sortie contient un lien vers les conditions de l’image de la Marketplace et indique si vous les avez acceptées précédemment. Veillez à utiliser uniquement des lettres minuscules dans les valeurs de paramètre.

```powershell
Get-AzureRmMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
```

Sortie :

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DV
                    M%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : 2UMWH6PHSAIM4U22HXPXW25AL2NHUJ7Y7GRV27EBL6SUIDURGMYG6IIDO3P47FFIBBDFHZHSQTR7PNK6VIIRYJRQ3WXSE6BTNUNENXA
Accepted          : False
Signdate          : 2/23/2018 7:43:00 PM
```

Utilisez la cmdlet [Set-AzureRmMarketplaceterms](/powershell/module/azurerm.marketplaceordering/set-azurermmarketplaceterms) pour accepter ou rejeter les conditions. Vous ne devez accepter qu’une fois les conditions par abonnement pour l’image. Veillez à utiliser uniquement des lettres minuscules dans les valeurs de paramètre. 

```powershell
$agreementTerms=Get-AzureRmMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"

Set-AzureRmMarketplaceTerms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016" -Terms $agreementTerms -Accept
```

Sortie :

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DV
                    M%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : XXXXXXK3MNJ5SROEG2BYDA2YGECU33GXTD3UFPLPC4BAVKAUL3PDYL3KBKBLG4ZCDJZVNSA7KJWTGMDSYDD6KRLV3LV274DLBXXXXXX
Accepted          : True
Signdate          : 2/23/2018 7:49:31 PM
```

### <a name="deploy-using-purchase-plan-parameters"></a>Procéder au déploiement à l’aide des paramètres de plan d’achat

Après avoir accepté les conditions d’une image, vous pouvez déployer une machine virtuelle dans cet abonnement. Comme illustré dans l’extrait de code suivant, utilisez la cmdlet [Set-AzureRmVMPlan](/powershell/module/azurerm.compute/set-azurermvmplan) pour définir les informations du plan de la Marketplace pour l’objet de machine virtuelle. Pour obtenir un script complet afin de créer des paramètres réseau pour la machine virtuelle et de terminer le déploiement, consultez les [exemples de script PowerShell](powershell-samples.md).

```powershell
...

$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace plan information

$publisherName = "microsoft-ads"

$productName = "windows-data-science-vm"

$planName = "windows2016"

$vmConfig = Set-AzureRmVMPlan -VM $vmConfig -Publisher $publisherName -Product $productName -Name $planName

$cred=Get-Credential

$vmConfig = Set-AzureRmVMOperatingSystem -Windows -VM $vmConfig -ComputerName "myVM" -Credential $cred

# Set the Marketplace image

$offerName = "windows-data-science-vm"

$skuName = "windows2016"

$version = "0.2.02"

$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version $version
...
```
Vous transmettez ensuite la configuration de la machine virtuelle en même temps que les objets de configuration du réseau à la cmdlet `New-AzureRmVM`.

## <a name="next-steps"></a>Étapes suivantes
Pour créer rapidement une machine virtuelle avec la cmdlet `New-AzureRmVM` en utilisant des informations d’image de base, consultez [Créer une machine virtuelle Windows avec PowerShell](quick-create-powershell.md).

Consultez un exemple de script PowerShell pour [créer une machine virtuelle entièrement configurée](../scripts/virtual-machines-windows-powershell-sample-create-vm.md).


