---
title: Exemples de modèles Resource Manager pour SQL Insights
description: Exemples de modèles Azure Resource Manager pour déployer et configurer SQL Insights.
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 03/25/2021
ms.openlocfilehash: dafb7335ef211cb9173ec2fb4565243d603d35fe
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730189"
---
# <a name="resource-manager-template-samples-for-sql-insights"></a>Exemples de modèles Resource Manager pour SQL Insights
Cet article comprend des exemples de [modèles Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) pour permettre à SQL Insights de superviser l’exécution de SQL dans Azure.  Pour plus d’informations sur l’offre et les versions de SQL prises en charge, consultez la [documentation de SQL Insights](sql-insights-overview.md). Chaque exemple comprend un fichier de modèle et un fichier de paramètres avec des exemples de valeurs à fournir au modèle.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="create-a-sql-insights-monitoring-profile"></a>Créer un profil de supervision SQL Insights
L’exemple suivant crée un profil de supervision SQL Insights, qui comprend les données de supervision SQL à collecter et la fréquence de collecte des données. Il spécifie également l’espace de travail auquel les données seront envoyées.


### <a name="template-file"></a>Fichier de modèle

Consultez le [fichier de modèle sur GitHub](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Create%20new%20profile/CreateNewProfile.armtemplate).

### <a name="parameter-file"></a>Fichier de paramètres

Consultez le [fichier de paramètres sur GitHub](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Create%20new%20profile/CreateNewProfile.parameters.json).


## <a name="add-a-monitoring-vm-to-a-sql-insights-monitoring-profile"></a>Ajouter une machine virtuelle de supervision à un profil de supervision SQL Insights
Une fois que vous avez créé un profil de supervision, vous devez allouer des machines virtuelles Azure qui seront configurées pour collecter à distance des données à partir des ressources SQL que vous spécifiez dans la configuration de cette machine virtuelle.  Pour plus d’informations, consultez la documentation sur l’activation de SQL Insights.

L’exemple suivant configure une machine virtuelle de supervision pour collecter les données à partir des ressources SQL spécifiées.


### <a name="template-file"></a>Fichier de modèle

Consultez le [fichier de modèle sur GitHub](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Add%20monitoring%20virtual%20machine/AddMonitoringVirtualMachine.armtemplate).

### <a name="parameter-file"></a>Fichier de paramètres

Consultez le [fichier de paramètres sur GitHub](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Add%20monitoring%20virtual%20machine/AddMonitoringVirtualMachine.parameters.json).


## <a name="create-an-alert-rule-for-sql-insights"></a>Créer une règle d’alerte pour SQL Insights
L’exemple suivant crée une règle d’alerte qui couvre les ressources SQL se trouvant dans l’étendue du profil de supervision spécifié.  Cette règle d’alerte s’affiche dans l’interface utilisateur de SQL Insights dans le panneau contextuel de l’interface utilisateur des alertes.  

Le fichier de paramètres contient des valeurs provenant de l’un des modèles d’alerte que nous fournissons dans SQL Insights. Vous pouvez le modifier pour qu’il signale d’autres données collectées pour SQL.  Le modèle ne spécifie pas de groupe d’actions pour la règle d’alerte.


#### <a name="template-file"></a>Fichier de modèle

Consultez le [fichier de modèle sur GitHub](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/Alerts/log-metric-noag.armtemplate).

### <a name="parameter-file"></a>Fichier de paramètres

Consultez le [fichier de paramètres sur GitHub](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/Alerts/sql-cpu-utilization-percent.parameters.json).





## <a name="next-steps"></a>Étapes suivantes

* [Obtenir d’autres exemples de modèle pour Azure Monitor](../resource-manager-samples.md).
* [En savoir plus sur SQL Insights](sql-insights-overview.md).
