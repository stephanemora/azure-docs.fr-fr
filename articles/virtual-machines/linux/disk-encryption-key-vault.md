---
title: Création et configuration d’un coffre de clés pour Azure Disk Encryption
description: Cet article décrit les étapes de création et de configuration d’un coffre de clés à utiliser avec Azure Disk Encryption.
ms.service: virtual-machines-linux
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: a818d9fe9707d1789fbe8e77489fc380fd2c92dd
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970622"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Création et configuration d’un coffre de clés pour Azure Disk Encryption

Azure Disk Encryption utilise Azure Key Vault pour contrôler et gérer les clés et les secrets de chiffrement de disque.  Pour plus d’informations sur les coffres de clés, consultez les articles [Prise en main du coffre de clés Azure](../../key-vault/key-vault-get-started.md) et [Sécuriser votre coffre de clés](../../key-vault/key-vault-secure-your-key-vault.md). 

> [!WARNING]
> - Si vous avez déjà utilisé Azure Disk Encryption avec Azure AD pour chiffrer une machine virtuelle, vous devez continuer à utiliser cette option pour chiffrer votre machine virtuelle. Pour plus d’informations, consultez [Création et configuration d’un coffre de clés pour Azure Disk Encryption avec Azure AD (version précédente)](disk-encryption-key-vault-aad.md).

La création et la configuration d’un coffre de clés à utiliser avec Azure Disk Encryption impliquent trois étapes :

1. Création d’un groupe de ressources, si nécessaire
2. Création d’un coffre de clés 
3. Définition de stratégies d’accès avancé au coffre de clés

Ces étapes sont illustrées dans les guides de démarrage rapide suivants :

- [Créer et chiffrer une machine virtuelle Linux avec Azure CLI](disk-encryption-cli-quickstart.md)
- [Créer et chiffrer une machine virtuelle Linux avec Azure PowerShell](disk-encryption-cli-quickstart.md)

Si vous le souhaitez, vous pouvez également générer ou importer une clé de chiffrement principale (KEK, key encryption key).

