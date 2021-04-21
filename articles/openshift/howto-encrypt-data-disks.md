---
title: Chiffrer des revendications de volume persistant avec une clé gérée par le client (CMK) sur Azure Red Hat OpenShift (ARO)
description: Instructions pour le déploiement de clé gérée par le client (CMK)/Bring Your Own Key (BYOK) pour Azure Red Hat OpenShift
ms.topic: article
ms.date: 02/24/2021
author: stuartatmicrosoft
ms.author: stkirk
ms.service: azure-redhat-openshift
keywords: chiffrement, byok, aro, cmk, openshift, red hat
ms.openlocfilehash: f6c80bab6f821dc7c85352bf57ebe255ae712d43
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783518"
---
# <a name="encrypt-persistent-volume-claims-with-a-customer-managed-key-cmk-on-azure-red-hat-openshift-aro-preview"></a>Chiffrer des revendications de volume persistant avec une clé gérée par le client (CMK) sur Azure Red Hat OpenShift (ARO) (préversion)

Le service Stockage Azure utilise le chiffrement côté serveur (SSE) pour [chiffrer](../storage/common/storage-service-encryption.md) automatiquement vos données lorsqu’elles sont rendues persistantes dans le cloud. Par défaut, les données sont chiffrées avec des clés managées par la plateforme Microsoft. Pour un contrôle supplémentaire sur les clés de chiffrement, vous pouvez fournir vos propres clés gérées par le client afin de chiffrer les données dans vos clusters Azure Red Hat OpenShift.

> [!NOTE]
> À l’heure actuelle, la prise en charge existe uniquement pour le chiffrement des volumes persistants ARO avec des clés gérées par le client. Cette fonctionnalité n’est actuellement pas disponible pour les disques de système d’exploitation de nœud worker ou maître.

> [!IMPORTANT]
> Les fonctionnalités d’évaluation ARO sont disponibles en libre-service en y adhérant. Les préversions sont fournies « en l’état » et « en fonction des disponibilités », et sont exclues des contrats de niveau de service et de la garantie limitée. Les préversions ARO sont, dans la mesure du possible, partiellement couvertes par le service clientèle. En tant que tel, ces fonctionnalités ne sont pas destinées à une utilisation en production.

## <a name="before-you-begin"></a>Avant de commencer
Cet article suppose que vous avez :

* Vous disposez déjà d’un cluster ARO avec OpenShift version 4.4 (ou ultérieure)

* L’outil en ligne de commande OpenShift **oc**, base64 (une partie de coreutils) et **Azure CLI** sont installés.

* Vous êtes connecté à votre cluster ARO avec **oc** en tant qu’utilisateur administrateur de cluster général (kubeadmin).

* Vous êtes connecté à Azure CLI à l’aide de la commande **az** avec un compte autorisé à accorder l’accès « Contributeur » dans le même abonnement que le cluster ARO.

## <a name="limitations"></a>Limites

* La disponibilité du chiffrement avec clé gérée par le client est propre à la région. Pour voir l’état d’une région Azure spécifique, consultez [Régions Azure][supported-regions].
* Si vous souhaitez utiliser des disques Ultra, vous devez d’abord les activer sur votre abonnement avant de commencer.

## <a name="declare-cluster--encryption-variables"></a>Déclarer les variables de chiffrement et de cluster
Vous devez configurer les variables ci-dessous en fonction de ce qui convient pour le cluster ARO dans lequel vous souhaitez activer les clés de chiffrement gérées par le client :
```
aroCluster="mycluster"             # The name of the ARO cluster that you wish to enable CMK on. This may be obtained from **az aro list -o table**
buildRG="mycluster-rg"             # The name of the resource group used when you initially built the ARO cluster. This may be obtained from **az aro list -o table**
desName="aro-des"                  # Your Azure Disk Encryption Set name. This must be unique in your subscription.
vaultName="aro-keyvault-1"         # Your Azure Key Vault name. This must be unique in your subscription.
vaultKeyName="myCustomAROKey"      # The name of the key to be used within your Azure Key Vault. This is the name of the key, not the actual value of the key that you will rotate.
```

## <a name="obtain-your-subscription-id"></a>Obtenir votre ID d’abonnement
Votre ID d’abonnement Azure est utilisé à plusieurs reprises lors la configuration de CMK. Obtenez-le et stockez-le en tant que variable :
```azurecli-interactive
# Obtain your Azure Subscription ID and store it in a variable
subId="$(az account list -o tsv | grep True | awk '{print $3}')"
```

