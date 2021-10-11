---
title: Changements importants à venir sur Azure Security Center
description: Changements à venir sur Azure Security Center dont vous devez peut-être tenir compte et pour lesquels une planification peut être nécessaire
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 10/05/2021
ms.author: memildin
ms.openlocfilehash: e6d1c8cf55687a8e4d7612ca432a314ae38256bc
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129534423"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Changements importants à venir sur Azure Security Center

> [!IMPORTANT]
> Les informations de cette page concernent des produits ou fonctionnalités en préversion, qui peuvent être modifiés de façon substantielle avant d’être commercialisés, le cas échéant. Microsoft n’offre aucun engagement ni garantie, formel ou implicite, concernant les informations contenues dans ce document.

Dans cette page, vous allez découvrir les changements planifiés pour Security Center. Elle décrit les changements planifiés du produit qui peuvent avoir un impact sur votre degré de sécurisation ou vos workflows.

Si vous recherchez les notes de publication les plus récentes, vous les trouverez dans [Nouveautés d’Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Changements planifiés

| Modifications planifiées       | Date estimée de la modification |
|----------------------|---------------------------|
| [Dépréciation d’une alerte de préversion : ARM.MCAS_ActivityFromAnonymousIPAddresses](#deprecating-a-preview-alert-armmcas_activityfromanonymousipaddresses)             | Octobre 2021|
| [L’implémentation héritée de la norme ISO 27001 est remplacée par la nouvelle norme ISO 27001:2013](#legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013)| Octobre 2021|
| [Modifications apportées aux suggestions pour la gestion des solutions de protection des points de terminaison](#changes-to-recommendations-for-managing-endpoint-protection-solutions)             | Octobre 2021    |
| [Améliorations apportées à la recommandation pour classer les données sensibles dans les bases de données SQL](#enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases)   | PREMIER TRIMESTRE 2022    |
|||

### <a name="deprecating-a-preview-alert-armmcas_activityfromanonymousipaddresses"></a>Dépréciation d’une alerte de préversion : ARM.MCAS_ActivityFromAnonymousIPAddresses

**Date estimée de la modification :** octobre 2021

Nous allons déprécier l’alerte de préversion suivante :

|Nom de l’alerte| Description|
|----------------------|---------------------------|
|**PRÉVERSION – Activité à partir d’une adresse IP à risque**<br>(ARM.MCAS_ActivityFromAnonymousIPAddresses)|Une activité utilisateur depuis une adresse IP qui a été identifiée comme adresse IP de proxy anonyme a été détectée.<br>Ces proxys sont utilisés par des individus souhaitant masquer l’adresse IP de leur appareil et peuvent être utilisés dans un but malveillant. Cette détection utilise un algorithme Machine Learning qui réduit les faux positifs, tels que les adresses IP mal étiquetées qui sont largement utilisées par d’autres utilisateurs de l’organisation.<br>Requiert une licence active de Microsoft Cloud App Security.|
|||

Nous avons mis au point de nouvelles alertes qui fournissent ces informations et qui les complètent. De plus, l’alerte plus récente (ARM_OperationFromSuspiciousIP, ARM_OperationFromSuspiciousProxyIP) ne nécessite pas de licence pour Microsoft Cloud App Security.

### <a name="legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013"></a>L’implémentation héritée de la norme ISO 27001 est remplacée par la nouvelle norme ISO 27001:2013

**Date estimée de la modification :** octobre 2021

L’implémentation héritée de la norme ISO 27001 sera supprimée du tableau de bord de conformité réglementaire de Security Center. Si vous effectuez le suivi de la conformité ISO 27001 avec Security Center, intégrez la nouvelle norme ISO 27001:2013 pour tous les groupes d’administration ou abonnements appropriés, car la norme ISO 27001 actuelle sera bientôt supprimée du tableau de bord.

:::image type="content" source="media/upcoming-changes/removing-iso-27001-legacy-implementation.png" alt-text="Tableau de bord de conformité réglementaire Security Center présentant le message relatif à la suppression de l’implémentation héritée de la norme ISO 27001." lightbox="media/upcoming-changes/removing-iso-27001-legacy-implementation.png":::

### <a name="changes-to-recommendations-for-managing-endpoint-protection-solutions"></a>Modifications apportées aux suggestions pour la gestion des solutions de protection des points de terminaison

**Date estimée de la modification :** octobre 2021

En août 2021, nous avons ajouté deux suggestions **en préversion** pour déployer et conserver les solutions de protection des points de terminaison sur vos machines. Pour plus d’informations, consultez [la note de publication](release-notes.md#two-new-recommendations-for-managing-endpoint-protection-solutions-in-preview).

Lorsque les suggestions sont publiées en disponibilité générale, elles remplacent les suggestions existantes suivantes :

- **La protection des points de terminaison doit être installée sur vos machines** remplacera :
    - [Installer la solution de protection des points de terminaison sur des machines virtuelles (clé : 83f577bd-a1b6-b7e1-0891-12ca19d1e6df)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/83f577bd-a1b6-b7e1-0891-12ca19d1e6df)
    - [Installer la solution de protection des points de terminaison sur vos machines (clé : 383cf3bc-fdf9-4a02-120a-3e7e36c6bfee)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/383cf3bc-fdf9-4a02-120a-3e7e36c6bfee)

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