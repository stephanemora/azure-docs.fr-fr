---
title: Azure Key Vault - Utilisation de la suppression réversible avec PowerShell
description: Exemples d’utilisation de la suppression réversible avec extraits de code PowerShell
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 79d8cb4b09ef547bf1c0b01f48872ddcb4f964ee
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616531"
---
# <a name="how-to-use-key-vault-soft-delete-with-powershell"></a>Utilisation de la suppression réversible Key Vault avec l’interface PowerShell

La fonctionnalité de suppression réversible d’Azure Key Vault permet de récupérer des coffres et des objets de coffres qui ont été supprimés. La suppression réversible traite plus particulièrement les scénarios suivants :

- Prise en charge de la suppression récupérable d’un coffre de clés
- Prise en charge de la suppression récupérable d’objets de coffre de clés (clés, secrets et certificats)

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- Azure PowerShell 1.0.0 ou version ultérieure - Si ce n'est déjà fait, installez Azure PowerShell et associez-le à votre abonnement Azure. Voir [Guide pratique pour installer et configurer Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). 

>[!NOTE]
> Il existe une version obsolète de notre fichier de mise en forme de sortie Key Vault PowerShell qui **peut** se charger dans votre environnement à la place de la version correcte. Nous prévoyons de publier une version mise à jour de PowerShell avec la correction nécessaire de la mise en forme de sortie ; cette rubrique sera mise à jour à cette occasion. D’ici là, si vous rencontrez ce problème de mise en forme, voici comment le contourner :
> - Utilisez la requête suivante si vous ne voyez pas la propriété de suppression réversible activée qui est décrite dans cette rubrique : `$vault = Get-AzKeyVault -VaultName myvault; $vault.EnableSoftDelete`.


Pour obtenir des informations de référence propres à Key Vault pour PowerShell, consultez la [référence Azure Key Vault PowerShell](/powershell/module/az.keyvault).

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

```powershell
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

### <a name="new-key-vault"></a>Nouveau coffre de clés

L’activation de la suppression réversible pour un nouveau coffre de clés s’effectue au moment de la création en ajoutant l’indicateur « soft-delete enable » à votre commande de création.

```powershell
New-AzKeyVault -Name "ContosoVault" -ResourceGroupName "ContosoRG" -Location "westus" -EnableSoftDelete
```

### <a name="verify-soft-delete-enablement"></a>Vérifier l’activation de la suppression réversible

Pour vérifier que la suppression réversible est activée pour un coffre de clés, exécutez la commande *show* et recherchez l’attribut « Soft Delete Enabled ? » :

```powershell
Get-AzKeyVault -VaultName "ContosoVault"
```

## <a name="deleting-a-soft-delete-protected-key-vault"></a>Suppression d'un coffre de clés protégé par la suppression réversible

Le comportement de la commande pour supprimer un coffre de clés varie selon que la suppression réversible est activée ou non.

> [!IMPORTANT]
>Si vous exécutez la commande suivante pour un coffre de clés pour lequel la suppression réversible n’est pas activée, vous supprimez définitivement ce coffre de clés et tout son contenu, sans aucune option de récupération !

```powershell
Remove-AzKeyVault -VaultName 'ContosoVault'
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>Comment la suppression réversible protège-t-elle votre coffre de clés ?

Quand la suppression réversible est activée :

- Un coffre de clés supprimé est enlevé de son groupe de ressources et placé dans un espace de noms réservé, associé à l’emplacement où il a été créé. 
- Les objets supprimés, tels que les clés, les secrets et les certificats, sont inaccessibles tant que leur coffre de clés conteneur est à l’état supprimé. 
- Le nom DNS d’un coffre de clés supprimé est réservé, empêchant toute création d’un coffre de clés avec le même nom.  

Vous pouvez afficher les coffres de clé associés à votre abonnement qui sont à l’état supprimé en exécutant la commande suivante :

```powershell
Get-AzKeyVault -InRemovedState 
```

- *ID* permet d’identifier la ressource lors de la récupération ou du vidage. 
- *ID de ressource* est l’ID de ressource d’origine de ce coffre. Ce coffre de clés étant maintenant à l’état supprimé, il n’existe aucune ressource avec cet ID de ressource. 
- *Date de vidage planifiée* indique quand le coffre sera supprimé définitivement si aucune action n’est effectuée. La période de rétention par défaut, utilisée pour calculer la *Date de vidage planifiée*, est de 90 jours.

## <a name="recovering-a-key-vault"></a>Récupération d’un coffre de clés

Pour récupérer un coffre de clés, vous spécifiez le nom du coffre de clés, le groupe de ressources et l’emplacement. Notez l’emplacement et le groupe de ressources du coffre de clés supprimé, car vous en aurez besoin pour le processus de récupération.

```powershell
Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
```

Quand un coffre de clés est récupéré, une ressource est créée avec l’ID de ressource d’origine du coffre de clés. Si le groupe de ressources d’origine est supprimé, un groupe de ressources doit être créé avec le même nom avant la tentative de récupération.

## <a name="deleting-and-purging-key-vault-objects"></a>Suppression et vidage d'objets du coffre de clés

