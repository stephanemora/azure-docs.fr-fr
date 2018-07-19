---
title: Guide pratique pour configurer des identités affectées par le système et l’utilisateur sur un groupe de machines virtuelles identiques Azure à l’aide de REST
description: Cet article contient des instructions pas à pas pour la configuration d’identités affectées par le système et l’utilisateur sur un groupe de machines virtuelles identiques Azure à l’aide de CURL pour effectuer des appels d’API REST.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/25/2018
ms.author: daveba
ms.openlocfilehash: a31f36c144cc449790a4c62e8e4e5efcd4cd325d
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901073"
---
# <a name="configure-managed-identity-on-a-virtual-machine-scale-set-using-rest-api-calls"></a>Configurer une identité managée sur un groupe de machines virtuelles identiques à l’aide d’appels d’API REST

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

L’identité managée fournit aux services Azure une identité système managée automatiquement dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. 

Dans cet article, découvrez comment effectuer les opérations suivantes sur des identités managées sur un groupe de machines virtuelles identiques, en utilisant CURL pour effectuer des appels vers le point de terminaison REST Azure Resource Manager :

- Activer et désactiver une identité affectée par le système sur un groupe de machines virtuelles identiques Azure
- Ajouter et supprimer une identité affectée par l’utilisateur sur un groupe de machines virtuelles identiques Azure

## <a name="prerequisites"></a>Prérequis

