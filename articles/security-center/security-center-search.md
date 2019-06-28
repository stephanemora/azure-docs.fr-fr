---
title: Recherche Azure Security Center | Microsoft Docs
description: Découvrez comment Azure Security Center utilise la recherche de Journaux Azure Monitor pour récupérer et analyser vos données de sécurité.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: rkarlin
ms.openlocfilehash: 90dbb33fa516d3a831d4e60969ac6b6c8312d539
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64574142"
---
# <a name="azure-security-center-search"></a>Recherche Azure Security Center

> [!NOTE]
> La mise hors service du tableau de bord de recherche du Security Center est prévue pour le 31 juillet 2019. Pour en savoir plus et connaître les alternatives en matière de services, consultez [Retrait des fonctionnalités du Security Center (juillet 2019)](security-center-features-retirement-july2019.md#menu_search).

Azure Security Center utilise la [recherche de Journaux Azure Monitor](../log-analytics/log-analytics-log-searches.md) pour récupérer et analyser vos données de sécurité. La solution Journaux Azure Monitor inclut un langage de requête pour rapidement récupérer et regrouper les données. Dans Security Center, vous pouvez utiliser la recherche de Journaux Azure Monitor pour créer des requêtes et analyser les données collectées.

Recherche est disponible dans le niveau Gratuit et le niveau Standard de Security Center.  Les données disponibles dans vos recherches dans les journaux dépendent du niveau appliqué à votre espace de travail.  Pour plus d’informations, consultez la [page de tarification](../security-center/security-center-pricing.md) de Security Center.


> [!NOTE]
> Security Center n’enregistre pas les données de sécurité d’un espace de travail avec le niveau Gratuit. Vous pouvez envoyer différents journaux d’activité à un espace de travail dans le niveau Gratuit et effectuer des recherches sur ces données, mais les résultats de recherche n’incluent pas de données de Security Center. Security Center enregistre uniquement les données dans un espace de travail appartenant au niveau Standard.
>
>

## <a name="access-search"></a>Accéder à la recherche
1. Dans le menu principal de Security Center, sélectionnez **Recherche**.

   ![Sélectionner Recherche dans les journaux][1]

2. Security Center répertorie tous les espaces de travail dans vos abonnements Azure. Sélectionnez un espace de travail. (Si vous avez un seul espace de travail, ce sélecteur d’espaces de travail n’apparaît pas.)

   ![Sélectionner un espace de travail][2]

3. **Recherche dans les journaux** s’ouvre. Pour interroger davantage de données dans l’espace de travail sélectionné, entrez cet exemple de requête :

   SecurityEvent | where EventID == 4625 | summarize count() by TargetAccount

   Le résultat affiche tous les comptes pour lesquels la connexion a échoué (événement 4625).

   ![Résultats de la recherche][3]

Consultez [Langage de requête Kusto](../log-analytics/log-analytics-search-reference.md) pour plus d’informations sur la façon d’interroger des données dans l’espace de travail sélectionné.

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris comment accéder à la recherche dans Security Center. Security Center utilise la recherche de Journaux Azure Monitor. Pour en savoir plus sur la recherche de Journaux Azure Monitor, consultez :

- [Présentation de Journaux Azure Monitor](../log-analytics/log-analytics-overview.md) \- Vue d’ensemble de Journaux Azure Monitor
- [Présentation des recherches de journaux dans Journaux Azure Monitor](../log-analytics/log-analytics-log-search-new.md) - Décrit la façon dont les recherches de journaux sont utilisées dans Journaux Azure Monitor, et définit les concepts que vous devez connaître avant de créer une recherche dans les journaux
- [Trouver des données avec les recherches de journaux dans Journaux Azure Monitor](../log-analytics/log-analytics-log-searches.md) - Tutoriel sur l’utilisation de la recherche dans les journaux
- [Informations de référence sur la recherche Kusto](../log-analytics/log-analytics-search-reference.md) - Décrit le langage de requête dans Journaux Azure Monitor

Pour plus d’informations sur Security Center, consultez :

- [Présentation de Security Center](security-center-intro.md) - Décrit les fonctionnalités clés de Security Center.

<!--Image references-->
[1]: ./media/security-center-search/search.png
[2]: ./media/security-center-search/workspace-selector.png
[3]: ./media/security-center-search/log-search.png
