---
title: Paramètres de stratégie pour le pare-feu d’applications web avec Azure Front Door
description: Découvrez le pare-feu d’applications web (WAF).
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: tyao;kumud
ms.openlocfilehash: 4c2f070e9b3c972f063008df8880b196ddb069cc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61459366"
---
# <a name="policy-settings-for-web-application-firewall-with-azure-front-door"></a>Paramètres de stratégie pour le pare-feu d’applications web avec Azure Front Door

Une stratégie de pare-feu d’applications web (WAF) permet de contrôler l’accès à vos applications web en un ensemble de règles personnalisées et gérées. Le nom de la stratégie WAF doit être unique. Vous recevrez une erreur de validation si vous essayez d’utiliser un nom existant. Il existe plusieurs paramètres de niveau de stratégie qui s’appliquent à toutes les règles spécifiées pour cette stratégie, comme décrit dans cet article.

## <a name="waf-state"></a>État du pare-feu d’applications web

Une stratégie WAF pour la porte d’entrée peut être dans l’un des deux états suivants :
- **Activée :** Quand une stratégie est activée, WAF inspecte activement les demandes entrantes et effectue les actions correspondantes conformément aux définitions des règles
- **Désactivé :** : quand une stratégie est désactivée, l’inspection WAF est suspendue. Les demandes entrantes contournent le pare-feu d’applications web et sont envoyées aux serveurs principaux en fonction de routage de porte d’entrée.

## <a name="waf-mode"></a>Mode WAF

Une stratégie AWAF peut être configurée pour s’exécuter dans les deux modes suivants :

- **Mode de détection** :lors d’une exécution en mode de détection, le pare-feu d’applications web n’entreprend aucune autre action à part la surveillance et l’enregistrement de la demande et la mise en correspondance de la règle WAF dans les journaux WAF. Activez les diagnostics de journalisation pour la porte d’entrée (en utilisant le portail, cela peut être obtenu en accédant à la section **Diagnostics** dans le portail Azure).

- **Mode de prévention** : une fois configuré pour s’exécuter en mode de prévention, le pare-feu d’applications web entreprend l’action spécifiée si une demande correspond à une règle. Toutes les demandes mises en correspondance sont également enregistrées dans les journaux WAF.

## <a name="waf-response-for-blocked-requests"></a>Réponse de WAF pour les demandes bloquées

Par défaut, lorsque WAF bloque une demande en raison d’une règle mise en correspondance, elle retourne un code d’état 403 avec le message **La demande est bloquée**. Une chaîne de référence est également retournée pour la journalisation.

Vous pouvez définir un code d’état de réponse et un message de réponse personnalisés lorsqu’une demande est bloquée par le pare-feu d’applications web. Les codes d’état personnalisés suivants sont pris en charge :

- 200    OK
- 403    Interdit
- 405    Méthode non autorisée
- 406    Non acceptable
- 429    Trop de requêtes

Le code d’état de réponse et le message de réponse personnalisés constituent un paramètre au niveau de la stratégie. Une fois configuré, toutes les demandes bloquées obtiennent le même état de réponse et message de réponse personnalisés.

## <a name="uri-for-redirect-action"></a>URI pour une action de redirection

Vous êtes invité à définir un URI vers lequel rediriger les demandes si l’action **REDIRECT** est sélectionnée pour n’importe laquelle des règles contenues dans une stratégie de pare-feu d’applications Web. Cet URI de redirection doit être un site HTTP(S) valide et une fois configuré, toutes les demandes correspondant à des règles avec une action « REDIRECT » seront redirigées vers le site spécifié.


## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment définir des [réponses personnalisées](waf-front-door-configure-custom-response-code.md) du pare-feu d’applications web
