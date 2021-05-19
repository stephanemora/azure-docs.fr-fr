---
title: Utiliser le pilote CSI du magasin de secrets pour les secrets Azure Kubernetes Service
description: Découvrez comment utiliser le pilote CSI du magasin de secrets pour intégrer des magasins de secrets à Azure Kubernetes Service (AKS).
author: nickomang
ms.author: nickoman
ms.service: container-service
ms.topic: how-to
ms.date: 03/30/2021
ms.custom: template-how-to, devx-track-azurecli
ms.openlocfilehash: 7f83171733abc07de5997503560c6cc7278f3f39
ms.sourcegitcommit: 1b19b8d303b3abe4d4d08bfde0fee441159771e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109752376"
---
# <a name="use-the-secrets-store-csi-driver-for-kubernetes-in-an-azure-kubernetes-service-aks-cluster-preview"></a>Utiliser le pilote CSI du magasin de secrets pour Kubernetes dans un cluster Azure Kubernetes Service (AKS) (préversion)

Le pilote CSI du magasin de secrets pour Kubernetes permet l’intégration d’Azure Key Vault en tant que magasin de secrets avec un cluster Kubernetes via un [volume CSI][kube-csi].

## <a name="prerequisites"></a>Prérequis

- Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

- Avant de commencer, installez la dernière version [d’Azure CLI](/cli/azure/install-azure-cli-windows) et de l’extension *aks-preview*.

## <a name="features"></a>Fonctionnalités

- Monter des secrets, des clés et/ou des certificats dans un pod à l’aide d’un volume CSI
- Prend en charge les volumes CSI inline (Kubernetes version v1.15+)
- Prend en charge le montage de plusieurs objets de stockage de secrets comme un seul volume
- Prend en charge la portabilité de pod avec SecretProviderClass CRD
- Prend en charge les conteneurs Windows (Kubernetes version v1.18+)
- Synchronisation avec les secrets Kubernetes (pilote CSI de magasin de secrets v0.0.10+)
- Prend en charge la rotation automatique du contenu monté et des secrets Kubernetes synchronisés (pilote CSI de magasin de secrets v0.0.15+)

## <a name="register-the-aks-azurekeyvaultsecretsprovider-preview-feature"></a>Inscrire la fonctionnalité d’évaluation `AKS-AzureKeyVaultSecretsProvider`

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Pour créer un cluster AKS capable d’utiliser le pilote CSI de magasin de secrets, vous devez activer l’indicateur de fonctionnalité `AKS-AzureKeyVaultSecretsProvider` sur votre abonnement.

Inscrivez l’indicateur de fonctionnalité `AKS-AzureKeyVaultSecretsProvider` à l’aide de la commande [az feature register][az-feature-register], comme indiqué dans l’exemple suivant :

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-AzureKeyVaultSecretsProvider"
```

Quelques minutes sont nécessaires pour que l’état s’affiche *Registered* (Inscrit). Vérifiez l’état de l’inscription à l’aide de la commande [az feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzureKeyVaultSecretsProvider')].{Name:name,State:properties.state}"
```

