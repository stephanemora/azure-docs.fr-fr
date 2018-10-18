---
title: Gérer les alertes émanant d’autres services de supervision
description: Gérer les alertes Nagios, Zabbix et SCOM dans Azure Monitor
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: d9d0cb326fb063e0a6bbfaab6a85961ab2b35416
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49389370"
---
# <a name="manage-alerts-from-other-monitoring-services"></a>Gérer les alertes émanant d’autres services de supervision

Vous pouvez maintenant visualiser vos alertes depuis Nagios, Zabbix et System Center Operations Manager dans le [système central des alertes](https://aka.ms/azure-alerts-overview). Ces alertes sont issues des intégrations avec les serveurs Nagios/Zabbix ou System Center Operations Manager dans Log Analytics. 

## <a name="prerequisites"></a>Prérequis
Tous les enregistrements dans le référentiel Log Analytics ayant le type Alerte sont importés dans le système central des alertes. Vous devez donc effectuer la configuration nécessaire pour collecter ces enregistrements.
1. Pour les alertes **Nagios** et **Zabbix**, [configurez ces serveurs](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) pour envoyer des alertes à Log Analytics.
1. Pour les alertes **System Center Operations Manager**, [connectez votre groupe d’administration Operations Manager à votre espace de travail Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). Toutes les alertes créées dans System Center Operations Manager sont importées dans Log Analytics.

## <a name="view-your-alert-instances"></a>Afficher vos instances d’alertes
Après avoir configuré l’importation dans Log Analytics, vous pouvez commencer à afficher les instances d’alertes à partir de ces services de supervision dans le [système central des alertes](https://aka.ms/azure-alerts-overview). Une fois qu’elles sont présentes dans le système central des alertes, vous pouvez [gérer vos instances d’alertes](https://aka.ms/managing-alert-instances), [gérer les groupes intelligents créés sur ces alertes](https://aka.ms/managing-smart-groups) et [modifier l’état de vos alertes et groupes intelligents](https://aka.ms/managing-alert-smart-group-states).

> [!NOTE]
>  Les alertes Nagios dans le système central des alertes ne sont pas paramétrables. Par exemple, la [condition de supervision](https://aka.ms/azure-alerts-overview) d’une alerte ne passe pas de « Activé » à « Résolu ». Au lieu de cela, le « Activé » et « Résolu » sont affichés comme des instances d’alerte distinctes. 
