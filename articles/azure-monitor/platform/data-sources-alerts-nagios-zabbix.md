---
title: Collecte d’alertes Nagios et Zabbix dans Azure Monitor| Microsoft Docs
description: Nagios et Zabbix sont des outils de surveillance open source. Vous pouvez collecter des alertes à partir de ces outils dans Azure Monitor, afin de les analyser avec des alertes provenant d’autres sources.  Cet article décrit comment configurer l’agent Log Analytics pour Linux pour la collecte d’alertes à partir de ces systèmes.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/27/2018
ms.author: magoedte
ms.openlocfilehash: ac8e214df6b6990e2b27b5897350c85e0a944e0c
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2019
ms.locfileid: "55997955"
---
# <a name="collect-alerts-from-nagios-and-zabbix-in-azure-monitor-from-log-analytics-agent-for-linux"></a>Collecte d’alertes de Nagios et Zabbix dans Azure Monitor à partir de l’agent Log Analytics pour Linux 
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

[Nagios](https://www.nagios.org/) et [Zabbix](http://www.zabbix.com/) sont des outils de surveillance open source. Vous pouvez collecter des alertes à partir de ces outils dans Azure Monitor, afin de les analyser avec des données de journal provenant d’autres sources.  Cet article décrit comment configurer l’agent Log Analytics pour Linux pour la collecte d’alertes à partir de ces systèmes.


> [!NOTE]
> Les [alertes créées par Azure Monitor](alerts-overview.md) sont stockées séparément des données de journal ; elles ne sont pas accessibles à partir des requêtes de journal.

 
## <a name="prerequisites"></a>Prérequis
L’agent Log Analytics pour Linux prend en charge la collecte d’alertes de Nagios jusqu’à la version 4.2.x et de Zabbix jusqu’à la version 2.x.

## <a name="configure-alert-collection"></a>Configuration de la collecte d’alertes

### <a name="configuring-nagios-alert-collection"></a>Configuration de la collecte d’alertes Nagios
Pour collecter des alertes, procédez comme suit sur le serveur Nagios.

1. Octroyez à l’utilisateur **omsagent** l’accès en lecture au fichier journal Nagios `/var/log/nagios/nagios.log`. Si le fichier nagios.log appartient au groupe `nagios`, vous pouvez ajouter l’utilisateur **omsagent** au groupe **nagios**. 

    sudo usermod -a -G nagios omsagent

2.  Modifiez le fichier de configuration dans `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`. Vérifiez que les entrées suivantes sont présentes et non mises en commentaire :  

        <source>  
          type tail  
          #Update path to point to your nagios.log  
          path /var/log/nagios/nagios.log  
          format none  
          tag oms.nagios  
        </source>  
      
        <filter oms.nagios>  
          type filter_nagios_log  
        </filter>  

3. Redémarrez le démon omsagent

    ```
    sudo sh /opt/microsoft/omsagent/bin/service_control restart
    ```

### <a name="configuring-zabbix-alert-collection"></a>Configuration de la collecte d’alertes Zabbix
Pour collecter les alertes à partir d’un serveur Zabbix, vous devez indiquer un utilisateur et un mot de passe en *texte clair*.  Même si ce n’est pas idéal, nous recommandons de créer un utilisateur Zabbix avec des autorisations en lecture seule pour intercepter les alarmes appropriées.

Procédez comme suit sur le serveur Nagios pour collecter les alertes.

1. Modifiez le fichier de configuration dans `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`. Vérifiez que les entrées suivantes sont présentes et non commentées.  Remplacez le nom d’utilisateur et le mot de passe par des valeurs pour votre environnement Zabbix.

        <source>
         type zabbix_alerts
         run_interval 1m
         tag oms.zabbix
         zabbix_url http://localhost/zabbix/api_jsonrpc.php
         zabbix_username Admin
         zabbix_password zabbix
        </source>

2. Redémarrez le démon omsagent

    `sudo sh /opt/microsoft/omsagent/bin/service_control restart`


## <a name="alert-records"></a>Enregistrements d’alerte
Vous pouvez récupérer les enregistrements d’alerte de Nagios et Zabbix à l’aide des [requêtes dans les journaux](../log-query/log-query-overview.md) dans Azure Monitor.

### <a name="nagios-alert-records"></a>Enregistrements d’alerte Nagios

Pour les enregistrements d’alerte collectés par Nagios, le **type** est **Alerte** et la valeur **SourceSystem** est **Nagios**.  Les propriétés des enregistrements sont décrites dans le tableau suivant.

| Propriété | Description |
|:--- |:--- |
| Type |*Alert* |
| SourceSystem |*Nagios* |
| AlertName |Nom de l’alerte. |
| AlertDescription | Description de l’alerte. |
| AlertState | État du service ou de l’hôte.<br><br>OK<br>AVERTISSEMENT<br>ACTIF<br>INACTIF |
| HostName | Nom de l’hôte qui a créé l’alerte. |
| PriorityNumber | Niveau de priorité de l’alerte. |
| StateType | Type d’état de l’alerte.<br><br>LÉGER : problème qui n’a pas été revérifié.<br>URGENT : problème qui a été revérifié un nombre spécifié de fois.  |
| TimeGenerated |Date et heure de la création de l’alerte. |


### <a name="zabbix-alert-records"></a>Enregistrements d’alerte Zabbix
Pour les enregistrements d’alerte collectés par Zabbix, le **type** est **Alerte** et la valeur **SourceSystem** est **Zabbix**.  Les propriétés des enregistrements sont décrites dans le tableau suivant.

| Propriété | Description |
|:--- |:--- |
| Type |*Alert* |
| SourceSystem |*Zabbix* |
| AlertName | Nom de l’alerte. |
| AlertPriority | Gravité de l’alerte.<br><br>non classée<br>information<br>Avertissement<br>average<br>élevée<br>urgence  |
| AlertState | État de l’alerte.<br><br>0 - l’état est à jour.<br>1 - l’état est inconnu.  |
| AlertTypeNumber | Indique si l’alerte peut générer plusieurs événements de problème.<br><br>0 - l’état est à jour.<br>1 - l’état est inconnu.    |
| Commentaires | Commentaires supplémentaires pour l’alerte. |
| HostName | Nom de l’hôte qui a créé l’alerte. |
| PriorityNumber | Valeur qui indique la gravité de l’alerte.<br><br>0 - non classée<br>1 - information<br>2 - avertissement<br>3 - moyenne<br>4 - élevée<br>5 - urgence |
| TimeGenerated |Date et heure de la création de l’alerte. |
| TimeLastModified |Date et heure de la dernière modification de l’état de l’alerte. |


## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur les [alertes](alerts-overview.md) dans Azure Monitor.
* Découvrez les [requêtes dans les journaux](../log-query/log-query-overview.md) pour analyser les données collectées à partir de sources de données et de solutions. 