---
title: Guide pratique de la Gestion des API Azure avec des réseaux virtuels internes | Microsoft Docs
description: Découvrez comment installer et configurer la Gestion des API Azure sur un réseau virtuel interne
services: api-management
documentationcenter: ''
author: vladvino
manager: kjoshi
editor: ''
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: apimpm
ms.openlocfilehash: 738dd451ca44801c799d793e6501c547655bd0e0
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/12/2019
ms.locfileid: "59524141"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Utiliser le service Gestion des API Azure avec un réseau virtuel interne
Avec les réseaux virtuels Azure, la Gestion des API Azure peut gérer des API inaccessibles sur Internet. Plusieurs technologies VPN sont disponibles pour établir la connexion. La Gestion des API peut être déployée selon deux modes principaux à l’intérieur d’un réseau virtuel :
* Externe
* Interne

En cas de déploiement de Gestion des API dans un mode réseau virtuel interne, tous les points de terminaison de service (passerelle, portail des développeurs, portail Azure, gestion directe et Git) ne sont visibles que dans un réseau virtuel auquel vous contrôlez l’accès. Aucun point de terminaison de service n’est inscrit sur le serveur DNS Public.

Avec la Gestion des API en mode interne, vous pouvez effectuer les scénarios suivants :

* Rendre les API hébergées dans votre centre de données privé accessibles de l’extérieur en toute sécurité à des tiers à l’aide de connexions site à site ou de connexions Azure VPN ExpressRoute.
* Activer les scénarios de cloud hybride en exposant vos API cloud et sur site par le biais d’une passerelle commune.
* Gérer vos API hébergées dans plusieurs emplacements géographiques à l’aide d’un seul point de terminaison de passerelle.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Conditions préalables

Pour effectuer les étapes décrites dans cet article, vous devez disposer des éléments suivants :

