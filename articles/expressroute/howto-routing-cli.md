---
title: 'Azure ExpressRoute : Configurer le peering : Interface de ligne de commande'
description: Cet article est conçu pour vous aider à créer et à provisionner le peering privé, public et Microsoft d’un circuit ExpressRoute. Cet article vous montre également comment vérifier l'état, mettre à jour ou supprimer des peerings pour votre circuit.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: how-to
ms.date: 04/24/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: cbde41bd79409255e1ebf5145548ea260ac8581d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84727039"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-cli"></a>Créer et modifier le peering d’un circuit ExpressRoute à l’aide de l’interface CLI

Cet article vous aide à créer et gérer la configuration de routage/le peering d’un circuit ExpressRoute dans le modèle de déploiement Resource Manager à l’aide de l’interface CLI. Vous pouvez également mettre à jour, supprimer et déprovisionner des peerings d’un circuit ExpressRoute, ainsi qu’en vérifier l’état. Si vous souhaitez utiliser une autre méthode pour votre circuit, sélectionnez un article dans la liste suivante :

> [!div class="op_single_selector"]
> * [Azure portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Peering public](about-public-peering.md)
> * [Vidéo - Peering privé](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vidéo - Peering Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (classique)](expressroute-howto-routing-classic.md)
> 

## <a name="configuration-prerequisites"></a>Prérequis de configuration

* Avant de commencer, installez la dernière version des commandes CLI (version 2.0 ou ultérieure). Pour plus d’informations sur l’installation des commandes CLI, consultez l’article [Installer l’interface de ligne de commande Azure](/cli/azure/install-azure-cli).
* Veillez à consulter les articles relatifs à la [configuration requise pour ExpressRoute](expressroute-prerequisites.md), à la [configuration requise pour le routage](expressroute-routing.md) et aux [workflows](expressroute-workflows.md) avant de commencer la configuration.
* Vous devez disposer d’un circuit ExpressRoute actif. Suivez les instructions permettant de [créer un circuit ExpressRoute](howto-circuit-cli.md) et faites-le activer par votre fournisseur de connectivité avant de poursuivre. Le circuit ExpressRoute doit être dans un état approvisionné et activé pour être en mesure d’exécuter les commandes de cet article.

Ces instructions s'appliquent uniquement aux circuits créés avec des fournisseurs de services proposant des services de connectivité de couche 2. Si vous utilisez un fournisseur de services proposant des services gérés de couche 3 (généralement un VPN IP, comme MPLS), votre fournisseur de connectivité configure et gère le routage pour vous.

Vous pouvez configurer le peering privé et le peering Microsoft pour un circuit ExpressRoute (le peering public Azure est déprécié pour les nouveaux circuits). Les peerings peuvent être configurés dans l’ordre de votre choix. Toutefois, vous devez veiller à finaliser une par une la configuration de chaque peering. Pour plus d’informations sur les domaines de routage et les peerings, consultez [Domaines de routage ExpressRoute](expressroute-circuit-peerings.md). Pour obtenir des informations sur le peering public, consultez [Peering public ExpressRoute](about-public-peering.md).

## <a name="microsoft-peering"></a><a name="msft"></a>Peering Microsoft

Cette section explique comment créer, obtenir, mettre à jour et supprimer la configuration de peering Microsoft pour un circuit ExpressRoute.

> [!IMPORTANT]
> Le peering Microsoft des circuits ExpressRoute qui ont été configurés avant le 1er août 2017 entraînera la publication de tous les préfixes de service via le peering Microsoft, même si aucun filtre d’itinéraire n’est défini. Le peering Microsoft des circuits ExpressRoute qui sont configurés le 1er août 2017 ou après n’entraînera la publication d’aucun préfixe tant qu’un filtre de routage n’aura pas été attaché au circuit. Pour plus d’informations, consultez [Configure a route filter for Microsoft peering](how-to-routefilter-powershell.md) (Configurer un filtre d’itinéraire pour l’homologation Microsoft).
> 
> 

### <a name="to-create-microsoft-peering"></a>Pour créer un peering Microsoft

1. Installez la dernière version de l’interface Azure CLI. Utilisez la dernière version de l’interface de ligne de commande (CLI) Azure. Examinez les [conditions préalables](expressroute-prerequisites.md) et les [flux de travail](expressroute-workflows.md) avant de commencer la configuration.

   ```azurecli
   az login
   ```

   Sélectionnez l’abonnement pour lequel vous souhaitez créer le circuit ExpressRoute.

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```
2. Créez un circuit ExpressRoute. Suivez les instructions permettant de [créer un circuit ExpressRoute](howto-circuit-cli.md) et faites-le approvisionner par votre fournisseur de connectivité. Si votre fournisseur de connectivité propose des services gérés de couche 3, vous pouvez lui demander d’activer le peering Microsoft pour vous. Dans ce cas, vous n'aurez pas besoin de suivre les instructions indiquées dans les sections suivantes. Toutefois, si votre fournisseur de connectivité ne gère pas le routage pour vous, après avoir créé votre circuit, continuez la configuration à l’aide de la procédure qui suit. 

3. Vérifiez que le circuit ExpressRoute est approvisionné et activé. Consultez l’exemple qui suit :

   ```azurecli
   az network express-route list
   ```

   La réponse ressemble à ce qui suit :

   ```output
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
    "bandwidthInMbps": 200,
    "peeringLocation": "Silicon Valley",
    "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

