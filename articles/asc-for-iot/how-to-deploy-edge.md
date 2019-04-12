---
title: Déployer Azure Security Center pour le module IoT Edge | Microsoft Docs
description: Découvrez comment déployer un centre de sécurité pour l’agent de sécurité IoT sur IoT Edge.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/1/2019
ms.author: mlottner
ms.openlocfilehash: d72980d6e27600cb844d5477d3b9a61d9e1573e4
ms.sourcegitcommit: f24b62e352e0512dfa2897362021b42e0cb9549d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59505615"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Déployer un module de sécurité sur votre appareil IoT Edge

> [!IMPORTANT]
> Azure Security Center pour IoT est disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

**Azure Security Center (ASC) pour IoT** module fournit une solution de sécurité complète pour votre appareil IoT Edge.
Module de sécurité collecte, agrège et analyse les données de sécurité brute à partir de votre système d’exploitation et le conteneur système dans les alertes et les recommandations de sécurité actionnables.
Pour plus d’informations, consultez [module de sécurité pour IoT Edge](security-edge-architecture.md).

Dans ce guide, vous allez apprendre à déployer un module de sécurité sur votre appareil IoT Edge.

## <a name="deploy-security-module"></a>Déployer le module de sécurité

Utilisez les étapes suivantes pour déployer un ASC pour le module de sécurité IoT pour IoT Edge.

### <a name="prerequisites"></a>Conditions préalables

- Dans votre IoT Hub, assurez-vous que votre appareil est [enregistré comme un appareil IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal).

