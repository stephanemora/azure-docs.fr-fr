---
title: Rechercher et utiliser des informations sur le plan d’achat du marketplace à l’aide de l’interface CLI
description: Découvrez comment utiliser Azure CLI pour rechercher des URN d’images et des paramètres de plan d’achat, tels que l’éditeur, l’offre, la référence SKU et la version, pour les images de machine virtuelle de Place de marché.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.collection: linux
ms.custom: contperf-fy21q3-portal, devx-track-azurecli
ms.openlocfilehash: be0535a49b47c45cad49abd1bf720b6347a660b8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484198"
---
# <a name="find-azure-marketplace-image-information-using-the-azure-cli"></a>Rechercher des informations sur les images de Place de marché Azure à l’aide d’Azure CLI

Cette rubrique décrit comment utiliser Azure CLI pour rechercher des images de machine virtuelle sur la Place de marché Microsoft Azure. Ces informations permettent de spécifier une image de la Place de marché lorsque vous créez une machine virtuelle par programmation avec l’interface CLI, des modèles de Resource Manager ou d’autres outils.

Vous pouvez également parcourir les images et offres disponibles à l’aide de [Place de marché Azure](https://azuremarketplace.microsoft.com/) ou [Azure PowerShell](../windows/cli-ps-findimage.md). 

## <a name="terminology"></a>Terminologie

Une image de Place de Marché dans Azure a les attributs suivants :

* **Éditeur** : organisation qui a créé l’image. Exemples : Canonical, MicrosoftWindowsServer
* **Offre** : nom du groupe d’images associées créé par un éditeur. Exemples : UbuntuServer, WindowsServer
* **Référence SKU** : instance d’une offre, par exemple une version majeure d’une distribution. Exemples : 18.04-LTS, 2019-Datacenter
* **Version** : numéro de version d’une référence SKU d’image. 

Ces valeurs peuvent être transmises individuellement ou en tant qu’*URN* d’image, combinant les valeurs séparées par le signe deux-points (:). Par exemple : *Éditeur*:*Offre*:*SKU*:*Version*. Vous pouvez remplacer le numéro de version dans l’URN par `latest` pour utiliser la version la plus récente de l’image. 

Si l’éditeur de l’image fournit des conditions de licences et d’achat supplémentaires, vous devez les accepter avant de pouvoir utiliser l’image.  Pour plus d’informations, consultez [Vérifier les informations du plan d’achat](#check-the-purchase-plan-information).



## <a name="list-popular-images"></a>Liste des images populaires

Exécutez la commande [az vm image list](/cli/azure/vm/image), sans l’option `--all` pour afficher la liste des images de machine virtuelle populaires sur la Place de marché Microsoft Azure. Par exemple, exécutez la commande suivante pour afficher une liste mise en cache d’images populaires dans un format de tableau :

```azurecli
az vm image list --output table
```

La sortie inclut l’URN de l’image. Vous pouvez également utiliser l’*UrnAlias*, qui est une version abrégée créée pour les images populaires telles que *UbuntuLTS*.

```output
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.5                 OpenLogic:CentOS:7.5:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
debian-10      Debian                  10                  Debian:debian-10:10:latest                                      Debian               latest
openSUSE-Leap  SUSE                    42.3                SUSE:openSUSE-Leap:42.3:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7-LVM               RedHat:RHEL:7-LVM:latest                                        RHEL                 latest
SLES           SUSE                    15                  SUSE:SLES:15:latest                                             SLES                 latest
UbuntuServer   Canonical               18.04-LTS           Canonical:UbuntuServer:18.04-LTS:latest                         UbuntuLTS            latest
WindowsServer  MicrosoftWindowsServer  2019-Datacenter     MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest     Win2019Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
```

## <a name="find-specific-images"></a>Recherche d’images spécifiques

Pour rechercher une image de machine virtuelle dans la Place de marché, utilisez la commande `az vm image list` avec l’option `--all`. L’exécution de cette version de la commande peut prendre un certain temps et retourner une sortie très longue. En général, la sortie peut être filtrée à l’aide de `--publisher` ou d’un autre paramètre. 

Par exemple, la commande suivante affiche toutes les offres Debian (n’oubliez pas que, sans le commutateur `--all`, elle effectue une recherche uniquement dans le cache local d’images courantes) :

```azurecli
az vm image list --offer Debian --all --output table 
```

Résultat partiel : 

```output
Offer                                    Publisher                         Sku                                      Urn                                                                                                   Version
---------------------------------------  --------------------------------  ---------------------------------------  ----------------------------------------------------------------------------------------------------  --------------
apache-solr-on-debian                    apps-4-rent                       apache-solr-on-debian                    apps-4-rent:apache-solr-on-debian:apache-solr-on-debian:1.0.0                                         1.0.0
atomized-h-debian10-v1                   atomizedinc1587939464368          hdebian10plan                            atomizedinc1587939464368:atomized-h-debian10-v1:hdebian10plan:1.0.0                                   1.0.0
atomized-h-debian9-v1                    atomizedinc1587939464368          hdebian9plan                             atomizedinc1587939464368:atomized-h-debian9-v1:hdebian9plan:1.0.0                                     1.0.0
atomized-r-debian10-v1                   atomizedinc1587939464368          rdebian10plan                            atomizedinc1587939464368:atomized-r-debian10-v1:rdebian10plan:1.0.0                                   1.0.0
atomized-r-debian9-v1                    atomizedinc1587939464368          rdebian9plan                             atomizedinc1587939464368:atomized-r-debian9-v1:rdebian9plan:1.0.0                                     1.0.0
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.7                         1.0.7
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.8                         1.0.8
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.9                         1.0.9
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.18                          1.0.18
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.19                          1.0.19
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.20                          1.0.20
apache-web-server-with-debian-10         cognosys                          apache-web-server-with-debian-10         cognosys:apache-web-server-with-debian-10:apache-web-server-with-debian-10:1.2019.1008                1.2019.1008
docker-ce-with-debian-10                 cognosys                          docker-ce-with-debian-10                 cognosys:docker-ce-with-debian-10:docker-ce-with-debian-10:1.2019.0710                                1.2019.0710
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201602010                                                                       8.0.201602010
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201603020                                                                       8.0.201603020
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201604050                                                                       8.0.201604050
...
```


## <a name="look-at-all-available-images"></a>Consulter toutes les images disponibles
 
Une autre façon de trouver une image dans un emplacement consiste à exécuter successivement les commandes [az vm image list-publishers](/cli/azure/vm/image), [az vm image list-offers](/cli/azure/vm/image) et [az vm image list-skus](/cli/azure/vm/image). Ces commandes vous permettent de déterminer les valeurs suivantes :

1. Répertoriez les éditeurs d’images pour un emplacement. Dans cet exemple, nous nous intéressons à la région *USA Ouest*.
    
    ```azurecli
    az vm image list-publishers --location westus --output table
    ```

1. pour un éditeur donné, en répertoriant ses offres ; Dans cet exemple, nous ajoutons *Canonical* comme éditeur.
    
    ```azurecli
    az vm image list-offers --location westus --publisher Canonical --output table
    ```

1. pour une offre donnée, en répertoriant ses références SKU. Dans cet exemple, nous ajoutons *UbuntuServer* comme offre.
    ```azurecli
    az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
    ```

1. Pour un éditeur, une offre et une référence SKU donnés, affichez toutes les versions de l’image. Dans cet exemple, nous ajoutons *18.04-LTS* comme référence SKU.

    ```azurecli
    az vm image list \
        --location westus \
        --publisher Canonical \  
        --offer UbuntuServer \    
        --sku 18.04-LTS \
        --all --output table
    ```

Passez cette valeur de la colonne URN avec le paramètre `--image` lorsque vous créez une machine virtuelle avec la commande [az vm create](/cli/azure/vm). Vous pouvez également remplacer le numéro de version dans l’URN par « latest » pour utiliser simplement la version la plus récente de l’image. 

Si vous déployez une machine virtuelle avec un modèle Resource Manager, vous définissez les paramètres d’image individuellement dans les propriétés `imageReference`. Consultez la [référence de modèle](/azure/templates/microsoft.compute/virtualmachines).


## <a name="check-the-purchase-plan-information"></a>Vérifier les informations du plan d’achat

Certaines images de machine virtuelle dans la Place de marché Microsoft Azure ont des conditions de licence et d’achat supplémentaires que vous devez accepter pour pouvoir les déployer par programme.  

Pour déployer une machine virtuelle à partir d’une telle image, vous devez accepter les conditions de l’image la première fois que vous l’utilisez, une fois par abonnement. Vous devez également spécifier les paramètres du *plan d’achat* pour déployer une machine virtuelle à partir de cette image.

Pour afficher les informations du plan d’achat d’une image, exécutez la commande [az vm image show](/cli/azure/image) avec l’URN de l’image. Si la propriété `plan` dans la sortie n’est pas `null`, l’image a des conditions que vous devez accepter avant le déploiement par programmation.

Par exemple, l’image Canonical Ubuntu Server 18.04 LTS ne possède pas de conditions supplémentaires, car les informations de `plan` sont `null` :

```azurecli
az vm image show --location westus --urn Canonical:UbuntuServer:18.04-LTS:latest
```

Sortie :

```output
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/18.04-LTS/Versions/18.04.201901220",
  "location": "westus",
  "name": "18.04.201901220",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

L’exécution d’une commande similaire pour le RabbitMQ certifié par Bitnami image affiche les propriétés `plan` suivantes : `name`, `product`, et `publisher`. (Certaines images ont également une propriété `promotion code`.) 

```azurecli
az vm image show --location westus --urn bitnami:rabbitmq:rabbitmq:latest
```
Sortie :

```output
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/bitnami/ArtifactTypes/VMImage/Offers/rabbitmq/Skus/rabbitmq/Versions/3.7.1901151016",
  "location": "westus",
  "name": "3.7.1901151016",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": {
    "name": "rabbitmq",
    "product": "rabbitmq",
    "publisher": "bitnami"
  },
  "tags": null
}
```

Pour déployer cette image, vous devez accepter les conditions générales et fournir les paramètres du plan d’achat lorsque vous déployez une machine virtuelle en utilisant cette image.

## <a name="accept-the-terms"></a>Accepter les conditions

Pour afficher et accepter les termes du contrat de licence, utilisez la commande [az vm image accept-terms](/cli/azure/vm/image/terms). Lorsque vous acceptez les termes, vous activez un déploiement par programmation dans votre abonnement. Vous ne devez accepter qu’une fois les conditions par abonnement pour l’image. Par exemple :

```azurecli
az vm image terms show --urn bitnami:rabbitmq:rabbitmq:latest
``` 

La sortie inclut un `licenseTextLink` aux termes du contrat de licence et indique que la valeur de `accepted` est `true` :

```output
{
  "accepted": true,
  "additionalProperties": {},
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.MarketplaceOrdering/offertypes/bitnami/offers/rabbitmq/plans/rabbitmq",
  "licenseTextLink": "https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_BITNAMI%253a24RABBITMQ%253a24RABBITMQ%253a24IGRT7HHPIFOBV3IQYJHEN2O2FGUVXXZ3WUYIMEIVF3KCUNJ7GTVXNNM23I567GBMNDWRFOY4WXJPN5PUYXNKB2QLAKCHP4IE5GO3B2I.txt",
  "name": "rabbitmq",
  "plan": "rabbitmq",
  "privacyPolicyLink": "https://bitnami.com/privacy",
  "product": "rabbitmq",
  "publisher": "bitnami",
  "retrieveDatetime": "2019-01-25T20:37:49.937096Z",
  "signature": "XXXXXXLAZIK7ZL2YRV5JYQXONPV76NQJW3FKMKDZYCRGXZYVDGX6BVY45JO3BXVMNA2COBOEYG2NO76ONORU7ITTRHGZDYNJNXXXXXX",
  "type": "Microsoft.MarketplaceOrdering/offertypes"
}
```

Pour accepter les conditions générales, saisissez :

```azurecli
az vm image terms accept --urn bitnami:rabbitmq:rabbitmq:latest
``` 

## <a name="deploy-a-new-vm-using-the-image-parameters"></a>Déployer une nouvelle machine virtuelle en utilisant les paramètres de l’image

Avec les informations sur l’image, vous pouvez la déployer à l’aide de la commande `az vm create`. 

Pour déployer une image qui n’a pas d’informations de plan, comme la dernière image Ubuntu Server 18.04 de Canonical, transmettez l’URN pour `--image` :

```azurecli-interactive
az group create --name myURNVM --location westus
az vm create \
   --resource-group myURNVM \
   --name myVM \
   --admin-username azureuser \
   --generate-ssh-keys \
   --image Canonical:UbuntuServer:18.04-LTS:latest 
