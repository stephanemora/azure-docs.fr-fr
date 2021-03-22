---
title: Changements importants à venir sur Azure Security Center
description: Changements à venir sur Azure Security Center dont vous devez peut-être tenir compte et pour lesquels une planification peut être nécessaire
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 03/10/2021
ms.author: memildin
ms.openlocfilehash: 49141f7f11c0e8ead090459238e15b56f57b990b
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "102633714"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Changements importants à venir sur Azure Security Center

> [!IMPORTANT]
> Les informations de cette page concernent des produits ou fonctionnalités en préversion, qui peuvent être modifiés de façon substantielle avant d’être commercialisés, le cas échéant. Microsoft n’offre aucun engagement ni garantie, formel ou implicite, concernant les informations contenues dans ce document.

Dans cette page, vous allez découvrir les changements planifiés pour Security Center. Elle décrit les changements planifiés du produit qui peuvent avoir un impact sur votre degré de sécurisation ou vos workflows.

Si vous recherchez les notes de publication les plus récentes, vous les trouverez dans [Nouveautés d’Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Changements planifiés

- [Les recommandations d’AWS seront publiées pour la disponibilité générale (GA)](#recommendations-from-aws-will-be-released-for-general-availability-ga)
- [Deux recommandations du contrôle de sécurité « Appliquer les mises à jour système » bientôt dépréciées](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [Améliorations apportées à la recommandation de classification des données SQL](#enhancements-to-sql-data-classification-recommendation)
- [Dépréciation de 11 alertes Azure Defender](#deprecation-of-11-azure-defender-alerts)


### <a name="recommendations-from-aws-will-be-released-for-general-availability-ga"></a>Les recommandations d’AWS seront publiées pour la disponibilité générale (GA)

**Date estimée de la modification :** Avril 2021

Azure Security Center protège les charges de travail dans Azure, Amazon Web Services (AWS) et Google Cloud Platform (GCP).

Les recommandations provenant d’AWS Security Hub sont en préversion depuis la présentation des connecteurs cloud. Les recommandations signalées comme **Préversion** ne sont pas incluses dans le calcul de votre degré de sécurisation, mais vous devez quand même les corriger dans la mesure du possible, afin qu’elles contribuent au résultat à la fin de la période de préversion.

Avec cette modification, deux ensembles de recommandations AWS seront déplacés vers la disponibilité générale :

- [Contrôles PCI DSS de Security Hub](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-pci-controls.html)
- [Contrôles CIS AWS Foundations Benchmark de Security Hub](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-cis-controls.html)

Lorsqu’ils sont en disponibilité générale et que les évaluations s’exécutent sur vos ressources AWS, les résultats ont un impact sur le degré de sécurisation combiné pour toutes vos ressources cloud hybrides et multiclouds. 


### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Deux recommandations du contrôle de sécurité « Appliquer les mises à jour système » bientôt dépréciées 

**Date estimée de la modification :** Mars 2021

Les deux recommandations suivantes seront dépréciées dès février 2021 :

- **Vos machines doivent être redémarrées pour appliquer les mises à jour système**. Cela peut avoir un léger impact sur votre degré de sécurisation.
- **L’agent de surveillance doit être installé sur vos machines**. Cette recommandation se réfère uniquement aux machines locales, et une partie de sa logique sera transférée vers une autre recommandation : **Les problèmes d’intégrité de l’agent Log Analytics doivent être résolus sur vos machines**. Cela peut avoir un léger impact sur votre degré de sécurisation.

Nous vous recommandons de vérifier vos configurations d’exportation continue et d’automatisation du workflow pour voir si ces recommandations y sont incluses. En outre, tous les tableaux de bord et autres outils de supervision susceptibles de les utiliser doivent être mis à jour en conséquence.

Pour plus d’informations sur ces recommandations, consultez la [page de référence sur les recommandations de sécurité](recommendations-reference.md).


### <a name="enhancements-to-sql-data-classification-recommendation"></a>Améliorations apportées à la recommandation de classification des données SQL

**Date estimée de la modification :** T2 2021

La recommandation **Les données sensibles de vos bases de données SQL doivent être classifiées** du contrôle de sécurité **Appliquer la classification des données** va être remplacée par une nouvelle version mieux alignée sur la stratégie de classification des données de Microsoft. L’ID de la recommandation sera donc également modifié (actuellement, il s’agit de b0df6f56-862d-4730-8597-38c0fd4ebd59).


### <a name="deprecation-of-11-azure-defender-alerts"></a>Dépréciation de 11 alertes Azure Defender

**Date estimée de la modification :** Mars 2021

Le mois prochain, les onze alertes Azure Defender listées ci-dessous seront dépréciées.

- Ces deux alertes seront remplacées par de nouvelles alertes offrant une meilleure couverture :

    | AlertType                | AlertDisplayName                                                         |
    |--------------------------|--------------------------------------------------------------------------|
    | ARM_MicroBurstDomainInfo | PRÉVERSION - Détection de l’exécution de la fonction « Get-AzureDomainInfo » du kit de ressources MicroBurst |
    | ARM_MicroBurstRunbook    | PRÉVERSION - Détection de l’exécution de la fonction « Get-AzurePasswords » du kit de ressources MicroBurst  |
    |                          |                                                                          |

- Ces neuf alertes concernent un connecteur Azure Active Directory Identity Protection qui a déjà été déprécié :

    | AlertType           | AlertDisplayName              |
    |---------------------|-------------------------------|
    | UnfamiliarLocation  | Propriétés de connexion inhabituelles |
    | AnonymousLogin      | Adresse IP anonyme          |
    | InfectedDeviceLogin | Adresse IP liée à un programme malveillant     |
    | ImpossibleTravel    | Voyage inhabituel               |
    | MaliciousIP         | Adresse IP malveillante          |
    | LeakedCredentials   | Informations d’identification divulguées            |
    | PasswordSpray       | Pulvérisation de mots de passe                |
    | LeakedCredentials   | Azure AD Threat Intelligence  |
    | AADAI               | Azure AD AI                   |
    |                     |                               |
 



## <a name="next-steps"></a>Étapes suivantes

Pour tous les changements récents du produit, consultez [Nouveautés d’Azure Security Center](release-notes.md).
