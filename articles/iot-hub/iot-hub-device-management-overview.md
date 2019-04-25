---
title: Vue d’ensemble de la gestion des appareils avec Azure IoT Hub | Microsoft Docs
description: 'Vue d’ensemble de la gestion des appareils dans Azure IoT Hub : cycle de vie des appareils d’entreprise et modèles de gestion des appareils tels que redémarrage, réinitialisation aux paramètres d’usine, mise à jour du microprogramme, configuration, représentations d’appareil, requêtes et travaux.'
author: bzurcher
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
origin.date: 08/24/2017
ms.date: 10/29/2018
ms.author: v-yiso
ms.openlocfilehash: bdc55af23568b5785a831e81f352400c728c902e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60400920"
---
# <a name="overview-of-device-management-with-iot-hub"></a>Vue d’ensemble de la gestion des appareils avec IoT Hub

Azure IoT Hub fournit les fonctionnalités et un modèle d’extensibilité qui permettent aux développeurs d’appareils et de serveur principal de créer des solutions robustes de gestion des appareils. Les appareils englobent les capteurs de contraintes et les microcontrôleurs à un seul objectif, les passerelles puissantes qui acheminent les communications pour les groupes d’appareils.  En outre, les cas d’utilisation et la configuration requise pour les opérateurs IoT varient considérablement entre les industries.  Malgré cette variation, la gestion des appareils avec IoT Hub fournit les fonctionnalités, les modèles et les bibliothèques de code pour répondre aux besoins d’un ensemble varié d’appareils et d’utilisateurs finaux.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Une composante essentielle de la création d’une solution IoT d’entreprise efficace consiste à fournir une stratégie portant sur la manière dont les opérateurs assurent la gestion continue de leur collection d’appareils. Les opérateurs IoT ont besoin d’outils et d’applications à la fois simples et fiables qui leur permettent de se concentrer sur les aspects plus stratégiques de leur travail. Cet article fournit :

- Une brève vue d’ensemble de l’approche de gestion des appareils Azure IoT Hub.
- Une description des principes courants de gestion des appareils.
- Une description du cycle de vie de l’appareil.
- Une vue d’ensemble des modèles courants de gestion des appareils.

## <a name="device-management-principles"></a>Principes de gestion des appareils
L’IoT implique un ensemble unique de problèmes de gestion des appareils et chaque solution d’entreprise doit tenir compte des principes suivants :

![Graphique Principes de gestion des appareils](media/iot-hub-device-management-overview/image4.png)

- **Mise à l’échelle et automatisation**: Solutions IoT nécessitent des outils simples qui peuvent automatiser les tâches de routine et activer un personnel opérationnel relativement réduit gérer des millions d’appareils. Au quotidien, les opérateurs doivent pouvoir gérer les opérations d’appareils à distance, en bloc et être avertis uniquement lorsque les problèmes qui surviennent nécessitent leur attention directe.

- **Transparence et compatibilité**: L’écosystème d’appareils est incroyablement varié. Les outils de gestion doivent être adaptés pour prendre en charge une multitude de classes, de plateformes et de protocoles d’appareils. Les opérateurs doivent pouvoir prendre en charge de nombreux types d’appareils, depuis les puces intégrées limitées à processus unique jusqu’aux ordinateurs puissants et entièrement fonctionnels.
- **La reconnaissance de contexte**: Les environnements IoT sont dynamiques et en constante évolution. La fiabilité du service est primordiale. Les opérations de gestion d’appareils doivent prendre en compte les facteurs suivants pour garantir que les interruptions de maintenance n’affectent pas les opérations critiques et ne créent pas de conditions dangereuses :
    * Fenêtres de maintenance SLA
    * États du réseau et de l’alimentation
    * Conditions d’utilisation
    * Géolocalisation des appareils
- **De nombreux rôles de service**: Pour les flux de travail uniques et les processus des rôles d’opérations IoT est cruciale. Le personnel en charge des opérations doit travailler en harmonie avec les contraintes des services informatiques en interne.  Ils doivent également trouver des façons durables pour faire remonter les informations sur les opérations d’appareil en temps réel aux superviseurs et autres rôles de gestion dans l’entreprise.

