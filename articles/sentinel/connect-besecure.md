---
title: Connecter des données Beyond Security beSECURE à Azure Sentinel | Microsoft Docs
description: Découvrez comment utiliser le connecteur de données Beyond Security beSECURE pour extraire des journaux beSECURE dans Azure Sentinel. Affichez les données beSECURE dans des classeurs, créez des alertes et améliorez l’examen.
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
ms.openlocfilehash: c7daf98effad685d5e0044df3c416f3023fe42ba
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122534833"
---
# <a name="connect-your-beyond-security-besecure-to-azure-sentinel"></a>Connecter Beyond Security beSECURE à Azure Sentinel

> [!IMPORTANT]
> Le connecteur Beyond Security beSECURE est actuellement disponible en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Le connecteur Beyond Security beSECURE vous permet de connecter facilement tous les journaux de votre solution de sécurité beSECURE à Azure Sentinel, de consulter des tableaux de bord, de créer des alertes personnalisées et d’améliorer l’investigation. L’intégration entre beSECURE et Azure Sentinel utilise l’API REST.

> [!NOTE]
> Les données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="configure-and-connect-besecure"></a>Configurer et connecter beSECURE

beSECURE peut s’intégrer à Azure Sentinel et y exporter directement des journaux.

1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Connecteurs de données**.

1. Dans la galerie **Connecteurs de données**, sélectionnez **Beyond Security beSECURE (préversion)** , puis sélectionnez **Ouvrir la page du connecteur**.

1. Suivez les étapes ci-dessous pour configurer votre solution beSECURE afin d’envoyer des résultats d’analyse, l’état d’analyse et les journaux de piste d’audit à Azure Sentinel.

    **Accédez au menu Intégration :**
    1. Cliquer sur l’option de menu Plus

    1. Sélectionner un serveur

    1. Sélectionner Intégration

    1. Activer Azure Sentinel

    **Fournissez à beSECURE des paramètres Azure Sentinel :**

      Copiez les valeurs *ID de l’espace de travail* et *Clé primaire* de la page du connecteur Azure Sentinel, collez-les dans la configuration beSECURE, puis cliquez sur **Modifier**.
      
      :::image type="content" source="media/connectors/workspace-id-primary-key.png" alt-text="{ID d’espace de travail et clé primaire}":::

## <a name="find-your-data"></a>Recherche de données

Après l’établissement d’une connexion réussie, les données s’affichent dans **Journaux** sous la section **CustomLogs**, dans une ou plusieurs des tables suivantes :
  - `beSECURE_ScanResults_CL`
  - `beSECURE_ScanEvents_CL`
  - `beSECURE_Audit_CL`

Pour interroger les journaux beSECURE dans les règles d’analytique, les requêtes de chasse, les investigations ou n’importe où ailleurs dans Azure Sentinel, entrez l’un des noms de tables ci-dessus en haut de la fenêtre de requête.

## <a name="validate-connectivity"></a>Valider la connectivité
Jusqu’à 20 minutes peuvent être nécessaires avant que vos journaux ne commencent à apparaître dans Log Analytics.

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter beSECURE à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](detect-threats-built-in.md).
- [Utilisez des classeurs](monitor-your-data.md) pour superviser vos données.