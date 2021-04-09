---
title: Connecter des données VMware ESXi à Azure Sentinel | Microsoft Docs
description: Découvrez comment utiliser le connecteur de données VMware ESXi pour extraire des journaux ESXi dans Azure Sentinel. Affichez les données ESXi dans des classeurs, créez des alertes et améliorez l’investigation.
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
ms.date: 03/01/2021
ms.author: yelevin
ms.openlocfilehash: 3d478a9ac3cf91f3f6815859b8534efff88f07b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101743038"
---
# <a name="connect-your-vmware-esxi-to-azure-sentinel"></a>Connecter votre VMware ESXi à Azure Sentinel

> [!IMPORTANT]
> Le connecteur VMware ESXi est actuellement en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

Cet article explique comment connecter votre appliance VMware ESXi à Azure Sentinel. Le connecteur de données VMware ESXi vous permet d’ingérer facilement vos journaux VMware ESXi dans Azure Sentinel, ce qui vous donne plus d’informations sur les activités ESXi de votre organisation et contribue à améliorer vos capacités en matière d’opérations de sécurité. L’intégration entre VMware ESXi et Azure Sentinel utilise un serveur Syslog avec l’agent Log Analytics installé. Elle utilise également un analyseur de journal personnalisé basé sur une fonction Kusto.

> [!NOTE]
> Les données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="prerequisites"></a>Prérequis

- Vous devez disposer d’autorisations en lecture et en écriture sur l’espace de travail Azure Sentinel.

- Votre solution VMware ESXi doit être configurée pour exporter des journaux via Syslog.

## <a name="send-vmware-esxi-logs-to-the-syslog-agent"></a>Envoyer des journaux VMware ESXi à l’agent Syslog  

Configurez VMware ESXi pour transférer les messages Syslog à votre espace de travail Azure Sentinel via l’agent Syslog.
3. Suivez les instructions de la page **VMware ESXi**.


1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Connecteurs de données**.

1. Dans la galerie **Connecteurs de données**, sélectionnez **VMware ESXi (préversion)** , puis **Ouvrir la page du connecteur**.

1. Suivez les instructions de la page du connecteur **VMware ESXi** :

    1. Installer et intégrer l'agent pour Linux

        - Choisissez une machine virtuelle Linux Azure ou une machine Linux non Azure (physique ou virtuelle).

    1. Configurer les journaux à collecter

        - Sélectionnez les installations et les gravités dans la **configuration des agents de l’espace de travail**.

    1. Configurer et connecter le VMware ESXi

        - Suivez ces instructions afin de configurer le VMware ESXi pour transférer syslog :
            - [VMware ESXi 3.5 et 4.x](https://kb.vmware.com/s/article/1016621)
            - [VMware ESXi 5.0 et versions ultérieures](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.monitoring.doc/GUID-9F67DB52-F469-451F-B6C8-DAE8D95976E7.html)

            Pour le serveur distant, utilisez l’adresse IP de la machine Linux sur laquelle vous avez installé l’agent Linux.

## <a name="validate-connectivity-and-find-your-data"></a>Valider la connectivité et rechercher vos données

Jusqu’à 20 minutes peuvent s’écouler avant que vos journaux commencent à apparaître dans Azure Sentinel. 

Une fois la connexion établie, les données s’affichent dans **Journaux**, sous la section *Gestion des journaux*, dans la table *Syslog*.

Ce connecteur de données dépend d’un analyseur basé sur une fonction Kusto pour pouvoir fonctionner normalement. [Procédez comme suit](https://aka.ms/sentinel-vmwareesxi-parser) pour créer l’alias de fonction Kusto **VMwareESXi**. Vous pouvez ensuite taper `VMwareESXi` dans n’importe quelle fenêtre de requête dans Azure Sentinel pour interroger les données.

Pour obtenir des exemples de requêtes utiles, consultez l’onglet **Étapes suivantes** dans la page du connecteur.

## <a name="next-steps"></a>Étapes suivantes

Ce document vous a montré comment connecter VMware ESXi à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilisez des classeurs](tutorial-monitor-your-data.md) pour superviser vos données.
