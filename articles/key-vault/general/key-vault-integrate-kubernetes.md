---
title: Intégrer Azure Key Vault à Kubernetes
description: Dans ce tutoriel, vous allez accéder à des secrets et les récupérer à partir d’Azure Key Vault en utilisant le pilote CSI (Container Storage Interface) du magasin des secrets pour ensuite les monter dans des pods Kubernetes.
author: taytran0
ms.author: t-trtr
ms.service: key-vault
ms.topic: tutorial
ms.date: 06/04/2020
ms.openlocfilehash: e945a30ca1fcd62fdfccd16d4e853540dbf73d8a
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84636933"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-secret-store-csi-driver-on-kubernetes"></a>Tutoriel : Configurer et exécuter le fournisseur Azure Key Vault pour le pilote CSI du magasin des secrets sur Kubernetes

Dans ce tutoriel, vous allez accéder à des secrets et les récupérer à partir d’Azure Key Vault en utilisant le pilote CSI (Container Storage Interface) du magasin des secrets pour ensuite les monter dans des pods Kubernetes.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer un principal du service
> * Déployer un cluster Azure Kubernetes Service
> * Installer Helm et le pilote CSI du magasin des secrets
> * Créer un coffre de clés Azure et définir des secrets
> * Créer votre propre objet SecretProviderClass
> * Déployer votre pod avec des secrets montés à partir de Key Vault

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Avant de commencer ce tutoriel, installez l’[interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

## <a name="create-a-service-principal-or-use-managed-identities"></a>Créer un principal du service ou utiliser des identités managées

Si vous envisagez d’utiliser des identités managées, vous pouvez passer à la section suivante.

Créez un principal du service pour contrôler les ressources accessibles à partir de votre coffre de clés Azure. L’accès de ce principal du service est limité par les rôles qui lui sont attribués. Cette fonctionnalité vous permet de contrôler la façon dont le principal du service peut gérer vos secrets. Dans l’exemple ci-dessous, le nom du principal du service est **contosoServicePrincipal**.

```azurecli
az ad sp create-for-rbac --name contosoServicePrincipal --skip-assignment
```
Cette opération retourne une série de paires clé/valeurs :

![Image](../media/kubernetes-key-vault-1.png)

Copiez les valeurs d’**appId** et de **password**. Vous aurez besoin de ces informations d’identification ultérieurement.



## <a name="deploy-an-azure-kubernetes-service-cluster-using-azure-cli"></a>Déployer un cluster Azure Kubernetes Service à l’aide de l’interface de ligne de commande Azure

Vous n’avez pas besoin d’utiliser Azure Cloud Shell, votre invite de commandes (terminal) avec l’interface de ligne de commande Azure installée est suffisante. 

Suivez ce [guide](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough) et effectuez les étapes des sections suivantes : **Créez un groupe de ressources**, **Créez un cluster AKS** et **Connectez-vous au cluster**.

**Remarque :** Si vous envisagez d’utiliser une identité de pod au lieu d’un principal du service, veillez à l’activer lors de la création du cluster Kubernetes, comme indiqué ci-dessous :

```azurecli
az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
```

1. [Définissez votre variable d’environnement PATH](https://www.java.com/en/download/help/path.xml) sur le fichier « kubectl.exe » qui a été téléchargé.
1. Vérifiez votre version de Kubernetes à l’aide de la commande ci-dessous. Cette commande génère la version du client et du serveur. La version du client est le fichier « kubectl.exe » que vous avez installé alors que la version du serveur est l’environnement Azure Kubernetes Service sur lequel votre cluster s’exécute.
    ```azurecli
    kubectl version
    ```
1. Assurez-vous que votre version de Kubernetes est **v1.16.0** ou supérieure. Cette commande met à niveau le cluster Kubernetes et le pool de nœuds. L’exécution peut prendre quelques minutes. Dans cet exemple, le groupe de ressources est **contosoResourceGroup** et le cluster Kubernetes est **contosoAKSCluster**.
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
1. Affichez les métadonnées du cluster AKS que vous avez créé à l’aide de la commande ci-dessous. Copiez les valeurs de **principalId**, **clientId**, **subscriptionId** et **nodeResourceGroup**.
    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    Voici la sortie avec les deux paramètres mis en surbrillance.
    
    ![Image](../media/kubernetes-key-vault-5.png) ![Image](../media/kubernetes-key-vault-6.png)
    
## <a name="install-helm-and-secrets-store-csi-driver"></a>Installer Helm et le pilote CSI du magasin des secrets

Vous devez installer [Helm](https://helm.sh/docs/intro/install/) pour installer le pilote CSI du magasin des secrets.

L’interface du pilote [CSI du magasin des secrets](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/charts/csi-secrets-store-provider-azure/README.md) vous permet d’obtenir des contenus de secrets stockés dans une instance Azure Key Vault et de monter ces contenus dans des pods Kubernetes.

1. Vérifiez que la version de Helm est v3 ou supérieure :
    ```azurecli
    helm version
    ```
1. Installez le pilote CSI du magasin des secrets et le fournisseur Azure Key Vault pour le pilote :
    ```azurecli
    helm repo add csi-secrets-store-provider-azure https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/charts

    helm install csi-secrets-store-provider-azure/csi-secrets-store-provider-azure --generate-name
    ```

## <a name="create-an-azure-key-vault-and-set-secrets"></a>Créer un coffre de clés Azure et définir des secrets

Suivez ce [guide](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-cli) pour créer votre propre coffre de clés et définir vos secrets.

**Remarque :** Vous n’avez pas besoin d’utiliser Azure Cloud Shell ni de créer un groupe de ressources. L’utilisation du groupe de ressources créé précédemment pour le cluster Kubernetes suffit.

## <a name="create-your-own-secretproviderclass-object"></a>Créer votre propre objet SecretProviderClass

Utilisez ce [modèle](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/v1alpha1_secretproviderclass.yaml) fourni pour créer votre propre objet SecretProviderClass personnalisé afin de fournir des paramètres spécifiques au fournisseur pour le pilote CSI du magasin des secrets. Cet objet permet à l’identité d’accéder à votre coffre de clés.

En utilisant l’exemple de fichier YAML SecretProviderClass fourni, vous allez renseigner les paramètres manquants. Les paramètres suivants sont requis :

1.  **userAssignedIdentityID :** ID client du principal du service
1.  **keyvaultName :** nom du coffre de clés
1.  **objects :** cet objet contiendra tout le contenu du secret à monter
    1.  **objectName :** nom du contenu du secret
    1.  **objectType :** type d’objet (secret, clé, certificat)
1.  **resourceGroup :** nom du groupe de ressources
1.  **subscriptionId :** ID d’abonnement du coffre de clés
1.  **tenantID :** ID de locataire (c’est-à-dire, ID de répertoire) du coffre de clés

Vous trouverez ci-dessous le modèle mis à jour ; téléchargez-le en tant que fichier .yaml et renseignez les champs obligatoires correspondants. Dans cet exemple, le coffre de clés est **contosoKeyVault5** et a deux secrets, **secret1** et **secret2**.

**Remarque :** Si vous utilisez des identités managées, le champ **usePodIdentity** doit avoir la valeur **true** et le champ **userAssignedIdentityID** ne doit afficher que des guillemets **""** . 

```yaml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: azure-kvname
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"                   # [REQUIRED] Set to "true" if using managed identities
    useVMManagedIdentity: "false"             # [OPTIONAL] if not provided, will default to "false"
    userAssignedIdentityID: "servicePrincipalClientID"       # [REQUIRED] If using a Service Principal, use the client id to specify which user assigned managed identity to use. If using a user assigned identity as the VM's managed identity, then specify the identity's client id. If empty, then defaults to use the system assigned identity on the VM
                                                             #     az ad sp show --id http://contosoServicePrincipal --query appId -o tsv
                                                             #     the above command will return the Client ID of your service principal
    keyvaultName: "contosoKeyVault5"          # [REQUIRED] the name of the Key Vault
                                              #     az keyvault show --name contosoKeyVault5
                                              #     the above command will displays the Key Vault metadata, which includes the subscription ID, resource group name, Key Vault 
    cloudName: ""                             # [OPTIONAL for Azure] if not provided, azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1                 # [REQUIRED] object name
                                              #     az keyvault secret list --vault-name “contosoKeyVault5”
                                              #     the above command will display a list of secret names from your Key Vault
          objectType: secret                  # [REQUIRED] object types: secret, key or cert
          objectVersion: ""                   # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: secret2
          objectType: secret
          objectVersion: ""
    resourceGroup: "contosoResourceGroup"     # [REQUIRED] the resource group name of the Key Vault
    subscriptionId: "subscriptionID"          # [REQUIRED] the subscription ID of the Key Vault
    tenantId: "tenantID"                      # [REQUIRED] the tenant ID of the Key Vault
```
Vous trouverez ci-dessous la sortie de la console pour « az keyvault show --name contosoKeyVault5 » avec les métadonnées en surbrillance appropriées :

![Image](../media/kubernetes-key-vault-2.png)

## <a name="assign-your-service-principal-or-use-managed-identities"></a>Affecter votre principal du service ou utiliser des identités managées

### <a name="using-service-principal"></a>Utilisation du principal du service

Si vous utilisez un principal du service, vous devez lui accorder l’autorisation d’accéder à votre coffre de clés et de récupérer des secrets. Affectez le rôle **« Lecteur »** et accordez au principal du service l’autorisation d’ **« obtenir »** des secrets de votre coffre de clés en effectuant les étapes ci-dessous :

1. Affectez le principal du service au coffre de clés existant. Le paramètre **$AZURE_CLIENT_ID** correspond à la valeur **appId** que vous avez copiée après la création de votre principal du service.
    ```azurecli
    az role assignment create --role Reader --assignee $AZURE_CLIENT_ID --scope /subscriptions/$SUBID/resourcegroups/$KEYVAULT_RESOURCE_GROUP/providers/Microsoft.KeyVault/vaults/$KEYVAULT_NAME
    ```

    Voici la sortie de la commande : 

    ![Image](../media/kubernetes-key-vault-3.png)

1. Accordez au principal du service l’autorisation d’obtenir des secrets :
    ```azurecli
    az keyvault set-policy -n $KEYVAULT_NAME --secret-permissions get --spn $AZURE_CLIENT_ID
    ```

1. Maintenant que vous avez configuré votre principal du service pour qu’il dispose de l’autorisation de lire les secrets du coffre de clés, **$AZURE_CLIENT_SECRET** correspond à la valeur **password** de votre principal du service. Ajoutez les informations d’identification du principal de service en tant que secret Kubernetes accessible par le pilote CSI du magasin des secrets :
    ```azurecli
    kubectl create secret generic secrets-store-creds --from-literal clientid=$AZURE_CLIENT_ID --from-literal clientsecret=$AZURE_CLIENT_SECRET
    ```

**Remarque :** Si vous recevez une erreur plus tard lors du déploiement du pod Kubernetes sur un ID de secret client non valide, il est possible que vous possédiez un ID de secret client plus ancien qui a expiré ou qui a été réinitialisé. Pour résoudre ce problème, supprimez vos secrets « secrets-store-creds » et créez-en un autre avec l’ID de secret client actuel. Exécutez la commande ci-dessous pour supprimer « secrets-store-creds » :
```azurecli
kubectl delete secrets secrets-store-creds
```

Si vous avez oublié l’ID de secret client de votre principal du service, vous pouvez le réinitialiser à l’aide de la commande suivante :

```azurecli
az ad sp credential reset --name contosoServicePrincipal --credential-description "APClientSecret" --query password -o tsv
```

### <a name="using-managed-identities"></a>Utilisation d’identités managées

Si vous utilisez des identités managées, affectez des rôles spécifiques au cluster AKS que vous avez créé. 
1. Pour créer/lister/lire une identité managée affectée par l’utilisateur, le rôle [Contributeur d’identité managée](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-identity-contributor) doit être affecté à votre cluster AKS. Vérifiez que **$clientId** correspond au cluster Kubernetes.

    ```azurecli
    az role assignment create --role "Managed Identity Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    ```

1. Installez l’identité Azure Active Directory (Azure AD) dans AKS.
    ```azurecli
    helm repo add aad-pod-identity https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts

    helm install pod-identity aad-pod-identity/aad-pod-identity
    ```

1. Créez une identité Azure AD. Copiez les valeurs de **clientId** et **principalId**.
    ```azurecli
    az identity create -g $resourceGroupName -n $identityName
    ```

1. Affectez le rôle de lecteur à l’identité Azure AD que vous venez de créer pour votre coffre de clés. Ensuite, donnez à l’identité l’autorisation d’obtenir des secrets à partir de votre coffre de clés. Vous allez utiliser les valeurs de **clientId** et **principalId** à partir de l’identité Azure que vous venez de créer.
    ```azurecli
    az role assignment create --role "Reader" --assignee $principalId --scope /subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/contosoResourceGroup/providers/Microsoft.KeyVault/vaults/contosoKeyVault5

    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-key-vault"></a>Déployer votre pod avec des secrets montés à partir de Key Vault

La commande ci-dessous permet de configurer votre objet SecretProviderClass :
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="using-service-principal"></a>Utilisation du principal du service

Si vous utilisez un principal du service, la commande ci-dessous déploie vos pods Kubernetes avec l’objet SecretProviderClass et les secrets secrets-store-creds que vous avez configurés. Voici le modèle pour le déploiement de [Linux](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/nginx-pod-secrets-store-inline-volume-secretproviderclass.yaml) et [Windows](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/windows-pod-secrets-store-inline-volume-secret-providerclass.yaml).
```azurecli
kubectl apply -f updateDeployment.yaml
```

### <a name="using-managed-identities"></a>Utilisation d’identités managées

Si vous utilisez des identités managées, vous allez créer un élément **AzureIdentity** dans votre cluster qui fera référence à l’identité que vous avez créée précédemment. Ensuite, créez un élément **AzureIdentityBinding** qui fera référence à l’élément **AzureIdentity** que vous avez créé. Utilisez le modèle ci-dessous, remplissez les paramètres correspondants, puis enregistrez-le en tant que **podIdentityAndBinding.yaml**.  
```yml
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentity
metadata:
    name: "azureIdentityName"               # The name of your Azure Idenity
spec:
    type: 0                                 # Set type: 0 for Managed Service Identity
    resourceID: /subscriptions/<SUBID>/resourcegroups/<RESOURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<AZUREIDENTITYNAME>
    clientID: "managedIdentityClientId"     # The clientId of your Azure Identity that you created earlier
---
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentityBinding
metadata:
    name: azure-pod-identity-binding
spec:
    azureIdentity: "azureIdentityName"      # The name of your Azure Idenity
    selector: azure-pod-identity-binding-selector
```
    
Exécutez la commande suivante pour exécuter la liaison :

```azurecli
kubectl apply -f podIdentityAndBinding.yaml
```

Vient ensuite le déploiement réel du pod. Vous trouverez ci-dessous le fichier YAML de déploiement qui utilisera la liaison d’identité du pod de la dernière étape. Enregistrez ce fichier en tant que **podBindingDeployment.yaml**.

```yml
kind: Pod
apiVersion: v1
metadata:
    name: nginx-secrets-store-inline
    labels:
    aadpodidbinding: azure-pod-identity-binding-selector
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
### <a name="check-status-and-secret-content"></a>Vérifier l’état et le contenu du secret 
Pour afficher les pods que vous avez déployés :
```azurecli
kubectl get pods
```

Pour vérifier l’état de votre pod, utilisez la commande suivante :
```azurecli
kubectl describe pod/nginx-secrets-store-inline
```

![Image](../media/kubernetes-key-vault-4.png)

Le pod déployé doit être dans l’état « En cours d’exécution ». Dans la section « Événements » en bas, tous les types d’événements à gauche sont classés comme « Normal ».
Une fois que vous avez vérifié que le pod est en cours d’exécution, vous pouvez vérifier qu’il dispose des secrets de votre coffre de clés.

Pour afficher tous les secrets détenus par le pod :
```azurecli
kubectl exec -it nginx-secrets-store-inline -- ls /mnt/secrets-store/
```

Pour obtenir le contenu d’un secret spécifique :
```azurecli
kubectl exec -it nginx-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

Vérifiez le contenu du secret affiché.

## <a name="next-steps"></a>Étapes suivantes

Vérifiez que le coffre de clés est récupérable :
> [!div class="nextstepaction"]
> [Activer la suppression réversible](https://docs.microsoft.com/azure/key-vault/general/soft-delete-clid)