## <a name="create-an-azure-key-vault-instance"></a>Créer une instance Azure Key Vault
Vous devez utiliser une instance Azure Key Vault pour stocker vos clés. Créez un nouveau coffre de clés avec la protection de purge activée. Créez ensuite une clé dans le coffre pour stocker votre propre clé personnalisée :

```azurecli-interactive
# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n $vaultName -g $buildRG --enable-purge-protection true -o table

# Create the actual key within the Azure Key Vault
az keyvault key create --vault-name $vaultName --name $vaultKeyName --protection software -o jsonc
```

## <a name="create-an-azure-disk-encryption-set"></a>Créer un jeu de chiffrement de disque Azure

Le jeu de chiffrement de disque Azure est utilisé comme point de référence pour les disques dans ARO. Il est connecté au coffre de clés Azure que nous avons créé à l’étape précédente, et extrait les clés gérées par le client à partir de cet emplacement.

```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId="$(az keyvault show --name $vaultName --query [id] -o tsv)"

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl="$(az keyvault key show --vault-name $vaultName --name $vaultKeyName  --query [key.kid] -o tsv)"

# Create an Azure disk encryption set
az disk-encryption-set create -n $desName -g $buildRG --source-vault $keyVaultId --key-url $keyVaultKeyUrl -o table
```

## <a name="grant-the-disk-encryption-set-access-to-key-vault"></a>Accorder au jeu de chiffrement de disque l’accès au coffre de clés
Accordez au jeu de chiffrement de disque que nous avons créé aux étapes précédentes l’accès à Azure Key Vault :

```azurecli-interactive
# First, find the disk encryption set's Azure Application ID value.
desIdentity="$(az disk-encryption-set show -n $desName -g $buildRG --query [identity.principalId] -o tsv)"

# Next, update the Key Vault security policy settings to allow access to the disk encryption set.
az keyvault set-policy -n $vaultName -g $buildRG --object-id $desIdentity --key-permissions wrapkey unwrapkey get -o table

# Now, ensure the Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc
```

### <a name="obtain-other-ids-required-for-role-assignments"></a>Obtenir les autres ID requis pour les attributions de rôles
Nous devons autoriser le cluster ARO à utiliser le jeu de chiffrement de disque pour chiffrer les revendications de volume persistant dans le cluster ARO. Pour ce faire, nous allons créer une nouvelle identité MSI (Managed Service Identity). Nous allons également définir d’autres autorisations pour l’identité MSI ARO et pour le jeu de chiffrement de disque.

```azurecli-interactive
# First, get the Azure Application ID of the service principal used in the ARO cluster.
aroSPAppId="$(oc get secret azure-credentials -n kube-system -o jsonpath='{.data.azure_client_id}' | base64 --decode)"

# Next, get the object ID of the service principal used in the ARO cluster.
aroSPObjId="$(az ad sp show --id $aroSPAppId -o tsv --query [objectId])"

# Set the name of the ARO Managed Service Identity. 
msiName="$aroCluster-msi"

# Create the Managed Service Identity (MSI) required for disk encryption.
az identity create -g $buildRG -n $msiName -o jsonc

# Get the ARO Managed Service Identity Azure Application ID.
aroMSIAppId="$(az identity show -n $msiName -g $buildRG -o tsv --query [clientId])"

# Get the resource ID for the disk encryption set and the Key Vault resource group.
buildRGResourceId="$(az group show -n $buildRG -o tsv --query [id])"
```

### <a name="implement-other-role-assignments-required-for-cmk-encryption"></a>Implémenter d’autres attributions de rôles requises pour le chiffrement CMK
Appliquez les attributions de rôles requises à l’aide des variables obtenues à l’étape précédente :

```azurecli-interactive
# Ensure the Azure Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc

# Assign the MSI AppID 'Reader' permission over the disk encryption set & Key Vault resource group.
az role assignment create --assignee $aroMSIAppId --role Reader --scope $buildRGResourceId -o jsonc

# Assign the ARO Service Principal 'Contributor' permission over the disk encryption set & Key Vault Resource Group.
az role assignment create --assignee $aroSPObjId --role Contributor --scope $buildRGResourceId -o jsonc
```

