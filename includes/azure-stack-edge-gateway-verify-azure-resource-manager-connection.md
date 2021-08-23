---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/30/2021
ms.author: alkohli
ms.openlocfilehash: e0770eb27bfebc085d99af9fdc9749676b699b70
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113106577"
---
Assurez-vous que les étapes suivantes peuvent être utilisées pour accéder à l’appareil à partir de votre client.

Vérifiez que votre client peut se connecter à l’instance Azure Resource Manager locale. 

1. Appelez les API de l’appareil local pour l’authentification :

    ### <a name="az"></a>[Az](#tab/az)

    ```powershell
    login-AzAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d  
    ```

    ### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

    ```powershell
    login-AzureRMAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d  
    ```

1. Indiquez le nom d’utilisateur `EdgeArmUser` et le mot de passe pour vous connecter via Azure Resource Manager. Si vous avez oublié le mot de passe, consultez [Réinitialiser le mot de passe pour Azure Resource Manager](../articles/databox-online/azure-stack-edge-gpu-set-azure-resource-manager-password.md) et utilisez le nouveau mot de passe pour vous connecter.