---
title: Préparer des appareils et des déploiements pour la production - Azure IoT Edge | Microsoft Docs
description: Découvrez comment faire passer votre solution Azure IoT Edge du développement à la production, et notamment comment configurer vos appareils avec les certificats nécessaires et élaborer un plan de déploiement des futures mises à jour du code.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 86b33bfa0f5383ac68080e2f8f7f9a004a1364a0
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/20/2018
ms.locfileid: "53652603"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>Préparer le déploiement en production d’une solution IoT Edge

Au moment de faire passer votre solution IoT Edge du développement à la production, vérifiez qu’elle est configurée de façon à offrir des performances continues.

Les informations indiquées dans cet article ne se valent pas toutes. Pour clarifier l’ordre de priorité, chaque section commence par une liste qui divise le travail en deux sections : **Important**, donc à effectuer avant de passer en production, ou **Utile**, c’est-à-dire bon à savoir.

## <a name="device-configuration"></a>Configuration de l’appareil

Il existe de nombreux types d’appareils IoT Edge : un Raspberry Pi, un portable, une machine virtuelle sur un serveur, etc. Vous pouvez avoir accès à l’appareil physiquement ou via une connexion virtuelle ; il peut aussi être isolé pendant de longues périodes. Dans les deux cas, l’objectif est de vérifier qu’il est configuré de façon à avoir un fonctionnement adéquat. 

* **Important**
    * Installer les certificats de production
    * Élaborer un plan de gestion des appareils
    * Utiliser Moby comme moteur de conteneur

* **Utile**
    * Choisir un protocole en amont

### <a name="install-production-certificates"></a>Installer les certificats de production

Un certificat d’autorité de certification doit être installé sur chaque appareil IoT Edge en production. Il est ensuite déclaré auprès du runtime IoT Edge dans le fichier config.yaml. Pour faciliter le développement et les tests, le runtime IoT Edge crée des certificats temporaires si aucun certificat n’est déclaré dans le fichier config.yaml. Toutefois, ces certificats temporaires expirent au bout de trois mois et ne sont pas sécurisés pour les scénarios de production. 

Pour comprendre le rôle du certificat d’autorité de certification d’appareil, voir [Comment Azure IoT Edge utilise les certificats](iot-edge-certs.md).

Pour savoir comment installer des certificats sur un appareil IoT Edge et y faire référence dans le fichier config.yaml, voir [Configurer un appareil IoT Edge comme passerelle transparente](how-to-create-transparent-gateway.md). Les étapes de configuration des certificats sont les mêmes que l’appareil soit ou non utilisé comme passerelle. Cet article fournit des scripts permettant de générer des exemples de certificats à des fins de test uniquement. N’utilisez pas ces exemples de certificats en production. 

### <a name="have-a-device-management-plan"></a>Élaborer un plan de gestion des appareils

Avant de mettre un appareil en production, il faut savoir comment gérer les mises à jour à venir. Pour un appareil IoT Edge, il peut y avoir plusieurs composants à mettre à jour :

* Microprogramme de l’appareil
* Bibliothèques du système d’exploitation
* Moteur de conteneur, comme Moby
* Démon IoT Edge
* Certificats d’autorité de certification

Pour savoir comment mettre à jour le démon IoT Edge, voir [Mettre à jour le runtime IoT Edge](how-to-update-iot-edge.md). Les méthodes actuelles de mise à jour du démon IoT Edge exigent un accès physique ou SSH à l’appareil IoT Edge. Si vous avez de nombreux appareils à mettre à jour, vous pouvez ajouter les étapes de mise à jour à un script ou utiliser un outil d’automatisation comme Ansible pour effectuer des mises à jour à grande échelle.

### <a name="use-moby-as-the-container-engine"></a>Utiliser Moby comme moteur de conteneur

La présence d’un moteur de conteneur fait partie des prérequis de tous les appareils IoT Edge. Seul le moteur Moby est pris en charge en production. Les autres moteurs de conteneur, comme Docker, fonctionnent avec IoT Edge et sont utilisables à des fins de développement. Le moteur Moby peut être redistribué s’il est utilisé avec Azure IoT Edge ; par ailleurs, Microsoft en assure la maintenance. Les autres moteurs de conteneur ne sont pas pris en charge sur un appareil IoT Edge.

### <a name="choose-upstream-protocol"></a>Choisir un protocole en amont

Le protocole (et donc le port) utilisé pour les communications en amont vers IoT Hub peut être configuré pour l’agent Edge comme pour le hub Edge. Le protocole par défaut, AMQP, est modifiable en fonction de la configuration réseau. 

