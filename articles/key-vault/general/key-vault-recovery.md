---
title: Présentation de la récupération Azure Key Vault | Microsoft Docs
description: Les fonctionnalités de récupération Key Vault sont conçues pour empêcher la suppression accidentelle ou malveillante de votre coffre de clés et des secrets, clés et certificats qui y sont stockés.
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.author: mbaldwin
author: msmbaldwin
manager: rkarlin
ms.date: 09/30/2020
ms.openlocfilehash: a9a783333d8edd40556b7744eb7cf7dfb52953a7
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97933980"
---
# <a name="azure-key-vault-recovery-management-with-soft-delete-and-purge-protection"></a>Gestion de la récupération d’Azure Key Vault avec suppression réversible et protection contre la suppression définitive

Cet article traite de deux fonctionnalités de récupération d’Azure Key Vault : la suppression réversible et de la protection contre le vidage. Ce document fournit une vue d’ensemble de ces fonctionnalités et vous montre comment les gérer via le portail Azure, Azure CLI et Azure PowerShell.

Pour plus d’informations sur Key Vault, consultez
- [Vue d'ensemble de Key Vault](overview.md)
- [Vue d’ensemble des clés, secrets et certificats Azure Key Vault](about-keys-secrets-certificates.md)

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/dotnet)
* [Module PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).
* [Azure CLI](/cli/azure/install-azure-cli)
* Un coffre de clés. Vous pouvez en créer un en utilisant le [portail Azure](../general/quick-create-portal.md), [Azure CLI](../general/quick-create-cli.md) ou [Azure PowerShell](../general/quick-create-powershell.md).

## <a name="what-are-soft-delete-and-purge-protection"></a>Définition de la suppression réversible et la protection contre le vidage

La [suppression réversible](soft-delete-overview.md) et la protection contre le vidage sont deux fonctionnalités différentes de récupération de coffre de clés.

> [!IMPORTANT]
> L’activation de la suppression réversible est essentielle pour que vos coffres de clés et vos informations d’identification soient protégés contre les suppressions accidentelles. Toutefois, l’activation de la suppression réversible est considérée comme un changement cassant, car elle peut vous obliger à changer la logique de votre application ou à fournir des autorisations supplémentaires à vos principaux de service. Avant d’activer la suppression réversible à l’aide des instructions ci-dessous, vérifiez que votre application est compatible avec le changement à l’aide de [**ce document** .](soft-delete-change.md)

**La suppression réversible** est conçue pour empêcher la suppression accidentelle de votre coffre de clés et de vos clés, secrets et certificats qui y sont stockés. Pensez à la suppression réversible comme à une corbeille. Lorsque vous supprimez un coffre de clés ou un objet de coffre de clés, il reste récupérable pendant une période de rétention configurable par l’utilisateur ou une durée par défaut de 90 jours. Les coffres de clés supprimés de manière réversible peuvent également être **vidés**, ce qui signifie qu’ils sont supprimés définitivement. Cela vous permet de recréer des coffres de clés et des objets de coffre de clés portant le même nom. La récupération et la suppression des coffres de clés et des objets nécessitent des autorisations d’accès élevées. **Une fois la suppression réversible activée, elle ne peut pas être désactivée.**

Il est important de noter que **les noms des coffres de clés sont généralement uniques**. Vous ne pourrez donc pas créer un coffre de clés portant le même nom qu’un coffre de clés supprimé de manière réversible. De même, les noms des clés, des secrets et des certificats sont uniques au sein d’un coffre de clés. Vous ne pouvez pas créer un secret, une clé ou un certificat portant le même nom qu’un autre supprimé de manière réversible.

**La protection contre le vidage** est conçue pour empêcher la suppression de votre coffre de clés et de vos clés, secrets et certificats par une personne interne malveillante. Considérez-la comme une corbeille dotée d’un verrou basé sur la durée. Vous pouvez récupérer des éléments à tout moment pendant la période de rétention configurable. **Vous ne pouvez pas supprimer définitivement ou vider un coffre de clés tant que la période de rétention n’est pas écoulée.** Une fois la période de rétention écoulée, le coffre de clés ou l’objet de coffre de clés est vidé automatiquement.

