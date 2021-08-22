---
title: Connecter BETTER Mobile Threat Defense (MTD) à Azure Sentinel | Microsoft Docs
description: Découvrez comment utiliser le connecteur de données BETTER Mobile Threat Defense (MTD) pour tirer les journaux MTD dans Azure Sentinel. Affichez les données MTD dans des classeurs, créez des alertes et améliorez l’investigation.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2021
ms.author: yelevin
ms.openlocfilehash: f3f5c2c2a22dd46d008a002db64c045af8132c8d
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122563714"
---
# <a name="connect-your-better-mobile-threat-defense-mtd-to-azure-sentinel"></a>Connecter BETTER Mobile Threat Defense (MTD) à Azure Sentinel

> [!IMPORTANT]
> Le connecteur BETTER Mobile Threat Defense (MTD) est actuellement disponible en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Le connecteur BETTER Mobile Threat Defense (MTD) vous permet de connecter facilement tous les journaux de votre solution de sécurité BETTER MTD à Azure Sentinel, de consulter des tableaux de bord, de créer des alertes personnalisées et d’améliorer l’investigation. L’intégration entre BETTER Mobile Threat Defense et Azure Sentinel utilise l’API REST.

> [!NOTE]
> Les données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="configure-and-connect-better-mobile-threat-defense"></a>Configurer et connecter BETTER Mobile Threat Defense

BETTER MTD peut intégrer et exporter des journaux directement dans Azure Sentinel.

1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Connecteurs de données**.

1. Dans la galerie **Connecteurs de données**, sélectionnez **BETTER Mobile Threat Defense (MTD) [préversion]** , puis sélectionnez **Ouvrir la page du connecteur**.

1. Suivez les étapes décrites sur la page du connecteur et sur [cette page de la documentation de BETTER MTD](https://mtd-docs.bmobi.net/integrations/azure-sentinel/setup-integration#mtd-integration-configuration) pour finaliser l’intégration sur la console BETTER MTD.

    Lorsque vous êtes invité à entrer les valeurs **ID de l’espace de travail** et **Clé primaire**, copiez-les à partir de la page du connecteur Azure Sentinel et collez-les dans la configuration BETTER MTD.

    :::image type="content" source="media/connectors/workspace-id-primary-key.png" alt-text="{ID d’espace de travail et clé primaire}":::

## <a name="find-your-data"></a>Recherche de données

Après l’établissement d’une connexion réussie, les données s’affichent dans **Journaux** sous la section **CustomLogs**, dans une ou plusieurs des tables suivantes :
- `BetterMTDDeviceLog_CL`
- `BetterMTDIncidentLog_CL`
- `BetterMTDAppLog_CL`
- `BetterMTDNetflowLog_CL`

Pour interroger les journaux BETTER MTD dans les règles d’analytique, les requêtes de chasse ou n’importe où ailleurs dans Azure Sentinel, entrez l’un des noms de tables ci-dessus en haut de la fenêtre de requête.

## <a name="validate-connectivity"></a>Valider la connectivité

Jusqu’à 20 minutes peuvent être nécessaires avant que vos journaux ne commencent à apparaître dans Log Analytics. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à connecter BETTER Mobile Threat Defense (MTD) à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](detect-threats-built-in.md).
- [Utilisez des classeurs](monitor-your-data.md) pour superviser vos données.