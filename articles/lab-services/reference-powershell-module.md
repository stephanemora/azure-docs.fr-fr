---
title: Module PowerShell pour Azure Lab Services | Microsoft Docs
description: Cet article fournit des informations sur un module PowerShell qui facilite la gestion des artefacts dans Azure Lab Services.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 81ed7d689105aa459151d698466b98f71f7b352e
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177685"
---
# <a name="azlabservices-powershell-module-preview"></a>Module PowerShell Az.LabServices (préversion)
Az.LabServices est un module PowerShell qui simplifie la gestion d’Azure Lab Services. Il offre des fonctions composables permettant de créer, d’interroger, de mettre à jour et de supprimer des comptes de labo, des labos, des machines virtuelles et des images. Pour plus d’informations sur ce module, consultez la [page d’accueil Az.LabServices sur GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

> [!NOTE]
> Ce module est en **préversion**. 

## <a name="example-command"></a>Exemple de commande
Voici un exemple d’utilisation d’une commande PowerShell pour arrêter toutes les machines virtuelles en cours d’exécution dans tous les labos.

```powershell
Get-AzLabAccount | Get-AzLab | Get-AzLabVm -Status Running | Stop-AzLabVm
```

## <a name="get-started"></a>Bien démarrer
1. Installez [Azure PowerShell](/powershell/azure/) s’il n’existe pas sur votre ordinateur. 
2. Téléchargez [Az.LabServices.psm1](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Modules/Library/Az.LabServices.psm1) sur votre machine.
3. Importez le module :

    ```powershell
    Import-Module .\Az.LabServices.psm1
    ```
4. Exécutez la commande suivante pour répertorier tous les labos de votre abonnement.

    ```powershell
    Get-AzLabAccount | Get-AzLab
    ```

## <a name="next-steps"></a>Étapes suivantes
Consultez la [page d’accueil Az.LabServices sur GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).