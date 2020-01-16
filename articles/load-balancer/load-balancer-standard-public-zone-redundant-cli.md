---
title: Équilibrer la charge de machines virtuelles redondantes interzone à l’aide d’Azure CLI
titleSuffix: Azure Load Balancer
description: Créer une instance publique de Standard Load Balancer avec un frontend interzone redondant à l’aide d’Azure CLI
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/09/2018
ms.author: allensu
ms.openlocfilehash: c33c1efd735a9c606ebe4625eb704005fff64a9e
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896061"
---
#  <a name="load-balance-vms-across-all-availability-zones-using-azure-cli"></a>Équilibrer la charge de machines virtuelles entre toutes les zones de disponibilité avec Azure CLI

Cet article détaille les étapes de la création d’un instance publique de [Standard Load Balancer](https://aka.ms/azureloadbalancerstandard) avec un frontend interzone redondant pour obtenir une redondance interzone sans dépendance sur plusieurs enregistrements DNS. Une adresse IP de serveur frontal unique est automatiquement redondante interzone.  Avec une adresse IP unique et un frontend redondant dans une zone pour votre équilibreur de charge, vous pouvez maintenant atteindre toutes les machines virtuelles d’un réseau virtuel au sein d’une région parmi toutes les zones de disponibilité. Utilisez les zones de disponibilité pour protéger vos applications et vos données dans l’éventualité peu probable d’une défaillance ou d’une perte d’un centre de données entier.

Pour plus d’informations sur l’utilisation des zones de disponibilité avec un équilibreur de charge standard, voir [Équilibreur de charge standard et zones de disponibilité](load-balancer-standard-availability-zones.md).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0.17 ou une version ultérieure pour poursuivre la procédure décrite dans ce didacticiel.  Pour connaître la version de l’interface, exécutez `az --version`. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI]( /cli/azure/install-azure-cli). 

