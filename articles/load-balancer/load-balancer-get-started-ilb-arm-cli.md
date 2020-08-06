---
title: Créer un équilibreur de charge interne - Azure CLI
titleSuffix: Azure Load Balancer
description: Dans cet article, vous découvrirez comment créer un équilibreur de charge interne avec Azure CLI
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/02/2020
ms.author: allensu
ms.openlocfilehash: 9a1587efc07cf852fe9389cc9bf39e693373ef89
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87502322"
---
# <a name="create-an-internal-load-balancer-to-load-balance-vms-using-azure-cli"></a>Créer un équilibreur de charge interne pour équilibrer la charge de machines virtuelles avec Azure CLI

Cet article explique comment créer un équilibreur de charge interne pour équilibrer la charge des machines virtuelles. Pour tester l’équilibreur de charge, vous déployez deux machines virtuelles exécutant un serveur Ubuntu pour équilibrer la charge d’une application web.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, vous devez exécuter Azure CLI version 2.0.28 ou ultérieure pour poursuivre la procédure décrite dans ce tutoriel. Pour connaître la version de l’interface, exécutez `az --version`. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](https://docs.microsoft.com/cli/azure/group). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

L’exemple suivant crée un groupe de ressources nommé *myResourceGroupILB* à l’emplacement *eastus* :

```azurecli-interactive
  az group create \
    --name myResourceGroupILB \
    --location eastus
```

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Créez un réseau virtuel nommé *myVnet* avec un sous-réseau nommé *mySubnet* dans *myResourceGroup* à l’aide de la commande [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet).

```azurecli-interactive
  az network vnet create \
    --name myVnet \
    --resource-group myResourceGroupILB \
    --location eastus \
    --subnet-name mySubnet
```

## <a name="create-standard-load-balancer"></a>Créer un Standard Load Balancer

Cette section explique en détail comment vous pouvez créer et configurer les composants suivants de l’équilibreur de charge :
  - Une configuration IP frontale qui reçoit le trafic réseau entrant sur l’équilibreur de charge.
  - Un pool d’IP principal où le pool frontal envoie le trafic réseau dont la charge a été équilibrée.
  - Une sonde d’intégrité qui détermine l’intégrité des instances de machine virtuelle principales.
  - Une règle d’équilibreur de charge qui définit la distribution du trafic vers les machines virtuelles.

### <a name="create-the-load-balancer"></a>Créer l’équilibreur de charge

Créez un équilibreur de charge interne à l’aide de la commande [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest), nommé **myLoadBalancer**, qui inclut une configuration IP de back-end nommée **myFrontEnd**, un pool de back-ends nommé **myBackEndPool** qui est associé à une adresse IP privée **10.0.0.7**. 

Utilisez `--sku basic` pour créer un équilibreur de charge de base. Microsoft recommande de sélectionner la référence SKU Standard pour les charges de travail de production.

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupILB \
    --name myLoadBalancer \
    --sku standard \
    --frontend-ip-name myFrontEnd \
    --private-ip-address 10.0.0.7 \
    --backend-pool-name myBackEndPool \
    --vnet-name myVnet \
    --subnet mySubnet      
```

### <a name="create-the-health-probe"></a>Créer la sonde d’intégrité

Une sonde d’intégrité vérifie toutes les instances de machine virtuelle pour s’assurer qu’elles peuvent recevoir le trafic réseau. L’instance de machine virtuelle présentant des contrôles de sonde défaillants est supprimée de l’équilibrage de charge jusqu’à ce qu’elle revienne en ligne et que la sonde valide son intégrité. Créez une sonde d’intégrité à l’aide de la commande [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) pour surveiller l’intégrité des machines virtuelles. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupILB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-the-load-balancer-rule"></a>Créer la règle d’équilibreur de charge

Une règle d’équilibreur de charge définit la configuration IP frontale pour le trafic entrant et le pool d’IP principal pour recevoir le trafic, ainsi que le port source et le port de destination requis. Créez une règle d’équilibreur de charge *myHTTPRule* à l’aide de la commande [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) pour écouter le port 80 dans le pool frontal *myFrontEnd* et envoyer le trafic réseau équilibré en charge vers le pool d’adresses principal *myBackEndPool* à l’aide du port 80 également. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupILB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```

Vous pouvez également créer une règle d’équilibreur de charge sur les [ports HA](load-balancer-ha-ports-overview.md) à l’aide de la configuration ci-dessous avec la version standard de Load Balancer.

```azurecli-interactive
az network lb rule create --resource-group myResourceGroupILB --lb-name myLoadBalancer --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name myFrontEnd --backend-address-pool-name myBackEndPool
```

## <a name="create-servers-for-the-backend-address-pool"></a>Créer des serveurs pour le pool d’adresses principal

Avant de déployer des machines virtuelles et de pouvoir tester votre équilibreur de charge, créez les ressources de réseau virtuel de prise en charge.

### <a name="create-nics"></a>Créer des cartes réseau

Créez deux interfaces réseau à l’aide de la commande [az network nic create](/cli/azure/network/nic#az-network-nic-create) et associez-les à l’adresse IP privée. 

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupILB \
    --name myNic$i \
    --vnet-name myVnet \
    --subnet mySubnet \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
done
```

## <a name="create-backend-servers"></a>Créer des serveurs principaux

Dans cet exemple, vous créez deux machines virtuelles à utiliser en tant que serveurs principaux pour l’équilibreur de charge. Pour vérifier que l’équilibreur de charge a bien été créé, vous installez également NGINX sur les machines virtuelles.

### <a name="create-an-availability-set"></a>Créer un groupe à haute disponibilité

Créer un groupe à haute disponibilité à l’aide de la commande [az vm availability-set create](/cli/azure/network/nic)

```azurecli-interactive
  az vm availability-set create \
    --resource-group myResourceGroupILB \
    --name myAvailabilitySet
```

### <a name="create-two-virtual-machines"></a>Créer deux machines virtuelles

Vous pouvez utiliser un fichier de configuration cloud-init pour installer NGINX et exécuter une application Node.js « Hello World » sur une machine virtuelle Linux. Dans l’interpréteur de commandes actuel, créez un fichier nommé cloud-init.txt et collez la configuration suivante dans l’interpréteur de commandes. Vérifiez que vous copiez bien l’intégralité du fichier cloud-init, en particulier la première ligne :

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

Créez les machines virtuelles avec la commande [az vm create](/cli/azure/vm#az-vm-create).

```azurecli-interactive
for i in `seq 1 2`; do
  az vm create \
    --resource-group myResourceGroupILB \
    --name myVM$i \
    --availability-set myAvailabilitySet \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
    done
```

Le déploiement des machines virtuelles peut nécessiter quelques minutes.

### <a name="create-a-vm-for-testing-the-load-balancer"></a>Créer une machine virtuelle pour tester l’équilibreur de charge

Pour tester l’équilibreur de charge, créez une machine virtuelle, *myVMTest*, et associez-la à *myNic3*.

```azurecli-interactive
 az vm create \
    --resource-group myResourceGroupILB \
    --name myVMTest \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password myPassword123456!
```

## <a name="test-the-internal-load-balancer"></a>Tester l’équilibreur de charge interne

Pour tester l’équilibreur de charge, vous devez tout d’abord obtenir l’adresse IP privée de l’équilibreur de charge. Connectez-vous ensuite à la machine virtuelle myVMTest et saisissez l’adresse IP privée dans la barre d’adresse de son navigateur web.

Pour obtenir l’adresse IP privée de l’équilibreur de charge, utilisez la commande [az network lb show](/cli/azure/network/public-ip). Copiez l’adresse IP privée, puis collez-la dans la barre d’adresse d’un navigateur web de votre machine virtuelle, *myVMTest*.

```azurecli-interactive
  az network lb show \
    --name myLoadBalancer \
    --resource-group myResourceGroupILB
```

![Tester l’équilibreur de charge](./media/load-balancer-get-started-ilb-arm-cli/load-balancer-test.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, vous pouvez utiliser la commande [az group delete](/cli/azure/group#az-group-delete) pour supprimer le groupe de ressources, l’équilibreur de charge et toutes les ressources associées.

```azurecli-interactive
  az group delete --name myResourceGroupILB
```


## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez créé un équilibreur de charge de base interne, associé des machines virtuelles à celui-ci, configuré la règle de trafic d’équilibreur de charge, la sonde d’intégrité, puis testé l’équilibreur de charge. Pour en savoir plus sur les équilibreurs de charge et leurs ressources associées, consultez les articles de procédures.
