---
title: Guide Azure Security Center pour IoT pour la configuration à l’aide d’Azure Sentinel (préversion) | Microsoft Docs
description: Ce guide explique comment configurer Azure Sentinel pour recevoir des données de votre solution Azure Security Center pour IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: mlottner
ms.openlocfilehash: f6e7eddd6ddbcec61c3d8d173891cbc9abaaf08f
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77465654"
---
> [!IMPORTANT]
> Le connecteur de données Azure Security Center pour IoT dans Azure Sentinel est actuellement en préversion publique.
> Cette fonctionnalité est fournie sans contrat de niveau de service et est déconseillée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel-preview"></a>Connecter vos données d’Azure Security Center pour IoT à Azure Sentinel (préversion) 

Dans ce guide, découvrez comment connecter vos données Azure Security Center pour IoT à Azure Sentinel.  

> [!div class="checklist"]
> * Prérequis 
> * Paramètres de connexion
> * Affichage des alertes Log Analytics 

Connectez les alertes d’Azure Security Center pour IoT et diffusez-les directement dans Azure Sentinel.

## <a name="prerequisites"></a>Prérequis

- Vous devez disposer des autorisations de **lecture** et d’**écriture** pour l’espace de travail.
- **Azure Security Center pour IoT** doit être **activée** sur votre ou vos IoT Hubs pertinents.
- Vous devez disposer des autorisations de **lecture** et d’**écriture** sur l’**IoT Hub** auquel vous souhaitez vous connecter.
- Vous devez également avoir des autorisations de **lecture** et d’**écriture** sur le **groupe de ressources IoT Hub**.

> [!NOTE]
> Vous devez disposer de la licence Azure Security Center de niveau Standard s’exécutant sur votre abonnement pour envoyer des alertes de ressource Azure générales. Avec la licence de niveau gratuit requise pour Azure Security Center pour IoT, seules les alertes liées à Azure Security Center pour IoT seront transférées à Azure Sentinel. 

## <a name="connect-to-azure-security-center-for-iot"></a>Se connecter à Azure Security Center pour IoT

1. Dans Azure Sentinel, sélectionnez **Connecteurs de données** puis cliquez sur la vignette **Azure Security Center pour IoT**.
1. Dans le volet inférieur droit, cliquez sur **Ouvrir la page des connecteurs**. 
1. Cliquez sur **Se connecter** à côté de chaque abonnement IoT Hub dont vous souhaitez diffuser les alertes et les alertes d’appareil dans Azure Sentinel. 
    - Si Azure Security Center pour IoT n’est pas activée sur ce hub, vous verrez un message Activer les avertissements. Cliquez sur le lien **Activer** pour démarrer le service. 
1. Vous pouvez décider si vous souhaitez que les alertes d’Azure Security Center pour IoT génèrent automatiquement des incidents dans Azure Sentinel. Sous **Créer des incidents**, sélectionnez **Activer** pour activer la règle analytique par défaut qui crée automatiquement des incidents à partir des alertes générées dans le service de sécurité connecté. Cette règle peut être changée ou modifiée sous les règles **Analytics** > **Activer**.

> [!NOTE]
>L’actualisation de la liste des hubs après y avoir apporté des modifications de connexion peut prendre 10 secondes ou plus. 

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

Dans ce document, vous avez appris à connecter Azure Security Center pour IoT à Azure Sentinel. Pour en savoir plus sur la détection des menaces et l’accès aux données de sécurité, consultez les articles suivants :

- Découvrez comment utiliser Azure Sentinel pour [obtenir une visibilité sur vos données et les menaces potentielles](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility).

- Découvrez comment [accéder à vos données de sécurité IoT](how-to-security-data-access.md).