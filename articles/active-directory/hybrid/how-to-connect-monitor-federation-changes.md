---
title: Surveiller les modifications apportées à la configuration de la fédération dans Azure AD | Microsoft Docs
description: Cet article explique comment surveiller les modifications apportées à la configuration de la fédération avec Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/21/2021
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 7ffc5980eed3268f299ee0073cfa810c17878e53
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114229706"
---
# <a name="monitor-changes-to-federation-configuration-in-your-azure-ad"></a>Surveiller les modifications apportées à la configuration de la fédération

Lorsque vous fédérez votre environnement local avec Azure AD, vous établissez une relation de confiance entre le fournisseur d'identité local et Azure AD. 

Grâce à cette confiance établie, Azure AD honore le jeton de sécurité émis par le fournisseur d'identité local après l'authentification afin d'accorder l'accès aux ressources protégées par Azure AD. 

Il est donc essentiel que cette confiance (configuration de la fédération) soit surveillée de près et que toute activité inhabituelle ou suspecte soit capturée.

Pour surveiller la relation de confiance, nous vous recommandons de configurer des alertes afin d'être notifié lorsque des modifications sont apportées à la configuration de la fédération.


## <a name="set-up-alerts-to-monitor-the-trust-relationship"></a>Configurer des alertes pour surveiller la relation de confiance

Procédez comme suit afin de configurer des alertes pour surveiller la relation de confiance :

1. [Configurez les journaux d'audit Azure AD](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) pour qu'ils soient acheminés vers un espace de travail Azure Log Analytics. 
2. [Créez une règle d'alerte](../../azure-monitor/alerts/alerts-log.md) qui se déclenchera en fonction de la requête de journal Azure AD. 
3. [Ajoutez à la règle d'alerte un groupe d'actions](../../azure-monitor/alerts/action-groups.md) qui sera notifié lorsque la condition d'alerte sera remplie.  

Une fois l'environnement configuré, le flux des données est le suivant : 

 1. Les journaux Azure AD sont renseignés en fonction de l'activité du locataire.  
 2. Les informations de journal sont transmises à l'espace de travail Azure Log Analytics.  
 3. Un travail lancé en arrière-plan à partir d'Azure Monitor exécute la requête de journal en fonction de la règle d'alerte configurée à l'étape de configuration (2) ci-dessus.  
    ```
     AuditLogs 
     |  extend TargetResource = parse_json(TargetResources) 
     | where ActivityDisplayName contains "Set federation settings on domain" or ActivityDisplayName contains "Set domain authentication" 
     | project TimeGenerated, SourceSystem, TargetResource[0].displayName, AADTenantId, OperationName, InitiatedBy, Result, ActivityDisplayName, ActivityDateTime, Type 
     ```
     
 4. Si le résultat de la requête correspond à la logique d'alerte (c'est-à-dire que le nombre de résultats est supérieur ou égal à 1), le groupe d'actions est déclenché. Dans ce cas, le flux passe à l'étape 5.  
 5. La notification est envoyée au groupe d'actions sélectionné lors de la configuration de l'alerte.

 > [!NOTE]
 >  En plus de configurer des alertes, nous vous recommandons d'examiner régulièrement les domaines configurés au sein de votre locataire Azure AD et de supprimer tous les domaines obsolètes, non reconnus ou suspects. 




## <a name="next-steps"></a>Étapes suivantes

- [Intégrer les journaux Azure AD avec les journaux Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)
- [Créer, afficher et gérer des alertes de journal à l’aide d’Azure Monitor](../../azure-monitor/alerts/alerts-log.md)
- [Gérer l’approbation AD FS avec Azure AD à l’aide d’Azure AD Connect](how-to-connect-azure-ad-trust.md)
- [Meilleures pratiques en matière de services de fédération Active Directory (AD FS)](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs)