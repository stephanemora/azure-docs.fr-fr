---
title: Affichage et analyse de données dans Azure Log Analytics | Microsoft Docs
description: Cet article décrit les portails que vous pouvez utiliser dans Azure Log Analytics pour créer et modifier les recherches dans les journaux.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: magoedte; bwren
ms.component: na
ms.openlocfilehash: 386aad94461fa3f2ceafb7564342797eefa2f086
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2018
ms.locfileid: "40246509"
---
# <a name="viewing-and-analyzing-data-in-log-analytics"></a>Affichage et analyse de données dans Log Analytics
Le portail Azure comporte deux options permettant d’analyser les données stockées dans Log Analystics et de créer des requêtes pour une analyse ad hoc. Les requêtes que vous créez à l’aide de ces portails peuvent être utilisées pour d’autres fonctionnalités telles que des alertes et des tableaux de bord.

## <a name="log-analytics-page-preview"></a>Page Log Analytics (préversion)
Ouvrez la page Log Analytics à partir de l’option **Journaux (préversion)** dans le menu Log Analytics. Il s’agit d’une nouvelle expérience pour utiliser les données de journal et créer des requêtes. Vous pouvez obtenir une introduction à ce portail et inspecter ses fonctionnalités dans la section [Prise en main de la page Log Analytics dans le portail Azure](query-language/get-started-analytics-portal.md).

La page Log Analytics apporte les améliorations suivantes à la [recherche dans les journaux](#log-search).

* Plusieurs onglets : créez des onglets distincts pour travailler avec plusieurs requêtes.
* Visualisations enrichies : diverses options de création de graphiques.
* Amélioration de la saisie semi-automatique Intellisense et de langage.
* Mise en surbrillance syntaxique : améliore la lisibilité des requêtes. 
* Explorateur de requêtes : requêtes et fonctions avec enregistrement d’accès.
* Vie Schéma : examinez la structure de vos données pour faciliter l’écriture des requêtes.
* Partager : créez des liens vers des requêtes, ou épinglez des requêtes sur n’importe quel tableau de bord Azure partagé.
* Analyse intelligente : identifie les pics dans vos graphiques et en analyse rapidement la cause.
* Sélection de colonnes : triez et regroupez les colonnes dans les résultats de la requête.

> [!NOTE]
> La page Log Analytics affiche les mêmes fonctionnalités que le portail Analytics avancé, un outil externe au portail Azure. Le portail Analytics avancé est toujours disponible, mais les liens et les autres références à celui-ci dans le portail Azure sont remplacés par cette nouvelle page.

![Portail Analytics avancé](media/log-analytics-log-search-portals/advanced-analytics-portal.png)


### <a name="firewall-requirements"></a>Configuration requise du pare-feu
Votre navigateur doit pouvoir accéder aux adresses suivantes pour pouvoir afficher la page Log Analytics et le portail Analytics avancé.  Si votre navigateur accède au portail Azure par le biais d’un pare-feu, vous devez activer l’accès à ces adresses.

| Uri | IP | Ports |
|:---|:---|:---|
| portal.loganalytics.io | Dynamique | 80,443 |
| api.loganalytics.io    | Dynamique | 80,443 |
| docs.loganalytics.io   | Dynamique | 80,443 |


## <a name="log-search"></a>Recherche dans les journaux
Ouvrez la page Recherche dans les journaux à partir de l’option **Journaux** du menu Log Analytics ou de l’option **Log Analytics** du menu Azure Monitor. Cette option permet d’analyser les données de journal à l’aide de requêtes de base. Elle fournit plusieurs fonctionnalités permettant de modifier les requêtes sans pour autant devoir connaître entièrement le langage utilisé dans celle-ci.  Vous pouvez obtenir un résumé de ces fonctionnalités dans la section [Créer des recherches dans les journaux dans Azure Log Analytics à l’aide du portail Recherche dans les journaux](log-analytics-log-search-log-search-portal.md). 


![Page Recherche dans les journaux](media/log-analytics-log-search-portals/log-search-portal.png)


## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la façon de créer des requêtes à l’aide du langage de requête, lisez un didacticiel sur l’utilisation de la page [Recherche dans les journaux](log-analytics-tutorial-viewdata.md)
- Passez en revue une [leçon utilisant le portail Analytics avancé](query-language/get-started-analytics-portal.md), qui fournit la même expérience que la page Log Analytics.

