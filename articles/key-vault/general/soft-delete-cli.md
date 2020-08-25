---
title: Azure Key Vault - Guide pratique pour utiliser la suppression réversible avec l’interface CLI
description: Découvrez comment utiliser Azure CLI pour mettre en application la fonctionnalité de suppression réversible d’Azure Key Vault qui permet de récupérer des coffres de clés et des objets de coffre de clés.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/11/2020
ms.author: sudbalas
ms.openlocfilehash: a86402fec698a299c7f233dcd8c7fde8270dd74d
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585659"
---
# <a name="how-to-use-key-vault-soft-delete-with-cli"></a>Guide pratique pour utiliser la suppression réversible Key Vault avec l’interface CLI

La fonctionnalité de suppression réversible d’Azure Key Vault permet de récupérer des coffres et des objets de coffres qui ont été supprimés. La suppression réversible traite plus particulièrement les scénarios suivants :

- Prise en charge de la suppression récupérable d’un coffre de clés
- Prise en charge de la suppression récupérable d’objets de coffre de clés (clés, secrets et certificats)

## <a name="prerequisites"></a>Prérequis

- Azure CLI - Si vous ne l’avez pas encore configuré pour votre environnement, consultez [Gérer Key Vault avec Azure CLI](manage-with-cli2.md).