+ **Un abonnement Azure actif**.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Une instance du service Gestion des API Azure**. Pour en savoir plus, voir [Créer une instance de gestion des API Azure](get-started-create-service-instance.md).
+ Lorsqu’un service de gestion des API est déployé dans un réseau virtuel, un [la liste des ports](./api-management-using-with-vnet.md#required-ports) sont utilisés et doivent être ouverts. 

## <a name="enable-vpn"> </a>Créer une Gestion des API dans un réseau virtuel interne
Le service de gestion des API dans un réseau virtuel interne est hébergé derrière un [équilibreur de charge interne (classique)](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-get-started-ilb-classic-cloud). Ceci est la seule option disponible et ne peut pas être modifié.

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Activer une connexion de réseau virtuel à l’aide du portail Azure

1. Accédez à votre instance Gestion des API Azure dans le [portail Azure](https://portal.azure.com/).
2. Sélectionnez **Réseau virtuel**.
3. Configurez l’instance Gestion des API pour la déployer à l’intérieur du réseau virtuel.

    ![Menu pour configurer une Gestion des API Azure dans un réseau virtuel interne][api-management-using-internal-vnet-menu]

4. Sélectionnez **Enregistrer**.

Une fois le déploiement réussi, vous devez voir **privé** adresse IP virtuelle et **public** une adresse IP virtuelle de votre service de gestion des API dans le panneau de vue d’ensemble. Le **privé** adresse IP virtuelle est une charge équilibrée IP adresse dans la gestion des API déléguée sous-réseau sur lequel `gateway`, `portal`, `management` et `scm` points de terminaison est accessible. Le **public** sert d’adresse IP virtuelle **uniquement** pour contrôler le trafic de plan vers `management` point de terminaison sur le port 3443 et peuvent être verrouillées jusqu'à la [ApiManagement] [ ServiceTags] servicetag.

![Tableau de bord Gestion des API avec réseau virtuel interne configuré][api-management-internal-vnet-dashboard]

> [!NOTE]
> La Console de test disponible sur le portail Azure ne fonctionnera pas pour le service de réseau VNET **Interne** déployé, car l’URL de la passerelle n’est pas inscrite sur le DNS Public. À la place, vous devez utiliser la Console de test fournie sur le **Portail des développeurs**.

### <a name="enable-a-virtual-network-connection-by-using-powershell-cmdlets"></a>Activer une connexion de réseau virtuel à l’aide des applets de commande PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Vous pouvez également activer une connectivité de réseau virtuel à l’aide d’applets de commande PowerShell.

* Créer un service de gestion des API au sein d’un réseau virtuel : Utilisez l’applet de commande [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) pour créer un service de gestion des API Azure au sein d’un réseau virtuel et le configurer pour utiliser le type de réseau virtuel interne.

* Mise à jour d’un déploiement existant d’un service de gestion des API à l’intérieur d’un réseau virtuel : Utilisez l’applet de commande [AzApiManagementRegion de mise à jour](/powershell/module/az.apimanagement/update-azapimanagementregion) pour déplacer un service de gestion des API existant à l’intérieur d’un réseau virtuel et le configurer pour utiliser le type de réseau virtuel interne.

## <a name="apim-dns-configuration"></a>Configuration DNS
Lorsque la Gestion des API se trouve en mode réseau virtuel externe, le DNS est géré par Azure. En mode réseau virtuel interne, vous devez gérer votre propre routage.

> [!NOTE]
> Le service Gestion des API n’écoute pas les demandes provenant des adresses IP. Il répond uniquement aux demandes pour le nom d’hôte configuré sur ses points de terminaison de service. Ces points de terminaison incluent une passerelle, le portail Azure, le portail des développeurs, un point de terminaison de gestion directe et Git.

### <a name="access-on-default-host-names"></a>Accès sur les noms d’hôtes par défaut
Lorsque vous créez un service de gestion des API, nommé « contosointernalvnet », par exemple, les points de terminaison de service suivants sont configurés par défaut :

   * Passerelle ou proxy : contosointernalvnet.azure-API.NET

   * Le portail Azure et le portail des développeurs : contosointernalvnet.portal.azure-API.NET

   * Point de terminaison de gestion directe : contosointernalvnet.management.azure-API.NET

   * GIT : contosointernalvnet.scm.azure-API.NET

Pour accéder à ces points de terminaison de service Gestion des API, vous pouvez créer une machine virtuelle dans un sous-réseau connecté au réseau virtuel dans lequel la Gestion des API est déployée. En supposant que l’adresse IP virtuelle interne de votre service est 10.1.0.5, vous pouvez mapper le fichier hosts, % SystemDrive%\drivers\etc\hosts, comme suit :

   * 10.1.0.5     contosointernalvnet.azure-api.net

   * 10.1.0.5     contosointernalvnet.portal.azure-api.net

   * 10.1.0.5     contosointernalvnet.management.azure-api.net

   * 10.1.0.5     contosointernalvnet.scm.azure-api.net

Vous pouvez alors accéder à tous les points de terminaison de service à partir de la machine virtuelle que vous avez créée.
Si vous utilisez un serveur DNS personnalisé dans un réseau virtuel, vous pouvez également créer des enregistrements DNS A et accéder à ces points de terminaison à partir de l’endroit de votre choix dans votre réseau virtuel.

### <a name="access-on-custom-domain-names"></a>Accès sur des noms de domaines personnalisés

1. Si vous ne souhaitez pas accéder au service Gestion des API avec les noms d’hôtes par défaut, vous pouvez configurer des noms de domaines personnalisés pour tous vos points de terminaison de service, comme indiqué dans l’image suivante :

   ![Configuration d’un domaine personnalisé pour la Gestion des API][api-management-custom-domain-name]

2. Vous pouvez ensuite créer des enregistrements dans votre serveur DNS de façon à accéder aux points de terminaison qui ne sont accessibles qu’à partir de votre réseau virtuel.

## <a name="routing"> </a> Routage
+ Une adresse IP virtuelle privée à charge équilibrée de la plage de sous-réseau est réservée et utilisée pour accéder aux points de terminaison du service Gestion des API service à partir du réseau virtuel.
+ Une adresse IP publique à charge équilibrée (adresse IP virtuelle) est également réservée pour fournir l’accès au point de terminaison de service de gestion uniquement sur le port 3443.
+ Une adresse IP d’une plage d’adresses IP de sous-réseau (adresse IP dynamique) est utilisée pour accéder aux ressources sur le réseau virtuel, tandis qu’une adresse IP publique (adresse IP virtuelle) est utilisée pour accéder aux ressources à l’extérieur du réseau virtuel.
+ Les adresses IP privée et publique à charge équilibrée se trouvent dans le panneau Vue d’ensemble/Bases sur le portail Azure.

## <a name="related-content"></a>Contenu connexe
Pour en savoir plus, consultez les articles suivants :
* [Problèmes courants de configuration réseau lors de la configuration de la Gestion des API Azure dans un réseau virtuel][Common network configuration problems]
* [FAQ des réseaux virtuels](../virtual-network/virtual-networks-faq.md)
* [Création d’un enregistrement A dans DNS](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/security-overview.md#service-tags

