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
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: 67bc104434dc0db30f5973bec0979afb7480fe4c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98621377"
---
# <a name="connect-your-data-from-azure-defender-formerly-azure-security-center-for-iot-to-azure-sentinel"></a>Connecter vos données d’Azure Defender (anciennement Azure Security Center) pour IoT à Azure Sentinel 

Utilisez le connecteur Defender pour IoT pour diffuser tous vos événements Defender pour IoT dans Azure Sentinel. 

Cette intégration permet aux organisations de détecter rapidement les attaques multiphases qui, souvent, franchissent les limites de l’informatique et de la formation organisationnelle. En outre, l’intégration de Defender pour IoT aux capacités d’orchestration, d’automatisation et de réponse aux problèmes de sécurité (SOAR) d’Azure Sentinel permet une réponse et une prévention automatisées grâce à des playbooks intégrés optimisés pour la formation organisationnelle. 
## <a name="prerequisites"></a>Conditions préalables requises

- Autorisations de **lecture** et d’**écriture** sur l’espace de travail sur lequel Azure Sentinel est déployé
- **Defender pour IoT** doit être **activé** sur le ou les hubs IoT correspondants
- Vous devez disposer des autorisations de **Contributeur** sur **l’Abonnement** à connecter

## <a name="connect-to-defender-for-iot"></a>Se connecter à Defender pour IoT

1. Dans Azure Sentinel, sélectionnez **Connecteurs de données** puis sélectionnez **Defender pour IoT** (qui peut encore être appelé Azure Security Center pour IoT) dans la galerie.

1. Au bas du volet droit, cliquez sur **Ouvrir la page des connecteurs**. 

1. Cliquez sur **Se connecter** à côté de chaque abonnement IoT Hub dont vous souhaitez diffuser les alertes et les alertes d’appareil dans Azure Sentinel. 
    - Vous recevrez un message d’erreur si Defender pour IoT n’est pas activé sur au moins un IoT Hub au sein d’un abonnement. Activez Defender pour IoT dans IoT Hub pour supprimer l’erreur.

1. Vous pouvez décider si vous souhaitez que les alertes de Defender pour IoT génèrent automatiquement des incidents dans Azure Sentinel. Sous **Créer des incidents**, sélectionnez **Activer** pour permettre à la règle d’analytique par défaut de créer automatiquement des incidents à partir des alertes générées. Cette règle est modifiable sous **Analytique** > **Règles actives**.

> [!NOTE]
> L’actualisation de la liste **Abonnement** peut prendre 10 secondes ou plus après la modification de la connexion. 

## <a name="log-analytics-alert-view"></a>Affichage des alertes Log Analytics

Pour utiliser le schéma pertinent dans Log Analytics afin d’afficher les alertes Defender pour IoT :

1. Ouvrez **Journaux** > **SecurityInsights** > **SecurityAlert** ou recherchez **SecurityAlert**. 

2. Filtrez les résultats pour afficher uniquement les alertes générées par Defender pour IoT à l’aide du filtre kql suivant :

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>Notes de service

Après la connexion d’un **Abonnement**, les données du hub sont disponibles dans Azure Sentinel environ 15 minutes plus tard.


## <a name="next-steps"></a>Étapes suivantes

Ce document vous a montré comment connecter Defender pour IoT à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilisez des classeurs](tutorial-monitor-your-data.md) pour superviser vos données.
