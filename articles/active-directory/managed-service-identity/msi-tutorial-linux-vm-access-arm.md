---
title: Utiliser une identité MSI attribuée à l’utilisateur sur une machine virtuelle Linux pour accéder à Azure Resource Manager
description: Ce tutoriel vous guide tout au long de l’utilisation d’une identité MSI (Managed Service Identity) attribuée à l’utilisateur sur une machine virtuelle Linux, pour accéder à Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: arluca
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 83203737706b859bfbdfc1b356bbaca6d0b65dc0
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33931395"
---
# <a name="use-a-user-assigned-identity-on-a-linux-vm-to-access-azure-resource-manager"></a>Utiliser une identité attribuée à l’utilisateur sur une machine virtuelle Linux pour accéder à Azure Resource Manager

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Ce tutoriel explique comment créer une identité attribuée à l’utilisateur, comment l’attribuer à une machine virtuelle Linux, puis l’utiliser pour accéder à l’API d’Azure Resource Manager. Les identités MSI sont gérées automatiquement par Azure. Elles permettent l’authentification auprès de services prenant en charge Azure AD authentication, sans devoir nécessairement incorporer des informations d’identification à votre code. 

Les identités MSI sont gérées automatiquement par Azure. Elles permettent l’authentification auprès de services prenant en charge Azure AD authentication, sans devoir nécessairement incorporer des informations d’identification à votre code.

Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer une identité attribuée à l’utilisateur
> * Attribuer l’identité attribuée à l’utilisateur à une machine virtuelle Linux 
> * Accorder à l’identité attribuée à l’utilisateur l’accès à un groupe de ressources dans Azure Resource Manager 
> * Obtenir un jeton d’accès par l’identité attribuée à l’utilisateur et l’utiliser pour appeler Azure Resource Manager 

## <a name="prerequisites"></a>Prérequis