> [!NOTE]
> La protection contre le vidage est conçue de sorte qu’aucun rôle ni aucune autorisation d’administrateur ne peut remplacer, désactiver ou déjouer la protection contre le vidage. **Une fois la protection contre le vidage activée, elle ne peut pas être désactivée ni remplacée par quiconque, y compris Microsoft.** Cela signifie que vous devez récupérer un coffre de clés supprimé ou attendre la fin de la période de rétention avant de réutiliser le nom du coffre de clés.

Pour plus d’informations sur la suppression réversible, consultez [Vue d’ensemble de la suppression réversible d’Azure Key Vault](soft-delete-overview.md).

# <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

## <a name="verify-if-soft-delete-is-enabled-on-a-key-vault-and-enable-soft-delete"></a>Vérifier si la suppression réversible est activée sur un coffre de clés et activer la suppression réversible

1. Connectez-vous au portail Azure.
1. Sélectionnez votre coffre de clés.
1. Cliquez sur le panneau « Propriétés ».
1. Vérifiez que la case d’option située à côté de la suppression réversible est définie sur « Activer la récupération ».
1. Si la suppression réversible n’est pas activée sur le coffre de clés, cliquez sur la case d’option pour activer la suppression réversible, puis cliquez sur « Enregistrer ».

:::image type="content" source="../media/key-vault-recovery-1.png" alt-text="Dans Propriétés, l’option de suppression réversible est mise en surbrillance, tout comme la valeur pour l’activer.":::

## <a name="grant-access-to-a-service-principal-to-purge-and-recover-deleted-secrets"></a>Accorder l’accès à un principal de service pour vider et récupérer les secrets supprimés

1. Connectez-vous au portail Azure.
1. Sélectionnez votre coffre de clés.
1. Cliquez sur le panneau « Stratégie d’accès ».
1. Dans le tableau, recherchez la ligne du principal de sécurité auquel vous souhaitez accorder l’accès (ou ajoutez un nouveau principal de sécurité).
1. Cliquez sur la liste déroulante des clés, certificats et secrets.
1. Faites défiler vers le bas de la liste déroulante, puis cliquez sur « Récupérer » et « Vider ».
1. Les principaux de sécurité auront également besoin d’une fonctionnalité d’extraction et de liste pour effectuer la plupart des opérations.

:::image type="content" source="../media/key-vault-recovery-2.png" alt-text="Dans le volet de navigation de gauche, le panneau Stratégies d’accès est mis en surbrillance. Dans Stratégies d’accès, la liste déroulante Positions secrètes s’affiche, et quatre éléments sont sélectionnés : Obtenir, Répertorier, Récupérer et Vider.":::

## <a name="list-recover-or-purge-a-soft-deleted-key-vault"></a>Répertorier, récupérer ou vider un coffre de clés supprimé de manière réversible

1. Connectez-vous au portail Azure.
1. Cliquez sur la barre de recherche en haut de la page.
1. Sous « Services récents », cliquez sur « Key Vault ». Ne cliquez pas sur un coffre de clés individuel.
1. En haut de l’écran, cliquez sur l’option « Gérer les coffres supprimés ».
1. Un volet contextuel s’ouvre sur le côté droit de l’écran.
1. Sélectionnez votre abonnement.
1. Si votre coffre de clés a été supprimé de manière réversible, il apparaît dans le volet contextuel à droite.
1. S’il y a trop de coffres, vous pouvez soit cliquer sur « Charger plus » en bas du volet contextuel, soit utiliser l’interface CLI ou PowerShell pour extraire les résultats.
1. Une fois que vous avez trouvé le coffre que vous souhaitez récupérer ou vider, cochez la case à côté de celui-ci.
1. Sélectionnez l’option Récupérer en bas du volet contextuel si vous souhaitez récupérer le coffre de clés.
1. Sélectionnez l’option Vider si vous souhaitez supprimer définitivement le coffre de clés.

:::image type="content" source="../media/key-vault-recovery-3.png" alt-text="Dans les coffres de clés, l’option Gérer les coffres supprimés est mise en surbrillance.":::

