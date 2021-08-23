---
title: Connecter votre appliance Imperva WAF Gateway à Azure Sentinel | Microsoft Docs
description: Découvrez comment utiliser le connecteur Imperva WAF Gateway pour extraire les journaux Imperva WAF dans Azure Sentinel. Affichez les données Imperva WAF dans des classeurs, créez des alertes et améliorez l’investigation.
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
ms.openlocfilehash: e6f68b249b9bedd638594bf6956e909ee8d792e5
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122563745"
---
# <a name="connect-your-imperva-waf-gateway-appliance-to-azure-sentinel"></a>Connecter votre appliance Imperva WAF Gateway à Azure Sentinel

> [!IMPORTANT]
> Le connecteur Imperva WAF Gateway est actuellement disponible en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Cet article explique comment connecter votre appliance Imperva WAF Gateway à Azure Sentinel. Le connecteur de données Imperva WAF Gateway vous permet de connecter facilement vos journaux Imperva WAF Gateway à Azure Sentinel, ce qui vous permet ensuite de consulter les données dans des classeurs, de les utiliser pour créer des alertes personnalisées et de les incorporer pour améliorer l’investigation. L’intégration entre Imperva WAF Gateway et Azure Sentinel utilise Syslog au format CEF, un redirecteur de journaux Linux, et l’agent Log Analytics.

> [!NOTE]
> Les données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="prerequisites"></a>Prérequis

- Vous devez disposer d’autorisations en lecture et en écriture dans l’espace de travail Azure Sentinel.

- Vous devez disposer d’autorisations de lecture sur les clés partagées pour accéder à l’espace de travail. [En savoir plus sur les clés d’espace de travail](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-imperva-waf-gateway-logs-to-azure-sentinel"></a>Envoyer les journaux Imperva WAF Gateway à Azure Sentinel

Pour récupérer ses journaux dans Azure Sentinel, configurez votre appliance Imperva WAF Gateway pour envoyer des messages Syslog au format CEF à un serveur de transfert de journaux basé sur Linux (exécutant rsyslog ou syslog-ng). L’agent Log Analytics sera installé sur ce serveur et transfèrera les journaux à votre espace de travail Azure Sentinel.

1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Connecteurs de données**.

1. Dans la galerie **Connecteurs de données**, sélectionnez **Imperva WAF Gateway (préversion)** , puis sélectionnez **Ouvrir la page du connecteur**.

1. Suivez les instructions de l’onglet **Instructions**, sous **Configuration** :

    1. Sous **1. Configurer l’agent Syslog Linux** : Effectuez cette étape si vous n’avez pas encore de redirecteur de journal en cours d’exécution, ou si vous en avez besoin d’un autre. Consultez [ÉTAPE 1 : Déployer le redirecteur de journal](connect-cef-agent.md) dans la documentation d’Azure Sentinel pour obtenir des instructions et des explications plus détaillées.

    1. Sous **2. Transférer les journaux CEF (Common Event Format) à l’agent Syslog** - Ce connecteur requiert la création d’une **Interface d’action** et d’un **ensemble d’actions** dans la console de gestion **Imperva SecureSphere MX**. Suivez les instructions d’Imperva pour [activer la journalisation des alertes Imperva WAF Gateway dans Azure Sentinel](https://community.imperva.com/blogs/craig-burlingame1/2020/11/13/steps-for-enabling-imperva-waf-gateway-alert). Cette configuration doit inclure les éléments suivants :
        - Destination du journal : nom d’hôte et/ou adresse IP de votre serveur de transfert de journaux
        - Protocole et port – TCP 514
        - Format du journal : CEF
        - Types de journaux : tous ceux disponibles

    1. Sous **3. Valider la connexion**  : Vérifiez l’ingestion des données en copiant la commande sur la page du connecteur et en l’exécutant sur votre redirecteur de journal. Consultez [ÉTAPE 3 : Valider la connectivité](connect-cef-verify.md) dans la documentation d’Azure Sentinel pour obtenir des instructions et des explications plus détaillées.

        Environ 20 minutes peuvent être nécessaires avant que vos journaux commencent à apparaître dans Log Analytics.

## <a name="find-your-data"></a>Recherche de données

Après l’établissement d’une connexion réussie, les données s’affichent dans **Journaux**, sous la section **Azure Sentinel** dans le tableau *CommonSecurityLog*.

Pour interroger les données Imperva WAF Gateway dans Log Analytics, copiez le code suivant dans la fenêtre de requête, en appliquant d’autres filtres au choix :

```kusto
CommonSecurityLog 
| where DeviceVendor == "Imperva Inc." 
| where DeviceProduct == "WAF Gateway" 
| where TimeGenerated == ago(5m)
```

Pour obtenir plus d’exemples de requêtes utiles, consultez l’onglet **Étapes suivantes** dans la page du connecteur.

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter Imperva WAF Gateway à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](detect-threats-built-in.md).
- [Utilisez des classeurs](monitor-your-data.md) pour superviser vos données.