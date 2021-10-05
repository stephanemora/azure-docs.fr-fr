---
title: Changements importants à venir sur Azure Security Center
description: Changements à venir sur Azure Security Center dont vous devez peut-être tenir compte et pour lesquels une planification peut être nécessaire
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 09/12/2021
ms.author: memildin
ms.openlocfilehash: 2d78588e900becc8fc802f1e4665b4fe533f0f6a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124766841"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Changements importants à venir sur Azure Security Center

> [!IMPORTANT]
> Les informations de cette page concernent des produits ou fonctionnalités en préversion, qui peuvent être modifiés de façon substantielle avant d’être commercialisés, le cas échéant. Microsoft n’offre aucun engagement ni garantie, formel ou implicite, concernant les informations contenues dans ce document.

Dans cette page, vous allez découvrir les changements planifiés pour Security Center. Elle décrit les changements planifiés du produit qui peuvent avoir un impact sur votre degré de sécurisation ou vos workflows.

Si vous recherchez les notes de publication les plus récentes, vous les trouverez dans [Nouveautés d’Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Changements planifiés

| Modifications planifiées       | Date estimée de la modification |
|----------------------|---------------------------|
| [L’implémentation héritée de la norme ISO 27001 est remplacée par la nouvelle norme ISO 27001:2013](#legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013)| Septembre 2021|
| [Modification du préfixe de certains types d’alerte de « ARM_ » à « VM_ »](#changing-prefix-of-some-alert-types-from-arm_-to-vm_)                                          | Octobre 2021|
| [Modifications apportées aux suggestions pour la gestion des solutions de protection des points de terminaison](#changes-to-recommendations-for-managing-endpoint-protection-solutions)             | Quatrième trimestre 2021    |
| [Améliorations apportées à la recommandation pour classer les données sensibles dans les bases de données SQL](#enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases)   | PREMIER TRIMESTRE 2022    |


### <a name="legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013"></a>L’implémentation héritée de la norme ISO 27001 est remplacée par la nouvelle norme ISO 27001:2013

**Date estimée de la modification :** septembre 2021

L’implémentation héritée de la norme ISO 27001 sera supprimée du tableau de bord de conformité réglementaire de Security Center. Si vous effectuez le suivi de la conformité ISO 27001 avec Security Center, intégrez la nouvelle norme ISO 27001:2013 pour tous les groupes d’administration ou abonnements appropriés, car la norme ISO 27001 actuelle sera bientôt supprimée du tableau de bord.

:::image type="content" source="media/upcoming-changes/removing-iso-27001-legacy-implementation.png" alt-text="Tableau de bord de conformité réglementaire Security Center présentant le message relatif à la suppression de l’implémentation héritée de la norme ISO 27001." lightbox="media/upcoming-changes/removing-iso-27001-legacy-implementation.png":::

### <a name="changing-prefix-of-some-alert-types-from-arm_-to-vm_"></a>Modification du préfixe de certains types d’alerte de « ARM_ » à « VM_ » 

**Date estimée de la modification :** octobre 2021

En juillet 2021, nous avons annoncé une [réorganisation logique d’Azure Defender pour les alertes Resource Manager](release-notes.md#logical-reorganization-of-azure-defender-for-resource-manager-alerts) 

Dans le cadre d’une réorganisation logique de certains des plans Azure Defender, nous avons déplacé vingt et une alertes d’[Azure Defender pour Resource Manager](defender-for-resource-manager-introduction.md) vers [Azure Defender pour les serveurs](defender-for-servers-introduction.md).

Nous envisageons à présent de mettre à jour les préfixes de ces alertes pour qu’elles correspondent à cette réaffectation. Nous allons remplacer « ARM_ » par « VM_ », comme indiqué dans la table suivante.

| Nom actuel                                   | Après cette modification                             |
|------------------------------------------------|-----------------------------------------------|
| ARM_AmBroadFilesExclusion                      | VM_AmBroadFilesExclusion                      |
| ARM_AmDisablementAndCodeExecution              | VM_AmDisablementAndCodeExecution              |
| ARM_AmDisablement                              | VM_AmDisablement                              |
| ARM_AmFileExclusionAndCodeExecution            | VM_AmFileExclusionAndCodeExecution            |
| ARM_AmTempFileExclusionAndCodeExecution        | VM_AmTempFileExclusionAndCodeExecution        |
| ARM_AmTempFileExclusion                        | VM_AmTempFileExclusion                        |
| ARM_AmRealtimeProtectionDisabled               | VM_AmRealtimeProtectionDisabled               |
| ARM_AmTempRealtimeProtectionDisablement        | VM_AmTempRealtimeProtectionDisablement        |
| ARM_AmRealtimeProtectionDisablementAndCodeExec | VM_AmRealtimeProtectionDisablementAndCodeExec |
| ARM_AmMalwareCampaignRelatedExclusion          | VM_AmMalwareCampaignRelatedExclusion          |
| ARM_AmTemporarilyDisablement                   | VM_AmTemporarilyDisablement                   |
| ARM_UnusualAmFileExclusion                     | VM_UnusualAmFileExclusion                     |
| ARM_CustomScriptExtensionSuspiciousCmd         | VM_CustomScriptExtensionSuspiciousCmd         |
| ARM_CustomScriptExtensionSuspiciousEntryPoint  | VM_CustomScriptExtensionSuspiciousEntryPoint  |
| ARM_CustomScriptExtensionSuspiciousPayload     | VM_CustomScriptExtensionSuspiciousPayload     |
| ARM_CustomScriptExtensionSuspiciousFailure     | VM_CustomScriptExtensionSuspiciousFailure     |
| ARM_CustomScriptExtensionUnusualDeletion       | VM_CustomScriptExtensionUnusualDeletion       |
| ARM_CustomScriptExtensionUnusualExecution      | VM_CustomScriptExtensionUnusualExecution      |
| ARM_VMAccessUnusualConfigReset                 | VM_VMAccessUnusualConfigReset                 |
| ARM_VMAccessUnusualPasswordReset               | VM_VMAccessUnusualPasswordReset               |
| ARM_VMAccessUnusualSSHReset                    | VM_VMAccessUnusualSSHReset                    |

En savoir plus sur les plans [Azure Defender pour Resource Manager](defender-for-resource-manager-introduction.md) et [Azure Defender pour les serveurs](defender-for-servers-introduction.md).


### <a name="changes-to-recommendations-for-managing-endpoint-protection-solutions"></a>Modifications apportées aux suggestions pour la gestion des solutions de protection des points de terminaison

**Date estimée de la modification :** quatrième trimestre 2021

En août 2021, nous avons ajouté deux suggestions **en préversion** pour déployer et conserver les solutions de protection des points de terminaison sur vos machines. Pour plus d’informations, consultez [la note de publication](release-notes.md#two-new-recommendations-for-managing-endpoint-protection-solutions-in-preview).

Lorsque les suggestions sont publiées en disponibilité générale, elles remplacent les suggestions existantes suivantes :

- **La protection des points de terminaison doit être installée sur vos machines** remplacera :
    - [Installer la solution de protection de point de terminaison sur les machines virtuelles](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/83f577bd-a1b6-b7e1-0891-12ca19d1e6df)
    - [Installer la solution de protection de point de terminaison sur vos machines](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/383cf3bc-fdf9-4a02-120a-3e7e36c6bfee) 

- **Les problèmes d’intégrité de la protection des points de terminaison doivent être résolus sur vos ordinateurs** remplacera la recommandation existante qui porte le même nom. Les deux suggestions ont des clés d’évaluation différentes :
    - Clé d’évaluation pour la recommandation de la **préversion** : 37a3689a-818e-4a0e-82ac-b1392b9bb000
    - Clé d’évaluation pour la recommandation **GA** : 3bcd234d-c9c7-c2a2-89e0-c01f419c1a8a

En savoir plus :
- [Solutions de protection des points de terminaison prises en charge par Security Center](security-center-services.md#endpoint-supported)
- [Comment ces suggestions évaluent l’état de vos solutions déployées](security-center-endpoint-protection.md)

### <a name="enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases"></a>Améliorations apportées à la recommandation pour classer les données sensibles dans les bases de données SQL

**Date estimée de la modification :** T1 2022

La recommandation **Les données sensibles de vos bases de données SQL doivent être classifiées** du contrôle de sécurité **Appliquer la classification des données** va être remplacée par une nouvelle version mieux alignée sur la stratégie de classification des données de Microsoft. L’ID de la recommandation sera donc également modifié (actuellement, il s’agit de b0df6f56-862d-4730-8597-38c0fd4ebd59).


## <a name="next-steps"></a>Étapes suivantes

Pour toutes les modifications récentes de Security Center, consultez [Nouveautés dans Azure Security Center](release-notes.md)