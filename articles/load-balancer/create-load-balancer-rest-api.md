---
title: Créer un équilibreur de charge à l’aide de l’API REST
titleSuffix: Azure Load Balancer
description: Dans cet article, découvrez comment créer un équilibreur de charge Azure à l’aide de l’API REST.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: load-balancer
ms.date: 06/06/2018
ms.author: allensu
ms.openlocfilehash: b52c554617bdcbe88b65639473044eb9c5eb7fa8
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045429"
---
# <a name="create-an-azure-basic-load-balancer-using-rest-api"></a>Créer un équilibreur de charge Azure de base à l’aide de l’API REST

Un équilibreur de charge Azure distribue les nouveaux flux entrants qui arrivent sur son frontend vers des instances de pool backend en fonction de règles et de sondes d’intégrité. L’équilibreur de charge se décline en deux références SKU : De base et Standard. Pour comprendre la différence entre les deux versions (SKU), voir [Comparaisons des SKU d’équilibreur de charge](concepts-limitations.md#skus).
 
Cette procédure montre comment créer un équilibreur de charge Azure de base à l’aide l’[API REST Azure](/rest/api/azure/) pour contribuer à équilibrer la charge d’une requête entrante sur plusieurs machines virtuelles d’un réseau virtuel Azure. Une documentation de référence complète et d’autres exemples sont disponibles dans la [Référence REST des équilibreurs de charge Azure](/rest/api/load-balancer/).
 
## <a name="build-the-request"></a>Générer la demande
Utilisez la requête PUT HTTP suivante pour créer un équilibreur de charge Azure de base.
 ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}?api-version=2018-02-01
  ```
### <a name="uri-parameters"></a>Paramètres URI

|Name  |Dans  |Obligatoire |Type |Description |
|---------|---------|---------|---------|--------|
|subscriptionId   |  path       |  True       |   string      |  Les informations d’identification d’abonnement qui identifient de façon unique l’abonnement Microsoft Azure. L’ID d’abonnement fait partie de l’URI pour chaque appel de service.      |
|resourceGroupName     |     path    | True        |  string       |   Nom du groupe de ressources.     |
|loadBalancerName     |  path       |      True   |    string     |    Le nom de l’équilibreur de charge.    |
|api-version    |   query     |  True       |     string    |  Version d’API client.      |



### <a name="request-body"></a>Corps de la demande

Le seul paramètre requis est `location`. Si vous ne définissez pas la *SKU*, un équilibreur de charge est créé par défaut.  Utilisez [paramètres facultatifs](https://docs.microsoft.com/rest/api/load-balancer/loadbalancers/createorupdate#request-body) pour personnaliser l’équilibreur de charge.

| Name | Type | Description |
| :--- | :--- | :---------- |
| location | string | Emplacement de la ressource. Obtenir une liste actuelle des emplacements à l’aide de l’opération [Lister les emplacements](https://docs.microsoft.com/rest/api/resources/subscriptions/listlocations). |


## <a name="example-create-and-update-a-basic-load-balancer"></a>Exemple : créer et mettre à jour d’un équilibreur de charge de base

Dans cet exemple, vous commencez par créer un équilibreur de charge ainsi que ses ressources. Ensuite, vous configurez les ressources de l’équilibreur de charge qui incluent une configuration d’adresses IP frontales, un pool d’adresses principales, une règle d’équilibrage de charge, une sonde d’intégrité et une règle NAT entrante.

Avant de créer un équilibreur de charge à l’aide de l’exemple ci-dessous, créez un réseau virtuel nommé *vnetlb* avec un sous-réseau nommé *subnetlb* dans un groupe de ressources nommé *rg1* à l’emplacement **USA Est**.

### <a name="step-1-create-a-basic-load-balancer"></a>ÉTAPE 1. Créer un équilibreur de charge de base public
Dans cette étape, vous créez un équilibreur de charge de base, nommée *lb* à l’emplacement **USA Est** dans le groupe de ressources *rg1*.
#### <a name="sample-request"></a>Exemple de requête

  ```HTTP    
  PUT https://management.azure.com/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb?api-version=2018-02-01
  ```
#### <a name="request-body"></a>Corps de la demande

  ```JSON
   {
    "location": "eastus",
   }
  ```
### <a name="step-2-configure-load-balancer-resources"></a>ÉTAPE 2. Configurer les ressources de l’équilibreur de charge
Dans cette étape, vous configurez les ressources de l’équilibreur de charge *lb* qui incluent une configuration d’adresses IP frontales (*fe-lb*), un pool d’adresses principales (*be-lb*), une règle d’équilibrage de charge (*rulelb*), une sonde d’intégrité (*probe-lb*) et une règle NAT entrante (*in-nat-rule*).
#### <a name="sample-request"></a>Exemple de requête

  ```HTTP    
  PUT https://management.azure.com/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb?api-version=2018-02-01
  ```
#### <a name="request-body"></a>Corps de la demande

  ```JSON
{
  "properties": {
    "frontendIPConfigurations": [
      {
        "name": "fe-lb",
        "properties": {
          "subnet": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/virtualNetworks/vnetlb/subnets/subnetlb"
          },
          "loadBalancingRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/loadBalancingRules/rulelb"
            }  ],
          "inboundNatRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/inboundNatRules/in-nat-rule"
            }  ]  }  }  ],
    "backendAddressPools": [
      {
        "name": "be-lb",
        "properties": {
          "loadBalancingRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/loadBalancingRules/rulelb"
            }  ]   }   }   ],
    "loadBalancingRules": [
      {
        "name": "rulelb",
        "properties": {
          "frontendIPConfiguration": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/frontendIPConfigurations/fe-lb"
          },
          "frontendPort": 80,
          "backendPort": 80,
          "enableFloatingIP": true,
          "idleTimeoutInMinutes": 15,
          "protocol": "Tcp",
          "loadDistribution": "Default",
          "backendAddressPool": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/backendAddressPools/be-lb"
          },
          "probe": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/probes/probe-lb"
          }  }  }  ],
    "probes": [
      {
        "name": "probe-lb",
        "properties": {
          "protocol": "Http",
          "port": 80,
          "requestPath": "healthcheck.aspx",
          "intervalInSeconds": 15,
          "numberOfProbes": 2,
          "loadBalancingRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/loadBalancingRules/rulelb"
            }  ]  }  } ],
    "inboundNatRules": [
      {
        "name": "in-nat-rule",
        "properties": {
          "frontendIPConfiguration": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/frontendIPConfigurations/fe-lb"
          },
          "frontendPort": 3389,
          "backendPort": 3389,
          "enableFloatingIP": true,
          "idleTimeoutInMinutes": 15,
          "protocol": "Tcp"
        } } ],
    "inboundNatPools": [],
    "outboundNatRules": []
  }  }
```
