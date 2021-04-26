---
title: Utiliser la gestion des API Azure avec des réseaux virtuels internes
titleSuffix: Azure API Management
description: Découvrez comment installer et configurer la Gestion des API Azure sur un réseau virtuel interne
services: api-management
documentationcenter: ''
author: vladvino
editor: ''
ms.service: api-management
ms.topic: how-to
ms.date: 04/12/2021
ms.author: apimpm
ms.openlocfilehash: 4298b291e5d183c31d30a548751599aeb3746c47
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534612"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Utiliser le service Gestion des API Azure avec un réseau virtuel interne
Avec les réseaux virtuels Azure, la Gestion des API Azure peut gérer des API inaccessibles sur Internet. Plusieurs technologies VPN sont disponibles pour établir la connexion. La Gestion des API peut être déployée selon deux modes principaux à l’intérieur d’un réseau virtuel :
* Externe
* Interne

En cas de déploiement de Gestion des API dans un mode réseau virtuel interne, tous les points de terminaison de service (passerelle proxy, portail des développeurs, gestion directe et Git) ne sont visibles que dans un réseau virtuel auquel vous contrôlez l’accès. Aucun point de terminaison de service n’est inscrit sur le serveur DNS Public.

> [!NOTE]
> En l'absence d'entrée DNS pour les points de terminaison de service, ces points de terminaison ne sont pas accessibles tant que [DNS est configuré](#apim-dns-configuration) pour le réseau virtuel.

Avec la Gestion des API en mode interne, vous pouvez effectuer les scénarios suivants :

* Rendre les API hébergées dans votre centre de données privé accessibles de l’extérieur en toute sécurité à des tiers à l’aide de connexions site à site ou de connexions Azure VPN ExpressRoute.
* Activer les scénarios de cloud hybride en exposant vos API cloud et sur site par le biais d’une passerelle commune.
* Gérer vos API hébergées dans plusieurs emplacements géographiques à l’aide d’un seul point de terminaison de passerelle.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Prérequis

Pour effectuer les étapes décrites dans cet article, vous devez disposer des éléments suivants :

+ **Un abonnement Azure actif**.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Une instance du service Gestion des API Azure**. Pour en savoir plus, voir [Créer une instance de gestion des API Azure](get-started-create-service-instance.md).

[!INCLUDE [api-management-public-ip-for-vnet](../../includes/api-management-public-ip-for-vnet.md)]

Lorsqu’un service Gestion des API est déployé dans un réseau virtuel, une [liste de ports](./api-management-using-with-vnet.md#required-ports) est utilisée et ces derniers doivent être ouverts. 

## <a name="creating-an-api-management-in-an-internal-virtual-network"></a><a name="enable-vpn"> </a>Créer une Gestion des API dans un réseau virtuel interne
Le service Gestion des API d’un réseau virtuel interne est hébergé derrière une référence SKU de base de l’équilibreur de charge interne si le service est créé avec l’API client version 2020-12-01. Pour le service créé avec des clients dotés de l’API version 2021-01-01-preview et ayant une adresse IP publique provenant de l’abonnement du client, il est hébergé derrière une référence standard d’équilibreur de charge interne. Pour plus d’informations, consultez la page [Références SKU Azure Load Balancer](../load-balancer/skus.md).

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Activer une connexion de réseau virtuel à l’aide du portail Azure

1. Accédez à votre instance Gestion des API Azure dans le [portail Azure](https://portal.azure.com/).
1. Sélectionnez **Réseau virtuel**.
1. Configurez le type d’accès **Interne**. Pour obtenir le détail des étapes, consultez [Activer la connectivité aux réseaux virtuels à l’aide du portail Azure](api-management-using-with-vnet.md#enable-vnet-connectivity-using-the-azure-portal).

    ![Menu pour configurer une Gestion des API Azure dans un réseau virtuel interne][api-management-using-internal-vnet-menu]

4. Sélectionnez **Enregistrer**.

Une fois le déploiement réussi, l'adresse IP virtuelle **privée** et l'adresse IP virtuelle **publique** de votre service Gestion des API doivent s'afficher dans le panneau Vue d’ensemble. L'adresse IP virtuelle **privée** est une adresse IP à charge équilibrée dans le sous-réseau délégué Gestion des API via lequel les points de terminaison `gateway`, `portal`, `management` et `scm` sont accessibles. L’adresse IP virtuelle **publique** est **uniquement** utilisée pour le trafic du plan de contrôle vers le point de terminaison `management` par le biais du port 3443, et peut être verrouillée sur la balise de service [ApiManagement][ServiceTags].

![Tableau de bord Gestion des API avec réseau virtuel interne configuré][api-management-internal-vnet-dashboard]

> [!NOTE]
> La Console de test disponible sur le portail Azure ne fonctionnera pas pour le service de réseau VNET **Interne** déployé, car l’URL de la passerelle n’est pas inscrite sur le DNS Public. À la place, vous devez utiliser la Console de test fournie sur le **Portail des développeurs**.

### <a name="deploy-api-management-into-virtual-network"></a><a name="deploy-apim-internal-vnet"> </a>Déployer Gestion des API dans Réseau virtuel

Vous pouvez également activer une connectivité de réseau virtuel à l’aide des méthodes suivantes.


### <a name="api-version-2020-12-01"></a>API version 2020-12-01

* [Modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/201-api-management-create-with-internal-vnet) Azure Resource Manager

     [![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-management-create-with-internal-vnet%2Fazuredeploy.json)

* Applets de commande Azure PowerShell : [créez](/powershell/module/az.apimanagement/new-azapimanagement) ou [mettez à jour](/powershell/module/az.apimanagement/update-azapimanagementregion) une instance Gestion des API dans un réseau virtuel

## <a name="dns-configuration"></a><a name="apim-dns-configuration"></a>Configuration DNS
Lorsque la Gestion des API se trouve en mode réseau virtuel externe, le DNS est géré par Azure. En mode réseau virtuel interne, vous devez gérer votre propre serveur DNS. Il est recommandé de configurer une zone privée Azure DNS et de la lier au service de gestion des API de réseau virtuel dans lequel elle est déployée. Apprenez à [configurer une zone privée dans Azure DNS](../dns/private-dns-getstarted-portal.md).

> [!NOTE]
> Le service Gestion des API n’écoute pas les demandes provenant des adresses IP. Il répond uniquement aux demandes pour le nom d’hôte configuré sur ses points de terminaison de service. Ces points de terminaison incluent une passerelle, le portail Azure, le portail des développeurs, un point de terminaison de gestion directe et Git.

### <a name="access-on-default-host-names"></a>Accès sur les noms d’hôtes par défaut
Lorsque vous créez un service Gestion des API, nommé « contosointernalvnet » par exemple, les points de terminaison de service suivants sont configurés par défaut :

   * Passerelle ou proxy : contosointernalvnet.azure-api.net

   * Portail des développeurs : contosointernalvnet.portal.azure-api.net

   * Nouveau portail des développeurs : contosointernalvnet.developer.azure-api.net

   * Point de terminaison de gestion directe : contosointernalvnet.management.azure-api.net

   * Git : contosointernalvnet.scm.azure-api.net

Pour accéder à ces points de terminaison de service Gestion des API, vous pouvez créer une machine virtuelle dans un sous-réseau connecté au réseau virtuel dans lequel la Gestion des API est déployée. En supposant que l’adresse IP virtuelle interne de votre service est 10.1.0.5, vous pouvez effectuer le mappage des fichiers hôtes, %SystemDrive%\drivers\etc\hosts, de la façon suivante :

   * 10.1.0.5     contosointernalvnet.azure-api.net

   * 10.1.0.5     contosointernalvnet.portal.azure-api.net

   * 10.1.0.5     contosointernalvnet.developer.azure-api.net

   * 10.1.0.5     contosointernalvnet.management.azure-api.net

   * 10.1.0.5     contosointernalvnet.scm.azure-api.net

Vous pouvez alors accéder à tous les points de terminaison de service à partir de la machine virtuelle que vous avez créée.
Si vous utilisez un serveur DNS personnalisé dans un réseau virtuel, vous pouvez également créer des enregistrements DNS A et accéder à ces points de terminaison à partir de l’endroit de votre choix dans votre réseau virtuel.

### <a name="access-on-custom-domain-names"></a>Accès sur des noms de domaines personnalisés

1. Si vous ne souhaitez pas accéder au service Gestion des API avec les noms d’hôtes par défaut, vous pouvez configurer des noms de domaines personnalisés pour tous vos points de terminaison de service, comme indiqué dans l’image suivante :

   ![Configuration d’un domaine personnalisé pour la Gestion des API][api-management-custom-domain-name]

2. Vous pouvez ensuite créer des enregistrements dans votre serveur DNS de façon à accéder aux points de terminaison qui ne sont accessibles qu’à partir de votre réseau virtuel.

## <a name="routing"></a><a name="routing"> </a> Routage

* Une adresse IP virtuelle *privée* à charge équilibrée de la plage de sous-réseau est réservée et utilisée pour accéder aux points de terminaison du service Gestion des API à partir du réseau virtuel. Cette adresse IP *privée* se trouve dans le panneau Vue d’ensemble du service sur le portail Azure. Cette adresse doit être inscrite sur les serveurs DNS utilisés par le réseau virtuel.
* Une adresse IP *publique* à charge équilibrée (adresse IP virtuelle) est également réservée pour fournir l’accès au point de terminaison de service de gestion sur le port 3443. Cette adresse IP *publique* se trouve dans le panneau Vue d’ensemble du service sur le portail Azure. L’adresse IP *publique* est uniquement utilisée pour le trafic du plan de contrôle vers le point de terminaison `management` par le biais du port 3443, et peut être verrouillée sur la balise de service [ApiManagement][ServiceTags].
* Les adresses IP de la plage d’adresses IP de sous-réseau (DIP) sont attribuées à chaque machine virtuelle du service et utilisées pour accéder aux ressources au sein du réseau virtuel. Une adresse IP publique est utilisée pour accéder aux ressources extérieures au réseau virtuel. Si des listes de restriction d’adresses IP sont utilisées pour sécuriser les ressources au sein du réseau virtuel, la plage entière du sous-réseau où le service Gestion des API est déployé doit être spécifiée pour accorder ou restreindre l’accès à partir du service.
* Les adresses IP privée et publique à charge équilibrée se trouvent dans le panneau Vue d’ensemble sur le portail Azure.
* Les adresses IP attribuées pour un accès public et privé peuvent changer si le service est supprimé, puis rajouté au réseau virtuel. Dans ce cas, il peut être nécessaire de mettre à jour les enregistrements DNS, règles d'acheminement et listes de restriction d'adresses IP au sein du réseau virtuel.

## <a name="related-content"></a><a name="related-content"> </a>Contenu associé
Pour en savoir plus, consultez les articles suivants :
* [Problèmes courants de configuration réseau lors de la configuration de la Gestion des API Azure dans un réseau virtuel][Common network configuration problems]
* [FAQ des réseaux virtuels](../virtual-network/virtual-networks-faq.md)
* [Création d’un enregistrement A dans DNS](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags
