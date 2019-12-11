---
title: Applications managées Azure avec notifications
description: Configurez l’application managée avec des points de terminaison webhook pour recevoir des notifications concernant les créations, les mises à jour, les suppressions et les erreurs sur les instances de l’application managée.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.reviewer: ''
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: 8cf9fc0b3d9c13ebc5309be6d27c7be0f2e60878
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805686"
---
# <a name="azure-managed-applications-with-notifications"></a>Applications managées Azure avec notifications

Les notifications d’applications managées Azure permettent aux éditeurs d’automatiser des actions en fonction des événements du cycle de vie des instances de l’application managée. Les éditeurs peuvent spécifier des points de terminaison webhook de notification personnalisés pour recevoir des notifications d’événements sur les instances nouvelles et existantes de l’application managée. Cela permet à l’éditeur de configurer des workflows personnalisés au moment du provisionnement, des mises à jour et de la suppression de l’application.

## <a name="getting-started"></a>Prise en main
Pour commencer à recevoir des applications managées, lancez un point de terminaison HTTPS public et spécifiez-le lors de la publication de la définition de l’application du Catalogue de services ou de l’offre de la Place de marché.

Voici la série d’étapes recommandée pour une mise en route rapide :
1. Lancez un point de terminaison HTTPS public qui journalise les requêtes POST entrantes et retourne `200 OK`.
2. Ajoutez le point de terminaison à la définition de l’application du catalogue de services ou à l’offre de la Place de marché, comme expliqué ci-dessous.
3. Créez une instance d’application managée qui fait référence à la définition de l’application ou à l’offre de la Place de marché.
4. Vérifiez que les notifications sont bien reçues.
5. Activez l’autorisation, comme expliqué dans la section **Authentification du point de terminaison** ci-dessous.
6. Suivez la documentation **Schéma de notification** ci-dessous pour analyser les demandes de notification et implémenter votre logique métier basée sur la notification.

## <a name="adding-service-catalog-application-definition-notifications"></a>Ajout de notifications de définition de l’application du catalogue de services
#### <a name="azure-portal"></a>Portail Azure
Lisez [Publier une application du catalogue de services en utilisant le portail Azure](./publish-portal.md) pour commencer.

![Notifications de définition de l’application du catalogue de services sur le portail](./media/publish-notifications/service-catalog-notifications.png)
#### <a name="rest-api"></a>API REST

> [!NOTE]
> Actuellement, un seul point de terminaison est pris en charge dans le cadre de **notificationEndpoints** dans les propriétés de définition de l’application

``` JSON
    {
      "properties": {
        "isEnabled": true,
        "lockLevel": "ReadOnly",
        "displayName": "Sample Application Definition",
        "description": "Notification-enabled application definition.",
        "notificationPolicy": {
            "notificationEndpoints": [
                {
                    "uri": "https://isv.azurewebsites.net:1214?sig=unique_token"
                }
            ]
        },
        "authorizations": [
          {
            "principalId": "d6b7fbd3-4d99-43fe-8a7a-f13aef11dc18",
            "roleDefinitionId": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635"
          },
        ...

```
## <a name="adding-marketplace-managed-application-notifications"></a>Ajout de notifications d’application managée de la Place de marché
Pour plus d’informations, consultez [Créer une offre d’application Azure](../marketplace/cloud-partner-portal/azure-applications/cpp-create-offer.md).

