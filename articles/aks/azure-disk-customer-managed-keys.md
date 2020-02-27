---
title: Utiliser une clé gérée par le client pour chiffrer les disques Azure dans Azure Kubernetes Service (AKS)
description: BYOK (Bring Your Own Keys) pour chiffrer les disques de données et de système d’exploitation AKS.
services: container-service
ms.topic: article
ms.date: 01/12/2020
ms.openlocfilehash: bb6ba5e6dd4ace9e33043079c0f435c10baf5cb2
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77596502"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>BYOK (Bring Your Own Keys) avec des disques Azure dans Azure Kubernetes Service (AKS)

Le stockage Azure chiffre toutes les données dans un compte de stockage au repos. Par défaut, les données sont chiffrées avec des clés managées par Microsoft Pour renforcer le contrôle sur les clés de chiffrement, vous pouvez fournir des [clés managées par le client][customer-managed-keys] à utiliser pour le chiffrement au repos des disques de système d’exploitation et des disques de données pour vos clusters AKS.

> [!NOTE]
> Les clusters AKS Windows et Linux BYOK sont disponibles dans les [régions Azure][supported-regions] qui prennent en charge le chiffrement côté serveur des disques managés Azure.

## <a name="before-you-begin"></a>Avant de commencer

* Cet article suppose que vous créez un *cluster AKS*.

* Vous devez activer la suppression réversible et la protection contre le vidage pour *Azure Key Vault* lors de l’utilisation de Key Vault pour chiffrer des disques managés.

* Vous devez disposer d’Azure CLI version 2.0.79 ou ultérieure et de l’extension aks-preview 0.4.26

> [!IMPORTANT]
> Les fonctionnalités d’évaluation AKS sont en libre-service et font l’objet d’un abonnement. Les versions préliminaires sont fournies « en l’état », « avec toutes les erreurs » et « en fonction des disponibilités », et sont exclues des contrats de niveau de service (sla) et de la garantie limitée. Les versions préliminaires AKS sont partiellement couvertes par le service clientèle sur la base du meilleur effort. En tant que tel, ces fonctionnalités ne sont pas destinées à une utilisation en production. Pour obtenir des informations supplémentaires, veuillez lire les articles de support suivants :
>
> * [Stratégies de support AKS](support-policies.md)
> * [FAQ du support Azure](faq.md)

## <a name="install-latest-aks-cli-preview-extension"></a>Installer la dernière extension de la préversion d’AKS CLI