:::image type="content" source="../media/key-vault-recovery-4.png" alt-text="Dans le volet Gérer les coffres de clés supprimés, le seul coffre de clés répertorié est mis en surbrillance et sélectionné, et le bouton Récupérer est mis en surbrillance.":::

## <a name="list-recover-or-purge-soft-deleted-secrets-keys-and-certificates"></a>Répertorier, récupérer ou vider les secrets, les clés et les certificats supprimés de manière réversible

1. Connectez-vous au portail Azure.
1. Sélectionnez votre coffre de clés.
1. Sélectionnez le panneau correspondant au type de secret que vous souhaitez gérer (clés, secrets ou certificats).
1. En haut de l’écran, cliquez sur « Gérer les clés/secrets/certificats supprimés ».
1. Un volet contextuel s’affiche sur le côté droit de l’écran.
1. Si votre secret, clé ou certificat ne figure pas dans la liste, il n’a pas été supprimé de manière réversible.
1. Sélectionnez le secret, la clé ou le certificat que vous souhaitez gérer.
1. Sélectionnez l’option de récupération ou de vidage en bas du volet contextuel.

:::image type="content" source="../media/key-vault-recovery-5.png" alt-text="Dans le panneau Clés, l’option Gérer les clés supprimées est mise en surbrillance.":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="key-vault-cli"></a>Coffre de clés (CLI)

* Vérifier si la suppression réversible est activée pour un coffre de clés

    ```azurecli
    az keyvault show --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* Activer la suppression réversible sur un coffre de clés

    La suppression réversible est activée par défaut pour tous les nouveaux coffres de clés. Si vous disposez actuellement d’un coffre de clés pour lequel la suppression réversible n’est pas activée, utilisez la commande suivante pour l’activer.

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-soft-delete true
    ```

* Supprimer un coffre de clés (récupérable si la suppression réversible est activée)

    ```azurecli
    az keyvault delete --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* Répertorier tous les coffres de clés supprimés de manière réversible

    ```azurecli
    az keyvault list-deleted --subscription {SUBSCRIPTION ID} --resource-type vault
    ```

* Récupérer un coffre de clés supprimé de manière réversible

    ```azurecli
    az keyvault recover --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* Vider un coffre de clés supprimé de manière réversible **(AVERTISSEMENT : CETTE OPÉRATION SUPPRIMERA DÉFINITIVEMENT VOTRE COFFRE DE CLÉS)**

    ```azurecli
    az keyvault purge --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* Activer la protection contre le vidage sur un coffre de clés

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-purge-protection true
    ```

## <a name="certificates-cli"></a>Certificats (CLI)

* Accorder l’accès pour vider et récupérer des certificats

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --certificate-permissions recover purge
    ```

* Suppression d'un certificat

    ```azurecli
    az keyvault certificate delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* Répertorier les certificats supprimés

    ```azurecli
    az keyvault certificate list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Récupérer un certificat supprimé

    ```azurecli
    az keyvault certificate recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* Vider un certificat supprimé de manière réversible **(AVERTISSEMENT : CETTE OPÉRATION SUPPRIMERA DÉFINITIVEMENT VOTRE CERTIFICAT)**

    ```azurecli
    az keyvault certificate purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

## <a name="keys-cli"></a>Clés (CLI)

* Accorder l’accès pour vider et récupérer des clés

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --key-permissions recover purge
    ```

* Touche Supprimer

    ```azurecli
    az keyvault key delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* Lister les clés supprimées

    ```azurecli
    az keyvault key list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Récupérer une clé supprimée

    ```azurecli
    az keyvault key recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* Vider une clé supprimée de manière réversible **(AVERTISSEMENT : CETTE OPÉRATION SUPPRIMERA DÉFINITIVEMENT VOTRE CLÉ)**

    ```azurecli
    az keyvault key purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

## <a name="secrets-cli"></a>Secrets (CLI)

