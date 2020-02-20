---
title: Recommandations de sécurité dans le Centre de sécurité Azure
description: Ce document vous montre comment les recommandations du Centre de sécurité Azure peuvent vous aider à protéger vos ressources Azure et à rester en conformité avec les stratégies de sécurité.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/29/2019
ms.author: memildin
ms.openlocfilehash: 4f41f4996f230a373245633b9449e93c403a1dfc
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77430293"
---
# <a name="security-recommendations-in-azure-security-center"></a>Recommandations de sécurité dans le Centre de sécurité Azure 
Cette rubrique explique comment visualiser et comprendre les recommandations d’Azure Security Center pour vous aider à protéger vos ressources Azure.

> [!NOTE]
> Ce document présente le service à l’aide d’un exemple de déploiement.  Ce document n'est pas un guide pas à pas.
>

## <a name="what-are-security-recommendations"></a>Quelles sont les recommandations de sécurité ?

Les recommandations sont des actions à effectuer pour sécuriser vos ressources.

Security Center analyse périodiquement l’état de sécurité de vos ressources Azure pour identifier les vulnérabilités de sécurité potentielles. Il fournit ensuite des recommandations sur la façon de les supprimer.

Chaque recommandation vous fournit les informations suivantes :

- Brève description de ce qui est recommandé.
- Étapes de correction à effectuer pour implémenter la recommandations. <!-- In some cases, Quick Fix remediation is available. -->
- Ressources nécessitant une action recommandée de votre part.
- **Impact du degré de sécurisation**, à savoir la proportion dans laquelle l’implémentation de cette recommandations augmentera votre degré de sécurisation.

## Surveiller les recommandations <a name="monitor-recommendations"></a>

Azure Security Center analyse l’état de sécurité de vos ressources pour identifier les vulnérabilités potentielles. La vignette **Recommandations** sous **Vue d’ensemble** montre le nombre total de recommandations identifiées par Security Center.

![Vue d’ensemble de Security Center](./media/security-center-recommendations/asc-overview.png)

1. Sélectionnez la **vignette Recommandations** sous **Vue d’ensemble**. La liste **Recommandations** s’ouvre.

      ![Afficher les recommandations](./media/security-center-recommendations/view-recommendations.png)

    Vous pouvez filtrer les recommandations. Pour filtrer les recommandations, sélectionnez **Filtrer** sur le panneau **Recommandations**. Le panneau **Filtrer** s’ouvre et vous permet d’afficher uniquement certains états ou niveaux de gravité.

   * **RECOMMANDATIONS** : La recommandation.
   * **IMPACT DU DEGRÉ DE SÉCURISATION** : Score généré par Security Center en utilisant vos recommandations en matière de sécurité et en appliquant des algorithmes avancés pour déterminer le degré d’importance de chaque recommandation. Pour plus d’informations, consultez [Calcul des scores de sécurité](security-center-secure-score.md#secure-score-calculation).
   * **RESSOURCE** : Ressources auxquelles s’appliquent les recommandations.
   * **BARRES D’ÉTAT** :  Donne le niveau de gravité de chaque recommandation :
       * **Élevé (rouge)**  : Existence d’une vulnérabilité sur une ressource importante (application, machine virtuelle ou groupe de sécurité réseau). Le problème doit être analysé.
       * **Moyen (Orange)**  : Il existe une vulnérabilité ; des étapes supplémentaires ou non critiques sont requises pour l’éliminer ou pour terminer un processus.
       * **Faible (bleu)**  : Existence d’une vulnérabilité devant être prise en compte, mais qui ne nécessite pas une attention immédiate. Par défaut, les recommandations de niveau Faible ne sont pas affichées, mais vous pouvez filtrer les recommandations pour les faire apparaître. 
       * **Sain (vert)**  :
       * **Non disponible (gris)**  :

1. Pour voir les détails de chaque recommandation, cliquez sur la recommandation.

    ![Détails de la recommandation](./media/security-center-recommendations/recommendation-details.png)

>[!NOTE] 
> Consultez [Modèles de déploiement classique et Resource Manager](../azure-classic-rm.md) pour les ressources Azure.
 
## <a name="next-steps"></a>Étapes suivantes

Ce document vous a présenté les recommandations de sécurité du Centre de sécurité. Pour savoir comment implémenter les recommandations :

* [Implémenter les recommandations](security-center-remediate-recommendations.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
