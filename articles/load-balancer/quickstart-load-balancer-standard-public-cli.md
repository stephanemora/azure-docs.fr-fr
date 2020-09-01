---
title: 'Démarrage rapide : Créer un équilibreur de charge public – Azure CLI'
titleSuffix: Azure Load Balancer
description: Ce démarrage rapide montre comment créer un équilibreur de charge public à l’aide d’Azure CLI
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
Customer intent: I want to create a load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/23/2020
ms.author: allensu
ms.custom: mvc, devx-track-javascript, devx-track-azurecli
ms.openlocfilehash: b437bfa205833594c9e76c6f0d8ff1923f51f117
ms.sourcegitcommit: e2b36c60a53904ecf3b99b3f1d36be00fbde24fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88762706"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-cli"></a>Démarrage rapide : Créer un équilibreur de charge public pour équilibrer la charge des machines virtuelles à l’aide d’Azure CLI

Commencez avec Azure Load Balancer en utilisant le Azure CLI pour créer un équilibreur de charge public et trois machines virtuelles.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure CLI installé localement ou Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Si vous choisissez d’installer et d’utiliser l’interface CLI en local, ce guide de démarrage nécessite Azure CLI version 2.0.28 ou ultérieure. Pour connaître la version de l’interface, exécutez `az --version`. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

Créez un groupe de ressources avec la commande [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) :

* Nommé **myResourceGroupLB**. 
* Dans l’emplacement **eastus**.

```azurecli-interactive
  az group create \
    --name myResourceGroupLB \
    --location eastus
```
---

# <a name="standard-sku"></a>[**Référence Standard**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Il est recommandé de disposer d’un équilibreur de charge de référence SKU Standard pour les charges de travail de production. Pour plus d’informations sur les références SKU, consultez **[Références SKU Azure Load Balancer](skus.md)** .

## <a name="configure-virtual-network"></a>Configurer un réseau virtuel

Avant de déployer des machines virtuelles et de tester votre équilibreur de charge, créez les ressources de réseau virtuel associées.

### <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Créez un réseau virtuel avec la commande [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt) :

* Nommé **myVNet**.
* Préfixe d’adresse **10.1.0.0/16**.
* Sous-réseau nommé **myBackendSubnet**.
* Préfixe du sous-réseau **10.1.0.0/24**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Emplacement **eastus**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupLB \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-network-security-group"></a>Créer un groupe de sécurité réseau

Pour un équilibreur de charge standard, les machines virtuelles à l’adresse principale doivent disposer d’interfaces réseau appartenant à un groupe de sécurité réseau. 

Créez un Groupe de sécurité réseau avec la commande [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) :

* Nommé **myNSG**.
* Dans le groupe de ressources **myResourceGroupLB**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupLB \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Créer une règle de groupe de sécurité réseau

Créez une règle de groupe de sécurité réseau avec la commande [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) :

* Nommé **myNSGRuleHTTP**.
* Dans le groupe de sécurité réseau que vous avez créé à l’étape précédente, **myNSG**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Protocole **(*)** .
* Direction **entrante**.
* Source **(*)** .
* Destination **(*)** .
* Port de destination **Port 80**.
* Accès **Autoriser**.
* Priorité **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupLB \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Créer des interfaces réseau pour les machines virtuelles

Créez les interfaces réseau avec la commande [az network nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) :

#### <a name="vm1"></a>MV1

* Nommée **myNicVM1**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Dans le réseau virtuel **myVNet**.
* Dans le sous-réseau **myBackendSubnet**.
* Dans le groupe de sécurité réseau **myNSG**.

