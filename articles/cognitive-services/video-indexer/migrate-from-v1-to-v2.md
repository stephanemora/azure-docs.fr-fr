---
title: Migrer de l’API Azure Video Indexer v1 vers la version v2
titlesuffix: Azure Cognitive Services
description: Cette rubrique explique comment migrer de l’API Azure Video Indexer v1 vers la version v2.
services: cognitive services
author: juliako
manager: cgronlun
ms.service: cognitive-services
ms.component: video-indexer
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: juliako
ms.openlocfilehash: 3c70bbe11d94e6b03d615b8d1394ccdca6bd3790
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2018
ms.locfileid: "45985627"
---
# <a name="migrate-from-the-video-indexer-api-v1-to-v2"></a>Migrer à partir de l’API Video Indexer v1 vers la version v2

> [!Note]
> L’API Video Indexer v1 est déconseillée depuis le 1er août 2018. Vous devez désormais utiliser l’API Video Indexer v2. <br/>Pour développer des contenus avec les API Video Indexer v2, consultez les instructions fournies [ici](https://api-portal.videoindexer.ai/). 

Cet article décrit les modifications introduites dans la version v2.  

## <a name="api-changes"></a>Modifications d'API

### <a name="authorization-and-operations"></a>Autorisations et opérations

Dans la version v2, Video Indexer a modifié le modèle d’authentification et d’autorisation de l’API. Il existe deux ensembles d’API : 

* Authorization 
* Opérations

L’API **Autorisation** est utilisée pour obtenir des jetons d’accès pour appeler l’API **Opérations**. L’API **Opérations** contient toutes les API de Video Indexer, comme Charger la vidéo, Obtenir des insights et d’autres opérations.

Une fois que vous êtes [abonné](video-indexer-get-started.md) à l’API **Autorisation**, vous serez en mesure d’obtenir des jetons d’accès en passant votre clé d’abonnement (comme vous l’avez fait dans v1.)

## <a name="getting-and-using-the-access-token-for-operations-apis"></a>Obtention et utilisation du jeton d’accès pour les API d’opération

Lorsque vous appelez les API d’**opérations**, la clé d’abonnement ne sera plus être utilisée. Au lieu de cela, vous allez transmettre les jetons d’accès obtenus par l’API **Autorisation**. 

Chaque demande doit avoir un jeton valide, correspondant au niveau d'accès de l’API que vous appelez. Par exemple, les opérations sur votre utilisateur, telle que l’obtention de vos comptes, requièrent un jeton d’accès utilisateur. Les opérations au niveau du compte, telle que l’énumération de toutes les vidéos, requièrent un jeton d’accès de compte. Les opérations sur des vidéos, par exemple la réindexation d’une vidéo, requièrent un jeton d’accès de compte ou un jeton d’accès vidéo.

Pour faciliter les choses, vous pouvez utiliser l’API **Autorisation** > **GetAccounts** (Obtenir les comptes) pour obtenir vos comptes sans pour autant disposer d’un jeton utilisateur. Vous pouvez également demander à obtenir les comptes avec des jetons valides, ce qui vous évite un appel supplémentaire pour obtenir un jeton de compte.

Pour plus d’informations sur les différents jetons d’accès, consultez [Utiliser l’API Azure Video Indexer](video-indexer-use-apis.md).

### <a name="locations"></a>Emplacements

Chaque appel à l’API doit inclure l’emplacement de votre compte Video Indexer. Les appels d’API sans emplacement ou avec un emplacement incorrect échoueront.

Les valeurs décrites dans le tableau suivant s’appliquent. **Valeur param.** est la valeur que vous transmettez lorsque vous utilisez l’API.

|**Name**|**Valeur param.**|**Description**|
|---|---|---|
|Version d’évaluation|trial|Utilisé pour les comptes d’évaluation. Par exemple : https://api.videoindexer.ai/trial/Accounts/{accountId}/Videos/{videoId}/Index?language=English.|
|USA Ouest|westus2|Utilisé pour la région Azure USA Ouest 2.  Par exemple : https://api.videoindexer.ai/westus2/Accounts/{accountId}/Videos/{videoId}/Index?language=English.|
|Europe Nord |northeurope|Utilisé pour la région Azure Europe Nord. Par exemple : https://api.videoindexer.ai/northeurope/Accounts/{accountId}/Videos/{videoId}/Index?language=English. |
|Asie Est|eastasia|Utilisé pour la région Azure Asie-Pacifique. Par exemple : https://api.videoindexer.ai/eastasia/Accounts/{accountId}/Videos/{videoId}/Index?language=English.|

### <a name="data-model"></a>Modèle de données

Video Indexer a maintenant un modèle de données simplifié pour fournir des insights beaucoup plus clairs. Pour plus d’informations sur la sortie produite par l’API v2, consultez [Examiner la sortie de Video Indexer produite par l’API v2](video-indexer-output-json-v2.md).

### <a name="swagger"></a>Swagger

Les définitions de l’API Video Indexer ont été mises à jour en conséquence et sont disponibles en téléchargement via le [Portail des développeurs Video Indexer](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token).


### <a name="v1-vs-v2-examples"></a>Exemples entre la V1 et la V2

Les nouvelles API V2 impliquent 3 paramètres principaux :

1. [LOCATION] : comme décrit ci-dessus. Soit trial, westus2, northeurope ou eastasia.
2. [YOUR_ACCOUNT_ID] : un identificateur de GUID de votre compte. Récupéré lors de l’obtention de tous les comptes (décrite ci-dessous).
3. [YOUR_VIDEO_ID] : l’ID de votre vidéo (par exemple « d4fa369abc »). Retourné lors du chargement d’une vidéo ou lorsque vous recherchez des vidéos.

#### <a name="uploading-a-video-in-v1"></a>Chargement d’une vidéo en V1 :

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns?name=some_name&description=some_description&privacy=private&videoUrl=http://URL_TO_YOUR_VIDEO
```

#### <a name="uploading-a-video-in-v2"></a>Chargement d’une vidéo en V2 :

1. Obtenir un jeton accès pour la requête de chargement :

  Vous pouvez obtenir tous les comptes et les jetons d’accès :

    ```
    https://api.videoindexer.ai/auth/[LOCATION]/Accounts?generateAccessTokens=true&allowEdit=true
    ```

  Ou bien obtenir le jeton d'accès d’un compte spécifique :
  
  ```
  https://api.videoindexer.ai/auth/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/AccessToken?allowEdit=true
  ```
2. Charger une vidéo :

  ```
  POST https://api.videoindexer.ai/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/Videos?name=MySample&description=MySampleDescription&videoUrl=[URL_ENCODED_VIDEO_URL]&accessToken=eyJ0eXAiOiJ...
  ```

#### <a name="getting-insights-in-v1"></a>Obtention d’insights en V1 :

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/[VIDEO_ID]
```
  
#### <a name="getting-insights-in-v2"></a>Obtention d’insights en V2 :

1. Utilisez le jeton d’accès de compte ou obtenez un jeton d’accès de niveau vidéo :

  ```
  https://api.videoindexer.ai/auth/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/AccessToken
  ```
  
2. Obtenir des insights :

  ```
  https://api.videoindexer.ai/[LOCATION]/Accounts[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```

#### <a name="getting-video-processing-state-in-v1"></a>Obtention de l’état de traitement vidéo en V1 :

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/[VIDEO_ID]/State
```
  
#### <a name="getting-video-processing-state-in-v2"></a>Obtention de l’état de traitement vidéo en V2 :

Dans l’API v2, l’état de traitement est retourné en tant que partie de l’API Get Video Index.

1. Utilisez le jeton d’accès de compte ou obtenez un jeton d’accès de niveau vidéo :

  ```
  https://api.videoindexer.ai/trial/[LOCATION]/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```
  
2. Obtenir des insights :

  ```
  https://api.videoindexer.ai/trial/[LOCATION]/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```

## <a name="next-steps"></a>Étapes suivantes

[Utiliser l’API Azure Video Indexer](video-indexer-use-apis.md)

