---
title: Journaux d’activité Azure Active Directory dans Azure Monitor (préversion) | Microsoft Docs
description: Présentation des journaux d’activité Azure Active Directory dans Azure Monitor (préversion)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 0d88aef46761e8c7d8217f04befec88816587c03
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358256"
---
# <a name="azure-ad-activity-logs-in-azure-monitor-preview"></a>Journaux d’activité Azure Active Directory dans Azure Monitor (préversion)

À l’aide d’Azure Monitor, vous pouvez maintenant acheminer les journaux d’activité Azure AD vers votre propre compte de stockage ou Event Hub. Avec la préversion publique des journaux d’activité Azure Active Directory disponible dans Azure Monitor, vous pouvez :

* Archiver vos journaux d’audit d’un compte de stockage Azure, ce qui vous permet de conserver les données longtemps.
* Diffuser vos journaux d’audit vers un Event Hub Azure pour analyser des données à l’aide des outils populaires de Security Information and Event Management (SIEM), comme Splunk et QRadar.
* Intégrer vos journaux d’audit à vos propres solutions de journaux d’activité personnalisés en les diffusant en continu vers un Event Hub.

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

## <a name="supported-reports"></a>Rapports pris en charge

Vous pouvez acheminer les journaux d’activité d’audit et authentifier des journaux d’activité vers votre compte de stockage Azure, un Event Hub ou une solution personnalisée à l’aide de cette fonctionnalité. 

* **Journaux d’audit** : les [rapports d’activité de journaux d’audit](active-directory-reporting-activity-audit-logs.md) vous permettent d’accéder à l’historique de toutes les tâches effectuées dans votre locataire.
* **Journaux d’identification** : le [rapport d’activité de connexions](active-directory-reporting-activity-sign-ins.md) vous permet d’identifier qui a effectué les tâches signalées par les journaux d’audit.

> [!NOTE]
> Les journaux d’activité d’audit et de connexions associés à B2C ne sont pas pris en charge pour le moment.
>

## <a name="prerequisites"></a>Prérequis

Pour utiliser cette fonctionnalité, vous avez besoin des éléments suivants :

