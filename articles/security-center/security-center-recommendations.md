---
title: Gestion des recommandations de sécurité dans Azure Security Center | Microsoft Docs
description: Ce document vous montre comment les recommandations du Centre de sécurité Azure peuvent vous aider à protéger vos ressources Azure et à rester en conformité avec les stratégies de sécurité.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2018
ms.author: rkarlin
ms.openlocfilehash: e3b4da1c1d835e9d630c000055af058aa7b45968
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58109186"
---
# <a name="managing-security-recommendations-in-azure-security-center"></a>Gestion des recommandations de sécurité dans le Centre de sécurité Azure
Ce document explique comment les recommandations du Centre de sécurité Azure peuvent vous aider à protéger vos ressources Azure.

> [!NOTE]
> Ce document présente le service à l’aide d’un exemple de déploiement.  Ce document n'est pas un guide pas à pas.
>
>

## <a name="what-are-security-recommendations"></a>Quelles sont les recommandations de sécurité ?
Le Centre de sécurité analyse l’état de sécurité de vos ressources Azure à intervalles réguliers. Lorsqu’il identifie des failles de sécurité potentielles, il crée des recommandations. Ces recommandations vous guident tout au long du processus de configuration des contrôles nécessaires.

## <a name="implementing-security-recommendations"></a>Mise en œuvre des recommandations de sécurité
### <a name="set-recommendations"></a>Définition de recommandations
Dans la section [Définition des stratégies de sécurité dans Azure Security Center](tutorial-security-policy.md), vous apprendrez à :

* configurer des stratégies de sécurité ;
* activer la collecte des données ;
* choisir les recommandations à afficher dans le cadre de votre stratégie de sécurité.

Les recommandations de stratégie actuelles se concentrent sur les mises à jour système, les règles de base, les logiciels anti-programme malveillant, les [groupes de sécurité réseau](../virtual-network/security-overview.md) pour les sous-réseaux et les interfaces réseau, l’audit des bases de données SQL, le chiffrement transparent des données de bases de données SQL et les pare-feu d’applications web.  [Définition de stratégies de sécurité](tutorial-security-policy.md) fournit une description de chacune des recommandations.

### <a name="monitor-recommendations"></a>Suivi des recommandations
Après la définition d’une stratégie de sécurité, le Centre de sécurité analyse l’état de sécurité de vos ressources pour identifier les vulnérabilités potentielles. La vignette **Recommandations** sous **Vue d’ensemble** vous permet de connaître le nombre total de recommandations fournies par Security Center.

![Panneau Recommandations][1]

Pour afficher les détails de chaque recommandation, sélectionnez la vignette **Recommandations** sous **Vue d’ensemble**. **Recommandations** s’ouvre.

![Filtrer les recommandations][2]

Vous pouvez filtrer les recommandations. Pour filtrer les recommandations, sélectionnez **Filtrer** sur le panneau **Recommandations**. Le panneau **Filtrer** s’ouvre et vous permet d’afficher uniquement certains états ou niveaux de gravité.


* **RECOMMANDATIONS**: La recommandation.
* **SECURE SCORE IMPACT**:
* **RESSOURCE** : Ressources auxquelles s’appliquent les recommandations.
* **BARRES D’ÉTAT**:  Donne le niveau de gravité de chaque recommandation :
   * **Élevé (rouge)**: Existence d’une vulnérabilité sur une ressource importante (application, machine virtuelle ou groupe de sécurité réseau). Le problème doit être analysé.
   * **Moyen (Orange)**: Il existe une vulnérabilité ; des étapes supplémentaires ou non critiques sont requises pour l’éliminer ou pour terminer un processus.
   * **Faible (bleu)**: Existence d’une vulnérabilité devant être prise en compte, mais qui ne nécessite pas une attention immédiate. Par défaut, les recommandations de niveau Faible ne sont pas affichées, mais vous pouvez filtrer les recommandations pour les faire apparaître. 
   * **Intègre (vert)**:
   * **Non disponible (gris)**:
 


> [!NOTE]
> Vous devez apprendre à différencier les [modèles de déploiement de type classique et Resource Manager](../azure-classic-rm.md) utilisés pour les ressources Azure.
> 
> 
> ### <a name="apply-recommendations"></a>Appliquer les recommandations
> Après avoir examiné toutes les recommandations, vous pouvez décider d’en appliquer une en priorité. Les recommandations à appliquer en priorité sont celles dont le niveau de gravité est le plus élevé.



## <a name="next-steps"></a>Étapes suivantes
Ce document vous a présenté les recommandations de sécurité du Centre de sécurité. Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Définition des stratégies de sécurité dans Azure Security Center](tutorial-security-policy.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions de partenaires avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaires.
* [FAQ d’Azure Security Center](security-center-faq.md) : découvrez les réponses aux questions les plus souvent posées à propos de l’utilisation de ce service.
* [Blog sur la sécurité Azure](https://blogs.msdn.com/b/azuresecurity/) : accédez à des billets de blog sur la sécurité et la conformité Azure.

<!--Image references-->
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png
