---
title: Vue d’ensemble de la récupération d’un module HSM managé Azure Key Vault | Microsoft Docs
description: Les fonctionnalités de récupération d’un module de sécurité matériel (HSM) managé sont conçues pour empêcher la suppression accidentelle ou malveillante de vos clés et ressources de module HSM.
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: how-to
ms.author: mbaldwin
author: mbaldwin
ms.date: 06/01/2021
ms.openlocfilehash: c0688d40e0ccb71b98e598da7d5ff8100b735229
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122563660"
---
# <a name="managed-hsm-soft-delete-and-purge-protection"></a>Suppression réversible et protection contre la suppression définitive d’un module HSM managé

Cet article décrit deux fonctionnalités de récupération d’un module HSM managé : la suppression réversible et la protection contre la suppression définitive. Il fournit une vue d’ensemble de ces fonctionnalités et montre comment les gérer à l’aide d’Azure CLI et d’Azure PowerShell.

Pour plus d’informations, consultez [Vue d’ensemble d’un module HSM managé](overview.md).

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. [Créez-en un gratuitement](https://azure.microsoft.com/free/dotnet).
* Le [module PowerShell](/powershell/azure/install-az-ps).
* Azure CLI 2.25.0 ou version ultérieure. Exécutez `az --version` pour déterminer la version dont vous disposez. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI]( /cli/azure/install-azure-cli).
* Un module de sécurité matériel (HSM) managé. Vous pouvez en créer un à l’aide de l’interface [Azure CLI](./quick-create-cli.md) ou d’[Azure PowerShell](./quick-create-powershell.md).
* Les utilisateurs doivent disposer des autorisations suivantes pour effectuer des opérations sur les clés ou modules de sécurité matériels (HSM). supprimés de manière réversible :

  | Attribution de rôle | Description |
  |---|---|
  |[Contributeur du HSM managé](../../role-based-access-control/built-in-roles.md#managed-hsm-contributor)|Lister, récupérer et supprimer définitivement des modules HSM supprimés de manière réversible|
  |[Utilisateur du chiffrement du HSM managé](./built-in-roles.md)|Lister les clés supprimées de manière réversible|
  |[Responsable du chiffrement du HSM managé](./built-in-roles.md)|Supprimer définitivement et récupérer des clés supprimées de manière réversible|



## <a name="what-are-soft-delete-and-purge-protection"></a>Présentation de la suppression réversible et la protection contre la suppression définitive

La [suppression réversible](soft-delete-overview.md) et la protection contre la suppression définitive sont des fonctionnalités de récupération.


La *suppression réversible* est conçue pour empêcher la suppression accidentelle de votre module HSM et de vos clés. La suppression réversible fonctionne comme une corbeille. Quand vous supprimez un module HSM ou une clé, ils restent récupérables pendant une période de conservation configurable ou pendant une durée par défaut de 90 jours. Les modules HSM et les clés supprimés de manière réversible peuvent également être *purgés*, ce qui signifie qu’ils sont supprimés définitivement. La purge vous permet de recréer des modules HSM et des clés portant le même nom que l’élément purgé. La récupération et la suppression de modules HSM et de clés nécessitent des attributions de rôles spécifiques. La suppression réversible ne peut pas être désactivée.

> [!NOTE]
> Étant donné que les ressources sous-jacentes restent allouées à votre module HSM même quand il est dans l’état supprimé, la ressource de module HSM continue à accumuler des frais horaires pendant qu’il se trouve dans cet état.

Les noms de HSM managés sont globalement uniques dans chaque environnement cloud. Vous ne pouvez donc pas créer un module HSM managé portant le même nom qu’un module HSM se trouvant actuellement dans l’état de suppression réversible. De même, les noms de clés sont uniques au sein d’un module HSM. Vous ne pouvez pas créer une clé portant le même nom qu’une clé se trouvant actuellement dans l’état de suppression réversible.

Pour plus d’informations, consultez [Vue d’ensemble de la suppression réversible d’un module HSM managé](soft-delete-overview.md).

La *protection contre la suppression définitive* est conçue pour empêcher la suppression de vos modules HSM et de vos clés par une personne malveillante interne à votre organisation. Elle est similaire à une corbeille dotée d’un verrou basé sur la durée. Vous pouvez récupérer des éléments à tout moment pendant la période de rétention configurable. Vous ne pouvez pas supprimer définitivement ou purger un module HSM ou une clé tant que la période de conservation n’est pas terminée. Au terme de la période de conservation, le module HSM ou la clé est automatiquement purgé.

> [!NOTE]
> Aucun rôle ni aucune autorisation d’administrateur ne peut remplacer, désactiver ou déjouer la protection contre la suppression définitive. *Si la protection contre la suppression définitive est activée, elle ne peut pas être désactivée ou remplacée par quiconque, notamment Microsoft.* Vous devez donc récupérer un module HSM supprimé ou attendre la fin de la période de conservation pour pouvoir réutiliser le nom du module HSM.

## <a name="manage-keys-and-managed-hsms"></a>Gérer des clés et des modules HSM managés

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="managed-hsms-cli"></a>Modules HSM managés (CLI) 

* Pour vérifier l’état de la suppression réversible et de la protection contre la suppression définitive pour un module HSM managé :

    ```azurecli
    az keyvault show --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} --hsm-name {HSM NAME}
    ```

* Pour supprimer un module HSM :

    ```azurecli
    az keyvault delete --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} --hsm-name {HSM NAME}
    ```
    
  Cette action est récupérable car la suppression réversible est activée par défaut.

* Pour lister tous les modules HSM supprimés de manière réversible :

    ```azurecli
    az keyvault list-deleted --subscription {SUBSCRIPTION ID} --resource-type hsm
    ```

* Pour récupérer un module HSM supprimé de manière réversible :

    ```azurecli
    az keyvault recover --subscription {SUBSCRIPTION ID} --hsm-name {HSM NAME} --location {LOCATION}
    ```


* Pour supprimer définitivement un module HSM supprimé de manière réversible :

    ```azurecli
    az keyvault purge --subscription {SUBSCRIPTION ID} --hsm-name {HSM NAME} --location {LOCATION}
    ```
    > [!WARNING] 
    > Cette opération supprimera définitivement votre module HSM.

* Pour activer la protection contre la suppression définitive sur un module HSM :

    ```azurecli
    az keyvault update-hsm --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} --hsm-name {HSM NAME} --enable-purge-protection true
    ```

### <a name="keys-cli"></a>Clés (CLI)

* Pour supprimer une clé :

    ```azurecli
    az keyvault key delete --subscription {SUBSCRIPTION ID} --hsm-name {HSM NAME} --name {KEY NAME}
    ```

* Pour lister les clés supprimées :

    ```azurecli
    az keyvault key list-deleted --subscription {SUBSCRIPTION ID} --hsm-name {HSM NAME}
    ```

* Pour récupérer une clé supprimée

    ```azurecli
    az keyvault key recover --subscription {SUBSCRIPTION ID} --hsm-name {HSM NAME} --name {KEY NAME}
    ```

* Pour supprimer définitivement une clé supprimée de manière réversible : 

    ```azurecli
    az keyvault key purge --subscription {SUBSCRIPTION ID} --hsm-name {HSM NAME} --name {KEY NAME}
    ```
    > [!WARNING] 
    > Cette opération supprimera définitivement votre clé.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

### <a name="managed-hsms-powershell"></a>Modules HSM managés (PowerShell)

* Pour vérifier l’état de la suppression réversible et de la protection contre la suppression définitive pour un module HSM managé :

    ```powershell
    Get-AzKeyVaultManagedHsm -Name "ContosoHSM"
    ```

* Pour supprimer un module HSM :

    ```powershell
    Remove-AzKeyVaultManagedHsm -Name 'ContosoHSM'
    ```
  Cette action est récupérable car la suppression réversible est activée par défaut.

### <a name="keys-powershell"></a>Clés (PowerShell)

* Pour supprimer une clé :

  ```powershell
  Remove-AzKeyVaultKey -HsmName ContosoHSM -Name 'MyKey'
  ```

* Pour lister toutes les clés supprimées : 

  ```powershell
  Get-AzKeyVaultKey -HsmName ContosoHSM -InRemovedState
  ```

* Pour récupérer une clé qui a été supprimée de façon réversible :

    ```powershell
    Undo-AzKeyVaultKeyRemoval -HsmName ContosoHSM -Name ContosoFirstKey
    ```

* Pour supprimer définitivement une clé supprimée de manière réversible :

    ```powershell
    Remove-AzKeyVaultKey -HsmName ContosoHSM -Name ContosoFirstKey -InRemovedState
    ```
    > [!WARNING] 
    > Cette opération supprimera définitivement votre clé.
    
---

## <a name="next-steps"></a>Étapes suivantes

- [Applets de commande PowerShell des modules HSM managés](/powershell/module/az.keyvault)
- [Commandes Azure CLI pour Key Vault](/cli/azure/keyvault)
- [Sauvegarde et restauration complètes des modules HSM managés](backup-restore.md)
- [Guide pratique pour activer la journalisation des modules HSM managés](logging.md)
