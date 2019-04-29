---
title: Règle personnalisée du pare-feu application Web pour Azure porte d’entrée
description: Découvrez comment utiliser des règles personnalisées web application firewall (WAF) protection de vos applications web contre les attaques malveillantes.
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/07/2019
ms.author: kumud;tyao
ms.openlocfilehash: 744c6fb9235c9daa2d5239ef9fd13679db943650
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61459706"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>Règles personnalisées pour le pare-feu d’applications web avec Azure porte d’entrée
Pare-feu d’applications Azure web (WAF) avec le service de la porte d’entrée vous permet de contrôler l’accès à vos applications web en fonction des conditions que vous définissez. Une règle de pare-feu d’applications Web personnalisée se compose d’un numéro de priorité, un type de règle, conditions de correspondance et une action. Il existe deux types de règles personnalisées : correspond aux règles et règles de restriction. Une règle de correspondance contrôle l’accès en fonction de conditions de correspondance alors qu’une règle de limite de taux contrôle d’accès basée sur la correspondance des conditions et les taux de demandes entrantes. Vous pouvez désactiver une règle personnalisée pour l’empêcher d’en cours d’évaluation, mais toujours conserver la configuration. Cet article décrit les règles de correspondance qui reposent sur les paramètres http.

## <a name="priority-match-conditions-and-action-types"></a>Priorité, les conditions de correspondance et les types d’action
Vous pouvez contrôler l’accès avec une règle de pare-feu d’applications Web personnalisée qui définit un numéro de priorité, un type de règle, conditions de correspondance et une action. 

- **Priorité :** est un entier unique qui décrit l’ordre d’évaluation des règles de pare-feu d’applications Web. Règles ayant des valeurs inférieures sont évaluées avant les règles des valeurs plus élevées

- **Action :** définit comment acheminer une demande si une règle de pare-feu d’applications Web est mis en correspondance. Vous pouvez choisir parmi les actions ci-dessous pour appliquer quand une demande correspond à une règle personnalisée.

    - *Autoriser* -WAF transfère la quête pour le back-end, enregistre une entrée dans les journaux WAF et s’arrête.
    - *Bloc* -demande est bloquée, WAF envoie la réponse au client sans la transférer la demande vers le back-end. WAF enregistre une entrée dans les journaux de pare-feu d’applications Web.
    - *Journal* -journaux WAF une entrée dans le pare-feu d’applications Web se connecte et se poursuit évaluent la règle suivante.
    - *Rediriger* -WAF redirige la requête à un URI spécifié, enregistre une entrée dans les journaux de pare-feu d’applications Web et se termine.

- **Condition de correspondance :** définit une variable de correspondance, un opérateur et correspond à la valeur. Chaque règle peut contenir plusieurs conditions de correspondance. Une condition de correspondance peut être basée sur le ci-dessous *correspondent à des variables*:
    - Remaddr (adresse IP du client)
    - RequestMethod
    - QueryString
    - PostArgs
    - RequestUri
    - RequestHeader
    - RequestBody

- **Opérateur :** liste inclut les éléments suivants :
    - Un : est souvent utilisée pour définir l’action par défaut si aucune règle n’est mises en correspondance. Tout est une correspondance opérateur all.
    - IPMatch : définir des restrictions d’adresse IP pour remaddr variable
    - GeoMatch : définir géo filtrage pour remaddr variable
    - Égal à
    - Contains
    - LessThan : contrainte de taille
    - GreaterThan : contrainte de taille
    - LessThanOrEqual : contrainte de taille
    - GreaterThanOrEqual : contrainte de taille
    - BeginsWith
     - EndsWith

Vous pouvez définir *negate* condition est true si le résultat d’une condition doit être inversé.

*Correspond à la valeur* définit la liste de valeurs de correspondance possible.
Prise en charge de la méthode de demande HTTP incluent des valeurs :
- GET
- POST
- PUT
- HEAD
- SUPPRIMER
- VERROU
- DÉVERROUILLER
- PROFIL
- OPTIONS
- PROPFIND
- PROPPATCH
- MKCOL
- COPIE
- DÉPLACER

## <a name="examples"></a>Exemples

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>Exemple de règles personnalisées WAF selon les paramètres http

Voici un exemple qui illustre la configuration d’une règle personnalisée avec deux conditions de correspondance. Les demandes sont à partir d’un site spécifié, tel que défini par le point d’accès et chaîne de requête ne contient-elle pas de « password ».

```
# http rules example
{
  "name": "AllowFromTrustedSites",
  "priority": 1,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestHeader",
      "selector": "Referer",
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "www.mytrustedsites.com/referpage.html"
      ]
    },
    {
      "matchVariable": "QueryString",
      "operator": "Contains",
      "matchValue": [
        "password"
      ],
      "negateCondition": true
    }
  ],
  "action": "Allow",
  "transforms": []
}

```
Un exemple de configuration pour le blocage de méthode « PUT » s’affiche comme suit :

``` 
# http Request Method custom rules
{
  "name": "BlockPUT",
  "priority": 2,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestMethod",
      "selector": null,
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "PUT"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

### <a name="size-constraint"></a>Contrainte de taille

Vous pouvez générer une règle personnalisée qui spécifie la contrainte de taille de la part d’une demande entrante. Par exemple, sous la règle bloque une Url qui est plus de 100 caractères.

```
# http parameters size constraint
{
  "name": "URLOver100",
  "priority": 5,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestUri",
      "selector": null,
      "operator": "GreaterThanOrEqual",
      "negateCondition": false,
      "matchValue": [
        "100"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur [pare-feu d’applications web](waf-overview.md)
- Découvrez comment [créer une porte d’entrée](quickstart-create-front-door.md).

