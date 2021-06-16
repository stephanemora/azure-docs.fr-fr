---
title: Configurer Microsoft OPC Publisher
description: Dans ce tutoriel, vous allez apprendre à configurer OPC Publisher en mode autonome.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: a889f0b740d506bd546f1fb67f050317146c31e8
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110677888"
---
# <a name="tutorial-configure-the-opc-publisher"></a>Tutoriel : Configurer OPC Publisher

Ce tutoriel contient des informations sur la configuration d’OPC Publisher. Vous pouvez utiliser plusieurs interfaces pour le configurer.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Configurer OPC Publisher par le biais du fichier de configuration.
> * Configurer OPC Publisher par le biais des arguments de ligne de commande.
> * Configurer OPC Publisher par le biais de méthodes directes IoT Hub.
> * Configurer OPC Publisher par le biais d’un microservice REST auxiliaire basé sur le cloud.

## <a name="configuring-security"></a>Configuration de la sécurité

IoT Edge fournit OPC Publisher avec sa configuration de sécurité pour un accès automatique à IoT Hub. Vous pouvez également exécuter OPC Publisher en tant que conteneur Docker autonome en spécifiant une chaîne de connexion d’appareil pour accéder à IoT Hub par le biais du paramètre de ligne de commande `dc`. Vous pouvez créer un appareil pour IoT Hub et récupérer sa chaîne de connexion par le biais du portail Azure.

Pour accéder aux ressources OPC UA, des certificats X.509 et leurs clés privées associées sont utilisés par OPC UA. C’est ce que l’on appelle l’authentification d’application OPC UA, et elle vient s’ajouter à l’authentification utilisateur OPC UA. OPC Publisher utilise un magasin de certificats basé sur un système de fichiers pour gérer tous les certificats d’application. Au démarrage, OPC Publisher vérifie si ce magasin de certificats contient un certificat qu’il peut utiliser. Dans la négative, il crée un certificat auto-signé et une clé privée associée. Les certificats auto-signés fournissent une authentification faible, car ils ne sont pas signés par une autorité de certification approuvée, mais au moins la communication avec la ressource active OPC UA peut être chiffrée.

La sécurité est activée dans le fichier de configuration par le biais de l’indicateur `"UseSecurity": true,`. Le point de terminaison le plus sécurisé disponible sur les serveurs OPC UA auquel OPC Publisher est censé se connecter est sélectionné automatiquement.
Par défaut, OPC Publisher utilise l’authentification utilisateur anonyme (en plus de l’authentification d’application décrite ci-dessus). Toutefois, il prend également en charge l’authentification utilisateur à l’aide du nom d’utilisateur et du mot de passe Il est possible de le spécifier à l’aide de l’interface de configuration de l’API REST (décrite ci-dessous) ou du fichier de configuration comme suit :
```
"OpcAuthenticationMode": "UsernamePassword",
"OpcAuthenticationUsername": "usr",
"OpcAuthenticationPassword": "pwd",
```
De plus, OPC Publisher version 2.5 et antérieures chiffrent le nom d’utilisateur et le mot de passe dans le fichier de configuration. Les versions 2.6 et ultérieures ne prennent en charge que le nom d’utilisateur et le mot de passe en texte brut. Une amélioration est prévue dans la prochaine version d’OPC Publisher.

Pour conserver la configuration de sécurité d’OPC Publisher entre les redémarrages, le certificat et la clé privée situés dans le répertoire du magasin de certificats doivent être mappés au système de fichiers du système d’exploitation de l’hôte IoT Edge. Consultez « Spécification des options de création de conteneur dans le portail Azure » ci-dessus.

## <a name="configuration-via-configuration-file"></a>Configuration par le biais d’un fichier de configuration

