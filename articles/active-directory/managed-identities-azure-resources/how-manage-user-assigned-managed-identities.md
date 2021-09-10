---
title: Gérer les identités managées affectées par l’utilisateur - Azure AD
description: Créer des identités managées affectées par l’utilisateur.
services: active-directory
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: how-to
ms.workload: identity
ms.date: 06/08/2021
ms.author: barclayn
zone_pivot_groups: identity-mi-methods
ms.openlocfilehash: 83f47dbbdd0968a845871b106be9142fd67f9967
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "122641203"
---
# <a name="manage-user-assigned-managed-identities"></a>Gérer les identités gérées affectées par l’utilisateur



Les identités gérées pour les ressources Azure éliminent la nécessité de gérer les informations d’identification dans le code. Elles vous permettent d’obtenir un jeton Azure Active Directory que vos applications peuvent utiliser lorsque vous accédez aux ressources qui prennent en charge l’authentification Azure AD. Azure gère l’identité pour que vous n’ayez pas à le faire.

Il existe deux types d’identités managées : celles affectées par le système et celles affectées par l’utilisateur. La principale différence entre eux est que le cycle de vie des identités managées affectées par le système est lié à la ressource dans laquelle elles sont utilisées. Les identités managées affectées par l’utilisateur peuvent être utilisées sur plusieurs ressources. Pour plus d’informations sur les identités managées, consultez [Que sont les identités managées pour les ressources Azure ?](overview.md).

::: zone pivot="identity-mi-methods-azp"
Dans cet article, vous allez découvrir comment créer, afficher, supprimer ou attribuer un rôle à une identité managée affectée par l’utilisateur avec le Portail Azure.

## <a name="prerequisites"></a>Prérequis

