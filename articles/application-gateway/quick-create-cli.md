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
ms.openlocfilehash: eb0f73d31abced8decbed31e5604a2056584eb98
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57549423"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>Démarrage rapide : Diriger le trafic web avec Azure Application Gateway - Azure CLI

Ce guide de démarrage rapide vous montre comment utiliser le portail Azure pour créer une passerelle d’application.  Après avoir créé la passerelle d’application, vous la testez pour vous assurer qu’elle fonctionne correctement. Avec Azure Application Gateway, vous dirigez le trafic web de votre application vers des ressources spécifiques en affectant des écouteurs à des ports, en créant des règles et en ajoutant des ressources à un pool de back-ends. Par souci de simplicité, cet article utilise une configuration simple avec une adresse IP frontale publique, un écouteur de base pour héberger un site unique sur cette instance Application Gateway, deux machines virtuelles utilisées pour le pool principal et une règle d’acheminement de requête simple.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prérequis

### <a name="azure-powershell-module"></a>Module Azure PowerShell

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, exécutez Azure CLI version 2.0.4 ou ultérieure. Pour connaître la version, exécutez **az --version**. Pour plus d’informations sur l’installation ou la mise à niveau, consultez [Installer Azure CLI]( /cli/azure/install-azure-cli).

### <a name="resource-group"></a>Groupe de ressources

Dans Azure, vous allouez les ressources associées à un groupe de ressources. Créez un groupe de ressources à l’aide de la commande [az group create](/cli/azure/group#az-group-create). 

L’exemple suivant crée un groupe de ressources nommé *myResourceGroupAG* à l’emplacement *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

### <a name="required-network-resources"></a>Ressources réseau requises 

Azure a besoin d’un réseau virtuel pour communiquer avec les différentes ressources que vous créez.  Le sous-réseau de passerelle d’application peut contenir uniquement des passerelles d’application. Aucune autre ressource n’est autorisée.  Vous pouvez créer un sous-réseau pour Application Gateway ou en utiliser qui existe déjà. Vous créez deux sous-réseaux dans cet exemple : un pour la passerelle d’application et un autre pour les serveurs back-end. Vous pouvez l’adresse IP frontale d’Application Gateway pour qu’elle soit publique ou privée conformément à votre cas d’utilisation. Dans cet exemple, nous allons choisir une adresse IP frontale publique.

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

### <a name="backend-servers"></a>Serveurs principaux

La partie principale peut se composer de cartes d’interface réseau, de groupes de machines virtuelles identiques, d’adresses IP publiques, d’adresses IP internes, de noms de domaine complets et de serveurs back-end multi-locataires comme Azure App Service. Dans cet exemple, vous créez deux machines virtuelles Azure à utiliser comme serveurs back-end pour la passerelle d’application. Vous installez également IIS sur les machines virtuelles pour vérifier qu’Azure a bien créé la passerelle d’application.

#### <a name="create-two-virtual-machines"></a>Créer deux machines virtuelles

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

Quand vous actualisez le navigateur, vous devez voir s’afficher le nom de la deuxième machine virtuelle. Une réponse valide vérifie que la passerelle d’application a été créée avec succès et qu’elle est capable de se connecter avec succès au serveur principal.

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’avez plus besoin des ressources que vous avez créées avec la passerelle d’application, utilisez la commande [az group delete](/cli/azure/group#az-group-delete) pour supprimer le groupe de ressources. En supprimant le groupe de ressources, vous supprimez aussi la passerelle d’application et toutes ses ressources associées.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Gérer le trafic web avec une passerelle d’application à l’aide d’Azure CLI](./tutorial-manage-web-traffic-cli.md)