Pour utiliser des clés gérées par le client, vous devez disposer de l’extension CLI *aks-preview* version 0.4.26 ou ultérieure. Installez l’extension Azure CLI *aks-preview* à l’aide de la commande [az extension add][az-extension-add], puis recherchez toutes les mises à jour disponibles à l’aide de la commande [az extension update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-azure-key-vault-instance"></a>Créer une instance Azure Key Vault

Utilisez une instance Azure Key Vault pour stocker vos clés.  Vous pouvez éventuellement utiliser le portail Azure pour [configurer des clés gérées par le client avec Azure Key Vault][byok-azure-portal]

Créez un *groupe de ressources*, puis une instance *Key Vault*. Activez ensuite la suppression réversible et la protection contre le vidage.  Veillez à utiliser les mêmes noms de régions et de groupes de ressources pour chaque commande.

```azurecli-interactive
# Optionally retrieve Azure region short names for use on upcoming commands
az account list-locations
```

```azurecli-interactive
# Create new resource group in a supported Azure region
az group create -l myAzureRegionName -n myResourceGroup

# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n myKeyVaultName -g myResourceGroup -l myAzureRegionName  --enable-purge-protection true --enable-soft-delete true
```

## <a name="create-an-instance-of-a-diskencryptionset"></a>Créer une instance de DiskEncryptionSet

Remplacez *myKeyVaultName* par le nom de votre coffre de clés.  Vous devez également disposer d’une *clé* stockée dans Azure Key Vault pour effectuer les étapes suivantes.  Stockez votre clé existante dans le coffre de clés que vous avez créé au cours des étapes précédentes, ou [générez une nouvelle clé][key-vault-generate] et remplacez *myKeyName* ci-dessous par son nom.
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query [id] -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query [key.kid] -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>Accorder à DiskEncryptionSet l’accès au coffre de clés

Utilisez DiskEncryptionSet et les groupes de ressources que vous avez créés lors des étapes précédentes, puis accordez à la ressource DiskEncryptionSet l’accès au coffre de clés Azure.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query [identity.principalId] -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get

# Assign the reader role
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>Créer un cluster AKS et chiffrer le disque de système d’exploitation

Créez un **groupe de ressources** et un cluster AKS, puis utilisez votre clé pour chiffrer le disque de système d’exploitation. Les clés gérées par le client sont prises en charge uniquement dans les versions de Kubernetes postérieures à la version 1.17. 

> [!IMPORTANT]
> Veillez à créer un nouveau groupe de ressources pour votre cluster AKS

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az resource show -n mydiskEncryptionSetName -g myResourceGroup --resource-type "Microsoft.Compute/diskEncryptionSets" --query [id] -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version 1.17.0 --generate-ssh-keys
```

Quand de nouveaux pools de nœuds sont ajoutés au cluster créé ci-dessus, la clé gérée par le client fournie pendant l’opération de création est utilisée pour chiffrer le disque de système d’exploitation.

## <a name="encrypt-your-aks-cluster-data-disk"></a>Chiffrer votre disque de données de cluster AKS

Vous pouvez également chiffrer les disques de données AKS avec vos propres clés.

> [!IMPORTANT]
> Vérifiez que vous disposez des informations d’identification AKS appropriées. Le principal du service doit disposer d’un accès de contributeur au groupe de ressources où diskencryptionset est déployé. Sinon, vous obtenez une erreur suggérant que le principal du service ne dispose pas d’autorisations.

```azurecli-interactive
# Retrieve your Azure Subscription Id from id property as shown below
az account list
```

```
someuser@Azure:~$ az account list
[
  {
    "cloudName": "AzureCloud",
    "id": "666e66d8-1e43-4136-be25-f25bb5de5893",
    "isDefault": true,
    "name": "MyAzureSubscription",
    "state": "Enabled",
    "tenantId": "3ebbdf90-2069-4529-a1ab-7bdcb24df7cd",
    "user": {
      "cloudShellID": true,
      "name": "someuser@azure.com",
      "type": "user"
    }
  }
]
```

Créez un fichier appelé **byok-azure-disk.yaml** et contenant les informations suivantes.  Remplacez myAzureSubscriptionId, myResourceGroup et myDiskEncrptionSetName par vos valeurs, puis appliquez le fichier YAML.  Veillez à utiliser le groupe de ressources dans lequel votre ressource DiskEncryptionSet est déployée.  Si vous utilisez Azure Cloud Shell, vous pouvez créer ce fichier à l’aide de vi ou de nano comme si vous travailliez sur un système virtuel ou physique :

```
kind: StorageClass
apiVersion: storage.k8s.io/v1  
metadata:
  name: hdd
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Standard_LRS
  kind: managed
  diskEncryptionSetID: "/subscriptions/{myAzureSubscriptionId}/resourceGroups/{myResourceGroup}/providers/Microsoft.Compute/diskEncryptionSets/{myDiskEncryptionSetName}"
```
Ensuite, exécutez ce déploiement dans votre cluster AKS :
```azurecli-interactive
# Get credentials
az aks get-credentials --name myAksCluster --resource-group myResourceGroup --output table

# Update cluster
kubectl apply -f byok-azure-disk.yaml
```

## <a name="limitations"></a>Limites

* BYOK est disponible uniquement en disponibilité générale et en préversion dans certaines [régions Azure][supported-regions]
* Chiffrement de disque de système d’exploitation pris en charge avec Kubernetes version 1.17 et versions ultérieures   
* Disponible uniquement dans les régions où BYOK est pris en charge
* Le chiffrement avec des clés gérées par le client est actuellement réservé aux nouveaux clusters AKS. Les clusters existants ne peuvent pas être mis à niveau
* Le cluster AKS utilisant Virtual Machine Scale Sets est nécessaire. Aucune prise en charge de Virtual Machine Availability Sets


## <a name="next-steps"></a>Étapes suivantes

Consulter les [bonnes pratiques relatives à la sécurité des clusters AKS][best-practices-security]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
[supported-regions]: /azure/virtual-machines/windows/disk-encryption#supported-regions
