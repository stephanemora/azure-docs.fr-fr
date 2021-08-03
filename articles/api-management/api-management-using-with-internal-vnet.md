---
title: Se connecter à un réseau virtuel interne à l’aide de la Gestion des API Azure
titleSuffix: Azure API Management
description: Découvrez comment installer et configurer la Gestion des API Azure sur un réseau virtuel interne
services: api-management
documentationcenter: ''
author: vladvino
editor: ''
ms.service: api-management
ms.topic: how-to
ms.date: 06/08/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8b25f1d92fb1ba7e7caa79ed3f4a31bdf9fa60fd
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111746306"
---
# <a name="connect-to-an-internal-virtual-network-using-azure-api-management"></a>Se connecter à un réseau virtuel interne à l’aide de la Gestion des API Azure 
Avec les réseaux virtuels (VNET) Azure, la Gestion des API Azure peut gérer des API inaccessibles sur Internet au moyen de plusieurs technologies VPN permettant d’établir la connexion. Vous pouvez déployer la Gestion des API via le mode [externe](./api-management-using-with-vnet.md) ou interne. Dans cet article, vous allez apprendre à déployer la Gestion des API en mode VNET interne.

Lorsque la Gestion des API se déploie en mode VNET interne, vous pouvez uniquement afficher les points de terminaison de service suivants dans un réseau virtuel dont vous contrôlez l’accès.
* La passerelle proxy
* Le portail des développeurs
* La gestion directe
* Git 

