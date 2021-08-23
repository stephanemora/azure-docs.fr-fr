---
title: Connecter des données Squid Proxy à Azure Sentinel | Microsoft Docs
description: Découvrez comment utiliser le connecteur de données Squid Proxy pour tirer des journaux Squid Proxy dans Azure Sentinel. Affichez les données Squid Proxy dans des classeurs, créez des alertes et améliorez l’investigation.
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
ms.date: 01/17/2021
ms.author: yelevin
ms.openlocfilehash: 4bf5d3c4a4ad70f5fc5f09c50144b759900370b6
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122563740"
---
# <a name="connect-your-squid-proxy-to-azure-sentinel"></a>Connecter Squid Proxy à Azure Sentinel

> [!IMPORTANT]
> Le connecteur Squid Proxy est actuellement disponible en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Cet article explique comment connecter votre appliance Squid Proxy à Azure Sentinel. Le connecteur de données Squid Proxy vous permet de connecter facilement vos journaux Squid à Azure Sentinel, ce qui vous permet ensuite de consulter les données dans des classeurs, de les utiliser pour créer des alertes personnalisées et de les incorporer pour améliorer l’investigation. L’intégration entre Squid Proxy et Azure Sentinel utilise le traitement local des fichiers par l’agent Log Analytics.

> [!NOTE]
> Les données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="prerequisites"></a>Prérequis

- Vous devez disposer d’autorisations en lecture et en écriture sur l’espace de travail Azure Sentinel.

## <a name="forward-squid-proxy-logs-to-the-log-analytics-agent"></a>Transférer des journaux Squid Proxy à l’agent Log Analytics  

Configurez Squid Proxy de manière à envoyer des fichier journaux vers votre espace de travail Azure via l’agent Log Analytics.

1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Connecteurs de données**.

1. Dans la galerie **Connecteurs de données**, sélectionnez **Squid Proxy (préversion)** , puis sélectionnez **Ouvrir la page du connecteur**.

1. Suivez les instructions de la page du connecteur **Squid Proxy** :

    1. Installer et intégrer l’agent pour Linux

        - Choisissez une machine virtuelle Linux Azure ou une machine Linux non Azure (physique ou virtuelle).

    1. Configurer les journaux à collecter

        - Dans les paramètres avancés de l’espace de travail, ajoutez un type de journal personnalisé, chargez un exemple de fichier et configurez-le comme indiqué.

## <a name="find-your-data"></a>Recherche de données

Une fois la connexion établie, les données s’affichent dans **Journaux**, sous **CustomLogs**, dans la table `SquidProxy_CL`.

Consultez l’onglet **Étapes suivantes** de la page du connecteur pour obtenir des exemples de requêtes utiles.

## <a name="validate-connectivity"></a>Valider la connectivité

Jusqu’à 20 minutes peuvent être nécessaires avant que vos journaux ne commencent à apparaître dans Log Analytics. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à connecter Squid Proxy à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](detect-threats-built-in.md).
- [Utilisez des classeurs](monitor-your-data.md) pour superviser vos données.