---
title: Déployer le module Azure Security Center pour IoT Edge | Microsoft Docs
description: Découvrez comment déployer un agent de sécurité Azure Security Center pour IoT sur IoT Edge.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2019
ms.author: mlottner
ms.openlocfilehash: 128265cd3e69cd27bab6538c9eb376410439824d
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176664"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Déployer un module de sécurité sur votre appareil IoT Edge


Le module **Azure Security Center pour IoT** constitue une solution de sécurité complète pour vos appareils IoT Edge.
Ce module de sécurité collecte, agrège et analyse des données de sécurité brutes tirées du système d’exploitation et du système de conteneur pour produire des alertes et des suggestions de sécurité actionnables.
Pour plus d’informations, consultez [Module de sécurité pour IoT Edge](security-edge-architecture.md).

Dans ce article, vous apprendrez à déployer un module de sécurité sur votre appareil IoT Edge.

## <a name="deploy-security-module"></a>Déployer un module de sécurité

Procédez comme suit pour déployer un module de sécurité Azure Security Center pour IoT sur IoT Edge.

### <a name="prerequisites"></a>Prérequis

1. Dans votre instance d’IoT Hub, assurez-vous que votre appareil est [inscrit en tant qu’appareil IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal).

1. Le module d’Azure Security Center pour IoT Edge nécessite l’installation de [l’infrastructure AuditD](https://linux.die.net/man/8/auditd) sur l’appareil IoT Edge.

    - Installez le framework en exécutant la commande suivante sur votre appareil IoT Edge :
   
    `sudo apt-get install auditd audispd-plugins`

    - Exécutez la commande suivante pour vérifier qu’AuditD est actif : 
   
    `sudo systemctl status auditd`<br>
    - La réponse attendue est : `active (running)` 
        

### <a name="deployment-using-azure-portal"></a>Déploiement à l’aide du Portail Microsoft Azure

1. À partir du portail Microsoft Azure, ouvrez la **Place de marché**.

1. Sélectionnez **Internet des objets**, puis recherchez **Azure Security Center pour IoT** et sélectionnez-le.

   ![Sélection d’Azure Security Center pour IoT](media/howto/edge-onboarding-8.png)

1. Cliquez sur **Créer** pour configurer le déploiement. 

1. Choisissez l’**abonnement** Azure de votre instance d’IoT Hub, puis sélectionnez votre instance d’**IoT Hub**.<br>Sélectionnez **Déployer sur un appareil** pour cibler un appareil unique ou **Déployer à l’échelle** pour cibler plusieurs appareils, puis cliquez sur **Créer**. Pour plus d’informations sur le déploiement à l’échelle, consultez [Comment déployer](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor). 

    >[!Note] 
    >Si vous avez sélectionné **Déployer à l’échelle**, ajoutez le nom de l’appareil et d’autres informations avant d’accéder à l’onglet **Ajouter des modules** dans les instructions suivantes.     

La création d’un déploiement IoT Edge pour Azure Security Center pour IoT s’effectue en trois étapes. Les sections suivantes les décrivent en détail. 

#### <a name="step-1-add-modules"></a>Étape 1 : Ajouter des modules

1. À partir de l’onglet **Ajouter des modules**, dans la zone **Modules de déploiement**, cliquez sur l’option **Configurer** pour **AzureSecurityCenterforIoT**. 
   
1. Remplacez **nom** par **azureiotsecurity**.
1. Remplacez **URI d’Image** par **mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.0**.
1. Vérifiez que la valeur du champ **Options de création de conteneur** est définie sur :      
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
1. Vérifiez que l’option **Définir les propriétés souhaitées du jumeau de module** est activée, puis remplacez l’objet de configuration par :
      
    ``` json
    { 
       "properties.desired":{ 
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration":{ 

          }
       }
    }
    ```

1. Cliquez sur **Enregistrer**.
1. Faites défiler l’écran vers le bas de l’onglet, puis sélectionnez **Configurer les paramètres avancés du runtime Edge**. 
   
1. Remplacez **Image** sous **Edge Hub** par **mcr.microsoft.com/azureiotedge-hub:1.0.9-rc2**.

1. Vérifiez que la valeur du champ **Options de création** est définie sur : 
         
    ``` json
    { 
       "HostConfig":{ 
          "PortBindings":{ 
             "8883/tcp":[ 
                { 
                   "HostPort":"8883"
                }
             ],
             "443/tcp":[ 
                { 
                   "HostPort":"443"
                }
             ],
             "5671/tcp":[ 
                { 
                   "HostPort":"5671"
                }
             ]
          }
       }
    }
    ```
1. Cliquez sur **Enregistrer**.
   
1. Cliquez sur **Suivant**.

#### <a name="step-2-specify-routes"></a>Étape 2 : Spécifier des routes 

1. Sous l’onglet **Spécifier des routes**, vérifiez que vous avez une route (explicite ou implicite) qui va transférer les messages depuis le module **azureiotsecurity** vers **$upstream** conformément aux exemples suivants, ensuite seulement cliquez sur **Suivant**. 

~~~Default implicit route
"route": "FROM /messages/* INTO $upstream" 
~~~

~~~Explicit route
"ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
~~~

#### <a name="step-3-review-deployment"></a>Étape 3 : Vérifier le déploiement

- Sous l’onglet **Vérifier le déploiement**, examinez les informations sur votre déploiement, puis sélectionnez **Envoyer** pour procéder au déploiement.

## <a name="diagnostic-steps"></a>Étapes de diagnostic

Si vous rencontrez un problème, les journaux de conteneur sont la meilleure façon d’en savoir plus sur l’état d’un appareil de module de sécurité IoT Edge. Utilisez les commandes et les outils de cette section pour recueillir des informations.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Vérifiez que les conteneurs requis sont installés et qu’ils fonctionnent comme prévu

1. Exécutez les commandes suivantes sur votre appareil IoT Edge :
    
    `sudo docker ps`
   
1. Vérifiez que les conteneurs suivants sont en cours d’exécution :
   
   | Nom | IMAGE |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.0 |
   | edgeHub | mcr.microsoft.com/azureiotedge-hub:1.0.9-rc2 |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0 |
   
   Si les conteneurs minimum requis ne sont pas présents, vérifiez si votre manifeste de déploiement IoT Edge correspond aux paramètres recommandés. Pour plus d’informations, consultez [Déployer votre module IoT Edge](#deployment-using-azure-portal).

### <a name="inspect-the-module-logs-for-errors"></a>Recherchez d’éventuelles erreurs dans journaux de module
   
1. Exécutez les commandes suivantes sur votre appareil IoT Edge :

   `sudo docker logs azureiotsecurity`
   
1. Pour consulter des journaux d’activité plus détaillés, ajoutez la variable d’environnement suivante au déploiement du module **azureiotsecurity** : `logLevel=Debug`.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les options de configuration, accédez au guide pratique de configuration du module. 
> [!div class="nextstepaction"]
> [Guide pratique de configuration du module](./how-to-agent-configuration.md)
