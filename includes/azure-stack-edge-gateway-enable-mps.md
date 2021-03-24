---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/11/2021
ms.author: alkohli
ms.openlocfilehash: b2c1ebe390b1a2dec7be678b5d6f3a991056a23b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102517556"
---
1. Avant de commencer, assurez-vous que :

    1. Vous avez configuré et [Activé votre appareil Azure Stack Edge Pro](../articles/databox-online/azure-stack-edge-gpu-deploy-activate.md) avec une ressource Azure Stack Edge dans Azure.
    1. Vous avez [Configuré le calcul sur cet appareil dans le portail Azure](../articles/databox-online/azure-stack-edge-deploy-configure-compute.md#configure-compute).
    
1. [Connectez-vous à l’interface PowerShell](../articles/databox-online/azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
1. Utilisez la commande suivante pour activer un MPS sur votre appareil.

    ```powershell
    Start-HcsGpuMPS
    ```