Les modules runtime ont tous les deux une variable d’environnement **UpstreamProtocol**, dont les valeurs valides sont les suivantes : 

* MQTT
* AMQP
* MQTTWS
* AMQPWS

Configurez la variable UpstreamProtocol pour l’agent Edge dans le fichier config.yaml sur l’appareil. Par exemple, si l’appareil IoT Edge se trouve derrière un serveur proxy qui bloque les ports AMQP, il peut se révéler nécessaire de configurer l’agent Edge de façon à ce qu’il utilise AMQP sur WebSocket (AMQPWS) pour établir la connexion initiale à IoT Hub. 

Une fois l’appareil IoT Edge connecté, poursuivez la configuration de la variable UpstreamProtocol pour les deux modules de runtime dans les déploiements à venir. Vous trouverez un exemple de ce processus dans [Configurer un appareil IoT Edge pour communiquer via un serveur proxy](how-to-configure-proxy-support.md).

## <a name="deployment"></a>Déploiement

* **Utile**
    * Rester cohérent avec le protocole en amont
    * Réduire l’espace mémoire utilisé par le hub Edge
    * Ne pas utiliser les versions de débogage des images de module

### <a name="be-consistent-with-upstream-protocol"></a>Rester cohérent avec le protocole en amont

Si vous avez configuré l’agent Edge sur votre appareil IoT Edge de façon à ce qu’il utilise un protocole autre que le protocole par défaut (AMQP), déclarez le même protocole dans tous les déploiements ultérieurs. Par exemple, si votre appareil IoT Edge se trouve derrière un serveur proxy qui bloque les ports AMQP, vous avez probablement configuré l’appareil se sorte qu’il se connecte via AMQP sur WebSocket (AMQPWS). Lorsque vous déployez des modules sur l’appareil, si vous ne configurez pas le même protocole APQPWS pour l’agent Edge et le hub Edge, le protocole par défaut, AMQP, remplacera les paramètres et vous empêchera de vous reconnecter. 

Il suffit de configurer la variable d’environnement UpstreamProtocol pour les deux modules : l’agent Edge et le hub Edge. Tous les modules supplémentaires adoptent le protocole défini dans les modules de runtime. 

Vous trouverez un exemple de ce processus dans [Configurer un appareil IoT Edge pour communiquer via un serveur proxy](how-to-configure-proxy-support.md).

### <a name="reduce-memory-space-used-by-edge-hub"></a>Réduire l’espace mémoire utilisé par le hub Edge

Si vous déployez des appareils contraints avec une quantité de mémoire disponible limitée, vous pouvez configurer le hub Edge de façon à ce qu’il s’exécute avec une capacité rationalisée et utilise moins d’espace disque. Ces configurations limitent les performances du hub Edge. Trouvez l’équilibre adapté à votre solution. 

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>Ne pas optimiser les performances sur les appareils contraints

Le hub Edge, optimisé par défaut du point de vue des performances, tente d’allouer de grands blocs de mémoire. Cette configuration risque provoquer des problèmes de stabilité sur les petits appareils, comme le Raspberry Pi. Si vous déployez des appareils offrant des ressources limitées, vous pouvez si vous le souhaitez définir la variable d’environnement **OptimizeForPerformance** sur **false** sur le hub Edge. 

