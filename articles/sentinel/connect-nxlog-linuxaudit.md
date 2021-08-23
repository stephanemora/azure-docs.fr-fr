---
title: Connecter des données de NXLog LinuxAudit à Azure Sentinel | Microsoft Docs
description: Découvrez comment utiliser le connecteur de données NXLog LinuxAudit pour extraire les journaux de LinuxAudit dans Azure Sentinel. Affichez les données de LinuxAudit dans des classeurs, créez des alertes et améliorez l’investigation.
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
ms.openlocfilehash: 6b10a0c4b1a655d998d9a418dde679c0c6b68b10
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122563722"
---
# <a name="connect-your-nxlog-linuxaudit-to-azure-sentinel"></a>Connecter votre NXLog LinuxAudit à Azure Sentinel

> [!IMPORTANT]
> Le connecteur NXLog LinuxAudit est actuellement en disponible en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Le connecteur [NXLog LinuxAudit](https://nxlog.co/documentation/nxlog-user-guide/im_linuxaudit.html) vous permet d’exporter facilement de événements de sécurité Linux vers Azure Sentinel en temps réel, ce qui vous donne une insight de l’activité du serveur de noms de domaine au sein de votre organisation. Le module NXLog LinuxAudit prend en charge les règles d’audit personnalisées, et collecte des journaux sans auditid ou tout autre logiciel d’espace utilisateur. Les adresses IP et les ID de groupe/utilisateur sont résolus dans leurs noms respectifs, ce qui rend les journaux [d’audit Linux](https://nxlog.co/documentation/nxlog-user-guide/linux-audit.html) plus intelligibles pour les analystes de sécurité. L’intégration entre NXLog LinuxAudit et Azure Sentinel est facilitée par l’API REST.

> [!NOTE]
> Les données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="configure-and-connect-nxlog-linuxaudit"></a>Configurer et connecter NXLog LinuxAudit

NXLog peut être configuré pour envoyer des événements au format JSON directement à Azure Sentinel.

1. Dans le portail Azure Sentinel, cliquez sur **Connecteurs de données**, puis sélectionnez le connecteur **NXLog LinuxAudit**.

1. Sélectionnez **Ouvrir la page du connecteur**.

1. Suivez les instructions pas à pas de la rubrique Intégration du *Guide de l’utilisateur NXLog* [Microsoft Azure Sentinel](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) pour configurer le transfert via l’API REST.

## <a name="find-your-data"></a>Recherche de données

Une fois la connexion établie, les données s’affichent dans **Journaux**, sous la section **Custom Logs** (Journaux personnalisés), dans la table *LinuxAudit_CL*.

## <a name="validate-connectivity"></a>Valider la connectivité

Jusqu’à 20 minutes peuvent être nécessaires avant que vos journaux ne commencent à apparaître dans Log Analytics.

## <a name="next-steps"></a>Étapes suivantes

Ce document vous a montré comment utiliser NXLog LinuxAudit pour ingérer des événements de sécurité Linux dans Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](detect-threats-built-in.md).
- [Utilisez des classeurs](monitor-your-data.md) pour superviser vos données.