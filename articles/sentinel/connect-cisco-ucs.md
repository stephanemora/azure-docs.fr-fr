---
title: Connecter des données de Cisco Unified Computing System (UCS) à Azure Sentinel | Microsoft Docs
description: Découvrez comment utiliser le connecteur de données Cisco UCS pour extraire des journaux Cisco UCS dans Azure Sentinel. Affichez les données Cisco UCS dans des classeurs, créez des alertes et améliorez l’investigation.
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
ms.openlocfilehash: caa83b9149f39f69d0cbf44a2d6cb01fdaf29721
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567807"
---
# <a name="connect-your-cisco-unified-computing-system-ucs-to-azure-sentinel"></a>Connecter votre Cisco Unified Computing System (UCS) à Azure Sentinel

> [!IMPORTANT]
> Le connecteur Cisco UCS est actuellement en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

Cet article explique comment connecter votre appliance Cisco Unified Computing System (UCS) à Azure Sentinel. Le connecteur de données Cisco UCS vous permet de connecter facilement vos journaux Cisco UCS à Azure Sentinel, pour ensuite consulter les données dans des classeurs, les utiliser pour créer des alertes personnalisées et les incorporer pour améliorer l’investigation. L’intégration entre Cisco UCS et Azure Sentinel utilise Syslog.

> [!NOTE]
> Les données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="prerequisites"></a>Prérequis

- Vous devez disposer d’autorisations en lecture et en écriture sur l’espace de travail Azure Sentinel.

- Votre solution Cisco UCS doit être configurée pour exporter des journaux via Syslog.

## <a name="forward-cisco-ucs-logs-to-the-syslog-agent"></a>Transférer Des journaux Cisco UCS à l’agent Syslog  

Configurez Cisco UCS pour transférer les messages Syslog à votre espace de travail Azure Sentinel via l’agent Syslog.

1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Connecteurs de données**.

1. Dans la galerie **Connecteurs de données**, sélectionnez le connecteur **Cisco UCS (préversion)** , puis **Ouvrir la page du connecteur**.

1. Suivez les instructions de la page du connecteur **Cisco UCS** :

    1. Installer et intégrer l'agent pour Linux

        - Choisissez une machine virtuelle Linux Azure ou une machine Linux non Azure (physique ou virtuelle).

    1. Configurer les journaux à collecter

        - Sélectionnez les installations et les gravités dans la configuration des paramètres avancés de l’espace de travail

    1. Configurer et connecter le connecteur Cisco UCS

        - Suivez [ces instructions](https://www.cisco.com/c/en/us/support/docs/servers-unified-computing/ucs-manager/110265-setup-syslog-for-ucs.html#configsremotesyslog) pour configurer le connecteur Cisco UCS pour transférer Syslog. Pour le serveur distant, utilisez l’adresse IP de la machine Linux sur laquelle vous avez installé l’agent Linux.

## <a name="find-your-data"></a>Recherche de données

Une fois la connexion correctement établie, les données apparaissent dans l’espace de travail Log Analytics sous Syslog.

Pour obtenir des exemples de requêtes utiles, consultez l’onglet **Étapes suivantes**.

## <a name="validate-connectivity"></a>Valider la connectivité

Jusqu’à 20 minutes peuvent être nécessaires avant que vos journaux ne commencent à apparaître dans Log Analytics.

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez découvert comment connecter Cisco UCS à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilisez des classeurs](tutorial-monitor-your-data.md) pour superviser vos données.
