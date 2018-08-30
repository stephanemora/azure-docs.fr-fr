---
title: Prise en main d’Analytique Data Lake à l’aide d’Azure PowerShell
description: Utilisez Azure PowerShell pour créer un compte Azure Data Lake Analytics, puis envoyer et exécuter un travail U-SQL.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 8a4e901e-9656-4a60-90d0-d78ff2f00656
ms.topic: conceptual
ms.date: 05/04/2017
ms.openlocfilehash: 4b4fa05164db402122efc745302dffe8c130b97b
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43048066"
---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Prise en main d’Analytique Data Lake à l’aide d’Azure PowerShell
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Découvrez comment utiliser Azure PowerShell pour créer des comptes Azure Data Lake Analytics, puis envoyer et exécuter des travaux U-SQL. Pour plus d’informations sur Analytique Data Lake, consultez [Présentation d’Analytique Data Lake Azure](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Prérequis

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

* **Un compte Azure Data Lake Analytics**. Consultez [Prise en main de Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal).
* **Un poste de travail sur lequel est installé Azure PowerShell**. Consultez [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).

## <a name="log-in-to-azure"></a>Connexion à Azure

Ce didacticiel suppose que vous savez déjà utiliser Azure PowerShell. En particulier, vous devez savoir comment vous connecter à Azure. Consultez [Prise en main d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) si vous avez besoin d’aide.

Pour vous connecter avec un nom d’abonnement :

```
Connect-AzureRmAccount -SubscriptionName "ContosoSubscription"
```

Au lieu du nom d’abonnement, vous pouvez également utiliser un ID d’abonnement pour vous connecter :

```
Connect-AzureRmAccount -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

En cas de réussite, la sortie de cette commande ressemble au texte suivant :

```
Environment           : AzureCloud
Account               : joe@contoso.com
TenantId              : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionId        : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionName      : ContosoSubscription
CurrentStorageAccount :
```

## <a name="preparing-for-the-tutorial"></a>Préparation pour le didacticiel

Dans ce didacticiel, les extraits de code PowerShell utilisent ces variables pour stocker ces informations :

```
$rg = "<ResourceGroupName>"
$adls = "<DataLakeStoreAccountName>"
$adla = "<DataLakeAnalyticsAccountName>"
$location = "East US 2"
```

## <a name="get-information-about-a-data-lake-analytics-account"></a>Obtenir des informations sur un compte Data Lake Analytics

```
Get-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla  
```

## <a name="submit-a-u-sql-job"></a>Envoyer un travail U-SQL

Créez une variable PowerShell qui contiendra le script U-SQL.

```
$script = @"
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();

"@
```

Envoyez le texte du script avec la cmdlet `Submit-AdlJob` et le paramètre `-Script`.

```
$job = Submit-AdlJob -Account $adla -Name "My Job" �Script $script
```

Sinon, vous pouvez envoyer un fichier de script à l’aide du paramètre `-ScriptPath` :

```
$filename = "d:\test.usql"
$script | out-File $filename
$job = Submit-AdlJob -Account $adla -Name "My Job" �ScriptPath $filename
```

Récupérez l’état d’un travail avec `Get-AdlJob`. 

```
$job = Get-AdlJob -Account $adla -JobId $job.JobId
```

Au lieu d’appeler Get-AdlJob maintes et maintes fois jusqu’à ce qu’un travail aboutisse, utilisez la cmdlet `Wait-AdlJob`.

```
Wait-AdlJob -Account $adla -JobId $job.JobId
```

Téléchargez le fichier de sortie à l’aide de `Export-AdlStoreItem`.

```
Export-AdlStoreItem -Account $adls -Path "/data.csv" -Destination "C:\data.csv"
```

## <a name="see-also"></a>Voir aussi
* Pour afficher le même didacticiel en utilisant d’autres outils, cliquez sur les sélecteurs d’onglet en haut de la page.
* Pour connaître U-SQL, voir [Prise en main du langage U-SQL d’Analytique Data Lake Azure](data-lake-analytics-u-sql-get-started.md).
* Pour les tâches de gestion, consultez [Gestion d’Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-manage-use-portal.md).
