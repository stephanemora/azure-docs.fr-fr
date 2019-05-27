---
title: Afficher un aperçu, pour créer un conteneur Windows Server sur un cluster Azure Kubernetes Service (AKS)
description: Découvrez comment créer un cluster Kubernetes rapidement, de déployer une application dans un conteneur Windows Server dans Azure Kubernetes Service (ACS) à l’aide de l’interface CLI.
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: zarhoads
ms.openlocfilehash: 328fb9707c7151b8696cfb55e13567db90e45b7f
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991151"
---
# <a name="preview---create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Afficher un aperçu, pour créer un conteneur Windows Server sur un cluster Azure Kubernetes Service (AKS) à l’aide de l’interface CLI Azure

AKS (Azure Kubernetes Service) est un service Kubernetes managé qui vous permet de déployer et de gérer rapidement des clusters. Dans cet article, vous déployez un cluster AKS à l’aide de l’interface CLI. Vous permet également de déployer un exemple d’application ASP.NET dans un conteneur Windows Server au cluster.

Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire.

![Image de la navigation vers l’exemple d’application ASP.NET](media/windows-container/asp-net-sample-app.png)

Cet article suppose une compréhension élémentaire des concepts de Kubernetes. Pour plus d’informations, consultez [Concepts de base de Kubernetes pour AKS (Azure Kubernetes Service)][kubernetes-concepts].

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et utiliser l’interface CLI localement, cet article nécessite que vous exécutiez Azure CLI version 2.0.61 ou version ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer Azure CLI 2.0][azure-cli-install].

## <a name="before-you-begin"></a>Avant de commencer

Vous devez ajouter un pool de nœuds supplémentaires après avoir créé votre cluster qui peut exécuter des conteneurs Windows Server. Ajout d’un pool de nœud supplémentaire est couverte dans une étape ultérieure, mais vous devez d’abord activer quelques fonctionnalités en version préliminaire.

> [!IMPORTANT]
> Fonctionnalités de préversion AKS sont libre-service et participer. Les préversions sont fournies pour recueillir des commentaires et des bogues à partir de notre communauté. Toutefois, ils ne sont pas pris en charge par le support technique Azure. Si vous créez un cluster, ou ajoutez ces fonctionnalités à des clusters existants, ce cluster est non pris en charge jusqu'à ce que la fonctionnalité n’est plus disponible en version préliminaire et atteignent à la disposition générale (GA).
>
> Si vous rencontrez des problèmes avec les fonctionnalités en version préliminaire, [de signaler un problème sur le référentiel GitHub d’AKS][aks-github] par le nom de la fonctionnalité d’aperçu dans le titre du bogue.

### <a name="install-aks-preview-cli-extension"></a>Installer l’extension CLI de préversion d’aks
    
Les commandes CLI pour créer et gérer plusieurs pools de nœud sont disponibles dans le *aks-preview* extension CLI. Installer le *aks-preview* extension Azure CLI à l’aide du [az extension ajoutez] [ az-extension-add] commande, comme indiqué dans l’exemple suivant :

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> Si vous avez installé précédemment le *aks-preview* extension, installez toutes des mises à jour à l’aide de la `az extension update --name aks-preview` commande.

### <a name="register-windows-preview-feature"></a>Inscrire la fonctionnalité en version préliminaire de Windows

Pour créer un cluster AKS qui peut utiliser plusieurs pools de nœuds et exécuter des conteneurs Windows Server, commencez par activer la *WindowsPreview* indicateurs sur votre abonnement de fonctionnalités. Le *WindowsPreview* fonctionnalité utilise également les clusters du pool de plusieurs nœuds et machines virtuelles identiques pour gérer le déploiement et la configuration des nœuds Kubernetes. Inscrire le *WindowsPreview* à l’aide de la fonctionnalité indicateur le [register de fonctionnalité az] [ az-feature-register] commande comme indiqué dans l’exemple suivant :

```azurecli-interactive
az feature register --name WindowsPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> N’importe quel cluster ACS que vous créez une fois que vous avez correctement inscrit le *WindowsPreview* indicateur de fonctionnalité utiliser cette expérience de cluster en préversion. Pour continuer à créer des clusters normales et entièrement pris en charge, n’activez pas les fonctionnalités en version préliminaire sur les abonnements de production. Utilisez un test distinct ou un abonnement Azure de développement pour le test des fonctionnalités en version préliminaire.

Quelques minutes sont nécessaires pour que l’état s’affiche *Registered* (Inscrit). Vous pouvez vérifier l'état de l'enregistrement à l'aide de la commande [az feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WindowsPreview')].{Name:name,State:properties.state}"
```