4. Configurez le peering Microsoft pour le circuit. Assurez-vous de disposer des informations suivantes avant de poursuivre.

   * Un sous-réseau /30 pour le lien principal. Il doit s’agir d’un préfixe IPv4 public valide vous appartenant et enregistré dans un registre RIR / IRR.
   * Un sous-réseau /30 pour le lien secondaire. Il doit s’agir d’un préfixe IPv4 public valide vous appartenant et enregistré dans un registre RIR / IRR.
   * Un ID VLAN valide pour établir ce peering. Assurez-vous qu'aucun autre peering sur le circuit n'utilise le même ID VLAN.
   * Un numéro AS pour le peering. Vous pouvez utiliser des numéros à 2 et 4 octets.
   * Préfixes publiés : Vous devez fournir la liste de tous les préfixes que vous prévoyez de publier sur la session BGP. Seuls les préfixes d'adresses IP publiques sont acceptés. Si vous prévoyez d’envoyer un jeu de préfixes, vous pouvez envoyer une liste séparée par des virgules. Ces préfixes doivent être enregistrés en votre nom dans un registre RIR / IRR.
   * **Facultatif -** ASN du client : si vous publiez des préfixes non enregistrés dans le numéro AS de peering, vous pouvez spécifier le numéro AS avec lequel ils sont enregistrés.
   * Nom du registre de routage : Vous pouvez spécifier les registres RIR/IRR sur lesquels le numéro AS et les préfixes sont inscrits.
   * **Facultatif :** un hachage MD5 si vous choisissez d’en utiliser un.

   Exécutez l’exemple suivant afin de configurer le peering Microsoft pour votre circuit :

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 123.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 123.0.0.4/30 --vlan-id 300 --peering-type MicrosoftPeering --advertised-public-prefixes 123.1.0.0/24
   ```

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>Pour afficher les détails du peering Microsoft

Vous pouvez obtenir les détails de la configuration à l’aide de l’exemple suivant :

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzureMicrosoftPeering
```
> [!IMPORTANT]
> Microsoft vérifie si les « préfixes publics publiés » et « ASN pairs » (ou « ASN client ») spécifiés vous sont attribués dans le registre de routage Internet. Si vous obtenez les préfixes publics d’une autre entité et si l’affectation n’est pas enregistrée avec le registre de routage, la validation automatique ne se termine pas et nécessite une validation manuelle. Si la validation automatique échoue, vous verrez « AdvertisedPublicPrefixesState » comme « validation requise » sur la sortie de la commande ci-dessus. 
> 
> Si vous voyez le message « Validation nécessaire », collectez le ou les documents qui affichent les préfixes publics qui sont attribués à votre organisation par l’entité répertoriée comme propriétaire des préfixes dans le registre de routage et soumettez ces documents pour validation manuelle en ouvrant un ticket de support comme indiqué ci-dessous. 
> 
>

Le résultat ressemble à l’exemple suivant :

