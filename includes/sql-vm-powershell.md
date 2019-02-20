---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: 3dc799ecc75589279c8d1c73062a8f2157761330
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56213018"
---
## <a name="start-your-powershell-session"></a>Démarrer votre session PowerShell
 

Exécutez la cmdlet [**Connect-Az Account**](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount) pour faire apparaître un écran de connexion dans lequel vous pouvez entrer vos informations d’identification. Utilisez les informations d’identification dont vous disposez pour vous connecter au portail Azure.

```powershell
Connect-AzAccount
```

Si vous possédez plusieurs abonnements, utilisez la cmdlet [**Set-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext) pour sélectionner l’abonnement que votre session PowerShell doit utiliser. Pour savoir quel abonnement la session PowerShell en cours utilise, exécutez la cmdlet [**Get-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/get-azcontext). Pour voir tous vos abonnements, exécutez la cmdlet [**Get-AzSubscription**](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription).

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```

