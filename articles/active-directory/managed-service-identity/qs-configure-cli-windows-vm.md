---
title: Guide pratique pour configurer des identités attribuées au système et à l’utilisateur sur une machine virtuelle Azure à l’aide d’Azure CLI
description: Instructions étape par étape pour configurer des identités attribuées au système et à l’utilisateur sur une machine virtuelle Azure à l’aide d’Azure CLI.
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
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: cb23db13d67047225102c6888e27e8f79a3e5abf
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2018
ms.locfileid: "39259311"
---
# <a name="configure-managed-service-identity-on-an-azure-vm-using-azure-cli"></a>Configurer Managed Service Identity sur une machine virtuelle Azure avec Azure CLI

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

L’identité du service administré fournit des services Azure avec une identité gérée automatiquement dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. 

Dans cet article, vous allez découvrir comment effectuer les opérations Managed Service Identity suivantes sur une machine virtuelle Azure à l’aide d’Azure CLI :
- Activer et désactiver l’identité attribuée au système sur une machine virtuelle Azure
- Ajouter et supprimer une identité attribuée à l’utilisateur sur une machine virtuelle Azure

## <a name="prerequisites"></a>Prérequis

- Si vous ne connaissez pas Managed Service Identity, consultez la [section Vue d’ensemble](overview.md). **Veillez à consulter [la différence entre les identité affectées par le système et celles affectées par l’utilisateur](overview.md#how-does-it-work)**.
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un essai gratuit](https://azure.microsoft.com/free/) avant de continuer.
- Pour effectuer les opérations de gestion dans cet article, votre compte doit disposer des attributions de rôles suivants :
    - [Contributeur de machines virtuelles](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) pour créer une machine virtuelle puis activer et supprimer l’identité managée affectée par le système et/ou l’utilisateur sur une machine virtuelle Azure.
    - [Contributeur d’identité managée](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) pour créer une identité affectée par l’utilisateur.
    - [Opérateur d’identité managée](/azure/role-based-access-control/built-in-roles#managed-identity-operator) pour attribuer et supprimer une identité affectée par l’utilisateur depuis et vers une machine virtuelle.
- Pour exécuter les exemples de script d’Azure CLI, vous disposez de trois options :
    - Utilisez [Azure Cloud Shell](../../cloud-shell/overview.md) à partir du portail Azure (voir section suivante).
    - Utilisez l’interface intégrée Azure Cloud Shell via le bouton « Essayer », situé dans le coin supérieur droit de chaque bloc de code.
    - [Installez la dernière version de CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 ou version ultérieure) si vous préférez utiliser une console CLI locale. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>Identité attribuée au système

Dans cette section, vous allez découvrir comment activer et désactiver l’identité attribuée au système sur une machine virtuelle Azure à l’aide d’Azure CLI.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>Activer une identité attribuée au système lors de la création d’une machine virtuelle Azure

Pour créer une machine virtuelle Azure avec l’identité attribuée au système activée

1. Si vous utilisez l’interface de ligne de commande Azure dans une console locale, commencez par vous connecter à Azure avec [az login](/cli/azure/reference-index#az_login). Utilisez un compte associé à l’abonnement Azure sur lequel vous souhaitez déployer la machine virtuelle :

   ```azurecli-interactive
   az login
   ```

2. Créez un [groupe de ressources](../../azure-resource-manager/resource-group-overview.md#terminology) pour l’imbrication et le déploiement de votre machine virtuelle et de ses ressources connexes, à l’aide de la commande [az group create](/cli/azure/group/#az_group_create). Vous pouvez ignorer cette étape si vous possédez déjà le groupe de ressources que vous souhaitez utiliser à la place :

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Créez une machine virtuelle à l’aide de la commande [az vm create](/cli/azure/vm/#az_vm_create). L’exemple suivant crée une machine virtuelle nommée *myVM* avec une identité attribuée au système, comme le demande le paramètre `--assign-identity`. Les paramètres `--admin-username` et `--admin-password` spécifient le nom d’utilisateur et le mot de passe d’administration du compte pour la connexion à la machine virtuelle. Mettez à jour ces valeurs en fonction de votre environnement : 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Activer une identité attribuée au système sur une machine virtuelle Azure existante

Si vous devez activer l’identité attribuée au système sur une machine virtuelle existante :

1. Si vous utilisez l’interface de ligne de commande Azure dans une console locale, commencez par vous connecter à Azure avec [az login](/cli/azure/reference-index#az_login). Utilisez un compte associé à l’abonnement Azure qui contient la machine virtuelle.

   ```azurecli-interactive
   az login
   ```

2. Utilisez [az vm identity assign](/cli/azure/vm/identity/#az_vm_identity_assign) avec la commande `identity assign` pour activer l’identité attribuée au système sur une machine virtuelle existante :

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-vm"></a>Désactiver l’identité attribuée au système à partir d’une machine virtuelle Azure

Si vous disposez d’une machine virtuelle dont l’identité affectée par le système est devenu inutile, mais qui a toujours besoin d’identités attribuées par l’utilisateur, utilisez la commande suivante :

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Si vous disposez d’une machine virtuelle dont l’identité affectée par le système est devenu inutile, et qui n’a pas d’identité attribuée par l’utilisateur, utilisez la commande suivante :

> [!NOTE]
> La valeur `none` respecte la casse. Elle doit être en minuscules. 

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none"
```

Pour supprimer l’extension de machine virtuelle Managed Service Identity, utilisez le commutateur `-n ManagedIdentityExtensionForWindows` ou `-n ManagedIdentityExtensionForLinux` (en fonction du type de machine virtuelle) avec [az vm extension delete](https://docs.microsoft.com/cli/azure/vm/#assign-identity) :

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

## <a name="user-assigned-identity"></a>Identité attribuée à l’utilisateur

Dans cette section, vous découvrez comment ajouter et supprimer une identité attribuée par l’utilisateur, sur une machine virtuelle Azure à l’aide d’Azure CLI.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-an-azure-vm"></a>Attribuer une identité attribuée à l’utilisateur lors de la création d’une machine virtuelle Azure

Cette section explique en détail comment créer une machine virtuelle avec attribution d’une identité attribuée à l’utilisateur. Si vous disposez déjà d’une machine virtuelle que vous souhaitez utiliser, ignorez cette section et passez à la suivante.

1. Vous pouvez ignorer cette étape si vous disposez déjà d’un groupe de ressources que vous souhaitez utiliser. Créez un [groupe de ressources](~/articles/azure-resource-manager/resource-group-overview.md#terminology) pour contenir et déployer votre identité de service managée en exécutant la commande [az group create](/cli/azure/group/#az_group_create). N’oubliez pas de remplacer les valeurs des paramètres `<RESOURCE GROUP>` et `<LOCATION>` par vos propres valeurs. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Créez une identité attribuée à l’utilisateur avec la commande [az identity create](/cli/azure/identity#az_identity_create).  Le paramètre `-g` spécifie le groupe de ressources où l’identité attribuée à l’utilisateur est créée, et le paramètre `-n` spécifie son nom.    
    
   [!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g myResourceGroup -n myUserAssignedIdentity
   ```
   La réponse contient les détails de l’identité attribuée à l’utilisateur qui a été créée, comme dans l’exemple suivant. La valeur d’ID de ressource attribuée à l’identité attribuée à l’utilisateur est utilisée à l’étape suivante.

   ```json
   {
       "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
       "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
       "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
       "location": "westcentralus",
       "name": "<MSI NAME>",
       "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
       "resourceGroup": "<RESOURCE GROUP>",
       "tags": {},
       "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
       "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. Créez une machine virtuelle à l’aide de la commande [az vm create](/cli/azure/vm/#az_vm_create). L’exemple suivant crée une machine virtuelle associée à la nouvelle identité attribuée à l’utilisateur, tel que spécifié par le paramètre `--assign-identity`. N’oubliez pas de remplacer les valeurs des paramètres `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` et `<MSI ID>` par vos propres valeurs. Pour `<MSI ID>`, utilisez la propriété `id` de la ressource de l’identité attribuée à l’utilisateur créée à l’étape précédente : 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <MSI ID>
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-vm"></a>Attribuer une identité attribuée à l’utilisateur à une machine virtuelle Azure existante

1. Créez une identité attribuée à l’utilisateur avec la commande [az identity create](/cli/azure/identity#az-identity-create).  Le paramètre `-g` spécifie le groupe de ressources où l’identité attribuée à l’utilisateur est créée, et le paramètre `-n` spécifie son nom. N’oubliez pas de remplacer les valeurs des paramètres `<RESOURCE GROUP>` et `<MSI NAME>` par vos propres valeurs :

    > [!IMPORTANT]
    > La création d’identités attribuées à l’utilisateur avec des caractères spéciaux (tels qu’un trait de soulignement) dans le nom n’est pas prise en charge actuellement. Utilisez des caractères alphanumériques. Revenez ultérieurement pour des mises à jour.  Pour plus d’informations, consultez [FAQ et problèmes connus](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
La réponse contient les détails de l’identité managée attribuée par l’utilisateur qui a été créée, comme dans l’exemple suivant. La valeur d’`id` de ressource attribuée à l’identité attribuée à l’utilisateur est utilisée à l’étape suivante.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
        "location": "westcentralus",
        "name": "<MSI NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. Attribuez l’identité attribuée à l’utilisateur à votre machine virtuelle à l’aide de la commande [az vm identity assign](/cli/azure/vm#az-vm-identity-assign). N’oubliez pas de remplacer les valeurs des paramètres `<RESOURCE GROUP>` et `<VM NAME>` par vos propres valeurs. `<MSI ID>` sera la propriété `id` de ressource de l’identité attribuée à l’utilisateur, tel que créée à l’étape précédente :

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI ID>
    ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vm"></a>Supprimer une identité attribuée à l’utilisateur d’une machine virtuelle Azure

Pour supprimer d’une machine virtuelle une identité affectée par l’utilisateur, utilisez [az vm identity remove](/cli/azure/vm#az-vm-identity-remove). N’oubliez pas de remplacer les valeurs des paramètres `<RESOURCE GROUP>` et `<VM NAME>` par vos propres valeurs. `<MSI NAME>` sera la propriété `name` de l’identité attribuée à l’utilisateur, qui est accessible dans la section d’identité de la machine virtuelle en exécutant la commande `az vm identity show` :

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI NAME>
```

Si votre machine virtuelle ne dispose pas d’identité affectée par le système, et que vous souhaitez supprimer toutes les identités affectées par l’utilisateur qu’elle contient, utilisez la commande suivante :

> [!NOTE]
> La valeur `none` est sensible à la casse. Elle doit être en minuscules.

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none" identity.identityIds=null
```

Si votre machine virtuelle dispose à la fois d’identités attribuées à l’utilisateur et d’identités attribuées au système, vous pouvez supprimer toutes les identités attribuées à l’utilisateur en choisissant de n’utiliser que des identités attribuées au système. Utilisez la commande suivante :

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.identityIds=null 
```

## <a name="related-content"></a>Contenu connexe
- [Vue d’ensemble de l’identité du service administré](overview.md)
- Pour obtenir les guides de démarrages rapides complets sur la création de machines virtuelles Azure, consultez : 
  - [Créer une machine virtuelle Windows avec l’interface Azure CLI](../../virtual-machines/windows/quick-create-cli.md)  
  - [Créer une machine virtuelle Linux avec Azure CLI](../../virtual-machines/linux/quick-create-cli.md) 

