* Accorder l’accès pour vider et récupérer des secrets

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --secret-permissions recover purge
    ```

* Supprimer un secret

    ```azurecli
    az keyvault secret delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* Répertorier les secrets supprimés

    ```azurecli
    az keyvault secret list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Récupérer un secret supprimé

    ```azurecli
    az keyvault secret recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* Vider un secret supprimé de manière réversible **(AVERTISSEMENT : CETTE OPÉRATION SUPPRIMERA DÉFINITIVEMENT VOTRE SECRET)**

    ```azurecli
    az keyvault secret purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

## <a name="key-vault-powershell"></a>Coffre de clés (PowerShell)

* Vérifier si la suppression réversible est activée pour un coffre de clés

    ```powershell
    Get-AzKeyVault -VaultName "ContosoVault"
    ```

* Supprimer un coffre de clés

    ```powershell
    Remove-AzKeyVault -VaultName 'ContosoVault'
    ```

* Répertorier tous les coffres de clés supprimés de manière réversible

    ```powershell
    Get-AzKeyVault -InRemovedState
    ```

* Récupérer un coffre de clés supprimé de manière réversible

    ```powershell
    Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
    ```

* Vider un coffre de clés supprimé de manière réversible **(AVERTISSEMENT : CETTE OPÉRATION SUPPRIMERA DÉFINITIVEMENT VOTRE COFFRE DE CLÉS)**

    ```powershell
    Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
    ```

* Activer la protection contre le vidage sur un coffre de clés

    ```powershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

## <a name="certificates-powershell"></a>Certificats (PowerShell)

* Accorder des autorisations pour récupérer et vider les certificats

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToCertificates recover,purge
    ```

* Supprimer un certificat

  ```powershell
  Remove-AzKeyVaultCertificate -VaultName ContosoVault -Name 'MyCert'
  ```

* Répertorier tous les certificats supprimés dans un coffre de clés

  ```powershell
  Get-AzKeyVaultCertificate -VaultName ContosoVault -InRemovedState
  ```

* Récupérer un certificat supprimé

  ```powershell
  Undo-AzKeyVaultCertificateRemoval -VaultName ContosoVault -Name 'MyCert'
  ```

* Vider un certificat supprimé de manière réversible **(AVERTISSEMENT : CETTE OPÉRATION SUPPRIMERA DÉFINITIVEMENT VOTRE CERTIFICAT)**

  ```powershell
  Remove-AzKeyVaultcertificate -VaultName ContosoVault -Name 'MyCert' -InRemovedState
  ```

## <a name="keys-powershell"></a>Clés (PowerShell)

* Accorder des autorisations pour récupérer et vider les clés

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys recover,purge
    ```

* Supprimer une clé

  ```powershell
  Remove-AzKeyVaultKey -VaultName ContosoVault -Name 'MyKey'
  ```

* Répertorier tous les certificats supprimés dans un coffre de clés

  ```powershell
  Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
  ```

* Récupérer une clé supprimée de manière réversible

    ```powershell
    Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
    ```

* Vider une clé supprimée de manière réversible **(AVERTISSEMENT : CETTE OPÉRATION SUPPRIMERA DÉFINITIVEMENT VOTRE CLÉ)**

    ```powershell
    Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
    ```

## <a name="secrets-powershell"></a>Secrets (PowerShell)

* Accorder des autorisations pour récupérer et vider les secrets

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToSecrets recover,purge
    ```

* Supprimer un secret nommé SQLPassword

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

* Répertorier tous les secrets supprimés dans un coffre de clés

  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

* Récupérer un secret supprimé

  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

* Vider un secret supprimé **(AVERTISSEMENT : CETTE OPÉRATION SUPPRIMERA DÉFINITIVEMENT VOTRE CLÉ)**

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```
---

## <a name="next-steps"></a>Étapes suivantes

- [Applets de commande PowerShell d’Azure Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault)
- [Commandes Azure CLI pour Key Vault](https://docs.microsoft.com/cli/azure/keyvault)
- [Sauvegarde Azure Key Vault](backup.md)
- [Guide pratique pour activer la journalisation de Key Vault](howto-logging.md)
- [Sécuriser l’accès à un coffre de clés](secure-your-key-vault.md)
- [Guide du développeur Azure Key Vault](developers-guide.md)
- [Bonnes pratiques pour utiliser un coffre de clés](security-overview.md)
