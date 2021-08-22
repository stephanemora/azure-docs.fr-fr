---
title: Connecter des données Sophos Cloud Optix à Azure Sentinel | Microsoft Docs
description: Découvrez comment utiliser le connecteur Sophos Cloud Optix pour extraire des journaux de <PRODUCT NAME> dans Azure Sentinel. Affichez les données de <PRODUCT NAME> dans des classeurs, créez des alertes et améliorez l’investigation.
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
ms.date: 01/26/2021
ms.author: yelevin
ms.openlocfilehash: f16c76c6a29bed37fb21ebaf420b0c6c92f6ada7
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122534874"
---
# <a name="connect-your-sophos-cloud-optix-to-azure-sentinel"></a>Connecter Sophos Cloud Optix à Azure Sentinel

> [!IMPORTANT]
> Le connecteur Sophos Cloud Optix est actuellement en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Le connecteur Sophos Cloud Optix vous permet de connecter facilement tous les journaux de votre solution de sécurité Sophos Cloud Optix à Azure Sentinel, de consulter des tableaux de bord, de créer des alertes personnalisées et d’améliorer l’investigation.  Cette capacité vous donne davantage d’insights sur la posture de sécurité et de conformité cloud de votre organisation, et améliore les capacités de fonctionnement de votre sécurité cloud. L’intégration entre Sophos Cloud Optix et Azure Sentinel utilise une API REST.

> [!NOTE]
> Les données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="configure-and-connect-sophos-cloud-optix"></a>Configurer et connecter Sophos Cloud Optix

Sophos Cloud Optix peut intégrer et exporter des journaux d’activité directement vers Azure Sentinel.

1. Dans le portail Azure Sentinel, cliquez sur **Connecteurs de données**, puis sélectionnez **Sophos Cloud Optix (préversion)** .

1. Sélectionnez **Ouvrir la page du connecteur**.

1. Copiez et enregistrez l’**ID de l’ espace de travail** et la **clé primaire** à partir de la page du connecteur.

1. Suivez les instructions de Sophos pour l’[intégrer avec Microsoft Azure Sentinel](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/tasks/IntegrateAzureSentinel.html) (à partir de la troisième étape).

## <a name="find-your-data"></a>Recherche de données

Une fois la connexion établie, les données apparaissent dans **Journaux**, sous la section **Custom Logs** (Journaux personnalisés), dans la table *SophosCloudOptix_CL*.

Pour interroger les données de Sophos Cloud Optix, entrez `SophosCloudOptix_CL` dans la fenêtre de requête. Pour obtenir des exemples de requêtes utiles, consultez l’onglet **Étapes suivantes** dans la page du connecteur, et la [documentation de Sophos](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/concepts/ExampleAzureSentinelQueries.html).

## <a name="validate-connectivity"></a>Valider la connectivité

Jusqu’à 20 minutes peuvent être nécessaires avant que vos journaux ne commencent à apparaître dans Log Analytics. 

## <a name="next-steps"></a>Étapes suivantes

Ce document vous a expliqué comment connecter Sophos Cloud Optix à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](detect-threats-built-in.md).
- [Utilisez des classeurs](monitor-your-data.md) pour superviser vos données.