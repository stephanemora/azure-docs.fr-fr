---
title: Stratégies d’accès Azure Video Analyzer
description: Cet article explique comment Azure Video Analyzer utilise les jetons JWT dans les stratégies d’accès pour sécuriser les vidéos.
ms.topic: reference
ms.date: 06/01/2021
ms.openlocfilehash: 3cf450249567d07bf6855d115a0e39640074eeb0
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2021
ms.locfileid: "114604196"
---
# <a name="access-policies"></a>Stratégies d'accès

Les stratégies d’accès définissent les autorisations et la durée d’accès à une ressource vidéo Video Analyzer spécifique. Ces stratégies d’accès offrent un contrôle et une flexibilité accrus. En effet, grâce à elles, les jetons JWT tiers (clients non AAD) peuvent autoriser les API clientes à effectuer les opérations suivantes : 

- Accès aux métadonnées de la vidéo. 
- Accès au streaming vidéo. 

## <a name="access-policy-definition"></a>Définition de la stratégie d’accès

```json
"name": "accesspolicyname1", 
"properties": { 
    "role": "Reader", 
    "authentication": { 
        "@type": "#Microsoft.VideoAnalyzer.JwtAuthentication", 
        "issuers": [ 
            "issuer1", 
            "issuer2" 
        ], 
        "audiences": [ 
            "audience1" 
        ], 
        "claims": [ 
            { 
                "name":"claimname1", 
                "value":"claimvalue1" 
            }, 
            { 
                "name":"claimname2", 
                "value":"claimvalue2" 
            } 
        ], 
        "keys": [ 
            { 
                "@type": "#Microsoft.VideoAnalyzer.RsaTokenKey", 
                "alg": "RS256", 
                "kid": "123", 
                "n": "YmFzZTY0IQ==", 
                "e": "ZLFzZTY0IQ==" 
            }, 
            { 
                "@type": "#Microsoft.VideoAnalyzer.EccTokenKey", 
                "alg": "ES256", 
                "kid": "124", 
                "x": "XX==", 
                "y": "YY==" 
            } 
        ] 
    } 
} 
```

> [!NOTE] 
> Un seul type de clé est nécessaire. 

### <a name="roles"></a>Rôles

Seul le rôle de lecteur est pris en charge.

### <a name="issuer-matching-rules"></a>Règles de correspondance de l’émetteur

Plusieurs problèmes peuvent être spécifiés dans la stratégie, un seul émetteur peut être spécifié dans le jeton.  L’émetteur correspond si l’émetteur du jeton fait partie des émetteurs spécifiés dans la stratégie.

### <a name="audience-matching-rules"></a>Règles de correspondance de l’audience

Si la valeur de l’audience est ${System.Runtime.BaseResourceUrlPattern} pour la ressource vidéo, l’audience fournie dans le jeton JWT doit correspondre à l’URL de la ressource de base. Sinon, l’audience du jeton doit correspondre à l’audience de la stratégie d’accès.

### <a name="claims-matching-rules"></a>Règles de correspondance des revendications

Plusieurs revendications peuvent être spécifiées dans la stratégie d’accès et dans le jeton JWT.  Toutes les revendications d’une stratégie d’accès doivent être fournies dans le jeton pour que la validation soit réussie. Toutefois, le jeton JWT peut avoir des revendications supplémentaires qui ne sont pas listées dans la stratégie d’accès.

### <a name="keys"></a>Keys

Deux types de clé sont pris en charge : RSA et ECC.

[RSA](https://wikipedia.org/wiki/RSA_(cryptosystem))

* @type- \#Microsoft.VideoAnalyzer.RsaTokenKey
* alg - Algorithme.  Peut prendre les valeurs 256, 384 ou 512 
* kid - ID de clé
* n - Modulo
* e - Exposant public 

[ECC](https://wikipedia.org/wiki/Elliptic-curve_cryptography)        

* @type- \#Microsoft.VideoAnalyzer.EccTokenKey
* alg - Algorithme.  Peut prendre les valeurs 256, 384 ou 512
* kid - ID de clé
* x - Valeur de la coordonnée.
* y - Valeur de la coordonnée.

### <a name="token-validation-process"></a>Processus de validation des jetons

Les clients doivent créer leurs propres jetons JWT, lesquels sont validés via la méthode suivante :

- Dans la liste des stratégies qui correspondent à l’ID de clé, nous validons :
  - Signature de jeton
  - Expiration du jeton
  - Émetteur
  - Public visé
  - Revendications supplémentaires

### <a name="policy-audience-and-token-matching-examples"></a>Exemples d’audience de stratégie et de correspondance des jetons :

| **Audience de stratégie**                      | URL demandée                         | URL du jeton                            | Résultats |
| ---------------------------------------- | ------------------------------------- | ------------------------------------ | ------ |
| (Tout littéral)                            | (TOUT)                                 | (Correspondance)                              | Accorder  |
| (Tout littéral)                            | (TOUT)                                 | (Aucune correspondance)                          | Deny   |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos                   | https://fqdn/videos/*                | Accorder  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos                   | https://fqdn/videos/{videoName}      | Deny   |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}       | https://fqdn/vid*                    | Accorder  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}       | https://fqdn/videos/*                | Accorder  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}       | https://fqdn/videos/{baseVideoName}* | Accorder  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}       | https://fqdn/videos/{videoName}      | Accorder  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}Suffix | https://fqdn/videos/{videoName}      | Deny   |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{otherVideoName}  | https://fqdn/videos/{videoName}      | Deny   |

> [!NOTE]  
> Video Analyzer prend en charge 20 stratégies au maximum.  ${System.Runtime.BaseResourceUrlPattern} permet une plus grande flexibilité d’accès à des ressources spécifiques via l’utilisation d’une stratégie d’accès et de plusieurs jetons.  Ces jetons autorisent ensuite l’accès à différentes ressources Video Analyzer en fonction de l’audience. 

## <a name="creating-an-access-policy"></a>Création d’une stratégie d’accès

Il existe deux façons de créer une stratégie d’accès.

### <a name="in-the-azure-portal"></a>Dans le portail Azure

1. Connectez-vous au portail Azure et accédez au groupe de ressources où se trouve votre compte Video Analyzer.
2. Sélectionnez la ressource Video Analyzer.
3. Sous Video Analyzer, sélectionnez Stratégies d’accès

   :::image type="content" source="./media/access-policies/access-policies-menu.png" alt-text="Menu Stratégies d’accès dans le portail Azure":::
4. Cliquez sur nouveau, puis entrez ce qui suit :

   - Nom de la stratégie d’accès : n’importe quel nom
   - Émetteur : doit correspondre à l’émetteur du jeton JWT 
   - Audience : Audience du jeton JWT -- ${System.Runtime.BaseResourceUrlPattern} est la valeur par défaut. 
   - Type de clé : kty 
   - Algorithme : alg
   - ID de clé : kid 
   - N / Valeur X 
   - E / Valeur Y 

   :::image type="content" source="./media/access-policies/access-policies-portal.png" alt-text="Stratégies d’accès dans le portail Azure":::
5. Cliquez sur `Save`.

### <a name="create-access-policy-via-api"></a>Créer une stratégie d’accès via l’API

Consultez l’API Azure Resource Manager (ARM) 

## <a name="next-steps"></a>Étapes suivantes

[Vue d'ensemble](overview.md)
