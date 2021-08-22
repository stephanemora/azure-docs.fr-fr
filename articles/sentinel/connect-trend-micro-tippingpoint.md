---
title: Connecter Trend Micro TippingPoint à Azure Sentinel | Microsoft Docs
description: Découvrez comment utiliser le connecteur de données Trend Micro TippingPoint pour extraire des journaux TippingPoint SMS dans Azure Sentinel. Affichez les données TippingPoint dans des classeurs, créez des alertes et améliorez l’examen.
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
ms.openlocfilehash: d59637f446b70b0c4de253edc96b2b3f2921d387
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122563749"
---
# <a name="connect-your-trend-micro-tippingpoint-solution-to-azure-sentinel"></a>Connectez votre solution Trend Micro TippingPoint à Azure Sentinel

> [!IMPORTANT]
> Le connecteur Trend Micro TippingPoint est actuellement disponible en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Cet article explique comment connecter votre solution de système Trend Micro TippingPoint Threat Protection System à Azure Sentinel. Le connecteur de données Trend Micro TippingPoint vous permet de connecter facilement vos journaux TippingPoint Security Management System (SMS) à Azure Sentinel, ce qui vous permet ensuite de consulter les données dans des classeurs, de les utiliser pour créer des alertes personnalisées et de les incorporer pour améliorer l’investigation.

> [!NOTE]
> Les données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="prerequisites"></a>Prérequis

- Vous devez disposer d’autorisations en lecture et en écriture dans l’espace de travail Azure Sentinel.

- Vous devez disposer d’autorisations de lecture sur les clés partagées pour accéder à l’espace de travail.

## <a name="send-trend-micro-tippingpoint-logs-to-azure-sentinel"></a>Envoyer des journaux Trend Micro TippingPoint à Azure Sentinel

Pour récupérer ses journaux dans Azure Sentinel, configurez votre solution TippingPoint TPS pour envoyer des messages Syslog au format CEF à votre serveur de transfert de journaux basé sur Linux (exécutant rsyslog ou syslog-ng). L’agent Log Analytics sera installé sur ce serveur et transfèrera les journaux à votre espace de travail Azure Sentinel. Le connecteur utilise une fonction d’analyseur pour convertir les données qu’il reçoit en schéma normalisé. 

1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Connecteurs de données**.

1. Dans la galerie **Connecteurs de données**, sélectionnez **Trend Micro TippingPoint (préversion)** , puis sélectionnez **Ouvrir la page du connecteur**.

1. Suivez les instructions de l’onglet **Instructions**, sous **Configuration** :

    1. Sous **1. Configurer l’agent Syslog Linux** : Effectuez cette étape si vous n’avez pas encore de redirecteur de journal en cours d’exécution, ou si vous en avez besoin d’un autre. Consultez [ÉTAPE 1 : Déployer le redirecteur de journal](connect-cef-agent.md) dans la documentation d’Azure Sentinel pour obtenir des instructions et des explications plus détaillées.

    1. Sous **2. Transférer les journaux Trend Micro TippingPoint SMS à l’agent Syslog** : Cette configuration doit inclure les éléments suivants :
        - Destination du journal : nom d’hôte et/ou adresse IP de votre serveur de transfert de journaux
        - Protocole et port : **TCP 514** (si cela est recommandé, veillez à effectuer la modification parallèle dans le démon Syslog sur votre serveur de transfert de journaux)
        - Format du journal : **format CEF ArcSight v4.2**
        - Types de journaux : tous ceux disponibles

    1. Sous **3. Valider la connexion**  : Vérifiez l’ingestion des données en copiant la commande sur la page du connecteur et en l’exécutant sur votre redirecteur de journal. Consultez [ÉTAPE 3 : Valider la connectivité](connect-cef-verify.md) dans la documentation d’Azure Sentinel pour obtenir des instructions et des explications plus détaillées.

        Environ 20 minutes peuvent être nécessaires avant que vos journaux commencent à apparaître dans Log Analytics.

## <a name="find-your-data"></a>Recherche de données

Après l’établissement d’une connexion réussie, les données s’affichent dans **Journaux**, sous la section **Azure Sentinel** dans le tableau *CommonSecurityLog*.

Pour obtenir les données de Trend Micro TippingPoint dans Log Analytics, vous allez interroger la fonction d’analyseur au lieu de la table. Copiez le code suivant dans la fenêtre de requête, en appliquant d’autres filtres au choix :

```kusto
TrendMicroTippingPoint
| sort by TimeGenerated
```

Pour obtenir plus d’exemples de requêtes, consultez l’onglet **Étapes suivantes** dans la page du connecteur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à connecter Trend Micro TippingPoint à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](detect-threats-built-in.md).
- [Utilisez des classeurs](monitor-your-data.md) pour superviser vos données.