> [!NOTE]
> La prise en charge des zones de disponibilité est assurée pour certaines ressources, régions et familles de tailles de machine virtuelle Azure. Pour bien démarrer avec les zones de disponibilité, et pour plus d’informations sur les ressources, les régions et les familles de tailles de machine virtuelle Azure pour lesquelles vous pouvez tester les zones de disponibilité, consultez [Vue d’ensemble des zones de disponibilité](https://docs.microsoft.com/azure/availability-zones/az-overview). Pour obtenir de l’aide, vous pouvez nous contacter sur [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) ou [ouvrir un ticket de support Azure](../azure-portal/supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az-group-create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

L’exemple suivant crée un groupe de ressources nommé *myResourceGroupSLB* à l’emplacement *westeurope* :

```azurecli-interactive
az group create \
--name myResourceGroupSLB \
--location westeurope
```

## <a name="create-a-zone-redundant-public-ip-standard"></a>Créer une adresse IP publique standard redondante dans une zone
Pour accéder à votre application sur Internet, vous avez besoin d’une adresse IP publique pour l’équilibreur de charge. Un serveur frontal redondant interzone est servi par toutes les zones de disponibilité d’une région simultanément. Créez une adresse IP publique redondante de zone avec la commande [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create). Par défaut, lorsque vous créez une adresse IP publique standard, elle est redondante dans une zone.

L’exemple suivant crée une adresse IP publique redondante dans une zone nommée *myPublicIP* dans le groupe de ressources *myResourceGroupLoadBalancer*.

```azurecli-interactive
az network public-ip create \
--resource-group myResourceGroupSLB \
--name myPublicIP \
--sku Standard
```

## <a name="create-azure-standard-load-balancer"></a>Créer une instance d’Azure Standard Load Balancer
Cette section explique en détail comment vous pouvez créer et configurer les composants suivants de l’équilibreur de charge :
- Un pool IP frontal qui reçoit le trafic réseau entrant sur l’équilibreur de charge.
- Un pool d’IP principal où le pool frontal envoie le trafic réseau dont la charge a été équilibrée.
- Une sonde d’intégrité qui détermine l’intégrité des instances de machine virtuelle principales.
- Une règle d’équilibreur de charge qui définit la distribution du trafic vers les machines virtuelles.

### <a name="create-the-load-balancer"></a>Créer l’équilibreur de charge
Créez un équilibreur de charge standard avec la commande [az network lb create](/cli/azure/network/lb#az-network-lb-create). L’exemple suivant crée un équilibreur de charge nommé *myLoadBalancer* et affecte l’adresse *myPublicIP* à la configuration IP frontale.

```azurecli-interactive
az network lb create \
--resource-group myResourceGroupSLB \
--name myLoadBalancer \
--public-ip-address myPublicIP \
--frontend-ip-name myFrontEnd \
--backend-pool-name myBackEndPool \
--sku Standard
```

## <a name="create-health-probe-on-port-80"></a>Créer une sonde d’intégrité sur le port 80

Une sonde d’intégrité vérifie toutes les instances de machine virtuelle pour s’assurer qu’elles peuvent transmettre le trafic réseau. L’instance de machine virtuelle présentant des contrôles de sonde défaillants est supprimée de l’équilibrage de charge jusqu’à ce qu’elle revienne en ligne et que la sonde valide son intégrité. Créez une sonde d’intégrité à l’aide de la commande az network lb probe create pour surveiller l’intégrité des machines virtuelles. Pour créer une sonde d’intégrité TCP, utilisez la commande [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create). L’exemple suivant permet de créer une sonde d’intégrité nommée *myHealthProbe* :

```azurecli-interactive
az network lb probe create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myHealthProbe \
--protocol tcp \
--port 80
```

## <a name="create-load-balancer-rule-for-port-80"></a>Créer une règle d’équilibreur de charge pour le port 80
Une règle d’équilibreur de charge définit la configuration IP frontale pour le trafic entrant et le pool d’IP principal pour recevoir le trafic, ainsi que le port source et le port de destination requis. Créez une règle d’équilibreur de charge *myLoadBalancerRuleWeb* à l’aide de la commande [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) pour écouter le port 80 dans le pool frontal *myFrontEndPool* et envoyer le trafic réseau équilibré en charge vers le pool d’adresses principal *myBackEndPool* à l’aide du port 80 également.

```azurecli-interactive
az network lb rule create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myLoadBalancerRuleWeb \
--protocol tcp \
--frontend-port 80 \
--backend-port 80 \
--frontend-ip-name myFrontEnd \
--backend-pool-name myBackEndPool \
--probe-name myHealthProbe
```

## <a name="configure-virtual-network"></a>Configurer un réseau virtuel
Avant de déployer des machines virtuelles et de pouvoir tester votre équilibreur de charge, créez les ressources de réseau virtuel de prise en charge.

### <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Créez un réseau virtuel nommé *myVnet* avec un sous-réseau nommé *mySubnet* dans le myResourceGroup à l’aide de la commande [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create).


```azurecli-interactive
az network vnet create \
--resource-group myResourceGroupSLB \
--location westeurope \
--name myVnet \
--subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Créer un groupe de sécurité réseau

Créez un groupe de sécurité réseau nommé *myNetworkSecurityGroup* pour définir les connexions entrantes à votre réseau virtuel avec [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create).

```azurecli-interactive
az network nsg create \
--resource-group myResourceGroupSLB \
--name myNetworkSecurityGroup
```

Créez une règle de groupe de sécurité réseau nommée *myNetworkSecurityGroupRule* pour le port 80 avec [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create).

```azurecli-interactive
az network nsg rule create \
--resource-group myResourceGroupSLB \
--nsg-name myNetworkSecurityGroup \
--name myNetworkSecurityGroupRule \
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
Créez trois cartes réseau virtuelles à l’aide de la commande [az network nic create](/cli/azure/network/nic#az-network-nic-create) et associez-les à l’adresse IP publique et au groupe de sécurité réseau. L’exemple suivant crée six cartes réseau virtuelles. (Une carte d’interface réseau virtuelle pour chaque machine virtuelle que vous créez pour votre application dans les étapes suivantes). Vous pouvez créer des machines virtuelles et des cartes d’interface réseau virtuelles supplémentaires à tout moment et les ajouter à l’équilibreur de charge :

```azurecli-interactive
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupSLB \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```
## <a name="create-backend-servers"></a>Créer des serveurs principaux
Dans cet exemple, vous créez trois machines virtuelles situées en zone 1, zone 2 et zone 3, pour qu’elles soient utilisées comme des serveurs principaux pour l’équilibreur de charge. Vous installez également NGINX sur les machines virtuelles pour vérifier que l’équilibreur de charge a bien été créé.

### <a name="create-cloud-init-config"></a>Créer une configuration cloud-init

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

### <a name="create-the-zonal-virtual-machines"></a>Créer les machines virtuelles de zone
Créez les machines virtuelles avec [az vm create](/cli/azure/vm#az-vm-create) dans la zone 1, la zone 2 et la zone 3. L’exemple suivant crée une machine virtuelle dans chaque zone et génère des clés SSH si elles n’existent pas déjà :

Créez une machine virtuelle dans chaque zone (zone 1, zone2 et zone 3) de l’emplacement *westeurope*.

```azurecli-interactive
for i in `seq 1 3`; do
  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --zone $i \
    --custom-data cloud-init.txt
done
```
## <a name="test-the-load-balancer"></a>Tester l’équilibreur de charge

Pour obtenir l’adresse IP publique de l’équilibreur de charge, utilisez la commande [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
Vous pouvez alors entrer l’adresse IP publique dans un navigateur web. N’oubliez pas : patientez quelques minutes jusqu’à ce que les machines virtuelles soient prêtes pour que l’équilibreur de charge commence à répartir du trafic vers ces dernières. L’application s’affiche, avec notamment le nom d’hôte de la machine virtuelle sur laquelle l’équilibreur de charge a distribué le trafic comme dans l’exemple suivant :

![Exécution de l’application Node.js](./media/load-balancer-standard-public-zone-redundant-cli/running-nodejs-app.png)

Pour voir l’équilibreur de charge répartir le trafic entre les machines virtuelles dans les trois zones de disponibilité exécutant votre application, vous pouvez arrêter une machine virtuelle dans une zone particulière et actualiser votre navigateur.

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur la [référence Standard de Load Balancer](./load-balancer-standard-overview.md)