- Si vous ne connaissez pas MSI, consultez la [section Vue d’ensemble](overview.md). **Veillez à lire [la différence entre les identités attribuées au système et celles attribuées à l’utilisateur](overview.md#how-does-it-work)**.
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un essai gratuit](https://azure.microsoft.com/free/) avant de continuer.
- Si vous utilisez Windows, installez le [sous-système Windows pour Linux](https://msdn.microsoft.com/commandline/wsl/about) ou utilisez [Azure Cloud Shell](../../cloud-shell/overview.md) dans le portail Azure.
- [Installez la console locale Azure CLI](/azure/install-azure-cli), si vous utilisez le [sous-système Windows pour Linux](https://msdn.microsoft.com/commandline/wsl/about) ou un [système d’exploitation de distribution Linux](/cli/azure/install-azure-cli-apt?view=azure-cli-latest).
- Si vous utilisez la console locale Azure CLI, connectez-vous à Azure à l’aide de la commande `az login` avec un compte associé à l’abonnement Azure sous lequel vous souhaitez gérer les identités affectées par le système ou l’utilisateur.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>Identité attribuée au système

Dans cette section, découvrez comment activer et désactiver une identité affectée par le système sur un groupe de machines virtuelles identiques, en utilisant CURL pour effectuer des appels vers le point de terminaison REST Azure Resource Manager.

### <a name="enable-system-assigned-identity-during-creation-of-a-virtual-machine-scale-set"></a>Activer une identité affectée par le système lors de la création d’un groupe de machines virtuelles identiques

Pour créer un groupe de machines virtuelles identiques dans lequel une identité affectée par le système est activée, vous devez créer un groupe de machines virtuelles identiques et récupérer un jeton d’accès pour utiliser CURL afin d’appeler le point de terminaison Resource Manager avec la valeur de type d’identité affectée par le système.

1. Créez un [groupe de ressources](../../azure-resource-manager/resource-group-overview.md#terminology) pour l’imbrication et le déploiement de votre groupe de machines virtuelles identiques et de ses ressources connexes, à l’aide de la commande [az group create](/cli/azure/group/#az_group_create). Vous pouvez ignorer cette étape si vous possédez déjà le groupe de ressources que vous souhaitez utiliser à la place :

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Créez une [interface réseau](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) pour votre groupe de machines virtuelles identiques :

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Récupérez un jeton d’accès de porteur, que vous allez utiliser à l’étape suivante dans l’en-tête d’autorisation pour créer votre groupe de machines virtuelles identiques avec une identité managée affectée par le système.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Créez un groupe de machines virtuelles identiques à l’aide de CURL pour appeler le point de terminaison REST Azure Resource Manager. L’exemple suivant crée un groupe de machines virtuelles identiques nommé *myVMSS* dans *myResourceGroup* avec une identité affectée par le système, comme identifié dans le corps de demande par la valeur `"identity":{"type":"SystemAssigned"}`. Remplacez `<ACCESS TOKEN>` par la valeur que vous avez reçue à l’étape précédente lorsque vous avez demandé un jeton d’accès du porteur et la valeur `<SUBSCRIPTION ID>` adaptée à votre environnement.
 
    ```bash   
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"SystemAssigned"},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
    ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Activer une identité attribuée au système sur un groupe de machines virtuelles identiques Azure existant

Pour activer l’identité affectée par le système sur un groupe de machines virtuelles identiques existant, obtenez un jeton d’accès, puis utilisez CURL pour appeler le point de terminaison REST Resource Manager afin de mettre à jour le type d’identité.

1. Récupérez un jeton d’accès de porteur, que vous allez utiliser à l’étape suivante dans l’en-tête d’autorisation pour créer votre groupe de machines virtuelles identiques avec une identité managée affectée par le système.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Utilisez la commande CURL suivante pour appeler le point de terminaison REST Azure Resource Manager afin d’activer l’identité affectée par le système sur votre groupe de machines virtuelles identiques, comme identifié dans le corps de la demande par la valeur `{"identity":{"type":"SystemAssigned"}` pour un groupe de machines virtuelles identiques nommé *myVMSS*.  Remplacez `<ACCESS TOKEN>` par la valeur que vous avez reçue à l’étape précédente lorsque vous avez demandé un jeton d’accès du porteur et la valeur `<SUBSCRIPTION ID>` adaptée à votre environnement.
   
   > [!IMPORTANT]
   > Pour éviter de supprimer des identités managées affectées par l’utilisateur existantes qui sont attribuées au groupe de machines virtuelles identiques, répertoriez les identités affectées par l’utilisateur à l’aide de la commande CURL suivante : `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Si vous avez attribué des identités affectées par l’utilisateur au groupe de machines virtuelles identiques, comme identifié dans la valeur `identity` de la réponse, passez à l’étape 3, qui montre comment conserver les identités affectées par l’utilisateur, tout en activant l’identité affectée par le système sur votre groupe de machines virtuelles identiques.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Pour activer une identité affectée par le système sur un groupe de machines virtuelles identiques avec des identités affectées par l’utilisateur existantes, vous devez ajouter `SystemAssigned` à la valeur `type`.  
   
   Par exemple, si votre groupe de machines virtuelles identiques a des identités affectées par l’utilisateur `ID1` et `ID2` qui lui sont affectées, et que vous souhaitez ajouter l’identité affectée par le système au groupe de machines virtuelles identiques, utilisez l’appel CURL suivant. Remplacez `<ACCESS TOKEN>` et `<SUBSCRIPTION ID>` par les valeurs adaptées à votre environnement.
   
   ```bash
   curl -v 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="disable-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Désactiver une identité attribuée au système à partir d’un groupe de machines virtuelles identiques Azure

Pour désactiver une identité affectée par le système sur un groupe de machines virtuelles identiques existant, obtenez un jeton d’accès, puis utilisez CURL pour appeler le point de terminaison REST Resource Manager afin de mettre à jour le type d’identité en le définissant sur `None`.

1. Récupérez un jeton d’accès de porteur, que vous allez utiliser à l’étape suivante dans l’en-tête d’autorisation pour créer votre groupe de machines virtuelles identiques avec une identité managée affectée par le système.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Mettez à jour le groupe de machines virtuelles identiques à l’aide de CURL pour appeler le point de terminaison REST Azure Resource Manager afin de désactiver l’identité affectée par le système.  L’exemple suivant désactive l’identité affectée par le système, comme identifié dans le corps de la demande par la valeur `{"identity":{"type":"None"}}` d’un groupe de machines virtuelles identiques nommé *myVMSS*.  Remplacez `<ACCESS TOKEN>` par la valeur que vous avez reçue à l’étape précédente lorsque vous avez demandé un jeton d’accès du porteur et la valeur `<SUBSCRIPTION ID>` adaptée à votre environnement.

   > [!IMPORTANT]
   > Pour éviter de supprimer des identités managées affectées par l’utilisateur existantes qui sont attribuées au groupe de machines virtuelles identiques, répertoriez les identités affectées par l’utilisateur à l’aide de la commande CURL suivante : `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Si vous avez attribué des identités affectées par l’utilisateur au groupe de machines virtuelles identiques, passez à l’étape 3, qui montre comment conserver les identités affectées par l’utilisateur, tout en supprimant l’identité affectée par le système de votre groupe de machines virtuelles identiques.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Pour supprimer l’identité affectée par le système d’un groupe de machines virtuelles identiques ayant des identités affectées par l’utilisateur, supprimez `SystemAssigned` de la valeur `{"identity":{"type:" "}}`, tout en conservant la valeur `UserAssigned` et le tableau `identityIds` qui définit quelles identités affectées par l’utilisateur sont affectées au groupe de machines virtuelles identiques.

## <a name="user-assigned-identity"></a>Identité attribuée par l’utilisateur

Dans cette section, découvrez comment ajouter et supprimer une identité affectée par un utilisateur dans un groupe de machines virtuelles identiques, à l’aide de CURL pour effectuer des appels vers le point de terminaison REST Azure Resource Manager.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Attribuer une identité affectée par l’utilisateur lors de la création d’un groupe de machines virtuelles identiques

1. Récupérez un jeton d’accès de porteur, que vous allez utiliser à l’étape suivante dans l’en-tête d’autorisation pour créer votre groupe de machines virtuelles identiques avec une identité managée affectée par le système.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Créez une [interface réseau](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) pour votre groupe de machines virtuelles identiques :

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Récupérez un jeton d’accès de porteur, que vous allez utiliser à l’étape suivante dans l’en-tête d’autorisation pour créer votre groupe de machines virtuelles identiques avec une identité managée affectée par le système.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Créez une identité affectée par l’utilisateur à l’aide des instructions disponibles ici : [Créer une identité managée affectée par l’utilisateur](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Créez un groupe de machines virtuelles identiques à l’aide de CURL pour appeler le point de terminaison REST Azure Resource Manager. L’exemple suivant crée un groupe de machines virtuelles identiques nommé *myVMSS* dans le groupe de ressources *myResourceGroup* avec une identité affectée par un utilisateur `ID1`, comme identifié dans le corps de demande par la valeur `"identity":{"type":"UserAssigned"}`. Remplacez `<ACCESS TOKEN>` par la valeur que vous avez reçue à l’étape précédente lorsque vous avez demandé un jeton d’accès du porteur et la valeur `<SUBSCRIPTION ID>` adaptée à votre environnement.
 
   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus",{"identity":{"type":"UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Attribuer une identité affectée par le système sur un groupe de machines virtuelles identiques Azure existant

1. Récupérez un jeton d’accès de porteur, que vous allez utiliser à l’étape suivante dans l’en-tête d’autorisation pour créer votre groupe de machines virtuelles identiques avec une identité managée affectée par le système.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Créez une identité affectée par l’utilisateur à l’aide des instructions disponibles ici : [Créer une identité managée affectée par l’utilisateur](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. Pour éviter de supprimer des identités managées affectées par l’utilisateur ou le système existantes qui sont attribuées au groupe de machines virtuelles identiques, vous devez répertorier les types d’identité affectés au groupe de machines virtuelles identiques à l’aide de la commande CURL suivante. Si vous avez affecté des identités managées au groupe de machines virtuelles identiques, celles-ci sont répertoriées sous la valeur `identity`.
 
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

4. Si vous n’avez attribué aucune identité affectée par le système ou l’utilisateur à votre groupe de machines virtuelles identiques, utilisez la commande CURL suivante pour appeler le point de terminaison REST Azure Resource Manager afin d’affecter la première identité affectée par l’utilisateur au groupe de machines virtuelles identiques.  Si vous avez attribué des identités affectées par l’utilisateur ou le système au groupe de machines virtuelles identiques, passez à l’étape 5, qui montre comment ajouter plusieurs identités affectées par l’utilisateur à un groupe de machines virtuelles identiques, tout en mettant également à jour l’identité affectée par le système.

   L’exemple suivant attribue l’identité affectée par l’utilisateur `ID1` à un groupe de machines virtuelles identiques nommé *myVMSS* dans le groupe de ressources *myResourceGroup*.  Remplacez `<ACCESS TOKEN>` par la valeur que vous avez reçue à l’étape précédente lorsque vous avez demandé un jeton d’accès du porteur et la valeur `<SUBSCRIPTION ID>` adaptée à votre environnement.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

5. Si vous avez attribué des identités affectées par l’utilisateur ou le système à votre groupe de machines virtuelles identiques, vous devez ajouter la nouvelle identité affectée par l’utilisateur au tableau `identityIDs`, tout en conservant les identités affectées par le système et l’utilisateur qui sont actuellement attribuées au groupe de machines virtuelles identiques.

   Par exemple, si une identité affectée par le système et l’identité affectée par l’utilisateur `ID1` sont actuellement attribuées à votre groupe de machines virtuelles identiques, et que vous souhaitez ajouter l’identité affectée par l’utilisateur `ID2` à celui-ci, utilisez la commande CURL suivante. Remplacez `<ACCESS TOKEN>` par la valeur que vous avez reçue à l’étape précédente lorsque vous avez demandé un jeton d’accès du porteur et la valeur `<SUBSCRIPTION ID>` adaptée à votre environnement.

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="remove-a-user-assigned-identity-from-a-virtual-machine-scale-set"></a>Supprimer une identité affectée par l’utilisateur d’un groupe de machines virtuelles identiques

1. Récupérez un jeton d’accès de porteur, que vous allez utiliser à l’étape suivante dans l’en-tête d’autorisation pour créer votre groupe de machines virtuelles identiques avec une identité managée affectée par le système.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Pour éviter de supprimer des identités managées affectées par l’utilisateur existantes qui sont attribuées au groupe de machines virtuelles identiques ou de supprimer l’identité affectée par le système, vous devez répertorier les identités managées à l’aide de la commande CURL suivante : 
   
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```
   
   Si vous avez affecté des identités managées à la machine virtuelle, celles-ci sont répertoriées dans la réponse sous la valeur `identity`. 
    
   Par exemple, si vous avez les identités `ID1` et `ID2` affectées par l’utilisateur qui ont été attribuées à votre groupe de machines virtuelles identiques et que vous voulez uniquement conserver l’identité `ID1` affectée par l’utilisateur et conserver l’identité affectée par le système, vous devez utiliser la même commande CURL que lorsque vous affectez une identité managée affectée par l’utilisateur à un groupe de machines virtuelles identiques en conservant uniquement la valeur `ID1` et conserver la valeur `SystemAssigned`. Cette opération supprime l’identité affectée par l’utilisateur `ID2` du groupe de machines virtuelles identiques, tout en conservant l’identité affectée par le système.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

Si votre groupe de machines virtuelles identiques dispose à la fois d’identités affectées par l’utilisateur et d’identités affectées par le système, vous pouvez supprimer toutes les identités affectées par l’utilisateur en choisissant de n’utiliser que des identités affectées par le système à l’aide de la commande suivante :

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```
    
Si votre groupe de machines virtuelles identiques a uniquement des identités affectées par l’utilisateur et que vous souhaitez les supprimer, utilisez la commande suivante :

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la façon de créer, de lister ou de supprimer des identités affectées par l’utilisateur à l’aide de REST, consultez :

- [Créer, lister ou supprimer une identité affectée par l’utilisateur à l’aide d’appels d’API REST](how-to-manage-ua-identity-rest.md)