Lorsque vous êtes prêt, actualisez l’inscription du fournisseur de ressources *Microsoft.ContainerService* à l’aide de la commande [az provider register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="install-the-aks-preview-cli-extension"></a>Installer l’extension CLI aks-preview

Vous devez également disposer de la version 0.5.9 ou d’une version ultérieure de l’extension Azure CLI *aks-preview*. Installez l’extension d’Azure CLI *aks-preview* à l’aide de la commande [az extension add][az-extension-add]. Si vous avez déjà installé l’extension, passez à la dernière version disponible avec la commande [az extension update][az-extension-update].

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-aks-cluster-with-secrets-store-csi-driver-support"></a>Créer un cluster AKS avec prise en charge du pilote CSI de magasin de secrets

> [!NOTE]
> Si vous envisagez de fournir l’accès au cluster par le biais d’une identité managée affectée par l’utilisateur ou par le système, activez Azure Active Directory sur votre cluster avec l’indicateur `enable-managed-identity`. Consultez [Utiliser les identités managées dans Azure Kubernetes Service][aks-managed-identity] pour en savoir plus.

Commencez par créer un groupe de ressources Azure :

```azurecli-interactive
az group create -n myResourceGroup -l westus
```

Pour créer un cluster AKS avec la capacité de pilote CSI de magasin de secrets, utilisez la commande [az aks create][az-aks-create] avec le module complémentaire `azure-keyvault-secrets-provider` :

```azurecli-interactive
az aks create -n myAKSCluster -g myResourceGroup --enable-addons azure-keyvault-secrets-provider
```

## <a name="upgrade-an-existing-aks-cluster-with-secrets-store-csi-driver-support"></a>Mettre à niveau un cluster AKS existant avec la prise en charge du pilote CSI de magasin de secrets

> [!NOTE]
> Si vous envisagez de fournir l’accès au cluster par le biais d’une identité managée affectée par l’utilisateur ou par le système, activez Azure Active Directory sur votre cluster avec l’indicateur `enable-managed-identity`. Consultez [Utiliser les identités managées dans Azure Kubernetes Service][aks-managed-identity] pour en savoir plus.

Pour mettre à niveau un cluster AKS existant avec la capacité de pilote CSI de magasin de secrets, utilisez la commande [az aks enable-addons][az-aks-enable-addons] avec le module complémentaire `azure-keyvault-secrets-provider` :

```azurecli-interactive
az aks enable-addons --addons azure-keyvault-secrets-provider --name myAKSCluster --resource-group myResourceGroup
```

## <a name="verify-secrets-store-csi-driver-installation"></a>Vérifier l’installation du pilote CSI de magasin de secrets

Ces commandes installent le pilote CSI du magasin de secrets et le fournisseur Azure Key Vault sur vos nœuds. Vérifiez en répertoriant tous les pods dotés des étiquettes « secrets-store-csi-driver » et « secrets-store-provider-azure » dans l’espace de noms kube-system et en vous assurant que votre sortie ressemble à ce qui suit :

```bash
kubectl get pods -n kube-system -l 'app in (secrets-store-csi-driver, secrets-store-provider-azure)'

NAMESPACE     NAME                                     READY   STATUS    RESTARTS   AGE
kube-system   aks-secrets-store-csi-driver-4vpkj       3/3     Running   2          4m25s
kube-system   aks-secrets-store-csi-driver-ctjq6       3/3     Running   2          4m21s
kube-system   aks-secrets-store-csi-driver-tlvlq       3/3     Running   2          4m24s
kube-system   aks-secrets-store-provider-azure-5p4nb   1/1     Running   0          4m21s
kube-system   aks-secrets-store-provider-azure-6pqmv   1/1     Running   0          4m24s
kube-system   aks-secrets-store-provider-azure-f5qlm   1/1     Running   0          4m25s
```


## <a name="enabling-and-disabling-autorotation"></a>Activation et désactivation de la rotation automatique

> [!NOTE]
> Lorsqu’il est activé, le pilote CSI du magasin de secrets met à jour le montage de pods et le secret Kubernetes défini dans secretObjects de SecretProviderClass en interrogeant les modifications toutes les deux minutes.

Pour activer la rotation automatique des secrets, utilisez l’indicateur `enable-secret-rotation` lors de la création de votre cluster :

```azurecli-interactive
az aks create -n myAKSCluster2 -g myResourceGroup --enable-addons azure-keyvault-secrets-provider --enable-secret-rotation
```

Ou mettez à jour un cluster existant avec le module complémentaire activé :

```azurecli-interactive
az aks update -g myResourceGroup -n myAKSCluster2 --enable-secret-rotation
```

Pour désactiver, utilisez l’indicateur `disable-secret-rotation` :

```azurecli-interactive
az aks update -g myResourceGroup -n myAKSCluster2 --disable-secret-rotation
```

## <a name="create-or-use-an-existing-azure-key-vault"></a>Créer ou utiliser un Azure Key Vault existant

En plus d’un cluster AKS, vous aurez besoin d’une ressource Azure Key Vault contenant le contenu de la clé secrète. Pour déployer une instance Azure Key Vault, procédez comme suit :

1. [Création d’un coffre de clés][create-key-vault]
2. [Définir un secret dans un coffre de clés][set-secret-key-vault]

Prenez note des propriétés suivantes pour une utilisation dans la section suivante :

- Nom de l’objet secret dans Key Vault
- Type de contenu secret (secret, clé, certificat)
- Nom de la ressource Key Vault
- ID de locataire Azure auquel appartient l’abonnement

## <a name="provide-identity-to-access-azure-key-vault"></a>Fournir l’identité pour accéder Azure Key Vault

L’exemple de cet article utilise un principal de service, mais le fournisseur Azure Key Vault offre quatre méthodes d’accès. Passez en revue les éléments et choisissez celui qui convient le mieux à votre cas d’utilisation. Gardez à l’esprit que des étapes supplémentaires peuvent être nécessaires en fonction de la méthode choisie, par exemple en accordant au principal de service des autorisations pour obtenir des secrets à partir du coffre de clés.

- [Principal du service][service-principal-access]
- [Identité de pod][pod-identity-access]
- [Identité managée affectée par l’utilisateur][ua-mi-access]
- [Identité managée affectée par le système][sa-mi-access]

## <a name="create-and-apply-your-own-secretproviderclass-object"></a>Créer et appliquer votre propre objet SecretProviderClass

Pour utiliser et configurer le pilote CSI de magasin de secrets pour votre cluster AKS, créez une ressource personnalisée SecretProviderClass.

Voici un exemple qui utilise un principal de service pour accéder au coffre de clés :

```yml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: azure-kvname
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"         # [OPTIONAL] if not provided, will default to "false"
    keyvaultName: "kvname"          # the name of the KeyVault
    cloudName: ""                   # [OPTIONAL for Azure] if not provided, azure environment will default to AzurePublicCloud 
    objects:  |
      array:
        - |
          objectName: secret1
          objectType: secret        # object types: secret, key or cert
          objectVersion: ""         # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: key1
          objectType: key
          objectVersion: ""
    tenantId: "<tenant-id>"                 # the tenant ID of the KeyVault
```

Pour plus d’informations, consultez [Créer votre propre objet SecretProviderClass][sample-secret-provider-class]. Veillez à utiliser les valeurs que vous avez notées ci-dessus.

### <a name="apply-the-secretproviderclass-to-your-cluster"></a>Appliquer la SecretProviderClass à votre cluster

Ensuite, déployez la SecretProviderClass que vous avez créé. Par exemple :

```bash
kubectl apply -f ./new-secretproviderclass.yaml
```

## <a name="update-and-apply-your-clusters-deployment-yaml"></a>Mise à jour et application du YAML de déploiement de votre cluster

Pour vous assurer que votre cluster utilise la nouvelle ressource personnalisée, mettez à jour le YAML de déploiement. Pour obtenir un exemple plus complet, consultez un [exemple de déploiement][sample-deployment] avec le principal du service pour accéder à Azure Key Vault. Veillez à suivre les étapes supplémentaires de la méthode de votre choix d’accès au coffre de clés.

```yml
kind: Pod
apiVersion: v1
metadata:
  name: busybox-secrets-store-inline
spec:
  containers:
  - name: busybox
    image: k8s.gcr.io/e2e-test-images/busybox:1.29
    command:
      - "/bin/sleep"
      - "10000"
    volumeMounts:
    - name: secrets-store-inline
      mountPath: "/mnt/secrets-store"
      readOnly: true
  volumes:
    - name: secrets-store-inline
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: "azure-kvname"
        nodePublishSecretRef:                       # Only required when using service principal mode
          name: secrets-store-creds                 # Only required when using service principal mode. The name of the Kubernetes secret that contains the service principal credentials to access keyvault.
```

Appliquez le déploiement mis à jour au cluster :

```bash
kubectl apply -f ./my-deployment.yaml
```

## <a name="validate-the-secrets"></a>Valider les secrets

Une fois le pod démarré, le contenu monté au niveau du chemin d’accès du volume spécifié dans le YAML de votre déploiement est disponible.

```Bash
## show secrets held in secrets-store
kubectl exec busybox-secrets-store-inline -- ls /mnt/secrets-store/

## print a test secret 'secret1' held in secrets-store
kubectl exec busybox-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

## <a name="disable-secrets-store-csi-driver-on-an-existing-aks-cluster"></a>Désactiver le pilote CSI de magasin de secrets sur un cluster AKS existant

Pour désactiver la capacité de pilote CSI de magasin de secrets dans un cluster existant, utilisez la commande [az aks disable-addons][az-aks-disable-addons] avec l’indicateur `azure-keyvault-secrets-provider` :

```azurecli-interactive
az aks disable-addons --addons azure-keyvault-secrets-provider -g myResourceGroup -n myAKSCluster
```

## <a name="next-steps"></a>Étapes suivantes
<!-- Add a context sentence for the following links -->
Après avoir appris à utiliser le pilote de magasin de secrets CSI avec un cluster AKS, consultez les ressources suivantes :

- [Activer les pilotes CSI pour les disques Azure et Azure Files sur AKS][csi-storage-drivers]

<!-- Links -->
<!-- Internal -->
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-enable-addons]: /cli/azure/aks#az_aks_enable_addons
[az-aks-disable-addons]: /cli/azure/aks#az_aks_disable_addons
[key-vault-provider]: ../key-vault/general/key-vault-integrate-kubernetes.md
[csi-storage-drivers]: ./csi-storage-drivers.md
[create-key-vault]: ../key-vault/general/quick-create-cli.md
[set-secret-key-vault]: ../key-vault/secrets/quick-create-portal.md
[aks-managed-identity]: ./use-managed-identity.md

<!-- External -->
[kube-csi]: https://kubernetes-csi.github.io/docs/
[key-vault-provider-install]: https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/installation
[sample-secret-provider-class]: https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/usage/#create-your-own-secretproviderclass-object
[service-principal-access]: https://azure.github.io/secrets-store-csi-driver-provider-azure/configurations/identity-access-modes/service-principal-mode/
[pod-identity-access]: https://azure.github.io/secrets-store-csi-driver-provider-azure/configurations/identity-access-modes/pod-identity-mode/
[ua-mi-access]: https://azure.github.io/secrets-store-csi-driver-provider-azure/configurations/identity-access-modes/user-assigned-msi-mode/
[sa-mi-access]: https://azure.github.io/secrets-store-csi-driver-provider-azure/configurations/identity-access-modes/system-assigned-msi-mode/
[sample-deployment]: https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/examples/service-principal/pod-inline-volume-service-principal.yaml
