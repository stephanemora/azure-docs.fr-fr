---
title: Module PowerShell pour Azure Lab Services | Microsoft Docs
description: Cet article fournit des informations sur un module PowerShell qui facilite la gestion des artefacts dans Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2019
ms.author: spelluru
ms.openlocfilehash: cc82355ee43f9fee4f9c2e1bb1bcc0481e4dcea3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73608541"
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
1. Installez [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) s’il n’existe pas sur votre ordinateur. 
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
