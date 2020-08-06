---
title: Démarrage rapide - Créer un point de terminaison privé Azure avec Azure CLI
description: Découvrir des informations sur le point de terminaison privé Azure dans ce guide de démarrage rapide
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: e7c098ba06086781306960f76978aac9e4fa06bc
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87502662"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-cli"></a>Démarrage rapide : Créer une instance Private Endpoint à l’aide d’Azure CLI

Private Endpoint est le bloc de construction fondamental pour Private Link dans Azure. Il permet à des ressources Azure, comme des machines virtuelles, de communiquer en privé avec des ressources Private Link. Dans ce guide de démarrage rapide, vous allez apprendre à créer une machine virtuelle sur un réseau virtuel et un serveur dans SQL Database avec un point de terminaison privé à l’aide d’Azure CLI. Ensuite, vous pourrez accéder à la machine virtuelle pour accéder de manière sécurisée à la ressource de liaison privée (un serveur privé dans SQL Database dans cet exemple).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous décidez d’installer et d’utiliser Azure CLI en local, ce guide de démarrage rapide nécessite que vous utilisiez Azure CLI version 2.0.28 ou ultérieure. Exécutez `az --version` pour rechercher la version installée. Pour des informations d'installation ou de mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Avant de pouvoir créer des ressources, vous devez créer un groupe de ressources qui hébergera le réseau virtuel. Créez un groupe de ressources avec la commande [az group create](/cli/azure/group). Cet exemple crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *westcentralus* :

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
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

Prenez note de l’adresse IP publique de la machine virtuelle. Vous utiliserez cette adresse pour vous connecter à la machine virtuelle à partir d’Internet à l’étape suivante.

## <a name="create-a-server-in-sql-database"></a>Créer un serveur dans SQL Database

Créez un serveur dans SQL Database avec la commande az sql server create. N’oubliez pas que le nom de votre serveur doit être unique dans Azure. Vous devez donc remplacer la valeur d’espace réservé entre crochets par votre propre valeur unique :

```azurecli-interactive
# Create a server in the resource group
az sql server create \
    --name "myserver"\
    --resource-group myResourceGroup \
    --location WestUS \
    --admin-user "sqladmin" \
    --admin-password "CHANGE_PASSWORD_1"

# Create a database in the server with zone redundancy as false
az sql db create \
    --resource-group myResourceGroup  \
    --server myserver \
    --name mySampleDatabase \
    --sample-name AdventureWorksLT \
    --edition GeneralPurpose \
    --family Gen4 \
    --capacity 1
```

L’ID de serveur est similaire à ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/myserver.``` Vous l’utiliserez à l’étape suivante.

## <a name="create-the-private-endpoint"></a>Créer l’instance Private Endpoint

Créez un point de terminaison privé pour le serveur SQL logique dans votre réseau virtuel :

```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<server ID>" \  
    --group-ids sqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>Configurer la zone DNS privée

Créez une zone DNS privée pour le domaine SQL Database, créez un lien d’association avec le réseau virtuel et créez un groupe de zones DNS pour associer le point de terminaison privé à la zone DNS privée. 

```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \
   --name  "privatelink.database.windows.net"
az network private-dns link vnet create --resource-group myResourceGroup \
   --zone-name  "privatelink.database.windows.net"\
   --name MyDNSLink \
   --virtual-network myVirtualNetwork \
   --registration-enabled false
az network private-endpoint dns-zone-group create \
   --resource-group myResourceGroup \
   --endpoint-name myPrivateEndpoint \
   --name MyZoneGroup \
   --private-dns-zone "privatelink.database.windows.net" \
   --zone-name sql
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

## <a name="access-sql-database-privately-from-the-vm"></a>Accéder à SQL Database en privé à partir de la machine virtuelle

Dans cette section, vous allez vous connecter à la base de données SQL à partir de la machine virtuelle en utilisant le point de terminaison privé.

1. Dans le Bureau à distance de *myVM*, ouvrez PowerShell.
2. Entrez nslookup myserver.database.windows.net

   Vous recevez un message similaire à celui ci :

    ```
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:  myserver.database.windows.net
    ```

3. Installer SQL Server Management Studio
4. Dans Se connecter au serveur, entrez ou sélectionnez les informations suivantes :

   - Type de serveur : Sélectionnez Moteur de base de données.
   - Nom du serveur : Sélectionnez myserver.database.windows.net
   - Nom d’utilisateur : Entrez le nom d’utilisateur fourni lors de la création.
   - Mot de passe : Entrez le mot de passe fourni lors de la création.
   - Mémorisez le mot de passe : Sélectionnez Oui.

5. Sélectionnez **Connecter**.
6. Parcourez **Bases de données** à partir du menu de gauche.
7. (Facultatif) Créer ou interroger des informations à partir de *mydatabase*
8. Fermez la connexion Bureau à distance sur *myVm*.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n'en avez plus besoin, vous pouvez utiliser az group delete pour supprimer le groupe de ressources, ainsi que toutes les ressources qu’il contient :

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [Azure Private Link](private-link-overview.md)
