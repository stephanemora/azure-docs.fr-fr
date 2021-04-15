---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/09/2021
ms.author: alkohli
ms.openlocfilehash: 1298673c475a0308f1db27641aae93837e6b5df3
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305552"
---
Assurez-vous que les étapes suivantes peuvent être utilisées pour accéder à l’appareil à partir de votre client.

Vérifiez que votre client peut se connecter à l’instance Azure Resource Manager locale. 

1. Appelez les API de l’appareil local pour l’authentification :

    ```powershell
    login-AzureRMAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d  
    ```

1. Indiquez le nom d’utilisateur `EdgeArmUser` et le mot de passe pour vous connecter via Azure Resource Manager. Si vous avez oublié le mot de passe, consultez [Réinitialiser le mot de passe pour Azure Resource Manager](../articles/databox-online/azure-stack-edge-gpu-set-azure-resource-manager-password.md) et utilisez le nouveau mot de passe pour vous connecter.