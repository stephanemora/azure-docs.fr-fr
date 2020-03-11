---
title: Recommandations concernant les conteneurs dans Azure Security Center | Microsoft Docs
description: Ce document explique les recommandations Azure Security Center pour la protection des conteneurs.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2e76c7f7-a3dd-4d9f-add9-7e0e10e9324d
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: memildin
ms.openlocfilehash: b1e1402f58b103570d6a98a5f9a01c8168abf749
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2020
ms.locfileid: "77912358"
---
# <a name="understand-azure-security-center-container-recommendations"></a>Comprendre les recommandations sur les conteneurs d’Azure Security Center

Quand vous migrez vos applications colossales pour exécuter des applications natives cloud, stratégiques et conteneurisées en production, vous pouvez tirer parti des fonctionnalités des conteneurs, notamment de la facilité et de la rapidité du déploiement et de la mise à jour. À mesure que le nombre de conteneurs déployés continue d’augmenter, vous devez mettre en place des solutions de sécurité qui vous offrent une bonne visibilité de l’état de sécurité de vos conteneurs pour pouvoir les protéger contre les menaces.

Azure Security Center fournit les fonctionnalités suivantes pour vous aider à sécuriser vos conteneurs :

- **Visibilité dans les conteneurs hébergés sur des machines IaaS Linux**<br>Dans Azure Security Center, l’onglet Conteneurs montre toutes les machines virtuelles déployées avec Docker. Lorsque vous examinez des problèmes de sécurité sur une machine virtuelle, Security Center fournit des informations supplémentaires liées aux conteneurs sur la machine, comme la version de Docker et le nombre d’images en cours d’exécution sur l’hôte.

    ![onglet Conteneur](./media/security-center-container-recommendations/docker-recommendation.png)


- **Recommandations de sécurité issues du test d’évaluation CIS pour Docker**<br>Security Center analyse vos configurations Docker afin d’en détecter les erreurs en fournissant la liste de toutes les règles ayant échoué qui ont été évaluées. Security Center fournit des instructions pour vous aider à résoudre ces problèmes rapidement sans perdre de temps. Security Center évalue continuellement les configurations Docker pour vous indiquer leur état le plus récent.

    ![onglet Conteneur](./media/security-center-container-recommendations/container-cis-benchmark.png)

- **Protection en temps réel contre les menaces visant les conteneurs**<br> Security Center assure une protection contre les menaces en temps réel pour vos conteneurs sur des machines Linux avec le composant AuditD. Les alertes identifient plusieurs activités Docker suspectes, comme la création d’un conteneur privilégié sur l’hôte, l’indication de l’exécution d’un serveur SSH (Secure Shell) à l’intérieur d’un conteneur Docker ou l’utilisation de mineurs de chiffrement. Vous pouvez utiliser ces informations pour remédier rapidement aux problèmes de sécurité et améliorer la sécurité de vos conteneurs.

    ![onglet Conteneur](./media/security-center-container-recommendations/docker-threat-detection.png)

## <a name="recommendations"></a>Recommandations
Utilisez les tableaux ci-dessous comme référence pour comprendre les conteneurs disponibles hébergés sur des machines IaaS Linux et l’évaluation de la sécurité de leurs configurations Docker.

| Recommandation | Description | Correction |
| --- | --- | --- |
|Corriger les vulnérabilités dans les configurations de sécurité de conteneur |Corrigez les vulnérabilités dans les configurations de sécurité de conteneur selon sur les meilleures pratiques de configuration.| Pour corriger les vulnérabilités dans les configurations de sécurité de conteneur, procédez comme suit :<br>1. Passez en revue la liste des règles ayant échoué.<br>2. Corrigez chaque règle selon les instructions spécifiées.|


## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les recommandations qui s’appliquent à d’autres types de ressources Azure, consultez les rubriques suivantes :

* [Surveiller l’identité et l’accès dans Azure Security Center](security-center-identity-access.md)
* [Protection de votre réseau dans Azure Security Center](security-center-network-recommendations.md)
* [Protection de votre service SQL Azure dans Azure Security Center](security-center-sql-service-recommendations.md)

Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Protection de vos machines et de vos applications dans Azure Security Center](security-center-virtual-machine-protection.md)
* [Définition des stratégies de sécurité dans Azure Security Center](tutorial-security-policy.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.