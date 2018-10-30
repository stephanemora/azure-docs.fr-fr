---
title: Tutoriel - Acheminer le trafic web selon l’URL - Azure CLI
description: Découvrez comment acheminer le trafic web selon l’URL vers des pools évolutifs spécifiques de serveurs à l’aide d’Azure CLI.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 10/25/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: f683d63683e903d947d0789a16a8efa48196d36a
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50026192"
---
# <a name="tutorial-route-web-traffic-based-on-the-url-using-the-azure-cli"></a>Tutoriel - Acheminer le trafic web selon l’URL à l’aide d’Azure CLI

Vous pouvez utiliser Azure CLI pour configurer l’acheminement du trafic web vers des pools de serveurs évolutifs spécifiques selon l’URL utilisée pour accéder à votre application. Dans ce tutoriel, vous créez une [passerelle d’application Azure](application-gateway-introduction.md) avec trois pools backend à l’aide de [groupes de machines virtuelles identiques](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Chaque pool backend a un usage spécifique comme les données courantes, les images et la vidéo.  Avec l’acheminement du trafic vers des pools distincts, vos clients ont la garantie d’obtenir les informations dont ils ont besoin lorsqu’ils en ont besoin.

Pour activer l’acheminement du trafic, vous créez des [règles d’acheminement](application-gateway-url-route-overview.md) assignées à des écouteurs qui écoutent des ports spécifiques pour veiller à ce que le trafic web arrive sur les serveurs appropriés dans les pools.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurer le réseau
> * Créer des écouteurs, un mappage de chemins d’URL et des règles
> * Créer des pools backend scalables

![Exemple d’acheminement d’URL](./media/tutorial-url-route-cli/scenario.png)

Si vous préférez, vous pouvez suivre ce didacticiel en utilisant [Azure PowerShell](tutorial-url-route-powershell.md) ou le [portail Azure](create-url-route-portal.md).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, ce tutoriel nécessite l’exécution d’Azure CLI version 2.0.4 ou ultérieure. Pour connaître la version de l’interface, exécutez `az --version`. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Créez un groupe de ressources à l’aide de la commande [az group create](/cli/azure/group#create).

L’exemple suivant crée un groupe de ressources nommé *myResourceGroupAG* à l’emplacement *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Créer des ressources réseau 

Créez le réseau virtuel nommé *myVNet* et le sous-réseau nommé *myAGSubnet* à l’aide de la commande [az network vnet create](/cli/azure/network/vnet#az-net). Ajoutez ensuite le sous-réseau nommé *myBackendSubnet* nécessaire aux serveurs backend à l’aide de la commande [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network_vnet_subnet_create). Créez l’adresse IP publique nommée *myAGPublicIPAddress* à l’aide de la commande [az network public-ip create](/cli/azure/network/public-ip#az-network_public_ip_create).

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24

az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24

az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-the-application-gateway-with-url-map"></a>Créer la passerelle d’application avec une carte d’URL

Utilisez la commande [az network application-gateway create](/cli/azure/network/application-gateway#create) pour créer une passerelle d’application nommée *myAppGateway*. Quand vous créez une passerelle d’application avec Azure CLI, vous spécifiez des informations de configuration, telles que la capacité, la référence SKU et les paramètres HTTP. La passerelle d’application est affectée à *myAGSubnet* et à *myAGPublicIPAddress*, que vous avez créés. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

 La création de la passerelle d’application peut prendre plusieurs minutes. Une fois la passerelle d’application créée, vous pouvez voir ces nouvelles fonctionnalités :

- *appGatewayBackendPool* : une passerelle d’application doit avoir au moins un pool d’adresses backend.
- *appGatewayBackendHttpSettings* : spécifie que le port 80 et le protocole HTTP sont utilisés pour la communication.
- *appGatewayHttpListener* : écouteur par défaut associé à *appGatewayBackendPool*.
- *appGatewayFrontendIP* - assigne *myAGPublicIPAddress* à *appGatewayHttpListener*.
- *rule1* : règle d’acheminement par défaut associée à *appGatewayHttpListener*.


### <a name="add-image-and-video-backend-pools-and-port"></a>Ajouter le port et les pools principaux image et vidéo

Ajoutez des pools principaux nommés *imagesBackendPool* et *videoBackendPool* à votre passerelle d’application à l’aide de la commande [az network application-gateway address-pool create](/cli/azure/network/application-gateway#az-network_application_gateway_address-pool_create). Pour ajouter le port frontal des pools, utilisez la commande [az network application-gateway frontend-port create](/cli/azure/network/application-gateway#az-network_application_gateway_frontend_port_create). 

```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name imagesBackendPool

az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name videoBackendPool

az network application-gateway frontend-port create \
  --port 8080 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name port8080
```

### <a name="add-backend-listener"></a>Ajouter un écouteur principal

Ajoutez l’écouteur principal nommé *backendListener*, nécessaire pour acheminer le trafic, à l’aide de la commande [az network application-gateway http-listener create](/cli/azure/network/application-gateway#az-network_application_gateway_http_listener_create).


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port port8080 \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-url-path-map"></a>Ajouter une carte de chemins d’accès URL

Les cartes de chemin d’accès URL assurent que certaines URL sont acheminées vers des pools principaux spécifiques. Créez des cartes de chemins d’accès URL nommées *imagePathRule* et *videoPathRule* à l’aide des commandes [az network application-gateway url-path-map create](/cli/azure/network/application-gateway#az-network_application_gateway_url_path_map_create) et [az network application-gateway url-path-map rule create](/cli/azure/network/application-gateway#az-network_application_gateway_url_path_map_rule_create).

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name myPathMap \
  --paths /images/* \
  --resource-group myResourceGroupAG \
  --address-pool imagesBackendPool \
  --default-address-pool appGatewayBackendPool \
  --default-http-settings appGatewayBackendHttpSettings \
  --http-settings appGatewayBackendHttpSettings \
  --rule-name imagePathRule

az network application-gateway url-path-map rule create \
  --gateway-name myAppGateway \
  --name videoPathRule \
  --resource-group myResourceGroupAG \
  --path-map-name myPathMap \
  --paths /video/* \
  --address-pool videoBackendPool
```

### <a name="add-routing-rule"></a>Ajouter une règle d’acheminement

La règle d’acheminement associe les cartes d’URL à l’écouteur créé. Ajoutez une règle nommée *rule2* à l’aide de la commande [az network application-gateway rule create](/cli/azure/network/application-gateway#az-network_application_gateway_rule_create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name rule2 \
  --resource-group myResourceGroupAG \
  --http-listener backendListener \
  --rule-type PathBasedRouting \
  --url-path-map myPathMap \
  --address-pool appGatewayBackendPool
```

## <a name="create-virtual-machine-scale-sets"></a>Créer des groupes de machines virtuelles identiques

Ce tutoriel crée trois groupes de machines virtuelles identiques prenant en charge les trois pools principaux qui ont été créés. Ils sont nommés *myvmss1*, *myvmss2* et *myvmss3*. Chacun contient deux instances de machines virtuelles sur lesquelles NGINX sera installé.

```azurecli-interactive
for i in `seq 1 3`; do

  if [ $i -eq 1 ]
  then
    poolName="appGatewayBackendPool" 
  fi

  if [ $i -eq 2 ]
  then
    poolName="imagesBackendPool"
  fi

  if [ $i -eq 3 ]
  then
    poolName="videoBackendPool"
  fi

  az vmss create \
    --name myvmss$i \
    --resource-group myResourceGroupAG \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password Azure123456! \
    --instance-count 2 \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --vm-sku Standard_DS2 \
    --upgrade-policy-mode Automatic \
    --app-gateway myAppGateway \
    --backend-pool-name $poolName
done
```

### <a name="install-nginx"></a>Installer NGINX

```azurecli-interactive
for i in `seq 1 3`; do
  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"], "commandToExecute": "./install_nginx.sh" }'
done
```

## <a name="test-the-application-gateway"></a>Tester la passerelle d’application

Pour obtenir l’adresse IP publique de la passerelle d’application, utilisez la commande [az network public-ip show](/cli/azure/network/public-ip#az-network_public_ip_show). Copiez l’adresse IP publique, puis collez-la dans la barre d’adresses de votre navigateur. Par exemple, *http://40.121.222.19*, *http://40.121.222.19:8080/images/test.htm* ou *http://40.121.222.19:8080/video/test.htm*.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Tester l’URL de base dans la passerelle d’application](./media/tutorial-url-route-cli/application-gateway-nginx.png)

Remplacez l’URL par http://&lt;ip-address&gt;:8080/images/test.html, en indiquant votre adresse IP à la place de &lt;ip-address&gt;, de façon à voir apparaître quelque chose ressemblant à l’exemple suivant :

![Tester l’URL images dans la passerelle d’application](./media/tutorial-url-route-cli/application-gateway-nginx-images.png)

Changez l’URL en http://&lt;ip-address&gt;:8080/video/test.html, en remplaçant &lt;ip-address&gt; par votre adresse IP, de façon à voir apparaître quelque chose ressemblant à l’exemple suivant.

![Tester l’URL vidéo dans la passerelle d’application](./media/tutorial-url-route-cli/application-gateway-nginx-video.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources, la passerelle d’application et toutes les ressources associées.

```azurecli-interactive
az group delete --name myResourceGroupAG --location eastus
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Configurer le réseau
> * Créer des écouteurs, un mappage de chemins d’URL et des règles
> * Créer des pools backend évolutifs

> [!div class="nextstepaction"]
> [Créer une passerelle d’application avec réacheminement par chemin d’URL](./tutorial-url-redirect-cli.md)
