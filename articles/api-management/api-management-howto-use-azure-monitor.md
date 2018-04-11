---
title: Surveiller les API publiées dans la Gestion des API Azure | Microsoft Docs
description: Suivez les étapes de ce didacticiel pour apprendre à surveiller votre API dans la Gestion des API.
services: api-management
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 93cbcf91af4ecf9425ed43ade400a0c82cea72d8
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="monitor-published-apis"></a>Surveiller les API publiées

Azure Monitor est un nouveau service Azure qui fournit une source unique d’analyse pour toutes vos ressources Azure. Avec Azure Monitor, vous pouvez visualiser, interroger, acheminer, archiver et agir sur les mesures et journaux provenant de vos ressources Azure, comme la gestion des API. 

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Afficher les journaux d’activité
> * Afficher les journaux de diagnostic
> * Afficher les métriques de votre API 
> * Configurer une règle d’alerte quand votre API obtient des appels non autorisés

La vidéo suivante montre comment surveiller la gestion des API à l’aide d’Azure Monitor. 

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]
>
>

## <a name="prerequisites"></a>Prérequis


+ Suivez le guide de démarrage rapide suivant : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).
+ Suivez également le didacticiel suivant : [Importer et publier votre première API](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="view-metrics-of-your-apis"></a>Afficher les métriques de vos API

Le service Gestion des API émet des métriques chaque minute, pour une visibilité en quasi temps réel de l’intégrité de vos API. Voici un récapitulatif d’une partie des métriques disponibles :

* Capacité (préversion) : vous permet de prendre des décisions concernant la mise à niveau/rétrogradation de vos services APIM. La métrique est émise chaque minute et reflète la capacité de la passerelle au moment de la création de rapports. Le calcul de cette métrique, dont la valeur est comprise entre 0 et 100, repose sur les recours à la passerelle, tels que l’utilisation du processeur et de la mémoire.
* Nombre total de demandes de passerelle : le nombre de requêtes d’API dans la période. 
* Demandes de la passerelle ayant abouti : le nombre de requêtes d’API ayant reçu des codes de réponse HTTP de succès, dont 304, 307 et toute valeur inférieure à 301 (par exemple, 200). 
* Demandes de la passerelle ayant échoué : le nombre de requêtes d’API ayant reçu des codes de réponse HTTP d’échec, dont 400 et toute valeur supérieure à 500.
* Demandes de la passerelle non autorisées : le nombre de requêtes d’API ayant reçu des codes de réponse HTTP comprenant 401, 403 et 429. 
* Autres demandes de la passerelle : le nombre de requêtes d’API ayant reçu des codes de réponse qui n’appartiennent à aucune des catégories précédentes (par exemple, 418).

Pour accéder aux métriques :

1. Sélectionnez **Métriques** dans le menu vers le bas de la page.
2. Dans la liste déroulante, sélectionner les métriques qui vous intéressent (vous pouvez ajouter plusieurs métriques). 
    
    Par exemple, sélectionnez **Nombre total de demandes de la passerelle** et **Demandes de la passerelle ayant échoué** dans la liste des métriques.
3. Le graphique affiche le nombre total d’appels d’API. Il montre également le nombre d’appels d’API qui ont échoué. 

## <a name="set-up-an-alert-rule-for-unauthorized-request"></a>Configurer une règle d’alerte pour une demande non autorisée

Vous pouvez configurer les paramètres pour recevoir des alertes en fonction des mesures et des journaux d’activité. Azure Monitor vous permet de configurer une alerte pour effectuer les opérations suivantes lors de son déclenchement :

* Envoyer un e-mail de notification
* Appeler un webhook
* Appeler une application logique Azure

Pour configurer des alertes :

1. Sélectionnez **Règles d’alerte** dans la barre de menus vers le bas de la page.
2. Sélectionnez **Ajouter une alerte Métrique**.
3. Entrez un **Nom** pour cette alerte.
4. Sélectionnez **Demandes de la passerelle non autorisées** comme métrique à surveiller.
5. Sélectionnez **Envoyer un e-mail aux propriétaires, aux contributeurs et aux lecteurs**.
6. Appuyez sur **OK**.
7. Essayez d’appeler notre API de conférence sans clé API. En tant que propriétaire de ce service Gestion des API, vous recevez une alerte par e-mail. 

    > [!TIP]
    > La règle d’alerte peut également appeler un webhook ou une application logique Azure quand elle est déclenchée.

    ![Configurer une alerte](./media/api-management-azure-monitor/set-up-alert.png)

## <a name="activity-logs"></a>Journaux d’activité

Les journaux d’activité fournissent des informations sur les opérations qui ont été effectuées sur les services de Gestion des API. Avec les journaux d’activité, vous pouvez déterminer « qui, quand et quoi » pour toutes les opérations d’écriture (PUT, POST, DELETE) sur vos services de Gestion des API. 

> [!NOTE]
> Les journaux d’activité n’incluent pas les opérations de lecture (GET) ou les opérations effectuées dans le portail Azure ou utilisant les API de gestion d’origine.

Vous pouvez accéder aux journaux d’activité dans votre service de Gestion des API, ou accéder aux journaux de toutes vos ressources Azure dans Azure Monitor. 

Pour afficher les journaux d’activité :

1. Sélectionnez votre instance de service APIM.
2. Cliquez sur **Journal d’activité**.

## <a name="diagnostic-logs"></a>Journaux de diagnostic

Les journaux de diagnostic offrent des informations détaillées sur les opérations et erreurs qui sont importantes pour l’audit ainsi qu’à des fins de dépannage. Les journaux de diagnostic diffèrent des journaux d’activité. Le journal d’activité fournit des informations sur les opérations qui ont été effectuées sur vos ressources Azure. Les journaux de diagnostic fournissent des informations sur les opérations effectuées par votre ressource.

Pour configurer les journaux de diagnostic :

1. Sélectionnez votre instance de service APIM.
2. Cliquez sur **Journal de diagnostic**.
3. Cliquez sur **Activer les diagnostics**. Vous pouvez archiver les journaux de diagnostic et les mesures dans un compte de stockage, les transmettre en continu à un hub d’événement ou les envoyer à Log Analytics. 

Le service Gestion des API fournit actuellement des journaux de diagnostic (par lot toutes les heures) concernant chaque requête d’API. Chaque entrée a le schéma suivant :

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

| Propriété  | type | Description |
| ------------- | ------------- | ------------- |
| isRequestSuccess | booléenne | True si la requête HTTP a échoué avec le code d’état de réponse dans la plage 2xx ou 3xx |
| time | date-time | Timestamp de réception de la requête HTTP par la passerelle |
| operationName | chaîne | Valeur constante « Microsoft.ApiManagement/GatewayLogs » |
| category | chaîne | Valeur constante « GatewayLogs » |
| durationMS | integer | Nombre de millisecondes entre le moment où la passerelle a reçu la requête et celui où la réponse complète a été envoyée |
| callerIpAddress | chaîne | Adresse IP de l’appelant de passerelle immédiat (peut être un intermédiaire) |
| correlationId | chaîne | Identificateur de requête http unique assigné par le service Gestion des API |
| location | chaîne | Nom de la région Azure dans laquelle se trouvait la passerelle qui a traité la requête |
| httpStatusCodeCategory | chaîne | Catégorie du code d’état de réponse http : réussite (inférieur ou égal à 301 ou 304 ou 307), non autorisé (401, 403, 429), erroné (400, entre 500 et 600), autre |
| ResourceId | chaîne | ID de la ressource du service Gestion des API /SUBSCRIPTIONS/<subscription>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.APIMANAGEMENT/SERVICE/<name> |
| properties | objet | Propriétés de la requête actuelle |
| method | chaîne | Méthode HTTP de la requête entrante |
| url | chaîne | URL de la requête entrante |
| clientProtocol | chaîne | Version du protocole HTTP de la requête entrante |
| responseCode | integer | Code d’état de la réponse HTTP envoyée à un client |
| backendMethod | chaîne | Méthode HTTP de la requête envoyée à un serveur principal |
| backendUrl | chaîne | URL de la requête envoyée à un serveur principal |
| backendResponseCode | integer | Code de la réponse HTTP reçue d’un serveur principal |
| backendProtocol | chaîne | Version du protocole HTTP de la requête envoyée à un service principal | 
| requestSize | integer | Nombre d’octets reçus d’un client au cours du traitement de la requête | 
| responseSize | integer | Nombre d’octets envoyés à un client au cours du traitement de la requête | 
| cache | chaîne | État d’implication du cache du service Gestion des API dans le traitement des requêtes (par exemple, atteint, manqué, aucun) | 
| cacheTime | integer | Nombre de millisecondes consacrées à l’ensemble des E/S du cache du service Gestion des API (connexion, envoi et réception d’octets) | 
| backendTime | integer | Nombre de millisecondes consacrées à l’ensemble des E/S du serveur principal (connexion, envoi et réception d’octets) | 
| clientTime | integer | Nombre de millisecondes consacrées à l’ensemble des E/S du client (connexion, envoi et réception d’octets) | 
| apiId | chaîne | Identificateur d’entité d’API pour la requête actuelle | 
| operationId | chaîne | Identificateur d’entité d’opération pour la requête actuelle | 
| productId | chaîne | Identificateur d’entité de produit pour la requête actuelle | 
| userId | chaîne | Identificateur d’entité d’utilisateur pour la requête actuelle | 
| apimSubscriptionId | chaîne | Identificateur d’entité d’abonnement pour la requête actuelle | 
| backendId | chaîne | Identificateur d’entité de serveur principal pour la requête actuelle | 
| LastError | objet | Dernière erreur de traitement de requête | 
| elapsed | integer | Nombre de millisecondes écoulées entre le moment où la passerelle a reçu la requête et celui où l’erreur s’est produite | 
| source | chaîne | Nom du gestionnaire interne de traitement ou de stratégie qui a provoqué l’erreur | 
| scope | chaîne | Étendue du document de stratégie qui contient la stratégie qui a provoqué l’erreur | 
| section | chaîne | Section du document de stratégie qui contient la stratégie qui a provoqué l’erreur | 
| motif | chaîne | Motif de l’erreur | 
| Message | chaîne | Message d’erreur | 

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Afficher les journaux d’activité
> * Afficher les journaux de diagnostic
> * Afficher les métriques de votre API 
> * Configurer une règle d’alerte quand votre API obtient des appels non autorisés

Passez au didacticiel suivant :

> [!div class="nextstepaction"]
> [Suivre des appels](api-management-howto-api-inspector.md)