```


Pour une image avec des paramètres de plan d’achat, comme l’image RabbitMQ Certified by Bitnami, vous transmettez l’URN pour `--image` et fournissez également les paramètres du plan d’achat :

```azurecli
az group create --name myPurchasePlanRG --location westus

az vm create \
   --resource-group myPurchasePlanRG \
   --name myVM \
   --admin-username azureuser \
   --generate-ssh-keys \
   --image bitnami:rabbitmq:rabbitmq:latest \
   --plan-name rabbitmq \
   --plan-product rabbitmq \
   --plan-publisher bitnami
```

Si vous recevez un message concernant l’acceptation des conditions de l’image, consultez la section [Accepter les conditions](#accept-the-terms). Assurez-vous que la sortie de `az vm image accept-terms` renvoie la valeur `"accepted": true,` indiquant que vous avez accepté les conditions de l’image.


## <a name="using-an-existing-vhd-with-purchase-plan-information"></a>Utilisation d’un VHD existant avec des informations de plan d’achat

Si vous disposez du VHD d’une machine virtuelle qui a été créée à l’aide d’une image payante de Place de marché Azure, vous devrez peut-être fournir les informations du plan d’achat lors de la création d’une machine virtuelle à partir de ce VHD. 

Si vous avez toujours la machine virtuelle d’origine, ou une autre machine virtuelle créée à l’aide de la même image, vous pouvez obtenir le nom du plan, l’éditeur et les informations sur le produit en utilisant [az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view). Cet exemple obtient une machine virtuelle nommée *myVM* dans le groupe de ressources *myResourceGroup*, puis affiche les informations du plan d’achat.

```azurepowershell-interactive
az vm get-instance-view -g myResourceGroup -n myVM --query plan
```

Si vous n’avez pas obtenu les informations du plan avant la suppression de la machine virtuelle d’origine, vous pouvez effectuer une [demande de support](https://ms.portal.azure.com/#create/Microsoft.Support). Les techniciens de support auront besoin du nom de la machine virtuelle, de l’ID d’abonnement et de l’horodatage de l’opération de suppression.

Une fois que vous avez les informations du plan, vous pouvez créer la machine virtuelle en utilisant le paramètre `--attach-os-disk` pour spécifier le VHD.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myNewVM \
  --nics myNic \
  --size Standard_DS1_v2 --os-type Linux \
  --attach-os-disk myVHD \
  --plan-name planName \
  --plan-publisher planPublisher \
  --plan-product planProduct 
```


## <a name="next-steps"></a>Étapes suivantes
Pour créer rapidement une machine virtuelle en utilisant les informations d’une image, consultez [Créer et gérer des machines virtuelles Linux avec l’interface Azure CLI](tutorial-manage-vm.md).
