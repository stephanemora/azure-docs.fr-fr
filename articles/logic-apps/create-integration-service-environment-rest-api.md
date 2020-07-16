---
title: Créer des environnements de service d'intégration (ISE) à l'aide de l'API REST Logic Apps
description: Créez un environnement de service d'intégration (ISE) à l'aide de l'API REST Logic Apps afin de pouvoir accéder aux réseaux virtuels Azure à partir d'Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: rarayudu, logicappspm
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: d33207639ebef912307a3c594ec274fd9609bd67
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84656540"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Créer un environnement de service d'intégration (ISE) à l'aide de l'API REST Logic Apps

Cet article explique comment créer un [*environnement de service d'intégration* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) à l'aide de l'API REST Logic Apps pour les scénarios dans lesquels vos applications logiques et vos comptes d'intégration doivent accéder à un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md). Un ISE est un environnement dédié qui utilise un stockage dédié et d’autres ressources qui sont conservées séparément du service Logic Apps multilocataire « mondial ». Cette séparation réduit également l’impact que d’autres locataires Azure peuvent avoir sur les performances de vos applications. Un environnement ISE vous fournit également vos propres adresses IP statiques. Ces adresses IP sont séparées des adresses IP statiques qui sont partagées par les applications logiques dans le service multilocataire public.

Vous pouvez également créer un environnement ISE à l’aide de l’[exemple de modèle de démarrage rapide Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) ou à l’aide du [portail Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

> [!IMPORTANT]
> Les applications logiques, les déclencheurs et actions intégrés et les connecteurs qui s’exécutent dans votre ISE utilisent un autre plan de tarification que celui basé sur la consommation. Pour plus d’informations sur la tarification et la facturation des environnements de service d’intégration, consultez le [modèle de tarif pour Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Pour connaître la tarification, consultez [Tarification Logic Apps](../logic-apps/logic-apps-pricing.md).

## <a name="prerequisites"></a>Prérequis

* Les [conditions préalables](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) et la [configuration requise pour activer l'accès à votre environnement ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) sont les mêmes que lorsque vous créez un environnement ISE à partir du portail Azure.

* Un outil que vous pouvez utiliser pour créer votre ISE en appelant l’API REST Logic Apps avec une requête PUT HTTPS. Par exemple, vous pouvez utiliser [Postman](https://www.getpostman.com/downloads/) ou créer une application logique qui effectue cette tâche

## <a name="send-the-request"></a>Envoyer la demande

Pour créer votre ISE en appelant l’API REST Logic Apps, émettez cette requête PUT HTTPS :

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Avec l’API REST version 2019-05-01 de Logic Apps, vous devez émettre votre propre requête PUT HTTP pour les connecteurs ISE.

Le déploiement prend généralement deux heures maximum. Parfois, le déploiement peut prendre jusqu’à quatre heures. Pour suivre l'état du déploiement, accédez au [portail Azure](https://portal.azure.com) et sélectionnez l'icône Notifications sur la barre d'outils Azure. Le volet Notifications s'ouvre alors.

> [!NOTE]
> Si le déploiement échoue ou si vous supprimez votre ISE, Azure peut prendre jusqu’à une heure avant de libérer vos sous-réseaux. Ce délai signifie que vous devrez peut-être attendre avant de réutiliser ces sous-réseaux dans un autre ISE.
>
> Si vous supprimez votre réseau virtuel, Azure a généralement besoin de jusqu'à deux heures pour libérer vos sous-réseaux, mais cette opération peut prendre plus longtemps. 
> Lors de la suppression de réseaux virtuels, assurez-vous qu’aucune ressource n’est restée connectée. 
> Consultez [Supprimer un réseau virtuel](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

## <a name="request-header"></a>En-tête de requête

Dans l’en-tête de la requête, incluez les propriétés suivantes :

* `Content-type`: affectez la valeur `application/json` à cette propriété.

* `Authorization`: affectez comme valeur de cette propriété le jeton du porteur pour le client qui a accès à l’abonnement Azure ou au groupe de ressources que vous souhaitez utiliser

<a name="request-body"></a>

## <a name="request-body"></a>Corps de la demande

Voici la syntaxe du corps de la requête, qui décrit les propriétés à utiliser lors de la création de votre ISE. Pour créer un environnement ISE qui autorise l’utilisation d’un certificat auto-signé installé à l’emplacement `TrustedRoot`, incluez l’objet `certificates` à l’intérieur de la section `properties` de la définition ISE. Pour une application ISE existante, vous pouvez envoyer une requête PATCH pour l’objet `certificates` seulement. Pour plus d’informations sur l’utilisation des certificats auto-signés, voir aussi [Connecteur HTTP - Certificats auto-signés](../connectors/connectors-native-http.md#self-signed).

```json
{
   "id": "/subscriptions/{Azure-subscription-ID/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      },
      // Include `certificates` object to enable self-signed certificate support
      "certificates": {
         "testCertificate": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

### <a name="request-body-example"></a>Exemple de corps de requête

Cet exemple de corps de requête montre les exemples de valeurs :

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      },
      "certificates": {
         "testCertificate": {
            "publicCertificate": "LS0tLS1CRUdJTiBDRV...",
            "kind": "TrustedRoot"
         }
   }
}
```

## <a name="next-steps"></a>Étapes suivantes

* [Ajouter des ressources à des environnements de service d'intégration](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Gérer les environnements de service d’intégration](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)

