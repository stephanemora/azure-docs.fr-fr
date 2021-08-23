---
title: Connecter WireX Network Forensics Platform (NFP) à Azure Sentinel | Microsoft Docs
description: Découvrez comment utiliser le connecteur de données WireX Systems NFP pour tirer vos journaux WireX NFP dans Azure Sentinel. Affichez les données WireX NFP dans des classeurs, créez des alertes et améliorez l’investigation.
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
ms.openlocfilehash: 42af2f2f4598e871f2fd736971d78b970eda8dac
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122525928"
---
# <a name="connect-your-wirex-network-forensics-platform-nfp-appliance-to-azure-sentinel"></a>Connecter votre appliance WireX Network Forensics Platform (NFP) à Azure Sentinel

> [!IMPORTANT]
> Le connecteur WireX Systems NFP est actuellement disponible en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Cet article explique comment connecter votre appliance WireX Systems Network Forensics Platform (NFP) à Azure Sentinel. Le connecteur de données WireX NFP vous permet de connecter facilement vos journaux NFP à Azure Sentinel, ce qui vous permet ensuite de consulter les données dans des classeurs, de les utiliser pour créer des alertes personnalisées et de les incorporer pour améliorer l’investigation. 

> [!NOTE] 
> Les données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="prerequisites"></a>Prérequis

- Vous devez disposer d’autorisations en lecture et en écriture dans l’espace de travail Azure Sentinel.

- Vous devez disposer d’autorisations de lecture sur les clés partagées pour accéder à l’espace de travail.

## <a name="send-wirex-nfp-logs-to-azure-sentinel"></a>Envoyer des journaux WireX NFP à Azure Sentinel

Pour récupérer ses journaux dans Azure Sentinel, configurez votre appliance WireX Systems NFP pour envoyer des messages Syslog au format CEF à un serveur de transfert de journaux basé sur Linux (exécutant rsyslog ou syslog-ng). L’agent Log Analytics sera installé sur ce serveur et transfèrera les journaux à votre espace de travail Azure Sentinel.

1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Connecteurs de données**.

1. Dans la galerie **Connecteurs de données**, sélectionnez **WireX Network Forensics Platform (préversion)** , puis sélectionnez **Ouvrir la page du connecteur**.

1. Suivez les instructions de l’onglet **Instructions**, sous **Configuration** :

    1. **1. Configurer l’agent Syslog Linux** : Effectuez cette étape si vous n’avez pas encore de redirecteur de journal en cours d’exécution, ou si vous en avez besoin d’un autre. Consultez [ÉTAPE 1 : Déployer le redirecteur de journal](connect-cef-agent.md) dans la documentation d’Azure Sentinel pour obtenir des instructions et des explications plus détaillées.

    1. **2. Transférer les journaux CEF (Common Event format) à l’agent Syslog** : Contactez le [support WireX](https://wirexsystems.com/contact-us/) pour connaître la configuration appropriée de votre solution WireX NFP. Cette configuration doit inclure les éléments suivants :
        - Destination du journal : nom d’hôte et/ou adresse IP de votre serveur de transfert de journaux
        - Protocole et port : TCP 514 (si cela est recommandé, veillez à effectuer la modification parallèle dans le démon Syslog sur votre serveur de transfert de journaux)
        - Format du journal : CEF
        - Types de journaux : tous ceux recommandés par WireX

    1. **3. Valider la connexion**  : Vérifiez l’ingestion des données en copiant la commande sur la page du connecteur et en l’exécutant sur votre redirecteur de journal. Consultez [ÉTAPE 3 : Valider la connectivité](connect-cef-verify.md) dans la documentation d’Azure Sentinel pour obtenir des instructions et des explications plus détaillées.

        Environ 20 minutes peuvent être nécessaires avant que vos journaux commencent à apparaître dans Log Analytics.

## <a name="find-your-data"></a>Recherche de données

Après l’établissement d’une connexion réussie, les données s’affichent dans **Journaux**, sous la section **Azure Sentinel** dans le tableau *CommonSecurityLog*.

Pour interroger les données de WireX NFP dans Log Analytics, copiez le code suivant dans la fenêtre de requête, en appliquant d’autres filtres au choix :

```kusto
CommonSecurityLog 
| where DeviceVendor == "WireX"
```

Pour obtenir plus d’exemples de requêtes, consultez l’onglet **Étapes suivantes** dans la page du connecteur.

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter WireX Systems NFP à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](detect-threats-built-in.md).
- [Utilisez des classeurs](monitor-your-data.md) pour superviser vos données.