---
title: Connecter des données Office 365 à Azure Sentinel | Microsoft Docs
description: Découvrez comment connecter des données Office 365 à Azure Sentinel.
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
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: df5aade7244f69e7264f901364ecc164351eec50
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815787"
---
# <a name="connect-data-from-office-365-logs"></a>Connecter des données de journaux Office 365



Vous pouvez diffuser des journaux d’audit dans Azure Sentinel en un seul clic à partir d’[Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide). Vous pouvez diffuser des journaux d’audit dans un espace de travail unique d’Azure Sentinel à partir de plusieurs locataires. Le connecteur de journal d’activité Office 365 fournit des informations sur les activités de l’utilisateur en cours. Vous obtenez des informations sur plusieurs actions utilisateur, administrateur, système et de stratégie et d’événements d’Office 365. En connectant les journaux d’activité d’Office 365 dans Azure Sentinel, vous pouvez utiliser ces données pour afficher des tableaux de bord, créer des alertes personnalisées et améliorer votre processus d’investigation.

> [!IMPORTANT]
> Si vous disposez d’une licence E3, avant de pouvoir accéder aux données par le biais de l’API d’activité de gestion Office 365, vous devez activer la journalisation d’audit unifiée pour votre organisation Office 365. Pour ce faire, activez le journal d’audit Office 365. Pour obtenir des instructions, voir [Activer ou désactiver la recherche dans un journal d’audit Office 365](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off). Pour en savoir plus, consultez la section [Référence de l’API Activité de gestion Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference).

## <a name="prerequisites"></a>Prérequis

- Vous devez être un administrateur général ou un administrateur de la sécurité sur ce locataire
- Sur votre ordinateur, à partir duquel vous vous êtes connecté à Azure Sentinel pour créer la connexion, assurez-vous que le port 4433 est ouvert pour le trafic web. Ce port peut être refermé une fois la connexion établie.
- Si votre locataire ne dispose pas d’une licence Office 365 E3 ou Office 365 E5, vous devez activer l’audit unifié pour celui-ci à l’aide de l’un des processus suivants :
    - [Utilisez le cmdlet Set-AdminAuditLogConfig](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps) et activez le paramètre « UnifiedAuditLogIngestionEnabled »).
    - [Ou utilisez l’interface utilisateur du Centre de sécurité et de conformité](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin).

## <a name="connect-to-office-365"></a>Connexion à Office 365

1. Dans Azure Sentinel, sélectionnez **Connecteurs de données** puis cliquez sur la vignette **Office 365**.

2. Si vous ne l’avez pas encore activé, vous pouvez le faire en accédant au panneau **Connecteurs de données**, puis en sélectionnant le connecteur **Office 365**. Ici, vous pouvez cliquer sur **Ouvrir la page du connecteur**, puis sous la section de configuration intitulée **Activer la solution Office 365 sur votre espace de travail**, utilisez le bouton **Installer la solution** pour l’activer. Si elle a déjà été activée, elle est identifiée dans l’écran de connexion comme étant déjà activée.
1. Office 365 vous permet de diffuser des données dans Azure Sentinel à partir de plusieurs locataires. Pour chaque locataire auquel vous souhaitez vous connecter, ajoutez le locataire sous **Connect tenants to Azure Sentinel** (Connecter des locataires à Azure Sentinel). 
1. Un écran Active Directory s’ouvre. Vous êtes invité à vous authentifier avec un utilisateur administrateur général sur chaque locataire que vous souhaitez connecter à Azure Sentinel, et à fournir des autorisations sur Azure Sentinel pour lire ses journaux. 
5. Dans la liste des locataires, vous pouvez voir l’ID d’annuaire Azure AD (ID de locataire) et deux cases à cocher pour les journaux Exchange et SharePoint. Vous pouvez sélectionner un seul ou tous les services listés que vous voulez ingérer dans Sentinel. Actuellement, Azure Sentinel prend en charge les journaux Exchange et SharePoint dans les services Office 365 existants.

4. Une fois que vous avez sélectionné les services (Exchange, SharePoint, etc.), vous pouvez cliquer sur Enregistrer dans le cadre d’ajout de locataire se trouvant sur la page. 

3. Pour utiliser le schéma pertinent dans Log Analytics pour les journaux d’activité Office 365, recherchez **OfficeActivity**.


## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter Office 365 à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).

