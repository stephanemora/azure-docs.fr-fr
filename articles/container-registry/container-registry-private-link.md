---
title: Configurer une liaison privée
description: Configurez un point de terminaison privé sur un registre de conteneurs et activez une liaison privée dans un réseau virtuel local
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: de8228d84497e71f24dba3dd4e6162cb6735a8c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498913"
---
# <a name="configure-azure-private-link-for-an-azure-container-registry"></a>Configurer Azure Private Link pour un registre de conteneurs Azure 

Vous pouvez configurer un [point de terminaison privé](../private-link/private-endpoint-overview.md) pour votre registre de conteneurs Azure afin que les clients figurant sur un réseau virtuel Azure puissent accéder en toute sécurité au registre via une [liaison privée](../private-link/private-link-overview.md). Le point de terminaison privé utilise une adresse IP issue de l’espace d’adressage du réseau virtuel pour votre registre. Le trafic réseau entre les clients figurant sur le réseau virtuel et le registre traverse le réseau virtuel et une liaison privée sur le réseau principal de Microsoft, ce qui élimine toute exposition sur l’Internet public.

Vous pouvez [configurer des paramètres DNS](../private-link/private-endpoint-overview.md#dns-configuration) pour votre point de terminaison privé, afin que les paramètres résolvent l’adresse IP privée allouée du registre. Avec la configuration DNS, les clients et les services du réseau peuvent continuer à accéder au registre avec le nom de domaine complet du registre, tel que *myregistry.azurecr.io*.

Cette fonctionnalité est disponible uniquement au niveau de service **Premium** de registre de conteneurs. Pour plus d’informations sur les niveaux de service et les limites de registre, consultez [Références SKU Azure Container Registry](container-registry-skus.md).

> [!IMPORTANT]
> Cette fonctionnalité est actuellement en préversion et certaines [limitations s’appliquent](#preview-limitations). Les préversions sont à votre disposition, à condition que vous acceptiez les [conditions d’utilisation supplémentaires][terms-of-use]. Certains aspects de cette fonctionnalité sont susceptibles d’être modifiés avant la mise à disposition générale.

## <a name="preview-limitations"></a>Limitations de la version préliminaire

* Actuellement, vous ne pouvez pas configurer une liaison privée avec un point de terminaison privé sur un [registre géorépliqué](container-registry-geo-replication.md). 

## <a name="prerequisites"></a>Prérequis

* Pour utiliser les étapes Azure CLI décrites dans cet article, Azure CLI version 2.2.0 ou ultérieure est recommandée. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][azure-cli]. Ou exécutez cela dans [Azure Cloud Shell](../cloud-shell/quickstart.md).
* Si vous ne disposez pas d’un registre de conteneurs, créez-en un (niveau Premium requis) et envoyez (push) un exemple d’image comme `hello-world` à partir de Docker Hub. Par exemple, utilisez le [portail Azure][quickstart-portal] ou [Azure CLI][quickstart-cli] pour créer un registre.
* Si vous souhaitez configurer l’accès au registre à l’aide d’une liaison privée dans un autre abonnement Azure, vous devez inscrire le fournisseur de ressources pour Azure Container Registry dans cet abonnement. Par exemple :

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

Les exemples Azure CLI fournis dans cet article utilisent les variables d’environnement suivantes. Remplacez les valeurs par celles appropriées pour votre environnement. Tous les exemples sont mis en forme pour l’interpréteur de commandes Bash :

```bash
registryName=<container-registry-name>
registryLocation=<container-registry-location> # Azure region such as westeurope where registry created
resourceGroup=<resource-group-name>
vmName=<virtual-machine-name>
```

## <a name="create-a-docker-enabled-virtual-machine"></a>Créer une machine virtuelle compatible Docker

À des fins de test, utilisez une machine virtuelle Ubuntu compatible Docker pour accéder à un registre de conteneurs Azure. Pour utiliser l’authentification Azure Active Directory auprès du registre, installez également l’[interface de ligne de commande Azure][azure-cli] sur la machine virtuelle. Si vous disposez déjà d’une machine virtuelle, ignorez cette étape de création.

Vous pouvez utiliser le même groupe de ressources pour votre machine virtuelle et votre registre de conteneurs. Cette configuration simplifie le nettoyage à la fin, mais n’est pas nécessaire. Si vous choisissez de créer un groupe de ressources distinct pour la machine virtuelle et le réseau virtuel, exécutez [az group create][az-group-create] :

```azurecli
az group create --name $resourceGroup --location $registryLocation
```

À présent, déployez une machine virtuelle Azure Ubuntu par défaut avec [az vm create][az-vm-create]. L’exemple suivant crée une machine virtuelle nommée *myDockerVM*.

```azurecli
az vm create \
  --resource-group $resourceGroup \
  --name $vmName \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Il faut quelques minutes pour que la machine virtuelle soit créée. Après l’exécution de la commande, notez la valeur de `publicIpAddress` qui s’affiche dans l’interface Azure CLI. Utilisez cette adresse pour établir des connexions SSH avec la machine virtuelle.

### <a name="install-docker-on-the-vm"></a>Installer Docker sur la machine virtuelle

Établissez une connexion SSH avec la machine virtuelle dès qu’elle est en cours d’exécution. Remplacez *publicIpAddress* par l’adresse IP publique de votre machine virtuelle.

```bash
ssh azureuser@publicIpAddress
```

Exécutez les commandes suivantes pour installer Docker sur la machine virtuelle Ubuntu :

```bash
sudo apt-get update
sudo apt install docker.io -y
```

Après l’installation, exécutez la commande suivante pour vérifier que Docker s’exécute correctement sur la machine virtuelle :

```bash
sudo docker run -it hello-world
```

Sortie :

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Installer l’interface de ligne de commande Microsoft Azure

Suivez les étapes de la section [Installer Azure CLI avec apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) pour installer Azure CLI sur votre machine virtuelle Ubuntu. Par exemple :

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Quittez la connexion SSH.

## <a name="set-up-private-link---cli"></a>Configurer une liaison privée – interface de ligne de commande

### <a name="get-network-and-subnet-names"></a>Obtenir les noms de réseau et de sous-réseau

Si vous ne les avez pas déjà, vous aurez besoin des noms d’un réseau virtuel et d’un sous-réseau pour configurer une liaison privée. Dans cet exemple, vous utilisez le même sous-réseau pour la machine virtuelle et le point de terminaison privé du registre. Toutefois, dans de nombreux scénarios, il convient de configurer le point de terminaison dans un sous-réseau distinct. 

Quand vous créez une machine virtuelle, Azure crée par défaut un réseau virtuel dans le même groupe de ressources. Le nom du réseau virtuel est basé sur le nom de la machine virtuelle. Par exemple, si vous nommez votre machine virtuelle *myDockerVM*, le nom de réseau virtuel par défaut est *myDockerVMVNET*, avec un sous-réseau nommé *myDockerVMSubnet*. Définissez ces valeurs dans les variables d’environnement en exécutant la commande [az network vnet list][az-network-vnet-list] :

```azurecli
networkName=$(az network vnet list \
  --resource-group $resourceGroup \
  --query '[].{Name: name}' --output tsv)

subnetName=$(az network vnet list \
  --resource-group $resourceGroup \
  --query '[].{Subnet: subnets[0].name}' --output tsv)

echo networkName=$networkName
echo subnetName=$subnetName
```

### <a name="disable-network-policies-in-subnet"></a>Désactiver les stratégies réseau dans le sous-réseau

[Désactivez les stratégies réseau](../private-link/disable-private-endpoint-network-policy.md) telles que les groupes de sécurité réseau dans le sous-réseau pour le point de terminaison privé. Mettez à jour votre configuration de sous-réseau avec [az network vnet subnet update][az-network-vnet-subnet-update] :

```azurecli
az network vnet subnet update \
 --name $subnetName \
 --vnet-name $networkName \
 --resource-group $resourceGroup \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>Configurer la zone DNS privée

Créez une zone DNS privée pour le domaine du registre de conteneurs Azure privé. Dans les étapes ultérieures, vous allez créer des enregistrements DNS pour votre domaine de registre à l’intérieur de cette zone DNS.

Pour utiliser une zone privée afin de remplacer la résolution DNS par défaut pour votre registre de conteneurs Azure, la zone doit être nommée **privatelink.azurecr.io**. Exécutez la commande [az network private-dns zone create][az-network-private-dns-zone-create] suivante pour créer la zone privée :

```azurecli
az network private-dns zone create \
  --resource-group $resourceGroup \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Créer un lien d’association

Exécutez [az network private-dns link vnet create][az-network-private-dns-link-vnet-create] pour associer votre zone privée au réseau virtuel. Cet exemple crée un lien appelé *myDNSLink*.

```azurecli
az network private-dns link vnet create \
  --resource-group $resourceGroup \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $networkName \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Créer un point de terminaison de registre privé

Dans cette section, créez le point de terminaison privé du registre dans le réseau virtuel. Tout d’abord, obtenez l’ID de ressource de votre registre :

```azurecli
registryID=$(az acr show --name $registryName \
  --query 'id' --output tsv)
```

Exécutez la commande [az network private-endpoint create][az-network-private-endpoint-create] pour créer le point de terminaison privé du registre.

L’exemple suivant crée le point de terminaison *myPrivateEndpoint* et la connexion de service *myConnection*. Pour spécifier une ressource de registre de conteneurs pour le point de terminaison, transmettez `--group-ids registry` :

```azurecli
az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group $resourceGroup \
    --vnet-name $networkName \
    --subnet $subnetName \
    --private-connection-resource-id $registryID \
    --group-ids registry \
    --connection-name myConnection
```

### <a name="get-private-ip-addresses"></a>Obtenir des adresses IP privées

Exécutez [az network private-endpoint show][az-network-private-endpoint-show] pour interroger le point de terminaison afin d’obtenir l’ID d’interface réseau :

```azurecli
networkInterfaceID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $resourceGroup \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

Deux adresses IP privées sont associées à l’interface réseau pour le registre de conteneurs : une pour le registre proprement dit et l’autre pour le point de terminaison de données du registre. Exécutez les commandes [az resource show][az-resource-show] suivantes pour obtenir les adresses IP privées pour le registre de conteneurs et le point de terminaison de données du registre :

```azurecli
privateIP=$(az resource show \
  --ids $networkInterfaceID \
  --api-version 2019-04-01 --query 'properties.ipConfigurations[1].properties.privateIPAddress' \
  --output tsv)

dataEndpointPrivateIP=$(az resource show \
  --ids $networkInterfaceID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[0].properties.privateIPAddress' \
  --output tsv)
```

### <a name="create-dns-records-in-the-private-zone"></a>Créer des enregistrements DNS dans la zone privée

Les commandes suivantes créent des enregistrements DNS dans la zone privée pour le point de terminaison du registre et son point de terminaison de données. Par exemple, si vous avez un registre nommé *myregistry* dans la région *westeurope*, les noms des points de terminaison sont `myregistry.azurecr.io` et `myregistry.westeurope.data.azurecr.io`. 

Tout d’abord, exécutez [az network private-dns record-set a create][az-network-private-dns-record-set-a-create] pour créer des jeux d’enregistrements A vides pour le point de terminaison du registre et le point de terminaison de données :

```azurecli
az network private-dns record-set a create \
  --name $registryName \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup

# Specify registry region in data endpoint name
az network private-dns record-set a create \
  --name ${registryName}.${registryLocation}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup
```

Exécutez [az network private-dns record-set a add-record][az-network-private-dns-record-set-a-add-record] pour créer les enregistrements A pour le point de terminaison du registre et le point de terminaison de données :

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $registryName \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup \
  --ipv4-address $privateIP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${registryName}.${registryLocation}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup \
  --ipv4-address $dataEndpointPrivateIP
```

La liaison privée est désormais configurée et prête à être utilisée.

## <a name="set-up-private-link---portal"></a>Configurer une liaison privée – portail

Les étapes suivantes supposent que vous disposez déjà d’un réseau virtuel et d’un sous-réseau configurés avec une machine virtuelle à des fins de test. Vous pouvez également [créer un réseau virtuel et un sous-réseau](../virtual-network/quick-create-portal.md).

### <a name="create-a-private-endpoint"></a>Créer un Private Endpoint

1. Dans le portail, accédez à votre registre de conteneurs.
1. Sous **Paramètres**, sélectionnez **Connexions des points de terminaison privés (préversion)** .
1. Sélectionnez **+ Point de terminaison privé**.
1. Sous l’onglet **Informations de base**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** | |
    | Abonnement | Sélectionnez votre abonnement. |
    | Resource group | Entrez le nom d’un groupe existant ou créez-en un nouveau.|
    | **Détails de l’instance** |  |
    | Nom | Entrez un nom unique. |
    |Région|Sélectionnez une région.|
    |||
5. Sélectionnez **Suivant : Ressource**.
6. Entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    |Méthode de connexion  | Sélectionnez **Se connecter à une ressource Azure dans mon répertoire**.|
    | Abonnement| Sélectionnez votre abonnement. |
    | Type de ressource | Sélectionnez **Microsoft.ContainerRegistry/registries**. |
    | Ressource |Sélectionnez le nom de votre registre.|
    |Sous-ressource cible |Sélectionnez le **registre**.|
    |||
7. Sélectionnez **Suivant : Configuration**.
8. Entrez ou sélectionnez les informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    |**Mise en réseau**| |
    | Réseau virtuel| Sélectionnez le réseau virtuel où votre machine virtuelle est déployée, tel que *myDockerVMVNET*. |
    | Subnet | Sélectionnez un sous-réseau, tel que *myDockerVMSubnet*, où votre machine virtuelle est déployée. |
    |**Intégration à un DNS privé**||
    |Intégrer à une zone DNS privée |Sélectionnez **Oui**. |
    |Zone DNS privée |Sélectionnez *(Nouveau) privatelink.azurecr.io* |
    |||

1. Sélectionnez **Revoir + créer**. Vous êtes redirigé vers la page **Vérifier + créer** où Azure valide votre configuration. 
2. Lorsque le message **Validation passed** (Validation réussie) apparaît, sélectionnez **Créer**.

Une fois le point de terminaison privé créé, les paramètres DNS dans la zone privée s’affichent dans la page **Vue d’ensemble** du point de terminaison.

![Paramètres DNS du point de terminaison](./media/container-registry-private-link/private-endpoint-overview.png)

Votre liaison privée est désormais configurée et prête à être utilisée.

## <a name="validate-private-link-connection"></a>Valider la connexion de liaison privée

Vous devez vérifier que les ressources contenues dans le sous-réseau du point de terminaison privé se connectent à votre registre via une adresse IP privée, et qu’elles sont intégrées à la zone DNS privée appropriée.

Pour valider la connexion de liaison privée, utilisez SSH sur la machine virtuelle que vous avez configurée dans le réseau virtuel.

Exécutez la commande `nslookup` pour résoudre l’adresse IP de votre registre via la liaison privée :

```bash
nslookup $registryName.azurecr.io
```

L’exemple de sortie montre l’adresse IP du registre dans l’espace d’adressage du sous-réseau :

```console
[...]
myregistry.azurecr.io       canonical name = myregistry.privatelink.azurecr.io.
Name:   myregistry.privatelink.azurecr.io
Address: 10.0.0.6
```

Comparez ce résultat avec l’adresse IP publique dans la sortie `nslookup` pour le même registre via un point de terminaison public :

```console
[...]
Non-authoritative answer:
Name:   myregistry.westeurope.cloudapp.azure.com
Address: 40.78.103.41
```

### <a name="registry-operations-over-private-link"></a>Opérations de registre via la liaison privée

Vérifiez également que vous pouvez effectuer des opérations de registre à partir de la machine virtuelle dans le sous-réseau. Établissez une connexion SSH à votre machine virtuelle et exécutez [az acr login][az-acr-login] pour vous connecter à votre registre. Selon la configuration de votre machine virtuelle, vous pouvez être amené à préfixer les commandes suivantes avec `sudo`.

```bash
az acr login --name $registryName
```

Effectuez des opérations de registre telles que `docker pull` pour extraire (pull) un exemple d’image du registre. Remplacez `hello-world:v1` par une image et une balise appropriées pour votre registre, précédées du nom du serveur de connexion du registre (entièrement en minuscules) :

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker tire (pull) correctement l’image sur la machine virtuelle.

## <a name="manage-private-endpoint-connections"></a>Gérer les connexions de point de terminaison privé

Gérez les connexions de point de terminaison privé d’un registre à l’aide du portail Azure ou en utilisant des commandes dans le groupe de commandes [az acr private-endpoint-connection][az-acr-private-endpoint-connection]. Ces opérations incluent l’approbation, la suppression, le fait de lister, le rejet et l’affichage des détails des connexions de point de terminaison privé d’un registre.

Par exemple, pour lister les connexions de point de terminaison privé d’un registre, exécutez la commande [az acr private-endpoint-connection list][az-acr-private-endpoint-connection-list]. Par exemple :

```azurecli
az acr private-endpoint-connection list \
  --registry-name $registryName 
```

Quand vous configurez une connexion de point de terminaison privé via la procédure décrite dans cet article, le registre accepte automatiquement les connexions à partir des clients et des services qui ont des autorisations RBAC sur le registre. Vous pouvez configurer le point de terminaison pour exiger l’approbation manuelle des connexions. Pour obtenir des informations sur l’approbation et le rejet des connexions de point de terminaison privé, consultez [Gérer une connexion de point de terminaison privé](../private-link/manage-private-endpoint.md).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous avez créé toutes les ressources Azure dans le même groupe de ressources et que vous n’en avez plus besoin, vous pouvez éventuellement supprimer les ressources à l’aide d’une seule commande [az group delete](/cli/azure/group) :

```azurecli
az group delete --name $resourceGroup
```

Pour nettoyer vos ressources dans le portail, accédez à votre groupe de ressources. Une fois le groupe de ressources chargé, cliquez sur **Supprimer le groupe de ressources** pour supprimer le groupe de ressources et les ressources à cet endroit.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur Liaison privée (Private Link), consultez la documentation [Azure Private Link](../private-link/private-link-overview.md).
* Une alternative à la liaison privée consiste à configurer des règles d’accès réseau pour limiter l’accès au registre. Pour en savoir plus, consultez [Restreindre l’accès à un registre de conteneurs Azure à l’aide d’un réseau virtuel Azure ou de règles de pare-feu](container-registry-vnet.md).

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-private-endpoint-connection]: /cli/azure/acr/private-endpoint-connection
[az-acr-private-endpoint-connection-list]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-list
[az-acr-private-endpoint-connection-approve]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-approve
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[az-network-private-endpoint-create]: /cli/azure/network/private-endpoint#az-network-private-endpoint-create
[az-network-private-endpoint-show]: /cli/azure/network/private-endpoint#az-network-private-endpoint-show
[az-network-private-dns-zone-create]: /cli/azure/network/private-dns/zone#az-network-private-dns-zone-create
[az-network-private-dns-link-vnet-create]: /cli/azure/network/private-dns/link/vnet#az-network-private-dns-link-vnet-create
[az-network-private-dns-record-set-a-create]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-create
[az-network-private-dns-record-set-a-add-record]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-add-record
[az-resource-show]: /cli/azure/resource#az-resource-show
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
