---
title: Connecter des données Juniper Networks SRX à Azure Sentinel | Microsoft Docs
description: Découvrez comment utiliser le connecteur de données Juniper SRX pour extraire des journaux Juniper SRX dans Azure Sentinel. Affichez les données Juniper SRX dans des classeurs, créez des alertes et améliorez l’examen.
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
ms.openlocfilehash: b10c47a31bf1be10c278d4d9e0dce633bc7bff6c
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530635"
---
# <a name="connect-your-juniper-srx-firewall-to-azure-sentinel"></a>Connecter votre pare-feu Juniper SRX à Azure Sentinel

> [!IMPORTANT]
> Le connecteur Juniper SRX est actuellement en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

Cet article explique comment connecter votre appliance de pare-feu Juniper SRX à Azure Sentinel. Le connecteur de données Juniper SRX vous permet de connecter facilement vos journaux SRX à Azure Sentinel, pour ensuite consulter les données dans des classeurs, les utiliser pour créer des alertes personnalisées et les incorporer pour améliorer l’investigation. L'intégration entre Juniper SRX et Azure Sentinel utilise Syslog.

> [!NOTE]
> Les données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="prerequisites"></a>Prérequis

- Vous devez disposer d’autorisations en lecture et en écriture sur l’espace de travail Azure Sentinel.

- Votre solution Juniper SRX doit être configurée pour exporter des journaux via Syslog.

## <a name="forward-juniper-srx-logs-to-the-syslog-agent"></a>Transférer des journaux Juniper SRX à l’agent Syslog  

Configurez Juniper SRX pour transférer les messages Syslog à votre espace de travail Azure Sentinel via l’agent Syslog.

1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Connecteurs de données**.

1. Dans la galerie **Connecteurs de données**, sélectionnez **Juniper SRX (préversion)** , puis sélectionnez **Ouvrir la page du connecteur**.

1. Suivez les instructions de la page du connecteur **Juniper SRX** :

    1. Installer et intégrer l'agent pour Linux

        - Choisissez une machine virtuelle Linux Azure ou une machine Linux non Azure (physique ou virtuelle).

    1. Configurer les journaux à collecter

        - Sélectionnez les installations et les gravités dans la configuration des agents de l’espace de travail.

    1. Configurer et connecter Juniper SRX

        - Suivez ces instructions afin de configurer le connecteur Juniper SRX pour transférer Syslog.
            - [Journaux de trafic (journaux de stratégie de sécurité)](https://kb.juniper.net/InfoCenter/index?page=content&id=KB16509&actp=METADATA)
            - [Journaux système](https://kb.juniper.net/InfoCenter/index?page=content&id=kb16502)
        - Pour le serveur distant, utilisez l’adresse IP de la machine Linux sur laquelle vous avez installé l’agent Linux.

## <a name="find-your-data"></a>Recherche de données

Une fois la connexion correctement établie, les données apparaissent dans l’espace de travail Log Analytics sous Syslog.

Consultez l’onglet **Étapes suivantes** de la page du connecteur pour obtenir des exemples de requêtes utiles.

## <a name="validate-connectivity"></a>Valider la connectivité

Jusqu’à 20 minutes peuvent être nécessaires avant que vos journaux ne commencent à apparaître dans Log Analytics.

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à connecter Juniper SRX à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilisez des classeurs](tutorial-monitor-your-data.md) pour superviser vos données.