- Si vous n’êtes pas familiarisé avec les identités managées pour ressources Azure, consultez la [section Vue d’ensemble](overview.md). Veillez à consulter la [différence entre les identités managées affectées par le système et celles affectées par l’utilisateur](overview.md#managed-identity-types) .
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un compte gratuit](https://azure.microsoft.com/free/) avant de continuer.


## <a name="create-a-user-assigned-managed-identity"></a>Créer une identité managée attribuée par l’utilisateur

Pour créer une identité managée affectée par l’utilisateur, votre compte a besoin de l’affectation de rôle [Contributeur d’identité managée](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

1. Connectez-vous au [Portail Azure](https://portal.azure.com) en utilisant un compte associé à l’abonnement Azure pour créer l’identité managée affectée par l’utilisateur.
1. Dans la zone de recherche, entrez **Identités managées**. Sous **Services**, sélectionnez **Identités managées**.
1. Sélectionnez **Ajouter** et entrez des valeurs dans les zones suivantes du volet **Créer une identité managée affectée par l’utilisateur** :
    - **Abonnement**: Sélectionnez l’abonnement sous lequel créer l’identité managée affectée par l’utilisateur.
    - **Groupe de ressources** : choisissez un groupe de ressources pour créer l’identité managée affectée à l’utilisateur ou sélectionnez **Créer un nouveau** pour créer un groupe de ressources.
    - **Région** : choisissez une région Azure où déployer l’identité managée affectée par l’utilisateur, par exemple **USA Ouest**.
    - **Nom de la ressource** : entrez le nom de votre identité managée affectée par l’utilisateur, par exemple UAI1.
  
[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]
  
   ![Capture d’écran montrant le volet Créer une identité managée affectée par l’utilisateur.](media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)
1. Sélectionnez **Vérifier + créer** pour passer en revue les modifications.
1. Sélectionnez **Create** (Créer).

## <a name="list-user-assigned-managed-identities"></a>Répertorier les identités managées affectées par l’utilisateur

Pour répertorier ou lire une identité managée affectée par l’utilisateur, votre compte a besoin de l’affectation de rôle [Opérateur d’identité managée](../../role-based-access-control/built-in-roles.md#managed-identity-operator) ou [Contributeur d’identité managée](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

1. Connectez-vous au [Portail Azure](https://portal.azure.com) en utilisant un compte associé à l’abonnement Azure afin de répertorier les identités managées affectées par l’utilisateur.
1. Dans la zone de recherche, entrez **Identités managées**. Sous **Services**, sélectionnez **Identités managées**.
1. Une liste d’identités managées affectées par l’utilisateur pour votre abonnement est retournée. Pour afficher les détails d’une identité managée affectée par l’utilisateur, sélectionnez son nom.

   ![Capture d’écran montrant la liste des identités managées affectées par l’utilisateur.](media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>Supprimer une identité managée affectée par l’utilisateur

Pour supprimer une identité managée affectée par l’utilisateur, votre compte a besoin de l’affectation de rôle [Contributeur d’identité managée](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

La suppression d’une identité affectée par l’utilisateur n’est pas supprimée de la machine virtuelle ou de la ressource à laquelle elle a été affectée. Pour supprimer l’identité affectée par l’utilisateur d’une machine virtuelle, reportez-vous à [Supprimer une identité managée affectée par l’utilisateur sur une machine virtuelle](qs-configure-portal-windows-vm.md#remove-a-user-assigned-managed-identity-from-a-vm).

1. Connectez-vous au [Portail Azure](https://portal.azure.com) en utilisant un compte associé à l’abonnement Azure afin de supprimer une identité managée affectée par l’utilisateur.
1. Sélectionnez l’identité managée affectée par l’utilisateur, puis cliquez sur **Supprimer**.
1. Dans la zone de confirmation, sélectionnez **Oui**.

   ![Capture d’écran montrant la suppression des identités managées affectées par l’utilisateur.](media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>Affecter un rôle à une identité managée affectée par l’utilisateur 

Pour affecter un rôle à une identité managée affectée par l’utilisateur, votre compte a besoin de l’affectation de rôle [Administrateur de l’accès utilisateur](../../role-based-access-control/built-in-roles.md#user-access-administrator).

1. Connectez-vous au [Portail Azure](https://portal.azure.com) en utilisant un compte associé à l’abonnement Azure afin de répertorier les identités managées affectées par l’utilisateur.
1. Dans la zone de recherche, entrez **Identités managées**. Sous **Services**, sélectionnez **Identités managées**.
1. Une liste d’identités managées affectées par l’utilisateur pour votre abonnement est retournée. Sélectionnez l’identité managée affectée par l’utilisateur à laquelle vous souhaitez affecter un rôle.
1. Sélectionnez **Contrôle d’accès (IAM)** , puis **Ajouter une attribution de rôle**.

   ![Capture d’écran montrant le début de l’identité managée affectée par l’utilisateur.](media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

1. Dans le volet **Ajouter une attribution de rôle**, configurez les valeurs suivantes, puis sélectionnez **Enregistrer** :
   - **Rôle** : rôle à attribuer.
   - **Attribuer l’accès à** : ressource à laquelle attribuer l’identité managée affectée par l’utilisateur.
   - **Sélectionner** : membre auquel attribuer l’accès.
   
   ![Capture d’écran qui montre l’identité managée affectée par l’utilisateur IAM.](media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)



::: zone-end



::: zone pivot="identity-mi-methods-azcli"

Dans cet article, vous allez découvrir comment créer, afficher, supprimer ou attribuer un rôle à une identité managée affectée par l’utilisateur avec Azure CLI.

## <a name="prerequisites"></a>Prérequis

- Si vous n’êtes pas familiarisé avec les identités managées pour ressources Azure, consultez la [section Vue d’ensemble](overview.md). *Veillez à consulter la [différence entre les identités managées affectées par le système et celles affectées par l’utilisateur](overview.md#managed-identity-types)* .
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un compte gratuit](https://azure.microsoft.com/free/) avant de continuer.


[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

> [!IMPORTANT]  
> Pour modifier les autorisations utilisateur lorsque vous utilisez un principal de service d’application à l’aide de l’interface CLI, il est nécessaire de fournir au principal de service des autorisations supplémentaires dans l’API Graph Azure Active Directory, car certaines parties de l’interface CLI effectuent des requêtes GET sur cette API. Dans le cas contraire, vous risquez de recevoir le message « Privilèges insuffisants pour terminer l’opération ». Pour ce faire, accédez à l’**inscription de l’application** dans Azure AD, sélectionnez votre application, sélectionnez **Autorisations d’API**, faites défiler la liste et sélectionnez **Azure Active Directory Graph**. Sélectionnez alors **Autorisations d’application**, puis ajoutez les autorisations nécessaires. 

## <a name="create-a-user-assigned-managed-identity"></a>Créer une identité managée attribuée par l’utilisateur 

Pour créer une identité managée affectée par l’utilisateur, votre compte a besoin de l’affectation de rôle [Contributeur d’identité managée](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Pour créer une identité managée affectée par l’utilisateur, utilisez la commande [az identity create](/cli/azure/identity#az_identity_create). Le paramètre `-g` spécifie le groupe de ressources dans lequel l’identité managée affectée par l’utilisateur doit être créée. Le paramètre `-n` spécifie son nom. Remplacez les valeurs des paramètres `<RESOURCE GROUP>` et `<USER ASSIGNED IDENTITY NAME>` par vos propres valeurs.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Répertorier les identités managées affectées par l’utilisateur

Pour répertorier ou lire une identité managée affectée par l’utilisateur, votre compte a besoin de l’affectation de rôle [Opérateur d’identité managée](../../role-based-access-control/built-in-roles.md#managed-identity-operator) ou [Contributeur d’identité managée](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Pour répertorier les identités affectées par l’utilisateur, utilisez la commande [az identity list](/cli/azure/identity#az_identity_list). Remplacez la valeur `<RESOURCE GROUP>` avec votre propre valeur.

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```

Dans la réponse JSON, les identités managées affectées par l’utilisateur ont une valeur `"Microsoft.ManagedIdentity/userAssignedIdentities"` retournée pour la clé `type`.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Supprimer une identité managée affectée par l’utilisateur

Pour supprimer une identité managée affectée par l’utilisateur, votre compte a besoin de l’affectation de rôle [Contributeur d’identité managée](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Pour supprimer une identité managée affectée par l’utilisateur, utilisez la commande [az identity delete](/cli/azure/identity#az_identity_delete). Le paramètre -n spécifie son nom. Le paramètre -g spécifie le groupe de ressources dans lequel l’identité managée affectée par l’utilisateur a été créée. Remplacez les valeurs des paramètres `<USER ASSIGNED IDENTITY NAME>` et `<RESOURCE GROUP>` par vos propres valeurs.

```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> La suppression d’une identité managée affectée par l’utilisateur n’a pas pour effet de supprimer la référence d’une ressource à laquelle elle a été affectée. Supprimez-les d’une machine virtuelle ou d’un groupe de machines virtuelles identiques à l’aide de la commande `az vm/vmss identity remove`.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une liste complète des commandes d’identité Azure CLI, consultez [az identity](/cli/azure/identity).

Pour plus d’informations sur la façon d’attribuer une identité managée affectée par l’utilisateur à une machine virtuelle Azure, voir [Configurer des identités managées pour ressources Azure sur une machine virtuelle Azure en utilisant Azure CLI](qs-configure-cli-windows-vm.md#user-assigned-managed-identity).


::: zone-end

::: zone pivot="identity-mi-methods-powershell"

Dans cet article, vous allez découvrir comment créer, afficher, supprimer ou attribuer un rôle à une identité managée affectée par l’utilisateur avec PowerShell.

## <a name="prerequisites"></a>Prérequis

- Si vous n’êtes pas familiarisé avec les identités managées pour ressources Azure, consultez la [section Vue d’ensemble](overview.md). *Veillez à consulter la [différence entre les identités managées affectées par le système et celles affectées par l’utilisateur](overview.md#managed-identity-types)* .
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un compte gratuit](https://azure.microsoft.com/free/) avant de continuer.
- Pour exécuter les exemples de scripts, vous avez deux options :
    - Utiliser [Azure Cloud Shell](../../cloud-shell/overview.md), que vous pouvez ouvrir en utilisant le bouton **Essayer** dans le coin supérieur droit des blocs de code.
    - Exécutez les scripts localement avec Azure PowerShell, comme décrit dans la section suivante.

Dans cet article, vous allez découvrir comment créer, lister et supprimer une identité managée affectée par l’utilisateur en utilisant PowerShell.

### <a name="configure-azure-powershell-locally"></a>Configurer Azure PowerShell localement

Pour utiliser Azure PowerShell localement dans cet article au lieu d’utiliser Cloud Shell :

1. Installez [la dernière version d’Azure PowerShell](/powershell/azure/install-az-ps) si ce n’est déjà fait.

1. Connectez-vous à Azure.

    ```azurepowershell
    Connect-AzAccount
    ```

1. Installez la [dernière version de PowerShellGet](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).

    ```azurepowershell
    Install-Module -Name PowerShellGet -AllowPrerelease
    ```

    Vous devrez peut-être quitter (`Exit`) la session PowerShell en cours après avoir exécuté cette commande, pour l’étape suivante.

1. Installez la version préliminaire du module `Az.ManagedServiceIdentity`, afin d’effectuer les opérations d’identité managée affectée par l’utilisateur qui sont décrites dans cet article.

    ```azurepowershell
    Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease
    ```

## <a name="create-a-user-assigned-managed-identity"></a>Créer une identité managée attribuée par l’utilisateur

Pour créer une identité managée affectée par l’utilisateur, votre compte a besoin de l’affectation de rôle [Contributeur d’identité managée](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Pour créer une identité managée affectée par l’utilisateur, utilisez la commande `New-AzUserAssignedIdentity`. Le paramètre `ResourceGroupName` spécifie le groupe de ressources dans lequel l’identité managée affectée par l’utilisateur doit être créée. Le paramètre `-Name` spécifie son nom. Remplacez les valeurs des paramètres `<RESOURCE GROUP>` et `<USER ASSIGNED IDENTITY NAME>` par vos propres valeurs.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```

## <a name="list-user-assigned-managed-identities"></a>Répertorier les identités managées affectées par l’utilisateur

Pour répertorier ou lire une identité managée affectée par l’utilisateur, votre compte a besoin de l’affectation de rôle [Opérateur d’identité managée](../../role-based-access-control/built-in-roles.md#managed-identity-operator) ou [Contributeur d’identité managée](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Pour répertorier les identités affectées par l’utilisateur, utilisez la commande [Get-AzUserAssigned]. Le paramètre `-ResourceGroupName` spécifie le groupe de ressources dans lequel l’identité managée affectée par l’utilisateur a été créée. Remplacez la valeur `<RESOURCE GROUP>` avec votre propre valeur.

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```

Dans la réponse, les identités managées affectées par l’utilisateur ont une valeur `"Microsoft.ManagedIdentity/userAssignedIdentities"` retournée pour la clé `Type`.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Supprimer une identité managée affectée par l’utilisateur

Pour supprimer une identité managée affectée par l’utilisateur, votre compte a besoin de l’affectation de rôle [Contributeur d’identité managée](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Pour supprimer une identité managée affectée par l’utilisateur, utilisez la commande `Remove-AzUserAssignedIdentity`. Le paramètre `-ResourceGroupName` spécifie le groupe de ressources dans lequel l’identité attribuée à l’utilisateur a été créée. Le paramètre `-Name` spécifie son nom. Remplacez les valeurs des paramètres `<RESOURCE GROUP>` et `<USER ASSIGNED IDENTITY NAME>` par vos propres valeurs.

```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```

> [!NOTE]
> La suppression d’une identité managée affectée par l’utilisateur n’a pas pour effet de supprimer la référence d’une ressource à laquelle elle a été affectée. Les attributions d’identités doivent être supprimées séparément.

## <a name="next-steps"></a>Étapes suivantes

Pour la liste complète et les détails des commandes d’identités managées Azure PowerShell pour ressources Azure, consultez [Az.ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity).


::: zone-end


::: zone pivot="identity-mi-methods-arm"

Dans cet article, vous allez créer une identité managée affectée par l’utilisateur en utilisant Azure Resource Manager.

## <a name="prerequisites"></a>Prérequis

- Si vous n’êtes pas familiarisé avec les identités managées pour ressources Azure, consultez la [section Vue d’ensemble](overview.md). *Veillez à consulter la [différence entre les identités managées affectées par le système et celles affectées par l’utilisateur](overview.md#managed-identity-types)* .
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un compte gratuit](https://azure.microsoft.com/free/) avant de continuer.

Vous ne pouvez pas répertorier et supprimer une identité managée affectée par l’utilisateur à l’aide d’un modèle Resource Manager. Pour créer et afficher une identité managée affectée par l’utilisateur, voir les articles suivants :

- [Afficher une identité managée affectée par l’utilisateur](how-to-manage-ua-identity-cli.md#list-user-assigned-managed-identities)
- [Supprimer une identité managée affectée par l’utilisateur](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-managed-identity)

## <a name="template-creation-and-editing"></a>Création et modification du modèle

Comme pour le Portail Azure et le script, les modèles Resource Manager offrent la possibilité de déployer des ressources nouvelles ou modifiées définies par un groupe de ressources Azure. Plusieurs options sont disponibles pour la modification du modèle et le déploiement, à la fois localement et sur le portail. Vous pouvez :

- Utiliser un [modèle personnalisé à partir de la Place de marché Microsoft Azure](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) pour créer un modèle à partir de zéro, ou à partir d’un modèle commun existant ou d’un [modèle de démarrage rapide](https://azure.microsoft.com/resources/templates/).
- Dériver à partir d’un groupe de ressources existant, en exportant un modèle à partir du [déploiement d’origine](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates), ou à partir de l’[état actuel du déploiement](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates).
- Utiliser un [éditeur local JSON (VS Code, par exemple)](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md), puis télécharger et déployer à l’aide de PowerShell ou d’Azure CLI.
- Utiliser le [projet de groupe de ressources Azure](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) de Visual Studio pour créer et déployer un modèle. 

## <a name="create-a-user-assigned-managed-identity"></a>Créer une identité managée attribuée par l’utilisateur 

Pour créer une identité managée affectée par l’utilisateur, votre compte a besoin de l’affectation de rôle [Contributeur d’identité managée](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Pour créer une identité managée affectée par l’utilisateur, utilisez le modèle suivant. Remplacez la valeur `<USER ASSIGNED IDENTITY NAME>` avec vos propres valeurs.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "resourceName": {
          "type": "string",
          "metadata": {
            "description": "<USER ASSIGNED IDENTITY NAME>"
          }
        }
  },
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('resourceName')]",
      "apiVersion": "2018-11-30",
      "location": "[resourceGroup().location]"
    }
  ],
  "outputs": {
      "identityName": {
          "type": "string",
          "value": "[parameters('resourceName')]"
      }
  }
}
```
## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la façon d’attribuer une identité managée affectée par l’utilisateur à une machine virtuelle Azure en utilisant un modèle Resource Manager, consultez [Configurer des identités managées pour ressources Azure sur une machine virtuelle Azure en utilisant un modèle](qs-configure-template-windows-vm.md).




::: zone-end


::: zone pivot="identity-mi-methods-rest"

Dans cet article, vous allez découvrir comment créer, lister et supprimer une identité managée affectée par l’utilisateur en utilisant REST.


## <a name="prerequisites"></a>Prérequis

- Si vous n’êtes pas familiarisé avec les identités managées pour ressources Azure, consultez la [section Vue d’ensemble](overview.md). *Veillez à consulter la [différence entre les identités managées affectées par le système et celles affectées par l’utilisateur](overview.md#managed-identity-types)* .
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un compte gratuit](https://azure.microsoft.com/free/) avant de continuer.
- Vous pouvez exécuter toutes les commandes de cet article dans le cloud ou localement :
    - Pour exécuter dans le cloud, utilisez [Azure Cloud Shell](../../cloud-shell/overview.md).
    - Pour une exécution locale, installez [curl](https://curl.haxx.se/download.html) et [Azure CLI](/cli/azure/install-azure-cli).


Dans cet article, découvrez comment créer, répertorier et supprimer une identité managée affectée par l’utilisateur en utilisant CURL pour effectuer des appels d’API REST.

## <a name="obtain-a-bearer-access-token"></a>Obtenir un jeton d’accès de porteur

1. Si vous travaillez localement, connectez-vous à Azure via Azure CLI.

    ```
    az login
    ```

1. Obtenir un jeton d’accès en utilisant [az account get-access-token](/cli/azure/account#az_account_get_access_token).

    ```azurecli-interactive
    az account get-access-token
    ```

## <a name="create-a-user-assigned-managed-identity"></a>Créer une identité managée attribuée par l’utilisateur 

Pour créer une identité managée affectée par l’utilisateur, votre compte a besoin de l’affectation de rôle [Contributeur d’identité managée](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```

**En-têtes de requête**

|En-tête de requête  |Description  |
|---------|---------|
|*Content-Type*     | Obligatoire. Défini sur `application/json`.        |
|*Autorisation*     | Obligatoire. Défini sur un jeton d’accès `Bearer` valide.        |

**Corps de la demande**

|Nom  |Description  |
|---------|---------|
|Emplacement     | Obligatoire. Emplacement de la ressource.        |

## <a name="list-user-assigned-managed-identities"></a>Répertorier les identités managées affectées par l’utilisateur

Pour répertorier ou lire une identité managée affectée par l’utilisateur, votre compte a besoin de l’affectation de rôle [Opérateur d’identité managée](../../role-based-access-control/built-in-roles.md#managed-identity-operator) ou [Contributeur d’identité managée](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview HTTP/1.1
```

|En-tête de requête  |Description  |
|---------|---------|
|*Content-Type*     | Obligatoire. Défini sur `application/json`.        |
|*Autorisation*     | Obligatoire. Défini sur un jeton d’accès `Bearer` valide.        |

## <a name="delete-a-user-assigned-managed-identity"></a>Supprimer une identité managée affectée par l’utilisateur

Pour supprimer une identité managée affectée par l’utilisateur, votre compte a besoin de l’affectation de rôle [Contributeur d’identité managée](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

> [!NOTE]
> La suppression d’une identité managée affectée par l’utilisateur n’a pas pour effet de supprimer la référence d’une ressource à laquelle elle a été affectée. Pour supprimer une identité managée affectée par l’utilisateur d’une machine virtuelle en utilisant CURL, consultez [Supprimer une identité affectée par l’utilisateur d’une machine virtuelle Azure](qs-configure-rest-vm.md#remove-a-user-assigned-managed-identity-from-an-azure-vm).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
DELETE https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```
|En-tête de requête  |Description  |
|---------|---------|
|*Content-Type*     | Obligatoire. Défini sur `application/json`.        |
|*Autorisation*     | Obligatoire. Défini sur un jeton d’accès `Bearer` valide.        |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’affectation d’une identité managée affectée par l’utilisateur à une machine virtuelle Azure ou un groupe de machines virtuelles identiques en utilisant CURL, consultez :
- [Configurer des identités managées pour ressources Azure sur une machine virtuelle Azure en utilisant des appels d’API REST](qs-configure-rest-vm.md#user-assigned-managed-identity) 
- [Configurer des identités managées sur un groupe de machines virtuelles identiques en utilisant des appels d’API REST](qs-configure-rest-vmss.md#user-assigned-managed-identity)

::: zone-end


