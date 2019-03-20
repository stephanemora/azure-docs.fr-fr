---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: aebb82690a7a49aba071ed64349d37d516208cca
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57556761"
---
Pour réinitialiser votre appareil, vous devez effacer en toute sécurité toutes les données sur le disque de données et le disque de démarrage de votre appareil. 

Utilisez le `Reset-HcsAppliance` effacent les disques de données et le disque de démarrage ou simplement les disques de données de l’applet de commande. Le `ClearData` et `BootDisk` commutateurs permettent de réinitialiser les disques de données et le disque de démarrage respectivement.

Si vous utilisez le périphérique dans l’interface utilisateur web locale de réinitialisation, seuls les disques de données sont effacés en toute sécurité, mais le disque de démarrage reste intact. Le disque de démarrage contient la configuration de l’appareil.

1. [Se connecter à l’interface PowerShell](#connect-to-the-powershell-interface).
2.  À l’invite de commandes, tapez :

    `Reset-HcsAppliance -ClearData -BootDisk`

    L’exemple suivant montre comment utiliser cette applet de commande :
    ```
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk
    
    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
