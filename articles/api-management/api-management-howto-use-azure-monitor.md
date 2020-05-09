---
title: Surveiller les API publiées dans la Gestion des API Azure | Microsoft Docs
description: Suivez les étapes de ce didacticiel pour apprendre à surveiller votre API dans la Gestion des API.
services: api-management
author: vladvino
manager: cfowler
ms.service: api-management
ms.workload: mobile
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: bee93cf84f4beda0684127102942447630219881
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82128840"
---
# <a name="monitor-published-apis"></a>Surveiller les API publiées

Avec Azure Monitor, vous pouvez visualiser, interroger, acheminer, archiver et agir sur les métriques ou les journaux d’activité provenant de vos ressources Azure.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Afficher les journaux d’activité
> * Afficher les journaux de ressources
> * Afficher les métriques de votre API 
> * Configurer une règle d’alerte quand votre API obtient des appels non autorisés

La vidéo suivante montre comment surveiller la gestion des API à l’aide d’Azure Monitor. 

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]

## <a name="prerequisites"></a>Prérequis

+ Apprenez la [terminologie relative à Gestion des API Azure](api-management-terminology.md).
+ Suivez ce guide de démarrage rapide : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).
+ Effectuez également toutes les étapes du tutoriel suivant : [Importer et publier votre première API](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="view-metrics-of-your-apis"></a>Afficher les métriques de vos API

Le service Gestion des API émet des métriques chaque minute, pour une visibilité en quasi temps réel de l’intégrité de vos API. Voici un récapitulatif d’une partie des métriques disponibles :

* Fonctionnalité : vous aide à prendre des décisions concernant la mise à niveau/le passage à une version antérieure de vos services APIM. La métrique est émise chaque minute et reflète la capacité de la passerelle au moment de la création de rapports. Le calcul de cette métrique, dont la valeur est comprise entre 0 et 100, repose sur les ressources de la passerelle, telles que l’utilisation du processeur et de la mémoire.
* Nombre total de demandes de passerelle : le nombre de requêtes d’API dans la période. 
* Demandes de la passerelle ayant abouti : le nombre de requêtes d’API ayant reçu des codes de réponse HTTP de succès, dont 304, 307 et toute valeur inférieure à 301 (par exemple, 200).
* Demandes de la passerelle ayant échoué : le nombre de requêtes d’API ayant reçu des codes de réponse HTTP d’échec, dont 400 et toute valeur supérieure à 500.
* Demandes de la passerelle non autorisées : le nombre de requêtes d’API ayant reçu des codes de réponse HTTP comprenant 401, 403 et 429.
* Autres demandes de la passerelle : le nombre de requêtes d’API ayant reçu des codes de réponse qui n’appartiennent à aucune des catégories précédentes (par exemple, 418).

![graphique des métriques](./media/api-management-azure-monitor/apim-monitor-metrics.png)

Pour accéder aux métriques :

1. Sélectionnez **Métriques** dans le menu vers le bas de la page.

    ![Mesures](./media/api-management-azure-monitor/api-management-metrics-blade.png)

1. Dans la liste déroulante, sélectionner les métriques qui vous intéressent. Par exemple, **Demandes**. 
1. Le graphique affiche le nombre total d’appels d’API.
1. Le graphique peut être filtré à l’aide des dimensions de la métrique **Requêtes**. Par exemple, cliquez sur **Ajouter un filtre**, choisissez **Code de réponse de back-end**, entrez 500 comme valeur. À présent, le graphique montre le nombre de requêtes qui ont échoué dans le back-end d’API.   

## <a name="set-up-an-alert-rule-for-unauthorized-request"></a>Configurer une règle d’alerte pour une demande non autorisée

Vous pouvez configurer les paramètres pour recevoir des alertes en fonction des mesures et des journaux d’activité. Azure Monitor vous permet de configurer une alerte pour effectuer les opérations suivantes lors de son déclenchement :

* Envoyer un e-mail de notification
* Appeler un webhook
* Appeler une application logique Azure

Pour configurer des alertes :

1. Sélectionnez **Alertes** dans la barre de menus vers le bas de la page.

    ![alertes](./media/api-management-azure-monitor/alert-menu-item.png)

2. Cliquez sur **Nouvelle règle d’alerte** pour cette alerte.
3. Cliquez sur **Ajouter une condition**.
4. Sélectionnez **Métriques** dans la liste déroulante Type de signal.
5. Sélectionnez **Demande de passerelle non autorisée** comme signal à superviser.

    ![alertes](./media/api-management-azure-monitor/signal-type.png)

6. Dans la vue **Configurer la logique du signal**, spécifiez un seuil après lequel l’alerte doit être déclenchée et cliquez sur **Terminé**.

    ![alertes](./media/api-management-azure-monitor/threshold.png)

7. Sélectionnez un groupe d’actions existant ou créez-en un. Dans l’exemple ci-dessous, un e-mail est envoyé aux administrateurs. 

    ![alertes](./media/api-management-azure-monitor/action-details.png)

8. Entrez un nom et une description de la règle d’alerte, et choisissez le niveau de gravité. 
9. Appuyez sur **Créer une règle d’alerte**.
10. À présent, essayez d’appeler l’API de conférence sans clé API. L’alerte est déclenchée et un e-mail est envoyé aux administrateurs. 

## <a name="activity-logs"></a>Journaux d’activité

Les journaux d’activité fournissent des informations sur les opérations qui ont été effectuées sur les services de Gestion des API. Avec les journaux d’activité, vous pouvez déterminer « qui, quand et quoi » pour toutes les opérations d’écriture (PUT, POST, DELETE) sur vos services de Gestion des API.

> [!NOTE]
> Les journaux d’activité n’incluent pas les opérations de lecture (GET) ou les opérations effectuées dans le portail Azure ou utilisant les API de gestion d’origine.

Vous pouvez accéder aux journaux d’activité dans votre service de Gestion des API, ou accéder aux journaux d’activité de toutes vos ressources Azure dans Azure Monitor. 

![journaux d’activité](./media/api-management-azure-monitor/apim-monitor-activity-logs.png)

Pour afficher les journaux d’activité :

1. Sélectionnez votre instance de service APIM.
2. Cliquez sur **Journal d’activité**.

    ![journal d’activité](./media/api-management-azure-monitor/api-management-activity-logs-blade.png)

3. Sélectionnez l’étendue de filtrage souhaitée, puis cliquez sur **Appliquer**.

## <a name="resource-logs"></a>Journaux de ressources

Les journaux de ressources offrent des informations détaillées sur les opérations et erreurs qui sont importantes pour l’audit et dans le cadre de dépannages. Les journaux de ressources diffèrent des journaux d’activité. Les journaux d’activité fournissent des informations sur les opérations qui ont été effectuées sur vos ressources Azure. Les journaux de ressources fournissent des informations détaillées sur les opérations effectuées par votre ressource.

Pour configurer les journaux de ressources :

1. Sélectionnez votre instance de service APIM.
2. Cliquez sur **Paramètres de diagnostic**.

    ![journaux de ressources](./media/api-management-azure-monitor/api-management-diagnostic-logs-blade.png)

3. Cliquez sur **Activer les diagnostics**. Vous pouvez archiver les journaux de ressources avec les métriques dans un compte de stockage, les envoyer en streaming à un hub d’événement ou les envoyer aux journaux Azure Monitor. 

Le service Gestion des API fournit actuellement des journaux de ressources (par lot toutes les heures) pour chaque requête d’API, chaque entrée ayant le schéma suivant :

```json
{  
    "isRequestSuccess" : "",
    "time": "",
    "operationName": "",
    "category": "",
    "durationMs": ,
    "callerIpAddress": "",
    "correlationId": "",
    "location": "",
    "httpStatusCodeCategory": "",
    "resourceId": "",
    "properties": {   
        "method": "", 
        "url": "", 
        "clientProtocol": "", 
        "responseCode": , 
        "backendMethod": "", 
        "backendUrl": "", 
        "backendResponseCode": ,
        "backendProtocol": "",  
        "requestSize": , 
        "responseSize": , 
        "cache": "", 
        "cacheTime": "", 
        "backendTime": , 
        "clientTime": , 
        "apiId": "",
        "operationId": "", 
        "productId": "", 
        "userId": "", 
        "apimSubscriptionId": "", 
        "backendId": "",
        "lastError": { 
            "elapsed" : "", 
            "source" : "", 
            "scope" : "", 
            "section" : "" ,
            "reason" : "", 
            "message" : ""
        } 
    }      
}  
```

| Propriété  | Type | Description |
| ------------- | ------------- | ------------- |
| isRequestSuccess | boolean | True si la requête HTTP a échoué avec le code d’état de réponse dans la plage 2xx ou 3xx |
| time | date-time | Horodatage du début du traitement de la requête par la passerelle |
| operationName | string | Valeur constante « Microsoft.ApiManagement/GatewayLogs » |
| catégorie | string | Valeur constante « GatewayLogs » |
| durationMS | entier | Nombre de millisecondes entre le moment où la passerelle a reçu la requête et celui où la réponse complète a été envoyée. Elle comprend clienTime, cacheTime et backendTime. |
| callerIpAddress | string | Adresse IP de l’appelant de passerelle immédiat (peut être un intermédiaire) |
| correlationId | string | Identificateur de requête http unique assigné par le service Gestion des API |
| location | string | Nom de la région Azure dans laquelle se trouvait la passerelle qui a traité la requête |
| httpStatusCodeCategory | string | Catégorie du code d’état de réponse http : réussite (inférieur ou égal à 301 ou 304 ou 307), non autorisé (401, 403, 429), erroné (400, entre 500 et 600), autre |
| resourceId | string | ID de la ressource du service Gestion des API /SUBSCRIPTIONS/\<subscription>/RESOURCEGROUPS/\<resource-group>/PROVIDERS/MICROSOFT.APIMANAGEMENT/SERVICE/\<name> |
| properties | object | Propriétés de la requête actuelle |
| method | string | Méthode HTTP de la requête entrante |
| url | string | URL de la requête entrante |
| clientProtocol | string | Version du protocole HTTP de la requête entrante |
| responseCode | entier | Code d’état de la réponse HTTP envoyée à un client |
| backendMethod | string | Méthode HTTP de la requête envoyée à un serveur principal |
| backendUrl | string | URL de la requête envoyée à un serveur principal |
| backendResponseCode | entier | Code de la réponse HTTP reçue d’un serveur principal |
| backendProtocol | string | Version du protocole HTTP de la requête envoyée à un service principal | 
| requestSize | entier | Nombre d’octets reçus d’un client au cours du traitement de la requête | 
| responseSize | entier | Nombre d’octets envoyés à un client au cours du traitement de la requête | 
| cache | string | État d’implication du cache du service Gestion des API dans le traitement des requêtes (par exemple, atteint, manqué, aucun) | 
| cacheTime | entier | Nombre de millisecondes consacrées à l’ensemble des E/S du cache du service Gestion des API (connexion, envoi et réception d’octets) | 
| backendTime | entier | Nombre de millisecondes consacrées à l’ensemble des E/S du serveur principal (connexion, envoi et réception d’octets) | 
| clientTime | entier | Nombre de millisecondes consacrées à l’ensemble des E/S du client (connexion, envoi et réception d’octets) | 
| apiId | string | Identificateur d’entité d’API pour la requête actuelle | 
| operationId | string | Identificateur d’entité d’opération pour la requête actuelle | 
| productId | string | Identificateur d’entité de produit pour la requête actuelle | 
| userId | string | Identificateur d’entité d’utilisateur pour la requête actuelle | 
| apimSubscriptionId | string | Identificateur d’entité d’abonnement pour la requête actuelle | 
| backendId | string | Identificateur d’entité de serveur principal pour la requête actuelle | 
| lastError | object | Dernière erreur de traitement de requête | 
| elapsed | entier | Nombre de millisecondes écoulées entre le moment où la passerelle a reçu la requête et celui où l’erreur s’est produite | 
| source | string | Nom du gestionnaire interne de traitement ou de stratégie qui a provoqué l’erreur | 
| scope | string | Étendue du document de stratégie qui contient la stratégie qui a provoqué l’erreur | 
| section | string | Section du document de stratégie qui contient la stratégie qui a provoqué l’erreur | 
| reason | string | Motif de l’erreur | 
| message | string | Message d’erreur | 

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Afficher les journaux d’activité
> * Afficher les journaux de ressources
> * Afficher les métriques de votre API
> * Configurer une règle d’alerte quand votre API obtient des appels non autorisés

Passez au tutoriel suivant :

> [!div class="nextstepaction"]
> [Suivre des appels](api-management-howto-api-inspector.md)
