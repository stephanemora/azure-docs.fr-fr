---
title: 'Démarrage rapide : Créer un équilibrage de charge de base - Azure CLI'
titlesuffix: Azure Load Balancer
description: Ce démarrage rapide montre comment créer un équilibreur de charge public à l’aide d’Azure CLI
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
tags: azure-resource-manager
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.custom: mvc
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: allensu
ms.openlocfilehash: 67d137039778b7ec8d5e65acb017b84ad6df5cb2
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274223"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-using-azure-cli"></a>Démarrage rapide : Créer un équilibreur de charge pour équilibrer la charge des machines virtuelles à l’aide d’Azure CLI

Ce démarrage rapide vous explique comment créer un équilibreur de charge Azure pour équilibrer la charge du trafic internet entre les machines virtuelles dans Azure. Pour tester l’équilibreur de charge, vous déployez deux machines virtuelles exécutant un serveur Ubuntu, puis vous équilibrez la charge d’une application web entre les deux.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, vous devez exécuter Azure CLI version 2.0.28 ou ultérieure pour poursuivre la procédure décrite dans ce tutoriel. Pour connaître la version de l’interface, exécutez `az --version`. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](https://docs.microsoft.com/cli/azure/group). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus* :

```azurecli-interactive
  az group create \
    --name myResourceGroupLB \
    --location eastus
```

## <a name="create-a-public-ip-address"></a>Créer une adresse IP publique

Pour accéder à votre application web sur Internet, vous avez besoin d’une adresse IP publique pour l’équilibreur de charge. Utilisez la commande [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) pour créer une adresse IP publique nommée *myPublicIP* dans *myResourceGroupLB*.

```azurecli-interactive
  az network public-ip create --resource-group myResourceGroupLB --name myPublicIP
```

## <a name="create-azure-load-balancer"></a>Créer un équilibreur de charge Azure

Cette section explique en détail comment vous pouvez créer et configurer les composants suivants de l’équilibreur de charge :
  - Un pool IP frontal qui reçoit le trafic réseau entrant sur l’équilibreur de charge.
  - Un pool d’IP principal où le pool frontal envoie le trafic réseau dont la charge a été équilibrée.
  - Une sonde d’intégrité qui détermine l’intégrité des instances de machine virtuelle principales.
  - Une règle d’équilibreur de charge qui définit la distribution du trafic vers les machines virtuelles.

### <a name="create-the-load-balancer"></a>Créer l’équilibreur de charge

Créez un équilibreur de charge Azure public à l’aide de la commande [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) nommé **myLoadBalancer**, qui inclut un pool frontal nommé **myFrontEndPool**, un pool principal nommé **myBackEndPool** qui est associé à l’adresse IP publique **myPublicIP** créée à l’étape précédente.

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupLB \
    --name myLoadBalancer \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool       
  ```

### <a name="create-the-health-probe"></a>Créer la sonde d’intégrité

Une sonde d’intégrité vérifie toutes les instances de machine virtuelle pour s’assurer qu’elles peuvent transmettre le trafic réseau. L’instance de machine virtuelle présentant des contrôles de sonde défaillants est supprimée de l’équilibrage de charge jusqu’à ce qu’elle revienne en ligne et que la sonde valide son intégrité. Créez une sonde d’intégrité à l’aide de la commande [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) pour surveiller l’intégrité des machines virtuelles. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Créer la règle d’équilibreur de charge

Une règle d’équilibreur de charge définit la configuration IP frontale pour le trafic entrant et le pool d’IP principal pour recevoir le trafic, ainsi que le port source et le port de destination requis. Créez une règle d’équilibreur de charge *myLoadBalancerRuleWeb* à l’aide de la commande [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) pour écouter le port 80 dans le pool frontal *myFrontEndPool* et envoyer le trafic réseau équilibré en charge vers le pool d’adresses principal *myBackEndPool* à l’aide du port 80 également. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```

## <a name="configure-virtual-network"></a>Configurer un réseau virtuel

Avant de déployer des machines virtuelles et de pouvoir tester votre équilibreur de charge, créez les ressources de réseau virtuel de prise en charge.

### <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Créez un réseau virtuel nommé *myVnet* avec un sous-réseau nommé *mySubnet* dans *myResourceGroup* à l’aide de la commande [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet).

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupLB \
    --location eastus \
    --name myVnet \
    --subnet-name mySubnet
```
###  <a name="create-a-network-security-group"></a>Créer un groupe de sécurité réseau
Créez un groupe de sécurité réseau pour définir les connexions entrantes vers votre réseau virtuel.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupLB \
    --name myNetworkSecurityGroup
```

### <a name="create-a-network-security-group-rule"></a>Créer une règle de groupe de sécurité réseau

Créez une règle de groupe de sécurité réseau pour autoriser les connexions entrantes via le port 80.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupLB \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleHTTP \
    --protocol tcp \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```
### <a name="create-nics"></a>Créer des cartes réseau

Créez deux interfaces réseau à l’aide de la commande [az network nic create](/cli/azure/network/nic#az-network-nic-create) et associez-les à l’adresse IP publique et au groupe de sécurité réseau. 

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNic$i \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
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
    --resource-group myResourceGroupLB \
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
    --resource-group myResourceGroupLB \
    --name myVM$i \
    --availability-set myAvailabilitySet \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
    --no-wait
    done
```
Le déploiement des machines virtuelles peut nécessiter quelques minutes.

## <a name="test-the-load-balancer"></a>Tester l’équilibreur de charge

Pour obtenir l’adresse IP publique de l’équilibreur de charge, utilisez la commande [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). Copiez l’adresse IP publique, puis collez-la dans la barre d’adresses de votre navigateur.

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
![Tester l’équilibreur de charge](./media/load-balancer-get-started-internet-arm-cli/running-nodejs-app.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, vous pouvez utiliser la commande [az group delete](/cli/azure/group#az-group-delete) pour supprimer le groupe de ressources, l’équilibreur de charge et toutes les ressources associées.

```azurecli-interactive 
  az group delete --name myResourceGroupLB
```


## <a name="next-steps"></a>Étapes suivantes
Dans ce démarrage rapide, vous avez créé un équilibreur de charge de base, associé des machines virtuelles à celui-ci, configuré la règle de trafic d’équilibreur de charge, la sonde d’intégrité, puis testé l’équilibreur de charge. Pour en savoir plus sur Azure Load Balancer, consultez les didacticiels qui lui sont consacrés.

> [!div class="nextstepaction"]
> [Didacticiels Azure Load Balancer](tutorial-load-balancer-basic-internal-portal.md)
