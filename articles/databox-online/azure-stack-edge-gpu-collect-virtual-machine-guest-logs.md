---
title: Collecte des journaux invités des machines virtuelles sur un appareil GPU Azure Stack Edge Pro
description: Explique comment créer un package de support avec des journaux invités pour les machines virtuelles sur un appareil GPU Azure Stack Edge Pro.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/30/2021
ms.author: alkohli
ms.openlocfilehash: 1c25ea8c35b81169119b0f10025b36319d4dc2c9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532444"
---
# <a name="collect-vm-guest-logs-on-an-azure-stack-edge-pro-gpu-device"></a>Collecte des journaux invités des machines virtuelles sur un appareil GPU Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Pour diagnostiquer les échecs de provisionnement de machines virtuelles sur votre appareil GPU Azure Stack Edge Pro, vous devez examiner les journaux invités de la machine virtuelle en question. Cet article explique comment collecter les journaux invités des machines virtuelles dans un package de support.

> [!NOTE]
> Vous pouvez également examiner les journaux d’activité des machines virtuelles dans le portail Azure. Pour plus d’informations, consultez [Surveiller l’activité des machines virtuelles sur votre appareil](azure-stack-edge-gpu-monitor-virtual-machine-activity.md).


## <a name="collect-vm-guest-logs-in-support-package"></a>Collecte des journaux invités des machines virtuelles dans le package de support

Pour collecter les journaux invités des machines virtuelles en échec sur un appareil GPU Azure Stack Edge Pro, procédez comme suit :

1. [Connectez-vous à l’interface PowerShell de votre appareil](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

2. Collectez les journaux InGuestLogs des machines virtuelles en échec et incluez-les dans un package de support en exécutant les commandes suivantes :

   ```powershell
   Get-VMInGuestLogs -FailedVM
   Get-HcsNodeSupportPackage -Path “\\<network path>” -Include InGuestVMLogFiles -Credential “domain_name\user”
   ```

   Vous trouverez les journaux dans le dossier `hcslogs\VmGuestLogs`.

3. Pour voir les détails de l’historique de provisionnement des machines virtuelles, examinez les journaux suivants :

   **Machines virtuelles Linux :**
   - /var/log/cloud-init-output.log
   - /var/log/cloud-init.log
   - /var/log/waagent.log

   **Machines virtuelles Windows :**
   - C:\Windows\Azure\Panther\WaSetup.xml

## <a name="next-steps"></a>Étapes suivantes

- [Examiner le journal d’activité des machines virtuelles](azure-stack-edge-gpu-monitor-virtual-machine-activity.md).
- [Résoudre les problèmes de provisionnement des machines virtuelles sur un appareil GPU Azure Stack Edge Pro](azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning.md).