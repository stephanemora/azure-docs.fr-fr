---
title: Changements importants à venir sur Azure Security Center
description: Changements à venir sur Azure Security Center dont vous devez peut-être tenir compte et pour lesquels une planification peut être nécessaire
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 05/09/2021
ms.author: memildin
ms.openlocfilehash: ed9ea3abf984f537ab693ccbadb90e2ba091f52d
ms.sourcegitcommit: b35c7f3e7f0e30d337db382abb7c11a69723997e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109683566"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Changements importants à venir sur Azure Security Center

> [!IMPORTANT]
> Les informations de cette page concernent des produits ou fonctionnalités en préversion, qui peuvent être modifiés de façon substantielle avant d’être commercialisés, le cas échéant. Microsoft n’offre aucun engagement ni garantie, formel ou implicite, concernant les informations contenues dans ce document.

Dans cette page, vous allez découvrir les changements planifiés pour Security Center. Elle décrit les changements planifiés du produit qui peuvent avoir un impact sur votre degré de sécurisation ou vos workflows.

Si vous recherchez les notes de publication les plus récentes, vous les trouverez dans [Nouveautés d’Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Changements planifiés

| Modifications planifiées                                                                                                                                                        | Date estimée de la modification |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------|
| [Deux recommandations du contrôle de sécurité « Appliquer les mises à jour système » bientôt dépréciées](#two-recommendations-from-apply-system-updates-security-control-being-deprecated) | Avril 2021                |
| [Préfixe des alertes Kubernetes passant de « AKS_ » à « K8s_ »](#prefix-for-kubernetes-alerts-changing-from-aks_-to-k8s_)                                               | Juin 2021                 |
| [L’implémentation héritée de la norme ISO 27001 est remplacée par la nouvelle norme ISO 27001:2013](#legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013)          | Juin 2021                 |
| [Les recommandations d’AWS seront publiées pour la disponibilité générale (GA)](#recommendations-from-aws-will-be-released-for-general-availability-ga)                     | **Août** 2021           |
| [Améliorations apportées à la recommandation de classification des données SQL](#enhancements-to-sql-data-classification-recommendation)                                                     | T2 2021                   |
|                                                                                                                                                                       |                           |


### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Deux recommandations du contrôle de sécurité « Appliquer les mises à jour système » bientôt dépréciées

**Date estimée de la modification :** Avril 2021

Les deux recommandations suivantes sont déconseillées :

- **La version du système d'exploitation (SE) doit être mise à jour pour vos rôles service cloud**. Par défaut, Azure met régulièrement à jour votre SE invité vers la dernière image prise en charge d’un produit de la famille de SE que vous avez spécifiée dans votre configuration de service (.cscfg), tel que Windows Server 2016.
- **Les services Kubernetes doivent être mis à niveau vers une version Kubernetes non vulnérable**. Les évaluations de cette recommandation ne sont pas aussi étendues que nous le voulions. La version actuelle de cette recommandation sera finalement remplacée par une version améliorée qui est mieux alignée sur les besoins de sécurité de nos clients.


### <a name="prefix-for-kubernetes-alerts-changing-from-aks_-to-k8s_"></a>Préfixe des alertes Kubernetes passant de « AKS_ » à « K8s_ »

**Date estimée de la modification :** juin 2021

Azure Defender pour Kubernetes s’est récemment développé pour protéger les clusters Kubernetes hébergés localement et dans plusieurs environnements multiclouds. Découvrez-en plus dans [Utiliser Azure Defender pour Kubernetes afin de protéger les déploiements Kubernetes hybrides et multiclouds (en préversion)](release-notes.md#use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-in-preview).

Pour signifier que les alertes de sécurité fournies par Azure Defender pour Kubernetes ne sont plus limitées aux clusters sur Azure Kubernetes Service, le préfixe des types d’alerte passe de « AKS_ » à « K8s_ ». Si nécessaire, les noms et descriptions sont également mis à jour. Par exemple, cette alerte :

|Alerte (type d’alerte)|Description|
|----|----|
|Détection d’un outil de test d’intrusion Kubernetes<br>(**AKS** _PenTestToolsKubeHunter)|L’analyse du journal d’audit de Kubernetes a détecté l’utilisation de l’outil de test d’intrusion Kubernetes dans le cluster **AKS**. Bien que ce comportement puisse être légitime, des attaquants peuvent utiliser ces outils publics à des fins malveillantes.
|||

devient :

|Alerte (type d’alerte)|Description|
|----|----|
|Détection d’un outil de test d’intrusion Kubernetes<br>(**K8s** _PenTestToolsKubeHunter)|L’analyse du journal d’audit de Kubernetes a détecté l’utilisation de l’outil de test d’intrusion Kubernetes dans le cluster **Kubernetes**. Bien que ce comportement puisse être légitime, des attaquants peuvent utiliser ces outils publics à des fins malveillantes.|
|||

Toutes les règles de suppression qui font référence à des alertes qui commencent par « AKS_ » sont automatiquement converties. Si vous avez configuré des exportations SIEM ou des scripts d’automatisation personnalisés qui font référence à des alertes Kubernetes par type d’alerte, vous avez besoin de les mettre à jour avec les nouveaux types d’alerte.

Pour obtenir la liste complète des alertes Kubernetes, consultez [Alertes pour les clusters Kubernetes](alerts-reference.md#alerts-akscluster).

### <a name="legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013"></a>L’implémentation héritée de la norme ISO 27001 est remplacée par la nouvelle norme ISO 27001:2013

L’implémentation héritée de la norme ISO 27001 sera supprimée du tableau de bord de conformité réglementaire de Security Center. Si vous effectuez le suivi de la conformité ISO 27001 avec Security Center, intégrez la nouvelle norme ISO 27001:2013 pour tous les groupes d’administration ou abonnements appropriés, car la norme ISO 27001 actuelle sera bientôt supprimée du tableau de bord.

:::image type="content" source="media/upcoming-changes/removing-iso-27001-legacy-implementation.png" alt-text="Tableau de bord de conformité réglementaire Security Center présentant le message relatif à la suppression de l’implémentation héritée de la norme ISO 27001." lightbox="media/upcoming-changes/removing-iso-27001-legacy-implementation.png":::

### <a name="recommendations-from-aws-will-be-released-for-general-availability-ga"></a>Les recommandations d’AWS seront publiées pour la disponibilité générale (GA)

**Date estimée de la modification :** Août 2021

Azure Security Center protège les charges de travail dans Azure, Amazon Web Services (AWS) et Google Cloud Platform (GCP).

Les recommandations provenant d’AWS Security Hub sont en préversion depuis la présentation des connecteurs cloud. Les recommandations signalées comme **Préversion** ne sont pas incluses dans le calcul de votre degré de sécurisation, mais vous devez quand même les corriger dans la mesure du possible, afin qu’elles contribuent au résultat à la fin de la période de préversion.

Avec cette modification, deux ensembles de recommandations AWS seront déplacés vers la disponibilité générale :

- [Contrôles PCI DSS de Security Hub](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-pci-controls.html)
- [Contrôles CIS AWS Foundations Benchmark de Security Hub](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-cis-controls.html)

Lorsqu’ils sont en disponibilité générale et que les évaluations s’exécutent sur vos ressources AWS, les résultats ont un impact sur le degré de sécurisation combiné pour toutes vos ressources cloud hybrides et multiclouds.



### <a name="enhancements-to-sql-data-classification-recommendation"></a>Améliorations apportées à la recommandation de classification des données SQL

**Date estimée de la modification :** T2 2021

La recommandation **Les données sensibles de vos bases de données SQL doivent être classifiées** du contrôle de sécurité **Appliquer la classification des données** va être remplacée par une nouvelle version mieux alignée sur la stratégie de classification des données de Microsoft. L’ID de la recommandation sera donc également modifié (actuellement, il s’agit de b0df6f56-862d-4730-8597-38c0fd4ebd59).



## <a name="next-steps"></a>Étapes suivantes

Pour tous les changements récents du produit, consultez [Nouveautés d’Azure Security Center](release-notes.md).