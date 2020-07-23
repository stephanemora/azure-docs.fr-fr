---
title: Rechercher un emplacement à l’aide des services Search d’Azure Maps | Microsoft Azure Maps
description: Cet article explique comment rechercher un emplacement à l’aide du service Search de Microsoft Azure Maps pour le géocodage et le géocodage inverse.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 2d8c7ce62f7c592c396fa1ea7a7f5e7dc7df2dc1
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86517584"
---
# <a name="search-for-a-location-using-azure-maps-search-services"></a>Rechercher un emplacement à l’aide des services Search d’Azure Maps

Le [service Search](https://docs.microsoft.com/rest/api/maps/search) d’Azure Maps est un ensemble d’API RESTful destinées aux développeurs souhaitant rechercher des adresses, des lieux, des listes d’entreprises par nom ou catégorie et d’autres informations d’ordre géographique. En plus de prendre en charge le géocodage traditionnel, les services peuvent aussi géocoder en inversé les adresses et rues de croisement en fonction des latitudes et longitudes. Les valeurs de latitude et de longitude renvoyées par le service peuvent être utilisées comme paramètres dans d’autres services d’Azure Maps, tels que les services [Itinéraire](https://docs.microsoft.com/rest/api/maps/route) et [Météo](https://docs.microsoft.com/rest/api/maps/weather).

Dans cet article, vous allez apprendre à :

* Demander des coordonnées de latitude et de longitude pour une adresse (emplacement de l’adresse géocodée) à l’aide de l’[API Search Address]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)
* Rechercher une adresse ou un point d’intérêt (POI) à l’aide de l’[API Fuzzy Search](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* Rechercher une adresse avec des propriétés et des coordonnées
* Créer une [recherche d’adresse inversée](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) pour traduire les coordonnées de l’emplacement en adresse postale
* Rechercher une intersection à l’aide d’une [API de recherche d’intersection d’adresse inverse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)

## <a name="prerequisites"></a>Prérequis

Pour réaliser les étapes de cet article, vous devez d’abord créer un compte Azure Maps et vous procurer la clé d’abonnement de ce compte. Suivez les instructions mentionnées dans [Créer un compte](quick-demo-map-app.md#create-an-azure-maps-account) pour créer un abonnement de compte Azure Maps, puis effectuez les étapes indiquées dans [Obtenir la clé primaire](quick-demo-map-app.md#get-the-primary-key-for-your-account) afin d’obtenir la clé primaire de votre compte. Pour plus d’informations sur l’authentification dans Azure Maps, voir [Gérer l’authentification dans Azure Maps](./how-to-manage-authentication.md).

Cet article utilise [l’application Postman](https://www.getpostman.com/apps) pour générer des appels REST. Vous pouvez utiliser l’environnement de développement d’API que vous préférez.

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>Demander la latitude et la longitude d’une adresse (géocodage)

Dans cet exemple, nous utilisons l’[API Get Search Address](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) d’Azure Maps pour convertir l’adresse postale en coordonnées de latitude et de longitude. Vous pouvez transmettre une adresse postale complète ou partielle à l’API et recevoir une réponse qui inclut les propriétés détaillées de l’adresse telles que la rue, le code postal et le pays ou la région, ainsi que sa position exprimée en latitude et longitude.

Si vous avez un ensemble d’adresses à géocoder, vous pouvez utiliser l’[API Post Search Address Batch](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatch) pour envoyer un lot de requêtes en un seul appel d’API.

1. Dans Postman, cliquez sur **New Request** (Nouvelle requête) | **GET request** (Requête GET) et nommez-la **Address Search** (Recherche d’adresses).

2. Sous l’onglet Builder (Générateur), sélectionnez la méthode HTTP **GET**, entrez l’URL de la requête pour le point de terminaison de votre API et sélectionnez un protocole d’autorisation, le cas échéant.

![Recherche d’adresses](./media/how-to-search-for-address/address_search_url.png)

| Paramètre | Valeur suggérée |
|---------------|------------------------------------------------| 
| HTTP method | GET |
| URL de la demande | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) | 
| Autorisation | No Auth (Pas d’autorisation) |

3. Cliquez sur **Params** (Paramètres), puis entrez les paires clé/valeur suivantes à utiliser en tant que paramètres de requête ou de chemin d’accès dans l’URL de requête : 

![Recherche d’adresses](./media/how-to-search-for-address/address_search_params.png) 

| Clé | Valeur | 
|------------------|-------------------------| 
| api-version | 1.0 | 
| subscription-key | \<your Azure Maps key\> | 
| query | 400 Broad St, Seattle, WA 98109 | 

4. Cliquez sur **Send** (Envoyer), puis examinez le corps de la réponse. 

Dans ce cas, vous avez spécifié une requête d’adresse complète et recevez un résultat unique dans le corps de la réponse. 

5. Dans Params (Paramètres), modifiez la chaîne de requête comme suit : 

    ```plaintext 
        400 Broad, Seattle 
    ``` 

6. Ajoutez la paire Clé/Valeur suivante à la section **Params** puis cliquez sur **Envoyer** : 

| Clé | Valeur | 
|-----|------------| 
| typeahead | true | 

L’indicateur **typeahead** indique à l’API Address Search de traiter la requête en tant qu’entrée partielle et de retourner un tableau de valeurs prédictives.

## <a name="using-fuzzy-search-api"></a>Utilisation de l’API Recherche approximative

L’[API Fuzzy Search](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) d’Azure Maps est le service recommandé à utiliser lorsque vous ne connaissez pas les entrées utilisateur pour une requête de recherche. L’API combine la recherche de points d’intérêt (POI) et le géocodage dans une « recherche canonique à ligne unique ». Par exemple, l’API peut gérer les entrées de toute combinaison de jetons de points d’intérêt ou d’adresses. L’API peut également être affinée avec une position contextuelle (paire lat./lon.), limitée par des coordonnées et un rayon, ou exécutée plus généralement sans point d’ancrage à biais géographique.

La plupart des requêtes de recherche utilisent par défaut le paramétrage `maxFuzzyLevel=1` pour optimiser les performances et réduire les résultats inhabituels. Selon le cas, vous pouvez remplacer ce paramétrage par défaut en passant le paramètre de requête `maxFuzzyLevel=2` ou `3`.

### <a name="search-for-an-address-using-fuzzy-search"></a>Rechercher une adresse à l’aide de Fuzzy Search

1. Ouvrez l’application Postman, cliquez sur New (Nouveau) | Create New (Créer), puis sélectionnez **GET request** (Requête GET). Entrez **Fuzzy Search** comme nom de requête, sélectionnez la collection ou le dossier dans lequel enregistrer la requête, puis cliquez sur **Save** (Enregistrer).

2. Sous l’onglet Builder (Générateur), sélectionnez la méthode **GET**, puis entrez l’URL de la requête pour le point de terminaison de votre API.

    ![Recherche approximative](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Paramètre | Valeur suggérée |
    |---------------|------------------------------------------------|
    | HTTP method | GET |
    | URL de la demande | [https://atlas.microsoft.com/search/fuzzy/json?](https://atlas.microsoft.com/search/fuzzy/json?) |
    | Autorisation | No Auth (Pas d’autorisation) |

    L’attribut **json** dans le chemin de l’URL détermine le format de la réponse. Cet article utilise json pour faciliter l’utilisation et la lisibilité. Les formats de réponse sont disponibles dans la définition **Get Search Fuzzy** (Obtenir Fuzzy Search) des [informations de référence sur l’API fonctionnelle Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

3. Cliquez sur **Params** (Paramètres), puis entrez les paires clé/valeur suivantes à utiliser en tant que paramètres de requête ou de chemin d’accès dans l’URL de requête :

    ![Recherche approximative](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Clé | Valeur |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<your Azure Maps key\> |
    | query | pizza |

4. Cliquez sur **Send** (Envoyer), puis examinez le corps de la réponse.

    La chaîne de requête ambiguë « pizza » a retourné 10 [résultats de point d’intérêt](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) (POI) dans les catégories « pizza » et « restaurant ». Chaque résultat retourne une adresse postale, des valeurs de latitude et longitude, un port d’affichage et des points d’entrée pour l’emplacement.
  
    Les résultats peuvent varier pour cette requête et ne sont pas liés à un emplacement de référence particulier. Vous pouvez utiliser le paramètre **countrySet** pour spécifier uniquement les pays/régions pour lesquels votre application a besoin d’une couverture. Le comportement par défaut consiste à faire porter la recherche sur le monde entier, avec le risque de renvoyer des résultats superflus.

5. Ajoutez la paire Clé/Valeur suivante à la section **Params** puis cliquez sur **Envoyer** :

    | Clé | Valeur |
    |------------------|-------------------------|
    | countrySet | US |
  
    Les résultats sont désormais délimités par le code de pays ; en l’occurrence, la requête retourne des pizzerias aux États-Unis.
  
    Pour obtenir des résultats pour un emplacement particulier, vous pouvez interroger un point d’intérêt et utiliser les valeurs de longitude et de latitude retournées dans l’appel au service Fuzzy Search. En l’occurrence, vous avez utilisé Search Service pour retourner l’emplacement de la tour Seattle Space Needle et utilisé les valeurs de latitude et de longitude pour orienter la recherche.
  
6. Dans Params (Paramètres), entrez la paire clé/valeur suivante, puis cliquez sur **Send** (Envoyer) :

    ![Recherche approximative](./media/how-to-search-for-address/fuzzy_search_latlon.png)
  
    | Clé | Valeur |
    |-----|------------|
    | lat | 47.620525 |
    | lon | -122.349274 |


## <a name="search-for-a-street-address-using-reverse-address-search"></a>Rechercher une adresse postale à l’aide d’une recherche d’adresse inverse

L’API [Get Search Address Reverse]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) (Obtenir des adresses de recherche inverse) d’Azure Maps permet de traduire une coordonnée (par exemple, 37,786505, -122,3862) en adresse postale intelligible. Le plus souvent, cela est nécessaire dans les applications de suivi où vous recevez un flux GPS de l’appareil ou d’une ressource, et souhaitez connaître l’adresse à laquelle correspondent les coordonnées.
Si vous avez un ensemble de coordonnées dont vous voulez inverser le géocodage pour obtenir l’adresse correspondante, vous pouvez utiliser l’[API Post Search Address Reverse Batch](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatch) pour envoyer un lot de requêtes dans un seul appel d’API.


1. Dans Postman, cliquez sur **New Request** (Nouvelle requête) | **GET request** (Requête GET) et nommez-la **Reverse Address Search** (Recherche d’adresse inverse).

2. Sous l’onglet Builder (Générateur), sélectionnez la méthode **GET**, puis entrez l’URL de la requête pour le point de terminaison de votre API.
  
    ![URL de la recherche d’adresse inverse](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Paramètre | Valeur suggérée |
    |---------------|------------------------------------------------|
    | HTTP method | GET |
    | URL de la demande | [https://atlas.microsoft.com/search/address/reverse/json?](https://atlas.microsoft.com/search/address/reverse/json?) |
    | Autorisation | No Auth (Pas d’autorisation) |
  
3. Cliquez sur **Params** (Paramètres), puis entrez les paires clé/valeur suivantes à utiliser en tant que paramètres de requête ou de chemin d’accès dans l’URL de requête :
  
    ![Paramètres de la recherche d’adresse inverse](./media/how-to-search-for-address/reverse_address_search_params.png)
  
    | Clé | Valeur |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<your Azure Maps key\> |
    | query | 47.591180,-122.332700 |
  
4. Cliquez sur **Send** (Envoyer), puis examinez le corps de la réponse.

    La réponse inclut des informations d’adresse clés sur Safeco Field.
  
5. Ajoutez la paire Clé/Valeur suivante à la section **Params** puis cliquez sur **Envoyer** :

    | Clé | Valeur |
    |-----|------------|
    | nombre | true |

    Si la demande comporte le paramètre de requête [number](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse), la réponse peut inclure le côté de la rue (gauche ou droit), ainsi qu’une position de décalage par rapport à ce numéro.
  
6. Ajoutez la paire Clé/Valeur suivante à la section **Params** puis cliquez sur **Envoyer** :

    | Clé | Valeur |
    |-----|------------|
    | returnSpeedLimit | true |
  
    Si le paramètre de requête [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) est défini, la réponse retourne la vitesse maximale publiée.

7. Ajoutez la paire Clé/Valeur suivante à la section **Params** puis cliquez sur **Envoyer** :

    | Clé | Valeur |
    |-----|------------|
    | returnRoadUse | true |

    Si le paramètre de requête [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) est défini, la réponse retourne le tableau de l’état du trafic routier des géocodes inverses au niveau des rues.

8. Ajoutez la paire Clé/Valeur suivante à la section **Params** puis cliquez sur **Envoyer** :

    | Clé | Valeur |
    |-----|------------|
    | roadUse | true |

    Vous pouvez limiter la requête de géocode inverse à un type spécifique de route à l’aide du paramètre de requête [roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse).
  
## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>Rechercher une intersection à l’aide d’une recherche d’intersection d’adresse inverse

1. Dans Postman, cliquez sur **New Request** (Nouvelle requête) | **GET request** (Requête GET) et nommez-la **Reverse Address Cross Street Search** (Recherche d’intersection d’adresse inverse).

2. Sous l’onglet Builder (Générateur), sélectionnez la méthode **GET**, puis entrez l’URL de la requête pour le point de terminaison de votre API.
  
    ![Recherche d’intersection d’adresse inverse](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Paramètre | Valeur suggérée |
    |---------------|------------------------------------------------|
    | HTTP method | GET |
    | URL de la demande | [https://atlas.microsoft.com/search/address/reverse/crossstreet/json?](https://atlas.microsoft.com/search/address/reverse/crossstreet/json?) |
    | Autorisation | No Auth (Pas d’autorisation) |
  
3. Cliquez sur **Params** (Paramètres), puis entrez les paires clé/valeur suivantes à utiliser en tant que paramètres de requête ou de chemin d’accès dans l’URL de requête :
  
    | Clé | Valeur |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<your Azure Maps key\> |
    | query | 47.591180,-122.332700 |
  
4. Cliquez sur **Send** (Envoyer), puis examinez le corps de la réponse.

## <a name="next-steps"></a>Étapes suivantes

- Explorer la [documentation sur l’API REST du service Recherche](https://docs.microsoft.com/rest/api/maps/search) Azure Maps.
- Découvrez les [meilleures pratiques pour le service Recherche Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search) et comment optimiser vos requêtes.
