---
title: Utilisation de points de terminaison privés avec Stockage Azure | Microsoft Docs
description: Vue d’ensemble des points de terminaison privés pour un accès sécurisé aux comptes de stockage à partir de réseaux virtuels.
services: storage
author: santoshc
ms.service: storage
ms.topic: article
ms.date: 09/25/2019
ms.author: santoshc
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 85b59c6549a62f7d9945f5739d1d0fde8c0fa3b8
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77158908"
---
# <a name="using-private-endpoints-for-azure-storage-preview"></a>Utilisation de points de terminaison privés pour Stockage Azure (préversion)

Vous pouvez utiliser des [points de terminaison privés](../../private-link/private-endpoint-overview.md) pour vos comptes Stockage Azure afin de permettre aux clients d’un réseau virtuel (VNet) d’accéder en toute sécurité aux données via une liaison [Private Link](../../private-link/private-link-overview.md). Le point de terminaison privé utilise une adresse IP de l’espace d’adressage du réseau virtuel pour votre service de compte de stockage. Le trafic réseau entre les clients sur le réseau virtuel et le compte de stockage traverse le réseau virtuel et une liaison privée sur le réseau principal de Microsoft, ce qui élimine l’exposition sur l’Internet public.

L’utilisation de points de terminaison privés pour votre compte de stockage vous permet d’effectuer les opérations suivantes :
- Sécurisez votre compte de stockage en configurant le pare-feu de stockage pour bloquer toutes les connexions sur le point de terminaison public pour le service de stockage.
- Améliorez la sécurité du réseau virtuel en vous permettant de bloquer l’exfiltration des données à partir du réseau virtuel.
- Connectez-vous en toute sécurité aux comptes de stockage à partir de réseaux locaux qui se connectent au réseau virtuel à l’aide de [VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [d’ExpressRoutes](../../expressroute/expressroute-locations.md) avec le peering privé.

## <a name="conceptual-overview"></a>Vue d’ensemble conceptuelle
![Vue d’ensemble des points de terminaison privés pour Stockage Azure](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

Un point de terminaison privé est une interface réseau spéciale pour un service Azure dans votre [réseau virtuel](../../virtual-network/virtual-networks-overview.md). Lorsque vous créez un point de terminaison privé pour votre compte de stockage, il offre une connectivité sécurisée entre les clients sur votre réseau virtuel et votre stockage. Une adresse IP est attribuée au point de terminaison privé à partir de la plage d’adresses IP de votre réseau virtuel. La connexion entre le point de terminaison privé et le service de stockage utilise une liaison privée.

Les applications du réseau virtuel peuvent se connecter en toute transparence au service de stockage sur le point de terminaison privé, **à l’aide des mêmes chaînes de connexion et mécanismes d’autorisation qu’ils utiliseraient dans tous les cas**. Les points de terminaison privés peuvent être utilisés avec tous les protocoles pris en charge par le compte de stockage, notamment REST et SMB.

Vous pouvez créer des points de terminaison privés dans des sous-réseaux qui utilisent des [points de terminaison de service](../../virtual-network/virtual-network-service-endpoints-overview.md). Les clients d’un sous-réseau peuvent donc se connecter à un compte de stockage à l’aide d’un point de terminaison privé, tout en utilisant des points de terminaison de service pour accéder à d’autres.

Quand vous créez un point de terminaison privé pour un service de stockage dans votre réseau virtuel, une demande de consentement est envoyée pour approbation au propriétaire du compte de stockage. Si l’utilisateur qui demande la création du point de terminaison privé est également propriétaire du compte de stockage, cette demande de consentement est automatiquement approuvée.

Les propriétaires de comptes de stockage peuvent gérer les demandes de consentement et les points de terminaison privés, via l’onglet « *Points de terminaison privés* » du compte de stockage dans le [portail Azure](https://portal.azure.com).

> [!TIP]
> Si vous souhaitez restreindre l’accès à votre compte de stockage uniquement par le biais du point de terminaison privé, configurez le pare-feu de stockage pour refuser ou contrôler l’accès via le point de terminaison public.

Vous pouvez sécuriser votre compte de stockage pour accepter uniquement les connexions à partir de votre réseau virtuel, en [configurant le pare-feu de stockage](storage-network-security.md#change-the-default-network-access-rule) afin de refuser l’accès via son point de terminaison public par défaut. Vous n’avez pas besoin d’une règle de pare-feu pour autoriser le trafic à partir d’un réseau virtuel doté d’un point de terminaison privé, puisque le pare-feu de stockage contrôle uniquement l’accès via le point de terminaison public. Les points de terminaison privés reposent plutôt sur le flux de consentement pour accorder l’accès aux sous-réseaux au service de stockage.

### <a name="private-endpoints-for-storage-service"></a>Points de terminaison privés pour le service de stockage

Lorsque vous créez le point de terminaison privé, vous devez spécifier le compte de stockage et le service de stockage auxquels il se connecte. Vous avez besoin d’un point de terminaison privé distinct pour chaque service de stockage dans un compte de stockage auquel vous devez accéder, à savoir [Objets Blob](../blobs/storage-blobs-overview.md), [Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md), [Fichiers](../files/storage-files-introduction.md), [Files d’attente](../queues/storage-queues-introduction.md), [Tables](../tables/table-storage-overview.md) ou [Sites web statiques](../blobs/storage-blob-static-website.md).

> [!TIP]
> Créez un point de terminaison privé distinct pour l’instance secondaire du service de stockage afin d’améliorer les performances de lecture sur les comptes RA-GRS.

Pour accéder en lecture à la région secondaire avec un compte de stockage configuré pour le stockage géoredondant, vous devez disposer de points de terminaison privés distincts pour les instances principale et secondaire du service. Vous n’avez pas besoin de créer un point de terminaison privé pour l’instance secondaire pour le **basculement**. Le point de terminaison privé se connecte automatiquement à la nouvelle instance principale après le basculement. Pour plus d'informations sur les options de redondance du stockage, consultez [Redondance du Stockage Azure](storage-redundancy.md).

#### <a name="resources"></a>Ressources

Pour plus d’informations sur la création d’un point de terminaison privé pour votre compte de stockage, reportez-vous aux articles suivants :

- [Se connecter en privé à un compte de stockage à partir de l’expérience Compte de stockage dans le portail Azure](../../private-link/create-private-endpoint-storage-portal.md)
- [Créer un point de terminaison privé à l’aide du centre Private Link dans le portail Azure](../../private-link/create-private-endpoint-portal.md)
- [Créer un point de terminaison privé à l’aide d’Azure CLI](../../private-link/create-private-endpoint-cli.md)
- [Créer un point de terminaison privé à l’aide d’Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)

### <a name="connecting-to-private-endpoints"></a>Connexion à des points de terminaison privés

Les clients sur un réseau virtuel utilisant le point de terminaison privé doivent utiliser la même chaîne de connexion pour le compte de stockage que les clients se connectant au point de terminaison public. Nous nous appuyons sur la résolution DNS pour acheminer automatiquement les connexions entre le réseau virtuel et le compte de stockage via une liaison privée.

> [!IMPORTANT]
> Utilisez la même chaîne de connexion pour vous connecter au compte de stockage avec des points de terminaison privés, comme vous le feriez dans le cas contraire. Veuillez ne pas vous connecter au compte de stockage à l’aide de son URL de sous-domaine « *privatelink* ».

Nous créons une [zone DNS privée](../../dns/private-dns-overview.md) attachée au réseau virtuel avec les mises à jour nécessaires pour les points de terminaison privés, par défaut. Toutefois, si vous utilisez votre propre serveur DNS, vous devrez peut-être apporter des modifications supplémentaires à votre configuration DNS. La section sur les [modifications DNS](#dns-changes-for-private-endpoints) ci-dessous décrit les mises à jour requises pour les points de terminaison privés.

## <a name="dns-changes-for-private-endpoints"></a>Modifications DNS pour les points de terminaison privés

Quand vous créez un point de terminaison privé, l’enregistrement de la ressource CNAME DNS pour le compte de stockage est mis à jour avec un alias dans un sous-domaine avec le préfixe « *privatelink* ». Par défaut, nous créons également une [zone DNS privée](../../dns/private-dns-overview.md) correspondant au sous-domaine « *privatelink* », avec les enregistrements de ressource DNS A pour les points de terminaison privés.

Lorsque vous résolvez l’URL du point de terminaison de stockage à l’extérieur du réseau virtuel avec le point de terminaison privé, elle correspond au point de terminaison public du service de stockage. En cas de résolution à partir du réseau virtuel hébergeant le point de terminaison privé, l’URL du point de terminaison de stockage correspond à l’adresse IP du point de terminaison privé.

Pour l’exemple illustré ci-dessus, les enregistrements de ressources DNS pour le compte de stockage « StorageAccountA », lorsqu’ils sont résolus à l’extérieur du réseau virtuel hébergeant le point de terminaison privé, sont les suivants :

| Name                                                  | Type  | Valeur                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<point de terminaison public du service de stockage\>                   |
| \<point de terminaison public du service de stockage\>                   | Un     | \<adresse IP publique du service de stockage\>                 |

Comme mentionné précédemment, vous pouvez refuser ou contrôler l’accès pour les clients en dehors du réseau virtuel via le point de terminaison public à l’aide du pare-feu de stockage.

Les enregistrements de ressources DNS pour StorageAccountA, lorsqu’ils sont résolus par un client dans le réseau virtuel hébergeant le point de terminaison privé, sont les suivants :

| Name                                                  | Type  | Valeur                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | Un     | 10.1.1.5                                              |

Cette approche permet d’accéder au compte de stockage **avec la même chaîne de connexion** pour les clients sur le réseau virtuel hébergeant les points de terminaison privés, ainsi que des clients en dehors du réseau virtuel.

Si vous utilisez un serveur DNS personnalisé sur votre réseau, les clients doivent pouvoir résoudre le nom de domaine complet du point de terminaison du compte de stockage vers l’adresse IP du point de terminaison privé. Vous devez configurer votre serveur DNS pour déléguer votre sous-domaine de liaison privée à la zone DNS privée du réseau virtuel, ou configurer les enregistrements A pour « *StorageAccountA.privatelink.blob.core.windows.net* » avec l’adresse IP du point de terminaison privé.

> [!TIP]
> Lorsque vous utilisez un serveur DNS personnalisé ou local, vous devez configurer votre serveur DNS pour résoudre le nom du compte de stockage dans le sous-domaine « privatelink » vers l’adresse IP du point de terminaison privé. Pour ce faire, vous pouvez déléguer le sous-domaine « privatelink » à la zone DNS privée du réseau virtuel ou configurer la zone DNS sur votre serveur DNS et ajouter les enregistrements A DNS.

Les noms des zones DNS recommandés pour les points de terminaison privés pour les services de stockage sont les suivants :

| Service de stockage        | Nom de la zone                            |
| :--------------------- | :----------------------------------- |
| Service d'objets blob           | `privatelink.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `privatelink.dfs.core.windows.net`   |
| Service Fichier           | `privatelink.file.core.windows.net`  |
| Service File d’attente          | `privatelink.queue.core.windows.net` |
| Service Table          | `privatelink.table.core.windows.net` |
| Sites web statiques        | `privatelink.web.core.windows.net`   |

#### <a name="resources"></a>Ressources

Pour plus d’informations sur la configuration de votre propre serveur DNS pour la prise en charge des points de terminaison privés, reportez-vous aux articles suivants :

- [Résolution de noms pour des ressources dans les réseaux virtuels Azure](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [Configuration DNS pour les points de terminaison privés](/azure/private-link/private-endpoint-overview#dns-configuration)

## <a name="pricing"></a>Tarifs

Pour plus d’informations sur les tarifs, consultez [Tarification Liaison privée Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Problèmes connus

### <a name="copy-blob-support"></a>Prise en charge de la copie Blob

Pendant la préversion, nous ne prenons pas en charge les commandes [Copier Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) émises sur les comptes de stockage accessibles via des points de terminaison privés lorsque le compte de stockage source est protégé par un pare-feu.

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Contraintes d’accès au stockage pour les clients dans des réseaux virtuels avec des points de terminaison privés

Les clients dans réseaux virtuels avec des points de terminaison privés existants sont soumis à des contraintes lors de l’accès à d’autres comptes de stockage qui ont des points de terminaison privés. Par exemple, supposons qu’un réseau virtuel N1 possède un point de terminaison privé pour un compte de stockage A1 pour, par exemple, le service Blob. Si le compte de stockage A2 possède un point de terminaison privé dans un réseau virtuel N2 pour le service Blob, les clients dans le réseau virtuel N1 doivent également accéder au service Blob du compte A2 à l’aide d’un point de terminaison privé. Si le compte de stockage A2 ne possède pas de points de terminaison privés pour le service Blob, les clients dans le réseau virtuel N1 peuvent accéder à son service Blob sans point de terminaison privé.

Cette contrainte résulte des modifications DNS effectuées lorsque le compte A2 crée un point de terminaison privé.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>Règles de groupe de sécurité réseau pour les sous-réseaux avec des points de terminaison privés

Actuellement, vous ne pouvez pas configurer de règles de [groupe de sécurité réseau](../../virtual-network/security-overview.md) pour les sous-réseaux avec des points de terminaison privés. Une solution de contournement limitée pour ce problème consiste à implémenter vos règles d’accès pour les points de terminaison privés sur les sous-réseaux sources, bien que cette approche puisse nécessiter une charge de gestion supérieure.
