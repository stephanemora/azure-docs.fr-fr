---
title: Connecter votre serveur Apache HTTP Server à Azure Sentinel | Microsoft Docs
description: Découvrez comment utiliser le connecteur Apache HTTP Server pour extraire des journaux Apache dans Azure Sentinel. Affichez les données Apache dans des classeurs, créez des alertes et améliorez l’investigation.
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
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 6a1a2a2a7dac961e49e6ced38803649ebf5ad523
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100096852"
---
# <a name="connect-your-apache-http-server-to-azure-sentinel"></a>Connecter votre serveur Apache HTTP Server à Azure Sentinel

> [!IMPORTANT]
> Le connecteur Apache HTTP Server est actuellement disponible en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

Cet article explique comment connecter votre serveur Apache HTTP Server à Azure Sentinel. Le connecteur Apache HTTP Server vous permet d’ingérer facilement vos journaux Apache HTTP Server dans Azure Sentinel, pour ensuite afficher les données dans des classeurs, les interroger pour créer des alertes personnalisées et les incorporer pour améliorer l’investigation. L’intégration entre Apache HTTP Server et Azure Sentinel utilise le traitement local des fichiers par l’agent Log Analytics.

> [!NOTE]
> Les données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="prerequisites"></a>Prérequis

- Vous devez disposer de l’autorisation d’écriture sur l’espace de travail Azure Sentinel.

## <a name="configure-and-integrate-apache-http-server-logs-via-log-analytics-agent"></a>Configurer et intégrer les journaux Apache HTTP Server via l’agent Log Analytics

Configurez Apache HTTP Server de manière à envoyer des fichier journaux vers votre espace de travail Azure via l’agent Log Analytics.
Configurez l’agent Log Analytics de manière à lire les fichiers journaux Apache HTTP Server.

1. Suivez les instructions figurant dans https://httpd.apache.org/docs/2.4/logs.html pour configurer l’emplacement des fichiers journaux dans Apache HTTP Server.

1. Dans le menu de navigation Azure Sentinel, sélectionnez **Connecteurs de données**, puis **Apache HTTP Server (préversion)** .

1. Sélectionnez **Ouvrir la page du connecteur**.

1. Suivez les instructions de la page Apache HTTP Server.

## <a name="find-your-data"></a>Recherche de données

Une fois la connexion établie, les données apparaissent dans Log Analytics sous ApacheHTTPServer_CL.

## <a name="validate-connectivity"></a>Valider la connectivité

Jusqu’à 20 minutes peuvent être nécessaires avant que vos journaux ne commencent à apparaître dans Log Analytics.

## <a name="next-steps"></a>Étapes suivantes

Ce document vous a montré comment connecter Apache HTTP Server à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilisez des classeurs](tutorial-monitor-your-data.md) pour superviser vos données.
