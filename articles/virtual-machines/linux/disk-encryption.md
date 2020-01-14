---
title: Chiffrement côté serveur de Disques managés Azure - Azure CLI
description: Le Stockage Azure protège vos données en les chiffrant au repos avant de les rendre persistantes dans des clusters de stockage. Vous pouvez compter sur des clés gérées par Microsoft pour le chiffrement de vos disques managés, ou bien utiliser des clés gérées par le client pour gérer le chiffrement avec vos propres clés.
author: roygara
ms.date: 12/13/2019
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.openlocfilehash: 4495b4489fd98df8dcba47548b64ddec2ad419d1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75462547"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Chiffrement côté serveur de Disques managés Azure

Les disques managés Azure chiffrent automatiquement vos données par défaut lors de leur conservation dans le cloud. Le chiffrement côté serveur protège vos données et vous aide à répondre aux engagements de votre entreprise en matière de sécurité et de conformité. Les données dans les disque managés Azure sont chiffrées en toute transparence à l’aide du [chiffrement AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 bits, un des chiffrements par blocs les plus puissants actuellement disponibles, et sont conformes à la norme FIPS 140-2.   

Le chiffrement n’a pas d’impact sur les performances des disques managés. Le chiffrement n’entraîne aucun coût supplémentaire.

Pour plus d’informations sur les modules cryptographiques des disque managés Azure, consultez [API de chiffrement : nouvelle génération](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

## <a name="about-encryption-key-management"></a>À propos de la gestion des clés de chiffrement

Vous pouvez vous appuyer sur les clés gérées par la plateforme pour le chiffrement de votre disque managé, ou vous pouvez gérer le chiffrement à l’aide de vos propres clés (préversion publique). Si vous choisissez de gérer le chiffrement avec vos propres clés, vous pouvez spécifier une *clé gérée par le client* à utiliser pour le chiffrement et le déchiffrement de toutes les données dans les disques managés. 

Les sections suivantes décrivent en détail chacune des options de gestion des clés.

## <a name="platform-managed-keys"></a>Clés gérées par la plateforme

Par défaut, les disques managés utilisent des clés de chiffrement gérées par la plateforme. Depuis le 10 juin 2017, l’ensemble des nouveaux disques managés, captures instantanées et images, ainsi que les nouvelles données écrites sur des disques managés existants, sont automatiquement chiffrés au repos à l’aide de clés gérées par des plateformes. 

## <a name="customer-managed-keys-public-preview"></a>Clés gérées par le client (préversion publique)

Vous pouvez choisir de gérer le chiffrement au niveau de chaque disque managé, avec vos propres clés. Le chiffrement côté serveur pour les disques managés avec des clés gérées par le client offre une expérience intégrée avec Azure Key Vault. Vous pouvez importer [vos clés RSA](../../key-vault/key-vault-hsm-protected-keys.md) vers votre Key Vault ou générer de nouvelles clés RSA dans Azure Key Vault. Les disques managés Azure gèrent le chiffrement et le déchiffrement de manière entièrement transparente à l’aide du [chiffrement d’enveloppe](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique). Ils chiffrent les données à l’aide d’une clé de chiffrement de données [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256, qui est à son tour protégée à l’aide de vos clés. Vous devez accorder l’accès aux disques managés dans votre Key Vault pour utiliser vos clés pour le chiffrement et le déchiffrement de la clé de chiffrement. Cela vous permet de contrôler intégralement vos données et vos clés. Vous pouvez désactiver vos clés ou révoquer l’accès aux disques managés à tout moment. Vous pouvez également auditer l’utilisation de la clé de chiffrement avec la surveillance Azure Key Vault pour vous assurer que seuls les disques managés ou d’autres services Azure approuvés accèdent à vos clés.

Le diagramme suivant montre comment les disques managés utilisent Azure Active Directory et Azure Key Vault pour effectuer des requêtes à l’aide de la clé gérée par le client :

![Flux de travail de clés gérées par le client pour disques managés](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


La liste suivante décrit le diagramme plus en détail :

1. Un administrateur Azure Key Vault crée des ressources de coffre de clés.
1. L’administrateur du coffre de clés importe ses clés RSA vers Key Vault ou génère de nouvelles clés RSA dans Key Vault.
1. Cet administrateur crée une instance de ressource de jeu de chiffrement de disque, en spécifiant un ID Azure Key Vault et une URL de clé. Le jeu de chiffrement de disque est une nouvelle ressource introduite pour simplifier la gestion des clés pour les disques managés. 
1. Lors de la création d’un jeu de chiffrement de disque, une [Identité managée affectée par le système](../../active-directory/managed-identities-azure-resources/overview.md) est créée dans Azure Active Directory (AD) et est associée au jeu de chiffrement de disque. 
1. L’administrateur du coffre de clés Azure accorde ensuite à l’identité managée l’autorisation d’effectuer des opérations dans le coffre de clés.
1. Un utilisateur de machine virtuelle crée des disques en les associant au jeu de chiffrement de disque. L’utilisateur de la machine virtuelle peut également activer le chiffrement côté serveur avec des clés gérées par le client pour les ressources existantes en les associant au jeu de chiffrement de disque. 
1. Les disques managés utilisent l’identité managée pour envoyer des demandes au Azure Key Vault.
1. Pour la lecture ou l’écriture de données, les disques managés envoient des demandes à Azure Key Vault pour chiffrer (envelopper) et déchiffrer (désenvelopper) la clé de chiffrement des données, afin d’effectuer le chiffrement et le déchiffrement des données. 

Pour révoquer l’accès aux clés managées par le client, consultez [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) et [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). La révocation de l’accès bloque efficacement l’accès à toutes les données dans le compte de stockage, car la clé de chiffrement n’est pas accessible au Stockage Azure.

### <a name="supported-scenarios-and-restrictions"></a>Scénarios pris en charge et restrictions

Durant la version préliminaire, seuls les scénarios suivants sont pris en charge :

- Créer une machine virtuelle à partir d’une image de la Place de marché Azure et chiffrer le disque du système d’exploitation avec le chiffrement côté serveur à l’aide de clés gérées par le client.
- Créer une image personnalisée chiffrée à l’aide du chiffrement côté serveur et des clés gérées par le client.
- Créer une machine virtuelle à partir d’une image personnalisée et chiffrer le disque du système d’exploitation à l’aide du chiffrement côté serveur et des clés gérées par le client.
- Créer des disques de données chiffrés à l’aide du chiffrement côté serveur et des clés gérées par le client.
- Créer des instantanés chiffrés à l’aide du chiffrement côté serveur et des clés gérées par le client.
- Créer des groupes de machines virtuelles identiques chiffrés à l’aide du chiffrement côté serveur et des clés gérées par le client.

La préversion présente également les restrictions suivantes :

- Disponible uniquement dans la région USA Centre-Ouest.
- Les disques créés à partir d’images personnalisées et chiffrées à l’aide du chiffrement côté serveur et des clés gérées par le client doivent être chiffrés à l’aide des mêmes clés gérées par le client et doivent figurer dans le même abonnement.
- Les instantanés créés à partir de disques chiffrés à l’aide du chiffrement côté serveur et des clés gérées par le client doivent être chiffrés avec les mêmes clés gérées par le client.
- Les images personnalisées chiffrées à l’aide du chiffrement côté serveur et des clés gérées par le client ne peuvent pas être utilisées dans la galerie d’images partagées.
- Votre Key Vault doit faire partie du même abonnement et de la même région que vos clés gérées par le client.
- Les disques, les instantanés et les images chiffrées avec des clés gérées par le client ne peuvent pas être déplacés vers un autre abonnement.

### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>Configuration de d’Azure Key Vault et de DiskEncryptionSet

1.  Créer une instance Azure Key Vault et une clé de chiffrement.

    Lorsque vous créez l’instance Key Vault, vous devez activer la suppression réversible et la protection de purge. La suppression réversible permet de s’assurer que Key Vault contient une clé supprimée pour une période de rétention donnée (90 jours par défaut). La protection de purge garantit qu’une clé supprimée ne peut pas être supprimée définitivement tant que la période de rétention n’est pas écoulée. Ces paramètres vous protègent contre la perte de données en raison d’une suppression accidentelle. Ces paramètres sont obligatoires lors de l’utilisation d’un coffre de clés Key Vault pour le chiffrement des disques managés.

    ```azurecli
    subscriptionId = <yourSubscriptionIDHere>
    rgName = <yourResourceGroupNameHere>
    location = <yourDesiredLocationHere>
    keyVaultName = <yourKeyVaultNameHere>
    keyName = <yourKeyNameHere>
    diskEncryptionSetName = <yourDiskEncryptionSetNameHere>
    diskName = <yourDiskNameHere>

    az account set -subscription $subscriptionId

    az keyvault create -n $keyVaultName -g $rgName -l $location --enable-purge-protection true --enable-soft-delete true

    az keyvault key create --vault-name $keyVaultName -n $keyName --protection software
    ```

1.  Créer une instance de DiskEncryptionSet. 
    
    ```azurecli
    keyVaultId = $(az keyvault show --name $keyVaultName --query [id] -o tsv)

    keyVaultKeyUrl = $(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)

    az group deployment create -g $rgName --template-uri "https://raw.githubusercontent.com/ramankumarlive/manageddiskscmkpreview/master/CreateDiskEncryptionSet.json" --parameters "diskEncryptionSetName = $diskEncryptionSetName" "keyVaultId = $keyVaultId" "keyVaultKeyUrl=$keyVaultKeyUrl" "region=$location"
    ```

1.  Accorder à la ressource DiskEncryptionSet l’accès au coffre de clés.

    ```azurecli
    desIdentity=$(az ad sp list --display-name $diskEncryptionSetName --query[].[objectId] -o tsv)

    az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get

    az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
    ```

### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Créer une machine virtuelle à l’aide d’une image marketplace, en chiffrant le système d’exploitation et les disques de données avec des clés gérées par le client

```azurecli
rgName="<yourResourceGroupName>"
vmName="<yourVMName>"
region="westcentralus"
password="<yourVMLocalAdminPassword>"
vmSize="Standard_DS3_V2"
diskEncryptionSetName="<yourDiskEncryptionSetName>"
templateURI="https://raw.githubusercontent.com/ramankumarlive/manageddiskscmkpreview/master/CreateVMWithDisksEncryptedWithCMK.json"

diskEncryptionSetId=$(az resource show -n $diskEncryptionSetName -g ssecmktesting --resource-type "Microsoft.Compute/diskEncryptionSets" --query [id] -o tsv)

az group deployment create -g $rgName --template-uri $templateURI --parameters "virtualMachineName=$vmName" "adminPassword=$password" "vmSize=$vmSize" "diskEncryptionSetId=$diskEncryptionSetId" "region=$region"

```

### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Créer un disque vide chiffré à l’aide du chiffrement côté serveur avec des clés gérées par le client et l’attacher à une machine virtuelle

```azurecli
vmName="<yourVMName>"
rgName="<yourResourceGroupName>"
diskName="<yourDiskName>"
diskSkuName="Premium_LRS"
diskSizeinGiB="30"
region="westcentralus"
diskLUN=2
diskEncryptionSetName="<yourDiskEncryptionSetName>"
templateURI="https://raw.githubusercontent.com/ramankumarlive/manageddiskscmkpreview/master/CreateEmptyDataDiskEncryptedWithSSECMK.json"

diskEncryptionSetId=$(az resource show -n $diskEncryptionSetName -g ssecmktesting --resource-type "Microsoft.Compute/diskEncryptionSets" --query [id] -o tsv)


az group deployment create -g $rgName --template-uri $templateURI --parameters  "diskName=$diskName" "diskSkuName=$diskSkuName" "dataDiskSizeInGb=$diskSizeinGiB" "diskEncryptionSetId=$diskEncryptionSetId" "region=$region"

diskId=$(az disk show -n $diskName -g $rgName --query [id] -o tsv)

az vm disk attach --vm-name $vmName --lun $diskLUN --ids $diskId

```

> [!IMPORTANT]
> Les clés managées par le client s’appuient sur des identités managées pour ressources Azure, une fonctionnalité d’Azure Active Directory (Azure AD). Quand vous configurez des clés gérées par le client, une identité managée est automatiquement affectée à vos ressources à l’arrière plan. Si, par la suite, vous déplacez l’abonnement, le groupe de ressources ou le disque managé d’un répertoire Azure AD à un autre, l’identité managée associée aux disques gérés n’est pas transférée vers le nouveau locataire, de sorte que les clés gérées par le client peuvent ne plus fonctionner. Pour plus d’informations, consultez [Transfert d’un abonnement entre des répertoires Azure AD](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Chiffrement côté serveur et chiffrement de disque Azure

[Azure Disk Encryption pour les machines virtuelles et les groupes de machines virtuelles identiques](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) utilise la fonctionnalité [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) de Windows et la fonctionnalité [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) de Linux pour chiffrer les disques managés avec des clés gérées par le client au sein de la machine virtuelle invitée.  Le chiffrement côté serveur avec des clés gérées par le client améliore l’utilisation de Azure Disk Encryption en vous permettant d’utiliser des types et des images de système d’exploitation pour vos machines virtuelles en chiffrant les données dans le service de stockage.

## <a name="next-steps"></a>Étapes suivantes

- [Découvrez les modèles Azure Resource Manager permettant de créer des disques chiffrés avec des clés gérées par le client](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Qu’est-ce qu’Azure Key Vault ?](../../key-vault/key-vault-overview.md)
