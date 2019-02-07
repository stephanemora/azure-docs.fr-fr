---
title: 'Démarrage rapide : Diriger le trafic web avec Azure Application Gateway - Azure CLI | Microsoft Docs'
description: Découvrez comment utiliser Azure CLI pour créer une passerelle Azure Application Gateway qui dirige le trafic web vers les machines virtuelles d’un pool principal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 1/8/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 0ba18b1ef0ba6c0a73759577c83ab80550baa6f8
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55754742"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>Démarrage rapide : Diriger le trafic web avec Azure Application Gateway - Azure CLI

Ce guide de démarrage rapide vous montre comment utiliser Azure CLI pour créer rapidement une passerelle d’application avec deux machines virtuelles dans son pool de back-ends. Vous la testez ensuite pour vérifier qu’elle fonctionne correctement. Avec Azure Application Gateway, vous dirigez le trafic web de votre application vers des ressources spécifiques en affectant des écouteurs à des ports, en créant des règles et en ajoutant des ressources à un pool de back-ends.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, exécutez Azure CLI version 2.0.4 ou ultérieure. Pour connaître la version, exécutez **az --version**. Pour plus d’informations sur l’installation ou la mise à niveau, consultez [Installer Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Dans Azure, vous allouez les ressources associées à un groupe de ressources. Créez un groupe de ressources à l’aide de la commande [az group create](/cli/azure/group#az-group-create). 

L’exemple suivant crée un groupe de ressources nommé *myResourceGroupAG* à l’emplacement *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Créer des ressources réseau 

Quand vous créez un réseau virtuel, la passerelle d’application peut communiquer avec d’autres ressources. Vous pouvez créer un réseau virtuel en même temps que la passerelle d’application. Vous créez deux sous-réseaux dans cet exemple : un pour la passerelle d’application et l’autre pour les machines virtuelles. Le sous-réseau de passerelle d’application peut contenir uniquement des passerelles d’application. Aucune autre ressource n’est autorisée.

Pour créer le réseau virtuel et le sous-réseau, utilisez [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). Exécutez [az network public-ip create](/cli/azure/network/public-ip) pour créer l’adresse IP publique.

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
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-backend-servers"></a>Créer des serveurs principaux

Dans cet exemple, vous créez deux machines virtuelles qu’Azure va utiliser comme serveurs back-end pour la passerelle d’application. 

### <a name="create-two-virtual-machines"></a>Créer deux machines virtuelles

Installez le [serveur web NGINX](https://docs.nginx.com/nginx/) sur les machines virtuelles pour vérifier que la passerelle d’application a bien été créée. Vous pouvez utiliser un fichier de configuration cloud-init pour installer NGINX et exécuter une application Node.js « Hello World » sur une machine virtuelle Linux. Pour plus d’informations sur cloud-init, consultez [Prise en charge de cloud-init pour les machines virtuelles dans Azure](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init).

Dans Azure Cloud Shell, copiez et collez la configuration suivante dans un fichier appelé *cloud-init.txt*. Entrez *editor cloud-init.txt* pour créer le fichier.

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

Créez les interfaces réseau avec la commande [az network nic create](/cli/azure/network/nic#az-network-nic-create). Pour créer les machines virtuelles, vous utilisez [az vm create](/cli/azure/vm#az-vm-create).

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupAG \
    --name myNic$i \
    --vnet-name myVNet \
    --subnet myBackendSubnet
  az vm create \
    --resource-group myResourceGroupAG \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
done
```

## <a name="create-the-application-gateway"></a>Créer la passerelle Application Gateway

Créez une passerelle d’application à l’aide de la commande [az network application-gateway create](/cli/azure/network/application-gateway). Quand vous créez une passerelle d’application avec Azure CLI, vous spécifiez des informations de configuration, telles que la capacité, la référence SKU et les paramètres HTTP. Azure ajoute ensuite les adresses IP privées des interfaces réseau en tant que serveurs dans le pool de back-ends de la passerelle d’application.

```azurecli-interactive
address1=$(az network nic show --name myNic1 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
address2=$(az network nic show --name myNic2 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Enabled \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$address1" "$address2"
```

La création de la passerelle d’application par Azure peut prendre jusqu’à 30 minutes. Une fois la passerelle d’application créée, vous pouvez afficher les paramètres suivants dans la section **Paramètres** de la page **Application Gateway** :

- **appGatewayBackendPool** : situé dans la page **Pools principaux**, ce paramètre spécifie le pool de back-ends nécessaire.
- **appGatewayBackendHttpSettings** : situé dans la page **Paramètres HTTP**, ce paramètre spécifie que la passerelle d’application utilise le port 80 et le protocole HTTP pour la communication.
- **appGatewayHttpListener** : situé dans la page **Écouteurs**, ce paramètre spécifie l’écouteur par défaut associé à **appGatewayBackendPool**.
- **appGatewayFrontendIP** : situé dans la page **Configurations d’adresses IP frontales**, ce paramètre assigne *myAGPublicIPAddress* à **appGatewayHttpListener**.
- **rule1** : situé dans la page **Règles**, ce paramètre spécifie la règle de routage par défaut associée à **appGatewayHttpListener**.

## <a name="test-the-application-gateway"></a>Tester la passerelle d’application

Azure ne nécessite pas de serveur web NGINX pour créer la passerelle d’application, mais vous avez installé ce serveur dans ce guide de démarrage rapide pour vérifier qu’Azure avait bien créé la passerelle d’application. Pour obtenir l’adresse IP publique de la nouvelle passerelle d’application, utilisez [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
``` 

Copiez et collez l’adresse IP publique dans la barre d’adresse de votre navigateur.
    
![Tester la passerelle d’application](./media/quick-create-cli/application-gateway-nginxtest.png)

Quand vous actualisez le navigateur, vous devez voir s’afficher le nom de la deuxième machine virtuelle.

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’avez plus besoin des ressources que vous avez créées avec la passerelle d’application, utilisez la commande [az group delete](/cli/azure/group#az-group-delete) pour supprimer le groupe de ressources. En supprimant le groupe de ressources, vous supprimez aussi la passerelle d’application et toutes ses ressources associées.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```
 
## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Gérer le trafic web avec une passerelle d’application à l’aide d’Azure CLI](./tutorial-manage-web-traffic-cli.md)

