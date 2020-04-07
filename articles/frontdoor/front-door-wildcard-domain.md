---
title: Azure Front Door - Prise en charge des domaines génériques
description: Cet article vous aide à comprendre la prise en charge par Azure Front Door du mappage et de la gestion des domaines génériques dans la liste des domaines personnalisés
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2020
ms.author: sharadag
ms.openlocfilehash: c568c9cc5c57098385cc7399459ec656cdbfc305
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79540961"
---
# <a name="wildcard-domains"></a>Domaines génériques

En dehors des domaines et sous-domaines apex, vous pouvez également mapper un nom de domaine générique à la liste d’hôtes frontend ou de domaines personnalisés de votre profil Front Door. Le fait d’avoir des domaines génériques dans votre configuration Front Door simplifie le comportement du routage du trafic de plusieurs sous-domaines pour une API, une application ou un site web à partir d’une même règle d’acheminement, sans avoir à modifier la configuration pour ajouter et/ou spécifier chaque sous-domaine séparément. Par exemple, vous pouvez définir le routage pour `customer1.contoso.com`, `customer2.contoso.com` et `customerN.contoso.com` à l’aide de la même règle d’acheminement en ajoutant un domaine générique `*.contoso.com`.

Voici les principaux scénarios résolus avec la prise en charge des domaines génériques :

- Il n’est plus nécessaire d’intégrer chaque sous-domaine sur votre instance Front Door puis d’activer HTTPS pour lier un certificat à chaque sous-domaine.
- Si une application ajoute un nouveau sous-domaine, vous n’avez plus besoin de changer votre configuration Front Door de production. Auparavant, il fallait ajouter le sous-domaine, lier un certificat, attacher une stratégie de pare-feu d’applications web, ajouter le domaine à des règles d’acheminement différentes.

> [!NOTE]
> Actuellement, les domaines génériques sont pris en charge uniquement via l’API, PowerShell et l’interface CLI. La prise en charge de l’ajout de la gestion de domaines génériques via le portail Azure n’est pas disponible.

## <a name="adding-wildcard-domains"></a>Ajout de domaines génériques

Vous pouvez intégrer un domaine générique sous la section Hôtes frontend ou Domaines. Comme pour les sous-domaines, Front Door vérifie qu’il existe également un mappage CNAME pour votre domaine générique. Ce mappage DNS peut être un mappage CNAME direct tel que `*.contoso.com` mappé à `contoso.azurefd.net` ou via le mappage afdverify temporaire tel que `afdverify.contoso.com` mappé à `afdverify.contoso.azurefd.net` qui vérifie également le mappage CNAME pour le domaine générique (Azure DNS prend en charge les enregistrements génériques).

Vous pouvez également ajouter autant de sous-domaines à un seul niveau du domaine générique dans les hôtes frontend, dans la limite du nombre maximal d’hôtes frontend. Cette fonctionnalité peut être requise pour définir un itinéraire pour un sous-domaine différent des autres domaines (à partir du domaine générique) ou pour avoir une stratégie de pare-feu d’applications web différente pour un sous-domaine spécifique. Ainsi, `*.contoso.com` autorise l’ajout de `foo.contoso.com` sans vérifier de nouveau la propriété du domaine, mais pas `foo.bar.contoso.com`, car il ne s’agit pas d’un sous-domaine à un seul niveau de `*.contoso.com`. Pour ajouter `foo.bar.contoso.com` sans validation supplémentaire de la propriété du domaine, il faut ajouter `*.bar.contosonews.com`.

### <a name="limitations"></a>Limites

1. Si un domaine générique est ajouté à un profil Front Door donné, il ne peut pas être ajouté à un autre profil Front Door. 
2. Si un domaine générique est ajouté à un profil Front Door donné, tous les sous-domaines associés ne peuvent pas être ajoutés à un autre profil Front Door ou Azure CDN de Microsoft
3. Si un sous-domaine ou un domaine générique est ajouté à un profil Front Door ou un profil Azure CDN de Microsoft, le domaine générique ne peut pas être ajouté à un autre profil Front Door. 
4. Si deux profils (Front Door ou Azure CDN de Microsoft) possèdent plusieurs sous-domaines d’un domaine racine, les domaines génériques ne peuvent être ajoutés à aucun des deux profils.

