---
title: Guide pratique pour configurer le pare-feu IP de votre compte Azure Cosmos
description: Découvrez comment configurer les stratégies de contrôle d’accès IP pour la prise en charge du pare-feu dans les comptes de base de données Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: d9a00bccb83fc60c96594ffacc5abde98c0f8470
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51632575"
---
# <a name="how-to-configure-ip-firewall-for-your-azure-cosmos-account"></a>Guide pratique pour configurer le pare-feu IP de votre compte Azure Cosmos

Vous pouvez sécuriser les données stockées dans votre compte Azure Cosmos à l’aide de pare-feu IP. Azure Cosmos DB prend en charge les contrôles d’accès basés sur IP pour le pare-feu entrant. Vous pouvez définir un pare-feu IP sur le compte Azure Cosmos de l’une des manières suivantes :

1. À partir du portail Azure
2. De manière déclarative en utilisant un modèle Azure Resource Manager
3. Programmatiquement avec Azure CLI ou Azure Powershell en mettant à jour la propriété **ipRangeFilter**.

## <a id="configure-ip-policy"></a> Configuration du pare-feu IP à l’aide du portail Azure

Pour définir la stratégie de contrôle d’accès IP sur le Portail Azure, accédez à la page du compte Azure Cosmos DB, cliquez sur **Pare-feu et réseaux virtuels** dans le menu de navigation, remplacez la valeur **Autoriser l’accès à partir de** par **Réseaux sélectionnés**, puis cliquez sur **Enregistrer**. 

![Capture d’écran montrant comment ouvrir la page Pare-feu sur le Portail Azure](./media/how-to-configure-firewall/azure-portal-firewall.png)

Une fois le contrôle d’accès IP activé, le portail Azure vous permet de spécifier des adresses IP, des plages d’adresses IP ainsi que des commutateurs pour pouvoir accéder à d’autres services Azure et au portail Azure. Plus d’informations sur ces commutateurs sont fournies dans les sections suivantes.

> [!NOTE]
> Une fois la stratégie de contrôle d’accès IP activée pour votre compte Azure Cosmos, toutes les demandes envoyées à votre compte Azure Cosmos à partir d’ordinateurs ne figurant pas dans la liste autorisée de plages d’adresses IP sont refusées. L’exploration de ressources Azure Cosmos DB à partir du portail est également bloquée pour garantir l’intégrité du contrôle d’accès.

### <a name="allow-requests-from-the-azure-portal"></a>Autoriser les demandes à partir du portail Azure 

Lorsque vous activez une stratégie de contrôle d’accès IP par programme, vous devez ajouter l’adresse IP du Portail Azure à la propriété **ipRangeFilter** pour conserver l’accès. Les adresses IP du portail sont :

|Région|Adresse IP|
|------|----------|
|Allemagne|51.4.229.218|
|Chine|139.217.8.252|
|Gouvernement des États-Unis|52.244.48.71|
|Toutes les régions sauf les trois ci-dessus|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|

Vous pouvez activer l’accès au portail Azure en choisissant l’option **Autoriser l’accès à partir du portail Azure** comme illustré dans la capture d’écran suivante : 

![Capture d’écran montrant comment activer l’accès au Portail Azure](./media/how-to-configure-firewall/enable-azure-portal.png)

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>Autoriser les demandes à partir de centres de données Azure internationaux ou d’autres sources au sein d’Azure

Si vous accédez au compte Azure Cosmos à partir de services qui ne fournissent pas d’adresse IP statique, comme Stream Analytics, Azure Functions, etc., vous pouvez toujours utiliser le pare-feu IP pour limiter l’accès. Pour autoriser l’accès au compte Azure Cosmos à partir de ces services, vous devez activer un paramètre de pare-feu. Pour activer ce paramètre de pare-feu, ajoutez l’adresse IP 0.0.0.0 à la liste des adresses IP autorisées. L’adresse IP 0.0.0.0 limite les demandes envoyées à votre compte Azure Cosmos DB à partir de la plage d’adresses IP du centre de données Azure. Ce paramètre n’autorise aucune autre plage d’adresses IP à accéder à votre compte Azure Cosmos DB.

> [!NOTE]
> Cette option configure le pare-feu pour autoriser toutes les demandes d’Azure, notamment les demandes envoyées par les abonnements d’autres clients déployés dans Azure. Comme la liste des adresses IP autorisées par cette option est grande, elle limite l’efficacité de la stratégie de pare-feu. Vous devez utiliser cette option uniquement si vos demandes ne proviennent pas d’adresses IP statiques ou de sous-réseaux de réseaux virtuels. Comme le portail est déployé dans Azure, quand vous choisissez cette option, vous autorisez automatiquement l’accès à partir du portail Azure.