```azurecli-interactive

  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>MV2

* Nommé **myNicVM2**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Dans le réseau virtuel **myVNet**.
* Dans le sous-réseau **myBackendSubnet**.

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm3"></a>MV3

* Nommée **myNicVM3**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Dans le réseau virtuel **myVNet**.
* Dans le sous-réseau **myBackendSubnet**.
* Dans le groupe de sécurité réseau **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM3 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

## <a name="create-backend-servers"></a>Créer des serveurs principaux

Dans cette section, vous allez créer :

* Un fichier de configuration cloud nommé **cloud-init.txt** pour la configuration du serveur.
* Trois machines virtuelles à utiliser en tant que serveurs principaux pour l’équilibreur de charge.

### <a name="create-cloud-init-configuration-file"></a>Créer un fichier de configuration cloud-init

Utilisez un fichier de configuration cloud-init pour installer NGINX et exécuter une application Node.js « Hello World » sur une machine virtuelle Linux. 

Dans votre interpréteur de commandes actuel, créez un fichier nommé cloud-init.txt. Copiez et collez l’extrait de code suivant dans l’interpréteur de commandes. Vérifiez que vous copiez bien le fichier cloud-init entier, en particulier la première ligne :

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
### <a name="create-virtual-machines"></a>Créer des machines virtuelles

Créez les machines virtuelles avec la commande [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) :

#### <a name="vm1"></a>MV1
* Nommée **myVM1**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Attachée à l’interface réseau **myNicVM1**.
* Image de machine virtuelle **UbuntuLTS**.
* Fichier de configuration **cloud-init.txt** que vous avez créé à l’étape précédente.
* Dans **Zone 1**.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 1 \
    --no-wait
    
```
#### <a name="vm2"></a>MV2
* Nommée **myVM2**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Attachée à l’interface réseau **myNicVM2**.
* Image de machine virtuelle **UbuntuLTS**.
* Fichier de configuration **cloud-init.txt** que vous avez créé à l’étape précédente.
* Dans **Zone 2**.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 2 \
    --no-wait
```

#### <a name="vm3"></a>MV3
* Nommée **myVM3**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Attachée à l’interface réseau **myNicVM3**.
* Image de machine virtuelle **UbuntuLTS**.
* Fichier de configuration **cloud-init.txt** que vous avez créé à l’étape précédente.
* Dans **Zone 3**.

```azurecli-interactive
   az vm create \
    --resource-group myResourceGroupLB \
    --name myVM3 \
    --nics myNicVM3 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 3 \
    --no-wait
```
Le déploiement des machines virtuelles peut prendre quelques minutes.

## <a name="create-a-public-ip-address"></a>Créer une adresse IP publique

Pour accéder à votre application web sur Internet, vous avez besoin d’une adresse IP publique pour l’équilibreur de charge. 

Utilisez la commande [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) pour effectuer les opérations suivantes :

* Créer une adresse IP publique redondante dans une zone standard nommée **myPublicIP**.
* Dans **myResourceGroupLB**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --sku Standard
```

Pour créer une adresse IP publique redondante zonale dans la Zone 1, procédez comme suit :

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --sku Standard \
    --zone 1
```

## <a name="create-standard-load-balancer"></a>Créer un équilibreur de charge standard

Cette section explique en détail comment vous pouvez créer et configurer les composants suivants de l’équilibreur de charge :

  * Un pool d’adresses IP frontal qui reçoit le trafic réseau entrant sur l’équilibreur de charge.
  * Un pool d’adresses IP principal auquel le pool frontal envoie le trafic réseau dont la charge est équilibrée.
  * Une sonde d’intégrité qui détermine l’intégrité des instances de machine virtuelle backend.
  * Une règle d’équilibreur de charge qui définit la distribution du trafic aux machines virtuelles.

### <a name="create-the-load-balancer-resource"></a>Créer la ressource d’équilibreur de charge

Créez un équilibreur de charge public avec la commande [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create) :

* Nommé **myLoadBalancer**.
* Un pool frontal nommé **myFrontEnd**.
* Un pool principal nommé **myBackEndPool**.
* Associé à l’adresse IP publique **myPublicIP** que vous avez créé à l’étape précédente. 

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupLB \
    --name myLoadBalancer \
    --sku Standard \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Créer la sonde d’intégrité

Une sonde d’intégrité vérifie toutes les instances de machine virtuelle pour s’assurer qu’elles peuvent transmettre du trafic réseau. 

Une machine virtuelle dont le contrôle de sonde a échoué est supprimée de l’équilibreur de charge. La machine virtuelle est rajoutée à l’équilibreur de charge une fois l’échec résolu.

Créez une sonde d’intégrité avec la commande [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create) :

* Analyse l’intégrité des machines virtuelles.
* Nommée **myHealthProbe.** .
* Protocole **TCP**.
* Surveillant le **Port 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Créer la règle d’équilibreur de charge

Une règle d’équilibreur de charge définit les éléments suivants :

* La configuration IP frontale pour le trafic entrant.
* Le pool d’adresses IP principal qui reçoit le trafic.
* Les ports source et de destination requis. 

Créez une règle d’équilibreur de charge avec la commande [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create) :

* Nommée **myHTTPRule**.
* Écoutant le **Port 80** dans le pool frontal **myFrontEnd**.
* Envoyant le trafic réseau dont la charge est équilibrée au pool d’adresses principal **myBackEndPool** à l’aide du **Port 80**. 
* Utilisant la sonde d’intégrité **myHealthProbe**.
* Protocole **TCP**.
* Activez la traduction d’adresses réseau source (SNAT) sortante à l’aide de l’adresse IP frontale.

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --disable-outbound-snat true 
```
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Ajouter des machines virtuelles au pool de back-ends de l’équilibreur de charge