Le moyen le plus simple de configurer OPC Publisher consiste à utiliser un fichier de configuration. Un exemple de fichier de configuration, ainsi que la documentation relative à son format, sont fournis par le biais du fichier [`publishednodes.json`](https://raw.githubusercontent.com/Azure/iot-edge-opc-publisher/master/opcpublisher/publishednodes.json) dans ce dépôt.
La syntaxe du fichier de configuration a changé au fil du temps et OPC Publisher peut toujours lire les anciens formats, mais il les convertit au format le plus récent lors de la persistance de la configuration, régulièrement effectuée de manière automatique.

Voici à quoi ressemble un fichier de configuration de base :
```
[
  {
    "EndpointUrl": "opc.tcp://testserver:62541/Quickstarts/ReferenceServer",
    "UseSecurity": true,
    "OpcNodes": [
      {
        "Id": "i=2258",
        "OpcSamplingInterval": 2000,
        "OpcPublishingInterval": 5000,
        "DisplayName": "Current time"
      }
    ]
  }
]
```

Les ressources OPC UA optimisent la bande passante réseau en envoyant les modifications de données à OPC Publisher uniquement lorsque les données ont changé. Si les modifications de données doivent être publiées plus souvent ou à intervalles réguliers, OPC Publisher prend en charge une « pulsation » pour chaque élément de données configuré, que vous pouvez activer en spécifiant la clé HeartbeatInterval dans la configuration de l’élément de données. L’intervalle est exprimée en secondes :
```
 "HeartbeatInterval": 3600,
```

Une ressource OPC UA envoie toujours la valeur actuelle d’un élément de données lorsque OPC Publisher s’y connecte pour la première fois. Pour empêcher la publication de ces données sur IoT Hub, vous pouvez spécifier la clé SkipFirst dans la configuration de l’élément de données :
```
 "SkipFirst": true,
```

Vous pouvez également activer ces deux paramètres globalement par le biais des options de ligne de commande.

## <a name="configuration-via-command-line-arguments"></a>Configuration par le biais d’arguments de ligne de commande

Vous avez à votre disposition plusieurs arguments de ligne de commande pour définir des paramètres globaux pour OPC Publisher. Ils sont décrits [ici](reference-command-line-arguments.md).


## <a name="configuration-via-the-built-in-opc-ua-server-interface"></a>Configuration par le biais de l’interface de serveur OPC UA intégrée

>[!NOTE] 
> Cette fonctionnalité est disponible uniquement dans les versions 2.5 et antérieures d’OPC Publisher.

OPC Publisher a un serveur OPC UA intégré, exécuté sur le port 62222. Il implémente trois méthodes OPC UA :

  - PublishNode
  - UnpublishNode
  - GetPublishedNodes

Cette interface est accessible à l’aide d’une application cliente OPC UA, par exemple [UA Expert](https://www.unified-automation.com/products/development-tools/uaexpert.html).

## <a name="configuration-via-iot-hub-direct-methods"></a>Configuration par le biais de méthodes directes IoT Hub

>[!NOTE] 
> Cette fonctionnalité est disponible uniquement dans les versions 2.5 et antérieures d’OPC Publisher.

OPC Publisher implémente les [méthodes directes IoT Hub](../iot-hub/iot-hub-devguide-direct-methods.md)suivantes, que vous pouvez appeler à partir d’une application (depuis n’importe où dans le monde) en tirant parti du [Kit SDK IoT Hub Device](../iot-hub/iot-hub-devguide-sdks.md) :

  - PublishNodes
  - UnpublishNodes
  - UnpublishAllNodes
  - GetConfiguredEndpoints
  - GetConfiguredNodesOnEndpoint
  - GetDiagnosticInfo
  - GetDiagnosticLog
  - GetDiagnosticStartupLog
  - ExitApplication
  - GetInfo

Nous avons fourni un [exemple d’application de configuration](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration), ainsi qu’une [application de lecture d’informations de diagnostic](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics) à partir d’OPC Publisher open source, qui tirent parti de cette interface.

## <a name="configuration-via-cloud-based-companion-rest-microservice"></a>Configuration par le biais du microservice REST auxiliaire basé sur le cloud

>[!NOTE] 
> Cette fonctionnalité est disponible uniquement dans les versions 2.6 et ultérieures d’OPC Publisher.

Un microservice auxiliaire basé sur le cloud avec une interface REST est décrit et disponible [ici](https://github.com/Azure/Industrial-IoT/blob/master/docs/services/publisher.md). Vous pouvez l’utiliser pour configurer OPC Publisher par le biais d’une interface compatible avec OpenAPI, par exemple Swagger.

## <a name="configuration-of-the-simple-json-telemetry-format-via-separate-configuration-file"></a>Configuration du format de télémétrie JSON simple par le biais d’un fichier de configuration distinct

>[!NOTE] 
> Cette fonctionnalité est disponible uniquement dans les versions 2.5 et antérieures d’OPC Publisher.

OPC Publisher permet de filtrer les parties du format de télémétrie simple non standardisé par le biais d’un fichier de configuration distinct, qui peut être spécifié à l’aide de l’option de ligne de commande tc. Si aucun fichier de configuration n’est spécifié, le format de télémétrie JSON complet est envoyé à IoT Hub. Le format du fichier de configuration de télémétrie distinct est décrit [ici](reference-opc-publisher-telemetry-format.md#opc-publisher-telemetry-configuration-file-format).

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez configuré OPC Publisher, l’étape suivante consiste à apprendre à régler les performances et la mémoire du module Edge :

> [!div class="nextstepaction"]
> [Réglage des performances et de la mémoire](tutorial-publisher-performance-memory-tuning-opc-publisher.md)