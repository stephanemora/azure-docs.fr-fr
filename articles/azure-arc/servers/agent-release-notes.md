---
title: Nouveautés de l’agent des serveurs avec Azure Arc (préversion)
description: Cet article contient les notes de publication de l’agent des serveurs avec Azure Arc (préversion). Pour la plupart des problèmes résumés ici, il existe des liens menant à des informations supplémentaires.
ms.topic: conceptual
ms.date: 08/31/2020
ms.openlocfilehash: 0f5060189d6f9cac620b49f414e0e27bed3e356e
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236531"
---
# <a name="whats-new-with-azure-arc-enabled-servers-preview-agent"></a>Nouveautés de l’agent des serveurs avec Azure Arc (préversion)

L’agent Connected Machine des serveurs avec Azure Arc (préversion) reçoit des améliorations de manière continue. Pour vous informer des développements les plus récents, cet article détaille les thèmes suivants :

- Versions les plus récentes
- Problèmes connus
- Résolution des bogues

## <a name="august-2020"></a>Août 2020

Version : 0.11

- Prise en charge d’Ubuntu 20.04.

- Améliorations de la fiabilité des déploiements d’extension.

### <a name="known-issues"></a>Problèmes connus

Si vous utilisez une version antérieure de l’agent Linux et que vous l’avez configurée pour utiliser un serveur proxy, vous devez reconfigurer le paramètre de serveur proxy après la mise à niveau. Pour cela, exécutez `sudo azcmagent_proxy add http://proxyserver.local:83`.

## <a name="next-steps"></a>Étapes suivantes

Avant d’évaluer ou d’activer les serveurs compatibles avec Arc (préversion) sur plusieurs machines hybrides, consultez [Vue d’ensemble de l’agent Machine connectée](agent-overview.md) pour comprendre les exigences, les détails techniques concernant l’agent et les méthodes de déploiement.