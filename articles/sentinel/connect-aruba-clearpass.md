---
title: Connecter des données d’Aruba ClearPass à Azure Sentinel | Microsoft Docs
description: Découvrez comment utiliser le connecteur Aruba ClearPass pour extraire des journaux d’Aruba ClearPass dans Azure Sentinel. Affichez les données d’Aruba ClearPass dans des classeurs, créez des alertes et améliorez l’investigation.
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
ms.date: 02/28/2021
ms.author: yelevin
ms.openlocfilehash: 1b03e2d8715b3d80425ef181dd908978d5df4cf2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101743187"
---
# <a name="connect-your-aruba-clearpass-to-azure-sentinel"></a>Connecter votre Aruba ClearPass à Azure Sentinel

> [!IMPORTANT]
> Le connecteur Aruba ClearPass est actuellement en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

Cet article explique comment connecter votre appliance Aruba ClearPass à Azure Sentinel. Le connecteur de données Aruba ClearPass vous permet de connecter facilement vos journaux Aruba ClearPass à Azure Sentinel, de façon à pouvoir afficher les données dans des classeurs, les interroger pour créer des alertes personnalisées, et les incorporer pour améliorer l’investigation. L’intégration entre Aruba ClearPass et Azure Sentinel utilise Syslog au format CEF, un redirecteur de journaux Linux, et l’agent Log Analytics. Elle utilise également un analyseur de journal personnalisé basé sur une fonction Kusto.

> [!NOTE]
> Les données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="prerequisites"></a>Prérequis

- Vous devez disposer d’autorisations en lecture et en écriture dans l’espace de travail Azure Sentinel.

- Vous devez disposer d’autorisations de lecture sur les clés partagées pour accéder à l’espace de travail. [En savoir plus sur les clés d’espace de travail](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-aruba-clearpass-logs-to-azure-sentinel"></a>Envoyer les journaux d’Aruba ClearPass à Azure Sentinel

Pour récupérer les journaux de votre appliance Aruba ClearPass dans Azure Sentinel, configurez l’appliance pour envoyer des messages Syslog au format CEF à un serveur de transfert de journaux basé sur Linux (exécutant rsyslog ou syslog-ng). L’agent Log Analytics sera installé sur ce serveur et transfèrera les journaux à votre espace de travail Azure Sentinel.

1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Connecteurs de données**.

1. Dans la galerie **Connecteurs de données**, sélectionnez **Aruba ClearPass (préversion)** , puis **Ouvrir la page du connecteur**.

1. Suivez les instructions de l’onglet **Instructions**, sous **Configuration** :

    1. Sous **1. Configurer l’agent Syslog Linux** : Effectuez cette étape si vous n’avez pas encore de redirecteur de journal en cours d’exécution, ou si vous en avez besoin d’un autre. Consultez [ÉTAPE 1 : Déployer le redirecteur de journal](connect-cef-agent.md) dans la documentation d’Azure Sentinel pour obtenir des instructions et des explications plus détaillées.

    1. Sous **2. Transférer les journaux d’Aruba ClearPass vers un agent syslog** : suivez les instructions d’Aruba pour [configurer ClearPass](https://www.arubanetworks.com/techdocs/ClearPass/6.7/PolicyManager/Content/CPPM_UserGuide/Admin/syslogExportFilters_add_syslog_filter_general.htm). Cette configuration doit inclure les éléments suivants :
        - Destination du journal : nom d’hôte et/ou adresse IP de votre serveur de transfert de journaux
        - Protocole et port : TCP 514 (si cela est recommandé, veillez à effectuer la modification parallèle dans le démon Syslog sur votre serveur de transfert de journaux)
        - Format du journal : CEF
        - Types de journaux – Tous disponibles ou appropriés

    1. Sous **3. Valider la connexion**  : Vérifiez l’ingestion des données en copiant la commande sur la page du connecteur et en l’exécutant sur votre redirecteur de journal. Consultez [ÉTAPE 3 : Valider la connectivité](connect-cef-verify.md) dans la documentation d’Azure Sentinel pour obtenir des instructions et des explications plus détaillées.

        Environ 20 minutes peuvent être nécessaires avant que vos journaux commencent à apparaître dans Log Analytics.

## <a name="find-your-data"></a>Recherche de données

Après l’établissement d’une connexion réussie, les données s’affichent dans **Journaux**, sous la section **Azure Sentinel** dans le tableau *CommonSecurityLog*.

Ce connecteur de données dépend d’un analyseur basé sur une fonction Kusto pour pouvoir fonctionner normalement. [Procédez comme suit](https://aka.ms/sentinel-arubaclearpass-parser) pour créer l’alias de la fonction Kusto **ArubaClearPass**.

Ensuite, pour interroger les données d’Aruba ClearPass dans Log Analytics, entrez `ArubaClearPass` en haut de la fenêtre de requête.

Pour obtenir des exemples de requêtes utiles, consultez l’onglet **Étapes suivantes** dans la page du connecteur.

## <a name="next-steps"></a>Étapes suivantes

Ce document vous a montré comment connecter Aruba ClearPass à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilisez des classeurs](tutorial-monitor-your-data.md) pour superviser vos données.
