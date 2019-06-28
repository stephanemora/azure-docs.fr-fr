---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: aebb82690a7a49aba071ed64349d37d516208cca
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177524"
---
Pour réinitialiser votre appareil, vous devez effacer en toute sécurité toutes les données sur le disque de données et le disque de démarrage de votre appareil. 

Utilisez la cmdlet `Reset-HcsAppliance` pour effacer les disques de données et le disque de démarrage ou simplement les disques de données. Les commutateurs `ClearData` et `BootDisk` permettent respectivement de réinitialiser les disques de données et le disque de démarrage.

Si vous utilisez la réinitialisation d’appareil dans l’interface utilisateur web locale, seuls les disques de données sont effacés en toute sécurité, mais le disque de démarrage reste intact. Le disque de démarrage contient la configuration de l’appareil.

1. [Connectez-vous à l’interface PowerShell](#connect-to-the-powershell-interface).
2. À l’invite de commandes, tapez :

    `Reset-HcsAppliance -ClearData -BootDisk`

    L’exemple suivant explique comment utiliser cette cmdlet :
    ```
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk
    
    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