## <a name="device-lifecycle"></a>Cycle de vie des appareils
Il existe un ensemble d’étapes de gestion d’appareils générale qui sont communes à tous les projets IoT d’entreprise. Dans Azure IoT, il existe cinq phases dans le cycle de vie des appareils :

![Les cinq phases du cycle de vie des appareils Azure IoT : planifier, approvisionner, configurer, surveiller, mettre hors service](./media/iot-hub-device-management-overview/image5.png)

Dans chacune de ces cinq étapes, il existe plusieurs conditions destinées à l’opérateur des appareils qui doivent être respectées pour fournir une solution complète :

* **Plan**: Permettre aux opérateurs créer un schéma de métadonnées d’appareil qui leur permet de facilement et précisément rechercher et cibler un groupe d’appareils pour les opérations de gestion en bloc. Vous pouvez utiliser la représentation physique d’appareil pour stocker les métadonnées de cet appareil sous la forme d’étiquettes et de propriétés.
  
    *Pour aller plus loin* : 
    * [Prise en main des représentations d’appareils](iot-hub-node-node-twin-getstarted.md)
    * [Comprendre les représentations d’appareils](iot-hub-devguide-device-twins.md)
    * [Guide pratique pour utiliser des propriétés de jumeau d’appareil](tutorial-device-twins.md)
    * [Bonnes pratiques en matière de configuration d’appareil dans une solution IoT](iot-hub-configuration-best-practices.md)

