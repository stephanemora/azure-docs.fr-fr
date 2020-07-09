---
title: Fichier include
description: Fichier include
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 07/07/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 837e82131b17fa02fca87da067eac6d77903b31d
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86111652"
---
Ces exemples montrent comment utiliser Azure Policy avec des abonnements qui ont été intégrés à Azure Lighthouse.

| **Modèle** | **Description** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) | Attribue une stratégie qui ajoute ou supprime une étiquette (avec l’effet de modification) dans un abonnement délégué. Pour plus d’informations, consultez [Déployer une stratégie pouvant être corrigée dans un abonnement délégué](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-audit-delegation) | Attribue une stratégie qui effectue un audit pour les attributions de délégation. |
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring) | Attribue une stratégie qui active les diagnostics sur les ressources Azure Key Vault dans un abonnement délégué (avec l’effet deployIfNotExists). Pour plus d’informations, consultez [Déployer une stratégie pouvant être corrigée dans un abonnement délégué](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-sub-monitoring) | Attribue plusieurs stratégies pour activer les diagnostics sur un abonnement délégué, et connecte toutes les machines virtuelles Windows et Linux à l’espace de travail Log Analytics créé par la stratégie. Pour plus d’informations, consultez [Déployer une stratégie pouvant être corrigée dans un abonnement délégué](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-initiative) | Applique une initiative (plusieurs définitions de stratégie associées) à un abonnement délégué. |

