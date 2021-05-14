---
title: Microsoft Connected Cache dans une configuration Azure IoT Edge pour l’Internet industriel des objets | Microsoft Docs
description: Tutoriel Microsoft Connected Cache dans une configuration Azure IoT Edge pour l’Internet industriel des objets
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 083c7bf6edc7da1fd617487e91b0a3848fb401fe
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811809"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-microsoft-connected-cache-within-an-azure-iot-edge-for-industrial-iot-configuration"></a>Exemple de scénario de déploiement de Microsoft Connected Cache Preview : Microsoft Connected Cache dans une configuration Azure IoT Edge pour l’Internet industriel des objets

Les réseaux de fabrication sont souvent organisés en niveaux hiérarchiques d’après le [modèle réseau Purdue](https://en.wikipedia.org/wiki/Purdue_Enterprise_Reference_Architecture) (inclus dans les normes [ISA 95](https://en.wikipedia.org/wiki/ANSI/ISA-95) et [ISA 99](https://www.isa.org/standards-and-publications/isa-standards/isa-standards-committees/isa99)). Dans ces réseaux, seul le niveau supérieur est connecté au cloud, et les niveaux inférieurs dans la hiérarchie peuvent uniquement communiquer avec les niveaux Nord et Sud adjacents.

Cet exemple GitHub, [Azure IoT Edge pour l’IOT industriel](https://github.com/Azure-Samples/iot-edge-for-iiot), déploie les composants suivants :

* Réseau Purdue simulé dans Azure
* Ressources industrielles 
* Hiérarchie de passerelles Azure IoT Edge
  
Ces composants permettent d’obtenir des données industrielles et de les charger en toute sécurité dans le cloud sans compromettre la sécurité du réseau. Microsoft Connected Cache peut être déployé pour prendre en charge le téléchargement de contenu à tous les niveaux au sein du réseau conforme à la norme ISA 95.

La clé de la configuration des déploiements de Microsoft Connected Cache dans un réseau ISA 95 est de configurer le proxy OT *et* l’hôte en amont sur la passerelle IoT Edge du niveau L3.

1. Configurez les déploiements de Microsoft Connected Cache aux niveaux L5 et L4 comme décrit dans l’exemple de passerelle IoT Edge imbriquée à deux niveaux 
2. Le déploiement sur la passerelle IoT Edge du niveau L3 doit spécifier les éléments suivants :
   
   * UPSTREAM_HOST : IP/FQDN de la passerelle IoT Edge du niveau L4, à laquelle le cache Microsoft Connected Cache du niveau L3 demande du contenu.
   * UPSTREAM_PROXY : IP/FQDN:PORT du serveur proxy OT.

3. Le proxy OT doit ajouter le FQDN/l’adresse IP du cache MCC L4 à la liste verte.

Pour vérifier le bon fonctionnement de Microsoft Connected Cache, exécutez la commande suivante dans le terminal de l’appareil IoT Edge qui héberge le module ou de tout autre appareil sur le réseau. Remplacez \<Azure IoT Edge Gateway IP\> par l’adresse IP ou le nom d’hôte de votre passerelle IoT Edge. (Consultez les détails de la variable d’environnement pour plus d’informations sur la visibilité de ce rapport.)

```bash
    wget http://<L3 IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```