---
title: 'Tutoriel : Activer le complément Ingress Controller pour un cluster AKS existant avec une passerelle Azure Application Gateway existante'
description: Ce tutoriel vous permet d’activer le complément Ingress Controller pour votre cluster AKS existant avec une passerelle Application Gateway existante
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: bfff962f6d302f589acc437550fa25f76ec7ce35
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102040417"
---
# <a name="tutorial-enable-application-gateway-ingress-controller-add-on-for-an-existing-aks-cluster-with-an-existing-application-gateway"></a>Tutoriel : Activer le module complémentaire Application Gateway Ingress Controller pour un cluster AKS existant avec une passerelle applicative existante

Vous pouvez utiliser Azure CLI ou le portail pour activer le module complémentaire [Application Gateway Ingress Controller (AGIC)](ingress-controller-overview.md) pour un cluster [Azure Kubernetes Services (AKS)](https://azure.microsoft.com/services/kubernetes-service/) existant. Ce tutoriel vous explique comment utiliser le complément AGIC pour exposer votre application Kubernetes dans un cluster AKS existant par le biais d’une passerelle Application Gateway existante qui a été déployée dans des réseaux virtuels distincts. Vous commencerez par créer un cluster AKS dans un réseau virtuel et une passerelle Application Gateway dans un autre réseau virtuel pour simuler des ressources existantes. Vous allez ensuite activer le module complémentaire AGIC, appairer les deux réseaux virtuels et déployer un exemple d’application qui sera exposé par le biais de la passerelle applicative à l’aide du module complémentaire AGIC. Si vous activez le complément AGIC pour une passerelle Application Gateway existante et un cluster AKS existant dans le même réseau virtuel, vous pouvez ignorer l’étape d’appairage ci-dessous. Le complément permet de déployer AGIC dans votre cluster AKS beaucoup plus rapidement qu’avec la [méthode Helm utilisée auparavant](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on). De plus, il offre une expérience entièrement managée.  

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer un groupe de ressources 
> * Créer un cluster AKS 
> * Créer une passerelle Application Gateway 
> * Activer le module complémentaire AGIC dans le cluster AKS existant via Azure CLI 
> * Activer le module complémentaire AGIC dans le cluster AKS existant via le portail 
> * Appairer le réseau virtuel Application Gateway et le réseau virtuel du cluster AKS
> * Déployer un exemple d’application à l’aide du complément AGIC pour l’entrée sur le cluster AKS
> * Vérifier que l’application est accessible par le biais de la passerelle Application Gateway

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Dans Azure, vous allouez les ressources associées à un groupe de ressources. Créez un groupe de ressources à l’aide de la commande [az group create](/cli/azure/group#az-group-create). L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement (région) *canadacentral*. 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-a-new-aks-cluster"></a>Déployer un nouveau cluster AKS

Vous allez maintenant déployer un nouveau cluster AKS pour simuler la présence d’un cluster AKS existant dans lequel vous voulez activer le complément AGIC.  

Dans l’exemple suivant, vous déployez un nouveau cluster AKS nommé *myCluster* en utilisant [Azure CNI](../aks/concepts-network.md#azure-cni-advanced-networking) et des [identités managées](../aks/use-managed-identity.md) dans le groupe de ressources que vous avez créé, *myResourceGroup*.

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity 
```

Pour configurer des paramètres supplémentaires dans la commande `az aks create`, consultez les informations de référence disponibles [ici](/cli/azure/aks#az-aks-create). 

## <a name="deploy-a-new-application-gateway"></a>Déployer une nouvelle passerelle Application Gateway 

Vous allez maintenant déployer une nouvelle passerelle Application Gateway afin de simuler la présence d’une passerelle Application Gateway existante que vous comptez ensuite utiliser pour équilibrer la charge du trafic vers votre cluster AKS, *myCluster*. La passerelle Application Gateway s’appellera *myApplicationGateway*, mais vous devez d’abord créer une ressource IP publique, nommée *myPublicIp* ainsi qu’un autre réseau virtuel appelé *myVnet* avec l’espace d’adressage 11.0.0.0/8 et un sous-réseau avec l’espace d’adressage 11.1.0.0/16 appelé *mySubnet*. Vous devez aussi déployer votre passerelle Application Gateway dans *mySubnet* avec *myPublicIp*. 

Quand vous utilisez un cluster AKS et une passerelle Application Gateway dans des réseaux virtuels distincts, les espaces d’adressage de ces deux réseaux ne doivent pas se chevaucher. L’espace d’adressage par défaut dans lequel un cluster AKS est déployé est 10.0.0.0/8. Nous définissons donc le préfixe d’adresse du réseau virtuel Application Gateway sur 11.0.0.0/8. 

```azurecli-interactive
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16 
az network application-gateway create -n myApplicationGateway -l canadacentral -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> Le complément Application Gateway Ingress Controller (AGIC) prend en charge **uniquement** les références SKU Application Gateway v2 (Standard et WAF), **pas** les références SKU Application Gateway v1. 

## <a name="enable-the-agic-add-on-in-existing-aks-cluster-through-azure-cli"></a>Activer le module complémentaire AGIC dans un cluster AKS existant via Azure CLI 

Si vous souhaitez continuer à utiliser Azure CLI, vous pouvez continuer à activer le module complémentaire AGIC dans le cluster AKS que vous avez créé, *myCluster*, et spécifier le module complémentaire AGIC pour qu’il utilise la passerelle applicative existante que vous avez créée, *myApplicationGateway*.

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

## <a name="enable-the-agic-add-on-in-existing-aks-cluster-through-portal"></a>Activer le module complémentaire AGIC dans un cluster AKS existant via la portail 

Si vous souhaitez utiliser portail Azure pour activer le module complémentaire AGIC, accédez à [(https://aka.ms/azure/portal/aks/agic)](https://aka.ms/azure/portal/aks/agic), puis à votre cluster AKS via le lien du portail. À partir de là, accédez à l’onglet Mise en réseau au sein de votre cluster AKS. Vous verrez une section Application Gateway Ingres Controller, qui vous permet d’activer ou de désactiver le module complémentaire du contrôleur d’entrée à l’aide de l’interface utilisateur du portail. Activez la case en regard de « Activer le contrôleur d’entrée », puis sélectionnez la passerelle applicative que vous avez créée, *myApplicationGateway* dans le menu déroulant. 

![Portail du contrôleur d’entrée d’Application Gateway](./media/tutorial-ingress-controller-add-on-existing/portal-ingress-controller-add-on.png)

## <a name="peer-the-two-virtual-networks-together"></a>Appairer les deux réseaux virtuels

Étant donné que nous avons déployé le cluster AKS dans son propre réseau virtuel et la passerelle Application Gateway dans un autre réseau virtuel, vous devez appairer les deux réseaux virtuels afin de rendre possible le trafic entre la passerelle Application Gateway et les pods dans le cluster. L’appairage des deux réseaux virtuels s’effectue en exécutant la commande Azure CLI deux fois pour garantir une connexion bidirectionnelle. La première commande crée une connexion d’appairage du réseau virtuel Application Gateway vers le réseau virtuel AKS. La seconde commande crée une connexion d’appairage dans l’autre direction.

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```

## <a name="deploy-a-sample-application-using-agic"></a>Déployer un exemple d’application utilisant le complément AGIC 

Vous allez maintenant déployer un exemple d’application dans le cluster AKS que vous avez créé, qui utilisera le complément AGIC pour l’entrée et connectera la passerelle Application Gateway au cluster AKS. Tout d’abord, exécutez la commande `az aks get-credentials` pour obtenir les informations de connexion au cluster AKS que vous avez déployé. 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

Une fois que vous avez les informations de connexion au cluster que vous avez créé, exécutez la commande suivante afin de configurer un exemple d’application qui utilise AGIC pour l’entrée au cluster. AGIC met à jour la passerelle Application Gateway que vous avez configurée précédemment avec les règles de routage correspondantes vers le nouvel exemple d’application que vous avez déployé.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>Vérifier que l’application est accessible

Maintenant que la passerelle Application Gateway est configurée pour envoyer le trafic vers le cluster AKS, vous devez vérifier que votre application est accessible. Recherchez d’abord l’adresse IP de l’entrée. 

```azurecli-interactive
kubectl get ingress
```

Vérifiez que l’exemple d’application que vous avez créé est opérationnel en accédant à l’adresse IP de la passerelle Application Gateway que vous avez obtenue en exécutant la commande ci-dessus ou en la cherchant avec `curl`. L’obtention de la mise à jour par Application Gateway peut prendre jusqu’à une minute. Si la passerelle Application Gateway est toujours dans l’état « Mise à jour » sur le portail, patientez jusqu’à la fin du processus avant de tenter d’atteindre l’adresse IP. 

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources, la passerelle d’application et toutes les ressources associées.

```azurecli-interactive
az group delete --name myResourceGroup 
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur la désactivation du complément AGIC](./ingress-controller-disable-addon.md)
