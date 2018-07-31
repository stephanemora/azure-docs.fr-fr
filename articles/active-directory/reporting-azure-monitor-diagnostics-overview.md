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
ms.openlocfilehash: 9b594360bc760fa9eec8ab9900c3aadcb10e9db6
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240102"
---
# <a name="azure-active-directory-activity-logs-in-azure-monitor-preview"></a>Journaux d’activité Azure Active Directory dans Azure Monitor (préversion)

À l’aide d’Azure Monitor, vous pouvez maintenant acheminer les journaux d’activité Azure AD vers votre propre compte de stockage ou Event Hub. Avec la préversion publique des journaux Azure Active Directory dans Azure Monitor, vous pouvez :

* Archiver vos journaux d’audit pour un compte de stockage Azure, ce qui vous permet de conserver les données pendant longtemps
* Diffuser en continu vos journaux d’audit vers un Event Hub Azure pour qu’ils soient analysés à l’aide d’outils SIEM populaires tels que Splunk et QRadar
* Intégrer vos journaux d’audit à vos propres solutions de journaux personnalisés en les diffusant en continu vers un Event Hub

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

## <a name="supported-reports"></a>Rapports pris en charge

Vous pouvez acheminer les journaux d’activité d’audit et les journaux d’activité de connexion vers votre compte de stockage Azure, votre Event Hub ou une solution personnalisée à l’aide de cette fonctionnalité. 

* **Journaux d’audit** : le [rapport d’activité de journaux d’audit](active-directory-reporting-activity-audit-logs.md) vous permet d’accéder à l’historique de toutes les tâches effectuées dans votre locataire.
* **Connexions** : le [rapport d’activité de connexions](active-directory-reporting-activity-sign-ins.md) vous permet d’identifier qui a effectué les tâches signalées par le rapport de journaux d’audit.

> [!NOTE]
> Les journaux d'activité d’audit et de connexions associés à B2C ne sont pas pris en charge pour le moment.
>

## <a name="prerequisites"></a>Prérequis

Pour utiliser cette fonctionnalité, vous avez besoin des éléments suivants :

