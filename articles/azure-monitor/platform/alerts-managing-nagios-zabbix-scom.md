---
title: Gérer les alertes provenant de System Center Operations Manager, Zabbix et Nagios dans Azure Monitor
description: Gérer les alertes provenant de System Center Operations Manager, Zabbix et Nagios dans Azure Monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.subservice: alerts
ms.openlocfilehash: 9fbad920bdfa26419e4b865db5d4e53dad97acce
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86539446"
---
# <a name="manage-alerts-from-system-center-operations-manager-zabbix-and-nagios-in-azure-monitor"></a>Gérer les alertes provenant de System Center Operations Manager, Zabbix et Nagios dans Azure Monitor

Vous pouvez maintenant visualiser vos alertes Nagios, Zabbix et System Center Operations Manager dans [Azure Monitor](https://aka.ms/azure-alerts-overview). Ces alertes sont issues des intégrations avec les serveurs Nagios/Zabbix ou System Center Operations Manager dans Log Analytics. 

## <a name="prerequisites"></a>Conditions préalables requises
Tous les enregistrements dans le référentiel Log Analytics ayant le type Alerte sont importés dans Azure Monitor. Vous devez donc effectuer la configuration nécessaire pour collecter ces enregistrements.
1. Pour les alertes **Nagios** et **Zabbix**, [configurez ces serveurs](../learn/quick-collect-linux-computer.md) pour [envoyer des alertes](./data-sources-custom-logs.md?toc=/azure/azure-monitor/toc.json) à Log Analytics.
1. Pour les alertes **System Center Operations Manager**, [connectez votre groupe d’administration Operations Manager à votre espace de travail Log Analytics](./om-agents.md). Ensuite, déployez la solution [Alert Management](./alert-management-solution.md) à partir de la Place de marché des solutions Azure. Quand vous avez terminé, toutes les alertes créées dans System Center Operations Manager sont importées dans Log Analytics.

## <a name="view-your-alert-instances"></a>Afficher vos instances d’alertes
Après avoir configuré l’importation dans Log Analytics, vous pouvez commencer à afficher les instances d’alertes à partir de ces services de supervision dans [Azure Monitor](https://aka.ms/azure-alerts-overview). Une fois qu’elles sont présentes dans Azure Monitor, vous pouvez [gérer vos instances d’alertes](https://aka.ms/managing-alert-instances), [gérer les groupes intelligents créés sur ces alertes](https://aka.ms/managing-smart-groups) et [modifier l’état de vos alertes et groupes intelligents](https://aka.ms/managing-alert-smart-group-states).

> [!NOTE]
>  1. Cette solution vous permet uniquement de voir les instances d’alertes déclenchées par System Center Operations Manager/Zabbix/Nagios dans Azure Monitor. La configuration de la règle d’alerte est uniquement consultable/modifiable dans les outils de supervision respectifs. 
>  1. Toutes les instances d’alertes déclenchées sont disponibles à la fois dans Azure Monitor et Azure Log Analytics. Actuellement, il n’existe aucun moyen de choisir entre les deux ou d’ingérer uniquement des alertes déclenchées spécifiques.
>  1. Toutes les alertes provenant de System Center Operations Manager, Zabbix et Nagios ont le type de signal « Inconnu » car le type de données de télémétrie sous-jacent n’est pas disponible.
>  1. Les alertes Nagios ne sont pas avec état : par exemple, la [condition de supervision](https://aka.ms/azure-alerts-overview) d’une alerte ne passe pas de « Déclenchée » à « Résolue ». Au lieu de cela, le « Activé » et « Résolu » sont affichés comme des instances d’alerte distinctes. 
