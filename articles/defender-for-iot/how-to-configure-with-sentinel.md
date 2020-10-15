---
title: Configurer Azure Sentinel pour Defender pour IoT (préversion)
description: Explique comment configurer Azure Sentinel pour recevoir des données de votre solution Defender pour IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 71147352c5b75195ed0dff2b05acc5315f3183cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90930653"
---
# <a name="connect-your-data-from-defender-for-iot-to-azure-sentinel-preview"></a>Connecter vos données de Defender pour IoT à Azure Sentinel (préversion)

Le connecteur de données Azure Security Center pour IoT dans Azure Sentinel est actuellement en préversion publique. Cette fonctionnalité est fournie sans contrat de niveau de service et est déconseillée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/en-us/support/legal/preview-supplemental-terms/).

Dans ce guide, découvrez comment connecter vos données Defender pour IoT à Azure Sentinel.

> [!div class="checklist"]
> * Prérequis
> * Paramètres de connexion
> * Affichage des alertes Log Analytics

Connectez les alertes de Defender pour IoT et diffusez-les directement dans Azure Sentinel.

## <a name="prerequisites"></a>Prérequis

- Vous devez disposer des autorisations de **lecture** et d’**écriture** pour l’espace de travail.
- **Defender pour IoT** doit être **activé** sur vos hubs IoT correspondants.
- Vous devez disposer des autorisations de **lecture** et d’**écriture** sur l’**IoT Hub** auquel vous souhaitez vous connecter.
- Vous devez également avoir des autorisations de **lecture** et d’**écriture** sur le **groupe de ressources IoT Hub**.

> [!NOTE]
> Vous devez disposer de la licence Azure Security Center de niveau Standard s’exécutant sur votre abonnement pour envoyer des alertes de ressource Azure générales. Avec la licence de niveau gratuit requise pour Defender pour IoT, seules les alertes concernant Defender pour IoT seront transférées à Azure Sentinel.

## <a name="connect-to-defender-for-iot"></a>Se connecter à Defender pour IoT

1. Dans Azure Sentinel, sélectionnez **Connecteurs de données** puis cliquez sur la vignette **Defender pour IoT**.
1. Au bas du volet droit, cliquez sur **Ouvrir la page des connecteurs**.
1. Cliquez sur **Se connecter** à côté de chaque abonnement IoT Hub dont vous souhaitez diffuser les alertes et les alertes d’appareil dans Azure Sentinel.
    - Si Defender pour IoT n’est pas activé sur ce hub, vous verrez s’afficher un message d’avertissement Activer. Cliquez sur le lien **Activer** pour démarrer et activer le service.
1. Vous pouvez décider si vous souhaitez que les alertes de Defender pour IoT génèrent automatiquement des incidents dans Azure Sentinel. Sous **Créer des incidents**, sélectionnez **Activer** pour permettre à la règle de créer automatiquement des incidents à partir des alertes générées.  Cette règle est modifiable sous **Analytics** > **Règles actives**.

> [!NOTE]
>L’actualisation de la liste des hubs après y avoir apporté des modifications de connexion peut prendre 10 secondes ou plus.

## <a name="log-analytics-alert-display"></a>Affichage de l’alerte Log Analytics

Pour utiliser le schéma pertinent dans Log Analytics afin d’afficher les alertes Defender pour IoT :

1. Ouvrez **Journaux** > **SecurityInsights** > **SecurityAlert** ou recherchez **SecurityAlert**.
1. Filtrez les résultats pour afficher uniquement les alertes générées par Defender pour IoT à l’aide du filtre kql suivant :

```kusto
SecurityAlert | where ProductName == "Defender for IoT"
```

### <a name="service-notes"></a>Notes de service

Après vous être connecté à un IoT Hub, les données du hub sont disponibles dans Azure Sentinel environ 15 minutes plus tard.

## <a name="next-steps"></a>Étapes suivantes

Ce document vous a montré comment connecter Defender pour IoT à Azure Sentinel. Pour en savoir plus sur la détection des menaces et l’accès aux données de sécurité, consultez les articles suivants :

- Découvrez comment utiliser Azure Sentinel pour [obtenir une visibilité sur vos données et les menaces potentielles](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility).

- Découvrez comment [accéder à vos données de sécurité IoT](how-to-security-data-access.md).