```output
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzureMicrosoftPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": {
    "advertisedPublicPrefixes": [
        ""
      ],
     "advertisedPublicPrefixesState": "",
     "customerASN": ,
     "routingRegistryName": ""
  }
  "name": "AzureMicrosoftPeering",
  "peerAsn": ,
  "peeringType": "AzureMicrosoftPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Pour mettre à jour la configuration de peering Microsoft

Vous pouvez mettre à jour toute partie de la configuration. Les préfixes publiés du circuit sont mis à jour de 123.1.0.0/24 à 124.1.0.0/24 dans l’exemple suivant :

```azurecli
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroup --peering-type MicrosoftPeering --advertised-public-prefixes 124.1.0.0/24
```

### <a name="to-add-ipv6-microsoft-peering-settings-to-an-existing-ipv4-configuration"></a><a name="addIPv6msft"></a>Pour ajouter des paramètres de peering Microsoft IPv6 à une configuration IPv4 existante

```azurecli
az network express-route peering update -g ExpressRouteResourceGroup --circuit-name MyCircuit --peering-type MicrosoftPeering --ip-version ipv6 --primary-peer-subnet 2002:db00::/126 --secondary-peer-subnet 2003:db00::/126 --advertised-public-prefixes 2002:db00::/126
```

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Pour supprimer le peering Microsoft

Vous pouvez supprimer votre configuration de peering en exécutant l’exemple suivant :

```azurecli
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name MicrosoftPeering
```

## <a name="azure-private-peering"></a><a name="private"></a>Peering privé Azure

Cette section explique comment créer, obtenir, mettre à jour et supprimer la configuration de peering privé Azure pour un circuit ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Pour créer un peering privé Azure

1. Installez la dernière version de l’interface Azure CLI. Vous devez utiliser la dernière version de l’interface de ligne de commande (CLI) Azure.* Prenez connaissance de la [configuration requise](expressroute-prerequisites.md) et des [workflows](expressroute-workflows.md) avant de commencer la configuration.

   ```azurecli
   az login
   ```

   Sélectionnez l’abonnement pour lequel vous souhaitez créer le circuit ExpressRoute.

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```
2. Créez un circuit ExpressRoute. Suivez les instructions permettant de [créer un circuit ExpressRoute](howto-circuit-cli.md) et faites-le approvisionner par votre fournisseur de connectivité. Si votre fournisseur de connectivité propose des services gérés de couche 3, vous pouvez lui demander d’activer le peering privé Azure pour vous. Dans ce cas, vous n'aurez pas besoin de suivre les instructions indiquées dans les sections suivantes. Toutefois, si votre fournisseur de connectivité ne gère pas le routage pour vous, après avoir créé votre circuit, continuez la configuration à l’aide de la procédure qui suit.

3. Vérifiez que le circuit ExpressRoute est approvisionné et activé. Consultez l’exemple qui suit :

   ```azurecli
   az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
   ```

   La réponse ressemble à ce qui suit :

   ```output
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
   "bandwidthInMbps": 200,
   "peeringLocation": "Silicon Valley",
   "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

4. Configurez le peering privé Azure pour le circuit. Assurez-vous de disposer des éléments suivants avant de procéder aux étapes suivantes :

   * Un sous-réseau /30 pour le lien principal. Le sous-réseau ne doit faire partie d’aucun espace d’adressage réservé aux réseaux virtuels.
   * Un sous-réseau /30 pour le lien secondaire. Le sous-réseau ne doit faire partie d’aucun espace d’adressage réservé aux réseaux virtuels.
   * Un ID VLAN valide pour établir ce peering. Assurez-vous qu'aucun autre peering sur le circuit n'utilise le même ID VLAN.
   * Un numéro AS pour le peering. Vous pouvez utiliser des numéros à 2 et 4 octets. Vous pouvez utiliser un numéro AS privé pour ce peering. Veillez à ne pas utiliser le numéro 65515.
   * **Facultatif :** un hachage MD5 si vous choisissez d’en utiliser un.

   Utilisez l’exemple suivant pour configurer le peering privé Azure pour votre circuit :

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering
   ```

   Si vous choisissez d’utiliser un hachage MD5, utilisez l’exemple suivant :

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Veillez à spécifier votre numéro AS comme ASN de peering et non pas comme ASN client.
   > 
   > 

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>Pour afficher les détails d’un peering privé Azure

Vous pouvez obtenir les détails de la configuration à l’aide de l’exemple suivant :

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

Le résultat ressemble à l’exemple suivant :

```output
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePrivatePeering",
  "ipv6PeeringConfig": null,
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePrivatePeering",
  "peerAsn": 7671,
  "peeringType": "AzurePrivatePeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Pour mettre à jour la configuration de peering privé Azure

Vous pouvez mettre à jour toute partie de la configuration à l’aide de l’exemple suivant : Dans cet exemple, l’ID VLAN du circuit est mis à jour de 100 à 500.

```azurecli
az network express-route peering update --vlan-id 500 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Pour supprimer un peering privé Azure

Vous pouvez supprimer votre configuration de peering en exécutant l’exemple suivant :

> [!WARNING]
> Vous devez vous assurer que la totalité des réseaux virtuels et des connexions ExpressRoute Global Reach sont supprimés avant d’exécuter cet exemple. 
> 
> 

```azurecli
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```


## <a name="next-steps"></a>Étapes suivantes

Ensuite, [liez un réseau virtuel à un circuit ExpressRoute](howto-linkvnet-cli.md).

* Pour plus d'informations sur les workflows ExpressRoute, consultez [Workflows ExpressRoute](expressroute-workflows.md).
* Pour plus d’informations sur le peering du circuit, consultez [Circuits ExpressRoute et domaines de routage](expressroute-circuit-peerings.md).
* Pour plus d’informations sur l’utilisation des réseaux virtuels, consultez la page [Présentation du réseau virtuel](../virtual-network/virtual-networks-overview.md).