Vous pouvez autoriser l’accès au portail Azure en choisissant l’option **Accepter les connexions des centres de données Azure publics**, comme illustré dans la capture d’écran suivante : 

![Capture d’écran montrant comment ouvrir la page Pare-feu sur le Portail Azure](./media/how-to-configure-firewall/enable-azure-services.png)

### <a name="requests-from-your-current-ip"></a>Demandes à partir de votre adresse IP actuelle

Pour simplifier le développement, le portail Azure vous aide à identifier et à ajouter l’adresse IP de votre ordinateur client à la liste autorisée, pour que les applications qui exécutent votre ordinateur puissent accéder à votre compte Azure Cosmos. L’adresse IP du client est automatiquement détectée par le portail. Il peut s’agir de l’adresse IP de votre ordinateur ou de votre passerelle réseau. N’oubliez pas de supprimer cette adresse IP avant de mettre vos charges de travail en production. 

Pour activer votre IP actuel, sélectionnez **Ajouter mon IP actuel**, qui ajoute votre IP actuel à la liste d’IP, puis cliquez sur **Enregistrer**.

![Capture d’écran montrant comment configurer les paramètres de pare-feu pour l’adresse IP actuelle](./media/how-to-configure-firewall/enable-current-ip.png)

### <a name="requests-from-cloud-services"></a>Demandes à partir de services cloud