Pour obtenir des informations de référence propres à Key Vault pour l’interface CLI, consultez [Informations de référence sur Key Vault Azure CLI](https://docs.microsoft.com/cli/azure/keyvault).

## <a name="required-permissions"></a>Autorisations requises

Les opérations Key Vault sont gérées séparément par l’intermédiaire d’autorisations de contrôle d’accès en fonction du rôle (RBAC) comme suit :

| Opération | Description | Autorisation utilisateur |
|:--|:--|:--|
|List|Énumère les coffres de clé supprimés.|Microsoft.KeyVault/deletedVaults/read|
|Recover|Restaure un coffre de clés supprimé.|Microsoft.KeyVault/vaults/write|
|Purge|Supprime définitivement un coffre de clés supprimé et tout son contenu.|Microsoft.KeyVault/locations/deletedVaults/purge/action|

Pour plus d’informations sur les autorisations et le contrôle d’accès, consultez [Sécuriser votre coffre de clés](secure-your-key-vault.md).

## <a name="enabling-soft-delete"></a>Activation de la suppression réversible

L’activation de la « suppression réversible » permet de récupérer un coffre de clés supprimé ou des objets stockés dans un coffre de clés.

> [!IMPORTANT]
> L’activation de la « suppression réversible » sur un coffre de clés est une action irréversible. Une fois que la propriété de suppression réversible a été définie sur « true », elle ne peut pas être changée ou supprimée.  

### <a name="existing-key-vault"></a>Coffre de clés existant

Pour un coffre de clés existant nommé ContosoVault, vous pouvez activer la suppression réversible comme suit. 

```azurecli
az keyvault update -n ContosoVault --enable-soft-delete true
```

### <a name="new-key-vault"></a>Nouveau coffre de clés

La suppression réversible est automatiquement activée sur tous les coffres de clés par défaut. Le 31 décembre 2020, il ne sera plus possible de créer un coffre de clés sans la suppression réversible activée.

### <a name="verify-soft-delete-enablement"></a>Vérifier l’activation de la suppression réversible

Pour vérifier que la suppression réversible est activée pour un coffre de clés, exécutez la commande *show* et recherchez l’attribut « Soft Delete Enabled ? » :

```azurecli
az keyvault show --name ContosoVault
```

## <a name="deleting-a-soft-delete-protected-key-vault"></a>Suppression d'un coffre de clés protégé par la suppression réversible

Le comportement de la commande pour supprimer un coffre de clés varie selon que la suppression réversible est activée ou non.

> [!IMPORTANT]
>Si vous exécutez la commande suivante pour un coffre de clés pour lequel la suppression réversible n’est pas activée, vous supprimez définitivement ce coffre de clés et tout son contenu, sans aucune option de récupération !

```azurecli
az keyvault delete --name ContosoVault
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>Comment la suppression réversible protège-t-elle votre coffre de clés ?

Quand la suppression réversible est activée :

- Un coffre de clés supprimé est enlevé de son groupe de ressources et placé dans un espace de noms réservé, associé à l’emplacement où il a été créé. 
- Les objets supprimés, tels que les clés, les secrets et les certificats, sont inaccessibles tant que leur coffre de clés conteneur est à l’état supprimé. 
- Le nom DNS d’un coffre de clés supprimé est réservé, empêchant toute création d’un coffre de clés avec le même nom.  

Vous pouvez afficher les coffres de clé associés à votre abonnement qui sont à l’état supprimé en exécutant la commande suivante :

```azurecli
az keyvault list-deleted
```
- *ID* permet d’identifier la ressource lors de la récupération ou du vidage. 
- *ID de ressource* est l’ID de ressource d’origine de ce coffre. Ce coffre de clés étant maintenant à l’état supprimé, il n’existe aucune ressource avec cet ID de ressource. 
- *Date de vidage planifiée* indique quand le coffre sera supprimé définitivement si aucune action n’est effectuée. La période de rétention par défaut, utilisée pour calculer la *Date de vidage planifiée*, est de 90 jours.

## <a name="recovering-a-key-vault"></a>Récupération d’un coffre de clés

Pour récupérer un coffre de clés, vous spécifiez le nom du coffre de clés, le groupe de ressources et l’emplacement. Notez l’emplacement et le groupe de ressources du coffre de clés supprimé, car vous en aurez besoin pour le processus de récupération.

```azurecli
az keyvault recover --location westus --resource-group ContosoRG --name ContosoVault
```

Quand un coffre de clés est récupéré, une ressource est créée avec l’ID de ressource d’origine du coffre de clés. Si le groupe de ressources d’origine est supprimé, un groupe de ressources doit être créé avec le même nom avant la tentative de récupération.

## <a name="deleting-and-purging-key-vault-objects"></a>Suppression et vidage d'objets du coffre de clés

La commande suivante supprime la clé nommée « ContosoFirstKey » dans un coffre de clés nommé « ContosoVault » pour lequel la suppression réversible est activée :

```azurecli
az keyvault key delete --name ContosoFirstKey --vault-name ContosoVault
```

Quand la suppression réversible est activée pour votre coffre de clés, une clé supprimée apparaît toujours comme ayant été supprimée, sauf quand vous énumérez ou récupérez explicitement les clés supprimées. La plupart des opérations effectuées sur une clé à l’état supprimé échoueront, sauf l’énumération d’une clé supprimée, sa récupération ou son vidage. 

Par exemple, pour demander à énumérer les clés supprimées dans un coffre de clés, exécutez la commande suivante :

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="transition-state"></a>État de transition 

Quand vous supprimez une clé dans un coffre de clés pour lequel la suppression réversible est activée, la transition peut prendre quelques secondes. Pendant cette transition, il peut sembler que la clé n’est pas à l’état actif ou à l’état supprimé. 

### <a name="using-soft-delete-with-key-vault-objects"></a>Utilisation de la suppression réversible avec des objets de coffre de clés

Tout comme les coffres de clés, une clé, un secret ou un certificat supprimé(e) reste à l’état supprimé pendant 90 jours, sauf si vous le récupérez ou si vous le videz.

#### <a name="keys"></a>Keys

Pour récupérer une clé qui a été supprimée de façon réversible :

```azurecli
az keyvault key recover --name ContosoFirstKey --vault-name ContosoVault
```

Pour supprimer définitivement (opération également appelée vidage) une clé qui a été supprimée de façon réversible :

> [!IMPORTANT]
> Le vidage d’une clé entraîne sa suppression définitive ; vous ne pourrez pas la récupérer ! 

```azurecli
az keyvault key purge --name ContosoFirstKey --vault-name ContosoVault
```

Les actions de **récupération** et de **vidage** ont leurs propres autorisations associées dans une stratégie d’accès au coffre de clés. Pour qu’un utilisateur ou un principal de service puisse exécuter une action **recover** ou **purge**, il doit disposer de l’autorisation correspondante pour cette clé ou ce secret. Par défaut, l’action **purge** n’est pas ajoutée à la stratégie d’accès d’un coffre de clés quand le raccourci « all » est utilisé pour accorder toutes les autorisations. Vous devez accorder spécifiquement l’autorisation **purge**. 

#### <a name="set-a-key-vault-access-policy"></a>Définir une stratégie d’accès au coffre de clés

La commande suivante accorde à user@contoso.com l’autorisation d’utiliser plusieurs opérations sur les clés dans *ContosoVault*, notamment **purge** :

```azurecli
az keyvault set-policy --name ContosoVault --key-permissions get create delete list update import backup restore recover purge
```

>[!NOTE] 
> Si vous avez un coffre de clés pour lequel la suppression réversible vient d’être activée, vous ne disposerez peut-être pas des autorisations **recover** et **purge**.

#### <a name="secrets"></a>Secrets

Comme les clés, les secrets sont gérés avec leurs propres commandes :

- Supprimer un secret nommé SQLPassword : 
  ```azurecli
  az keyvault secret delete --vault-name ContosoVault -name SQLPassword
  ```

- Énumérer tous les secrets supprimés dans un coffre de clés : 
  ```azurecli
  az keyvault secret list-deleted --vault-name ContosoVault
  ```

- Récupérer un secret à l’état supprimé : 
  ```azurecli
  az keyvault secret recover --name SQLPassword --vault-name ContosoVault
  ```

- Vider un secret à l’état supprimé : 

  > [!IMPORTANT]
  > Le vidage d’un secret entraîne sa suppression définitive ; vous ne pourrez pas le récupérer ! 

  ```azurecli
  az keyvault secret purge --name SQLPAssword --vault-name ContosoVault
  ```

## <a name="purging-a-soft-delete-protected-key-vault"></a>Vidage d'un coffre de clés protégé par la suppression réversible

> [!IMPORTANT]
> Le vidage d'un coffre de clés ou d'un des objets qui y est contenu entraîne sa suppression définitive, ce qui signifie que vous ne pourrez pas le récupérer !

La fonction de vidage permet de supprimer définitivement un objet d’un coffre de clés ou un coffre de clés tout entier précédemment supprimé de façon réversible. Comme démontré à la section précédente, les objets stockés dans un coffre de clés et pour lesquels la fonction de suppression réversible est activée peuvent passer par différents états :

- **Actif** : avant la suppression.
- **Supprimé de manière réversible** : après la suppression ; l'objet peut être répertorié et rétabli à l'état Actif.
- **Supprimé définitivement** : après le vidage ; il impossible de récupérer l'objet.

Cela s'applique également au coffre de clés. Pour supprimer définitivement un coffre de clés supprimé de manière réversible et son contenu, vous devez vider le coffre de clés.

### <a name="purging-a-key-vault"></a>Vidage d'un coffre de clés

Quand un coffre de clés est vidé, tout son contenu est supprimé définitivement, notamment les clés, les secrets et les certificats. Pour vider un coffre de clés supprimé de manière réversible, utilisez la commande `az keyvault purge`. Vous pouvez trouver l’emplacement des coffres de clés supprimés pour votre abonnement à l’aide de la commande `az keyvault list-deleted`.

```azurecli
az keyvault purge --location westus --name ContosoVault
```

### <a name="purge-permissions-required"></a>Autorisations de vidage requises
- Pour vider un coffre de clés supprimé, l’utilisateur a besoin de l’autorisation RBAC sur l’opération *Microsoft.KeyVault/locations/deletedVaults/purge/action*. 
- Pour énumérer un coffre de clés supprimé, l’utilisateur a besoin de l’autorisation RBAC sur l’opération *Microsoft.KeyVault/deletedVaults/read*. 
- Par défaut, seul un administrateur d’abonnement dispose de ces autorisations. 

### <a name="scheduled-purge"></a>Vidage planifié

L’énumération des objets du coffre de clés supprimés indique également quand est planifiée leur suppression définitive par Key Vault. *Date de vidage planifiée* indique quand un objet de coffre de clés sera supprimé définitivement si aucune action n’est effectuée. Par défaut, la période de rétention d’un objet de coffre de clés supprimé est de 90 jours.

>[!IMPORTANT]
>Un objet de coffre dont le vidage est déclenché par son champ *Date de vidage planifiée* est supprimé définitivement. Il n’est pas récupérable !

## <a name="enabling-purge-protection"></a>Activation de la protection contre le vidage

Quand la protection contre le vidage est activée, un coffre ou un objet dans un état Supprimé ne peut pas être supprimé définitivement tant que la période de conservation de 90 jours ne s’est pas écoulée. Ce type de coffre ou d’objet peut toujours être récupéré. Cette fonctionnalité donne l’assurance qu’un coffre ou un objet ne peut jamais être supprimé définitivement tant que la période de conservation ne s’est pas écoulée.

Vous pouvez activer la protection contre le vidage seulement si la suppression réversible est également activée. La désactivation de la protection contre le vidage n’est pas prise en charge.

Pour activer à la fois la suppression réversible et la protection contre le vidage lors de la création d’un coffre, utilisez la commande [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) :

```azurecli
az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
```

Pour ajouter la protection contre le vidage à un coffre existant (où la suppression réversible est déjà activée), utilisez la commande [az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) :

```azurecli
az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
```

## <a name="other-resources"></a>Autres ressources

- Pour obtenir une vue d’ensemble de la fonctionnalité de suppression réversible de Key Vault, consultez [Vue d’ensemble de la suppression réversible d’Azure Key Vault](soft-delete-overview.md).
- Pour obtenir une vue d’ensemble de l’utilisation d’Azure Key Vault, consultez [Qu’est-ce qu’Azure Key Vault ?](overview.md)

