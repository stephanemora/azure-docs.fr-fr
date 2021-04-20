---
title: Changements importants à venir sur Azure Security Center
description: Changements à venir sur Azure Security Center dont vous devez peut-être tenir compte et pour lesquels une planification peut être nécessaire
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 04/08/2021
ms.author: memildin
ms.openlocfilehash: f9e1b5d19acbc9bcee86c374a3f843530b8adc61
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103757"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Changements importants à venir sur Azure Security Center

> [!IMPORTANT]
> Les informations de cette page concernent des produits ou fonctionnalités en préversion, qui peuvent être modifiés de façon substantielle avant d’être commercialisés, le cas échéant. Microsoft n’offre aucun engagement ni garantie, formel ou implicite, concernant les informations contenues dans ce document.

Dans cette page, vous allez découvrir les changements planifiés pour Security Center. Elle décrit les changements planifiés du produit qui peuvent avoir un impact sur votre degré de sécurisation ou vos workflows.

Si vous recherchez les notes de publication les plus récentes, vous les trouverez dans [Nouveautés d’Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Changements planifiés

| Modifications planifiées                                                                                                                                                        | Date estimée de la modification |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------|
| [21 recommandations se déplaçant entre les contrôles de sécurité](#21-recommendations-moving-between-security-controls)                                                           | Avril 2021                |
| [Deux recommandations du contrôle de sécurité « Appliquer les mises à jour système » bientôt dépréciées](#two-recommendations-from-apply-system-updates-security-control-being-deprecated) | Avril 2021                |
| [Les recommandations d’AWS seront publiées pour la disponibilité générale (GA)](#recommendations-from-aws-will-be-released-for-general-availability-ga)                     | **Août** 2021             |
| [Améliorations apportées à la recommandation de classification des données SQL](#enhancements-to-sql-data-classification-recommendation)                                                     | T2 2021                   |
|                                                                                                                                                                       |                           |


### <a name="21-recommendations-moving-between-security-controls"></a>21 recommandations se déplaçant entre les contrôles de sécurité 

**Date estimée de la modification :** Avril 2021

Les recommandations suivantes sont déplacées vers un autre contrôle de sécurité. Chaque contrôle de sécurité est un groupe logique de recommandations de sécurité associées, qui reflète les surfaces d'attaque vulnérables. Ce déplacement garantit que toutes ces recommandations sont dans le contrôle le plus approprié pour atteindre son objectif. 

Découvrez les recommandations de chaque contrôle de sécurité dans Contrôles de sécurité et leurs recommandations.

|Recommandation |Modification et impact  |
|---------|---------|
|L’évaluation des vulnérabilités doit être activée sur vos serveurs SQL<br>L’évaluation des vulnérabilités doit être activée sur vos instances managées SQL<br>Les vulnérabilités de vos bases de données SQL doivent être corrigées<br>Les vulnérabilités sur vos bases de données SQL dans les machines virtuelles doivent être corrigées     |Déplacement de Corriger les vulnérabilités (6 points)<br>vers Corriger les configurations de sécurité (4 points).<br>Ces recommandations ont un impact réduit sur votre score, en fonction de votre environnement.|
|Plusieurs propriétaires doivent être affectés à votre abonnement<br>Les variables de compte Automation doivent être chiffrées<br> Appareils IoT – Le processus audité a arrêté d’envoyer des événements<br> Appareils IoT – Échec de la validation de la base du système d’exploitation<br> Appareils IoT – Mise à niveau de la suite de chiffrement TLS requise<br> Appareils IoT – Ports ouverts sur l’appareil<br> Appareils IoT – Stratégie de pare-feu permissive trouvée dans l’une des chaînes<br> Appareils IoT – Règle de pare-feu permissive trouvée dans la chaîne d’entrée<br> Appareils IoT – Règle de pare-feu permissive trouvée dans la chaîne de sortie<br>Les journaux de diagnostic dans IoT Hub doivent être activés<br> Appareils IoT – Agent envoyant des messages sous-exploités<br>Appareils IoT : la stratégie de filtre IP par défaut devrait être refusée<br>Appareils IoT : plage d’adresses IP large pour la règle de filtre IP<br>Appareils IoT : les intervalles et la taille des messages des agents doivent être ajustés<br>Appareils IoT : informations d’identification et d’authentification identiques<br>Appareils IoT : le processus audité a arrêté d’envoyer des événements<br>Appareils IoT : la configuration de ligne de base du système d’exploitation doit être corrigée|Déplacement vers **Implémenter les meilleures pratiques de sécurité**.<br>Lorsqu’une recommandation se déplace vers le contrôle de sécurité Implémenter les bonnes pratiques de sécurité, ce qui n’est pas du tout judicieux, la recommandation n’affecte plus votre score sécurisé.|
|||


### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Deux recommandations du contrôle de sécurité « Appliquer les mises à jour système » bientôt dépréciées

**Date estimée de la modification :** Avril 2021

Les deux recommandations suivantes sont déconseillées :

- **La version du système d'exploitation (SE) doit être mise à jour pour vos rôles service cloud**. Par défaut, Azure met régulièrement à jour votre SE invité vers la dernière image prise en charge d’un produit de la famille de SE que vous avez spécifiée dans votre configuration de service (.cscfg), tel que Windows Server 2016.
- **Les services Kubernetes doivent être mis à niveau vers une version Kubernetes non vulnérable**. Les évaluations de cette recommandation ne sont pas aussi étendues que nous le voulions. La version actuelle de cette recommandation sera finalement remplacée par une version améliorée qui est mieux alignée sur les besoins de sécurité de nos clients.


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