* **Approvisionner**: Configurer de nouveaux appareils à IoT Hub et permettre aux opérateurs détecter immédiatement les fonctionnalités des appareils en toute sécurité.  Utilisez le registre des identités IoT Hub pour créer des informations d’identification et des identités d’appareils flexibles et effectuez cette opération en bloc à l’aide d’un travail. Créez les appareils afin qu’ils signalent leurs fonctionnalités et leur condition via les propriétés de l’appareil dans la représentation d’appareil.
  
    *Pour aller plus loin* : 
    * [Gérer les identités des appareils](iot-hub-devguide-identity-registry.md)
    * [Gestion en bloc des identités d’appareils](iot-hub-bulk-identity-mgmt.md)
    * [Guide pratique pour utiliser des propriétés de jumeau d’appareil](tutorial-device-twins.md)
    * [Bonnes pratiques en matière de configuration d’appareil dans une solution IoT](iot-hub-configuration-best-practices.md)
    * [Service Azure IoT Hub Device Provisioning](https://azure.microsoft.com/documentation/services/iot-dps)

* **Configurer** : Faciliter les modifications de configuration en bloc et les mises à jour du microprogramme des appareils tout en conservant l’intégrité et sécurité. Effectuez ces opérations de gestion d’appareils en bloc à l’aide des propriétés requises ou de méthodes directes et de travaux de diffusion.
  
    *Pour aller plus loin* :
    * [Guide pratique pour utiliser des propriétés de jumeau d’appareil](tutorial-device-twins.md)
    * [Configurer et surveiller des appareils IoT à grande échelle](iot-hub-auto-device-config.md)
    * [Bonnes pratiques en matière de configuration d’appareil dans une solution IoT](iot-hub-configuration-best-practices.md)

* **Moniteur**: Surveiller l’intégrité de la collection de périphérique, l’état des opérations en cours et avertir les utilisateurs à des problèmes qui peuvent nécessiter leur attention.  Appliquez la représentation physique d’appareil pour autoriser les appareils à signaler des conditions de fonctionnement en temps réel et l’état des opérations de mise à jour. Créez des rapports de tableau de bord puissants qui font remonter les problèmes les plus immédiats à l’aide de requêtes sur la représentation physique d’appareil.
  
    *Pour aller plus loin* : 
    * [Guide pratique pour utiliser des propriétés de jumeau d’appareil](tutorial-device-twins.md)
    * [Langage de requête IoT Hub pour les jumeaux d’appareil, les travaux et le routage des messages](iot-hub-devguide-query-language.md)
    * [Configurer et surveiller des appareils IoT à grande échelle](iot-hub-auto-device-config.md)
    * [Bonnes pratiques en matière de configuration d’appareil dans une solution IoT](iot-hub-configuration-best-practices.md)

* **Mettre hors service**: Remplacer ou retirer des appareils après une défaillance, cycle, de mettre à niveau ou à la fin de la durée de vie du service.  Utilisez la représentation d’appareil pour conserver les informations sur l’appareil si l’appareil physique est remplacé ou archivé en cas de mise hors service. Utilisez le registre des identités IoT Hub pour la révocation en toute sécurité des informations d’identification et des identités des appareils.
  
    *Pour aller plus loin* : 
    * [Guide pratique pour utiliser des propriétés de jumeau d’appareil](tutorial-device-twins.md)
    * [Gérer les identités des appareils](iot-hub-devguide-identity-registry.md)

## <a name="device-management-patterns"></a>Modèle de gestion des appareils

IoT Hub permet de mettre en œuvre l’ensemble suivant de modèles de gestion des appareils. Les [didacticiels de gestion des appareils](iot-hub-node-node-device-management-get-started.md) vous montrent plus en détail comment étendre ces modèles pour les adapter à votre situation et comment concevoir de nouveaux modèles basés sur ces modèles de base.

* **Redémarrez**: L’application back-end informe l’appareil via une méthode directe qu’un redémarrage a été lancée.  L’appareil utilise les propriétés signalées pour mettre à jour l’état de redémarrage de l’appareil.
  
    ![Graphique du modèle de redémarrage de la gestion des appareils](./media/iot-hub-device-management-overview/reboot-pattern.png)

* **Les paramètres d’usine**: L’application back-end informe l’appareil via une méthode directe qu’une réinitialisation des paramètres a été lancée. L’appareil utilise les propriétés signalées pour mettre à jour l’état de réinitialisation aux paramètres d’usine de l’appareil.
  
    ![Graphique du modèle de réinitialisation aux paramètres d’usine de la gestion des appareils](./media/iot-hub-device-management-overview/facreset-pattern.png)

* **Configuration** : L’application back-end utilise les propriétés souhaitées pour configurer le logiciel en cours d’exécution sur l’appareil. L’appareil utilise les propriétés signalées pour mettre à jour l’état de configuration de l’appareil.
  
    ![Graphique du modèle de configuration de la gestion des appareils](./media/iot-hub-device-management-overview/configuration-pattern.png)

* **Mise à jour du microprogramme**: L’application back-end utilise une configuration de la gestion automatique des appareils pour sélectionner les appareils de recevoir la mise à jour pour indiquer les appareils où trouver la mise à jour et pour surveiller le processus de mise à jour. L’appareil met en œuvre un processus en plusieurs étapes pour télécharger, vérifier et appliquer l’image du microprogramme, puis redémarre avant de se reconnecter au service IoT Hub. Tout au long de ce processus en plusieurs étapes, l’appareil utilise les propriétés signalées pour mettre à jour la progression et l’état de l’appareil.
  
    ![Graphique du modèle de mise à jour du microprogramme de la gestion des appareils](media/iot-hub-device-management-overview/fwupdate-pattern.png)

* **Rapports de progression et l’état**: Requêtes de représentation d’appareil principal s’exécute de nouveau la solution sur un ensemble d’appareils afin de créer des rapports sur l’état et la progression des actions en cours d’exécution sur les appareils.
  
    ![Graphique du modèle de signalement de la progression et de l’état de la gestion des appareils](./media/iot-hub-device-management-overview/report-progress-pattern.png)

## <a name="next-steps"></a>Étapes suivantes
Les fonctionnalités, les modèles et les bibliothèques de code fournis par IoT Hub pour la gestion des appareils vous permettent de créer des applications IoT qui respectent les exigences d’opérateur IoT en entreprise dans chaque phase du cycle de vie de l’appareil.

Pour plus d’informations sur les fonctionnalités de gestion des appareils dans IoT Hub, consultez le didacticiel [Prise en main de la gestion des appareils](iot-hub-node-node-device-management-get-started.md).