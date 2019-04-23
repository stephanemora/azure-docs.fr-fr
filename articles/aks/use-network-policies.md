---
title: Sécuriser les pods avec des stratégies réseau dans Azure Kubernetes Service (AKS)
description: Apprenez à sécuriser le trafic qui transite vers et depuis des pods à l’aide de stratégies de réseau Kubernetes dans Azure Kubernetes Service (ACS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/08/2019
ms.author: iainfou
ms.openlocfilehash: 29180d6c1bb5f0991a4f33c3b7c9418f84d8260c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59785892"
---
# <a name="preview---secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Version préliminaire - sécuriser le trafic entre les pods à l’aide de stratégies de réseau dans Azure Kubernetes Service (ACS)

Lorsque vous exécutez des applications modernes, basées sur des microservices dans Kubernetes, vous souhaitez la plupart du temps décider des composants qui peuvent communiquer entre eux. Le principe du moindre privilège doit être appliqué à la façon dont le trafic peut transiter entre les pods dans un cluster Azure Kubernetes Service (AKS). Supposons que vous avez probablement souhaitez bloquer le trafic directement vers les applications back-end. Le *stratégie réseau* fonctionnalité dans Kubernetes vous permet de définir les règles de trafic entrant et sortant entre les pods dans un cluster.

Cet article vous montre comment installer le moteur de stratégie de réseau et de créer des stratégies de réseau Kubernetes pour contrôler le flux du trafic entre les pods dans ACS. Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire.

> [!IMPORTANT]
> Fonctionnalités de préversion AKS sont libre-service et participer. Les préversions sont fournies pour recueillir des commentaires et des bogues à partir de notre communauté. Toutefois, ils ne sont pas pris en charge par le support technique Azure. Si vous créez un cluster, ou ajoutez ces fonctionnalités à des clusters existants, ce cluster est non pris en charge jusqu'à ce que la fonctionnalité n’est plus disponible en version préliminaire et atteignent à la disposition générale (GA).
>
> Si vous rencontrez des problèmes avec les fonctionnalités en version préliminaire, [de signaler un problème sur le référentiel GitHub d’AKS][aks-github] par le nom de la fonctionnalité d’aperçu dans le titre du bogue.

## <a name="before-you-begin"></a>Avant de commencer

Vous avez besoin d’Azure CLI version 2.0.61 ou ultérieur installé et configuré. Exécutez  `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez  [Installation d’Azure CLI 2.0][install-azure-cli].

Pour créer un cluster AKS qui peut utiliser la stratégie réseau, d’abord activer un indicateur de fonctionnalité sur votre abonnement. Pour enregistrer l’indicateur de fonctionnalité *EnableNetworkPolicy*, utilisez la commande [az feature register][az-feature-register], comme indiqué dans l’exemple suivant :

```azurecli-interactive
az feature register --name EnableNetworkPolicy --namespace Microsoft.ContainerService
```

Quelques minutes sont nécessaires pour que l’état s’affiche *Registered* (Inscrit). Vous pouvez vérifier l’état d’inscription à l’aide de la [liste des fonctionnalités az] [ az-feature-list] commande :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableNetworkPolicy')].{Name:name,State:properties.state}"
```

Lorsque vous êtes prêt, actualisez l’inscription de le *Microsoft.ContainerService* fournisseur de ressources à l’aide de la [register de fournisseur az] [ az-provider-register] commande :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-network-policy"></a>Présentation de la stratégie réseau

Tous les pods dans un cluster ACS peuvent envoyer et recevoir du trafic sans limitations, par défaut. Pour améliorer la sécurité, vous pouvez définir des règles qui contrôlent le flux du trafic. Applications back-end sont souvent exposées seulement à des services frontaux requis, par exemple. Ou bien, les composants de base de données sont uniquement accessibles aux couches d’application qui s’y connecter.

Stratégie de réseau est une spécification de Kubernetes qui définit les stratégies d’accès pour la communication entre les Pods. À l’aide de stratégies de réseau, vous définissez un ensemble ordonné de règles pour envoyer et recevoir le trafic et les appliquer à une collection de pods qui correspondent à un ou plusieurs sélecteurs d’étiquette.

Ces règles de stratégie réseau sont définies comme YAML manifestes. Stratégies de réseau peuvent être inclus dans le cadre d’un manifeste plus large qui crée également un déploiement ou un service.

### <a name="network-policy-options-in-aks"></a>Options de stratégie de réseau dans ACS

Azure fournit deux façons d’implémenter la stratégie de réseau. Vous choisissez une option de stratégie réseau lorsque vous créez un cluster AKS. L’option de stratégie ne peut pas être modifiée une fois que le cluster est créé :

* Implémentation d’Azure, appelée *des stratégies de réseau Azure*.
* *Stratégies de réseau tricolore*, un réseau de l’open source et de la solution de sécurité réseau fondé par [Tigera][tigera].

Les deux implémentations utilisent Linux *IPTables* pour appliquer les stratégies spécifiées. Les stratégies sont traduites en ensembles de paires IP autorisées et interdites. Ces paires sont programmées puis en tant que règles de filtre d’IPTable.

Stratégie de réseau ne fonctionne qu’avec l’option Azure CNI (Avancé). Implémentation est différente pour les deux options :

* *Les stratégies de réseau Azure* -le CNI Azure configure un pont dans l’hôte de machine virtuelle pour la mise en réseau intra-nœud. Les règles de filtrage sont appliquées lorsque les paquets passent par le pont.
* *Stratégies de réseau tricolore* -le CNI Azure définit des itinéraires de noyau local pour le trafic intra-nœud. Les stratégies sont appliquées sur l’interface de réseau du pod.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Différences entre Azure et tricolore stratégies et leurs fonctionnalités

| Fonctionnalité                               | Azure                      | Calico                      |
|------------------------------------------|----------------------------|-----------------------------|
| Plateformes prises en charge                      | Linux                      | Linux                       |
| Options de mise en réseau prises en charge             | Azure CNI                  | Azure CNI                   |
| Conformité avec la spécification de Kubernetes | Tous les types de stratégie pris en charge |  Tous les types de stratégie pris en charge |
| Fonctionnalités supplémentaires                      | Aucun                       | Étendue de modèle de stratégie consistant à la stratégie de réseau globale, définir de réseau Global et point de terminaison hôte. Pour plus d’informations sur l’utilisation de la `calicoctl` CLI pour gérer ces étendues des fonctionnalités, consultez [référence de l’utilisateur calicoctl][calicoctl]. |
| Support                                  | Prise en charge par le support Azure et l’équipe d’ingénierie | Prise en charge de la Communauté de tricolore. Pour plus d’informations sur un support payant supplémentaire, consultez [les options de support de projet tricolore][calico-support]. |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Créer un cluster AKS et activer la stratégie réseau

Pour afficher les stratégies de réseau en action, nous allons créer, puis développer une stratégie qui définit le flux de trafic :

* Refuser tout trafic sur un pod.
* Autoriser le trafic en fonction des étiquettes de pod.
* Autoriser le trafic en fonction de l’espace de noms.

Tout d’abord, nous allons créer un cluster AKS qui prend en charge de la stratégie de réseau. La fonctionnalité de stratégie réseau peut uniquement être activée lorsque le cluster est créé. Vous ne pouvez pas activer une stratégie réseau sur un cluster AKS existant.

Pour utiliser la stratégie de réseau avec un cluster AKS, vous devez utiliser le [Azure CNI plug-in] [ azure-cni] et définir votre propre réseau virtuel et les sous-réseaux. Pour de plus amples informations sur la façon de planifier les plages de sous-réseau nécessaires, consultez [Configurer le réseau avancé][use-advanced-networking].

L’exemple de script suivant :

* Crée un réseau virtuel et un sous-réseau.
* Crée un Azure Active Directory (Azure AD) principal de service pour une utilisation avec le cluster AKS.
* Assigne des autorisations *Contributeur* pour le principal du service du cluster AKS sur le réseau virtuel.
* Crée un cluster AKS dans le réseau virtuel défini et Active la stratégie de réseau.
    * Le *azure* option de stratégie réseau est utilisée. Pour utiliser tricolore en tant que l’option de stratégie réseau au lieu de cela, utilisez le `--network-policy calico` paramètre.

Fournissez votre propre *SP_PASSWORD* sécurisé. Vous pouvez remplacer le *RESOURCE_GROUP_NAME* et *nom_cluster* variables :

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
# Enable network policy by using the `--network-policy` parameter
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --kubernetes-version 1.12.6 \
    --generate-ssh-keys \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-policy azure
```

La création du cluster ne prend que quelques minutes. Lorsque le cluster est prêt, configurer `kubectl` pour vous connecter à votre cluster Kubernetes à l’aide de la [az aks get-credentials] [ az-aks-get-credentials] commande. Cette commande télécharge les informations d’identification et configure l’interface CLI Kubernetes pour les utiliser :

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Refuser tout trafic entrant sur un pod

Avant de définir des règles autorisant un trafic réseau particulier, commencez par créer une stratégie réseau pour refuser tout trafic. Cette stratégie vous donne un point de départ pour commencer à la liste verte uniquement le trafic souhaité. Vous pouvez aussi constater aisément que le trafic est ignoré lorsque la stratégie réseau est appliquée.

Pour les règles de trafic et l’environnement d’application exemple, nous allons tout d’abord créer un espace de noms appelé *développement* pour exécuter les pods d’exemple :

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Créer un pod de back-end d’exemple qui exécute NGINX. Ce pod principal permet de simuler un exemple principal basée sur le web d’application. Créez ce pod dans l’espace de noms *development* et ouvrez le port *80* pour gérer le trafic web. Étiquetez le pod avec *app=webapp,role=backend* de façon à pouvoir le cibler avec une stratégie réseau dans la section suivante :

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

Créez un autre module et attacher une session de terminal pour tester que vous pouvez accéder avec succès de la page Web NGINX de valeur par défaut :

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

À l’invite de shell, utilisez `wget` pour confirmer que vous pouvez accéder à la page Web NGINX de valeur par défaut :

```console
wget -qO- http://backend
```

L’exemple de sortie suivant montre que la page Web NGINX de valeur par défaut est retournée :

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Quittez la session de terminal attachée. Le pod de test est automatiquement supprimé.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Créer et appliquer une stratégie réseau

Maintenant que vous avez confirmé que vous pouvez utiliser la page Web NGINX de base sur le pod de back-end d’exemple, créez une stratégie de réseau pour refuser tout le trafic. Créez un fichier nommé `backend-policy.yaml` et collez le manifeste YAML suivant. Ce manifeste utilise un *podSelector* pour attacher la stratégie avec les pods qui ont le *app:webapp, rôle : principal* étiquette, comme votre pod NGINX d’exemple. Aucune règle n’est définie sous *ingress* (entrée), tout trafic entrant sur le pod est donc refusé :

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

Appliquer la stratégie de réseau à l’aide de la [kubectl appliquer] [ kubectl-apply] commande et spécifiez le nom de votre manifeste YAML :

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Tester la stratégie réseau


Nous allons voir si vous pouvez utiliser à nouveau la page Web NGINX sur le pod de back-end. Créez un autre pod de test et attachez une session de terminal :

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

À l’invite de shell, utilisez `wget` pour voir si vous pouvez accéder à la page Web NGINX de valeur par défaut. Cette fois, définissez une valeur de délai d’attente sur *2* secondes. La stratégie de réseau maintenant bloque tout le trafic entrant, la page ne peut pas être chargée, comme indiqué dans l’exemple suivant :

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Quittez la session de terminal attachée. Le pod de test est automatiquement supprimé.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Autoriser le trafic entrant en fonction d’une étiquette de pod

Dans la section précédente, un bloc NGINX principal a été planifié, et une stratégie de réseau a été créée pour refuser tout le trafic. Nous allons créer un pod frontal et mettre à jour de la stratégie de réseau pour autoriser le trafic des pods frontal.

Mettez à jour la stratégie réseau pour autoriser le trafic provenant des pods étiquetés *app:webapp,role:frontend* et dans n’importe quel espace de noms. Modifier le précédent *back-end-policy.yaml* fichier, puis ajoutez *matchLabels* entrée règles afin que votre manifeste ressemble à l’exemple suivant :

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
> Cette stratégie de réseau utilise un élément *namespaceSelector* et un élément *podSelector* pour la règle d’entrée. La syntaxe YAML est importante pour les règles d’entrée soit additifs. Dans cet exemple, les deux éléments doivent correspondre pour que la règle d’entrée soit appliquée. Les versions antérieures à Kubernetes *1.12* ne peut pas interpréter ces éléments correctement et restreindre le trafic réseau comme prévu. Pour plus d’informations sur ce comportement, consultez [comportement de vers et depuis les sélecteurs][policy-rules].

Appliquer la stratégie de réseau mis à jour à l’aide de la [kubectl appliquer] [ kubectl-apply] commande et spécifiez le nom de votre manifeste YAML :

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

Planifier un pod qui porte *app = webapp, rôle = frontend* et d’attachement d’une session de terminal :

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

À l’invite de shell, utilisez `wget` pour voir si vous pouvez accéder à la page Web NGINX de valeur par défaut :

```console
wget -qO- http://backend
```

Étant donné que la règle d’entrée autorise le trafic avec pods qui ont les étiquettes *application : application Web, rôle : serveur frontal*, le trafic à partir du bloc frontal est autorisé. L’exemple de sortie suivant montre la page Web NGINX par défaut retournée :

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Quittez la session de terminal attachée. Le pod est automatiquement supprimé.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Tester un pod sans étiquette correspondante

La stratégie réseau autorise le trafic provenant des pods étiquetés *app: webapp,role: frontend*, mais doit refuser tout autre trafic. Nous allons le tester pour voir si un autre pod sans ces étiquettes peut accéder au pod NGINX back-end. Créez un autre pod de test et attachez une session de terminal :

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

À l’invite de shell, utilisez `wget` pour voir si vous pouvez accéder à la page Web NGINX de valeur par défaut. La stratégie de réseau bloque le trafic entrant, la page ne peut pas être chargé, comme indiqué dans l’exemple suivant :

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Quittez la session de terminal attachée. Le pod de test est automatiquement supprimé.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Autoriser le trafic uniquement à partir d’un espace de noms défini

Les exemples précédents, vous avez créé une stratégie réseau qui a refusé tout le trafic et ensuite mis à jour la stratégie pour autoriser le trafic des pods avec une étiquette spécifique. Un autre besoin courant consiste à limiter le trafic vers uniquement au sein d’un espace de noms donné. Si les exemples précédents ont été pour le trafic dans un *développement* espace de noms, créez une stratégie de réseau qui empêche le trafic à partir d’un autre espace de noms, tel que *production*, d’atteindre les pods.

En premier lieu, créez un espace de noms pour simuler un espace de noms de production :

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Planifiez un pod de test dans l’espace de noms *production* qui est étiqueté en tant que *app=webapp,role=frontend*. Attachez une session de terminal :

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

À l’invite de shell, utilisez `wget` pour confirmer que vous pouvez accéder à la page Web NGINX de valeur par défaut :

```console
wget -qO- http://backend.development
```

Étant donné que les étiquettes pour le pod correspond à ce qui est actuellement autorisé dans la stratégie de réseau, le trafic est autorisé. La stratégie réseau n’examine pas les espaces de noms, elle ne tient compte que des étiquettes de pod. L’exemple de sortie suivant montre la page Web NGINX par défaut retournée :

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Quittez la session de terminal attachée. Le pod de test est automatiquement supprimé.

```console
exit
```

### <a name="update-the-network-policy"></a>Mettre à jour la stratégie réseau

Nous allons mettre à jour la règle d’entrée *namespaceSelector* section pour autoriser uniquement le trafic depuis le *développement* espace de noms. Modifiez le fichier manifeste *backend-policy.yaml*, comme indiqué dans l’exemple suivant :

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

Dans les exemples plus complexes, vous pouvez définir plusieurs règles d’entrée, comme un *namespaceSelector* , puis un *podSelector*.

Appliquer la stratégie de réseau mis à jour à l’aide de la [kubectl appliquer] [ kubectl-apply] commande et spécifiez le nom de votre manifeste YAML :

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Tester la stratégie réseau mise à jour

Planifier un autre pod dans le *production* espace de noms et d’attachement d’une session de terminal :

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

À l’invite de shell, utilisez `wget` pour voir que la stratégie réseau refuse maintenant le trafic :

```console
$ wget -qO- --timeout=2 http://backend.development

wget: download timed out
```

Quittez le pod de test :

```console
exit
```

Avec le trafic refusé à partir de la *production* espace de noms, la planification de nouveau un pod test la *développement* espace de noms et d’attachement d’une session de terminal :

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

À l’invite de shell, utilisez `wget` pour voir que la stratégie de réseau autorise le trafic :

```console
wget -qO- http://backend
```

Le trafic est autorisé, car le pod est planifié dans l’espace de noms que correspond à ce qui est autorisé dans la stratégie de réseau. L’exemple de sortie suivant montre la page Web NGINX par défaut retournée :

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Quittez la session de terminal attachée. Le pod de test est automatiquement supprimé.

```console
exit
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Dans cet article, nous avons créé deux espaces de noms et appliqué de stratégie réseau. Pour nettoyer ces ressources, utilisez le [kubectl supprimer] [ kubectl-delete] commande et spécifier les noms de ressources :

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les ressources réseau, consultez [réseau concepts pour les applications dans Azure Kubernetes Service (ACS)][concepts-network].

Pour en savoir plus sur les stratégies, consultez [stratégies de réseau Kubernetes][kubernetes-network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues]
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/v3.5/reference/calicoctl/
[calico-support]: https://www.projectcalico.org/support

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
