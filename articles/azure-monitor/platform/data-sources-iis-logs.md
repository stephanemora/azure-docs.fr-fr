---
title: Journaux IIS dans Azure Log Analytics | Microsoft Docs
description: Internet Information Services (IIS) enregistre l'activité des utilisateurs dans des fichiers journaux qui peuvent être collectés par Log Analytics.  Cet article décrit comment configurer la collecte des journaux IIS et les détails des enregistrements qu’ils créent dans l’espace de travail Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: cec5ff0a-01f5-4262-b2e8-e3db7b7467d2
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: bwren
ms.comopnent: ''
ms.openlocfilehash: e33c30f9de56c4c5dd5f898a6a5136bbcef36c18
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52336442"
---
# <a name="iis-logs-in-log-analytics"></a>Journaux IIS dans Log Analytics
Internet Information Services (IIS) enregistre l'activité des utilisateurs dans des fichiers journaux qui peuvent être collectés par Log Analytics.  

![Journaux IIS](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Configuration de journaux IIS
Log Analytics regroupant les entrées des fichiers journaux créés par IIS, vous devez [configurer IIS pour la journalisation](https://technet.microsoft.com/library/hh831775.aspx).

Log Analytics prend en charge uniquement les fichiers journaux IIS stockés au format W3C, et ne prend pas en charge les champs personnalisés ou IIS Advanced Logging.  
Log Analytics ne collecte pas de journaux aux formats natifs NCSA ou IIS.

Configurez les journaux IIS dans Log Analytics à partir du [menu Données dans Paramètres Log Analytics](agent-data-sources.md#configuring-data-sources).  Aucune configuration n’est requise autre que la sélection de l’option **Collecter les fichiers journaux IIS au format W3C**.


## <a name="data-collection"></a>Collecte des données
Log Analytics collecte les entrées de journal IIS à partir de chaque agent chaque fois que le journal est fermé et qu’un nouveau est créé. Cette fréquence est contrôlée par le paramètre de **planification de la substitution de fichier journal** pour le site IIS qui est, par défaut, défini sur une fois par jour. Par exemple, si le paramètre est **horaire**, Log Analytics collecte le journal toutes les heures.  Si le paramètre est **quotidien**, Log Analytics collecte le journal toutes les 24 heures.


## <a name="iis-log-record-properties"></a>Propriétés d’enregistrement de journal IIS
Les enregistrements de journal IIS sont de type **W3CIISLog** et leurs propriétés sont décrites dans le tableau suivant :

| Propriété | Description |
|:--- |:--- |
| Ordinateur |Nom de l'ordinateur à partir duquel l'événement a été collecté. |
| cIP |Adresse IP du client. |
| csMethod |Méthode de la requête, par exemple GET ou POST. |
| csReferer |Site à partir duquel l'utilisateur a suivi un lien vers le site actuel. |
| csUserAgent |Type de navigateur du client. |
| csUserName |Nom de l'utilisateur authentifié qui a accédé au serveur. Les utilisateurs anonymes sont indiqués par un trait d'union. |
| csUriStem |Cible de la requête, par exemple une page web. |
| csUriQuery |Requête, le cas échéant, que le client tentait d'effectuer. |
| ManagementGroupName |Nom du groupe d’administration pour les agents Operations Manager.  Pour les autres agents, il s’agit d’AOI-\<workspace ID\> |
| RemoteIPCountry |Pays de l'adresse IP du client. |
| RemoteIPLatitude |Latitude de l'adresse IP du client. |
| RemoteIPLongitude |Longitude de l'adresse IP du client. |
| scStatus |Code d'état HTTP. |
| scSubStatus |Code d'erreur du sous-état. |
| scWin32Status |Code d’état Windows. |
| sIP |Adresse IP du serveur web. |
| SourceSystem |OpsMgr |
| sPort |Port sur le serveur auquel client est connecté. |
| sSiteName |Nom du site IIS. |
| TimeGenerated |Date et heure de consignation de l'entrée. |
| TimeTaken |Délai de traitement de la requête en millisecondes. |

## <a name="log-searches-with-iis-logs"></a>Recherches de journaux avec les journaux IIS
Le tableau suivant fournit plusieurs exemples de requêtes de journaux qui extraient des enregistrements de journaux IIS.

| Requête | Description |
|:--- |:--- |
| W3CIISLog |Tous les enregistrements de journaux IIS. |
| W3CIISLog &#124; où scStatus==500 |Tous les enregistrements de journaux IIS dont l’état renvoyé est 500. |
| W3CIISLog &#124; résumer count() par cIP |Nombre d’entrées de journaux IIS par adresse IP du client. |
| W3CIISLog &#124; où csHost==« www.contoso.com » &#124; résumer count() par csUriStem |Nombre d’entrées de journaux IIS par URL pour l'hôte www.contoso.com. |
| W3CIISLog &#124; résumer sum(csBytes) par ordinateur &#124; prendre 500000 |Nombre total d'octets reçus par chaque ordinateur IIS. |

## <a name="next-steps"></a>Étapes suivantes
* Configurez Log Analytics pour collecter d’autres [sources de données](agent-data-sources.md) à analyser.
* Découvrez les [recherches de journaux](../../log-analytics/log-analytics-queries.md) pour analyser les données collectées à partir de sources de données et de solutions.
* Configurez les alertes dans Log Analytics pour être informé de façon proactive des conditions importantes rencontrées dans les journaux IIS.
