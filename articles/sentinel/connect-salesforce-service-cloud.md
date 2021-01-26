---
title: Connecter des données Salesforce Service Cloud à Azure Sentinel | Microsoft Docs
description: Découvrez comment utiliser le connecteur de données Salesforce Service Cloud pour tirer des journaux Salesforce dans Azure Sentinel. Affichez les données Salesforce dans des classeurs, créez des alertes et améliorez l’investigation.
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
ms.openlocfilehash: d323af2695a41e685e722c98603cf5df09866a15
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567819"
---
# <a name="connect-your-salesforce-service-cloud-to-azure-sentinel"></a>Connecter Salesforce Service Cloud à Azure Sentinel

> [!IMPORTANT]
> Le connecteur Salesforce Service Cloud est actuellement disponible en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

Cet article explique comment connecter votre solution Salesforce Service Cloud à Azure Sentinel. Le connecteur de données Salesforce Service Cloud vous permet de connecter facilement vos données Salesforce à Azure Sentinel, ce qui vous permet ensuite de les consulter dans des classeurs, de les utiliser pour créer des alertes personnalisées et de les incorporer pour améliorer l’investigation. L’intégration entre Salesforce Service Cloud et Azure Sentinel utilise l’API REST.

> [!NOTE]
> Les données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="prerequisites"></a>Prérequis

- Vous devez disposer d’autorisations en lecture et en écriture sur l’espace de travail Azure Sentinel.

- Vous devez disposer d’autorisations de lecture sur les clés partagées pour accéder à l’espace de travail. [En savoir plus sur les clés d’espace de travail](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key).

- Vous devez disposer d’autorisations en lecture et en écriture sur Azure Functions pour créer une application de fonction. [En savoir plus sur Azure Functions](/azure/azure-functions/).

- Vous devez disposer des informations d’identification suivantes de l’API REST Salesforce : **Nom d’utilisateur de l’API Salesforce**, **Mot de passe de l’API Salesforce**, **Jeton de sécurité Salesforce**, **Clé consommateur Salesforce**, **Secret consommateur Salesforce**. [En savoir plus sur l’API REST Salesforce](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/quickstart.htm).

## <a name="configure-and-connect-salesforce-service-cloud"></a>Configurer et connecter Salesforce Service Cloud

Salesforce Service Cloud peut intégrer et exporter des journaux directement dans Azure Sentinel.

1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Connecteurs de données**.

1. Dans la galerie **Connecteurs de données**, sélectionnez **Salesforce Service Cloud (préversion)** , puis sélectionnez **Ouvrir la page du connecteur**.

1. Suivez les étapes décrites dans la section **Configuration** de la page du connecteur.

## <a name="find-your-data"></a>Recherche de données

Une fois la connexion établie, les données s’affichent dans **Journaux**, sous la section **CustomLogs**, dans la table `SalesforceServiceCloud_CL`.

Consultez l’onglet **Étapes suivantes** de la page du connecteur pour obtenir des exemples de requêtes utiles.

## <a name="validate-connectivity"></a>Valider la connectivité

Jusqu’à 20 minutes peuvent être nécessaires avant que vos journaux ne commencent à apparaître dans Log Analytics.

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à connecter Salesforce Service Cloud à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilisez des classeurs](tutorial-monitor-your-data.md) pour superviser vos données.
