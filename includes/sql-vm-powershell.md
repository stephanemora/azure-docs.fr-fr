---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: 27da28073b01c6bc43868172d6c989d9518d8f53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95564054"
---
## <a name="start-your-powershell-session"></a>Démarrer votre session PowerShell
 

Exécutez la cmdlet [**Connect-Az Account**](/powershell/module/Az.Accounts/Connect-AzAccount) pour faire apparaître un écran de connexion dans lequel vous pouvez entrer vos informations d’identification. Utilisez les informations d’identification dont vous disposez pour vous connecter au portail Azure.

```powershell
Connect-AzAccount
```

Si vous possédez plusieurs abonnements, utilisez la cmdlet [**Set-AzContext**](/powershell/module/az.accounts/set-azcontext) pour sélectionner l’abonnement que votre session PowerShell doit utiliser. Pour savoir quel abonnement la session PowerShell en cours utilise, exécutez la cmdlet [**Get-AzContext**](/powershell/module/az.accounts/get-azcontext). Pour voir tous vos abonnements, exécutez la cmdlet [**Get-AzSubscription**](/powershell/module/az.accounts/get-azsubscription).

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```