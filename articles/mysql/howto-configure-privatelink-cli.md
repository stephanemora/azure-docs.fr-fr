---
title: Méthode de configuration via l’interface CLI de Private Link pour Azure Database pour MySQL (préversion)
description: Découvrez comment configurer une instance Private Link pour Azure Database pour MySQL à partir d’Azure CLI
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 37f408ba633abdd95824b2e94253251eacf1f7f4
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980844"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mysql-preview-using-cli"></a>Créer et gérer Private Link pour Azure Database pour MySQL (préversion) à l’aide de l’interface CLI

Private Endpoint est le composant fondamental de Private Link dans Azure. Il permet à des ressources Azure, comme des machines virtuelles, de communiquer en privé avec des ressources Private Link. Dans cet article, vous apprendrez à utiliser Azure CLI pour créer une machine virtuelle dans un réseau virtuel Azure et un serveur Azure Database pour MySQL avec un point de terminaison privé Azure.

> [!NOTE]
> Cette fonctionnalité est disponible dans toutes les régions Azure où Azure Database pour MySQL prend en charge les niveaux tarifaires Usage général et Mémoire optimisée.

## <a name="prerequisites"></a>Conditions préalables requises

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous décidez d’installer et d’utiliser Azure CLI en local, ce guide de démarrage rapide nécessite que vous utilisiez Azure CLI version 2.0.28 ou ultérieure. Exécutez `az --version` pour rechercher la version installée. Pour des informations d'installation ou de mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Avant de pouvoir créer des ressources, vous devez créer un groupe de ressources qui hébergera le réseau virtuel. Créez un groupe de ressources avec la commande [az group create](/cli/azure/group). Cet exemple crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *westeurope* :

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-virtual-network"></a>Création d'un réseau virtuel
Créez un réseau virtuel avec la commande [az network vnet create](/cli/azure/network/vnet). Cet exemple crée un réseau virtuel par défaut nommé *myVirtualNetwork* avec un sous-réseau nommé *mySubnet* :

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```

## <a name="disable-subnet-private-endpoint-policies"></a>Désactiver les stratégies Private Endpoint du sous-réseau 
Azure déploie des ressources sur un sous-réseau au sein d’un réseau virtuel. vous devez donc créer ou mettre à jour le sous-réseau pour désactiver les stratégies réseau de Private Endpoint. Mettez à jour une configuration de sous-réseau nommée *mySubnet* avec [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) :

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>Création de la machine virtuelle 
Créez une machine virtuelle avec la commande az vm create. Lorsque vous y êtes invité, indiquez un mot de passe à utiliser comme informations d’identification pour vous connecter à la machine virtuelle. Cet exemple crée une machine virtuelle nommée *myVm* : 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
Notez l’adresse IP publique de la machine virtuelle. Vous utiliserez cette adresse pour vous connecter à la machine virtuelle à partir d’Internet à l’étape suivante.

## <a name="create-an-azure-database-for-mysql-server"></a>Création d’un serveur Azure Database pour MySQL 
Créez un serveur Azure Database pour MySQL avec la commande az mysql server create. N’oubliez pas que le nom de votre serveur MySQL doit être unique dans Azure. Par conséquent, remplacez la valeur d’espace réservé entre crochets par votre propre valeur unique : 

```azurecli-interactive
# Create a logical server in the resource group 
az mysql server create \
--name mydemoserver \
--resource-group myResourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

Notez que l’ID du serveur MySQL est similaire à ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/servername.``` Vous allez utiliser l’ID du serveur MySQL à l’étape suivante. 

## <a name="create-the-private-endpoint"></a>Créer l’instance Private Endpoint 
Créez un point de terminaison privé pour le serveur MySQL dans votre réseau virtuel : 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<MySQL Server ID>" \  
    --group-ids mysqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>Configurer la zone DNS privée 
Créez une zone DNS privée pour le domaine du serveur MySQL et créez un lien d’association avec le réseau virtuel. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for MySQL name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.database.windows.net --resource-group myResourceGroup -a <Private IP Address>
```

## <a name="connect-to-a-vm-from-the-internet"></a>Se connecter à une machine virtuelle à partir d’Internet

Connectez-vous à la machine virtuelle *myVm* à partir d’Internet comme suit :

1. Dans la barre de recherche du portail, entrez *myVm*.

1. Sélectionnez le bouton **Connexion**. Après avoir sélectionné le bouton **Connecter**, **Se connecter à la machine virtuelle** s’ouvre.

1. Sélectionnez **Télécharger le fichier RDP**. Azure crée un fichier de protocole RDP (Remote Desktop Protocol) ( *.rdp*) et le télécharge sur votre ordinateur.

1. Ouvrez le fichier .rdp* téléchargé.

    1. Si vous y êtes invité, sélectionnez **Connexion**.

    1. Entrez le nom d’utilisateur et le mot de passe spécifiés lors de la création de la machine virtuelle.

        > [!NOTE]
        > Vous devrez peut-être sélectionner **Plus de choix** > **Utiliser un autre compte**, pour spécifier les informations d’identification que vous avez entrées lorsque vous avez créé la machine virtuelle.

1. Sélectionnez **OK**.

1. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Si vous recevez un avertissement de certificat, sélectionnez **Oui** ou **Continuer**.

1. Une fois que le bureau de la machine virtuelle s’affiche, réduisez-le pour revenir à votre poste de travail local.  

## <a name="access-the-mysql-server-privately-from-the-vm"></a>Accéder au serveur MySQL en privé à partir de la machine virtuelle

1. Dans le Bureau à distance de  *myVM*, ouvrez PowerShell.

2. Entrez  `nslookup mydemomysqlserver.mysql.privatelink.database.azure.com`. 

    Vous recevez un message similaire à celui ci :
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemomysqlserver.mysql.privatelink.database.azure.com
    Address:  10.1.3.4

3. Test the private link connection for the MySQL server using any available client. In the example below I have used [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) to do the operation.


4. In **New connection**, enter or select this information:

    | Setting | Value |
    | ------- | ----- |
    | Connection Name| Select the connection name of your choice.|
    | Hostname | Select *mydemoserver.mysql.privatelink.database.azure.com* |
    | Username | Enter username as *username@servername* which is provided during the MySQL server creation. |
    | Password | Enter a password provided during the MySQL server creation. |
    ||

5. Select Connect.

6. Browse databases from left menu.

7. (Optionally) Create or query information from the MySQL database.

8. Close the remote desktop connection to myVm.

## Clean up resources 
When no longer needed, you can use az group delete to remove the resource group and all the resources it has: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur [Qu’est-ce qu’Azure Private Endpoint ?](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)