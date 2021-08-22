---
title: Connecter des données Cisco Meraki à Azure Sentinel | Microsoft Docs
description: Découvrez comment utiliser le connecteur Cisco Meraki pour extraire des journaux Cisco Meraki dans Azure Sentinel. Affichez les données Cisco Meraki dans des classeurs, créez des alertes et améliorez l’investigation.
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
ms.openlocfilehash: 64828011cc92145e6635c995294f234754999a8b
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122525657"
---
# <a name="connect-your-cisco-meraki-to-azure-sentinel"></a>Connecter votre Cisco Meraki à Azure Sentinel

> [!IMPORTANT]
> Le connecteur Cisco Meraki est actuellement en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Cet article explique comment connecter votre ou vos appliances Cisco Meraki (MX/MS/MR) à Azure Sentinel. Le connecteur de données Cisco Meraki vous permet de connecter facilement vos journaux Cisco Meraki à Azure Sentinel, de consulter des tableaux de bord, de créer des alertes personnalisées et d’améliorer les enquêtes. L’intégration entre Cisco Meraki et Azure Sentinel utilise un serveur Syslog avec l’agent Log Analytics installé. Elle utilise également un analyseur de journal personnalisé basé sur une fonction Kusto.

> [!NOTE]
> Les données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="prerequisites"></a>Prérequis

- Vous devez disposer d’autorisations en lecture et en écriture sur l’espace de travail Azure Sentinel.

- Votre solution Cisco Meraki doit être configurée pour exporter des journaux via Syslog.

## <a name="send-cisco-meraki-logs-to-azure-sentinel-via-the-syslog-agent"></a>Envoyer des journaux Cisco Meraki à Azure Sentinel via l’agent Syslog  

Configurez Cisco Meraki pour transférer les messages Syslog à votre espace de travail Azure Sentinel via l’agent Syslog.

1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Connecteurs de données**.

1. Dans la galerie **Connecteurs de données**, sélectionnez le connecteur **Cisco Meraki (préversion)** , puis **Ouvrir la page du connecteur**.

1. Suivez les instructions de la page du connecteur **Cisco Meraki** :

    1. Installer et intégrer l'agent pour Linux

        - Choisissez une machine virtuelle Linux Azure ou une machine Linux non Azure (physique ou virtuelle).

    1. Configurer les journaux à collecter

        - Sélectionnez les installations et les gravités dans la **configuration des agents de l’espace de travail**.

    1. Configurer et connecter le ou les appareils Cisco Meraki

        - Suivez [ces instructions](https://documentation.meraki.com/General_Administration/Monitoring_and_Reporting/Meraki_Device_Reporting_-_Syslog%2C_SNMP_and_API) pour configurer le ou les appareils Cisco Meraki pour transférer Syslog. Pour le serveur distant, utilisez l’adresse IP de la machine Linux sur laquelle vous avez installé l’agent Linux.

## <a name="validate-connectivity-and-find-your-data"></a>Valider la connectivité et rechercher vos données

Jusqu’à 20 minutes peuvent s’écouler avant que vos journaux commencent à apparaître dans Azure Sentinel. 

Une fois la connexion établie, les données s’affichent dans **Journaux**, sous la section *Gestion des journaux*, dans la table *Syslog*.

Ce connecteur de données dépend d’un analyseur basé sur une fonction Kusto pour pouvoir fonctionner normalement. [Procédez comme suit](https://aka.ms/sentinel-ciscomeraki-parser) pour créer l’alias de la fonction **CiscoMeraki** Kusto. Vous pouvez ensuite taper `CiscoMeraki` dans la fenêtre de requête pour interroger les données.

Pour obtenir des exemples de requêtes utiles, consultez l’onglet **Étapes suivantes** dans la page du connecteur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez découvert comment connecter Cisco Meraki à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](detect-threats-built-in.md).
- [Utilisez des classeurs](monitor-your-data.md) pour superviser vos données.