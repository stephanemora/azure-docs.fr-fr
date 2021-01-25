---
title: Changements importants à venir sur Azure Security Center
description: Changements à venir sur Azure Security Center dont vous devez peut-être tenir compte et pour lesquels une planification peut être nécessaire
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/18/2021
ms.author: memildin
ms.openlocfilehash: ba9a640c2231c7098e58ad6e29bbfa196436a7f9
ms.sourcegitcommit: 61d2b2211f3cc18f1be203c1bc12068fc678b584
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98562316"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Changements importants à venir sur Azure Security Center

> [!IMPORTANT]
> Les informations de cette page concernent des produits ou fonctionnalités en préversion, qui peuvent être modifiés de façon substantielle avant d’être commercialisés, le cas échéant. Microsoft n’offre aucun engagement ni garantie, formel ou implicite, concernant les informations contenues dans ce document.

Dans cette page, vous allez découvrir les changements planifiés pour Security Center. Elle décrit les changements planifiés du produit qui peuvent avoir un impact sur votre degré de sécurisation ou vos workflows.

Si vous recherchez les notes de publication les plus récentes, vous les trouverez dans [Nouveautés d’Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Changements planifiés

- [Deux recommandations du contrôle de sécurité « Appliquer les mises à jour système » bientôt dépréciées](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [Améliorations apportées à la recommandation de classification des données SQL](#enhancements-to-sql-data-classification-recommendation)
- [Ressources « non applicables » à signaler comme « Conformes » dans les évaluations Azure Policy](#not-applicable-resources-to-be-reported-as-compliant-in-azure-policy-assessments)
- [Ajout de 35 recommandations (préversion) pour mieux détailler le benchmark de sécurité Azure](#35-preview-recommendations-being-added-to-increase-coverage-of-azure-security-benchmark)


### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Deux recommandations du contrôle de sécurité « Appliquer les mises à jour système » bientôt dépréciées 

**Date estimée de la modification :** Février 2021

Les deux recommandations suivantes seront dépréciées dès février 2021 :

- **Vos machines doivent être redémarrées pour appliquer les mises à jour système**. Cela peut avoir un léger impact sur votre degré de sécurisation.
- **L’agent de surveillance doit être installé sur vos machines**. Cette recommandation se réfère uniquement aux machines locales et une partie de sa logique sera transférée vers une autre recommandation : **Les problèmes d’intégrité de l’agent Log Analytics doivent être résolus sur vos machines**. Cela peut avoir un léger impact sur votre degré de sécurisation.

Nous vous recommandons de vérifier vos configurations d’exportation continue et d’automatisation du workflow pour voir si ces recommandations y sont incluses. En outre, tous les tableaux de bord et autres outils de supervision susceptibles de les utiliser doivent être mis à jour en conséquence.

Pour plus d’informations sur ces recommandations, consultez la [page de référence sur les recommandations de sécurité](recommendations-reference.md).


### <a name="enhancements-to-sql-data-classification-recommendation"></a>Améliorations apportées à la recommandation de classification des données SQL

**Date estimée de la modification :** T2 2021

La version actuelle de la recommandation **Les données sensibles de vos bases de données SQL doivent être classifiées** du contrôle de sécurité **Appliquer la classification des données** va être dépréciée et remplacée par une nouvelle version mieux alignée sur la stratégie de classification des données de Microsoft. Par conséquent :

- La recommandation n’affectera plus votre degré de sécurisation
- Le contrôle de sécurité (« Appliquer la classification des données ») n’affectera plus votre degré de sécurisation
- L’ID de la recommandation sera également modifié (actuellement, il s’agit de b0df6f56-862D-4730-8597-38c0fd4ebd59)



### <a name="not-applicable-resources-to-be-reported-as-compliant-in-azure-policy-assessments"></a>Ressources « non applicables » à signaler comme « Conformes » dans les évaluations Azure Policy

**Date estimée de la modification :** Janvier 2021

Actuellement, les ressources qui sont évaluées pour une recommandation et considérées comme **non applicables** apparaissent dans Azure Policy comme « Non conformes ». Aucune action de l’utilisateur ne peut changer leur état en « Conforme ». À partir de ce changement planifié, elles seront signalées comme « Conformes » pour une plus grande clarté.

La seule incidence sera observée dans Azure Policy, où le nombre de ressources compatibles augmentera. Il n’y aura aucune incidence sur votre degré de sécurisation dans Azure Security Center.

### <a name="35-preview-recommendations-being-added-to-increase-coverage-of-azure-security-benchmark"></a>Ajout de 35 recommandations (préversion) pour mieux détailler le benchmark de sécurité Azure

**Date estimée de la modification :** Janvier 2021

Le Benchmark de sécurité Azure est l’ensemble des directives propres à Azure et créées par Microsoft contenant les bonnes pratiques de sécurité et de conformité basées sur les infrastructures de conformité courantes. [En savoir plus sur le Benchmark de sécurité Azure](../security/benchmarks/introduction.md).

Les 35 recommandations suivantes (préversion) seront ajoutées à Security Center pour mieux détailler ce benchmark.

Les recommandations en préversion ne rendent pas une ressource non saine et ne sont pas incluses dans les calculs de votre degré de sécurisation. Corrigez-les là où c’est possible, de sorte que quand la période de préversion se termine, elles soient prises en compte dans le calcul de votre degré de sécurisation. Découvrez comment répondre à ces recommandations dans [Corriger les recommandations dans Azure Security Center](security-center-remediate-recommendations.md).

| Contrôle de sécurité                     | Nouvelles recommandations                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Activer le chiffrement des données au repos            | - Les comptes Azure Cosmos DB doivent utiliser des clés gérées par le client pour chiffrer les données au repos<br>- Les espaces de travail Azure Machine Learning doivent être chiffrés avec une clé gérée par le client (CMK)<br>- La protection des données BYOK (Bring Your Own Key) doit être activée pour les serveurs MySQL<br>- La protection des données BYOK (Bring Your Own Key) doit être activée pour les serveurs PostgreSQL<br>- Les comptes Cognitive Services doivent activer le chiffrement des données avec une clé gérée par le client (CMK)<br>- Les registres de conteneurs doivent être chiffrés avec une clé gérée par le client (CMK)<br>- Les instances managées SQL doivent utiliser des clés gérées par le client pour chiffrer les données au repos<br>- Les serveurs SQL doivent utiliser des clés gérées par le client pour chiffrer les données au repos<br>- Les comptes de stockage doivent utiliser une clé gérée par le client (CMK) pour le chiffrement                                                                                                                                                              |
| Implémenter les bonnes pratiques de sécurité    | - Les abonnements doivent avoir l’adresse e-mail d’un contact pour le signalement des problèmes de sécurité<br> - Le provisionnement automatique de l’agent Log Analytics doit être activé sur votre abonnement<br> - La notification par e-mail pour les alertes à gravité élevée doit être activée<br> - La notification par e-mail au propriétaire de l’abonnement pour les alertes à gravité élevée doit être activée<br> - La protection contre la suppression définitive doit être activée sur les coffres de clés<br> - La suppression réversible doit être activée sur les coffres de clés |
| Gérer l’accès et les autorisations        | - Les applications de fonction doivent avoir l’option « Certificats clients (certificats clients entrants) » activée |
| Protéger les applications contre les attaques DDoS | - Le pare-feu d’applications web (WAF) doit être activé pour Application Gateway<br> - Web Application Firewall (WAF) doit être activé pour le service Azure Front Door Service |
| Restreindre l’accès réseau non autorisé | - Le pare-feu doit être activé sur Key Vault<br> - Le point de terminaison privé doit être configuré pour Key Vault<br> - App Configuration doit utiliser une liaison privée<br> - Azure Cache pour Redis doit se trouver dans un réseau virtuel<br> - Les domaines Azure Event Grid doivent utiliser une liaison privée<br> - Les rubriques Azure Event Grid doivent utiliser une liaison privée<br> - Les espaces de travail Azure Machine Learning doivent utiliser une liaison privée<br> - Azure SignalR Service doit utiliser une liaison privée<br> - Azure Spring Cloud doit utiliser l’injection de réseau<br> - Les registres de conteneurs ne doivent pas autoriser un accès réseau sans restriction<br> - Les registres de conteneurs doivent utiliser une liaison privée<br> - L’accès au réseau public doit être désactivé pour les serveurs MariaDB<br> - L’accès au réseau public doit être désactivé pour les serveurs MySQL<br> - L’accès au réseau public doit être désactivé pour les serveurs PostgreSQL<br> - Le compte de stockage doit utiliser une connexion de liaison privée<br> - Les comptes de stockage doivent utiliser des règles de réseau virtuel pour restreindre l’accès au réseau<br> - Les modèles VM Image Builder doivent utiliser une liaison privée|
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Liens connexes :

- [En savoir plus sur le Benchmark de sécurité Azure](../security/benchmarks/introduction.md)
- [En savoir plus sur Azure Database for MariaDB](../mariadb/overview.md)
- [En savoir plus sur Azure Database pour MySQL](../mysql/overview.md)
- [En savoir plus sur Azure Database pour PostgreSQL](../postgresql/overview.md)





## <a name="next-steps"></a>Étapes suivantes

Pour tous les changements récents du produit, consultez [Nouveautés d’Azure Security Center](release-notes.md).