Lorsque vous êtes prêt, actualisez l’inscription du fournisseur de ressources *Microsoft.ContainerService* à l’aide de la commande [az provider register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Limites

Les limitations suivantes s’appliquent lorsque vous créez et gérez les clusters AKS prenant en charge plusieurs pools de nœud :

* Plusieurs pools de nœud sont disponibles pour les clusters créés une fois que vous avez correctement inscrit le *WindowsPreview*. Plusieurs pools de nœuds sont également disponibles si vous vous inscrivez le *MultiAgentpoolPreview* et *VMSSPreview* fonctionnalités pour votre abonnement. Impossible d’ajouter ou de gérer des pools de nœuds avec un cluster ACS existant créé avant que ces fonctionnalités ont été correctement inscrits.
* Vous ne pouvez pas supprimer le premier pool de nœud.

Bien que cette fonctionnalité est disponible en version préliminaire, les limitations supplémentaires suivantes s’appliquent :

* Le cluster AKS peut avoir un maximum de huit pools de nœuds.
* Le cluster AKS peut avoir un maximum de 400 nœuds dans ces pools de huit nœuds.
* Le nom de pool de nœud Windows Server a une limite de 6 caractères.
* Pools de nœuds Windows Server ne sont pas disponibles dans les régions du Canada pour l’instant.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources Azure est un groupe logique dans lequel des ressources Azure sont déployées et gérées. Lorsque vous créez un groupe de ressources, vous devez spécifier un emplacement. Il s’agit de l’emplacement de stockage des métadonnées de groupe de ressources. C’est également là que vos ressources s’exécutent dans Azure si vous ne spécifiez pas une autre région lors de la création de ressources. Créez un groupe de ressources à l’aide de la commande [az group create][az-group-create].

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

L’exemple de sortie suivant montre que le groupe de ressources a été créé correctement :

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

## <a name="create-aks-cluster"></a>Créer un cluster ACS
Pour exécuter un cluster AKS qui prend en charge les pools de nœuds pour les conteneurs Windows Server, votre cluster doit utiliser une stratégie de réseau qui utilise [Azure CNI] [ azure-cni-about] plug-in de réseau (Avancé). Pour plus d’informations pour aider à planifier les plages de sous-réseau requise et les considérations relatives au réseau, consultez [configurer la mise en réseau Azure CNI][use-advanced-networking]. Utilisez le [créer az aks] [ az-aks-create] commande pour créer un cluster AKS nommé *myAKSCluster*. Cette commande crée les ressources réseau nécessaires si elles n’existent pas.
  * Le cluster est configuré avec un seul nœud
  * Le *-admin-mot de passe windows* et *windows-admin-username* paramètres définir les informations d’identification d’administrateur pour tous les conteneurs Windows Server créées sur le cluster.

Fournir votre propre sécurisé *PASSWORD_WIN*.

```azurecli-interactive
PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --enable-addons monitoring \
    --kubernetes-version 1.14.0 \
    --generate-ssh-keys \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --enable-vmss \
    --network-plugin azure
```

Au bout de quelques minutes, la commande se termine et retourne des informations au format JSON sur le cluster.

## <a name="add-a-windows-server-node-pool"></a>Ajouter un pool de nœud Windows Server

Par défaut, un cluster AKS est créé avec un pool de nœuds qui permettre exécuter des conteneurs Linux. Utilisez `az aks nodepool add` commande pour ajouter un pool de nœud supplémentaires qui peut s’exécuter de conteneurs Windows Server.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1 \
    --kubernetes-version 1.14.0
```

La commande ci-dessus crée un nouveau pool de nœud nommé *npwin* et l’ajoute à la *myAKSCluster*. Lorsque vous créez un pool de nœuds pour exécuter des conteneurs Windows Server, la valeur par défaut *taille de machine virtuelle de nœud* est *Standard_D2s_v3*. Si vous choisissez de configurer le *taille de machine virtuelle de nœud* paramètre, veuillez consulter la liste de [restreint les tailles de machine virtuelle][restricted-vm-sizes]. La taille minimale recommandée est *Standard_D2s_v3*. La commande ci-dessus utilise également le sous-réseau par défaut dans le réseau virtuel par défaut créé lors de l’exécution `az aks create`.

## <a name="connect-to-the-cluster"></a>Connexion au cluster

Pour gérer un cluster Kubernetes, vous utilisez [kubectl][kubectl], le client de ligne de commande Kubernetes. Si vous utilisez Azure Cloud Shell, `kubectl` est déjà installé. Pour installer `kubectl` en local, utilisez la commande [az aks install-cli][az-aks-install-cli] :

```azurecli
az aks install-cli
```

Exécutez la commande [az aks get-credentials][az-aks-get-credentials] pour configurer `kubectl` afin de vous connecter à votre cluster Kubernetes. Cette commande télécharge les informations d’identification et configure l’interface CLI Kubernetes pour les utiliser.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Pour vérifier la connexion à votre cluster, utilisez la commande [kubectl get][kubectl-get] pour retourner une liste des nœuds du cluster.

```azurecli-interactive
kubectl get nodes
```

L’exemple de sortie suivant montre le nœud unique créé au cours des étapes précédentes. Vérifiez que l’état du nœud est *Ready* :

```
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.0
aksnpwin987654                      Ready    agent   108s   v1.14.0
```

## <a name="run-the-application"></a>Exécution de l'application

Un fichier manifeste Kubernetes définit un état souhaité pour le cluster, notamment les images conteneur à exécuter. Dans cet article, un manifeste est utilisé pour créer tous les objets nécessaires pour exécuter l’exemple d’application ASP.NET dans un conteneur Windows Server. Ce manifeste comprend un [déploiement Kubernetes] [ kubernetes-deployment] pour l’exemple d’application ASP.NET et une externe [service Kubernetes] [ kubernetes-service] à accéder à l’application à partir d’internet.

L’exemple d’application ASP.NET est fourni dans le cadre de la [exemples .NET Framework] [ dotnet-samples] et s’exécute dans un conteneur Windows Server. AKS nécessite des conteneurs Windows Server doit être basé sur les images de *Windows Server 2019* ou supérieur. Le fichier manifeste doit également définir de Kubernetes un [sélecteur de nœud] [ node-selector] pour indiquer à votre cluster AKS pour exécuter les pod de votre application d’exemple ASP.NET sur un nœud qui peut exécuter des conteneurs Windows Server.

Créez un fichier nommé `sample.yaml`, et copiez-y la définition YAML suivante. Si vous utilisez Azure Cloud Shell, vous pouvez créer ce fichier à l’aide de `vi` ou de `nano` comme si vous travailliez sur un système virtuel ou physique :

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800m
          requests:
            cpu: .1
            memory: 300m
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

Déployez l’application à l’aide de la commande [kubectl apply][kubectl-apply] et spécifiez le nom de votre manifeste YAML :

```azurecli-interactive
kubectl apply -f sample.yaml
```

L’exemple de sortie suivant montre le déploiement et le Service créé avec succès :

```
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Test de l’application

Quand l’application s’exécute, un service Kubernetes expose le front-end de l’application sur Internet. L’exécution de ce processus peut prendre plusieurs minutes.

Pour surveiller la progression, utilisez la commande [kubectl get service][kubectl-get] avec l’argument `--watch`.

```azurecli-interactive
kubectl get service sample --watch
```

Initialement le *EXTERNAL-IP* pour le *exemple* service est indiqué en tant que *en attente*.

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quand l’adresse *EXTERNAL-IP* passe de l’état *pending* à une adresse IP publique réelle, utilisez `CTRL-C` pour arrêter le processus de surveillance `kubectl`. L’exemple de sortie suivant montre une adresse IP publique valide affectée au service :

```
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Pour voir l’exemple d’application en action, ouvrez un navigateur web à l’adresse IP externe de votre service.

![Image de la navigation vers l’exemple d’application ASP.NET](media/windows-container/asp-net-sample-app.png)

## <a name="delete-cluster"></a>Supprimer le cluster

Lorsque vous n’avez plus besoin du cluster, utilisez la commande [az group delete][az-group-delete] pour supprimer le groupe de ressources, le service conteneur et toutes les ressources associées.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Lorsque vous supprimez le cluster, le principal de service Azure Active Directory utilisé par le cluster AKS n’est pas supprimé. Pour obtenir des instructions sur la façon de supprimer le principal de service, consultez [Considérations principales et suppression du principal de service AKS][sp-delete].

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous déployé un cluster Kubernetes et déployé un exemple d’application ASP.NET dans un conteneur Windows Server à ce dernier. [Accédez au tableau de bord web Kubernetes][kubernetes-dashboard] pour le cluster que vous venez de créer.

Pour en savoir plus sur ACS et parcourir le code complet de l’exemple de déploiement, passez au tutoriel sur le cluster Kubernetes.

> [!div class="nextstepaction"]
> [Tutoriel ACS][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[aks-github]: https://github.com/azure/aks/issues
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: https://aka.ms/coingfonboarding
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest#az-aks-browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-provider-register]: /cli/azure/provider#az-provider-register
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[use-advanced-networking]: configure-advanced-networking.md