La commande suivante supprime la clé nommée « ContosoFirstKey » dans un coffre de clés nommé « ContosoVault » pour lequel la suppression réversible est activée :

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey
```

Quand la suppression réversible est activée pour votre coffre de clés, une clé supprimée apparaît toujours comme étant supprimée, sauf si vous énumérez explicitement les clés supprimées. La plupart des opérations effectuées sur une clé à l’état supprimé échouent, sauf l’énumération, la récupération ou le vidage d’une clé supprimée. 

Par exemple, la commande suivante énumère les clés supprimées dans le coffre de clés « ContosoVault » :

```powershell
Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
```

### <a name="transition-state"></a>État de transition 

Quand vous supprimez une clé dans un coffre de clés pour lequel la suppression réversible est activée, la transition peut prendre quelques secondes. Pendant cette transition, il peut sembler que la clé n’est pas à l’état actif ou à l’état supprimé. 

### <a name="using-soft-delete-with-key-vault-objects"></a>Utilisation de la suppression réversible avec des objets de coffre de clés

Tout comme les coffres de clés, une clé, un secret ou un certificat supprimé(e) reste à l’état supprimé pendant 90 jours, sauf si vous le récupérez ou si vous le videz. 

#### <a name="keys"></a>Keys

Pour récupérer une clé qui a été supprimée de façon réversible :

```powershell
Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
```

Pour supprimer définitivement (opération également appelée vidage) une clé qui a été supprimée de façon réversible :

> [!IMPORTANT]
> Le vidage d’une clé entraîne sa suppression définitive ; vous ne pourrez pas la récupérer ! 

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
```

Les actions de **récupération** et de **vidage** ont leurs propres autorisations associées dans une stratégie d’accès au coffre de clés. Pour qu’un utilisateur ou un principal de service puisse exécuter une action **recover** ou **purge**, il doit disposer de l’autorisation correspondante pour cette clé ou ce secret. Par défaut, l’action **purge** n’est pas ajoutée à la stratégie d’accès d’un coffre de clés quand le raccourci « all » est utilisé pour accorder toutes les autorisations. Vous devez accorder spécifiquement l’autorisation **purge**. 

#### <a name="set-a-key-vault-access-policy"></a>Définir une stratégie d’accès au coffre de clés

La commande suivante accorde à user@contoso.com l’autorisation d’utiliser plusieurs opérations sur les clés dans *ContosoVault*, notamment **purge** :

```powershell
Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys get,create,delete,list,update,import,backup,restore,recover,purge
```

>[!NOTE] 
> Si vous avez un coffre de clés pour lequel la suppression réversible vient d’être activée, vous ne disposerez peut-être pas des autorisations **recover** et **purge**.

#### <a name="secrets"></a>Secrets

Comme les clés, les secrets sont gérés avec leurs propres commandes :

- Supprimer un secret nommé SQLPassword : 
  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

- Énumérer tous les secrets supprimés dans un coffre de clés : 
  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

- Récupérer un secret à l’état supprimé : 
  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

- Vider un secret à l’état supprimé : 

  > [!IMPORTANT]
  > Le vidage d’un secret entraîne sa suppression définitive ; vous ne pourrez pas le récupérer !

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```

#### <a name="certificates"></a>Certificats

Vous pouvez gérer les certificats à l’aide des commandes ci-dessous :

- Supprimer un certificat nommé SQLPassword : 
  ```powershell
  Remove-AzKeyVaultCertificate -VaultName ContosoVault -Name 'MyCert'
  ```

- Lister tous les certificats supprimés dans un coffre de clés : 
  ```powershell
  Get-AzKeyVaultCertificate -VaultName ContosoVault -InRemovedState
  ```

- Récupérer un certificat dans l’état supprimé : 
  ```powershell
  Undo-AzKeyVaultCertificateRemoval -VaultName ContosoVault -Name 'MyCert'
  ```

- Vider un certificat dans l’état supprimé : 

  > [!IMPORTANT]
  > Le vidage d’un certificat entraîne sa suppression définitive ; vous ne pourrez pas le récupérer !

  ```powershell
  Remove-AzKeyVaultcertificate -VaultName ContosoVault -Name 'MyCert' -InRemovedState 
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

Quand un coffre de clés est vidé, tout son contenu est supprimé définitivement, notamment les clés, les secrets et les certificats. Pour vider un coffre de clés supprimé de manière réversible, utilisez la commande `Remove-AzKeyVault` avec l'option `-InRemovedState` et spécifiez l'emplacement du coffre de clés supprimé avec l'argument `-Location location`. Vous pouvez déterminer l’emplacement d’un coffre supprimé à l’aide de la commande `Get-AzKeyVault -InRemovedState`.

```powershell
Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
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

Vous pouvez activer la protection contre le vidage seulement si la suppression réversible est également activée. 

Pour activer à la fois la suppression réversible et la protection contre le vidage lors de la création d’un coffre, utilisez l’applet de commande [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault?view=azps-1.5.0) :

```powershell
New-AzKeyVault -Name ContosoVault -ResourceGroupName ContosoRG -Location westus -EnableSoftDelete -EnablePurgeProtection
```

Pour ajouter la protection contre le vidage à un coffre existant (où la suppression réversible est déjà activée), utilisez les applets de commande [Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault?view=azps-1.5.0), [Get-AzResource](/powershell/module/az.resources/get-azresource?view=azps-1.5.0) et [Set-AzResource](/powershell/module/az.resources/set-azresource?view=azps-1.5.0) :

```
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

## <a name="other-resources"></a>Autres ressources

- Pour obtenir une vue d’ensemble de la fonctionnalité de suppression réversible de Key Vault, consultez [Vue d’ensemble de la suppression réversible d’Azure Key Vault](overview-soft-delete.md).
- Pour obtenir une vue d’ensemble de l’utilisation d’Azure Key Vault, consultez [Qu’est-ce qu’Azure Key Vault ?](overview.md)
