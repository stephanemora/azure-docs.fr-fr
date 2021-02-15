---
title: Contrôles de conformité réglementaire d’Azure Policy pour Recherche cognitive Azure
description: Liste les contrôles de conformité réglementaire d’Azure Policy pour Recherche cognitive Azure. Ces définitions de stratégie intégrées fournissent des approches courantes pour la gestion de la conformité de vos ressources Azure.
ms.date: 02/09/2021
ms.topic: sample
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.custom: subject-policy-compliancecontrols
ms.openlocfilehash: ac1723b40544ef9420b7d9d780b598275cfa0167
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100095957"
---
# <a name="azure-policy-regulatory-compliance-controls-for-azure-cognitive-search"></a>Contrôles de conformité réglementaire d’Azure Policy pour Recherche cognitive Azure

Si vous utilisez [Azure Policy](../governance/policy/overview.md) pour appliquer les recommandations du [benchmark de sécurité Azure](../security/benchmarks/introduction.md), vous savez probablement déjà que vous pouvez créer des stratégies pour identifier et corriger les services non conformes. Ces stratégies peuvent être personnalisées ou basées sur des définitions intégrées qui fournissent des critères de conformité et des solutions appropriées s’inscrivant dans les bonnes pratiques bien définies.

Pour le service Recherche cognitive Azure, il existe actuellement une définition intégrée, indiquée ci-dessous, que vous pouvez utiliser dans une attribution de stratégie. Elle est destinée à la journalisation et la supervision. Quand vous utilisez cette définition intégrée dans une [stratégie que vous créez](../governance/policy/assign-policy-portal.md), le système recherche les services de recherche qui n’ont pas de [journalisation des diagnostics](search-monitor-logs.md), puis l’active en conséquence.

La [conformité réglementaire Azure Policy](../governance/policy/concepts/regulatory-compliance.md) fournit des définitions d’initiatives créées et gérées par Microsoft, qui sont dites _intégrées_, pour les **domaines de conformité** et les **contrôles de sécurité** associés à différents standards de conformité. Cette page liste les **domaines de conformité** et les **contrôles de sécurité** pour Recherche cognitive Azure. Vous pouvez affecter les initiatives intégrées pour un **contrôle de sécurité** individuellement, pour rendre vos ressources Azure conformes au standard spécifique.

[!INCLUDE [azure-policy-compliancecontrols-introwarning](../../includes/policy/standards/intro-warning.md)]

[!INCLUDE [azure-policy-compliancecontrols-search](../../includes/policy/standards/byrp/microsoft.search.md)]

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur la [Conformité réglementaire d’Azure Policy](../governance/policy/concepts/regulatory-compliance.md).
- Consultez les définitions intégrées dans le [dépôt Azure Policy de GitHub](https://github.com/Azure/azure-policy).
