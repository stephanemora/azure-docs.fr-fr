---
title: Intégrer des journaux d’activité à partir de ressources Azure à vos systèmes SIEM | Microsoft Docs
description: Découvrez Azure Log Integration, ses fonctionnalités principales et son fonctionnement.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/28/2019
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 65e256b476c1e459ae937d9f6cbb43e0020fd9fe
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66298132"
---
# <a name="introduction-to-azure-log-integration"></a>Présentation d’Azure Log Integration

>[!IMPORTANT]
> La fonctionnalité d’intégration des journaux Azure sera déconseillée à partir du 15/06/2019. Les téléchargements AzLog ont été désactivés le 27 juin 2018. Pour obtenir des conseils pour évoluer, consultez la publication [Utiliser Azure Monitor pour intégrer avec des outils SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/). 

Azure Log Integration a été rendu disponible pour simplifier la tâche d’intégration des journaux d’activité Azure avec votre système local SIEM (Security Information and Event Management).

 La méthode recommandée pour l’intégration des journaux d’activité Azure doit utiliser les connecteurs du fournisseur de votre système SIEM. Azure Monitor permet de diffuser les journaux d’activité aux concentrateurs d’événements, et les fournisseurs de SIEM peuvent écrire des connecteurs pour intégrer davantage les journaux d’activité à partir du concentrateur d’événements dans le système SIEM.  Pour une description du fonctionnement, suivez les instructions données dans [Diffuser des données de surveillance Azure vers un hub d’événements](../azure-monitor/platform/stream-monitoring-data-event-hubs.md). Cet article répertorie également les systèmes SIEM pour lesquels les connecteurs Azure directs sont déjà disponibles.  

> [!IMPORTANT]
> Si vous êtes principalement intéressé par la collecte des journaux d’activité de machine virtuelle, la plupart des fournisseurs SIEM intègrent cette option à leur solution. L’utilisation du connecteur du fournisseur SIEM doit toujours être l’alternative privilégiée.

La documentation sur la fonctionnalité d’intégration des journaux Azure est toujours maintenue jusqu'à ce qu’elle soit déconseillée.

Pour en savoir plus sur la fonctionnalité d’intégration des journaux Azure :

Azure Log Integration collecte les événements Windows à partir des journaux de l’observateur d’événements, des [journaux d’activité Azure](../azure-monitor/platform/activity-logs-overview.md), des [alertes Azure Security Center](../security-center/security-center-intro.md) et des [journaux Diagnostics Azure](../azure-monitor/platform/diagnostic-logs-overview.md) des ressources Azure. Azure Log Integration permet à votre solution SIEM d’offrir un tableau de bord unifié pour toutes vos ressources, qu’elles soient locales ou dans le cloud. Vous pouvez utiliser un tableau de bord pour recevoir, agréger, mettre en corrélation et analyser des alertes pour des événements de sécurité.

> [!NOTE]
> Actuellement, Azure Log Integration prend uniquement en charge les clouds Azure Government et commerciaux Azure. Les autres clouds ne sont pas pris en charge.

![Le processus d’Azure Log Integration][1]

## <a name="what-logs-can-i-integrate"></a>Quels journaux d’activité puis-je intégrer ?

Azure génère une journalisation complète pour chaque service Azure. Les journaux d’activité sont de trois types :

* **Journaux d’activité de contrôle/gestion** : Fournissent une visibilité sur les opérations CREATE, UPDATE et DELETE [d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Un journal d’activité Azure est un exemple de ce type de journal.
* **Journaux d’activité de plan de données** : Fournissent une visibilité sur les événements qui sont déclenchés quand vous utilisez une ressource Azure. Les chaînes **Système**, **Sécurité** et **Application** de l’Observateur d’événements Windows sont des exemples de ce type de journal, dans une machine virtuelle Windows. La journalisation Diagnostics Azure, configurée via Azure Monitor, en est un autre exemple.
* **Événements traités** : Fournissent des informations sur les alertes et les événements traités pour vous. Les alertes Azure Security Center sont un exemple de ce type d’événement. Azure Security Center traite et analyse votre abonnement, afin de proposer des alertes pertinentes pour votre niveau de sécurité actuel.

Azure Log Integration prend en charge ArcSight, QRadar et Splunk. Renseignez-vous auprès de votre fournisseur SIEM pour déterminer s’il dispose d’un connecteur natif. N’utilisez pas Azure Log Integration si un connecteur natif est disponible.

Si aucune autre option n’est disponible, envisagez d’utiliser Azure Log Integration. Le tableau ci-après inclut nos recommandations :

|SIEM | Le client utilise déjà un intégrateur de journaux Azure | Le client examine les options d’intégration SIEM|
|---------|--------------------------|-------------------------------------------|
|**Splunk** | Commencez à effectuer une migration vers le [module complémentaire Azure Monitor pour Splunk](https://splunkbase.splunk.com/app/3534/). | Utilisez le [connecteur Splunk](https://splunkbase.splunk.com/app/3534/). |
|**QRadar** | Effectuez une migration vers le connecteur QRadar documenté dans la dernière section de [Diffuser des données de surveillance Azure vers un hub d’événements pour les utiliser dans un outil externe](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) ou commencez à l’utiliser. | Utilisez le connecteur QRadar documenté dans la dernière section de [Diffuser des données de surveillance Azure vers un hub d’événements pour les utiliser dans un outil externe](../azure-monitor/platform/stream-monitoring-data-event-hubs.md). |
|**ArcSight** | Continuez à utiliser l’intégrateur de journaux Azure jusqu’à ce qu’un connecteur soit disponible, puis effectuez la migration vers la solution basée sur le connecteur.  | Envisagez d’utiliser les journaux d'activité Azure Monitor en guise d'alternative. Ne recourez à Azure Log Integration que si vous êtes disposé à exécuter le processus de migration une fois que le connecteur deviendra disponible. |

> [!NOTE]
> Bien qu’Azure Log Integration soit une solution gratuite, le stockage des informations des fichiers de journaux est facturé, via Stockage Azure.

Si vous avez besoin d’une assistance, vous pouvez ouvrir une [demande de support](../azure-supportability/how-to-create-azure-support-request.md). Pour le service, sélectionnez **Intégration du journal**.

## <a name="next-steps"></a>Étapes suivantes

Cet article vous a présenté Azure Log Integration. Pour plus d’informations sur Azure Log Integration et les types de journaux d’activité pris en charge, consultez les articles suivants :

* [Intégration des journaux Azure avec Azure Diagnostics Logging et Windows Event Forwarding](security-azure-log-integration-get-started.md). Ce didacticiel vous guide tout au long de l’installation d’Azure Log Integration. Il décrit également comment intégrer des journaux du stockage Diagnostics Azure pour Windows (WAD), des journaux d’activité Azure, des alertes Azure Security Center et des journaux d’audit Azure Active Directory.
* [Forum aux questions sur l’intégration des journaux Azure](security-azure-log-integration-faq.md). Ce FAQ répond aux questions courantes sur Azure Log Integration.
* Découvrez comment [diffuser des données de surveillance Azure vers un hub d’événements pour les utiliser dans un outil externe](../azure-monitor/platform/stream-monitoring-data-event-hubs.md).

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
