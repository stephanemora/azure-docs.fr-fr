---
title: Collecte des journaux invités des machines virtuelles sur un appareil GPU Azure Stack Edge Pro
description: Explique comment créer un package de support avec des journaux invités pour les machines virtuelles sur un appareil GPU Azure Stack Edge Pro.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/03/2021
ms.author: alkohli
ms.openlocfilehash: 7ffb96cdc3fbb561009b7f545a3b9a1da9eef729
ms.sourcegitcommit: ef950cf37f65ea7a0f583e246cfbf13f1913eb12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111421001"
---
# <a name="collect-vm-guest-logs-on-an-azure-stack-edge-pro-gpu-device"></a>Collecte des journaux invités des machines virtuelles sur un appareil GPU Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Pour diagnostiquer les échecs de provisionnement de machines virtuelles sur votre appareil GPU Azure Stack Edge Pro, vous devez examiner les journaux invités de la machine virtuelle en question. Cet article explique comment collecter les journaux invités des machines virtuelles dans un package de support.

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

- [Résolution des problèmes de provisionnement des machines virtuelles sur un appareil GPU Azure Stack Edge Pro](azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning.md)