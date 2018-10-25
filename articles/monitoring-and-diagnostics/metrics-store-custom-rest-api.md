---
title: Envoyer des métriques personnalisées pour une ressource Azure à un magasin de métriques Azure Monitor à l’aide d’une API REST
description: Envoyer des métriques personnalisées pour une ressource Azure à un magasin de métriques Azure Monitor à l’aide d’une API REST
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: d36697e6b5765ecf35ed9b3add45cff6c33823a5
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49958216"
---
# <a name="send-custom-metrics-for-an-azure-resource-to-the-azure-monitor-metric-store-using-a-rest-api"></a>Envoyer des métriques personnalisées pour une ressource Azure à un magasin de métriques Azure Monitor à l’aide d’une API REST

Cet article vous montre comment envoyer des métriques personnalisées pour les ressources Azure au magasin de métriques Azure Monitor par le biais de l’API REST.  Une fois que les métriques se trouvent dans Azure Monitor, vous pouvez effectuer toutes les opérations que vous réalisez avec les métriques standards, comme la création de graphiques, la création d’alertes, le transfert vers d’autres outils externes, etc.  

>[!NOTE] 
>L’API REST autorise uniquement l’envoi de métriques personnalisées pour les ressources Azure. Si vous souhaitez envoyer des métriques pour les ressources dans différents environnements ou en local, vous pouvez utiliser [Application Insights](../application-insights/app-insights-api-custom-events-metrics.md).    


## <a name="create-and-authorize-a-service-principal-to-emit-metrics"></a>Créer et autoriser un principal de service pour émettre des métriques 

Créez un principal de service dans votre locataire Azure Active Directory en suivant les instructions indiquées dans [Créer un principal du service](../active-directory/develop/howto-create-service-principal-portal.md). 

Notez ce qui suit au cours de ce processus : 

- Vous pouvez indiquer n’importe quelle URL pour l’URL de connexion.  
- Créer une clé secrète client pour cette application  
- Enregistrez la clé et l’ID client pour une utilisation ultérieure.  

Donnez à l’application lors de l’étape 1 « Publication des métriques de surveillance » les autorisations relatives à la ressource dont vous souhaitez émettre des métriques. Si vous envisagez d’utiliser l’application pour émettre des métriques personnalisées concernant de nombreuses ressources, vous pouvez accorder ces autorisations au niveau du groupe de ressources ou de l’abonnement. 

## <a name="get-an-authorization-token"></a>Obtenir un jeton d’autorisation
Ouvrez une invite de commandes et exécuter la commande suivante.
```shell
curl -X POST https://login.microsoftonline.com/<yourtenantid>/oauth2/token -F "grant_type=client_credentials" -F "client_id=<insert clientId from earlier step> " -F "client_secret=<insert client secret from earlier step>" -F "resource=https://monitoring.azure.com/"
```
Sauvegardez le jeton d’accès à partir de la réponse.

![Jeton d'accès](./media/metrics-store-custom-rest-api/accesstoken.png)

## <a name="emit-the-metric-via-the-rest-api"></a>Émettre la métrique par le biais de l’API REST 

1. Collez le code JSON suivant dans un fichier et enregistrez-le sous le nom custommetric.json sur votre ordinateur local. Mettez à jour le paramètre de temps dans le fichier JSON. 
    
    ```json
    { 
        "time": "2018-09-13T16:34:20”, 
        "data": { 
            "baseData": { 
                "metric": "QueueDepth", 
                "namespace": "QueueProcessing", 
                "dimNames": [ 
                  "QueueName", 
                  "MessageType" 
                ], 
                "series": [ 
                  { 
                    "dimValues": [ 
                      "ImagesToProcess", 
                      "JPEG" 
                    ], 
                    "min": 3, 
                    "max": 20, 
                    "sum": 28, 
                    "count": 3 
                  } 
                ] 
            } 
        } 
    } 
    ``` 

1. Dans la fenêtre d’invite de commandes, publiez les données métriques 
    - Région Azure : doit correspondre à la région de déploiement de la ressource pour laquelle vous émettez des métriques. 
    - ID de ressource : ID de ressource de la ressource Azure pour laquelle vous suivez la métrique.  
    - Jeton d’accès : collez le jeton obtenu précédemment

    ```Shell 
    curl -X POST curl -X POST https://<azureRegion>.monitoring.azure.com/<resourceId> /metrics -H "Content-Type: application/json" -H "Authorization: Bearer <AccessToken>" -d @custommetric.json 
    ```
1. Modifiez le timestamp et les valeurs dans le fichier JSON. 
1. Répétez les deux étapes précédentes plusieurs fois afin d’avoir des données pour plusieurs minutes.

## <a name="troubleshooting"></a>Résolution de problèmes 
Si vous obtenez une erreur dans une partie du processus, pensez aux éléments suivants :

1. Vous ne pouvez pas émettre de métriques si un abonnement ou un groupe de ressources est considéré comme votre ressource Azure. 
1. Vous ne pouvez pas envoyer une métrique datant de plus de 20 minutes dans le magasin. Le magasin de métriques est optimisé pour la génération d’alertes et la création de graphiques en temps réel. 
2. Le nombre de noms de dimension doit correspondre aux valeurs, et inversement. Vérifiez les valeurs. 
2. Vous émettez peut-être des métriques pour une région qui ne prend pas en charge les métriques personnalisées. Consultez les [régions (préversion) de métriques personnalisées prises en charge](metrics-custom-overview.md#supported-regions) 



## <a name="view-your-metrics"></a>Afficher vos métriques 

1. Se connecter au portail Azure. 

1. Dans le menu de gauche, cliquez sur **Surveiller** 

1. Dans la page Surveiller, cliquez sur **Métriques**. 

   ![Jeton d'accès](./media/metrics-store-custom-rest-api/metrics.png) 

1. Modifiez la période d’agrégation afin d’indiquer **30 dernières minutes**.  

1. Dans la liste déroulante de *ressources*, sélectionnez la ressource pour laquelle vous avez émis la métrique.  

1. Dans la liste déroulante d’*espaces de noms*, sélectionnez **QueueProcessing** 

1. Dans la liste déroulante de *métriques*, sélectionnez **QueueDepth**.  

 
## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [métriques personnalisées](metrics-custom-overview.md).