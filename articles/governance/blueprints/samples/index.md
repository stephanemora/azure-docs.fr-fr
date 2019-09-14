---
title: Index des exemples de blueprints
description: Index des exemples d’environnement standard et de conformité pour le déploiement avec Azure Blueprints.
author: DCtheGeek
manager: carmonm
ms.service: blueprints
ms.topic: sample
ms.date: 09/04/2019
ms.author: dacoulte
ms.custom: fasttrack-edit
ms.openlocfilehash: ea2c56e35741a49d057f2261f7b53fb754a43f8b
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70967981"
---
# <a name="azure-blueprints-samples"></a>Exemples Azure Blueprints

Le tableau suivant inclut des liens vers des exemples pour Azure Blueprints. Chaque exemple est de qualité production et prêt à être déployé aujourd’hui pour vous aider à répondre à vos divers besoins de conformité.

## <a name="standards-based-blueprint-samples"></a>Exemples de blueprints basés sur des normes

|  |  |
|---------|---------|
| [PBMM fédéral du Canada](./canada-federal-pbmm/index.md) | Propose des garde-fous pour la conformité au profil PBMN (Protected B, Medium Integrity, Medium Availability) fédéral du Canada. |
| [CIS Microsoft Azure Foundations Benchmark](./cis-azure-1.1.0/index.md)| Fournit un ensemble de stratégies pour vous aider à vous conformer aux recommandations de CIS Microsoft Azure Foundations Benchmark. |
| [IRS 1075](./irs-1075/index.md) | Propose des garde-fous pour la conformité à la norme IRS 1075.|
| [ISO 27001](./iso27001/index.md) | Propose des garde-fous pour la conformité à la norme ISO 27001. |
| [Services partagés ISO 27001](./iso27001-shared/index.md) | Fournit un ensemble de modèles d’infrastructure conformes et de garde-fou stratégiques qui facilitent l’attestation ISO 27001. |
| [Charge de travail App Service Environment/SQL Database ISO 27001](./iso27001-ase-sql-workload/index.md) | Fournit une infrastructure supplémentaire à l’exemple de blueprint [Services partagés ISO 27001](./iso27001-shared/index.md). |
| [NIST SP 800-53 R4](./nist-sp-800-53-rev4/index.md) | Propose des rails de sécurité pour la conformité à NIST SP 800-53 R4. |
| [PCI-DSS v3.2.1](./pci-dss-3.2.1/index.md) | Fournit un ensemble de stratégies pour la mise en conformité à PCI-DSS v3.2.1. |
| [Gouvernance de UK OFFICIAL et de UK NHS](./ukofficial/index.md) | Fournit un ensemble de modèles d’infrastructure conformes et de garde-fous stratégiques qui facilitent l’attestation UK OFFICIAL et UK NHS. |
| [Base du framework d’adoption du cloud](./caf-foundation/index.md) | Fournit un ensemble de contrôles destinés à vous aider à gérer vos ressources cloud en adéquation avec le [Framework d’adoption de Microsoft Cloud pour Azure](/azure/architecture/cloud-adoption/governance/journeys/index). |
| [Zone d’accueil de migration du framework d’adoption du cloud](./caf-migrate-landing-zone/index.md) | Fournit un ensemble de contrôles destinés à vous aider à configurer la migration de votre première charge de travail et à gérer vos ressources cloud en adéquation avec le [Framework d’adoption de Microsoft Cloud pour Azure](/azure/architecture/cloud-adoption/migrate/index). |

## <a name="samples-strategy"></a>Stratégie d’exemples

![Stratégie d’exemples de blueprint](../media/blueprint-samples-strategy.png)

Les blueprints de base et de zone d’accueil de migration du framework d’adoption du cloud partent du principe que le client prépare un abonnement propre existant pour la migration de ressources et charges de travail locales vers Azure
(régions A et B dans la figure ci-dessus).  

Vous avez la possibilité d’effectuer une itération sur les exemples de blueprint et de rechercher des modèles de personnalisations qu’un client applique. Vous avez également la possibilité d’aborder de manière proactive des blueprints spécifiques d’un secteur, par exemple, ceux des services financiers et du commerce électronique (extrémité supérieure de la région B). De même, nous envisageons de créer des blueprints répondant à des considérations architecturales complexes, telles que des abonnements multiples, la haute disponibilité, des ressources interrégionales et des clients qui appliquent des contrôles à des abonnements et ressources existants (régions C et D).

Il existe des exemples de blueprints qui répondent à un scénario de client où les exigences de conformité et les complexités architecturales sont élevées (région E de la figure ci-dessus). La région F ci-dessus sera utilisée par les clients et partenaires qui tirent parti des exemples de blueprints et les personnalisent pour leurs besoins spécifiques.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le [cycle de vie des blueprints](../concepts/lifecycle.md)
- Comprendre comment utiliser les [paramètres statiques et dynamiques](../concepts/parameters.md).
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](../concepts/sequencing-order.md).
- Découvrir comment utiliser le [verrouillage de ressources de blueprint](../concepts/resource-locking.md).
- Découvrir comment [mettre à jour des affectations existantes](../how-to/update-existing-assignments.md).
- Résoudre les problèmes durant l’affectation d’un blueprint en suivant les étapes de [dépannage général](../troubleshoot/general.md).