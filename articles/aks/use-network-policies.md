---
title: Sécuriser les pods avec des stratégies réseau dans Azure Kubernetes Service (AKS)
description: Découvrez comment sécuriser le trafic qui transite par des pods à l’aide de stratégies réseau Kubernetes dans Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 02/12/2019
ms.author: iainfou
ms.openlocfilehash: 250c4fc6e51bacc68c965394b9fd430b1b75a52c
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447172"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Sécuriser le trafic entre les pods avec des stratégies réseau dans Azure Kubernetes Service (AKS)

Lorsque vous exécutez des applications modernes, basées sur des microservices dans Kubernetes, vous souhaitez la plupart du temps décider des composants qui peuvent communiquer entre eux. Le principe du privilège minimum doit être appliqué à la façon dont le trafic peut transiter entre les pods dans un cluster AKS. Par exemple, il y a de grandes chances que vous souhaitiez bloquer le trafic directement sur les applications backend. Dans Kubernetes, la fonctionnalité *Network Policy* (Stratégie réseau) vous permet de définir des règles de trafic d’entrée et de sortie entre les pods d’un cluster.

Cet article vous montre comment utiliser des stratégies réseau pour contrôler le flux du trafic entre les pods dans AKS.

> [!IMPORTANT]
> Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire. Les préversions sont à votre disposition, à la condition d’accepter les [conditions d’utilisation supplémentaires][terms-of-use]. Certains aspects de cette fonctionnalité sont susceptibles d’être modifiés avant la mise à disposition générale.

## <a name="before-you-begin"></a>Avant de commencer

Azure CLI 2.0.56 (ou version ultérieure) doit être installé et configuré. Exécutez  `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez  [Installation d’Azure CLI 2.0][install-azure-cli].

Pour créer un AKS avec une stratégie réseau, activez tout d’abord un indicateur de fonctionnalité sur votre abonnement. Pour enregistrer l’indicateur de fonctionnalité *EnableNetworkPolicy*, utilisez la commande [az feature register][az-feature-register], comme indiqué dans l’exemple suivant :

```azurecli-interactive
az feature register --name EnableNetworkPolicy --namespace Microsoft.ContainerService
```

Quelques minutes sont nécessaires pour que l’état s’affiche *Registered* (Inscrit). Vous pouvez vérifier l'état de l'enregistrement à l'aide de la commande [az feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableNetworkPolicy')].{Name:name,State:properties.state}"
```