* Un abonnement Azure. Si vous n’en avez pas, vous pouvez demander un [essai gratuit](https://azure.microsoft.com/free/).
* Une [licence](https://azure.microsoft.com/pricing/details/active-directory/) Azure AD Free, Basic, Premium 1 ou Premium 2 pour accéder aux journaux Azure AD dans le portail Azure. 

Selon où vous souhaitez acheminer les données du journal d’audit, vous devez disposer d’un des éléments suivants :

* Un compte de stockage Azure avec les autorisations *ListKeys* activées. Nous vous recommandons d'utiliser un compte de stockage général et pas un compte de stockage blob. Pour plus d’informations sur la tarification du stockage, utilisez la [calculatrice de prix pour le stockage Azure](https://azure.microsoft.com/pricing/calculator/?service=storage). 
* Un espace de noms Event Hubs Azure, pour l’intégration à des solutions tierces.

> [!NOTE]
> Vous devez être *administrateur général* ou *administrateur de la sécurité* dans le locataire Azure AD pour accéder aux journaux d’activité Azure AD.
>

## <a name="cost-considerations"></a>Considérations relatives au coût

Si vous avez déjà une licence Azure AD, vous avez besoin d’un abonnement Azure pour configurer le compte de stockage et l’Event Hub. L’abonnement Azure est fourni gratuitement, mais vous devez payer pour utiliser des ressources Azure, y compris le compte de stockage que vous utilisez pour l’archivage et l’Event Hub que vous utilisez pour la diffusion en continu. La quantité de données, et donc le coût, varient considérablement selon la taille du locataire. 

### <a name="storage-size-for-activity-logs"></a>Taille de stockage des journaux d’activité

Chaque événement du journal d’audit utilise environ 2 Ko de stockage de données. Par conséquent, pour un locataire avec 100 000 utilisateurs, qui impliquerait environ 1,5 million d’événements par jour, vous auriez besoin d’environ 3 Go de stockage de données par jour. Dans la mesure où les écritures se produisent dans des lots de 5 minutes environ, vous pouvez anticiper environ 9 000 opérations d’écriture par mois. Le tableau suivant contient une estimation approximative des coûts, selon la taille du locataire. Ces coûts sont calculés pour un compte de stockage à usage général v2 dans USA Ouest, pendant au moins un an de rétention. Utilisez la [calculatrice de prix de stockage Azure](https://azure.microsoft.com/pricing/details/storage/blobs/) pour obtenir une estimation plus précise pour le volume de données que vous prévoyez pour votre application. Chaque événement du journal d’audit utilise environ 2 Ko de stockage de données. Par conséquent, pour un locataire avec 100 000 utilisateurs, qui impliquerait environ 1,5 million d’événements par jour, vous auriez besoin d’environ 3 Go de stockage de données par jour. Dans la mesure où les écritures se produisent dans des lots de 5 minutes environ, vous pouvez anticiper environ 9 000 opérations d’écriture par mois. Le tableau suivant contient une estimation approximative des coûts, selon la taille du locataire. Ces coûts sont calculés pour un compte de stockage à usage général v2 dans USA Ouest, pendant au moins un an de rétention. Utilisez la [calculatrice de prix de stockage Azure](https://azure.microsoft.com/pricing/details/storage/blobs/) pour obtenir une estimation plus précise pour le volume de données que vous prévoyez pour votre application. 

| Catégorie de journal | Nombre d’utilisateurs | Nombre d'événements par jour | Volume approximatif de données par mois | Coût approximatif par mois | Coût approximatif par an |
|--------------|-----------------|----------------------|--------------------------------------|----------------------------|---------------------------|
| Audit | 100 000 | 1,5 million | 90 Go | 1,93 $ | 23,12 $ |
| Audit | 1 000 | 15000 | 900 Mo | 0,02 $ | 0,24 $ |
| Connexions | 1 000 | 34 800 | 4 Go | 0,13 $ | 1,56 $ |
| Connexions | 100 000 | 15 millions | 1,7 To | 35,41 $ | 424,92 $ | 


### <a name="event-hub-messages-for-activity-logs"></a>Messages Event Hub pour les journaux d’activité

Les événements sont regroupés dans des intervalles d’environ cinq minutes et sont envoyés sous forme de message unique contenant tous les événements au cours de cette période de temps. La taille maximale d’un message du Event Hub est de 256 Ko. Si la taille de tous les messages pendant cette période de temps dépasse ce volume, plusieurs messages sont envoyés. 

Par exemple, il y a environ 18 événements par seconde pour un locataire important de plus de 100 000 utilisateurs, ce qui correspond à 5 400 événements toutes les 5 minutes. Étant donné que les journaux d’audit sont d’environ 2 Ko par événement, cela équivaut à 10,8 Mo de données. Par conséquent, 43 messages seront envoyés au Event Hub dans cet intervalle de 5 minutes. Le tableau suivant contient les coûts approximatifs pour un Event Hub de base dans USA Ouest, en fonction du volume de données d’événement. Utilisez la [calculatrice de prix Event Hub](https://azure.microsoft.com/pricing/details/event-hubs/) pour obtenir une estimation plus précise pour le volume de données que vous prévoyez pour votre application.

| Catégorie de journal | Nombre d’utilisateurs | Nombre d'événements par seconde | Nombre d’événements par intervalle de 5 minutes | Volume par intervalle | Nombre de messages par intervalle | Nombre de messages par mois | Coût approximatif par mois |
|--------------|-----------------|-------------------------|----------------------------------------|---------------------|---------------------------------|------------------------------|----------------------------|
| Audit | 100 000 | 18 | 5400 | 10,8 Mo | 43 | 371,520 | 10,83 $ |
| Audit | 1 000 | 0.1 | 52 | 104 Ko | 1 | 8 640 | 10,8 $ |
| Connexions | 1 000 | 178 | 53 400 | 106,8 Mo | 418 | 3 611 520 | 11,06 $ |  


## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

Cette section contient les questions fréquemment posées et les problèmes connus avec les journaux Azure Active Directory dans Azure Monitor.

**Q : Par quoi dois-je commencer ?** 

**R :** Commencez par la [présentation](reporting-azure-monitor-diagnostics-overview.md) pour avoir une idée de ce dont vous avez besoin pour déployer cette fonctionnalité. Une fois que vous avez pris connaissance des prérequis, effectuez les didacticiels pour apprendre à configurer et acheminer vos journaux vers Event Hubs.

---

**Q : Quels sont les journaux inclus ?**

**R :** Les journaux d’audit et de connexion sont disponibles pour le routage via cette fonctionnalité. Néanmoins, les événements d’audit associés à B2C ne sont pas inclus à l’heure actuelle. Consultez le [schéma des journaux d’audit](reporting-azure-monitor-diagnostics-audit-log-schema.md) et le [schéma des journaux de connexion](reporting-azure-monitor-diagnostics-sign-in-log-schema.md) pour savoir quels types de journaux et quels journaux basés sur les fonctionnalités sont actuellement pris en charge. 

---

**Q : Combien de temps après une action les journaux correspondants s’affichent-ils dans Event Hubs ?**

**R :** Les journaux s’affichent dans Event Hubs dans un délai de deux à cinq minutes après l’exécution de l’action. Pour plus d’informations sur Event Hubs, consultez la section [Qu’est-ce qu’Event Hubs ?](/azure/event-hubs/event-hubs-what-is-event-hubs.md).

---

**Q : Combien de temps après une action les journaux correspondants s’affichent-ils dans les comptes de stockage ?**

**R :** Pour les comptes de stockage Azure, la latence est d’environ 5 à 15 minutes après l’exécution de l’action.

---

**Q : Combien me coûtera le stockage de mes données ?**

**R :** Le coût du stockage dépend de la taille de vos journaux, ainsi que de la période de rétention que vous choisissez. Pour obtenir une estimation des coûts pour les locataires selon le volume de journaux que vous générez, consultez la [présentation](reporting-azure-monitor-diagnostics-overview.md).

---

**Q : Quel est le coût de la diffusion en continu de mes données vers Event Hubs ?**

**R :** Le coût de la diffusion en continu dépend du nombre de messages reçus par minute. Consultez la [présentation](reporting-azure-monitor-diagnostics-overview.md) pour en savoir plus sur la façon dont le coût est calculé. Vous pourrez également obtenir une estimation du coût en fonction du nombre de messages. 

---

**Q : Quels sont les outils SIEM actuellement pris en charge ?** 

**R :** Pour le moment, Azure Monitor est pris en charge par Splunk, QRadar et Sumologic. Toutefois, les outils SIEM sont les seuls compatibles avec les journaux Azure Active Directory. Pour en savoir plus sur le fonctionnement des connecteurs, consultez la section [Diffuser des données de surveillance Azure vers un Event Hub pour les utiliser dans un outil externe](/azure/monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs).

---

**Q : Puis-je accéder aux données d’un Event Hub sans avoir recours à un outil SIEM externe ?** 

**R :** Oui, vous pouvez utiliser l’[API Event Hub](/azure/event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph) pour accéder aux journaux à partir de votre application personnalisée. 

---


## <a name="next-steps"></a>Étapes suivantes

* [Archiver les journaux d’activité dans un compte de stockage](reporting-azure-monitor-diagnostics-azure-storage-account.md)
* [Acheminer les journaux d’activité vers un Event Hub](reporting-azure-monitor-diagnostics-azure-event-hub.md)
* [En savoir plus sur les journaux de diagnostic Azure](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)