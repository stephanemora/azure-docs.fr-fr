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
ms.openlocfilehash: 1c0be8ed8c176fe32b8fe7fd420d65727c5288d2
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986638"
---
Ces exemples montrent comment utiliser Azure Policy avec des abonnements qui ont été intégrés pour la gestion des ressources déléguées Azure.

| **Modèle** | **Description** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) | Attribue une stratégie qui ajoute ou supprime une étiquette (avec l’effet de modification) dans un abonnement délégué. Pour plus d’informations, consultez [Déployer une stratégie pouvant être corrigée dans un abonnement délégué](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-audit-delegation) | Attribue une stratégie qui effectue un audit pour les attributions de délégation. |
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring) | Attribue une stratégie qui active les diagnostics sur les ressources Azure Key Vault dans un abonnement délégué (avec l’effet deployIfNotExists). Pour plus d’informations, consultez [Déployer une stratégie pouvant être corrigée dans un abonnement délégué](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-sub-monitoring) | Attribue plusieurs stratégies pour activer les diagnostics sur un abonnement délégué, et connecte toutes les machines virtuelles Windows et Linux à l’espace de travail Log Analytics créé par la stratégie. Pour plus d’informations, consultez [Déployer une stratégie pouvant être corrigée dans un abonnement délégué](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-initiative) | Applique une initiative (plusieurs définitions de stratégie associées) à un abonnement délégué. |