Lorsque vous êtes prêt, actualisez l’inscription du fournisseur de ressources *Microsoft.ContainerService* à l’aide de la commande [az provider register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-network-policy"></a>Présentation de la stratégie réseau

Par défaut, tous les pods d’un cluster AKS peuvent envoyer et recevoir du trafic sans aucune limite. Pour améliorer la sécurité, vous pouvez définir des règles qui contrôlent le flux du trafic. Par exemple, les applications backend ne sont souvent exposées qu’aux services frontend nécessaires, et les composants de base de données ne sont accessibles qu’aux couches d’application qui s’y connectent.

Les stratégies réseau sont des ressources Kubernetes qui vous permettent de contrôler le flux du trafic entre les pods. Vous pouvez choisir d’autoriser ou de refuser un trafic en fonction de paramètres, tels que des étiquettes attribuées, un espace de noms ou un port de trafic. Les stratégies réseau sont définies en tant que manifestes YAML et peuvent être intégrées à un manifeste plus vaste qui crée également un déploiement ou un service.

Pour voir des stratégies réseau en action, nous allons créer, puis développer une stratégie qui définit le flux de trafic comme suit :

* Refuser tout trafic sur un pod.
* Autoriser le trafic en fonction des étiquettes de pod.
* Autoriser le trafic en fonction de l’espace de noms.

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Créer un cluster AKS et activer la stratégie réseau

La stratégie réseau n’est activable qu’une fois le cluster créé. Vous ne pouvez pas activer une stratégie réseau sur un cluster AKS existant. 

Pour utiliser la stratégie réseau avec un cluster AKS, vous devez utiliser le [plug-in Azure CNI][azure-cni] et définir vos propre réseau et sous-réseaux virtuels. Pour de plus amples informations sur la façon de planifier les plages de sous-réseau nécessaires, consultez [Configurer le réseau avancé][use-advanced-networking].

L’exemple de script suivant :

* Crée un réseau virtuel et un sous-réseau.
* Crée un principal du service Azure Active Directory (AD) pour une utilisation avec le cluster AKS.
* Assigne des autorisations *Contributeur* pour le principal du service du cluster AKS sur le réseau virtuel.
* Crée un cluster AKS dans le réseau virtuel défini et active la stratégie réseau.

Fournissez votre propre *SP_PASSWORD* sécurisé. Si vous le souhaitez, remplacez les variables *RESOURCE_GROUP_NAME* et *CLUSTER_NAME* :

```azurecli-interactive
SP_PASSWORD=mySecurePassword
RESOURCE_GROUP_NAME=myResourceGroup-NP
CLUSTER_NAME=myAKSCluster
LOCATION=canadaeast

# Create a resource group
az group create --name $RESOURCE_GROUP_NAME --location $LOCATION

# Create a virtual network and subnet
az network vnet create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16

# Create a service principal and read in the application ID
SP_ID=$(az ad sp create-for-rbac --password $SP_PASSWORD --skip-assignment --query [appId] -o tsv)

# Wait 15 seconds to make sure that service principal has propagated
echo "Waiting for service principal to propagate..."
sleep 15

# Get the virtual network resource ID
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP_NAME --name myVnet --query id -o tsv)

# Assign the service principal Contributor permissions to the virtual network resource
az role assignment create --assignee $SP_ID --scope $VNET_ID --role Contributor

# Get the virtual network subnet resource ID
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP_NAME --vnet-name myVnet --name myAKSSubnet --query id -o tsv)

# Create the AKS cluster and specify the virtual network and service principal information
# Enable network policy using the `--network-policy` parameter
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --kubernetes-version 1.12.4 \
    --generate-ssh-keys \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-policy calico
```

La création du cluster ne prend que quelques minutes. Ensuite, configurez `kubectl` afin de vous connecter à votre cluster Kubernetes au moyen de la commande [az aks get-credentials][az-aks-get-credentials]. Cette commande télécharge les informations d’identification et configure l’interface CLI Kubernetes pour les utiliser :

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Refuser tout trafic entrant sur un pod

Avant de définir des règles autorisant un trafic réseau particulier, commencez par créer une stratégie réseau pour refuser tout trafic. Cette stratégie vous donne un point de départ pour l’établissement de la liste verte uniquement du trafic souhaité. Vous pouvez aussi constater aisément que le trafic est ignoré lorsque la stratégie réseau est appliquée.

Pour notre exemple d’environnement d’application et de règles de trafic, nous allons tout d’abord créer un espace de noms nommé *development* (développement) pour exécuter nos exemples de pods :

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

À présent, créez un exemple de pod backend qui exécute NGINX. Ce pod principal peut être utilisé pour simuler un exemple d’application web backend. Créez ce pod dans l’espace de noms *development* et ouvrez le port *80* pour gérer le trafic web. Étiquetez le pod avec *app=webapp,role=backend* de façon à pouvoir le cibler avec une stratégie réseau dans la section suivante :

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

Pour vérifier que vous accédez correctement à la page web NGINX par défaut, créez un autre pod et attachez une session de terminal :

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Lorsque vous êtes dans l’invite de l’interpréteur de commandes, utilisez `wget` pour vérifier votre accès à la page web NGINX par défaut :

```console
wget -qO- http://backend
```

L’exemple de sortie suivant montre que la page web NGINX par défaut est retournée :

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Quittez la session de terminal attachée. Le pod de test est automatiquement supprimé :

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Créer et appliquer une stratégie réseau

À présent que vous avez vérifié votre accès à la page web NGINX de base dans l’exemple de pod backend, créez une stratégie réseau pour refuser tout trafic. Créez un fichier nommé `backend-policy.yaml` et collez le manifeste YAML suivant. Ce manifeste utilise *podSelector* pour attacher la stratégie aux pods dotés de l’étiquette *app:webapp,role:backend*, comme votre exemple de pod NGINX. Aucune règle n’est définie sous *ingress* (entrée), tout trafic entrant sur le pod est donc refusé :

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress: []
```

Appliquez la stratégie réseau à l’aide de la commande [kubectl apply][kubectl-apply] et précisez le nom de votre manifeste YAML :

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Tester la stratégie réseau

Voyons si vous pouvez accéder de nouveau à la page web NGINX sur le pod backend. Créez un autre pod de test et attachez une session de terminal :

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Lorsque vous êtes dans l’invite de l’interpréteur de commandes, utilisez `wget` pour voir si vous pouvez accéder à la page web NGINX par défaut. Cette fois, définissez une valeur de délai d’attente sur *2* secondes. La stratégie réseau bloque à présent tout le trafic entrant, la page ne peut donc pas être chargée, comme illustré dans l’exemple suivant :

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Quittez la session de terminal attachée. Le pod de test est automatiquement supprimé :

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Autoriser le trafic entrant en fonction d’une étiquette de pod

À la section précédente, un pod backend NGINX a été planifié, et une stratégie réseau créée pour refuser tout trafic. À présent, nous allons créer un pod frontend et mettre à jour la stratégie réseau pour autoriser le trafic provenant de pods frontend.

Mettez à jour la stratégie réseau pour autoriser le trafic provenant des pods étiquetés *app:webapp,role:frontend* et dans n’importe quel espace de noms. Modifiez le précédent fichier *back-end-policy.yaml*, puis ajoutez des règles d’entrée *matchLabels* afin que votre manifeste ressemble à l’exemple suivant :

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector: {}
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

> [!NOTE]
> Cette stratégie de réseau utilise un élément *namespaceSelector* et un élément *podSelector* pour la règle d’entrée. La syntaxe YAML est importante pour que les règles d’entrée soient additives ou non. Dans cet exemple, les deux éléments doivent correspondre pour que la règle d’entrée soit appliquée. Les versions de Kubernetes antérieures à *1.12* risquent de ne pas interpréter correctement ces éléments et de limiter le trafic réseau comme prévu. Pour plus d’informations, consultez [Comportement des sélecteurs To et From][policy-rules].

Appliquez la stratégie réseau mise à jour à l’aide de la commande [kubectl apply][kubectl-apply] et spécifiez le nom de votre manifeste YAML :

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

Maintenant, planifiez un pod portant l’étiquette *app=webapp,role=frontend* et attachez une session de terminal :

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Lorsque vous êtes dans l’invite de l’interpréteur de commandes, utilisez `wget` pour voir si vous pouvez accéder à la page web NGINX par défaut :

```console
wget -qO- http://backend
```

À partir du moment où la règle d’entrée autorise le trafic avec les pods étiquetés *app: webapp,role: frontend*, le trafic provenant du pod frontend est autorisé. La sortie de l’exemple suivant montre la page web NGINX par défaut qui est retournée :

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Quittez la session de terminal attachée. Le pod est automatiquement supprimé :

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Tester un pod sans étiquette correspondante

La stratégie réseau autorise le trafic provenant des pods étiquetés *app: webapp,role: frontend*, mais doit refuser tout autre trafic. Nous allons vérifier qu’un autre pod sans cette étiquette ne peut pas accéder au pod backend NGINX. Créez un autre pod de test et attachez une session de terminal :

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Lorsque vous êtes dans l’invite de l’interpréteur de commandes, utilisez `wget` pour voir si vous pouvez accéder à la page web NGINX par défaut. La stratégie réseau bloque le trafic entrant et la page ne peut donc pas être chargée, comme montré dans l’exemple suivant :

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Quittez la session de terminal attachée. Le pod de test est automatiquement supprimé :

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Autoriser le trafic uniquement à partir d’un espace de noms défini

Dans les exemples précédents, vous avez créé une stratégie réseau qui a refusé tout trafic, puis vous avez mis à jour la stratégie afin d’autoriser le trafic provenant de pods dotés d’une étiquette particulière. Il est également souvent utile de limiter le trafic à un seul espace de noms donné. Si les précédents exemples se rapportaient au trafic dans un espace de noms *development*, vous avez peut-être envie à présent de créer une stratégie réseau qui empêche le trafic provenant d’un autre espace de noms, tel que *production*, d’atteindre les pods.

En premier lieu, créez un espace de noms pour simuler un espace de noms de production :

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Planifiez un pod de test dans l’espace de noms *production* qui est étiqueté en tant que *app=webapp,role=frontend*. Attachez une session de terminal :

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Lorsque vous êtes dans l’invite de l’interpréteur de commandes, utilisez `wget` pour vérifier votre accès à la page web NGINX par défaut :

```console
wget -qO- http://backend.development
```

Dans la mesure où les étiquettes des pods correspondent à ce qui est actuellement autorisé dans la stratégie réseau, le trafic est autorisé. La stratégie réseau n’examine pas les espaces de noms, elle ne tient compte que des étiquettes de pod. La sortie de l’exemple suivant montre la page web NGINX par défaut qui est retournée :

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Quittez la session de terminal attachée. Le pod de test est automatiquement supprimé :

```console
exit
```

### <a name="update-the-network-policy"></a>Mettre à jour la stratégie réseau

À présent, nous allons mettre à jour la section *namespaceSelector* de la règle d’entrée pour n’autoriser que le trafic provenant de l’espace de noms *development*. Modifiez le fichier manifeste *backend-policy.yaml*, comme indiqué dans l’exemple suivant :

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          purpose: development
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

Dans des exemples plus complexes, vous pouvez définir plusieurs règles d’entrée, par exemple pour utiliser un *namespaceSelector*, puis un *podSelector*.

Appliquez la stratégie réseau mise à jour à l’aide de la commande [kubectl apply][kubectl-apply] et spécifiez le nom de votre manifeste YAML :

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Tester la stratégie réseau mise à jour

À présent, planifiez un autre pod dans l’espace de noms *production* et attachez une session de terminal :

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Lorsque vous êtes dans l’invite de l’interpréteur de commandes, utilisez `wget` pour voir la stratégie réseau refuser désormais le trafic :

```console
$ wget -qO- --timeout=2 http://backend.development

wget: download timed out
```

Quittez le pod de test :

```console
exit
```

En maintenant le refus sur le trafic provenant de l’espace de noms *production*, replanifiez à présent un pod de test dans l’espace de noms *development* et attachez une session de terminal :

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Lorsque vous êtes dans l’invite de l’interpréteur de commandes, utilisez `wget` pour voir la stratégie réseau autoriser le trafic :

```console
wget -qO- http://backend
```

Dès lors que le pod est planifié dans l’espace de noms qui correspond à ce qui est autorisé dans la stratégie réseau, le trafic est autorisé. La sortie de l’exemple suivant montre la page web NGINX par défaut qui est retournée :

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Quittez la session de terminal attachée. Le pod de test est automatiquement supprimé :

```console
exit
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Dans cet article, nous avons créé deux espaces de noms et appliqué une stratégie réseau. Pour nettoyer ces ressources, utilisez la commande [kubectl deletet][kubectl-delete] et spécifiez les noms des ressources comme suit :

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les ressources réseau, voir [Concepts de réseau pour les applications dans AKS (Azure Kubernetes Service)][concepts-network].

Pour en savoir plus sur l’utilisation de stratégies, consultez [Stratégies de réseau Kubernetes][kubernetes-network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
