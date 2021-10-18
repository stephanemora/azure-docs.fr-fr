---
title: Adresses IP du service Gestion des API Azure | Microsoft Docs
description: Apprenez à récupérer les adresses IP d'un service Gestion des API Azure et leurs modifications.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 04/13/2021
ms.author: apimpm
ms.custom: fasttrack-edit
ms.openlocfilehash: fe5f282150aae2103d20963416f390bf159c48ea
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129856882"
---
# <a name="ip-addresses-of-azure-api-management"></a>Adresses IP du service Gestion des API Azure

Cet article explique comment récupérer les adresses IP d'un service Gestion des API Azure. Les adresses IP peuvent être publiques ou privées si le service se trouve dans un réseau virtuel.

Vous pouvez utiliser des adresses IP pour créer des règles de pare-feu, filtrer le trafic entrant à destination des services principaux, ou limiter le trafic sortant.

## <a name="ip-addresses-of-api-management-service"></a>Adresses IP du service Gestion des API

Chaque instance du service Gestion des API dans les niveaux Développeur, De base, Standard ou Premium a des adresses IP publiques propres cette instance de service (elles ne sont pas partagées avec d’autres ressources). 

Vous pouvez récupérer les adresses IP à partir du tableau de bord de vue d’ensemble de votre ressource dans le portail Azure.

![Adresse IP du service Gestion des API](media/api-management-howto-ip-addresses/public-ip.png)

Vous pouvez également les récupérer par programmation à l'aide de l'appel d'API suivant :

```
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>
```

Les adresses IP publiques seront contenues dans la réponse :

```json
{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.77.143.53"
    ],
    ...
  }
  ...
}
```

Dans les [déploiements multirégionaux](api-management-howto-deploy-multi-region.md), chaque déploiement régional possède une adresse IP publique.

## <a name="ip-addresses-of-api-management-service-in-vnet"></a>Adresses IP du service Gestion des API dans un réseau virtuel

Si votre service Gestion des API se trouve dans un réseau virtuel, il dispose de deux types d'adresses IP : publiques et privées.

Les adresses IP publiques sont utilisées pour les communications internes sur le port `3443` - afin de gérer la configuration (par exemple, via Azure Resource Manager). Dans la configuration de réseau virtuel externe, elles sont également utilisées pour le trafic des API runtime. 

Les adresses IP virtuelles privées, disponibles **uniquement** dans le [mode réseau virtuel interne](api-management-using-with-internal-vnet.md), sont utilisées pour se connecter aux points de terminaison (passerelles, portail des développeurs et plan de gestion pour un accès direct à l’API) du service Gestion des API depuis le réseau. Vous pouvez les utiliser pour configurer des enregistrements DNS au sein du réseau.

Vous verrez des adresses des deux types sur le portail Azure et dans la réponse de l'appel d'API :

![Gestion des API dans une adresse IP de réseau virtuel](media/api-management-howto-ip-addresses/vnet-ip.png)


```json
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>

{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.85.20.170"
    ],
    "privateIPAddresses": [
      "192.168.1.5"
    ],
    ...
  },
  ...
}
```

La gestion des API utilise une adresse IP publique pour les connexions extérieures au réseau virtuel et une adresse IP privée pour les connexions au sein du réseau virtuel. 

Quand la gestion des API est déployée dans la [configuration VNet interne](api-management-using-with-internal-vnet.md) et qu’elle se connecte à des back-ends privés (sur l’intranet), les adresses IP internes du sous-réseau sont utilisées pour le trafic d’API du runtime. Quand une demande est envoyée par la gestion des API à un back-end privé, une adresse IP publique est montrée comme origine de la demande. Par conséquent, dans cette configuration, s’il y a une exigence concernant la limitation du trafic entre la gestion des API et un back-end interne, il vaut mieux utiliser l’ensemble des préfixes de sous-réseau de gestion des API avec une règle IP et pas uniquement l’adresse IP privée associée à la ressource de gestion des API. 

Quand une demande est envoyée par la gestion des API à un back-end public (sur Internet), une adresse IP publique est toujours montrée comme origine de la demande.

## <a name="ip-addresses-of-consumption-tier-api-management-service"></a>Adresses IP d'un service Gestion des API de niveau Consommation

Si votre service Gestion des API est un service de niveau Consommation, il ne possède pas d'adresse IP dédiée. Le service de niveau Consommation est exécuté sur une infrastructure partagée et sans adresse IP déterministe. 

Pour limiter le trafic, vous pouvez utiliser la plage d'adresses IP des centres de données Azure. Pour connaître la procédure à suivre, reportez-vous à l'[article de la documentation consacré à Azure Functions](../azure-functions/ip-addresses.md#data-center-outbound-ip-addresses).

## <a name="changes-to-the-ip-addresses"></a>Modification des adresses IP

Aux niveaux Développeur, De base, Standard et Premium d'un service Gestion des API, les adresses IP publiques sont statiques pendant toute la durée de vie du service, avec les exceptions suivantes :

* Le service est supprimé, puis recréé.
* L’abonnement au service est [suspendu](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) ou [fait l’objet d’un avertissement ](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states)(par exemple pour non-paiement), puis réactivé.
* Le Réseau virtuel Azure est ajouté au service ou supprimé de celui-ci.
* Le service Gestion des API bascule entre les modes de déploiement de réseau virtuel Externe et Interne.

Dans les [déploiements multirégionaux](api-management-howto-deploy-multi-region.md), l'adresse IP régionale change si une région est libérée, puis rétablie. L’adresse IP régionale change également lorsque vous activez, ajoutez ou supprimez des [zones de disponibilité](zone-redundancy.md).