![Notifications de définition de l’application du catalogue de services sur le portail](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>Déclencheurs d’événement
Le tableau suivant décrit toutes les combinaisons possibles de EventType + ProvisioningState et leurs déclencheurs :

Type d’événement | ProvisioningState | Déclencheur pour la notification
---|---|---
PUT | Acceptée | Le groupe de ressources managé a été créé et projeté correctement après une opération PUT de l’application. (Avant le lancement du déploiement dans le RG managé.)
PUT | Succeeded | Le provisionnement complet de l’application managée a réussi après une opération PUT.
PUT | Échec | Échec de l’opération PUT de provisionnement d’une instance d’application à tout moment.
PATCH | Succeeded | Après une opération PATCH réussie sur une instance d’application managée pour mettre à jour les balises, la stratégie d’accès JIT ou l’identité managée.
SUPPRIMER | En cours de suppression | Dès que l’utilisateur lance une opération DELETE d’une instance d’application managée.
SUPPRIMER | Deleted | Après la suppression complète et réussie de l’application managée.
SUPPRIMER | Échec | Après une erreur pendant le processus de déprovisionnement qui bloque la suppression.
## <a name="notification-schema"></a>Schéma de notification
Lorsque vous lancez votre point de terminaison de webhook pour gérer les notifications, vous devez analyser la charge utile pour obtenir des propriétés importantes afin de pouvoir agir sur la notification. Les notifications d’application managée de type Catalogue de services et Place de marché fournissent un grand nombre des mêmes propriétés avec la différence moindre décrite ci-dessous.

#### <a name="service-catalog-application-notification-schema"></a>Schéma de notification de l’application du catalogue de services
Voici un exemple de notification du catalogue de services après le provisionnement réussi d’une instance d’application managée.
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>"    
}

```

Si le provisionnement échoue, une notification avec les détails de l’erreur est envoyée au point de terminaison spécifié.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>",
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

#### <a name="marketplace-application-notification-schema"></a>Schéma de notification de l’application de la Place de marché

Voici un exemple de notification du catalogue de services après le provisionnement réussi d’une instance d’application managée.
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    }
}

```

Si le provisionnement échoue, une notification avec les détails de l’erreur est envoyée au point de terminaison spécifié.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    },
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

Paramètre | Description
---|---
eventType | Type d’événement ayant déclenché la notification. (Par exemple, « PUT », « PATCH », « DELETE »)
applicationId | Identificateur de ressource complet de l’application managée pour laquelle la notification a été déclenchée. 
eventTime | Horodatage de l’événement ayant déclenché la notification. (Date et heure au format UTC ISO 8601.)
provisioningState | État de provisionnement de l’instance d’application managée. (Par exemple, « Succeeded », « Failed », « Deleting », « Deleted »)
billingDetails | Détails de facturation de l’instance d’application managée. Contient le resourceUsageId qui peut être utilisé pour interroger la Place de marché afin d’obtenir des détails sur l’utilisation.
error | *Spécifié uniquement lorsque provisioningState est égal à Failed*. Contient le code d’erreur, le message et les détails relatifs au problème à l’origine de l’échec.
applicationDefinitionId | *Spécifié uniquement pour les applications managées du catalogue de services*. Représente l’identificateur de ressource complet de la définition d’application pour laquelle l’instance d’application managée a été provisionnée.
Plan | *Spécifié uniquement pour les applications managées de la Place de marché*. Représente l’éditeur, l’offre, la référence (SKU) et la version de l’instance de l’application managée.

## <a name="endpoint-authentication"></a>Authentification du point de terminaison
Pour sécuriser le point de terminaison webhook et garantir l’authenticité de la notification :
- Fournissez un paramètre de requête au-dessus de l’URI webhook, comme https://your-endpoint.com?sig=Guid. Avec chaque notification, vérifiez rapidement que le paramètre de requête `sig` a la valeur attendue `Guid`.
- Émettez une opération GET sur l’instance d’application managée avec applicationId. Vérifiez que provisioningState correspond au provisioningState de la notification pour garantir la cohérence.

## <a name="notification-retries"></a>Nouvelles tentatives de notification

Le service de notification d’application managée attend une réponse `200 OK` du point de terminaison webhook à la notification. Le service de notification effectue une nouvelle tentative si le point de terminaison webhook retourne un code d’erreur HTTP >=500, 429 ou si le point de terminaison est temporairement inaccessible. Si le point de terminaison webhook n’est pas disponible dans un délai de 10 heures, le message de notification est abandonné et les nouvelles tentatives s’arrêtent.

