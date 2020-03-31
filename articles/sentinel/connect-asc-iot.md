---
title: Connecter Azure Security Center pour IoT à Azure Sentinel | Microsoft Docs
description: Découvrez comment connecter des données Azure Security Center pour IoT à Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: yelevin
ms.openlocfilehash: 3af51110a4c4604444573f62be65077c786db606
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588635"
---
# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel"></a>Connecter vos données d’Azure Security Center pour IoT à Azure Sentinel 


> [!IMPORTANT]
> Le connecteur de données Azure Security Center pour IoT est actuellement en préversion publique. Cette fonctionnalité est fournie sans contrat de niveau de service et est déconseillée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Utilisez le connecteur Azure Security Center pour IoT pour diffuser tous vos événements Azure Security Center pour IoT dans Azure Sentinel. 

## <a name="prerequisites"></a>Conditions préalables requises

- Autorisations de **lecture** et d’**écriture** sur l’espace de travail sur lequel Azure Sentinel est déployé
- **Azure Security Center pour IoT** doit être **activé** sur vos IoT Hubs appropriés
- Autorisations de **lecture** et d’**écriture** sur l’**Azure IoT Hub** auquel vous souhaitez vous connecter
- Autorisations de **lecture** et d’**écriture** sur le **groupe de ressources Azure IoT Hub**

> [!NOTE]
> Bien que vous deviez activer la licence Azure Security Center de niveau **standard** sur votre abonnement pour diffuser des alertes de ressource IoT vers Azure Sentinel, vous devez uniquement activer la licence Azure Security Center de niveau **gratuit** sur votre abonnement pour afficher les alertes Azure Security Center pour IoT dans Azure Sentinel. 

## <a name="connect-to-azure-security-center-for-iot"></a>Se connecter à Azure Security Center pour IoT

1. Dans Azure Sentinel, sélectionnez **Connecteurs de données** puis cliquez sur la vignette **Azure Security Center pour IoT**.
1. Dans le volet inférieur droit, cliquez sur **Ouvrir la page des connecteurs**. 
1. Cliquez sur **Se connecter** à côté de chaque abonnement IoT Hub dont vous souhaitez diffuser les alertes et les alertes d’appareil dans Azure Sentinel. 
    - Si Azure Security Center pour IoT n’est pas activée sur ce hub, vous verrez s’afficher un message d’avertissement **Activer**. Cliquez sur le lien **Activer** pour démarrer le service. 
1. Vous pouvez décider si vous souhaitez que les alertes d’Azure Security Center pour IoT génèrent automatiquement des incidents dans Azure Sentinel. Sous **Créer des incidents**, sélectionnez **Activer** pour activer la règle analytique par défaut qui crée automatiquement des incidents à partir des alertes générées dans le service de sécurité connecté. Cette règle peut être changée ou modifiée sous les règles **Analytics** > **Activer**.

> [!NOTE]
> L’actualisation de la liste du hub peut prendre un certain temps après l’apport de modifications à la connexion. 

## <a name="log-analytics-alert-display"></a>Affichage de l’alerte Log Analytics

Pour utiliser le schéma pertinent dans Log Analytics afin d’afficher les alertes Azure Security Center pour IoT :

1. Ouvrez **Journaux** > **SecurityInsights** > **SecurityAlert** ou recherchez **SecurityAlert**. 
2. Filtrez les résultats pour afficher uniquement les alertes générées par Azure Security Center pour IoT à l’aide du filtre kql suivant :

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>Notes de service

Après vous être connecté à un IoT Hub, les données du hub sont disponibles dans Azure Sentinel environ 15 minutes plus tard.


## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à connecter des données Azure Security Center pour IoT à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilisez des classeurs](tutorial-monitor-your-data.md) pour superviser vos données.
