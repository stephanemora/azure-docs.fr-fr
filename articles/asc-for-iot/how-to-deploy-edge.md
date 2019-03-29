---
title: Déployer un ASC pour le module IoT Edge | Microsoft Docs
description: Découvrez comment déployer ASC pour l’agent de sécurité IoT sur IoT Edge.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 87094b265a0c30c0349d64e4b956224525c04f74
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2019
ms.locfileid: "58580492"
---
# <a name="deploy-security-module-on-your-iot-edge-device"></a>Déployer le module de sécurité sur votre appareil IoT Edge

> [!IMPORTANT]
> ASC pour IoT est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

ASC pour IoT **azureiotsecurity** module fournit une solution de sécurité complète pour votre appareil IoT Edge.
Module de sécurité collecte, agrège et analyse les données de sécurité brute à partir de votre système d’exploitation et le conteneur système dans les alertes et les recommandations de sécurité actionnables.
Pour plus d’informations, consultez [module de sécurité pour IoT Edge](security-edge-architecture.md).

Dans ce guide, vous allez apprendre à déployer un module de sécurité sur votre appareil IoT Edge.

## <a name="deploy-security-module"></a>Déployer le module de sécurité

Utilisez les étapes suivantes pour déployer un ASC pour le module de sécurité IoT pour IoT Edge.

### <a name="prerequisites"></a>Conditions préalables

1. Assurez-vous que votre appareil est [enregistré comme un appareil IoT Edge](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-register-device-portal).

1. Nécessite l’ASC pour le module IoT Edge la [AuditD framework](https://linux.die.net/man/8/auditd) installé sur l’appareil Edge.

   Installer le framework en exécutant la commande suivante sur votre appareil Edge :
   
   `apt-get install auditd audispd-plugins`

### <a name="deployment-using-azure-portal"></a>Déploiement à l’aide du portail Azure

1. Ouvrez **place de marché** dans le portail Azure.

1. Recherchez **sécurité azure iot** , puis cliquez sur **Azure IoT sécurité**.

   ![](media/howto/edge_onboarding_7.png)

1. Cliquez sur **Créer**.

1. Choisissez votre **abonnement**, **IoT Hub** et **nom de l’appareil IoT Edge**, puis cliquez sur **créer**.

1. Cliquez sur **suivant** à deux reprises à **révision déploiement**.

1. Assurez-vous que **edgeHub.settings.createOptions** est configuré comme suit :

   `"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}]}}}"`

1. Cliquez sur **Submit** pour procéder au déploiement.


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les options de configuration, continuer au guide pratique pour la configuration du module. 
> [!div class="nextstepaction"]
> [Configuration du module comment guide](./how-to-agent-configuration.md)