Ajoutez les machines virtuelles au pool de back-ends avec la commande [az network nic ip-config address-pool add](https://docs.microsoft.com/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add) :

#### <a name="vm1"></a>MV1
* Dans le pool d’adresses de back-end **myBackEndPool**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Associée à l’interface réseau **myNicVM1** et **ipconfig1**.
* Associée à l’équilibreur de charge **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>MV2
* Dans le pool d’adresses de back-end **myBackEndPool**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Associée à l’interface réseau **myNicVM2** et **ipconfig1**.
* Associée à l’équilibreur de charge **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm3"></a>MV3
* Dans le pool d’adresses de back-end **myBackEndPool**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Associée à l’interface réseau **myNicVM3** et **ipconfig1**.
* Associée à l’équilibreur de charge **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM3 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

## <a name="create-outbound-rule-configuration"></a>Créer une configuration de règle de trafic sortant
Les règles de trafic sortant de l’équilibreur de charge configurent la SNAT sortante pour les machines virtuelles dans le pool principal. 

Pour plus d’informations sur les connexions sortantes, consultez [Connexions sortantes dans Azure](load-balancer-outbound-connections.md).

### <a name="create-outbound-public-ip-address-or-public-ip-prefix"></a>Créez une adresse IP publique sortante ou un préfixe d’adresse IP publique.

Utilisez la commande [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) pour créer une adresse IP unique pour la connectivité sortante.  

Utilisez la commande [az network public-ip prefix create](https://docs.microsoft.com/cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-create) pour créer un préfixe d’adresse IP publique pour la connectivité sortante.

Pour plus d’informations sur la mise à l’échelle d’une traduction d’adresses réseau (NAT) sortante et d’une connectivité sortante, consultez [Mettre à l’échelle un NAT sortante avec plusieurs adresses IP](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scale).

#### <a name="public-ip"></a>Adresse IP publique

* Nommée **myPublicIPOutbound**.
* Dans **myResourceGroupLB**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIPOutbound \
    --sku Standard
```

Pour créer une adresse IP publique redondante zonale dans la Zone 1, procédez comme suit :

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIPOutbound \
    --sku Standard \
    --zone 1
```
#### <a name="public-ip-prefix"></a>Préfixe d’adresse IP publique

* Nommé **myPublicIPPrefixOutbound**.
* Dans **myResourceGroupLB**.
* Longueur de préfixe de **28**.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupLB \
    --name myPublicIPPrefixOutbound \
    --length 28
```
Pour créer un préfixe d’adresse IP publique redondante zonale dans la Zone 1 :

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupLB \
    --name myPublicIPPrefixOutbound \
    --length 28 \
    --zone 1
```

### <a name="create-outbound-frontend-ip-configuration"></a>Créer une configuration d’adresse IP frontale sortante

Créez une configuration d’adresse IP frontale avec la commande [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create) :

Sélectionnez les commandes d’adresse IP publique ou de préfixe d’adresse IP publique en fonction de la décision prise à l’étape précédente.

#### <a name="public-ip"></a>Adresse IP publique

* Nommée **myFrontEndOutbound**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Associé à une adresse IP publique **myPublicIPOutbound**.
* Associée avec l’équilibreur de charge **myLoadBalancer**.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myResourceGroupLB \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-address myPublicIPOutbound 
```

#### <a name="public-ip-prefix"></a>Préfixe d’adresse IP publique

* Nommée **myFrontEndOutbound**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Associée avec le préfixe d’adresse IP publique **myPublicIPPrefixOutbound**.
* Associée avec l’équilibreur de charge **myLoadBalancer**.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myResourceGroupLB \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-prefix myPublicIPPrefixOutbound 
```

### <a name="create-outbound-pool"></a>Créer un pool sortant

Créez un pool sortant avec la commande [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create) :

* Nommé **myBackendPoolOutbound**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Associée avec l’équilibreur de charge **myLoadBalancer**.

```azurecli-interactive
  az network lb address-pool create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myBackendPoolOutbound
```
### <a name="create-outbound-rule"></a>Créer une règle de trafic sortant

Créez une règle de trafic sortant pour le pool principal sortant avec la commande [az network lb outbound-rule create](https://docs.microsoft.com/cli/azure/network/lb/outbound-rule?view=azure-cli-latest#az-network-lb-outbound-rule-create) :

* Nommée **myOutboundRule**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Associée à l’équilibreur de charge **myLoadBalancer**.
* Associée au serveur frontal **myFrontEndOutbound**.
* Protocole **Tous**.
* Délai d’inactivité de **15**.
* **10000** ports sortants.
* Associée au pool principal **myBackEndPoolOutbound**.

```azurecli-interactive
  az network lb outbound-rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myOutboundRule \
    --frontend-ip-configs myFrontEndOutbound \
    --protocol All \
    --idle-timeout 15 \
    --outbound-ports 10000 \
    --address-pool myBackEndPoolOutbound
```
### <a name="add-virtual-machines-to-outbound-pool"></a>Ajouter des machines virtuelles à un pool sortant

Ajoutez les machines virtuelles au pool sortant avec la commande [az network nic ip-config address-pool add](https://docs.microsoft.com/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add) :


#### <a name="vm1"></a>MV1
* Dans le pool d’adresses principal **myBackEndPoolOutbound**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Associée à l’interface réseau **myNicVM1** et **ipconfig1**.
* Associée à l’équilibreur de charge **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPoolOutbound \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>MV2
* Dans le pool d’adresses principal **myBackEndPoolOutbound**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Associée à l’interface réseau **myNicVM2** et **ipconfig1**.
* Associée à l’équilibreur de charge **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPoolOutbound \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm3"></a>MV3
* Dans le pool d’adresses principal **myBackEndPoolOutbound**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Associée à l’interface réseau **myNicVM3** et **ipconfig1**.
* Associée à l’équilibreur de charge **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPoolOutbound \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM3 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

# <a name="basic-sku"></a>[**Référence De base**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Il est recommandé de disposer d’un équilibreur de charge de référence SKU Standard pour les charges de travail de production. Pour plus d’informations sur les références SKU, consultez **[Références SKU Azure Load Balancer](skus.md)** .

## <a name="configure-virtual-network"></a>Configurer un réseau virtuel

Avant de déployer des machines virtuelles et de tester votre équilibreur de charge, créez les ressources de réseau virtuel associées.

### <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Créez un réseau virtuel avec la commande [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt) :

* Nommé **myVNet**.
* Préfixe d’adresse **10.1.0.0/16**.
* Sous-réseau nommé **myBackendSubnet**.
* Préfixe du sous-réseau **10.1.0.0/24**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Emplacement **eastus**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupLB \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-network-security-group"></a>Créer un groupe de sécurité réseau

Pour un équilibreur de charge standard, les machines virtuelles à l’adresse principale doivent disposer d’interfaces réseau appartenant à un groupe de sécurité réseau. 

Créez un Groupe de sécurité réseau avec la commande [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) :

* Nommé **myNSG**.
* Dans le groupe de ressources **myResourceGroupLB**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupLB \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Créer une règle de groupe de sécurité réseau

Créez une règle de groupe de sécurité réseau avec la commande [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) :

* Nommé **myNSGRuleHTTP**.
* Dans le groupe de sécurité réseau que vous avez créé à l’étape précédente, **myNSG**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Protocole **(*)** .
* Direction **entrante**.
* Source **(*)** .
* Destination **(*)** .
* Port de destination **Port 80**.
* Accès **Autoriser**.
* Priorité **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupLB \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Créer des interfaces réseau pour les machines virtuelles

Créez les interfaces réseau avec la commande [az network nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) :

#### <a name="vm1"></a>MV1

* Nommée **myNicVM1**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Dans le réseau virtuel **myVNet**.
* Dans le sous-réseau **myBackendSubnet**.
* Dans le groupe de sécurité réseau **myNSG**.

```azurecli-interactive

  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>MV2

* Nommé **myNicVM2**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Dans le réseau virtuel **myVNet**.
* Dans le sous-réseau **myBackendSubnet**.
* Dans le groupe de sécurité réseau **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM2 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm3"></a>MV3

* Nommée **myNicVM3**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Dans le réseau virtuel **myVNet**.
* Dans le sous-réseau **myBackendSubnet**.
* Dans le groupe de sécurité réseau **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM3 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

## <a name="create-backend-servers"></a>Créer des serveurs principaux

Dans cette section, vous allez créer :

* Un fichier de configuration cloud nommé **cloud-init.txt** pour la configuration du serveur. 
* Un groupe à haute disponibilité pour les machines virtuelles.
* Trois machines virtuelles à utiliser en tant que serveurs principaux pour l’équilibreur de charge.


Pour vérifier que l’équilibreur de charge a bien été créé, vous installez NGINX sur les machines virtuelles.

### <a name="create-cloud-init-configuration-file"></a>Créer un fichier de configuration cloud-init

Utilisez un fichier de configuration cloud-init pour installer NGINX et exécuter une application Node.js « Hello World » sur une machine virtuelle Linux. 

Dans votre interpréteur de commandes actuel, créez un fichier nommé cloud-init.txt. Copiez et collez l’extrait de code suivant dans l’interpréteur de commandes. Vérifiez que vous copiez bien le fichier cloud-init entier, en particulier la première ligne :

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
### <a name="create-availability-set-for-virtual-machines"></a>Créer un groupe à haute disponibilité pour des machines virtuelles

Créez le groupe à haute disponibilité avec la commande [az vm availability-set create](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest#az-vm-availability-set-create) :

* Nommé **myAvSet**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Emplacement **eastus**.

```azurecli-interactive
  az vm availability-set create \
    --name myAvSet \
    --resource-group myResourceGroupLB \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Créer des machines virtuelles

Créez les machines virtuelles avec la commande [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) :

#### <a name="vm1"></a>MV1
* Nommée **myVM1**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Attachée à l’interface réseau **myNicVM1**.
* Image de machine virtuelle **UbuntuLTS**.
* Fichier de configuration **cloud-init.txt** que vous avez créé à l’étape précédente.
* Dans le groupe à haute disponibilité **myAvSet**.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet \
    --no-wait 
```
#### <a name="vm2"></a>MV2
* Nommée **myVM2**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Attachée à l’interface réseau **myNicVM2**.
* Image de machine virtuelle **UbuntuLTS**.
* Fichier de configuration **cloud-init.txt** que vous avez créé à l’étape précédente.
* Dans **Zone 2**.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet  \
    --no-wait
```

#### <a name="vm3"></a>MV3
* Nommée **myVM3**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Attachée à l’interface réseau **myNicVM3**.
* Image de machine virtuelle **UbuntuLTS**.
* Fichier de configuration **cloud-init.txt** que vous avez créé à l’étape précédente.
* Dans **Zone 3**.

```azurecli-interactive
   az vm create \
    --resource-group myResourceGroupLB \
    --name myVM3 \
    --nics myNicVM3 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet  \
    --no-wait
```
Le déploiement des machines virtuelles peut prendre quelques minutes.


## <a name="create-a-public-ip-address"></a>Créer une adresse IP publique

Pour accéder à votre application web sur Internet, vous avez besoin d’une adresse IP publique pour l’équilibreur de charge. 

Utilisez la commande [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) pour effectuer les opérations suivantes :

* Créer une adresse IP publique redondante dans une zone standard nommée **myPublicIP**.
* Dans **myResourceGroupLB**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --sku Basic
```

## <a name="create-basic-load-balancer"></a>Créer un équilibreur de charge de base

Cette section explique en détail comment vous pouvez créer et configurer les composants suivants de l’équilibreur de charge :

  * Un pool d’adresses IP frontal qui reçoit le trafic réseau entrant sur l’équilibreur de charge.
  * Un pool d’adresses IP principal auquel le pool frontal envoie le trafic réseau dont la charge est équilibrée.
  * Une sonde d’intégrité qui détermine l’intégrité des instances de machine virtuelle backend.
  * Une règle d’équilibreur de charge qui définit la distribution du trafic aux machines virtuelles.

### <a name="create-the-load-balancer-resource"></a>Créer la ressource d’équilibreur de charge

Créez un équilibreur de charge public avec la commande [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create) :

* Nommé **myLoadBalancer**.
* Un pool frontal nommé **myFrontEnd**.
* Un pool principal nommé **myBackEndPool**.
* Associé à l’adresse IP publique **myPublicIP** que vous avez créé à l’étape précédente. 

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupLB \
    --name myLoadBalancer \
    --sku Basic \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Créer la sonde d’intégrité

Une sonde d’intégrité vérifie toutes les instances de machine virtuelle pour s’assurer qu’elles peuvent transmettre du trafic réseau. 

Une machine virtuelle dont le contrôle de sonde a échoué est supprimée de l’équilibreur de charge. La machine virtuelle est rajoutée à l’équilibreur de charge une fois l’échec résolu.

Créez une sonde d’intégrité avec la commande [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create) :

* Analyse l’intégrité des machines virtuelles.
* Nommée **myHealthProbe.** .
* Protocole **TCP**.
* Surveillant le **Port 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Créer la règle d’équilibreur de charge

Une règle d’équilibreur de charge définit les éléments suivants :

* La configuration IP frontale pour le trafic entrant.
* Le pool d’adresses IP principal qui reçoit le trafic.
* Les ports source et de destination requis. 

Créez une règle d’équilibreur de charge avec la commande [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create) :

* Nommée **myHTTPRule**.
* Écoutant le **Port 80** dans le pool frontal **myFrontEnd**.
* Envoyant le trafic réseau dont la charge est équilibrée au pool d’adresses principal **myBackEndPool** à l’aide du **Port 80**. 
* Utilisant la sonde d’intégrité **myHealthProbe**.
* Protocole **TCP**.

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Ajouter des machines virtuelles au pool de back-ends de l’équilibreur de charge

Ajoutez les machines virtuelles au pool de back-ends avec la commande [az network nic ip-config address-pool add](https://docs.microsoft.com/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add) :


#### <a name="vm1"></a>MV1
* Dans le pool d’adresses de back-end **myBackEndPool**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Associée à l’interface réseau **myNicVM1** et **ipconfig1**.
* Associée à l’équilibreur de charge **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>MV2
* Dans le pool d’adresses de back-end **myBackEndPool**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Associée à l’interface réseau **myNicVM2** et **ipconfig1**.
* Associée à l’équilibreur de charge **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm3"></a>MV3
* Dans le pool d’adresses de back-end **myBackEndPool**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Associée à l’interface réseau **myNicVM3** et **ipconfig1**.
* Associée à l’équilibreur de charge **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM3 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```
---

## <a name="test-the-load-balancer"></a>Tester l’équilibreur de charge

Pour obtenir l’adresse IP publique de l’équilibreur de charge, utilisez la commande [az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show). 

Copiez l’adresse IP publique, puis collez-la dans la barre d’adresses de votre navigateur.

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```
:::image type="content" source="./media/load-balancer-standard-public-cli/running-nodejs-app.png" alt-text="Tester l’équilibreur de charge" border="true":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, utilisez la commande [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) pour supprimer le groupe de ressources, l’équilibreur de charge et toutes les ressources associées.

```azurecli-interactive
  az group delete \
    --name myResourceGroupLB
```

## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide

* Vous avez créé un équilibreur de charge standard ou public
* Attaché des machines virtuelles. 
* Vous avez configuré la règle de trafic et la sonde d’intégrité de l’équilibreur de charge.
* Vous avez testé l’équilibreur de charge.

Pour en savoir plus sur Azure Load Balancer, consultez [Qu’est-ce qu’Azure Load Balancer ?](load-balancer-overview.md) et [Questions fréquentes sur Load Balancer](load-balancer-faqs.md).

Découvrez-en plus sur les [équilibreurs de charge et les zones de disponibilité](load-balancer-standard-availability-zones.md).
