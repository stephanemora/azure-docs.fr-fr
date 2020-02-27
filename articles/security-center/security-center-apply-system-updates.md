---
title: Appliquer les mises à jour système dans Azure Security Center | Microsoft Docs
description: Ce document vous explique comment implémenter les recommandations de l’Azure Security Center **Appliquer les mises à jour système** et **Redémarrer après l’application des mises à jour système**.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e5bd7f55-38fd-4ebb-84ab-32bd60e9fa7a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: 3f27753b0775f44cbdf9d4c478a19e423b8e1f19
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604555"
---
# <a name="apply-system-updates-in-azure-security-center"></a>Appliquer les mises à jour système dans Azure Security Center
Azure Security Center recherche quotidiennement les mises à jour manquantes du système d’exploitation sur les ordinateurs et machines virtuelles Windows et Linux. Security Center récupère une liste des mises à jour de sécurité et critiques disponibles dans Windows Update ou Windows Server Update Services (WSUS), selon le service configuré sur un ordinateur Windows. Security Center recherche également les dernières mises à jour dans les systèmes Linux. S’il manque une mise à jour système sur votre machine virtuelle ou ordinateur, Security Center vous recommande de l’appliquer.

## <a name="implement-the-recommendation"></a>Implémenter la recommandation
L’application des mises à jour système est présentée sous forme de recommandation dans Security Center. S’il manque une mise à jour système sur votre machine virtuelle ou ordinateur, cette recommandation s’affichera dans **Recommandations** et dans **Calcul**.  Lorsque vous sélectionnez la recommandation, le tableau de bord **Appliquer les mises à jour système** s’ouvre.

Dans cet exemple, nous utilisons **Calcul**.

1. Dans le menu principal de Security Center, sélectionnez **Calcul**.

   ![Sélectionner Calcul][1]

2. Dans **Calcul**, sélectionnez **Mises à jour système manquantes**. Le tableau de bord **Appliquer les mises à jour système** s’ouvre.

   ![Tableau de bord Appliquer les mises à jour système][2]

   La partie supérieure du tableau de bord indique :

    - Le nombre total de mises à jour système manquantes pour les ordinateurs et machines virtuelles Windows et Linux.
    - Le nombre total de mises à jour critiques manquantes sur vos ordinateurs et machines virtuelles.
    - Le nombre total de mises à jour de sécurité manquantes sur l’ensemble de vos ordinateurs et machines virtuelles.

   La partie inférieure du tableau de bord répertorie toutes les mises à jour manquantes sur l’ensemble de vos ordinateurs et machines virtuelles, ainsi que le niveau de gravité de la mise à jour manquante.  Cette liste comprend les éléments suivants :

    - NOM : nom de la mise à jour manquante.
    - NOMBRE DE MACHINES VIRTUELLES ET D’ORDINATEURS : nombre total de machines virtuelles et d’ordinateurs sur lesquels cette mise à jour n’a pas été installée.
    - ÉTAT : état actuel de la recommandation :

      - Ouverte : la suggestion n’a pas encore été prise en compte.
      - En cours : la recommandation est actuellement appliquée aux ressources ; aucune action de votre part n’est nécessaire.
      - Résolue : la recommandation a déjà été achevée. (Une fois problème résolu, l’entrée a été grisée).

    - Niveau de gravité : Donne le niveau de gravité de chaque recommandation :

      - Élevée : Existence d’une vulnérabilité sur une ressource importante (application, machine virtuelle, groupe de sécurité réseau). Le problème doit être analysé.
      - Moyenne : Certaines étapes supplémentaires sont nécessaires pour terminer un processus ou éliminer une vulnérabilité.
      - Faible : Existence d’une vulnérabilité devant être prise en compte, mais qui ne nécessite aucune attention immédiate. Par défaut, les recommandations de niveau Faible ne sont pas affichées, mais vous pouvez filtrer les recommandations pour les faire apparaître.

3. Sélectionnez une mise à jour manquante dans la liste pour afficher les détails.

   ![Mise à jour de sécurité manquante][3]

4. Sélectionnez l’icône **Recherche** dans le ruban supérieur.  Une requête de recherche dans les journaux d'activité Azure Monitor filtrée sur les ordinateurs n’ayant pas la mise à jour installée s’ouvre.

   ![Recherche Journaux Azure Monitor][4]

5. Sélectionnez un ordinateur dans la liste pour plus d’informations. Un autre résultat de recherche s’ouvre avec les informations filtrées uniquement pour cet ordinateur.

    ![Recherche Journaux Azure Monitor][5]

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Définition des stratégies de sécurité dans Azure Security Center](tutorial-security-policy.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez comment les recommandations peuvent vous aider à protéger vos ressources Azure.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions de partenaires avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaires.
* [Blog sur la sécurité Azure](https://blogs.msdn.com/b/azuresecurity/) : recherchez des billets de blog sur la sécurité et la conformité Azure.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/missing-system-updates.png
[2]:./media/security-center-apply-system-updates/apply-system-updates.png
[3]: ./media/security-center-apply-system-updates/detail-on-missing-update.png
[4]: ./media/security-center-apply-system-updates/log-search.png
[5]: ./media/security-center-apply-system-updates/search-details.png
