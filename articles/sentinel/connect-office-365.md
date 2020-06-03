---
title: Connecter des données Office 365 à Azure Sentinel | Microsoft Docs
description: Découvrez comment connecter des données Office 365 à Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/12/2020
ms.author: yelevin
ms.openlocfilehash: c3e63063b3ea4e7fba3997ddd645aa59fe857488
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758569"
---
# <a name="connect-data-from-office-365-logs"></a>Connecter des données de journaux Office 365



Vous pouvez diffuser des journaux d’audit dans Azure Sentinel en un seul clic à partir d’[Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide). Vous pouvez diffuser en continu des journaux d’audit d’Office 365 vers votre espace de travail Azure Sentinel sur le même locataire. Le connecteur de journal d’activité Office 365 fournit des informations sur les activités de l’utilisateur en cours. Vous obtenez des informations sur plusieurs actions utilisateur, administrateur, système et de stratégie et d’événements d’Office 365. En connectant les journaux d’activité d’Office 365 dans Azure Sentinel, vous pouvez utiliser ces données pour afficher des tableaux de bord, créer des alertes personnalisées et améliorer votre processus d’investigation.

> [!IMPORTANT]
> Si vous disposez d’une licence E3, avant de pouvoir accéder aux données par le biais de l’API d’activité de gestion Office 365, vous devez activer la journalisation d’audit unifiée pour votre organisation Office 365. Pour ce faire, activez le journal d’audit Office 365. Pour obtenir des instructions, voir [Activer ou désactiver la recherche dans un journal d’audit Office 365](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off). Pour en savoir plus, consultez la section [Référence de l’API Activité de gestion Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference).

## <a name="prerequisites"></a>Prérequis

- Vous devez être un administrateur général ou un administrateur de la sécurité sur ce locataire.
- L’audit unifié doit être activé pour votre locataire. L’audit unifié est activé par défaut pour les locataires dotés des licences Office 365 E3 ou E5. <br>Si votre locataire ne dispose pas d’une de ces licences, vous devez activer l’audit unifié pour celui-ci à l’aide de l’une des méthodes suivantes :
    - [Utilisez le cmdlet Set-AdminAuditLogConfig](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps) et activez le paramètre « UnifiedAuditLogIngestionEnabled »).
    - [Utilisez l’interface utilisateur du Centre de sécurité et de conformité](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin).
   
   > [!NOTE]
   > Actuellement, le connecteur de données O365 ne capture automatiquement que l’activité Exchange et SharePoint, comme indiqué dans la page Connecteur de la section Types de données. Nous vous recommandons de consulter [cet article si vous avez besoin de données d’audit de Teams et de protéger Teams à l’aide de Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/protecting-your-teams-with-azure-sentinel/ba-p/1265761). 

## <a name="connect-to-office-365"></a>Connexion à Office 365

1. Dans Azure Sentinel, sélectionnez **Connecteurs de données** puis cliquez sur la vignette **Office 365**.

2. Si vous ne l’avez pas encore activé, vous pouvez le faire en accédant au panneau **Connecteurs de données**, puis en sélectionnant le connecteur **Office 365**. Ici, vous pouvez cliquer sur le lien **Open Connector Page** (Ouvrir la page des connecteurs) et, sous la section **Configuration**, sélectionnez tous les journaux d’activité Office 365 que vous souhaitez connecter à Azure Sentinel. 
   > [!NOTE]
   > Si vous avez déjà connecté plusieurs locataires dans une version précédemment prise en charge du connecteur Office 365 dans Azure Sentinel, vous serez en mesure d’afficher et de modifier les journaux collectés à partir de chaque locataire. Vous ne serez pas en mesure d’ajouter des locataires supplémentaires, mais vous pouvez supprimer les locataires ajoutés précédemment.
3. Pour utiliser le schéma pertinent dans Log Analytics pour les journaux d’activité Office 365, recherchez **OfficeActivity**.


## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter Office 365 à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).

