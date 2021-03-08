---
title: Connecter les données de Broadcom Symantec Data Loss Prevention (DLP) à Azure Sentinel | Microsoft Docs
description: Découvrez comment utiliser le connecteur de données de Broadcom Symantec DLP afin d’extraire des journaux Symantec DLP dans Azure Sentinel. Affichez les données Symantec DLP dans des classeurs, créez des alertes et améliorez l’investigation.
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
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: 7f89780f2ed440898f5a28d78ec541c48a958b90
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101743042"
---
# <a name="connect-your-broadcom-symantec-data-loss-prevention-dlp-to-azure-sentinel"></a>Connecter votre Broadcom Symantec Data Loss Prevention (DLP) à Azure Sentinel

> [!IMPORTANT]
> Le connecteur Broadcom Symantec DLP est actuellement en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

Cet article explique comment connecter votre appliance Symantec DLP pour Broadcom à Azure Sentinel. Le connecteur de données Broadcom Symantec DLP vous permet de connecter facilement vos journaux Symantec DLP à Azure Sentinel, de consulter des tableaux de bord, de créer des alertes personnalisées et d’améliorer l’investigation. Cette capacité vous donne davantage d’insights sur les informations de votre organisation ainsi que leur circulation, et améliore vos capacités d’opération de sécurité. L’intégration entre Broadcom Symantec DLP et Azure Sentinel utilise Syslog au format CEF, un redirecteur de journaux Linux, et l’agent Log Analytics. Elle utilise également un analyseur de journal personnalisé basé sur une fonction Kusto.

> [!NOTE]
> Les données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="prerequisites"></a>Prérequis

- Vous devez disposer d’autorisations en lecture et en écriture dans l’espace de travail Azure Sentinel.

- Vous devez disposer d’autorisations de lecture sur les clés partagées pour accéder à l’espace de travail. [En savoir plus sur les clés d’espace de travail](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-broadcom-symantec-dlp-logs-to-azure-sentinel"></a>Envoyer des journaux Broadcom Symantec DLP à Azure Sentinel

Pour récupérer les journaux de votre appliance Broadcom Symantec DLP dans Azure Sentinel, configurez l’appliance pour envoyer des messages Syslog au format CEF à un serveur de transfert de journaux basé sur Linux (exécutant rsyslog ou syslog-ng). L’agent Log Analytics sera installé sur ce serveur et transfèrera les journaux à votre espace de travail Azure Sentinel.

1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Connecteurs de données**.

1. Dans la galerie **Connecteurs de données**, sélectionnez le connecteur **Broadcom Symantec DLP (préversion)** , puis **Ouvrir la page du connecteur**.

1. Suivez les instructions de l’onglet **Instructions**, sous **Configuration** :

    1. Sous **1. Configurer l’agent Syslog Linux** : Effectuez cette étape si vous n’avez pas encore de redirecteur de journal en cours d’exécution, ou si vous en avez besoin d’un autre. Consultez [ÉTAPE 1 : Déployer le redirecteur de journal](connect-cef-agent.md) dans la documentation d’Azure Sentinel pour obtenir des instructions et des explications plus détaillées.

    1. Sous **2. Transférer les journaux Symantec DLP à un agent Syslog** : suivez les instructions de Broadcom pour [configurer Symantec DLP](https://help.symantec.com/cs/DLP15.7/DLP/v27591174_v133697641/Configuring-the-Log-to-a-Syslog-Server-action?locale=EN_US). Cette configuration doit inclure les éléments suivants :
        - Destination du journal : nom d’hôte et/ou adresse IP de votre serveur de transfert de journaux
        - Protocole et port : TCP 514 (si cela est recommandé, veillez à effectuer la modification parallèle dans le démon Syslog sur votre serveur de transfert de journaux)
        - Format du journal : CEF
        - Types de journaux – tous disponibles ou appropriés

    1. Sous **3. Valider la connexion**  : Vérifiez l’ingestion des données en copiant la commande sur la page du connecteur et en l’exécutant sur votre redirecteur de journal. Consultez [ÉTAPE 3 : Valider la connectivité](connect-cef-verify.md) dans la documentation d’Azure Sentinel pour obtenir des instructions et des explications plus détaillées.

        Environ 20 minutes peuvent être nécessaires avant que vos journaux commencent à apparaître dans Log Analytics.

## <a name="find-your-data"></a>Recherche de données

Après l’établissement d’une connexion réussie, les données s’affichent dans **Journaux**, sous la section **Azure Sentinel** dans le tableau *CommonSecurityLog*.

Ce connecteur de données dépend d’un analyseur basé sur une fonction Kusto pour pouvoir fonctionner normalement. [Procédez comme suit](https://aka.ms/sentinel-symantecdlp-parser) pour créer l’alias de la fonction Kusto **SymantecDLP**.

Ensuite, pour interroger les données de Broadcom Symantec DLP dans Log Analytics, entrez `SymantecDLP` en haut de la fenêtre de requête.

Pour obtenir des exemples de requêtes utiles, consultez l’onglet **Étapes suivantes** dans la page du connecteur.

## <a name="next-steps"></a>Étapes suivantes

Ce document vous a montré comment connecter Symantec DLP à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilisez des classeurs](tutorial-monitor-your-data.md) pour superviser vos données.
