---
title: Portails servant à la création et la modification des requêtes de journal dans Azure Log Analytics | Microsoft Docs
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
ms.date: 06/11/2018
ms.author: magoedte; bwren
ms.component: na
ms.openlocfilehash: e2ea0bf1fb3f1c63f4e6f037e465e8fdfd9a4374
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37133350"
---
# <a name="portals-for-creating-and-editing-log-queries-in-azure-log-analytics"></a>Portails servant à la création et la modification des requêtes de journal dans Azure Log Analytics

Utilisez les recherches dans les journaux à plusieurs emplacements de Log Analytics pour récupérer des données à partir de l’espace de travail.  Toutefois, pour la création et la modification de requêtes, ainsi que le travail interactif avec des données renvoyées, vous disposez des deux options décrites ci-dessous.  

## <a name="log-search"></a>Recherche dans les journaux 
La page Recherche dans les journaux est accessible depuis le portail Azure.  Il convient pour la création de requêtes de base pouvant être créées sur une seule ligne.  Il est possible d’utiliser la page Recherche dans les journaux sans ouvrir de portail externe. Vous pouvez en outre l’utiliser pour exécuter diverses fonctions avec recherches dans les journaux, y compris la création de règles d’alerte, la création de groupes d’ordinateurs et l’exportation des résultats de la requête.  

La page Recherche dans les journaux fournit plusieurs fonctionnalités permettant de modifier la requête sans pour autant devoir connaître entièrement le langage utilisé dans celle-ci.  Vous pouvez obtenir un résumé de ces fonctionnalités dans la section [Créer des recherches dans les journaux dans Azure Log Analytics à l’aide du portail Recherche dans les journaux](log-analytics-log-search-log-search-portal.md).


![Page Recherche dans les journaux](media/log-analytics-log-search-portals/log-search-portal.png)

## <a name="advanced-analytics-portal"></a>Portail Analytics avancé
Le portail d’analytique avancée est un portail dédié qui propose des fonctionnalités avancées non disponibles sur la page Recherche dans les journaux du portail Azure.  Ces fonctionnalités incluent la possibilité de modifier une requête sur plusieurs lignes et d’exécuter de façon sélective du code, des données Intellisense contextuelles et Smart Analytics.  Le portail Analytics avancé est plus approprié pour la conception des requêtes complexes qui sont soit enregistrées sous la forme d’une recherche dans les journaux soit copiées et collées dans d’autres éléments de Log Analytics.  Ouvrez le portail d’analytique avancée à partir du lien figurant dans la page Recherche dans les journaux.

![Portail Analytics avancé](media/log-analytics-log-search-portals/advanced-analytics-portal.png)


En raison de ses fonctionnalités avancées, vous utiliserez généralement le portail Analytics avancé comme votre principal outil de création et modification des requêtes.  Une fois que vous avez la certitude que la requête fonctionne comme prévu, copiez-la et collez-la à un autre emplacement, par exemple dans la page Recherche dans les journaux ou dans le concepteur de vues.  

### <a name="firewall-requirements"></a>Configuration requise du pare-feu
Votre navigateur doit pouvoir accéder aux adresses suivantes pour pouvoir utiliser le portail Analytics avancé.  Si votre navigateur accède au portail Azure par le biais d’un pare-feu, vous devez activer l’accès à ces adresses.

| Uri | IP | Ports |
|:---|:---|:---|
| portal.loganalytics.io | Dynamique | 80,443 |
| api.loganalytics.io    | Dynamique | 80,443 |
| docs.loganalytics.io   | Dynamique | 80,443 |


## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la façon de créer des requêtes à l’aide du langage de requête, lisez un didacticiel sur l’utilisation de la page [Recherche dans les journaux](log-analytics-tutorial-viewdata.md).
- Pour créer des requêtes sophistiquées et les utiliser comme environnement de développement pour vos recherches dans les journaux, consultez le [portail d’analytique avancée](https://go.microsoft.com/fwlink/?linkid=856587).

