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
ms.date: 01/25/2021
ms.author: memildin
ms.openlocfilehash: d5de16c8156762a229d6c707080bc197dc206a7c
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99475588"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Changements importants à venir sur Azure Security Center

> [!IMPORTANT]
> Les informations de cette page concernent des produits ou fonctionnalités en préversion, qui peuvent être modifiés de façon substantielle avant d’être commercialisés, le cas échéant. Microsoft n’offre aucun engagement ni garantie, formel ou implicite, concernant les informations contenues dans ce document.

Dans cette page, vous allez découvrir les changements planifiés pour Security Center. Elle décrit les changements planifiés du produit qui peuvent avoir un impact sur votre degré de sécurisation ou vos workflows.

Si vous recherchez les notes de publication les plus récentes, vous les trouverez dans [Nouveautés d’Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Changements planifiés

- [Les recommandations en matière de protection des charges de travail Kubernetes seront bientôt en disponibilité générale](#kubernetes-workload-protection-recommendations-will-soon-be-released-for-general-availability-ga)
- [Deux recommandations du contrôle de sécurité « Appliquer les mises à jour système » bientôt dépréciées](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [Améliorations apportées à la recommandation de classification des données SQL](#enhancements-to-sql-data-classification-recommendation)


### <a name="kubernetes-workload-protection-recommendations-will-soon-be-released-for-general-availability-ga"></a>Les recommandations en matière de protection des charges de travail Kubernetes seront bientôt en disponibilité générale

**Date estimée de la modification :** Février 2021

Les recommandations en matière de protection des charges de travail Kubernetes décrites dans [Protéger vos charges de travail Kubernetes](kubernetes-workload-protections.md) sont actuellement en préversion. Tant qu’une recommandation est en préversion, elle ne rend pas une ressource non saine et n’est pas incluse dans les calculs de votre niveau de sécurité.

Ces recommandations seront bientôt mises en disponibilité générale (GA) et *seront* incluses dans le calcul du niveau de sécurité. Si vous ne les avez pas encore corrigé, cela peut avoir un léger impact sur votre niveau de sécurité.

Corrigez-les dans la mesure du possible (découvrez comment dans [Appliquer les recommandations d’Azure Security Center](security-center-remediate-recommendations.md)).

Les recommandations en matière de protection des charges de travail Kubernetes sont les suivantes :

- Le module complémentaire Azure Policy pour Kubernetes doit être installé et activé sur vos clusters
- Les limites de mémoire et du processeur du conteneur doivent être appliquées
- Les conteneurs privilégiés doivent être évités
- Le système de fichiers racine immuable (en lecture seule) doit être appliqué pour les conteneurs
- Tout conteneur avec une élévation des privilèges doit être évité
- L’exécution des conteneurs en tant qu’utilisateur racine doit être évitée
- Éviter les conteneurs partageant des espaces de noms d’hôte sensibles
- Les fonctionnalités Linux à privilèges minimum doivent être appliquées pour les conteneurs
- L’utilisation des montages de volume HostPath de pad doit être limitée à une liste connue
- Les conteneurs doivent écouter uniquement sur les ports autorisés
- Les services doivent écouter uniquement sur les ports autorisés
- L’utilisation du réseau hôte et des ports doit être limitée
- Le remplacement ou la désactivation du profil AppArmor des conteneurs doit être limité
- Les images conteneur doivent être déployées à partir de registres approuvés uniquement             

Pour plus d’informations sur ces recommandations, consultez [Protéger vos charges de travail Kubernetes](kubernetes-workload-protections.md).

### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Deux recommandations du contrôle de sécurité « Appliquer les mises à jour système » bientôt dépréciées 

**Date estimée de la modification :** Février 2021

Les deux recommandations suivantes seront dépréciées dès février 2021 :

- **Vos machines doivent être redémarrées pour appliquer les mises à jour système**. Cela peut avoir un léger impact sur votre degré de sécurisation.
- **L’agent de surveillance doit être installé sur vos machines**. Cette recommandation se réfère uniquement aux machines locales, et une partie de sa logique sera transférée vers une autre recommandation : **Les problèmes d’intégrité de l’agent Log Analytics doivent être résolus sur vos machines**. Cela peut avoir un léger impact sur votre degré de sécurisation.

Nous vous recommandons de vérifier vos configurations d’exportation continue et d’automatisation du workflow pour voir si ces recommandations y sont incluses. En outre, tous les tableaux de bord et autres outils de supervision susceptibles de les utiliser doivent être mis à jour en conséquence.

Pour plus d’informations sur ces recommandations, consultez la [page de référence sur les recommandations de sécurité](recommendations-reference.md).


### <a name="enhancements-to-sql-data-classification-recommendation"></a>Améliorations apportées à la recommandation de classification des données SQL

**Date estimée de la modification :** T2 2021

La version actuelle de la recommandation **Les données sensibles de vos bases de données SQL doivent être classifiées** du contrôle de sécurité **Appliquer la classification des données** va être remplacée par une nouvelle version mieux alignée sur la stratégie de classification des données de Microsoft. Par conséquent :

- La recommandation n’affectera plus votre degré de sécurisation
- Le contrôle de sécurité (« Appliquer la classification des données ») n’affectera plus votre degré de sécurisation
- L’ID de la recommandation sera également modifié (actuellement, il s’agit de b0df6f56-862D-4730-8597-38c0fd4ebd59)



## <a name="next-steps"></a>Étapes suivantes

Pour tous les changements récents du produit, consultez [Nouveautés d’Azure Security Center](release-notes.md).
