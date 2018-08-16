---
title: Chiffrement du service de stockage Azure à l’aide de clés gérées par le client dans Azure Key Vault | Microsoft Docs
description: La fonctionnalité Azure Storage Service Encryption permet de chiffrer Stockage Blob Azure et Azure Files côté service lors du stockage des données, et de le déchiffrer quand vous récupérez les données à l’aide de clés gérées par le client.
services: storage
author: lakasa
ms.service: storage
ms.topic: article
ms.date: 08/01/2018
ms.author: lakasa
ms.component: common
ms.openlocfilehash: 0e1ebd8868cfe5ef69a09219ffc82092fb85a4c8
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39527084"
---
# <a name="storage-service-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Chiffrement du service de stockage à l’aide de clés gérées par le client dans Azure Key Vault
Microsoft Azure s’engage à vous aider à protéger et préserver vos données pour répondre aux engagements de votre entreprise en matière de sécurité et de conformité. Une manière de protéger vos données avec la plateforme de stockage Azure consiste à utiliser Storage Service Encryption (SSE), qui chiffre automatiquement vos données lors de leur écriture dans le stockage et les déchiffre lors de leur récupération. Le chiffrement et le déchiffrement sont automatiques et transparents ; ils utilisent la technologie de [chiffrement AES](https://wikipedia.org/wiki/Advanced_Encryption_Standard) 256 bits, l’un des chiffrements par bloc les plus sécurisés qui soient.

Vous pouvez utiliser des clés de chiffrement gérées par Microsoft avec SSE ou utiliser vos propres clés de chiffrement. Cet article décrit comment utiliser vos propres clés de chiffrement. Pour plus d’informations sur l’utilisation de clés gérées par Microsoft, ou sur SSE en général, consultez [Storage Service Encryption pour les données au repos](storage-service-encryption.md).

SSE pour le stockage d’objets blob et Azure Files est intégré à Azure Key Vault afin que vous puissiez utiliser un coffre de clés pour gérer vos clés de chiffrement. Vous pouvez créer vos propres clés de chiffrement et les stocker dans un coffre de clés, ou utiliser les API d’Azure Key Vault pour générer des clés de chiffrement. Azure Key Vault vous permet de gérer et de contrôler vos clés, ainsi que d’auditer votre utilisation des clés.

> [!Note]  
> Storage Service Encryption avec des clés gérées par le client n’est pas disponible pour [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md). [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md) utilise les fonctionnalités standard [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) sur Windows et [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) sur Linux pour fournir une solution de chiffrement intégrée à KeyVault.

Pourquoi créer vos propres clés ? Les clés personnalisées vous permettent de gagner en flexibilité : vous pouvez créer, changer, désactiver et définir des contrôles d’accès. Les clés personnalisées vous permettent également d’effectuer un audit des clés de chiffrement utilisées pour protéger vos données.

## <a name="get-started-with-customer-managed-keys"></a>Prise en main des clés gérées par le client
Pour utiliser des clés gérées par le client avec SSE, vous pouvez créer un coffre de clés et une clé ou utiliser un coffre de clés et une clé existants. Le compte de stockage et le coffre de clés doivent se trouver dans la même région, mais ils peuvent appartenir à des abonnements différents. 

### <a name="step-1-create-a-storage-account"></a>Étape 1 : création d’un compte de stockage
Commencez par créer un compte de stockage si vous n’en avez pas encore. Pour plus d’informations, consultez la rubrique [Créer un compte de stockage](storage-quickstart-create-account.md).

### <a name="step-2-enable-sse-for-blob-and-file-storage"></a>Étape 2 : activer SSE pour le service de stockage d’objets blob et de fichiers
Pour activer SSE à l’aide de clés de gérée par le client, deux fonctionnalités de protection des clés, Suppression réversible et Ne pas vider, doivent également être activées. Ces paramètres empêchent la suppression accidentelle ou intentionnelle des clés. La période de rétention maximale des clés est définie à 90 jours, ce qui protège les utilisateurs contre les intervenants malveillants ou les rançongiciels.

Si vous souhaitez activer des clés pour SSE par programmation gérée par le client, vous pouvez utiliser [l’API REST du fournisseur de ressources de stockage Azure](https://docs.microsoft.com/rest/api/storagerp), la [Storage Resource Provider Client Library pour .NET](https://docs.microsoft.com/dotnet/api), [ Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) ou [Azure CLI](https://docs.microsoft.com/azure/storage/storage-azure-cli).

Pour utiliser des clés gérée par le client avec SSE, vous devez attribuer une identité de compte de stockage au compte de stockage. Vous pouvez définir l’identitié en exécutant la commande PowerShell suivante :

```powershell
Set-AzureRmStorageAccount -ResourceGroupName \$resourceGroup -Name \$accountName -AssignIdentity
```

Vous pouvez activer les fonctionnalités Suppression réversible et Ne pas vider en exécutant les commandes PowerShell suivantes :

```powershell
($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName
$vaultName).ResourceId).Properties | Add-Member -MemberType NoteProperty -Name
enableSoftDelete -Value 'True'

Set-AzureRmResource -resourceid $resource.ResourceId -Properties
$resource.Properties

($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName
$vaultName).ResourceId).Properties | Add-Member -MemberType NoteProperty -Name
enablePurgeProtection -Value 'True'

Set-AzureRmResource -resourceid $resource.ResourceId -Properties
$resource.Properties
```

### <a name="step-3-enable-encryption-with-customer-managed-keys"></a>Étape 3 : activer le chiffrement avec des clés gérées par le client
Par défaut, SSE utilise les clés gérées par Microsoft. Vous pouvez activer SSE avec les clés gérées par le client pour le compte de stockage dans le [portail Azure](https://portal.azure.com/). Sur le panneau **Paramètres** du compte de stockage, cliquez sur **Chiffrement**. Sélectionnez l’option **Utiliser votre propre clé**, comme indiqué dans l’illustration suivante.

![Capture d’écran du portail affichant l’option de chiffrement](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)

### <a name="step-4-select-your-key"></a>Étape 4 : sélectionner votre clé
Vous pouvez spécifier votre clé en tant qu’URI, ou en sélectionnant la clé dans un coffre de clés.

#### <a name="specify-a-key-as-a-uri"></a>Spécifier une clé en tant qu’URI
Pour spécifier votre clé à partir d’un URI, procédez comme suit :

1. Choisissez l’option **Entrer l’URI de la clé**.  
2. Dans le champ **Clé URI**, spécifiez l’URI.

    ![Capture d’écran du portail affichant l’option de chiffrement avec saisie de l’URI de la clé](./media/storage-service-encryption-customer-managed-keys/ssecmk2.png)


#### <a name="specify-a-key-from-a-key-vault"></a>Spécifiez une clé à partir d’un coffre de clés 
Pour spécifier votre clé à partir d’un coffre de clés, procédez comme suit :

1. Choisissez l’option **Sélectionner dans le coffre de clés**.  
2. Cliquez sur le coffre de clés contenant la clé que vous souhaitez utiliser.
3. Sélectionnez la clé dans le coffre de clés.

    ![Capture d’écran du portail affichant l’option de chiffrement Utiliser votre propre clé](./media/storage-service-encryption-customer-managed-keys/ssecmk3.png)

Si le compte de stockage n’a pas d’accès au coffre de clés, vous pouvez exécuter la commande Azure PowerShell indiquée dans l’image suivante pour accorder l’accès.

![Capture d’écran du portail affichant l’accès refusé au coffre de clés](./media/storage-service-encryption-customer-managed-keys/ssecmk4.png)

Vous pouvez également accorder l’accès par le biais du portail Azure en accédant à Azure Key Vault dans le portail Azure et en accordant l’accès au compte de stockage.


Vous pouvez associer la clé ci-dessus à un compte de stockage existant à l’aide des commandes PowerShell suivantes :
```powershell
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount"
$keyVault = Get-AzureRmKeyVault -VaultName "mykeyvault"
$key = Get-AzureKeyVaultKey -VaultName $keyVault.VaultName -Name "keytoencrypt"
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVault.VaultName -ObjectId $storageAccount.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
Set-AzureRmStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName -AccountName $storageAccount.StorageAccountName -EnableEncryptionService "Blob" -KeyvaultEncryption -KeyName $key.Name -KeyVersion $key.Version -KeyVaultUri $keyVault.VaultUri
```

### <a name="step-5-copy-data-to-storage-account"></a>Étape 5 : copier les données dans le compte de stockage
Pour transférer des données vers votre nouveau compte de stockage afin qu’il soit chiffré. Pour plus d’informations, voir [FAQ sur le chiffrement du service de stockage](storage-service-encryption.md#faq-for-storage-service-encryption).

### <a name="step-6-query-the-status-of-the-encrypted-data"></a>Étape 6 : interroger l’état des données chiffrées
Interroger l’état des données chiffrées.

## <a name="faq-for-sse-with-customer-managed-keys"></a>FAQ pour SSE avec clés gérées par le client
**J’utilise le stockage Premium. Puis-je utiliser des clés gérées par le client avec SSE ?**  
Oui, SSE avec des clés gérées par Microsoft ou par le client est pris en charge sur le stockage Standard et sur le stockage Premium.

**Puis-je créer des comptes de stockage dans lesquels SSE avec des clés gérées par le client est activé à l’aide d’Azure PowerShell et de l’interface de ligne de commande Azure ?**  
Oui.

**Quel est le coût supplémentaire du stockage Azure si j’utilise des clés gérées par le client avec SSE ?**  
Un coût spécifique est associé à l’utilisation d’Azure Key Vault. Pour plus d’informations, consultez [Tarification d’Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/). SSE est activé pour tous les comptes de stockage et ne génère aucun coût supplémentaire.

**Storage Service Encryption est-il disponible sur Azure Managed Disks ?**  
Storage Service Encryption est disponible pour Azure Managed Disks avec des clés gérées par Microsoft, mais pas avec des clés gérées par le client. À la place de Managed Disks prenant en charge SSE avec des clés gérées par le client, nous recommandons [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md), qui utilise les fonctionnalités standard [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) sur Windows et [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) sur Linux pour fournir un chiffrement intégré à KeyVault.

**En quoi Storage Service Encryption est-il différent d’Azure Disk Encryption ?**  
Azure Disk Encryption fournit une intégration entre les solutions basées sur le système d’exploitation, telles que BitLocker et DM-Crypt, et Azure Key Vault. Storage Service Encryption fournit un chiffrement en mode natif dans la couche de plateforme de stockage Azure, sous la machine virtuelle.

**Est-il possible de révoquer l’accès aux clés de chiffrement ?**
Oui, vous pouvez révoquer l’accès à tout moment. Il existe plusieurs façons de révoquer l’accès à vos clés. Reportez-vous aux pages [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) et [Interface de ligne de commande Azure Key Vault](https://docs.microsoft.com/cli/azure/keyvault) pour plus d’informations. La révocation de l’accès bloque efficacement l’accès à tous les objets blob dans le compte de stockage, car la clé de chiffrement du compte n’est pas accessible au stockage Azure.

**Puis-je créer un compte de stockage et une clé dans une autre région ?**  
Non, le compte de stockage, Azure Key Vault et la clé doivent être dans la même région.

**Puis-je activer des clés gérées par le client pour SSE lors de la création du compte de stockage ?**  
Non. Lorsque vous créez le compte de stockage, seules les clés gérées par Microsoft sont disponibles pour SSE. Pour utiliser des clés gérées par le client, vous devez mettre à jour les propriétés du compte de stockage. Vous pouvez utiliser REST ou l’une des bibliothèques clientes de stockage pour mettre à jour votre compte de stockage par programmation, ou mettre à jour les propriétés du compte de stockage à l’aide du portail Azure après la création du compte.

**Puis-je désactiver le chiffrement lorsque j’utilise des clés gérées par le client avec SSE ?**  
Non, vous ne pouvez pas désactiver le chiffrement. Le chiffrement est activé par défaut pour le stockage d’objets blob Azure, Azure Files, le stockage File d’attente Azure et le stockage de tables Azure. Vous pouvez éventuellement passer de l’utilisation de clés gérées par Microsoft à l’utilisation de clés gérées par le client, et vice versa.

**Est-ce que SSE est activé lorsque je crée un compte de stockage ?**  
SSE est activé pour tous les comptes de stockage et pour le stockage d’objets blob, Azure Files, le stockage File d’attente Azure et le stockage de tables Azure.

**Je ne parviens pas à activer SSE à l’aide de clés gérées par le client sur mon compte de stockage.**  
S’agit-il d’un compte de stockage Azure Resource Manager? Les comptes de stockage classiques ne sont pas pris en charge avec des clés gérées par le client. SSE avec des clés gérées par le client ne peut être activé que sur des comptes de stockage Resource Manager.

**Que signifient Suppression réversible et Ne pas vider ? Dois-je activer ce paramètre pour utiliser SSE avec des clés de gérées par le client ?**  
Suppression réversible et Ne pas vider doivent être activés pour utiliser SSE avec des clés gérées par le client. Ces paramètres empêchent la suppression accidentelle ou intentionnelle de votre clé. La période de rétention maximale des clés est définie à 90 jours, ce qui protège les utilisateurs contre les intervenants malveillants et les rançongiciels. Ce paramètre ne peut pas être désactivé.

**SSE avec des clés gérées par le client est-il uniquement autorisé dans des régions spécifiques ?**  
SSE avec des clés gérées par le client est actuellement disponible dans toutes les régions pour le stockage d’objets blob et Azure Files.

**Comment obtenir de l’aide si je rencontre des problèmes ou que je souhaite envoyer des commentaires ?**  
Contactez [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) pour les problèmes liés à Storage Service Encryption.

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur l’ensemble complet des fonctionnalités de sécurité qui aident les développeurs à créer des applications sécurisées, consultez le [Guide sur la sécurité du stockage](storage-security-guide.md).
- Pour plus d’informations générales sur Azure Key Vault, consultez [Qu’est-ce qu’Azure Key Vault ?](https://docs.microsoft.com/azure/key-vault/key-vault-whatis).
- Pour prendre en main Azure Key Vault, consultez [Prise en main d’Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).