## <a name="create-a-k8s-storage-class-for-encrypted-premium--ultra-disks"></a>Créer une classe de stockage k8s pour les disques Ultra et Premium chiffrés
Générez les classes de stockage à utiliser pour la CMK pour les disques Premium_LRS et UltraSSD_LRS :

```azurecli-interactive
# Premium Disks
cat > managed-premium-encrypted-cmk.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-encrypted-cmk
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Premium_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/$subId/resourceGroups/$buildRG/providers/Microsoft.Compute/diskEncryptionSets/$desName"
  resourceGroup: $buildRG
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF

# Ultra Disks
cat > managed-ultra-encrypted-cmk.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-ultra-encrypted-cmk
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: UltraSSD_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/$subId/resourceGroups/$buildRG/providers/Microsoft.Compute/diskEncryptionSets/$desName"
  resourceGroup: $buildRG
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF
```

Ensuite, exécutez ce déploiement dans votre cluster ARO pour appliquer la configuration de la classe de stockage :

```azurecli-interactive
# Update cluster with the new storage classes
oc apply -f managed-premium-encrypted-cmk.yaml
oc apply -f managed-ultra-encrypted-cmk.yaml
```

## <a name="test-encryption-with-customer-managed-keys-optional"></a>Tester le chiffrement avec clé gérée par le client (facultatif)
Pour vérifier si votre cluster utilise une clé gérée par le client pour le chiffrement des revendications de volume persistant, nous allons créer une revendication de volume persistant à l’aide de la nouvelle classe de stockage. L’extrait de code ci-dessous crée un pod et monte une revendication de volume persistant à l’aide de disques Premium.
```
# Create a pod which uses a persistent volume claim referencing the new storage class
cat > test-pvc.yaml<< EOF
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mypod-with-cmk-encryption-pvc
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium-encrypted-cmk
  resources:
    requests:
      storage: 1Gi
---
kind: Pod
apiVersion: v1
metadata:
  name: mypod-with-cmk-encryption
spec:
  containers:
  - name: mypod-with-cmk-encryption
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: mypod-with-cmk-encryption-pvc
EOF
```
### <a name="apply-the-test-pod-configuration-file-optional"></a>Appliquer le fichier de configuration de pod de test (facultatif)
Exécutez les commandes ci-dessous pour appliquer la configuration de pod de test et retourner l’UID de la nouvelle revendication de volume persistant. L’UID sera utilisé pour vérifier que le disque est chiffré à l’aide d’une clé CMK.
```azurecli-interactive
# Apply the test pod configuration file and set the PVC UID as a variable to query in Azure later.
pvcUid="$(oc apply -f test-pvc.yaml -o jsonpath='{.items[0].metadata.uid}')"

# Determine the full Azure Disk name.
pvName="$(oc get pv pvc-$pvcUid -o jsonpath='{.spec.azureDisk.diskName}')"
```
> [!NOTE]
> Parfois, il peut y avoir un léger délai lors de l’application des attributions de rôles dans Azure Active Directory. Selon la vitesse à laquelle ces instructions sont exécutées, la commande « Déterminer le nom complet du disque Azure » peut échouer. Si cela se produit, passez en revue la sortie d’**oc describe pvc mypod-with-cmk-encryption-pvc** pour vérifier que le disque a été correctement provisionné. Si la propagation de l’attribution de rôle n’est pas terminée, vous devrez peut-être *supprimer* et *réappliquer* le YAML de pod et PVC.

### <a name="verify-pvc-disk-is-configured-with-encryptionatrestwithcustomerkey-optional"></a>Vérifier que le disque des revendications de volume persistant est configuré avec « EncryptionAtRestWithCustomerKey » (facultatif)
Le pod doit créer une revendication de volume persistant qui fait référence à la classe de stockage de CMK. L’exécution de la commande suivante permet de valider que le déploiement de la revendication de volume persistant a été effectué comme prévu :
```azurecli-interactive
# Describe the OpenShift cluster-wide persistent volume claims
oc describe pvc

# Verify with Azure that the disk is encrypted with a customer-managed key
az disk show -n $pvName -g $buildRG -o json --query [encryption]
```

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[best-practices-security]: ../aks/operator-best-practices-cluster-security.md
[byok-azure-portal]: ../storage/common/customer-managed-keys-configure-key-vault.md
[customer-managed-keys]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[key-vault-generate]: ../key-vault/general/manage-with-cli2.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions