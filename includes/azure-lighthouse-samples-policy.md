---
title: Fichier Include
description: Fichier Include
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/19/2019
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 55eee87ed1e07b61a7f8287e86ddc0a7a6aa4df9
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2020
ms.locfileid: "75456777"
---
Ces exemples montrent comment utiliser Azure Policy avec des abonnements qui ont été intégrés pour la gestion des ressources déléguées Azure.

| **Modèle** | **Description** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-add-or-replace-tag) | Attribue une stratégie qui ajoute ou supprime une étiquette (avec l’effet de modification) dans un abonnement délégué. Pour plus d’informations, consultez [Déployer une stratégie pouvant être corrigée dans un abonnement délégué](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-audit-delegation) | Attribue une stratégie qui effectue un audit pour les attributions de délégation. |
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring) | Attribue une stratégie qui active les diagnostics sur les ressources Azure Key Vault dans un abonnement délégué (avec l’effet deployIfNotExists). Pour plus d’informations, consultez [Déployer une stratégie pouvant être corrigée dans un abonnement délégué](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-sub-monitoring) | Attribue plusieurs stratégies pour activer les diagnostics sur un abonnement délégué, et connecte toutes les machines virtuelles Windows et Linux à l’espace de travail Log Analytics créé par la stratégie. Pour plus d’informations, consultez [Déployer une stratégie pouvant être corrigée dans un abonnement délégué](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-initiative) | Applique une initiative (plusieurs définitions de stratégie associées) à un abonnement délégué. |

