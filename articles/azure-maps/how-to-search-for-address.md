---
title: Rechercher une adresse à l’aide du service Azure Maps Search | Microsoft Docs
description: Découvrez comment rechercher une adresse à l’aide du service Azure Maps Search
author: walsehgal
ms.author: v-musehg
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 56194bcfb9531def87a9918ad442a2927413c964
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432958"
---
# <a name="find-an-address-using-the-azure-maps-search-service"></a>Rechercher une adresse à l’aide du service Azure Maps Search

Le service Azure Maps Search est un ensemble d’API RESTful destinées aux développeurs souhaitant rechercher des adresses, des lieux, des points d’intérêt, des listes d’entreprises et d’autres informations d’ordre géographique. Le service affecte une combinaison latitude/longitude à une adresse, intersection, caractéristique géographique ou point d’intérêt spécifique. Les valeurs de latitude et de longitude renvoyées par le service peuvent être utilisées comme paramètres dans d’autres services Azure Maps, tels que les itinéraires et les flux de trafic.

Dans cet article, vous allez apprendre à :

* Rechercher une adresse à l’aide de l’[API de recherche approximative](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* Rechercher une adresse avec des propriétés et des coordonnées
* Effectuer une [recherche d’adresse inverse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) pour rechercher une adresse postale
* Rechercher une intersection à l’aide d’une [API de recherche d’intersection d’adresse inverse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)

## <a name="prerequisites"></a>Conditions préalables requises

Pour appeler les API du service Maps, vous avez besoin d’un compte et d’une clé Maps. Suivez les instructions mentionnées dans [Créer un compte](quick-demo-map-app.md#create-an-account-with-azure-maps) pour créer un abonnement de compte Azure Maps, puis effectuez les étapes indiquées dans [Obtenir la clé primaire](quick-demo-map-app.md#get-the-primary-key-for-your-account) afin d’obtenir la clé primaire de votre compte. Pour plus d’informations sur l’authentification dans Azure Maps, consultez [Gérer l’authentification dans Azure Maps](./how-to-manage-authentication.md).

Cet article utilise [l’application Postman](https://www.getpostman.com/apps) pour générer des appels REST. Vous pouvez utiliser l’environnement de développement d’API que vous préférez.

## <a name="using-fuzzy-search"></a>Utilisation de Fuzzy Search

L’API par défaut pour le service de recherche repose sur le mécanisme de [recherche partielle](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) et est utile lorsque vous ne connaissez pas les entrées utilisateur pour une requête de recherche. L’API combine la recherche de points d’intérêt et le géocodage dans une « recherche canonique à ligne unique ». Par exemple, l’API peut gérer les entrées de toute combinaison de jetons de points d’intérêt ou d’adresses. L’API peut également être affinée avec une position contextuelle (paire lat./lon.), limitée par des coordonnées et un rayon, ou exécutée plus généralement sans point d’ancrage à biais géographique.

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

    L’attribut **json** dans le chemin de l’URL détermine le format de la réponse. Vous utilisez json tout au long de cet article pour des raisons de facilité d’utilisation et de lisibilité. Les formats de réponse sont disponibles dans la définition **Get Search Fuzzy** (Obtenir Fuzzy Search) des [informations de référence sur l’API fonctionnelle Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

3. Cliquez sur **Params** (Paramètres), puis entrez les paires clé/valeur suivantes à utiliser en tant que paramètres de requête ou de chemin d’accès dans l’URL de requête :

    ![Recherche approximative](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Clé | Valeur |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<votre clé Azure Maps\> |
    | query | pizza |

4. Cliquez sur **Send** (Envoyer), puis examinez le corps de la réponse.

    La chaîne de requête ambiguë « pizza » a retourné 10 [résultats de point d’intérêt](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) dans les catégories « pizza » et « restaurant ». Chaque résultat retourne une adresse postale, les valeurs latitude/longitude, un point de vue et les points d’entrée de l’emplacement.
  
    Les résultats peuvent varier pour cette requête et ne sont pas liés à un emplacement de référence particulier. Vous pouvez utiliser le paramètre **countrySet** pour ne spécifier que les pays/régions que votre application doit couvrir. En effet, le comportement par défaut consiste à effectuer la recherche dans le monde entier, ce qui peut générer des résultats superflus.

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

## <a name="search-for-address-properties-and-coordinates"></a>Rechercher des propriétés et des coordonnées d’adresse

Vous pouvez transmettre une adresse postale complète ou partielle à l’API Search Address et recevoir une réponse qui inclut les propriétés détaillées de l’adresse telles que la commune ou la subdivision, ainsi que sa position exprimée en latitude et longitude.

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
    | subscription-key | \<votre clé Azure Maps\> |
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

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Rechercher une adresse postale à l’aide d’une recherche d’adresse inverse

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
    | subscription-key | \<votre clé Azure Maps\> |
    | query | 47.591180,-122.332700 |
  
4. Cliquez sur **Send** (Envoyer), puis examinez le corps de la réponse.

    La réponse inclut des informations d’adresse clés sur Safeco Field.
  
5. Ajoutez la paire Clé/Valeur suivante à la section **Params** puis cliquez sur **Envoyer** :

    | Clé | Valeur |
    |-----|------------|
    | nombre | true |

    Si la requête comporte le paramètre [number](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse), la réponse peut inclure le côté de la rue (gauche/droite) et également une position de décalage par rapport à ce numéro.
  
6. Ajoutez la paire Clé/Valeur suivante à la section **Params** puis cliquez sur **Envoyer** :

    | Clé | Valeur |
    |-----|------------|
    | returnSpeedLimit | true |
  
    Si le paramètre de requête [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) est défini, la réponse retourne la vitesse maximale autorisée.

7. Ajoutez la paire Clé/Valeur suivante à la section **Params** puis cliquez sur **Envoyer** :

    | Clé | Valeur |
    |-----|------------|
    | returnRoadUse | true |

    Si le paramètre de requête [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) est défini, la réponse retourne le tableau de l’état du trafic routier des géocodes inverses au niveau des rues.

8. Ajoutez la paire Clé/Valeur suivante à la section **Params** puis cliquez sur **Envoyer** :

    | Clé | Valeur |
    |-----|------------|
    | roadUse | true |

    Vous pouvez limiter la requête de géocode inverse à un type spécifique de trafic routier à l’aide du paramètre de requête [roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse).
  
## <a name="search-for-the-cross-street-using-reverse-address-cross-street-search"></a>Rechercher une intersection à l’aide d’une recherche d’intersection d’adresse inverse

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
    | subscription-key | \<votre clé Azure Maps\> |
    | query | 47.591180,-122.332700 |
  
4. Cliquez sur **Send** (Envoyer), puis examinez le corps de la réponse.

## <a name="next-steps"></a>Étapes suivantes

- Explorez la documentation de l’API du [service Azure Maps Search](https://docs.microsoft.com/rest/api/maps/search).
