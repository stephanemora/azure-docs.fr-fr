---
title: Connecter Azure Defender pour IoT à Azure Sentinel | Microsoft Docs
description: Découvrez comment connecter Azure Defender (anciennement Azure Security Center) pour IoT à Azure Sentinel.
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
ms.date: 12/16/2020
ms.author: yelevin
ms.openlocfilehash: 95e78c7557092a4d1203a8df3a107fe7b63eac9b
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97631439"
---
# <a name="connect-your-data-from-azure-defender-formerly-azure-security-center-for-iot-to-azure-sentinel"></a>Connecter vos données d’Azure Defender (anciennement Azure Security Center) pour IoT à Azure Sentinel 


> [!IMPORTANT]
> Le connecteur de données Defender pour IoT est actuellement en préversion publique. Cette fonctionnalité est fournie sans contrat de niveau de service et est déconseillée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Utilisez le connecteur Defender pour IoT pour diffuser tous vos événements Defender pour IoT dans Azure Sentinel. 

Cette intégration permet aux organisations de détecter rapidement les attaques multiphases qui, souvent, franchissent les limites de l’informatique et de la formation organisationnelle. En outre, l’intégration de Defender pour IoT aux capacités d’orchestration, d’automatisation et de réponse de la sécurité (SOAR) d’Azure Sentinel permet une réponse et une prévention automatisées grâce à des playbooks intégrés optimisés pour la formation organisationnelle. 
## <a name="prerequisites"></a>Conditions préalables requises

- Autorisations de **lecture** et d’**écriture** sur l’espace de travail sur lequel Azure Sentinel est déployé
- **Defender pour IoT** doit être **activé** sur vos hubs IoT correspondants
- Vous devez disposer des autorisations de **contributeur** sur l’**abonnement** si vous souhaitez vous connecter

## <a name="connect-to-defender-for-iot"></a>Se connecter à Defender pour IoT

1. Dans Azure Sentinel, sélectionnez **Connecteurs de données** puis sélectionnez **Defender pour IoT** (qui peut encore être appelé Azure Security Center pour IoT) dans la galerie.

1. Au bas du volet droit, cliquez sur **Ouvrir la page des connecteurs**. 

1. Cliquez sur **Se connecter** à côté de chaque abonnement IoT Hub dont vous souhaitez diffuser les alertes et les alertes d’appareil dans Azure Sentinel. 
    - Vous recevrez un message d’erreur si Defender pour IoT n’est pas activé sur au moins un IoT Hub au sein d’un abonnement. Activez Defender pour IoT dans IoT Hub pour supprimer l’erreur.

1. Vous pouvez décider si vous souhaitez que les alertes de Defender pour IoT génèrent automatiquement des incidents dans Azure Sentinel. Sous **Créer des incidents**, sélectionnez **Activer** pour permettre à la règle des analyses par défaut de créer automatiquement des incidents à partir des alertes générées. Cette règle est modifiable sous **Analytics** > **Règles actives**.

> [!NOTE]
> L’actualisation de la liste **Abonnement** peut prendre 10 secondes ou plus après avoir apporté des modifications à la connexion. 

## <a name="log-analytics-alert-view"></a>Affichage des alertes Log Analytics

Pour utiliser le schéma pertinent dans Log Analytics afin d’afficher les alertes Defender pour IoT :

1. Ouvrez **Journaux** > **SecurityInsights** > **SecurityAlert** ou recherchez **SecurityAlert**. 

2. Filtrez les résultats pour afficher uniquement les alertes générées par Defender pour IoT à l’aide du filtre kql suivant :

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>Notes de service

Après vous être connecté un **Abonnement**, les données du hub sont disponibles dans Azure Sentinel environ 15 minutes plus tard.


## <a name="next-steps"></a>Étapes suivantes

Ce document vous a montré comment connecter Defender pour IoT à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilisez des classeurs](tutorial-monitor-your-data.md) pour superviser vos données.