- Si vous ne connaissez pas Managed Service Identity, consultez la [section Vue d’ensemble](overview.md). **Veillez à consulter [la différence entre les identités attribuées au système et celles attribuées à l’utilisateur](overview.md#how-does-it-work)**.
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un essai gratuit](https://azure.microsoft.com/free/) avant de continuer.
- Pour effectuer les étapes de création de ressources et de gestion de rôles nécessaires dans ce tutoriel, votre compte doit bénéficier des autorisations « Propriétaire » avec l’étendue appropriée (votre abonnement ou groupe de ressources). Si vous avez besoin d’aide concernant l’attribution de rôle, consultez [Utiliser le contrôle d’accès en fonction du rôle pour gérer l’accès aux ressources d’un abonnement Azure](/azure/role-based-access-control/role-assignments-portal).

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0.4 ou une version ultérieure pour poursuivre la procédure décrite dans ce guide de démarrage rapide. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Créer une machine virtuelle Linux dans un nouveau groupe de ressources

Pour ce didacticiel, vous commencez par créer une machine virtuelle Linux. Vous pouvez également choisir d’utiliser une machine virtuelle existante.

1. Cliquez sur **Créer une ressource** en haut à gauche du portail Azure.
2. Sélectionnez **Compute**, puis sélectionnez **Ubuntu Server 16.04 LTS**.
3. Saisissez les informations de la machine virtuelle. Dans **Type d’authentification**, sélectionnez **Clé publique SSH** ou **Mot de passe**. Les informations d’identification créées vous permettent de vous connecter à la machine virtuelle.

    ![Créer une machine virtuelle Linux](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Choisissez un **Abonnement** pour la machine virtuelle dans la liste déroulante.
5. Pour sélectionner un nouveau **Groupe de ressources** dans lequel vous souhaitez créer la machine virtuelle, choisissez **Créer nouveau**. Lorsque vous avez terminé, cliquez sur **OK**.
6. Choisissez la taille de la machine virtuelle. Pour voir plus de tailles, sélectionnez **Afficher tout** ou modifiez le filtre de type de disque pris en charge. Conservez les valeurs par défaut dans le panneau des paramètres et cliquez sur **OK**.

## <a name="create-a-user-assigned-identity"></a>Créer une identité attribuée à l’utilisateur

1. Si vous utilisez la console CLI (au lieu d’une session Azure Cloud Shell), commencez par vous connecter à Azure. Utilisez un compte associé à l’abonnement Azure sur lequel vous souhaitez créer l’identité attribuée à l’utilisateur :

    ```azurecli
    az login
    ```

2. Créez une identité attribuée à l’utilisateur avec la commande [az identity create](/cli/azure/identity#az_identity_create). Le paramètre `-g` spécifie le groupe de ressources où l’identité MSI est créée, tandis que le paramètre `-n` indique son nom. N’oubliez pas de remplacer les valeurs des paramètres `<RESOURCE GROUP>` et `<MSI NAME>` par vos propres valeurs :
    
    > [!IMPORTANT]
    > La création d’identités attribuées à l’utilisateur ne prend en charge que les caractères alphanumériques et le trait d’union (0-9 ou a-z ou A-Z ou -). En outre, le nom doit être limité à 24 caractères pour que l’attribution à la machine virtuelle/au groupe de machines virtuelles identiques fonctionne correctement. Revenez ultérieurement pour des mises à jour. Pour plus d’informations, consultez [FAQ et problèmes connus](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    La réponse contient les détails de l’identité attribuée à l’utilisateur qui a été créée, comme dans l’exemple suivant. Notez la valeur `id` pour votre identité attribuée à l’utilisateur, car elle est utilisée à l’étape suivante :

    ```json
    {
    "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=12344643-8088-4d70-9532-c3a0fdc190fz",
    "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
    "location": "westcentralus",
    "name": "<MSI NAME>",
    "principalId": "9012",
    "resourceGroup": "<RESOURCE GROUP>",
    "tags": {},
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
    }
    ```

## <a name="assign-a-user-assigned-identity-to-your-linux-vm"></a>Attribuer une identité attribuée à l’utilisateur à votre machine virtuelle Linux

Une identité attribuée à l’utilisateur peut être utilisée par les clients sur plusieurs ressources Azure. Utilisez les commandes suivantes pour attribuer l’identité attribuée à l’utilisateur à une seule machine virtuelle. Utilisez la propriété `Id` retournée à l’étape précédente pour le paramètre `-IdentityID`.

Attribuez l’identité MSI affectée à l’utilisateur à votre machine virtuelle Linux à l’aide de la commande [az vm assign-identity](/cli/azure/vm#az_vm_assign_identity). N’oubliez pas de remplacer les valeurs des paramètres `<RESOURCE GROUP>` et `<VM NAME>` par vos propres valeurs. Utilisez la propriété `id` retournée à l’étape précédente comme valeur du paramètre `--identities`.

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="grant-your-user-assigned-identity-access-to-a-resource-group-in-azure-resource-manager"></a>Accorder à votre identité attribuée à l’utilisateur l’accès à un groupe de ressources dans Azure Resource Manager 

Managed Service Identity (MSI) fournit des identités que votre code peut utiliser pour demander des jetons d’accès afin de s’authentifier auprès d’API de ressources qui prennent en charge l’authentification Azure AD. Dans ce tutoriel, votre code accède à l’API Azure Resource Manager.  

Avant que ce code puisse accéder à l’API, vous devez accorder à l’identité l’accès à une ressource dans Azure Resource Manager. Dans le cas présent, le groupe de ressources dans lequel la machine virtuelle est contenue. Mettez à jour la valeur de `<SUBSCRIPTION ID>` et `<RESOURCE GROUP>` en fonction de votre environnement. En outre, remplacez `<MSI PRINCIPALID>` par la propriété `principalId` retournée par la commande `az identity create` dans [Créer une identité MSI affectée à l’utilisateur](#create-a-user-assigned-msi) :

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP> "
```

La réponse contient les détails de l’affectation de rôle qui a été créée, comme dans l’exemple suivant :

```json
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Authorization/roleAssignments/b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "name": "b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "properties": {
    "principalId": "f5fdfdc1-ed84-4d48-8551-999fb9dedfbl",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}

```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Obtenir un jeton d’accès à l’aide de l’identité de la machine virtuelle et l’utiliser pour appeler Gestionnaire des ressources 

Pour la suite de ce didacticiel, nous allons utiliser la machine virtuelle que nous avons créée précédemment.

Pour effectuer cette procédure, vous avez besoin d’un client SSH. Si vous utilisez Windows, vous pouvez utiliser le client SSH dans le [Sous-système Windows pour Linux](https://msdn.microsoft.com/commandline/wsl/about). 

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le portail, accédez à **Machines virtuelles**, puis à la machine virtuelle Linux. Ensuite, dans **Vue d’ensemble**, cliquez sur **Connecter**. Copiez la chaîne permettant de se connecter à votre machine virtuelle.
3. Connectez-vous à la machine virtuelle à l’aide du client SSH de votre choix. Si vous utilisez Windows, vous pouvez utiliser le client SSH dans le [Sous-système Windows pour Linux](https://msdn.microsoft.com/commandline/wsl/about). Si vous avez besoin d’aide pour configurer les clés de votre client SSH, consultez [Comment utiliser les clés SSH avec Windows sur Azure](~/articles/virtual-machines/linux/ssh-from-windows.md), ou [Comment créer et utiliser une paire de clés publique et privée SSH pour les machines virtuelles Linux dans Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).
4. Dans la fenêtre du terminal, à l’aide de CURL, envoyez une requête au point de terminaison d’identité du service IMDS (Instance Metadata Service) Azure pour obtenir un jeton d’accès à Azure Resource Manager.  

   La requête CURL permettant d’acquérir un jeton d’accès est illustrée dans l’exemple suivant. Veillez à remplacer `<CLIENT ID>` par la propriété `clientId` retournée avec la commande `az identity create` dans [Créer une identité attribuée à l’utilisateur](#create-a-user-assigned-msi) : 
    
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<MSI CLIENT ID>"   
   ```
    
    > [!NOTE]
    > La valeur du paramètre `resource` doit correspondre exactement à ce qui est attendu par Azure AD. Lorsque vous utilisez l’ID de ressource Resource Manager, vous devez inclure la barre oblique de fin à l’URI. 
    
    La réponse inclut le jeton d’accès dont vous avez besoin pour accéder à Azure Resource Manager. 
    
    Exemple de réponse :  

    ```bash
    {
    "access_token":"eyJ0eXAiOi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"
    } 
    ```

5. Utilisez le jeton d’accès pour accéder à Azure Resource Manager et lisez les propriétés du groupe de ressources dont vous avez précédemment accordé l’accès à votre identité attribuée à l’utilisateur. Veillez à remplacer `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` par les valeurs que vous avez spécifiées auparavant, et `<ACCESS TOKEN>` par le jeton retourné à l’étape précédente.

    > [!NOTE]
    > L’URL respecte la casse, par conséquent veillez à ce que l’usage des minuscules et des majuscules soit strictement identique à celui que vous avez précédemment suivi lorsque vous avez nommé le groupe de ressources, que la majuscule « G » soit également bien respectée dans `resourceGroups`.  

    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    La réponse contient les informations particulières du groupe de ressources, comme dans l’exemple suivant : 

    ```bash
    {
    "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/DevTest",
    "name":"DevTest",
    "location":"westus",
    "properties":{"provisioningState":"Succeeded"}
    } 
    ```
    
## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble de Managed Service Identity, consultez [Vue d’ensemble](overview.md).