## <a name="certificate-binding-for-wildcard-domains-and-its-subdomains"></a>Liaison de certificat pour un domaine générique et ses sous-domaines

Pour accepter le trafic HTTPS sur votre domaine générique, vous devez activer HTTPS sur le domaine générique. La liaison de certificat pour le domaine générique nécessite un certificat générique, c’est-à-dire que le nom de l’objet du certificat doit également comporter le domaine générique.

> [!NOTE]
> Actuellement, seule l’option consistant à utiliser votre propre certificat SSL personnalisé est disponible pour activer HTTPS pour les domaines génériques. Les certificats Front Door gérés ne peuvent pas être utilisés pour les domaines génériques. 

Vous pouvez choisir d’utiliser le même certificat générique que celui de votre coffre de clés pour les sous-domaines, ou utiliser des certificats gérés Front Door pour les sous-domaines.
Si un sous-domaine est ajouté pour un domaine générique et que celui-ci possède déjà un certificat associé, HTTPS ne peut pas être désactivé pour ce sous-domaine. Le sous-domaine utilise par défaut la liaison de certificat du domaine générique, sauf s’il est remplacé par un autre certificat Key Vault ou un certificat géré Front Door.

## <a name="web-application-firewall-for-wildcard-domains-and-its-subdomains"></a>Pare-feu d’applications web pour un domaine générique et ses sous-domaines

Les stratégies de pare-feu d’applications web peuvent être attachées à un domaine générique similaire aux autres domaines. Une stratégie de pare-feu d’applications web peut être appliquée au sous-domaine d’un domaine générique. Pour les sous-domaines, vous devez spécifier explicitement la stratégie de pare-feu d’applications web à utiliser, même s’il s’agit de la même stratégie que celle du domaine générique. Les sous-domaines n’héritent **pas** automatiquement de la stratégie de pare-feu d’applications web du domaine générique.

Si, dans votre scénario, vous ne souhaitez pas que le pare-feu d’applications web s’exécute pour un sous-domaine, vous pouvez créer une stratégie de pare-feu d’applications web vide, sans ensemble de règles géré ou personnalisé.

## <a name="routing-rules-for-wildcard-domains-and-its-subdomains"></a>Règles d’acheminement pour un domaine générique et ses sous-domaines

Lors de la configuration d’une règle d’acheminement, vous pouvez sélectionner un domaine générique comme hôte frontend. Vous pouvez également avoir un comportement de routage différent pour le domaine générique et pour les sous-domaines. Comme le décrit l’article [Fonctionnement des correspondances d’itinéraires Front Door](front-door-route-matching.md), la correspondance la plus spécifique pour le domaine entre différentes règles d’acheminement sera choisie au moment de l’exécution.

> [!WARNING]
> Si vous avez deux règles d’acheminement, par exemple **Itinéraire 1** : `*.foo.com/*` mappé au pool principal A et **Itinéraire 2** : `bar.foo.com/somePath/*` mappé au pool principal B, et si une requête arrive pour `bar.foo.com/anotherPath/*`, vos clients obtiennent un échec car l’instance Front Door ne trouve pas de correspondance dans ces itinéraires. Cela est dû au fait que, selon notre [algorithme de correspondance d’itinéraires](front-door-route-matching.md), l’instance Front Door sélectionne Itinéraire 2 en fonction d’une correspondance de domaine plus spécifique, mais uniquement pour découvrir qu’il n’existe aucun modèle de chemin d’accès correspondant. 


## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [créer une porte d’entrée](quickstart-create-front-door.md).
- Découvrez comment [ajouter un domaine personnalisé à une instance Front Door](front-door-custom-domain.md).
- Découvrez [comment activer le protocole HTTPS pour un domaine personnalisé](front-door-custom-domain-https.md).
