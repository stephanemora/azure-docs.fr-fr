---
title: Stratégies dans Gestion des API Azure | Microsoft Docs
description: Apprenez à créer, à modifier et à configurer des stratégies dans Gestion des API. Consultez des exemples de code et d’autres ressources disponibles.
services: api-management
documentationcenter: ''
author: dlepow
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/25/2021
ms.author: danlep
ms.openlocfilehash: 37e25908bd08ca9618bc56bb2e10ff8e48a46fc6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128649675"
---
# <a name="policies-in-azure-api-management"></a>Stratégies dans Gestion des API Azure

Dans Gestion des API Azure, les éditeurs d’API peuvent modifier le comportement des API par le biais d’une configuration à l’aide de stratégies. Les stratégies sont un ensemble d'instructions exécutées de manière séquentielle sur demande ou sur réponse d'une API. Exemples d’instructions courantes :

* Conversion de format XML en JSON.
* Limitation de la fréquence des appels pour restreindre le nombre d’appels entrants provenant d’un développeur. 

De nombreuses autres stratégies sont disponibles.

Les stratégies sont appliquées à l’intérieur de la passerelle entre le consommateur de l’API et l’API managée. Tandis que la passerelle reçoit les requêtes et les transmet, intactes, à l’API sous-jacente, une stratégie peut appliquer des modifications à la requête entrante et à la réponse sortante.

Sauf indication contraire de la stratégie, les expressions de stratégie peuvent être utilisées comme valeurs d’attribut ou valeurs de texte dans n’importe quelle stratégie de Gestion des API. Certaines stratégies sont basées sur des expressions de stratégie, telles que [Control flow][Control flow] et [Set variable][Set variable]. Pour plus d’informations, consultez les articles [Stratégies avancées][Advanced policies] et [Expressions de stratégie][Policy expressions].

## <a name="understanding-policy-configuration"></a><a name="sections"> </a>Configuration de la stratégie

Les définitions de stratégie sont des documents XML simples qui décrivent une séquence d’instructions entrantes et sortantes. Vous pouvez modifier le code XML directement dans la fenêtre de définition, qui fournit également les éléments suivants :
* Une liste d’instructions à droite.
* Les instructions applicables à l’étendue actuelle activées et mises en surbrillance.

Lorsque vous cliquez sur une instruction active, le code XML correspondant est inséré à l'emplacement du curseur dans la fenêtre de définition. 

> [!NOTE]
> Si la stratégie que vous souhaitez ajouter n’est pas activée, vérifiez que vous êtes dans l’étendue correcte pour cette stratégie. Chaque instruction de stratégie est conçue pour une utilisation dans certaines étendues et sections de la stratégie. Pour consulter les sections et les étendues d’une stratégie, consultez la section **Utilisation** dans la [Référence de la stratégie][Policy Reference].

La configuration est composée des sections `inbound`, `backend`, `outbound` et `on-error`. Cette série d’instructions de stratégie spécifiées s’exécute dans l’ordre pour une requête et une réponse.

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is forwarded to 
         the backend service go here -->
  </backend>
  <outbound>
    <!-- statements to be applied to the response go here -->
  </outbound>
  <on-error>
    <!-- statements to be applied if there is an error condition go here -->
  </on-error>
</policies> 
```

Si une erreur se produit pendant le traitement d’une requête :
* Les étapes restantes dans les sections `inbound`, `backend` ou `outbound` sont ignorées.
* L’exécution passe aux instructions de la section `on-error`.

En plaçant des instructions de stratégie dans la section `on-error`, vous pouvez :
* Examiner l’erreur à l’aide de la propriété `context.LastError`.
* Inspecter et personnaliser la réponse d’erreur à l’aide de la stratégie `set-body`.
* Configurer ce qui se passe si une erreur se produit. 

Pour plus d'informations, consultez [Gestion des erreurs dans les stratégies de gestion des API](./api-management-error-handling-policies.md) pour les codes d’erreur suivants :
* Étapes intégrées
* Erreurs susceptibles de se produire pendant le traitement des instructions de stratégie. 

## <a name="how-to-configure-policies"></a><a name="scopes"> </a>Configuration des stratégies

Pour plus d’informations sur la façon de configurer des stratégies, consultez [Définir ou modifier des stratégies](set-edit-policies.md).

## <a name="policy-reference"></a>Référence de stratégie

Pour obtenir la liste complète des instructions et des paramètres de stratégie, consultez la section [Référence de stratégie](./api-management-policies.md).

## <a name="policy-samples"></a>Exemples de stratégie

Consultez [Exemples de stratégie](./policy-reference.md) pour obtenir plus d’exemples de code.

## <a name="examples"></a>Exemples

### <a name="apply-policies-specified-at-different-scopes"></a>Appliquer des stratégies spécifiées à différentes portées

Si vous avez une stratégie configurée au niveau global et une stratégie configurée pour une API, les deux stratégies sont appliquées dès que cette API spécifique est utilisée. Le service Gestion des API permet de trier de façon déterminée les instructions de stratégie combinées via l’élément `base`. 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

Dans l’exemple de définition de stratégie ci-dessus :
* L’instruction `cross-domain` s’exécute avant toute stratégie supérieure.
* La stratégie `find-and-replace` s’exécute après toute stratégie supérieure. 

>[!NOTE]
> Si vous supprimez la balise `<base />` au niveau de l’étendue de l’API, seules les stratégies configurées dans l’étendue de l’API seront appliquées. Aucune stratégie de produit ou d’étendue globale ne sera appliquée.

### <a name="restrict-incoming-requests"></a>Limiter les demandes entrantes

Pour ajouter une nouvelle instruction afin de limiter les demandes entrantes à certaines adresses IP, placez le curseur juste à l’intérieur du contenu de l’élément XML `inbound`, puis cliquez sur l’instruction **Restrict caller IPs** (Limiter les adresses IP d’appelant).

![Restriction policies][policies-restrict]

Ceci ajoute un code XML à l'élément `inbound` , indiquant comment configurer l'instruction.

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address"/>
</ip-filter>
```

Pour limiter les demandes entrantes et n'accepter que celles venant de l'adresse IP 1.2.3.4, modifiez le code XML comme suit :

```xml
<ip-filter action="allow">
    <address>1.2.3.4</address>
</ip-filter>
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation de stratégies, consultez les pages :

+ [Transform and protect your API](transform-api.md) (Transformer et protéger votre API)
+ [Référence de stratégie](./api-management-policies.md) pour obtenir la liste complète des instructions et des paramètres de stratégie
+ [Exemples de stratégie](./policy-reference.md)   

[Policy Reference]: ./api-management-policies.md
[Product]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md
[Operation]: ./mock-api-responses.md

[Advanced policies]: ./api-management-advanced-policies.md
[Control flow]: ./api-management-advanced-policies.md#choose
[Set variable]: ./api-management-advanced-policies.md#set-variable
[Policy expressions]: ./api-management-policy-expressions.md

[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
