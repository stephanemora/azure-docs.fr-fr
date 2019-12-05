---
title: Désintégrer Azure Sentinel | Microsoft Docs
description: Guide pratique pour supprimer votre instance Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2019
ms.author: rkarlin
ms.openlocfilehash: a45f273078a622de5e256457fc45b6cb6cae512f
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74464128"
---
# <a name="remove-azure-sentinel-from-your-tenant"></a>Supprimer Azure Sentinel de votre locataire

Si vous ne souhaitez plus utiliser Azure Sentinel, cet article explique comment le supprimer de votre locataire.

## <a name="how-to-delete-azure-sentinel"></a>Guide pratique pour supprimer Azure Sentinel

En arrière-plan, quand vous installez Azure Sentinel, la solution **SecurityInsights** est installée sur l’espace de travail que vous avez sélectionné. La première chose à faire est donc de supprimer la solution **SecurityInsights**.

1.  Accédez à **Azure Sentinel**. Sélectionnez ensuite **Configuration**, **Paramètres d’espace de travail**, puis **Solutions**.

2.  Sélectionnez `SecurityInsights`, puis cliquez dessus.

    ![Rechercher la solution SecurityInsights](media/offboard/find-solution.png)

3.  En haut de la page, sélectionnez **Supprimer**.

    > [!IMPORTANT]
    > Si vous supprimez l’espace de travail, cela risque d’affecter les autres solutions et sources de données qui utilisent cet espace de travail, notamment Azure Monitor. Pour vérifier les solutions qui utilisent cet espace de travail, consultez [Lister les solutions de monitoring installées](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions). Pour savoir quelles données de solutions sont ingérées dans l’espace de travail, consultez [Présentation du volume de données ingéré](../azure-monitor/platform/manage-cost-storage.md#understanding-ingested-data-volume).

    ![Supprimer la solution SecurityInsights](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>Que se passe-t-il en arrière-plan ?

Quand vous supprimez la solution, l’exécution de la première phase du processus de suppression par Azure Sentinel prend jusqu’à 48 heures.

Une fois la déconnexion identifiée, le processus d’annulation de l’intégration commence.

**La configuration de ces connecteurs est supprimée :**
-   Office 365

-   AWS

-   Alertes de sécurité des services Microsoft (Azure ATP, Microsoft Cloud App Security, ce qui inclut la génération de rapports Cloud Discovery Shadow IT, Azure AD Identity Protection, Microsoft Defender ATP, Azure Security Center)

-   Informations sur les menaces

-   Journaux de sécurité courants (notamment les journaux CEF, Barracuda et Syslog) (Si vous disposez d’Azure Security Center, ces journaux continueront d’être collectés.)

-   Événements de sécurité Windows (Si vous disposez d’Azure Security Center, ces journaux continueront d’être collectés.)

Au cours des 48 premières heures, les données et les règles d’alerte (dont la configuration de l’automatisation en temps réel) ne seront plus accessibles ou ne pourront plus être interrogées dans Azure Sentinel.

**Au bout de 30 jours, ces ressources sont supprimées :**

-   Incidents (dont les métadonnées d’enquête)

-   Règles d'alerte

-   Signets

Vos playbooks, classeurs enregistrés, requêtes de repérage enregistrées et notebooks ne sont pas supprimés. **Certains peuvent cesser de fonctionner en raison des données supprimées. Vous pouvez les supprimer manuellement.**

Une fois que vous avez supprimé le service, il y a une période de grâce de 30 jours pendant laquelle vous pouvez réactiver la solution. Vos données et règles d’alerte sont alors restaurées, mais les connecteurs configurés qui étaient déconnectés doivent être reconnectés.

> [!NOTE]
> Si vous supprimez la solution, votre abonnement reste inscrit auprès du fournisseur de ressources Azure Sentinel. **Vous pouvez le supprimer manuellement.**




## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à supprimer le service Azure Sentinel. Si vous changez d’avis et que vous voulez le réinstaller :
- Bien démarrer avec l’[intégration d’Azure Sentinel](quickstart-onboard.md).

