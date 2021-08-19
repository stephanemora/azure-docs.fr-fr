---
title: Changements importants à venir sur Azure Security Center
description: Changements à venir sur Azure Security Center dont vous devez peut-être tenir compte et pour lesquels une planification peut être nécessaire
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 07/25/2021
ms.author: memildin
ms.openlocfilehash: 2770c3532dd83051f9c2c7dcc770850e85aeae4b
ms.sourcegitcommit: 63f3fc5791f9393f8f242e2fb4cce9faf78f4f07
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2021
ms.locfileid: "114689583"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Changements importants à venir sur Azure Security Center

> [!IMPORTANT]
> Les informations de cette page concernent des produits ou fonctionnalités en préversion, qui peuvent être modifiés de façon substantielle avant d’être commercialisés, le cas échéant. Microsoft n’offre aucun engagement ni garantie, formel ou implicite, concernant les informations contenues dans ce document.

Dans cette page, vous allez découvrir les changements planifiés pour Security Center. Elle décrit les changements planifiés du produit qui peuvent avoir un impact sur votre degré de sécurisation ou vos workflows.

Si vous recherchez les notes de publication les plus récentes, vous les trouverez dans [Nouveautés d’Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Changements planifiés

| Modifications planifiées                                                                                                                                                                                          | Date estimée de la modification |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------|
| [L’implémentation héritée de la norme ISO 27001 est remplacée par la nouvelle norme ISO 27001:2013](#legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013)                                            | Juillet 2021                 |
| [Dépréciation de la recommandation « Les problèmes d’intégrité de l’agent Log Analytics doivent être résolus sur vos machines »](#deprecating-recommendation-log-analytics-agent-health-issues-should-be-resolved-on-your-machines) | Juillet 2021                 |
| [Réorganisation logique d’Azure Defender pour les alertes Gestionnaire des ressources](#logical-reorganization-of-azure-defender-for-resource-manager-alerts)                                                           | Août 2021               |
| [Exportations CSV à limiter à 20 Mo](#csv-exports-to-be-limited-to-20-mb)                                                                                                                               | Août 2021               |
| [Activer le contrôle de sécurité Azure Defender à inclure au degré de sécurisation](#enable-azure-defender-security-control-to-be-included-in-secure-score)                                                         | T3 2021                   |
| [Améliorations apportées à la recommandation pour classer les données sensibles dans les bases de données SQL](#enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases)                                               | PREMIER TRIMESTRE 2022                   ||                                                                                                                                                                                                         |                           |


### <a name="legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013"></a>L’implémentation héritée de la norme ISO 27001 est remplacée par la nouvelle norme ISO 27001:2013

**Date estimée de la modification :** juillet 2021

L’implémentation héritée de la norme ISO 27001 sera supprimée du tableau de bord de conformité réglementaire de Security Center. Si vous effectuez le suivi de la conformité ISO 27001 avec Security Center, intégrez la nouvelle norme ISO 27001:2013 pour tous les groupes d’administration ou abonnements appropriés, car la norme ISO 27001 actuelle sera bientôt supprimée du tableau de bord.

:::image type="content" source="media/upcoming-changes/removing-iso-27001-legacy-implementation.png" alt-text="Tableau de bord de conformité réglementaire Security Center présentant le message relatif à la suppression de l’implémentation héritée de la norme ISO 27001." lightbox="media/upcoming-changes/removing-iso-27001-legacy-implementation.png":::

### <a name="deprecating-recommendation-log-analytics-agent-health-issues-should-be-resolved-on-your-machines"></a>Dépréciation de la recommandation « Les problèmes d’intégrité de l’agent Log Analytics doivent être résolus sur vos machines »

**Date estimée de la modification :** juillet 2021

Nous avons constaté que la recommandation **Les problèmes d’intégrité de l’agent Log Analytics doivent être résolus sur vos machines** influe sur les scores de sécurité de manière incohérente avec le focus CSPM (Cloud Security Posture Management) de Security Center. En règle générale, CSPM est associé à l’identification des erreurs de configuration de sécurité. Les problèmes d’intégrité de l’agent n’entrent pas dans cette catégorie de problèmes.

En outre, la recommandation est une anomalie par rapport aux autres agents liés à Security Center : il s’agit du seul agent présentant une recommandation relative aux problèmes d’intégrité.

Cette recommandation sera dépréciée.

À la suite de cette dépréciation, nous allons également apporter des modifications mineures aux recommandations relatives à l’installation de l’agent de Log Analytics (**L’agent Log Analytics doit être installé sur...** ).

Il est probable que cette modification influe sur vos scores de sécurité. Pour la plupart des abonnements, nous pensons que cette modification se traduira par un score plus élevé, mais il est possible que les mises à jour apportées à la recommandation d’installation se traduisent par un score moins élevé dans certains cas.

> [!TIP]
> La page [Inventaire des ressources](asset-inventory.md) est également concernée par cette modification, car elle affiche aussi des informations indiquant si une machine est surveillée, non surveillée ou partiellement surveillée (un état qui fait référence à un agent avec problèmes d’intégrité). 


### <a name="logical-reorganization-of-azure-defender-for-resource-manager-alerts"></a>Réorganisation logique d’Azure Defender pour les alertes Gestionnaire des ressources

**Date estimée de la modification :** Août 2021

Les alertes énumérées ci-dessous sont actuellement fournies dans le cadre du plan [Azure Defender pour Resource Manager](defender-for-resource-manager-introduction.md).

Dans le cadre d’une réorganisation logique de certains des plans Azure Defender, nous déplaçons certaines alertes d’**Azure Defender pour Resource Manager** à **Azure Defender pour les serveurs**.

Les alertes sont organisées en fonction de deux principes essentiels :

- Les alertes qui fournissent une protection de plan de contrôle, sur de nombreux types de ressources Azure, feront partie d’Azure Defender pour Resource Manager
- Les alertes qui protègent des charges de travail spécifiques seront déplacées vers le plan Azure Defender correspondant associé à cette charge de travail

Voici les alertes qui font actuellement partie d’Azure Defender pour Resource Manager, et qui, suite à cette modification, seront déplacées vers Azure Defender pour les serveurs :

- ARM_AmBroadFilesExclusion
- ARM_AmDisablementAndCodeExecution
- ARM_AmDisablement
- ARM_AmFileExclusionAndCodeExecution
- ARM_AmTempFileExclusionAndCodeExecution
- ARM_AmTempFileExclusion
- ARM_AmRealtimeProtectionDisabled
- ARM_AmTempRealtimeProtectionDisablement
- ARM_AmRealtimeProtectionDisablementAndCodeExec
- ARM_AmMalwareCampaignRelatedExclusion
- ARM_AmTemporarilyDisablement
- ARM_UnusualAmFileExclusion
- ARM_CustomScriptExtensionSuspiciousCmd
- ARM_CustomScriptExtensionSuspiciousEntryPoint
- ARM_CustomScriptExtensionSuspiciousPayload
- ARM_CustomScriptExtensionSuspiciousFailure
- ARM_CustomScriptExtensionUnusualDeletion
- ARM_CustomScriptExtensionUnusualExecution
- ARM_VMAccessUnusualConfigReset
- ARM_VMAccessUnusualPasswordReset
- ARM_VMAccessUnusualSSHReset

En savoir plus sur [Azure Defender pour Resource Manager](defender-for-resource-manager-introduction.md) et [Azure Defender pour les serveurs](defender-for-servers-introduction.md).


### <a name="csv-exports-to-be-limited-to-20-mb"></a>Exportations CSV à limiter à 20 Mo

**Date estimée de la modification :** Août 2021

Lors de l’exportation des données de recommandations Security Center, il n’existe actuellement aucune limite quant au volume de données que vous pouvez télécharger.

:::image type="content" source="media/upcoming-changes/download-csv-report.png" alt-text="Bouton « Télécharger le rapport CSV » de Security Center pour exporter les données de recommandation.":::

Avec cette modification, nous instaurons une limite de 20 Mo.

S’il vous faut exporter de plus grands volumes de données, utilisez les filtres disponibles avant sélection ou sélectionnez des sous-ensembles de vos abonnements et téléchargez les données par lots.

:::image type="content" source="media/upcoming-changes/filter-subscriptions.png" alt-text="Filtrage des abonnements dans le portail Azure.":::

Apprenez-en davantage sur [l’exécution d’une exportation CSV de vos recommandations de sécurité](continuous-export.md#manual-one-time-export-of-alerts-and-recommendations).

### <a name="enable-azure-defender-security-control-to-be-included-in-secure-score"></a>Activer le contrôle de sécurité Azure Defender à inclure au degré de sécurisation

**Date estimée de la modification :** T3 2021

Les recommandations de renforcement de Security Center sont regroupées en contrôles de sécurité. Chaque contrôle est un groupe logique de recommandations de sécurité associées et reflète les surfaces d'attaque vulnérables. La contribution de chaque contrôle de sécurité au degré de sécurisation global est indiquée clairement sur la page de recommandations, ainsi que dans la liste des contrôles dans [Contrôles de sécurité et leurs recommandations](secure-score-security-controls.md#security-controls-and-their-recommendations).

Depuis son introduction, le contrôle **Activer Azure Defender** a obtenu un score maximal possible de 0 point. **Avec cette modification, le contrôle contribuera à votre degré de sécurisation**.

Lorsque vous activez Azure Defender, vous étendez les fonctionnalités du mode Gratuit de Security Center à vos charges de travail exécutées dans des clouds privés et dans d’autres clouds publics, pour une gestion unifiée de la sécurité et une protection contre les menaces sur l’ensemble des charges de travail de cloud hybride. Voici quelques-unes des principales fonctionnalités d’Azure Defender : licences intégrées Microsoft Defender pour point de terminaison pour vos serveurs, analyse des vulnérabilités pour les machines virtuelles et les registres de conteneurs, alertes de sécurité basées sur les analyses comportementales avancées et Machine Learning, ainsi que les fonctionnalités de sécurité des conteneurs. Pour obtenir la liste exhaustive, consultez [Azure Security Center gratuit et Azure Defender activé](security-center-pricing.md).

Cette modification aura un impact sur le degré de sécurisation de tous les abonnements qui ne sont pas protégés par Azure Defender. Nous vous suggérons d’activer Azure Defender avant cette modification pour vous assurer qu’il n’y a aucun impact sur le vôtre. 

Pour plus d’informations, consultez [Démarrage rapide : Activer Azure Defender](enable-azure-defender.md).

### <a name="enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases"></a>Améliorations apportées à la recommandation pour classer les données sensibles dans les bases de données SQL

**Date estimée de la modification :** T1 2022

La recommandation **Les données sensibles de vos bases de données SQL doivent être classifiées** du contrôle de sécurité **Appliquer la classification des données** va être remplacée par une nouvelle version mieux alignée sur la stratégie de classification des données de Microsoft. L’ID de la recommandation sera donc également modifié (actuellement, il s’agit de b0df6f56-862d-4730-8597-38c0fd4ebd59).


## <a name="next-steps"></a>Étapes suivantes

Pour tous les changements récents du produit, consultez [Nouveautés d’Azure Security Center](release-notes.md).