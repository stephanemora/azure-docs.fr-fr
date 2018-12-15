---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: aeefb63a283c473a98639ead1aa6640d88409125
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318128"
---
## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure avec la commande `Connect-AzureRmAccount` et suivez les instructions à l’écran.

```powershell
Connect-AzureRmAccount
```

Si vous ne savez pas quelle localisation utiliser, affichez la liste des localisations disponibles. Affichez la liste des localisations avec l’exemple de code suivant et trouvez celui que vous souhaitez utiliser. Cet exemple utilise **eastus**. Stockez la localisation dans une variable et utilisez cette variable pour changer son contenu dans un seul emplacement.

```powershell
Get-AzureRmLocation | select Location
$location = "eastus"
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources Azure avec [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

```powershell
$resourceGroup = "myResourceGroup"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>Créez un compte de stockage.

Créez un compte de stockage standard à usage général avec réplication LRS avec la cmdlet [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). Ensuite, obtenez le contexte du compte de stockage qui définit le compte de stockage à utiliser. Si un compte de stockage est utilisé, référencez le contexte au lieu d’entrer les informations d’identification à plusieurs reprises. Utilisez l’exemple suivant pour créer un compte de stockage nommé *mystorageaccount* avec les options de stockage localement redondant (LRS) et de chiffrement des objets blob (activées par défaut).

```powershell
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name "mystorageaccount" `
  -SkuName Standard_LRS `
  -Location $location `

$ctx = $storageAccount.Context
```