> [!Note]
> Les étapes de cet article sont automatisées dans le [script d’interface CLI des prérequis Azure Disk Encryption](https://github.com/ejarvi/ade-cli-getting-started) et le [script PowerShell des prérequis Azure Disk Encryption](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).

## <a name="install-tools-and-connect-to-azure"></a>Installer les outils et se connecter à Azure

Les étapes décrites dans cet article peuvent être effectuées avec [Azure CLI](/cli/azure/), le [module Az Azure PowerShell](/powershell/azure/overview) ou le [portail Azure](https://portal.azure.com). 

Le portail est accessible par le biais de votre navigateur, alors qu’Azure CLI et Azure PowerShell nécessitent une installation locale. Pour plus d’informations, consultez [Azure Disk Encryption pour Linux : Installer les outils](disk-encryption-linux.md#install-tools-and-connect-to-azure).

### <a name="connect-to-your-azure-account"></a>Se connecter au compte Azure

Avant d’utiliser Azure CLI ou Azure PowerShell, vous devez vous connecter à votre abonnement Azure. Pour cela, vous devez [vous connecter avec Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), [vous connecter avec Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0) ou fournir vos informations d’identification dans le portail Azure quand vous y êtes invité.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

*Si vous disposez déjà d’un groupe de ressources, vous pouvez passer à [Créer un coffre de clés](#create-a-key-vault).*

Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. 

Créez un groupe de ressources à l’aide de la commande Azure CLI [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create), de la commande Azure PowerShell [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) ou du [portail Azure](https://portal.azure.com).

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az group create --name "myResourceGroup" --location eastus
```
### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>Création d’un coffre de clés

*Si vous disposez déjà d’un coffre de clés, vous pouvez passer à [Définir des stratégies d’accès avancé au coffre de clés](#set-key-vault-advanced-access-policies).*

Créez un coffre de clés à l’aide de la commande Azure CLI [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create), de la commande Azure PowerShell [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault), du [portail Azure](https://portal.azure.com) ou d’un [modèle Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

>[!WARNING]
> Pour garantir que les secrets de chiffrement ne franchissent pas les limites régionales, Azure Disk Encryption exige que le coffre de clés se trouve dans la même région que les machines virtuelles. Créez et utilisez un coffre de clés situé dans la même région que les machines virtuelles à chiffrer. 

Chaque coffre de clés doit avoir un nom unique. Remplacez <your-unique-keyvault-name> par le nom de votre coffre de clés dans les exemples suivants.

### <a name="azure-cli"></a>Azure CLI

Quand vous créez un coffre de clés à l’aide d’Azure CLI, ajoutez l’indicateur « --enabled-for-disk-encryption ».

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

Quand vous créez un coffre de clés à l’aide d’Azure PowerShell, ajoutez l’indicateur « -EnabledForDiskEncryption ».

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Modèle Resource Manager

Vous pouvez aussi créer un coffre de clés à l’aide du [modèle Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. Dans le modèle de démarrage rapide Azure, cliquez sur **Déployer sur Azure**.
2. Sélectionnez l’abonnement, le groupe de ressources, l’emplacement du groupe de ressources, le nom du coffre de clés, l’ID d’objet, les conditions juridiques et le contrat, puis cliquez sur **Acheter**. 


##  <a name="set-key-vault-advanced-access-policies"></a>Définir des stratégies d’accès avancé au coffre de clés

La plateforme Azure doit avoir accès aux clés et aux clés secrètes de chiffrement dans votre coffre de clés afin de les mettre à disposition de la machine virtuelle pour lancer et déchiffrer les volumes. 

Si vous n’avez pas activé votre coffre de clés pour le chiffrement de disque, le déploiement ou le déploiement de modèle au moment de la création (comme indiqué à l’étape précédente), vous devez mettre à jour ses stratégies d’accès avancé.  

### <a name="azure-cli"></a>Azure CLI

Utilisez la commande [az keyvault update](/cli/azure/keyvault#az-keyvault-update) afin d’activer le chiffrement de disque pour le coffre de clés. 

 - **Activer Key Vault pour le chiffrement de disque :** Enabled-for-disk-encryption est requis. 

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Activer Key Vault pour le déploiement, si nécessaire :** autorise le fournisseur de ressources Microsoft.Compute à récupérer des secrets à partir de ce coffre de clés lorsque ce dernier est référencé dans le cadre de la création de ressources, par exemple lors de la création d’une machine virtuelle.

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Activer Key Vault pour le déploiement d’un modèle, si nécessaire :** autoriser Resource Manager à récupérer des secrets dans le coffre.
     ```azurecli-interactive  
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-template-deployment "true"
     ```

###  <a name="azure-powershell"></a>Azure PowerShell
 Utilisez l’applet de commande PowerShell de coffre de clés [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) pour activer le chiffrement de disque pour le coffre de clés.

  - **Activer Key Vault pour le chiffrement de disque :** EnabledForDiskEncryption est requis pour Azure Disk Encryption.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDiskEncryption
     ```

  - **Activer Key Vault pour le déploiement, si nécessaire :** autorise le fournisseur de ressources Microsoft.Compute à récupérer des secrets à partir de ce coffre de clés lorsque ce dernier est référencé dans le cadre de la création de ressources, par exemple lors de la création d’une machine virtuelle.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDeployment
     ```

  - **Activer Key Vault pour le déploiement d’un modèle, si nécessaire :** autorise Azure Resource Manager à obtenir des secrets à partir de ce coffre de clés lorsque ce dernier est référencé dans un déploiement de modèle.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForTemplateDeployment
     ```

### <a name="azure-portal"></a>Portail Azure

1. Sélectionnez votre coffre de clés, accédez à **Stratégies d’accès**, puis sélectionnez **Cliquez ici pour afficher les stratégies d’accès avancé**.
2. Cochez la case **Activer l’accès à Azure Disk Encryption pour chiffrer des volumes**.
3. Sélectionnez **Activer l’accès aux machines virtuelles Azure pour le déploiement** et/ou **Activer l’accès à Azure Resource Manager pour le déploiement de modèles** si nécessaire. 
4. Cliquez sur **Enregistrer**.

    ![Stratégies d’accès avancé au coffre de clés Azure](./media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>Configurer une clé de chiffrement principale (KEK)

Si vous souhaitez utiliser une clé de chiffrement à clé pour renforcer la protection des clés de chiffrement, ajoutez une clé de chiffrement à clé à votre coffre de clés. Quand une clé de chiffrement principale est spécifiée, Azure Disk Encryption utilise cette clé pour wrapper les secrets de chiffrement avant d’écrire dans Key Vault.

Vous pouvez générer une nouvelle clé de chiffrement principale (KEK, key encryption key) à l’aide de la commande Azure CLI [az keyvault key create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create), de la cmdlet Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) ou du [portail Azure](https://portal.azure.com/). Vous devez générer un type de clé RSA. Azure Disk Encryption ne prend pas encore en charge l’utilisation de clés à courbe elliptique.

Vous pouvez au lieu de cela importer une clé KEK à partir de votre HSM de gestion des clés locales. Pour plus d’informations, consultez la [documentation concernant Key Vault](../../key-vault/key-vault-hsm-protected-keys.md). 

Les URL des clés KEK de votre coffre de clés doivent être versionnées. Azure met en vigueur cette restriction de gestion de version. Voici des exemples d’URL de clé secrète et de clé de chiffrement à clé valides :

* Exemple d’URL de secret valide : *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* Exemple d’URL de clé KEK valide : *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

Azure Disk Encryption ne prend pas en charge l’intégration de numéros de port aux secrets de coffre de clés et aux URL KEK. Voici des exemples d’URL de coffre de clés valides et non valides :

  * URL de coffre de clés acceptable : *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * URL de coffre de clés inacceptable : *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>Azure CLI

Utilisez la commande Azure CLI [az keyvault key create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) pour générer une nouvelle clé KEK et la stocker dans votre coffre de clés.

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA-HSM
```

Au lieu de cela, vous pouvez importer une clé privée à l’aide de la commande Azure CLI [az keyvault key import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) :

Dans les deux cas, vous devez fournir le nom de votre clé KEK au paramètre --key-encryption-key de la commande Azure CLI [az vm encryption enable](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-enable). 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure PowerShell 

Utilisez la cmdlet Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey?view=azps-2.5.0) pour générer une nouvelle clé KEK et la stocker dans votre coffre de clés.

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

Au lieu de cela, vous pouvez importer une clé privée à l’aide de la commande Azure PowerShell [az keyvault key import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import).

Dans les deux cas, vous devez fournir l’ID de votre coffre de clés KEK et l’URL de votre clé KEK aux paramètres -KeyEncryptionKeyVaultId et -KeyEncryptionKeyUrl de la commande Azure PowerShell [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.5.0). Notez que cet exemple part du principe que vous utilisez le même coffre de clés pour la clé de chiffrement de disque et la clé KEK.

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```
 
## <a name="next-steps"></a>Étapes suivantes

- [Script d’interface CLI des prérequis Azure Disk Encryption](https://github.com/ejarvi/ade-cli-getting-started)
- [Script PowerShell des prérequis Azure Disk Encryption](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- En savoir plus sur les [scénarios Azure Disk Encryption sur les machines virtuelles Linux](disk-encryption-linux.md)
- En savoir plus sur la [résolution des problèmes liés à Azure Disk Encryption](disk-encryption-troubleshooting.md)
- Lire les [exemples de scripts Azure Disk Encryption](disk-encryption-sample-scripts.md)
