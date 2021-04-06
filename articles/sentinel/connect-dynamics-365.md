---
title: Connecter des journaux Dynamics 365 à Azure Sentinel | Microsoft Docs
description: Apprenez à utiliser le connecteur d’activités Dynamics 365 Common Data Service (CDS) pour apporter des informations sur les activités d’administration, d’utilisateur et de support en cours.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2020
ms.author: yelevin
ms.openlocfilehash: 018368b6284cf39edec01f0a9a943b8ea15c85d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98103878"
---
# <a name="connect-dynamics-365-activity-logs-to-azure-sentinel"></a>Connecter des journaux d’activité Dynamics 365 à Azure Sentinel

Le connecteur d’activités [Dynamics 365](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description) Common Data Service (CDS) fournit des informations sur les activités d’administration, d’utilisateur et de support, ainsi que sur les événements de journalisation Microsoft Social Engagement. En connectant les journaux Dynamics 365 CRM à Azure Sentinel, vous pouvez afficher ces données dans des classeurs, les utiliser pour créer des alertes personnalisées et les exploiter pour améliorer votre processus d’investigation. Ce nouveau connecteur Azure Sentinel collecte les données de Dynamics CDS à partir de l’API de gestion Office. Pour en savoir plus sur les activités de Dynamics CDS auditées dans Power Platform, consultez [Activer et utiliser la journalisation des activités](/power-platform/admin/enable-use-comprehensive-auditing).

> [!IMPORTANT]
>
> Le connecteur d’activités Dynamics 365 Common Data Service (CDS) est actuellement disponible en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

## <a name="prerequisites"></a>Prérequis

- Vous devez disposer d’autorisations en lecture et en écriture dans l’espace de travail Azure Sentinel.

- Vous devez disposer d’une [licence de production Microsoft Dynamics 365](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description). Ce connecteur n’est pas disponible pour les environnements de bac à sable.
    - Un abonnement Microsoft 365 Entreprise [E3 ou E5](/power-platform/admin/enable-use-comprehensive-auditing#requirements) est requis pour la journalisation des activités.

- Pour extraire des données de l’API de gestion Office :
    - Vous devez être administrateur général sur votre locataire.

    - [La journalisation d’audit Office](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center) doit être activée dans [Centre de sécurité et de conformité Office](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance).

## <a name="enable-the-dynamics-365-activities-data-connector"></a>Activer le connecteur de données des activités Dynamics 365

1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Connecteurs de données**.

1. Dans la galerie **Connecteurs de données**, sélectionnez **Dynamics 365 (préversion)** , puis sélectionnez **Ouvrir la page du connecteur** dans le volet de visualisation.

1. Dans l’onglet **Instructions** , sous **Configuration**, cliquez sur **Connexion**. 

    Une fois le connecteur activé, il vous faudra environ 30 minutes pour voir les données arriver dans le graphique. 

    Selon le [journal d’audit Office dans le centre de conformité](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance#requirements-to-search-the-audit-log), il peut s’écouler jusqu’à 30 minutes ou jusqu’à 24 heures après qu’un événement se soit produit pour que l’enregistrement correspondant du journal d’audit soit renvoyé dans les résultats.

1. Les journaux d’audit Microsoft Dynamics se trouvent dans la table `Dynamics365Activity`. Voir la [documentation de référence sur le schéma](/azure/azure-monitor/reference/tables/dynamics365activity) de la table.

## <a name="querying-the-data"></a>Interrogation des données

1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Journaux**.

1. Exécutez la requête suivante pour vérifier l’arrivée des journaux :

    ```kusto
    Dynamics365Activity
    | take 10
    ```


## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter les données d’activités de Dynamics 365 à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Commencez à détecter les menaces avec Azure Sentinel à l’aide de règles [intégrées](tutorial-detect-threats-built-in.md) ou [personnalisées](tutorial-detect-threats-custom.md).
