---
title: Journaux d’activité IIS dans Azure Monitor | Microsoft Docs
description: Internet Information Services (IIS) enregistre l'activité des utilisateurs dans des fichiers journaux qui peuvent être collectés par Azure Monitor.  Cet article décrit comment configurer la collecte des journaux d’activité IIS et des détails des enregistrements qu’ils créent dans Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: cec5ff0a-01f5-4262-b2e8-e3db7b7467d2
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: bwren
ms.openlocfilehash: 402cd4723791c0bc33db22c8857d1b785862f596
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797840"
---
# <a name="collect-iis-logs-in-azure-monitor"></a>Collecter des journaux d’activité IIS dans Azure Monitor
Internet Information Services (IIS) enregistre l'activité des utilisateurs dans des fichiers journaux qui peuvent être collectés par Azure Monitor et stockés en tant que [données de journal](data-platform.md).

![Journaux d’activité IIS](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Configuration de journaux d’activité IIS
Azure Monitor regroupe des entrées des fichiers journaux créés par IIS. Vous devez donc [configurer IIS pour la journalisation](https://technet.microsoft.com/library/hh831775.aspx).

Azure Monitor prend en charge uniquement les fichiers journaux IIS stockés au format W3C, et ne prend pas en charge les champs personnalisés ou IIS Advanced Logging. Il ne collecte pas les journaux d’activité au format natif NCSA ou IIS.

Configurez les journaux d’activité IIS dans Azure Monitor à partir du [menu des paramètres avancés](agent-data-sources.md#configuring-data-sources).  Aucune configuration n’est requise autre que la sélection de l’option **Collecter les fichiers journaux IIS au format W3C**.


## <a name="data-collection"></a>Collecte des données
Azure Monitor collecte les entrées de journal IIS à partir de chaque agent, à chaque fois que le journal est fermé et qu’un nouveau est créé. Cette fréquence est contrôlée par le paramètre de **planification de la substitution de fichier journal** pour le site IIS qui est, par défaut, défini sur une fois par jour. Par exemple, si le paramètre est **horaire**, Azure Monitor collecte le journal toutes les heures.  Si le paramètre est **quotidien**, Azure Monitor collecte le journal toutes les 24 heures.


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

## <a name="log-queries-with-iis-logs"></a>Enregistrer des requêtes avec les journaux d’activité IIS
Le tableau suivant fournit plusieurs exemples de requêtes de journaux qui extraient des enregistrements de journaux IIS.

| Requête | Description |
|:--- |:--- |
| W3CIISLog |Tous les enregistrements de journaux IIS. |
| W3CIISLog &#124; où scStatus==500 |Tous les enregistrements de journaux IIS dont l’état renvoyé est 500. |
| W3CIISLog &#124; résumer count() par cIP |Nombre d’entrées de journaux IIS par adresse IP du client. |
| W3CIISLog &#124; where csHost=="www\.contoso.com" &#124; summarize count() by csUriStem |Entrées de journal de nombre d’IIS par URL pour l’hôte www\.contoso.com. |
| W3CIISLog &#124; résumer sum(csBytes) par ordinateur &#124; prendre 500000 |Nombre total d'octets reçus par chaque ordinateur IIS. |

## <a name="next-steps"></a>Étapes suivantes
* Configurez Azure Monitor pour collecter d’autres [sources de données](agent-data-sources.md) à analyser.
* Découvrez les [requêtes dans les journaux](../log-query/log-query-overview.md) pour analyser les données collectées à partir de sources de données et de solutions.