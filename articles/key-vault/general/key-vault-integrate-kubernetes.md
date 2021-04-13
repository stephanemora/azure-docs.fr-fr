---
title: Intégrer Azure Key Vault à Kubernetes
description: Dans ce tutoriel, vous allez accéder à des secrets dans votre coffre de clés Azure et les récupérer en utilisant le pilote CSI (Container Storage Interface) du magasin des secrets pour les monter dans des pods Kubernetes.
author: msmbaldwin
ms.author: mbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: 6cf76e980fab4e5be3f8c2c6d72baff05ab03815
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106108385"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-the-secrets-store-csi-driver-on-kubernetes"></a>Tutoriel : Configurer et exécuter le fournisseur Azure Key Vault pour le pilote CSI du magasin des secrets sur Kubernetes

> [!IMPORTANT]
> Le pilote CSI du Magasin des secrets est un projet open source qui n’est pas pris charge par le support technique Azure. Signalez tous les commentaires et problèmes liés à l’intégration Key Vault du pilote CSI sur le dépôt [GitHub](https://github.com/kubernetes-sigs/secrets-store-csi-driver) de ce pilote. Cet outil est fourni pour permettre aux utilisateurs d’effectuer une auto-installation dans des clusters et recueillir les commentaires de notre communauté.

Dans ce tutoriel, vous allez accéder à des secrets dans votre coffre de clés Azure et les récupérer en utilisant le pilote CSI (Container Storage Interface) du magasin des secrets pour monter ces secrets dans des pods Kubernetes en tant que volume.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Utiliser des identités managées.
> * Déployer un cluster AKS (Azure Kubernetes Service) à l’aide d’Azure CLI.
> * Installez Helm, le pilote CSI du Magasin des secrets, et le fournisseur Azure Key Vault pour le pilote CSI.
> * Créer un coffre de clés Azure et définir vos secrets.
> * Créer votre propre objet SecretProviderClass.
> * Déployer votre pod avec les secrets montés de votre coffre de clés.

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

* Avant de commencer ce tutoriel, installez l’[interface de ligne de commande Azure](/cli/azure/install-azure-cli-windows).

Ce tutoriel suppose que vous exécutez Azure Kubernetes Service sur des nœuds Linux.

## <a name="use-managed-identities"></a>Utiliser des identités managées

Ce diagramme montre le flux d’intégration AKS – Key Vault pour l’identité managée :

![Diagramme montrant le flux d’intégration AKS – Key Vault pour l’identité managée](../media/aks-key-vault-integration-flow.png)

## <a name="deploy-an-azure-kubernetes-service-aks-cluster-by-using-the-azure-cli"></a>Déployer un cluster AKS (Azure Kubernetes Service) à l’aide d’Azure CLI

Vous n’avez pas besoin d’utiliser Azure Cloud Shell. Votre invite de commandes (terminal) couplée à l’interface installée Azure CLI suffira. 

Suivez les instructions des sections « Créer un groupe de ressources », « Créer un cluster AKS » et « Se connecter au cluster » dans l’article [Déployer un cluster Azure Kubernetes Service à l’aide d’Azure CLI](../../aks/kubernetes-walkthrough.md). 

> [!NOTE] 
> Si vous envisagez d’utiliser une identité de pod, le plug-in réseau recommandé est `azure`. Pour plus d’informations, consultez ce [document](https://azure.github.io/aad-pod-identity/docs/configure/aad_pod_identity_on_kubenet/). Créez le cluster Kubernetes, comme indiqué dans la commande suivante :
>
> ```azurecli
> az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
> ```

1. [Définissez votre variable d’environnement PATH](https://www.java.com/en/download/help/path.xml) sur le fichier *kubectl.exe* que vous avez téléchargé.
2. Vérifiez votre version de Kubernetes à l’aide de la commande suivante, qui indique la version du client et du serveur. La version du client est le fichier *kubectl.exe* que vous avez installé, et la version du serveur est l’environnement AKS (Azure Kubernetes Service) sur lequel votre cluster s’exécute.
    ```azurecli
    kubectl version
    ```
3. Vérifiez que votre version de Kubernetes est la version 1.1.16.0 ou ultérieure. Pour les clusters Windows, vérifiez que votre version de Kubernetes est 1.18.0 ou une version ultérieure. La commande suivante met à niveau le cluster Kubernetes et le pool de nœuds. L’exécution de la commande peut prendre quelques minutes. Dans cet exemple, le groupe de ressources est *contosoResourceGroup* et le cluster Kubernetes est *contosoAKSCluster*.
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
4. Pour afficher les métadonnées du cluster AKS que vous avez créé, utilisez la commande suivante. Copiez les valeurs de **principalId**, **clientId**, **subscriptionId** et **nodeResourceGroup** pour une utilisation ultérieure. Si le cluster AKS n’a pas été créé alors que les identités managées étaient activées, les paramètres **principalId** et **clientId** seront Null. 

    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    Dans la sortie, deux paramètres sont mis en évidence :
    
    ![Capture d’écran d’Azure CLI avec les valeurs de principalId et clientId mises en évidence](../media/kubernetes-key-vault-2.png) ![Capture d’écran d’Azure CLI avec les valeurs de subscriptionId et nodeResourceGroup mises en évidence](../media/kubernetes-key-vault-3.png)
    
## <a name="install-helm-and-the-secrets-store-csi-driver"></a>Installer Helm et le pilote CSI du magasin des secrets
> [!NOTE]
> L’installation ci-dessous fonctionne uniquement sur AKS sur Linux. Pour plus d’informations sur l’installation du pilote CSI du magasin des secrets, consultez [Fournisseur Azure Key Vault pour le pilote CSI du magasin des secrets](https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/installation/). 

Pour installer le pilote CSI du Magasin des secrets et le fournisseur Azure Key Vault, vous devez d’abord installer [Helm](https://helm.sh/docs/intro/install/).

L’interface du pilote [CSI du Magasin des secrets](https://azure.github.io/secrets-store-csi-driver-provider-azure/) vous permet d’obtenir les secrets stockés dans votre instance Azure Key Vault, puis d’utiliser l’interface du pilote pour monter le contenu secret dans des pods Kubernetes.

1. Vérifiez que la version de Helm est v3 ou ultérieure :
    ```azurecli
    helm version
    ```
1. Installez le pilote CSI du magasin des secrets et le fournisseur Azure Key Vault pour le pilote :
    ```azurecli
    helm repo add csi-secrets-store-provider-azure https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/charts

    helm install csi-secrets-store-provider-azure/csi-secrets-store-provider-azure --generate-name
    ```
> [!NOTE] 
> Si vous envisagez d’utiliser le pilote CSI du Magasin des secrets et le fournisseur Azure Key Vault sur des nœuds Windows, activez le pilote et le fournisseur sur les nœuds Windows à l’aide des [valeurs de configuration de Helm](https://github.com/Azure/secrets-store-csi-driver-provider-azure/tree/master/charts/csi-secrets-store-provider-azure#configuration).

## <a name="create-an-azure-key-vault-and-set-your-secrets"></a>Créer un coffre de clés Azure et définir vos secrets

Pour créer votre propre coffre de clés et définir vos secrets, suivez les instructions fournies dans [Définir et récupérer un secret depuis Azure Key Vault à l’aide d’Azure CLI](../secrets/quick-create-cli.md).

> [!NOTE] 
> Vous n’avez pas besoin d’utiliser Azure Cloud Shell ni de créer un groupe de ressources. Vous pouvez utiliser le groupe de ressources que vous avez créé précédemment pour le cluster Kubernetes.

## <a name="create-your-own-secretproviderclass-object"></a>Créer votre propre objet SecretProviderClass

Pour créer votre propre objet SecretProviderClass personnalisé avec des paramètres propres au fournisseur pour le pilote CSI du magasin des secrets, [utilisez ce modèle](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/service-principal/v1alpha1_secretproviderclass_service_principal.yaml). Cet objet permet à l’identité d’accéder à votre coffre de clés.

Dans l’exemple de fichier YAML SecretProviderClass, spécifiez les paramètres manquants. Les paramètres suivants sont requis :

* **keyvaultName** : nom de votre coffre de clés
* **objects** : liste de tous les contenus secrets que vous souhaitez monter
    * **objectName** : nom du contenu de secret
    * **objectType** : type d’objet (secret, clé, certificat)
* **tenantID** : ID de locataire ou ID de répertoire de votre coffre de clés

La documentation sur tous les champs obligatoires et les configurations prises en charge est disponible ici : [Lien](https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/usage/#create-your-own-secretproviderclass-object)

Le modèle mis à jour figure dans l’exemple suivant. Téléchargez-le sous forme de fichier YAML, puis complétez les champs obligatoires. Dans cet exemple, le coffre de clés est **contosoKeyVault5**. Il comporte deux secrets : **secret1** et **secret2**.

> [!NOTE] 
> Si vous utilisez des identités de pod, définissez la valeur **usePodIdentity** sur *true*, et la valeur **userAssignedIdentityID** sur une paire de guillemets droits ( **""** ). Si vous utilisez des identités managées, définissez la valeur de **useVMManagedIdentity** sur *true*, et la valeur de **userAssignedIdentityID** sur un clientID ou une identité affectée par l’utilisateur.

```yaml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: azure-kvname
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"                                                 # [OPTIONAL] if not provided, will default to "false". Set to "true" if using pod identities.
    useVMManagedIdentity: "false"                                           # [OPTIONAL] if not provided, will default to "false". Set to "true" if using managed identities.
    userAssignedIdentityID: "<clientID of user-assigned managed identity"   # [OPTIONAL] If you're using managed identities, use the client id to specify which user-assigned managed identity to use. If the value is empty, it defaults to use the system-assigned identity on the VM
    keyvaultName: "contosoKeyVault5"                                        # [REQUIRED] the name of the key vault
                                                                            #     az keyvault show --name contosoKeyVault5
                                                                            #     the preceding command will display the key vault metadata, which includes the subscription ID, resource group name, key vault 
    cloudName: ""                                                           # [OPTIONAL] if not provided, Azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1                                               # [REQUIRED] object name
                                                                            #     az keyvault secret list --vault-name "contosoKeyVault5"
                                                                            #     the above command will display a list of secret names from your key vault
          objectType: secret                                                # [REQUIRED] object types: secret, key, or cert
          objectVersion: ""                                                 # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: secret2
          objectType: secret
          objectVersion: ""
    tenantId: "tenantID"                                                    # [REQUIRED] the tenant ID of the key vault
```
L’image suivante montre la sortie de la console pour **az keyvault show --name contosoKeyVault5** avec les métadonnées importantes mises en évidence :

![Capture d’écran montrant la sortie de la console pour « az keyvault show --name contosoKeyVault5 »](../media/kubernetes-key-vault-4.png)

## <a name="install-azure-active-directory-azure-ad-pod-identity"></a>Installer une Identité de pod Azure Active Directory (Azure AD)

1. Affectez des rôles spécifiques au cluster AKS que vous avez créé. 

    La documentation relative à toutes les attributions de rôle requises avec une identité de pod Azure Active Directory (Azure AD) est disponible ici : [Lien](https://azure.github.io/aad-pod-identity/docs/getting-started/role-assignment/)

    ```azurecli
    VAULT_RESOURCE_GROUP=contosoResourceGroup
    NODE_RESOURCE_GROUP=contosoResourceGroup
    
    az role assignment create --role "Managed Identity Operator" --assignee $clientId --scope /subscriptions/<SUBID>/resourcegroups/$VAULT_RESOURCE_GROUP
    
    az role assignment create --role "Managed Identity Operator" --assignee $clientId --scope /subscriptions/<SUBID>/resourcegroups/$NODE_RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/<SUBID>/resourcegroups/$NODE_RESOURCE_GROUP
    ```

2. Installez l’identité Azure Active Directory (Azure AD) dans AKS.

    > [!NOTE] 
    > Si vous utilisez un cluster AKS avec un plug-in réseau kubenet, consultez ce [document](https://azure.github.io/aad-pod-identity/docs/configure/aad_pod_identity_on_kubenet/) sur la manière de déployer une identité de pod dans le cluster.

    ```azurecli
    helm repo add aad-pod-identity https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts

    helm install pod-identity aad-pod-identity/aad-pod-identity
    ```

3. Créez une identité managée affectée par l’utilisateur. Dans la sortie, copiez la valeur de **clientId** en vue d’une utilisation ultérieure.
    ```azurecli
    az identity create -g $resourceGroupName -n $identityName
    ```

4. Accordez des autorisations d’identité pour recevoir des secrets de votre coffre de clés. Utilisez le **clientID** de l’identité managée affectée par l’utilisateur.
    ```azurecli
    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    az keyvault set-policy -n contosoKeyVault5 --key-permissions get --spn $clientId
    az keyvault set-policy -n contosoKeyVault5 --certificate-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-your-key-vault"></a>Déployer votre pod avec les secrets montés de votre coffre de clés

Pour configurer votre objet SecretProviderClass, exécutez la commande suivante :
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="use-managed-identities"></a>Utiliser des identités managées

Si vous utilisez des identités de pod, créez une *AzureIdentity* dans votre cluster, qui référence l’identité que vous avez créée précédemment. Ensuite, créez un objet *AzureIdentityBinding* qui fait référence à l’objet AzureIdentity que vous avez créé. Spécifiez les paramètres du modèle suivant, puis enregistrez-le sous le nom *podIdentityAndBinding.yaml*.  

```yml
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentity
metadata:
    name: azureIdentityName                 # The name of your Azure identity
spec:
    type: 0                                 # Set type: 0 for managed identity
    resourceID: /subscriptions/<SUBID>/resourcegroups/<RESOURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<AZUREIDENTITYNAME>
    clientID: "<managed identity clientID>"   # The clientId of the User-assigned managed identity that you created earlier
---
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentityBinding
metadata:
    name: azure-pod-identity-binding
spec:
    azureIdentity: "azureIdentityName"      # The name of your Azure identity
    selector: azure-pod-identity-binding-selector
```
    
Exécutez la commande suivante pour configurer la liaison :

```azurecli
kubectl apply -f podIdentityAndBinding.yaml
```

Ensuite, déployez le pod. Le code suivant est le fichier YAML de déploiement qui utilise la liaison d’identité du pod de l’étape précédente. Enregistrez ce fichier en tant que *podBindingDeployment.yaml*.

```yml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-secrets-store-inline
  labels:
    aadpodidbinding: azure-pod-identity-binding-selector # The selector defined in AzureIdentityBinding in the previous step
spec:
  containers:
    - name: nginx
      image: nginx
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
          secretProviderClass: azure-kvname
```

Exécutez la commande suivante pour déployer votre pod :

```azurecli
kubectl apply -f podBindingDeployment.yaml
```

### <a name="check-the-pod-status-and-secret-content"></a>Vérifier l’état du pod et le contenu du secret 

Pour afficher les pods que vous avez déployés, exécutez la commande suivante :
```azurecli
kubectl get pods
```

Pour vérifier l’état de votre pod, exécutez la commande suivante :
```azurecli
kubectl describe pod/nginx-secrets-store-inline
```

![Capture d’écran de la sortie Azure CLI indiquant l’état « Running » (En cours d’exécution) et présentant tous les événements comme étant de type « Normal » ](../media/kubernetes-key-vault-6.png)

Dans la fenêtre de sortie, le pod déployé doit être à l’état *Running* (En cours d’exécution). Dans la section **Events** au bas, tous les événements sont affichés comme étant de type *Normal*.

Après avoir vérifié que le pod est bien en cours d’exécution, vous pouvez vérifier qu’il contient les secrets de votre coffre de clés.

Pour afficher tous les secrets contenus dans le pod, exécutez la commande suivante :
```azurecli
kubectl exec nginx-secrets-store-inline -- ls /mnt/secrets-store/
```

Pour afficher le contenu d’un secret spécifique, exécutez la commande suivante :
```azurecli
kubectl exec nginx-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

Vérifiez que le contenu du secret est affiché.

## <a name="resources"></a>Ressources
[À propos d’Azure Key Vault](overview.md)
[Guide du développeur Azure Key Vault](developers-guide.md)
[Pilote des secrets CSI](https://secrets-store-csi-driver.sigs.k8s.io/introduction.html)


Pour vérifier que votre coffre de clés est récupérable, consultez :
> [!div class="nextstepaction"]
> [Activer la suppression réversible](./key-vault-recovery.md)
