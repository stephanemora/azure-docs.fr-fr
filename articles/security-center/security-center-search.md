---
title: Recherche Azure Security Center | Microsoft Docs
description: Découvrez comment Azure Security Center utilise la recherche de journaux Azure Monitor pour récupérer et analyser vos données de sécurité.
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
ms.openlocfilehash: 6cbf3d70bd835ce1b838b19c93507f7d9487a418
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58074503"
---
# <a name="azure-security-center-search"></a>Recherche Azure Security Center
Azure Security Center utilise [Azure Monitor enregistre recherche](../log-analytics/log-analytics-log-searches.md) pour récupérer et analyser vos données de sécurité. Journaux d’analyse Azure inclut un langage de requête pour rapidement récupérer et consolider les données. À partir du centre de sécurité, vous pouvez utiliser la recherche de journaux Azure Monitor pour construire des requêtes et analyser les données collectées.

Recherche est disponible dans le niveau Gratuit et le niveau Standard de Security Center.  Les données disponibles dans vos recherches dans les journaux dépendent du niveau appliqué à votre espace de travail.  Pour plus d’informations, consultez la [page de tarification](../security-center/security-center-pricing.md) de Security Center.


> [!NOTE]
> Security Center n’enregistre pas les données de sécurité d’un espace de travail avec le niveau Gratuit. Vous pouvez envoyer différents journaux à un espace de travail dans le niveau Gratuit et effectuer des recherches sur ces données, mais les résultats de recherche n’incluent pas de données de Security Center. Security Center enregistre uniquement les données dans un espace de travail appartenant au niveau Standard.
>
>

## <a name="access-search"></a>Accéder à la recherche
1. Dans le menu principal de Security Center, sélectionnez **Recherche**.

   ![Sélectionner Recherche dans les journaux][1]

2. Security Center répertorie tous les espaces de travail dans vos abonnements Azure. Sélectionnez un espace de travail. (Si vous avez un seul espace de travail, ce sélecteur d’espaces de travail n’apparaît pas.)

   ![Sélectionner un espace de travail][2]

3. **Recherche dans les journaux** s’ouvre. Pour interroger davantage de données dans l’espace de travail sélectionné, entrez cet exemple de requête :

   SecurityEvent | where EventID == 4625 | summarize count() by TargetAccount

   Le résultat affiche tous les comptes pour lesquels l’ouverture de session a échoué (événement 4625).

   ![Résultats de la recherche][3]

Consultez [langage de requête Kusto](../log-analytics/log-analytics-search-reference.md) pour plus d’informations sur comment interroger des données dans l’espace de travail sélectionné.

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris comment accéder à la recherche dans Security Center. Security Center utilise la recherche de journaux Azure Monitor. Pour en savoir plus sur la recherche de journaux Azure Monitor, consultez :

- [Nouveautés d’Azure Monitor journaux ?](../log-analytics/log-analytics-overview.md) -Présentation de journaux Azure Monitor
- [Recherches dans les journaux de présentation dans les journaux Azure Monitor](../log-analytics/log-analytics-log-search-new.md) : décrit l’utilisation des recherches dans les journaux dans les journaux Azure Monitor et présente les concepts que vous devraient comprendre avant de créer une recherche de journal
- [Rechercher des données à l’aide de recherches dans les journaux dans Azure Monitor journaux](../log-analytics/log-analytics-log-searches.md) -didacticiel sur l’utilisation de recherche de journal
- [Référence de recherche Kusto](../log-analytics/log-analytics-search-reference.md) – décrit le langage de requête dans les journaux Azure Monitor

Pour plus d’informations sur Security Center, consultez :

- [Présentation de Security Center](security-center-intro.md) - Décrit les fonctionnalités clés de Security Center.

<!--Image references-->
[1]: ./media/security-center-search/search.png
[2]: ./media/security-center-search/workspace-selector.png
[3]: ./media/security-center-search/log-search.png