> [!NOTE]
> Aucun point de terminaison de service n’est inscrit sur le DNS public. Les points de terminaison de service restent inaccessibles à moins de [configurer le DNS](#apim-dns-configuration) pour le réseau virtuel.

Utilisez la Gestion des API en mode interne pour :

* Rendre les API hébergées dans votre centre de données privé accessibles de manière sécurisée à des tiers, en utilisant des connexions site à site ou des connexions Azure VPN ExpressRoute.
* Activer les scénarios de cloud hybride en exposant vos API cloud et sur site par le biais d’une passerelle commune.
* Gérer vos API hébergées dans plusieurs localisations géographiques à l’aide d’un seul point de terminaison de passerelle.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Prérequis

+ **Un abonnement Azure actif**. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Une instance du service Gestion des API Azure**. Pour en savoir plus, voir [Créer une instance de gestion des API Azure](get-started-create-service-instance.md).

[!INCLUDE [api-management-public-ip-for-vnet](../../includes/api-management-public-ip-for-vnet.md)]

Lorsqu’un service Gestion des API est déployé dans un réseau virtuel, [certains ports](./api-management-using-with-vnet.md#required-ports) sont utilisés et doivent être ouverts.

## <a name="creating-an-api-management-in-an-internal-vnet"></a><a name="enable-vpn"> </a>Création d’un service Gestion des API dans un réseau virtuel interne
Le service Gestion des API dans un réseau virtuel interne est hébergé derrière un équilibreur de charge interne. Le SKU de l’équilibreur de charge dépend de l’API de gestion utilisée pour créer le service. Pour plus d’informations, consultez la page [Références SKU Azure Load Balancer](../load-balancer/skus.md).

| Version de l'API | Hébergé derrière |
| ----------- | ------------- |
| 2020-12-01 | Un équilibreur de charge interne dans le SKU De base |
| 2020-01-01-preview, avec une adresse IP publique de votre abonnement | Un équilibreur de charge interne du SKU Standard |

### <a name="enable-a-vnet-connection-using-the-azure-portal"></a>Activer une connexion VNET à l’aide du portail Azure

1. Accédez à votre instance Gestion des API Azure dans le [portail Azure](https://portal.azure.com/).
1. Sélectionnez **Réseau virtuel**.
1. Configurez le type d’accès **Interne**. Pour obtenir le détail des étapes, consultez [Activer la connectivité aux réseaux virtuels à l’aide du portail Azure](api-management-using-with-vnet.md#enable-vnet-connectivity-using-the-azure-portal).

    ![Menu de configuration d’une instance Gestion des API Azure dans un réseau virtuel interne][api-management-using-internal-vnet-menu]

4. Sélectionnez **Enregistrer**.

Une fois le déploiement réussi, vous devez voir l’adresse IP virtuelle **privée** et l’adresse IP virtuelle **publique** de votre service Gestion des API s’afficher dans le panneau **Vue d’ensemble**. 

| Adresse IP virtuelle | Description |
| ----- | ----- |
| **Adresse IP virtuelle privée** | Adresse IP à charge équilibrée à partir du sous-réseau délégué par la Gestion des API, sur lequel vous pouvez accéder aux points de terminaison `gateway`, `portal`, `management` et `scm`. |  
| **Adresse IP virtuelle publique** | Utilisée *uniquement* pour le trafic du plan de contrôle vers le point de terminaison `management` sur `port 3443`. Peut être verrouillée sur l’étiquette de service [GestionApi][ServiceTags]. |  

![Tableau de bord Gestion des API avec un réseau virtuel interne configuré][api-management-internal-vnet-dashboard]

> [!NOTE]
> L’URL de la passerelle n’étant pas inscrite sur le DNS public, la console de test disponible sur le portail Azure ne fonctionnera pas pour le service de VNET **Interne** déployé. À la place, vous devez utiliser la console de test fournie sur le **Portail des développeurs**.

### <a name="deploy-api-management-into-vnet"></a><a name="deploy-apim-internal-vnet"> </a>Déployer la Gestion des API dans un réseau virtuel

Vous pouvez également activer une connectivité VNET à l’aide des méthodes suivantes.


### <a name="api-version-2020-12-01"></a>API version 2020-12-01

* [Modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.apimanagement/api-management-create-with-internal-vnet) Azure Resource Manager

     [![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.apimanagement%2Fapi-management-create-with-internal-vnet%2Fazuredeploy.json)

* Applets de commande Azure PowerShell – [Créer](/powershell/module/az.apimanagement/new-azapimanagement) ou [mettre à jour](/powershell/module/az.apimanagement/update-azapimanagementregion) une instance Gestion des API dans un réseau virtuel

## <a name="dns-configuration"></a><a name="apim-dns-configuration"></a>Configuration DNS
En mode VNET externe, Azure gère le DNS. En mode VNET interne, vous devez gérer votre propre DNS. Nous recommandons les actions suivantes :
1. Configuration d’une zone privée Azure DNS.
1. Liaison de la zone privée Azure DNS au réseau virtuel dans lequel vous avez déployé votre service Gestion des API. 

Apprenez à [configurer une zone privée dans Azure DNS](../dns/private-dns-getstarted-portal.md).

> [!NOTE]
> Le service Gestion des API n’écoute pas les demandes provenant des adresses IP. Il répond uniquement aux demandes pour le nom d’hôte configuré sur ses points de terminaison de service. Ces points de terminaison sont les suivants :
> * Passerelle
> * Le portail Azure
> * Le portail des développeurs
> * Point de terminaison de la gestion directe
> * Git

### <a name="access-on-default-host-names"></a>Accès sur les noms d’hôtes par défaut
Lorsque vous créez un service Gestion des API (`contosointernalvnet`, par exemple), les points de terminaison de service suivants sont configurés par défaut :

| Point de terminaison | Configuration du point de terminaison |
| ----- | ----- |
| Passerelle ou proxy | `contosointernalvnet.azure-api.net` |
| Portail des développeurs | `contosointernalvnet.portal.azure-api.net` |
| Le nouveau portail des développeurs | `contosointernalvnet.developer.azure-api.net` |
| Point de terminaison de la gestion directe | `contosointernalvnet.management.azure-api.net` |
| Git | `contosointernalvnet.scm.azure-api.net` |

Pour accéder à ces points de terminaison de service Gestion des API, vous pouvez créer une machine virtuelle dans un sous-réseau connecté au réseau virtuel dans lequel la Gestion des API est déployée. En supposant que l’adresse IP virtuelle interne de votre service est 10.1.0.5, vous pouvez effectuer le mappage du fichier hôte, `%SystemDrive%\drivers\etc\hosts`, de la façon suivante :

| Adresse IP virtuelle interne | Configuration du point de terminaison |
| ----- | ----- |
| 10.1.0.5 | `contosointernalvnet.azure-api.net` |
| 10.1.0.5 | `contosointernalvnet.portal.azure-api.net` |
| 10.1.0.5 | `contosointernalvnet.developer.azure-api.net` |
| 10.1.0.5 | `contosointernalvnet.management.azure-api.net` |
| 10.1.0.5 | `contosointernalvnet.scm.azure-api.net` |

Vous pouvez alors accéder à tous les points de terminaison de service à partir de la machine virtuelle que vous avez créée.
Si vous utilisez un serveur DNS personnalisé dans un réseau virtuel, vous pouvez également créer des enregistrements A DNS et accéder à ces points de terminaison à partir de l’endroit de votre choix dans votre réseau virtuel.

### <a name="access-on-custom-domain-names"></a>Accès sur des noms de domaines personnalisés

Si vous ne souhaitez pas accéder au service Gestion des API avec les noms d’hôtes par défaut : 

1. Configurez des noms de domaines personnalisés pour tous vos points de terminaison de service, comme indiqué dans l’image suivante :

   ![Configuration d’un domaine personnalisé pour la Gestion des API][api-management-custom-domain-name]

2. Créez des enregistrements dans votre serveur DNS pour accéder aux points de terminaison accessibles depuis votre réseau virtuel.

## <a name="routing"></a><a name="routing"> </a> Routage

* Une adresse IP virtuelle *privée* à charge équilibrée issue de la plage de sous-réseau (DIP) est réservée pour accéder aux points de terminaison du service Gestion des API depuis le réseau virtuel. 
    * Recherchez cette adresse IP privée dans le panneau Vue d’ensemble du service sur le portail Azure. 
    * Inscrivez cette adresse auprès des serveurs DNS utilisés par le réseau virtuel.
* Une adresse IP *publique* à charge équilibrée (adresse IP virtuelle) est également réservée pour fournir l’accès au point de terminaison du service de gestion sur `port 3443`. 
    * Recherchez cette adresse IP publique dans le panneau Vue d’ensemble du service sur le portail Azure. 
    * Utilisez uniquement l’adresse IP *publique* pour le trafic du plan de contrôle vers le point de terminaison `management` sur `port 3443`. 
    * Cette adresse IP peut être verrouillée sur l’étiquette de service [GestionApi][ServiceTags].
* Les adresses DIP seront affectées à chaque machine virtuelle du service, et utilisées pour accéder aux ressources *au sein* du réseau virtuel. Une adresse IP virtuelle est utilisée pour accéder aux ressources *situées en dehors* du réseau virtuel. Si une restriction d’IP liste des ressources sécurisées au sein du réseau virtuel, vous devez préciser la plage entière du sous-réseau où le service Gestion des API est déployé pour accorder ou restreindre l’accès à partir du service.
* Les adresses IP privée et publique à charge équilibrée se trouvent dans le panneau Vue d’ensemble sur le portail Azure.
* Si vous supprimez ou ajoutez le service dans le réseau virtuel, les adresses IP affectées à l’accès public et privé peuvent changer. Vous aurez peut-être besoin de mettre à jour les inscriptions DNS, les règles d’acheminement et les listes de restriction d’IP au sein du réseau virtuel.

## <a name="related-content"></a><a name="related-content"> </a>Contenu associé
Pour en savoir plus, consultez les articles suivants :
* [Problèmes de configuration réseau courants lors de la configuration de la Gestion des API Azure dans un réseau virtuel][Common network configuration problems]
* [Forum aux questions sur les réseaux virtuels](../virtual-network/virtual-networks-faq.md)
* [Création d’un enregistrement A dans DNS](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/updated-api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/updated-api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/updated-api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags
