---
title: Paramètres de stratégie de pare-feu d’applications web avec Azure porte d’entrée
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
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797461"
---
# <a name="policy-settings-for-web-application-firewall-with-azure-front-door"></a>Paramètres de stratégie de pare-feu d’applications web avec Azure porte d’entrée

Une stratégie de pare-feu d’applications Web (WAF) permet de contrôler l’accès à vos applications web en un ensemble de règles personnalisés et gérés. Le nom de la stratégie de pare-feu d’applications Web doit être unique. Vous recevrez une erreur de validation si vous essayez d’utiliser un nom existant. Il existe plusieurs paramètres de niveau de stratégie qui s’appliquent à toutes les règles spécifiées pour cette stratégie, comme décrit dans cet article.

## <a name="waf-state"></a>État du pare-feu d’applications Web

Une stratégie de pare-feu d’applications Web pour la porte d’entrée peut être dans un des deux états suivants :
- **Activée :** Quand une stratégie est activée, WAF inspecte activement les demandes entrantes et effectue des actions correspondantes conformément aux définitions de règle
- **Désactivé :** : quand une stratégie est désactivée, l’inspection WAF est suspendue. Les demandes entrantes contournent WAF et sont envoyées aux serveurs principaux en fonction de routage de la porte d’entrée.

## <a name="waf-mode"></a>Mode WAF

Stratégie de pare-feu d’applications Web peut être configurée pour s’exécuter dans les deux modes suivants :

- **Mode de détection** lors de l’exécuter en mode de détection, WAF ne pas rien d’autre que de surveiller et enregistrer la demande et sa mise en correspondance règle WAF dans journaux WAF. Activer les diagnostics de journalisation pour la porte d’entrée (à l’aide du portail, cela peut être obtenue en accédant à la **Diagnostics** section dans le portail Azure).

- **Mode de prévention** lorsque configuré pour s’exécuter en mode de prévention, effectuées par WAF l’action spécifiée si une demande correspond à une règle. Toutes les demandes de mise en correspondance sont également enregistrés dans les journaux WAF.

## <a name="waf-response-for-blocked-requests"></a>Réponse de WAF pour les demandes bloquées

Par défaut, lorsque WAF bloque une demande en raison d’une règle de mise en correspondance, elle retourne un code de 403 état avec - **la demande est bloquée** message. Une chaîne de référence est également retournée pour la journalisation.

Lorsqu’une demande est bloquée par le pare-feu d’applications Web, vous pouvez définir un code d’état de réponse personnalisée et un message de réponse. Codes d’état personnalisés suivants sont pris en charge :

- 200    OK
- 403    Forbidden
- 405 Méthode non autorisée
- 406 Non acceptable
- 429 trop grand nombre de demandes

Message d’état réponse personnalisée code et de réponse est un paramètre de niveau de stratégie. Une fois qu’il est configuré, toutes les demandes bloquées Obtient le même état de réponse personnalisée et un message de réponse.

## <a name="uri-for-redirect-action"></a>URI d’action de redirection

Vous êtes invité à définir un URI pour rediriger les demandes vers if le **rediriger** action est sélectionnée pour toutes les règles contenues dans une stratégie de pare-feu d’applications Web. Cette redirection URI doit être un site HTTP (S) valide et une fois configuré, toutes les demandes des règles de correspondance avec une action « Redirection » seront redirigés vers le site spécifié.


## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment définir WAF [réponses personnalisées](waf-front-door-configure-custom-response-code.md)