Dans Azure, les services cloud sont une méthode courante d’hébergement de la logique de service de couche intermédiaire à l’aide d’Azure Cosmos DB. Pour autoriser l’accès à votre compte Azure Cosmos à partir d’un service cloud, l’adresse IP publique de ce dernier doit être ajoutée à la liste autorisée d’adresses IP associées à votre compte Azure Cosmos en [configurant la stratégie de contrôle d’accès IP](#configure-ip-policy). De cette façon, toutes les instances de rôle des services cloud ont accès à votre compte Azure Cosmos. Vous pouvez récupérer des adresses IP pour vos services cloud dans le portail Azure, comme illustré dans la capture d’écran suivante :

![Capture d’écran illustrant l’adresse IP publique pour un service cloud affichée dans le portail Azure](./media/how-to-configure-firewall/public-ip-addresses.png)

Quand vous effectuer un scale-out de votre service cloud en ajoutant des instances de rôle supplémentaires, ces nouvelles instances ont automatiquement accès au compte Azure Cosmos, car elles font partie du même service cloud.

### <a name="requests-from-virtual-machines"></a>Demandes à partir de machines virtuelles

Des [machines virtuelles](https://azure.microsoft.com/services/virtual-machines/) ou des [jeux de mise à l’échelle de machine virtuelle](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) peuvent également être utilisés pour héberger les services de couche intermédiaire à l’aide d’Azure Cosmos DB. Pour configurer votre compte Azure Cosmos afin qu’il autorise l’accès à partir de machines virtuelles, les adresses IP publiques des machines virtuelles ou du groupe de machines virtuelles identiques doivent figurer parmi les adresses IP autorisées de votre compte Azure Cosmos en [configurant la stratégie de contrôle d’accès IP](#configure-ip-policy). Vous pouvez récupérer des adresses IP pour les machines virtuelles dans le portail Azure, comme illustré dans la capture d’écran suivante :

![Capture d’écran illustrant une adresse IP publique pour une machine virtuelle affichée dans le portail Azure](./media/how-to-configure-firewall/public-ip-addresses-dns.png)

Quand vous ajoutez des instances de machine virtuelle supplémentaires au groupe, elles ont automatiquement accès à votre compte Azure Cosmos.

### <a name="requests-from-the-internet"></a>Demandes à partir d’Internet

Quand vous accédez à votre compte Azure Cosmos à partir d’un ordinateur sur Internet, l’adresse IP ou la plage d’adresses IP de l’ordinateur doit être ajoutée à la liste d’adresses IP autorisées pour votre compte.

## <a id="configure-ip-firewall-arm"></a>Configurer le pare-feu IP à l’aide du modèle Resource Manager

Pour configurer le contrôle d’accès à votre compte Azure Cosmos, le modèle Resource Manager doit spécifier l’attribut **ipRangeFilter** avec une liste de plages d’adresses IP à ajouter à une liste verte. Par exemple, ajoutez le code JSON suivant à votre modèle :

```json
   {
     "apiVersion": "2015-04-08",
     "type": "Microsoft.DocumentDB/databaseAccounts",
     "kind": "GlobalDocumentDB",
     "name": "[parameters('databaseAccountName')]",
     "location": "[resourceGroup().location]",
     "properties": {
     "databaseAccountOfferType": "Standard",
     "name": "[parameters('databaseAccountName')]",
     "ipRangeFilter":"183.240.196.255, 104.42.195.92,40.76.54.131, 52.176.6.30,52.169.50.45,52.187.184.26"
   }
```

## <a id="configure-ip-firewall-cli"></a>Configuration de la stratégie de contrôle d’accès IP à l’aide d’Azure CLI

La commande suivante montre comment créer un compte Azure Cosmos avec le contrôle d’accès IP : 

```azurecli-interactive

name="<Azure Cosmos account name>"
resourceGroupName="<Resource group name>"

az cosmosdb create \
  --name $name \
  --kind GlobalDocumentDB \
  --resource-group $resourceGroupName \
  --max-interval 10 \
  --max-staleness-prefix 200 \
  --ip-range-filter "183.240.196.255, 104.42.195.92,40.76.54.131, 52.176.6.30,52.169.50.45,52.187.184.26"
```

Pour mettre à jour les paramètres de pare-feu pour un compte existant, exécutez la commande suivante :

```azurecli-interactive
az cosmosdb update \
      --name $name \
      --resource-group $resourceGroupName \
      --ip-range-filter "183.240.196.255, 104.42.195.92,40.76.54.131, 52.176.6.30,52.169.50.45,52.187.184.26"
```

## <a id="troubleshoot-ip-firewall"></a>Comment résoudre les problèmes de stratégie de contrôle d’accès IP

Vous pouvez résoudre les problèmes de stratégie de contrôle d’accès IP en utilisant les options suivantes : 

### <a name="azure-portal"></a>Portail Azure 
En activant une stratégie de contrôle d’accès IP pour votre compte Azure Cosmos, toutes les demandes envoyées à votre compte à partir d’ordinateurs ne figurant pas dans la liste autorisée de plages d’adresses IP sont bloquées. Par conséquent, si vous voulez autoriser des opérations de plan de données du portail comme la navigation dans les conteneurs et l’interrogation de documents, vous devez autoriser explicitement l’accès au portail Azure à l’aide du volet **Pare-feu** du portail.

### <a name="sdks"></a>Kits SDK 
Quand vous accédez à des ressources Azure Cosmos à l’aide de kits SDK sur des ordinateurs qui ne sont pas dans la liste autorisée, **une réponse générique 404 Introuvable est retournée sans plus de détails**. Vérifiez la liste d’adresses IP autorisées pour votre compte et vérifiez que la configuration de stratégie appropriée est appliquée à votre compte Cosmos. 

### <a name="check-source-ips-in-blocked-requests"></a>Vérifier les adresses IP sources dans les demandes bloquées
Activez la journalisation des diagnostics sur votre compte Azure Cosmos, ces journaux montrent chaque demande et réponse. **Les messages associés au pare-feu sont journalisés en interne avec le code de retour 403**. En filtrant ces messages, vous pouvez voir les adresses IP sources des demandes bloquées. Consultez [Journalisation des diagnostics Azure Cosmos DB](logging.md).

### <a name="requests-from-subnet-with-service-endpoint-for-azure-cosmos-database-enabled"></a>Demandes à partir d’un sous-réseau avec un point de terminaison de service activé pour la base de données Azure Cosmos
Les demandes à partir d’un sous-réseau de réseau virtuel avec un point de terminaison de service activé pour Azure Cosmos DB envoient l’identité de réseau virtuel et de sous-réseau aux comptes Azure Cosmos. Comme ces demandes n’ont pas l’adresse IP publique de la source, elles sont rejetées par les filtres IP. Pour autoriser l’accès à partir de sous-réseaux spécifiques de réseaux virtuels, ajoutez la liste de contrôle d’accès des réseaux virtuels indiquée dans [Comment configurer l’accès à votre compte Azure Cosmos à partir d’un réseau virtuel et d’un sous-réseau](how-to-configure-vnet-service-endpoint.md). L’application des règles de pare-feu peut prendre jusqu’à 15 minutes.


## <a name="next-steps"></a>Étapes suivantes

Pour configurer un point de terminaison de service de réseau virtuel pour votre compte Azure Cosmos DB, consultez les articles suivants :

* [Contrôle d’accès à votre compte Azure Cosmos à partir d’un réseau virtuel et d’un sous-réseau](vnet-service-endpoint.md)
* [Guide pratique pour configurer l’accès à votre compte Azure Cosmos à partir d’un réseau virtuel et d’un sous-réseau](how-to-configure-vnet-service-endpoint.md)


