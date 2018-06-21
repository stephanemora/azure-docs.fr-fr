---
title: Guide pour les données personnelles stockées dans Azure Log Analytics | Microsoft Docs
description: Cet article explique comment gérer les données personnelles stockées dans Azure Log Analytics et décrit les méthodes permettant de les identifier et les supprimer.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: magoedte
ms.openlocfilehash: 056779943d05ca743db63f1bc91be058cfae7b30
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659490"
---
# <a name="guidance-for-personal-data-stored-in-log-analytics"></a>Guide pour les données personnelles stockées dans Log Analytics

Log Analytics est une banque de données pouvant contenir des données personnelles. Cet article explique où se trouvent généralement ces données dans Log Analytics, ainsi que les fonctionnalités disponibles pour gérer ces données.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>Stratégie de gestion des données personnelles

S’il dépend au final de vous et de votre entreprise de déterminer la stratégie de gestion de vos données privées (le cas échéant), voici quelques approches possibles. Elles sont répertoriées par ordre de préférence décroissant d’un point de vue technique :

* Là où c’est possible, arrêtez la collecte des données, masquez, anonymisez ou réduisez les données collectées pour les exclure de ce qui peut être considéré comme « privé ». Il s’agit _de loin_ de la meilleure approche, qui vous évite de devoir créer une stratégie de gestion de données très coûteuse et avec un impact fort.
* Quand ce n’est pas possible, essayez de normaliser les données pour réduire l’impact sur la plateforme de données et les performances. Par exemple, au lieu de journaliser un ID utilisateur explicite, créez des données de recherche qui vont établir une corrélation entre d’une part le nom d’utilisateur et ses informations détaillées et d’autre part un ID interne, qui peuvent ensuite être enregistrés ailleurs. De cette façon, si un de vos utilisateurs vous demande de supprimer ses données personnelles, il est possible que la seule suppression de la ligne dans la table de recherche correspondant à l’utilisateur soit suffisante. 
* Enfin, si des données privées doivent être collectées, créez un processus autour du chemin de l’API de vidage et du chemin de l’API de la requête existante pour répondre à vos obligations liées à l’exportation et à la suppression des données privées associées à un utilisateur. 

## <a name="where-to-look-for-private-data-in-log-analytics"></a>Où rechercher des données privées dans Log Analytics ?

Log Analytics est un store flexible qui, tout en prescrivant un schéma pour vos données, vous permet de remplacer chaque champ par des valeurs personnalisées. En outre, tous les schémas personnalisés peuvent être ingérés. Ainsi, il est impossible de dire exactement où les données privées se trouvent dans votre espace de travail spécifique. Cependant, les emplacements suivants constituent de bons points de départ pour votre inventaire :

* *Adresses IP* : Log Analytics collecte plusieurs informations sur les adresses IP dans plusieurs tables différentes. Par exemple, la requête suivante affiche toutes les tables où des adresses IPv4 ont été collectées au cours des dernières 24 heures :
    ```
    search * 
    | where * matches regex @'\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)(\.|$)){4}\b' //RegEx originally provided on https://stackoverflow.com/questions/5284147/validating-ipv4-addresses-with-regexp
    | summarize count() by $table
    ```
* *ID d’utilisateur* : les ID d’utilisateur se trouvent dans une grande variété de solutions et de tables. Vous pouvez rechercher un nom d’utilisateur particulier dans tout votre jeu de données avec la commande de recherche :
    ```
    search "[username goes here]"
    ```
N’oubliez pas de rechercher non seulement les noms d’utilisateur explicites, mais aussi les GUID, qui peuvent permettre de remonter directement à un utilisateur particulier !
* *ID d’appareil* : comme les ID d’utilisateur, les ID d’appareil sont parfois considérés comme étant « privés ». Utilisez la même approche que ci-dessus pour les ID d’utilisateur, pour identifier les tables où cela peut poser un problème. 
* *Données personnalisées* : Log Analytics permet la collecte selon différentes méthodes : journaux personnalisés et champs personnalisés, [l’API du collecteur de données HTTP](log-analytics-data-collector-api.md) et les données personnalisées collectées dans le cadre de journaux des événements système. Tous ces éléments sont susceptibles de contenir des données privées et ils doivent être examinés pour vérifier si de telles données s’y trouvent.
* *Données capturées par les solutions* : comme le mécanisme des solutions est ouvert, nous vous recommandons d’examiner toutes les tables générées par les solutions pour vérifier leur conformité.