- ASC pour le module IoT Edge nécessite [AuditD framework](https://linux.die.net/man/8/auditd) est installé sur l’appareil IoT Edge.

    - Installer le framework en exécutant la commande suivante sur votre appareil IoT Edge :
   
      `sudo apt-get install auditd audispd-plugins`
   
    - Vérifiez Qu'auditd est active en exécutant la commande suivante :
   
      `sudo systemctl status auditd`
      
        La réponse attendue est `active (running)`. 

### <a name="deployment-using-azure-portal"></a>Déploiement à l’aide du portail Azure

1. À partir du portail Azure, ouvrez **place de marché**.

1. Sélectionnez **Internet des objets**, puis recherchez **Azure Security Center pour IoT** et sélectionnez-le.

   ![Sélectionnez Azure Security Center pour IoT](media/howto/edge-onboarding-8.png)

1. Cliquez sur **créer** pour configurer le déploiement. 

1. Choisissez Azure **abonnement** de votre IoT Hub, puis sélectionnez votre **IoT Hub**.<br>Sélectionnez **déployer sur un appareil** pour cibler un appareil unique ou sélectionnez **déployer à l’échelle** pour cibler plusieurs appareils, puis cliquez sur **créer**. Pour plus d’informations sur le déploiement à grande échelle, consultez [comment déployer](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor). 

    >[!Note] 
    >Si vous avez sélectionné **déployer à l’échelle**, ajoutez le nom de l’appareil et les détails avant de poursuivre la **ajouter des Modules** onglet dans les instructions suivantes.     

Il existe trois étapes pour créer un déploiement IoT Edge d’Azure Security Center pour IoT. Les sections suivantes les décrivent en détail. 

#### <a name="step-1-add-modules"></a>Étape 1 : Ajouter des modules

1. À partir de la **ajouter des Modules** onglet, **Modules de déploiement** zone, cliquez sur **AzureSecurityCenterforIoT**. 
   
1. Modifier le **nom** à **azureiotsecurity**.
1. Modifier le **URI d’Image** à **mcr.microsoft.com/ascforiot/azureiotsecurity:0.0.3**.
1. Vérifiez le **Options de création de conteneur** a la valeur :      
    ``` json
    {
        "NetworkingConfig": {
            "EndpointsConfig": {
                "host": {}
            }
        },
        "HostConfig": {
            "Privileged": true,
            "NetworkMode": "host",
            "PidMode": "host",
            "Binds": [
                "/:/host"
            ]
        }
    }    
    ```
1. Vérifiez que **propriétés souhaitées des représentations de module de jeu** est sélectionné et modifiez l’objet de configuration pour :
      
    ``` json
      "properties.desired": {
        "azureiot*com^securityAgentConfiguration^1*0*0": {
        }
      }
      ```

1. Cliquez sur **Enregistrer**.
1. Faites défiler vers le bas de l’onglet et sélectionnez **configurer les paramètres du Runtime Edge avancés**.
   
   >[!Note]
   > Faire **pas** désactiver la communication AMQP pour IoT Edge Hub.
   > Azure Security Center pour le module IoT requiert une communication de AMQP avec IoT Edge Hub.
   
1. Modifier le **Image** sous **Edge Hub** à **mcr.microsoft.com/ascforiot/edgehub:1.0.9-preview**.

   >[!Note]
   > Azure Security Center pour le module IoT nécessite une version dupliquée de IoT Edge Hub, basé sur le SDK version 1.20.
   > En modifiant l’image de IoT Edge Hub, vous demandez votre appareil IoT Edge pour remplacer la dernière version stable par la version dupliquée du Hub IoT Edge, qui n’est pas officiellement pris en charge par le service IoT Edge.

1. Vérifiez **Options créer** est définie sur : 
         
    ``` json
    {
      "HostConfig": {
        "PortBindings": {
          "8883/tcp": [{"HostPort": "8883"}],
          "443/tcp": [{"HostPort": "443"}],
          "5671/tcp": [{"HostPort": "5671"}]
        }
      }
    }
    ```
      
1. Cliquez sur **Enregistrer**.
   
1. Cliquez sur **Suivant**.

#### <a name="step-2-specify-routes"></a>Étape 2 : Spécifier des routes 

1. Dans le **spécifier des itinéraires** onglet, définissez la **ASCForIoTToIoTHub** router vers **» à partir / messages/modules/azureiotsecurity/\* dans $ en amont »**, puis cliquez sur  **Suivant**.

   ![Spécifier des routes](media/howto/edge-onboarding-9.png)

#### <a name="step-3-review-deployment"></a>Étape 3 : Vérifier le déploiement

1. Dans le **passez en revue le déploiement** onglet, passez en revue les informations de votre déploiement, puis sélectionnez **Submit** pour procéder au déploiement.

## <a name="diagnostic-steps"></a>Étapes de diagnostic

Si vous rencontrez un problème, les journaux de conteneur sont la meilleure façon d’en savoir plus sur l’état d’un appareil de module de sécurité IoT Edge. Utilisez les commandes et les outils de cette section pour recueillir des informations.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Vérifiez que les conteneurs requis sont installés et qu’il fonctionne comme prévu

1. Exécutez la commande suivante sur votre appareil IoT Edge :
    
     `sudo docker ps`
   
1. Vérifiez que les conteneurs suivants sont en cours d’exécution :
   
   | Nom | IMAGE |
   | --- | --- |
   | azureIoTSecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:0.0.3 |
   | edgeHub | mcr.microsoft.com/ascforiot/edgehub:1.0.9-preview |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0 |
   
   Si la configuration minimale requise conteneurs ne sont pas présents, vérifiez si votre manifeste de déploiement IoT Edge est aligné avec les paramètres recommandés. Pour plus d’informations, consultez [module déployer IoT Edge](#deployment-using-azure-portal).

### <a name="inspect-the-module-logs-for-errors"></a>Examinez les journaux de module pour les erreurs
   
1. Exécutez la commande suivante sur votre appareil IoT Edge :

   `sudo docker logs azureiotsecurity`
   
1. Pour les journaux plus détaillés, ajoutez la variable d’environnement suivante à **azureiotsecurity** du déploiement du module : `logLevel=Debug`.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les options de configuration, continuer au guide pratique pour la configuration du module. 
> [!div class="nextstepaction"]
> [Guide pratique de configuration de module](./how-to-agent-configuration.md)