Pour plus d’informations, voir [Problèmes de stabilité sur les appareils avec contraintes de ressources](troubleshoot.md#stability-issues-on-resource-constrained-devices).

#### <a name="disable-unused-protocols"></a>Désactiver les protocoles inutilisés

Il existe un autre moyen d’optimiser les performances du hub Edge et de réduire son utilisation de la mémoire : désactiver les têtes de tous les protocoles non utilisés dans la solution. 

Les têtes de protocole se configurent en définissant des variables d’environnement booléennes pour le module du hub Edge dans les manifestes de déploiement. Voici les trois variables en question :

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

Ces variables comportent toutes les trois *deux traits de soulignement*. Elles peuvent être définies sur true ou false. 

#### <a name="reduce-storage-time-for-messages"></a>Réduire le temps de stockage des messages

Le module du hub Edge stocke temporairement les messages si, pour une raison ou pour une autre, il n’est pas possible de les remettre à IoT Hub. Vous pouvez configurer la durée pendant laquelle le hub Edge conserve les messages non remis avant qu’ils n’expirent. Si vous avez des problèmes de mémoire sur votre appareil, vous pouvez diminuer la valeur **timeToLiveSecs** dans le jumeau de module du hub Edge. 

La valeur par défaut du paramètre timeToLiveSecs est de 7 200 secondes, soit deux heures. 

### <a name="do-not-use-debug-versions-of-module-images"></a>Ne pas utiliser les versions de débogage des images de module

Lors du passage de scénarios de test à des scénarios de production, pensez à supprimer les configurations de débogage des manifestes de déploiement. Vérifiez qu’aucune des images de modules des manifestes de déploiement ne comporte le suffixe **\.debug**. Si vous avez ajouté des options de création pour exposer les ports dans les modules à des fins de débogage, supprimez-les également. 

## <a name="container-management"></a>Gestion de conteneur

* **Important**
    * Gérer l’accès au registre de conteneurs
    * Utiliser des balises pour gérer les versions

### <a name="manage-access-to-your-container-registry"></a>Gérer l’accès au registre de conteneurs

Avant de déployer des modules sur des appareils IoT Edge en production, veillez à contrôler l’accès à votre registre de conteneurs pour éviter que des intrus ne puissent accéder à vos images conteneur ou les modifier. Utilisez un registre de conteneurs privé, et non public, pour gérer les images conteneur. 

Dans les tutoriels et autres documents, nous prescrivons d’utiliser les mêmes informations d’identification de registre de conteneur sur l’appareil IoT Edge que sur l’ordinateur de développement. Ces instructions, qui ne sont destinées qu’à aider à configurer plus facilement les environnements de test et de développement, ne doivent pas être suivies dans un scénario de production. Azure Container Registry recommande de [s’authentifier auprès des principaux de service](../container-registry/container-registry-auth-service-principal.md) lorsque les applications ou les services extraient des images conteneur de manière automatisée ou sans assistance, comme les appareils IoT Edge. Créez un principal de service avec accès en lecture seule au registre de conteneurs, et indiquez ce nom d’utilisateur et ce mot de passe dans le manifeste de déploiement.

### <a name="use-tags-to-manage-versions"></a>Utiliser des balises pour gérer les versions

Une balise est un concept Docker servant à faire la distinction entre les versions des conteneurs Docker. Ce sont des suffixes comme **1.0**, ajoutés à la fin d’un référentiel de conteneur. Exemple : **mcr.microsoft.com/azureiotedge-agent:1.0**. Les balises étant mutables et modifiables pour pointer vers un autre conteneur à tout moment, il est essentiel que votre équipe se mette d’accord sur une convention à suivre pour mettre à jour vos images de module par la suite. 

Les balises aident également à appliquer des mises à jour sur les appareils IoT Edge. Lorsque vous envoyez une version mise à jour d’un module à votre registre de conteneurs, incrémentez la balise. Ensuite, transmettez un nouveau déploiement sur vos appareils avec la balise incrémentée. Le moteur de conteneur la reconnaîtra comme une nouvelle version et extraira la dernière version du module sur l’appareil. 

Pour un exemple de convention de balise, voir [Mettre à jour le runtime IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags) : vous découvrirez comment IoT Edge utilise des balises propagées et des balises spécifiques pour effectuer le suivi des versions. 

## <a name="networking"></a>Mise en réseau

* **Utile**
    * Vérifier la configuration sortante/entrante
    * Mettre les connexions sur liste verte
    * Configurer la communication via un proxy

### <a name="review-outboundinbound-configuration"></a>Vérifier la configuration sortante/entrante

Les canaux de communication entre Azure IoT Hub et IoT Edge sont toujours configurés pour être sortants. Dans la plupart des scénarios IoT Edge, seules trois connexions sont nécessaires. Une connexion doit être établie entre le moteur de conteneur et le ou les registres de conteneurs qui contiennent les images de module. Le runtime IoT Edge doit être connecté à IoT Hub pour récupérer des informations de configuration des appareils et envoyer des messages et des données de télémétrie. Enfin, si vous utilisez l’approvisionnement automatique, le démon IoT Edge doit se connecter au service Device Provisioning. Pour plus d’informations, voir [Règles de configuration du pare-feu et des ports](troubleshoot.md#firewall-and-port-configuration-rules-for-iot-edge-deployment).

### <a name="whitelist-connections"></a>Mettre les connexions sur liste verte

Si votre configuration réseau exige de mettre explicitement sur liste verte les connexions effectuées à partir d’appareils IoT Edge, passez en revue la liste suivante de composants IoT Edge :

* **L’agent IoT Edge** ouvre une connexion AMQP/MQTT persistante à IoT Hub, éventuellement sur WebSockets. 
* Le **hub IoT Edge** ouvre une seule connexion AMQP persistante ou plusieurs connexions MQTT à IoT Hub, éventuellement sur WebSockets. 
* Le **démon IoT Edge** effectue des appels HTTPS intermittents à IoT Hub. 

Dans les trois cas, le nom DNS respecte le modèle \*.azure-devices.net. 

Par ailleurs, le **Moteur de conteneur** effectue des appels aux registres de conteneurs via HTTPS. Pour récupérer les images de conteneur du runtime IoT Edge, le nom DNS est mcr.microsoft.com. Le moteur de conteneur se connecte aux autres registres configurés dans le déploiement. 

Cette liste de vérification est un point de départ pour les règles de pare-feu :

   | URL (\* = caractère générique) | Ports TCP sortants | Usage |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Registre de conteneurs Microsoft |
   | global.azure-devices-provisioning.net  | 443 | Accès DPS (facultatif) |
   | \*.azurecr.io | 443 | Registres de conteneurs personnels et tiers |
   | \*.blob.core.windows.net | 443 | Téléchargement de deltas d’image | 
   | \*.azure-devices.net | 5671, 8883, 443 | Accès IoT Hub |
   | \*.docker.io  | 443 | Accès Docker (facultatif) |

### <a name="configure-communication-through-a-proxy"></a>Configurer la communication via un proxy

Si vos appareils sont destinés à être déployés sur un réseau qui utilise un serveur proxy, ils doivent être en mesure de communiquer via le proxy pour accéder à IoT Hub et aux registres de conteneurs. Pour plus d’informations, voir [Configurer un appareil IoT Edge de façon à ce qu’il communique via un serveur proxy](how-to-configure-proxy-support.md).

## <a name="solution-management"></a>Gestion de solution

* **Utile**
    * Configurer les journaux et les diagnostics
    * Prendre en compte les tests et les pipelines CI/CD

### <a name="set-up-logs-and-diagnostics"></a>Configurer les journaux et les diagnostics

Sous Linux, le démon IoT Edge utilise journald comme pilote de journalisation par défaut. Vous pouvez vous servir de l’outil en ligne de commande `journalctl` pour interroger les journaux du démon. Sous Windows, le démon IoT Edge utilise les diagnostics PowerShell. Interrogez les journaux du démon avec `Get-WinEvent`. Les modules IoT Edge utilisent le pilote JSON (pilote par défaut de Docker) pour la journalisation.  

Lorsque vous testez un déploiement IoT Edge, vous pouvez généralement accéder à vos appareils pour récupérer les journaux et résoudre les problèmes. Dans un scénario de déploiement, vous n’avez pas forcément cette option. Réfléchissez à la façon dont vous allez collecter des informations sur vos appareils en production. Il est possible d’utiliser un module de journalisation, par exemple, [logspout-loganalytics](https://github.com/veyalla/logspout-loganalytics), qui recueille des informations auprès des autres modules et les envoie vers le cloud. Vous pouvez également concevoir votre propre module de journalisation. 

Si vous craignez que les journaux ne deviennent trop volumineux sur un appareil avec contraintes de ressources, plusieurs possibilités s’offrent à vous pour réduire l’utilisation de la mémoire. 

* Vous pouvez en particulier limiter la taille de tous les fichiers journaux Docker dans le démon Docker proprement dit. Pour Linux, configurez le démon à l’adresse `/etc/docker/daemon.json` ; pour Windows, à l’adresse `C:\ProgramData\docker\confige\daemon.json`. 
* Si vous souhaitez ajuster la taille du fichier journal de chaque conteneur, vous pouvez utiliser les options de création de chaque module. 
* Configurez Docker de façon à ce qu’il gère automatiquement les journaux en définissant journald comme pilote de journalisation par défaut de Docker. 
* Supprimez régulièrement les anciens journaux de votre appareil en installant un outil logrotate pour Docker. Utilisez la spécification de fichier suivante : 

   ```
   /var/lib/docker/containers/*/*-json.log{
       copytruncate
       daily
       rotate7
       delaycompress
       compress
       notifempty
       missingok
   }
   ```

### <a name="consider-tests-and-cicd-pipelines"></a>Prendre en compte les tests et les pipelines CI/CD

Pour maximiser l’efficacité du scénario de déploiement IoT Edge, intégrez votre déploiement de production dans vos pipelines de tests et CI/CD. Azure IoT Edge prend en charge plusieurs plateformes CI/CD, notamment Azure DevOps. Pour plus d’informations, voir [Intégration continue et déploiement continu sur Azure IoT Edge](how-to-ci-cd.md).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur le [déploiement automatique IoT Edge](module-deployment-monitoring.md).
* Découvrez comment IoT Edge prend en charge [l’intégration continue et le déploiement continu](how-to-ci-cd.md).