* Un abonnement Azure. Si vous n’en avez pas, vous pouvez demander un [essai gratuit](https://azure.microsoft.com/free/).
* Une [licence](https://azure.microsoft.com/pricing/details/active-directory/) Azure AD Free, Basic, Premium 1 ou Premium 2 permet d’accéder aux journaux d’activité Azure AD dans le portail Azure. 

Selon l’endroit où vous souhaitez acheminer les données du journal d’audit, vous devez disposer de l’un des éléments suivants :

* Un compte de stockage Azure doté des autorisations *ListKeys* activées. Nous vous recommandons d’utiliser un compte de stockage général et pas un compte de stockage blob. Pour plus d’informations sur la tarification du stockage, utilisez la [Calculatrice de prix pour le stockage Azure](https://azure.microsoft.com/pricing/calculator/?service=storage). 
* Un espace de noms Azure Event Hubs pour intégrer avec des solutions tierces.

> [!NOTE]
> Vous devez être *administrateur général* ou *administrateur de la sécurité* dans le locataire Azure AD pour accéder aux journaux d’activité Azure AD.
>

## <a name="cost-considerations"></a>Considérations relatives au coût

Si vous avez déjà une licence Azure AD, vous avez besoin d’un abonnement Azure pour configurer le compte de stockage et l’Event Hub. L’abonnement Azure est fourni gratuitement, mais vous devez payer pour utiliser des ressources Azure, y compris le compte de stockage que vous utilisez pour l’archivage, et l’Event Hub que vous utilisez pour la diffusion en continu. La quantité de données, et donc le coût, varient considérablement selon la taille du locataire. 

### <a name="storage-size-for-activity-logs"></a>Taille de stockage des journaux d’activité

Chaque événement du journal d’audit utilise environ 2 Ko de stockage de données. Par conséquent, pour un locataire possédant 100 000 utilisateurs, et qui impliquerait environ 1,5 million d’événements par jour, vous auriez besoin d’environ 3 Go de stockage de données par jour. Dans la mesure où les écritures sont réalisées par lots de cinq minutes environ, vous pouvez anticiper environ 9000 opérations d’écriture par mois. 

Le tableau suivant contient une estimation des coûts, selon la taille du locataire. Ces coûts sont calculés pour un compte de stockage à usage général v2 dans la région USA Ouest, pendant au moins un an de rétention. Utilisez la [Calculatrice de prix de stockage Azure](https://azure.microsoft.com/pricing/details/storage/blobs/) pour obtenir une estimation plus précise du volume de données que vous prévoyez pour votre application. 

| Catégorie de journal | Nombre d’utilisateurs | Événements par jour | Volume de données par mois (estimation) | Coût par mois (estimation) | Coût par an (estimation) |
|--------------|-----------------|----------------------|--------------------------------------|----------------------------|---------------------------|
| Audit | 100 000 | 1,5&nbsp;million | 90 Go | 1,93 $ | 23,12 $ |
| Audit | 1 000 | 15 000 | 900 Mo | 0,02 $ | 0,24 $ |
| Connexions | 1 000 | 34800 | 4 Go | 0,13 $ | 1,56 $ |
| Connexions | 100 000 | 15&nbsp;millions | 1,7 To | 35,41 $ | 424,92 $ | 


### <a name="event-hub-messages-for-activity-logs"></a>Messages Event Hub pour les journaux d’activité

Les événements sont regroupés dans des intervalles d’environ cinq minutes et sont envoyés sous forme de message unique contenant tous les événements apparus au cours de cette période de temps. La taille maximale d’un message dans le Event Hub est de 256 Ko. Si la taille de tous les messages pendant cette période de temps dépasse ce volume, plusieurs messages sont envoyés. 

Par exemple, on compte normalement environ 18 événements par seconde pour un locataire important de plus de 100 000 utilisateurs, un taux qui équivaut à 5 400 événements toutes les cinq minutes. Étant donné que les journaux d’audit font environ 2 Ko par événement, cela équivaut à 10.8 Mo de données. Par conséquent, 43 sont envoyés à l’Event Hub dans cet intervalle de cinq minutes. 

Le tableau suivant contient les coûts approximatifs pour un Event Hub de base dans l’ouest des États-Unis, en fonction du volume de données d’événement. Utilisez la [Calculatrice de prix Event Hub](https://azure.microsoft.com/pricing/details/event-hubs/) pour obtenir une estimation plus précise du volume de données que vous prévoyez pour votre application.

| Catégorie de journal | Nombre d’utilisateurs | Événements par seconde | Événements par intervalle de cinq minutes | Volume par intervalle | Messages par intervalle | Messages par mois | Coût par mois (estimation) |
|--------------|-----------------|-------------------------|----------------------------------------|---------------------|---------------------------------|------------------------------|----------------------------|
| Audit | 100 000 | 18 | 5400 | 10,8 Mo | 43 | 371,520 | 10,83 $ |
| Audit | 1 000 | 0.1 | 52 | 104 Ko | 1 | 8640 | 10,80 $ |
| Connexions | 1 000 | 178 | 53400 | 106,8&nbsp;MO | 418 | 3 611 520 | 11,06 $ |  


## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

Cette section répond aux questions fréquemment posées et traitent des problèmes rencontrés avec les journaux d’activité Azure Active Directory dans Azure Monitor.

**Q : Par quoi dois-je commencer ?** 

**R** : Cet article décrit les fonctionnalités que vous devez déployer. Une fois que vous avez rempli les conditions préalables, accédez aux didacticiels qui peuvent aider à configurer et à acheminer vos journaux d’activité vers un Event Hub.

---

**Q : Quels sont les journaux inclus ?**

**R** : Les journaux d’audit et d’activité de connexion sont disponibles pour le routage via cette fonctionnalité. Néanmoins, les événements d’audit associés à B2C ne sont pas inclus à l’heure actuelle. Consultez le [Schéma des journaux d’audit](reporting-azure-monitor-diagnostics-audit-log-schema.md) et le [Schéma des journaux d’activité de connexion](reporting-azure-monitor-diagnostics-sign-in-log-schema.md) pour savoir quels types de journaux d’activité et quelles fonctionnalités de ces journaux d’activité sont actuellement pris en charge. 

---

**Q : Combien de temps après une action les journaux correspondants s’affichent-ils dans Event Hubs ?**

**R** : les journaux apparaissent normalement dans votre Event Hub dans les deux à cinq minutes après que l’action soit effectuée. Pour plus d’informations sur Event Hubs, consultez la section [Qu’est-ce qu’Event Hubs ?](../event-hubs/event-hubs-about.md).

---

**Q : Combien de temps après une action les journaux correspondants s’affichent-ils dans les comptes de stockage ?**

**R** : Pour les comptes de stockage Azure, la latence est d’environ 5 à 15 minutes après l’exécution de l’action.

---

**Q : Combien me coûtera le stockage de mes données ?**

**R** : Les coûts du stockage dépendent de la taille de vos journaux, ainsi que de la période de rétention que vous choisissez. Pour obtenir la liste de l’estimation des coûts pour les clients, qui dépendent du volume de journaux d’activité générés, consultez la section [Taille de stockage des journaux d’activité](https://review.docs.microsoft.com/en-us/azure/active-directory/reporting-azure-monitor-diagnostics-overview?branch=pr-en-us-47660#storage-size-for-activity-logs).

---

**Q : Quel est le coût de la diffusion en continu de mes données vers un Event Hubs ?**

**R** : Le coût de la diffusion en continu dépend du nombre de messages reçus par minute. Cet article explique comment sont calculés les coûts et répertorie les estimations de coûts, qui sont basées sur le nombre de messages. 

---

**Q : Quels sont les outils SIEM actuellement pris en charge ?** 

**R** : Pour le moment, Azure Monitor est pris en charge par Splunk, QRadar et Sumo Logic. Toutefois, Splunk est le seul outil SIEM compatible avec les journaux d’activité Azure Active Directory. Pour en savoir plus sur le fonctionnement des connecteurs, consultez la section [Diffuser des données de surveillance Azure vers un Event Hub pour les utiliser dans un outil externe](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md).

---

**Q : Puis-je accéder aux données d’un Event Hub sans avoir recours à un outil SIEM externe ?** 

**R** : Oui. Vous pouvez utiliser l’[API Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md) pour accéder aux journaux d’activité à partir de votre application personnalisée. 

---


## <a name="next-steps"></a>Étapes suivantes

* [Archiver les journaux d’activité dans un compte de stockage](reporting-azure-monitor-diagnostics-azure-storage-account.md)
* [Acheminer les journaux d’activité vers un Event Hub](reporting-azure-monitor-diagnostics-azure-event-hub.md)
* [En savoir plus sur les journaux de diagnostic Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)