## <a name="how-to-export-and-delete-private-data"></a>Comment exporter et supprimer des données privées

Comme mentionné plus haut dans la section [Stratégie de gestion des données personnelles](#strategy-for-personal-data-handling), il est __fortement__ recommandé (si c’est possible) de restructurer votre stratégie de collecte des données pour éviter la collecte de données privées, de les masquer ou de les anonymiser, ou à défaut d’éviter qu’elles soient considérées comme « privées ». Une gestion des données va avant tout générer des coûts pour vous et votre équipe, pour définir et automatiser une stratégie et créer une interface destinée à vos clients qui leur permette d’interagir avec leurs données, ainsi que des coûts liés à la maintenance. De plus, elle est coûteuse en termes de ressources informatiques pour Log Analytics, et un grand nombre d’appels d’API de requête ou de vidage simultanés peut avoir un impact potentiellement négatif sur toutes les autres interactions avec les fonctionnalités de Log Analytics. Cela étant dit, il existe en effet des scénarios valides où des données privées doivent être collectées. Dans ces cas, les données doivent être gérées comme décrit dans cette section.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Afficher et exporter

Pour les demandes de visualisation et d’exportation de données, vous devez utiliser [l’API de requête](https://dev.loganalytics.io/). L’implémentation de la logique pour convertir la forme des données selon un format approprié pour vos utilisateurs dépend de vous. [Azure Functions](https://azure.microsoft.com/services/functions/) est l’endroit idéal pour héberger cette logique.

### <a name="delete"></a>Supprimer

> [!WARNING]
> Les suppressions dans Log Analytics sont destructrices et non réversibles ! Soyez très prudents quand vous les réalisez.

Nous avons rendu disponible un chemin d’API *de vidage* dans le cadre d’une gestion de la confidentialité. Ce chemin doit être utilisé avec prudence en raison du risque associé à cette opération, de l’impact potentiel sur les performances, et de la possibilité d’influer sur les agrégations, les mesures et d’autres aspects de vos données Log Analytics. Pour d’autres approches de la gestion des données privées, consultez la section [Stratégie de gestion des données personnelles](#strategy-for-personal-data-handling).

Le vidage est une opération nécessitant des privilèges élevés, qu’aucune application ni utilisateur dans Azure (y compris même le propriétaire de la ressource) n’a l’autorisation d’exécuter sans qu’un rôle lui soit explicitement accordé dans Azure Resource Manager. Ce rôle est _Videur de données_ et il doit être délégué avec prudence en raison du risque de perte de données. 

Une fois que le rôle Azure Resource Manager a été affecté, deux nouveaux chemins d’API sont disponibles : 

* [POST purge] (https://docs.microsoft.com/rest/api/loganalytics/workspaces%202015-03-20/purge) - prend un objet spécifiant les paramètres des données à supprimer et retourne un GUID de référence 
* GET purge status - l’appel de POST purge retourne un en-tête « x-ms-état-location » qui inclut une URL que vous pouvez appeler pour déterminer l’état de votre API de vidage. Par exemple : 

    ```
    x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/Microsoft.OperatonalInsights/workspaces/[WorkspaceName]/operations/purge-[PurgeOperationId]?api-version=2015-03-20
    ```

Alors que nous nous attendons à ce que la grande majorité des opérations de vidage soient effectuées beaucoup plus rapidement que ce que prévoit notre contrat SLA, en raison de leur impact important sur la plateforme de données utilisée par Log Analytics, le contrat SLA formel pour la réalisation des opérations de vidage est défini à 30 jours. 

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la façon dont les données sont collectées, traitées et sécurisées, consultez [Sécurité des données Log Analytics](log-analytics-data-security.md).