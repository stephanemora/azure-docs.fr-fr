---
title: Déploiement automatique pour les groupes d’appareils - Azure IoT Edge | Microsoft Docs
description: Utiliser des déploiements automatiques dans Azure IoT Edge pour gérer des groupes d’appareils en fonction d’étiquettes partagées
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 376ee74732daf526b31129fa8c93cbaa32350eae
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58107812"
---
# <a name="understand-iot-edge-automatic-deployments-for-single-devices-or-at-scale"></a>Comprendre les déploiements automatiques IoT Edge pour un seul ou de nombreux appareils

Les appareils Azure IoT Edge suivent un [cycle de vie des appareils](../iot-hub/iot-hub-device-management-overview.md) similaire à d’autres types d’appareils IoT :

1. Provisionnez de nouveaux appareils IoT Edge en créant une image d’un appareil doté d’un système d’exploitation et en installant le [runtime IoT Edge](iot-edge-runtime.md).
2. Configurez les appareils de façon à ce qu’ils exécutent les [modules IoT Edge](iot-edge-modules.md), puis analysez leur fonctionnement. 
3. Enfin, mettez les appareils hors service lorsqu’ils sont remplacés ou deviennent obsolètes.  

Azure IoT Edge propose deux façons de configurer les modules à exécuter sur les appareils IoT Edge : une pour le développement et les itérations rapides sur un seul appareil (vous avez utilisé cette méthode dans les [tutoriels](tutorial-deploy-function.md) Azure IoT Edge), et une pour la gestion de grandes flottes d’appareils IoT Edge. Ces deux approches sont disponibles dans le portail Azure et par programmation. Pour cibler des groupes ou un grand nombre d’appareils, vous pouvez spécifier les appareils sur lesquels vous aimeriez déployer vos modules en utilisant des [étiquettes](../iot-edge/how-to-deploy-monitor.md#identify-devices-using-tags) dans le jumeau d’appareil. Les étapes suivantes parlent d’un déploiement dans un groupe d’appareils de l’État de Washington identifié avec la propriété tags. 

Cet article se concentre sur les étapes de configuration et de surveillance des appareils, collectivement appelées des déploiements automatiques IoT Edge. Les étapes de déploiement global sont les suivantes : 

1. Un opérateur définit un déploiement qui décrit un ensemble de modules, ainsi que les appareils cibles. Chaque déploiement possède un manifeste de déploiement qui reflète ces informations. 
2. Le service de IoT Hub communique avec tous les appareils ciblés pour les configurer avec les modules souhaités. 
3. Le service IoT Hub récupère l’état des appareils IoT Edge et les met à la disposition de l’opérateur.  Par exemple, un opérateur voit quand un appareil Edge n’est pas correctement configuré ou si un module échoue durant l’exécution. 
4. À tout moment, les nouveaux appareils IoT Edge qui remplissent les conditions de ciblage sont configurés pour le déploiement. Par exemple, un déploiement qui cible tous les appareils IoT Edge dans l’état de Washington configure automatiquement un nouvel appareil IoT Edge une fois qu’il est approvisionné et ajouté au groupe des périphériques de l’état de Washington. 
 
Cet article décrit chaque composant impliqué dans la configuration et la surveillance d’un déploiement. Pour connaître la procédure de création et de mise à jour d’un déploiement, consultez [Déployer et surveiller des modules IoT Edge à l’échelle](how-to-deploy-monitor.md).

## <a name="deployment"></a>Déploiement

Un déploiement automatique IoT Edge assigne des images de module IoT Edge à exécuter en tant qu’instances sur un ensemble ciblé d’appareils IoT Edge. Il fonctionne en configurant un manifeste de déploiement IoT Edge pour inclure une liste de modules comprenant les paramètres d’initialisation correspondants. Un déploiement peut être affecté à un appareil unique (basé sur l’ID de l’appareil) ou à un groupe d’appareils (basé sur des balises). Lorsqu’un appareil IoT Edge reçoit un manifeste de déploiement, il télécharge et installe les images conteneurs dans les référentiels conteneurs respectifs, puis les configure en conséquence. Une fois qu’un déploiement est créé, un opérateur peut surveiller l’état de déploiement pour voir si les appareils ciblés sont configurés correctement.

Seuls les appareils IoT Edge peuvent être configurés avec un déploiement. L’appareil destiné à recevoir le déploiement doit satisfaire aux prérequis suivants :

* Le système d’exploitation de base
* Un système de gestion de conteneur, comme Moby ou Docker
* L’approvisionnement du runtime IoT Edge 

### <a name="deployment-manifest"></a>Manifeste de déploiement

Un manifeste de déploiement est un document JSON qui décrit les modules devant être configurés sur les appareils IoT Edge ciblés. Il contient les métadonnées de configuration pour tous les modules, y compris les modules système requis (en particulier l’agent de IoT Edge et l’hub IoT Edge).  

Les métadonnées de configuration pour chaque module incluent : 

* Version 
* Type 
* État (par exemple, en cours d’exécution ou arrêté) 
* Stratégie de redémarrage 
* Registre d’images et de conteneurs
* Itinéraires pour les données en entrée et en sortie 

Si l’image du module est stockée dans un registre de conteneurs privé, l’agent IoT Edge conserve les informations d’identification du registre. 

### <a name="target-condition"></a>Condition cible

La condition cible est évaluée en permanence tout au long de la durée de vie du déploiement. Les nouveaux appareils qui répondent aux exigences sont inclus ; tous les appareils existants qui n’y satisfont plus sont supprimés. Le déploiement est réactivé si le service détecte une modification de la condition cible. 

Prenons l’exemple d’un déploiement A comportant la condition cible tags.environment = 'prod'. Au lancement du déploiement, il y a 10 appareils de production. Les modules sont correctement installés sur ces 10 appareils. L’état de l’Agent IoT Edge affiche 10 appareils au total, 10 réponses correctes, 0 échecs de réponse et 0 réponses en attente. On ajoute maintenant cinq appareils avec tags.environment = 'prod'. Le service détecte la modification, et l’état de l’Agent IoT Edge devient : 15 appareils au total, 10 réponses correctes, 0 échecs de réponses et 5 réponses en attente quand il tente de déployer les cinq nouveaux appareils.

Utilisez une condition booléenne sur des balises des jumeaux d’appareils ou deviceId pour sélectionner les appareils cibles. Si vous souhaitez utiliser une condition avec des balises, vous devez ajouter les propriétés "tags":{} dans le jumeau d’appareil au même niveau. [En savoir plus sur les balises dans le jumeau d’appareil](../iot-hub/iot-hub-devguide-device-twins.md)

Exemples de conditions cibles :

* deviceId =’linuxprod1’
* tags.environment =’prod’
* tags.environment = ’prod’ AND tags.location = ’westus’
* tags.environment = ’prod’ OR tags.location = ’westus’
* tags.operator = ’John’ AND tags.environment = ’prod’ NOT deviceId = ’linuxprod1’

Voici quelques-unes des contraintes qui s’appliquent à la création d’une condition cible :

* Dans le jumeau d’appareil, seuls les balises et deviceId permettent de créer une condition cible.
* Les guillemets doubles ne sont autorisés nulle part dans la condition cible. Utilisez des guillemets simples.
* Les guillemets simples représentent les valeurs de la condition cible. Par conséquent, vous devez échapper le guillemet simple avec un autre guillemet simple s’il fait partie du nom de l’appareil. Par exemple, pour cibler un appareil nommé `operator'sDevice`, écrivez `deviceId='operator''sDevice'`.
* Les nombres, les lettres et les caractères suivants sont autorisés dans les valeurs de la condition cible : `-:.+%_#*?!(),=@;$`.

### <a name="priority"></a>Priorité

Une priorité définit si un déploiement doit être appliqué à un appareil ciblé par rapport à d’autres déploiements. Une priorité de déploiement est un entier positif. Plus le nombre est élevé, plus la priorité est supérieure. Si un périphérique IoT Edge est ciblé par plusieurs déploiements, le déploiement avec la priorité la plus élevée s’applique.  Les déploiements avec une priorité plus faible ne sont pas appliqués, ni fusionnés.  Si un périphérique est ciblé par au moins deux déploiements de priorité égale, c’est le déploiement créé le plus récemment (déterminé par l’horodatage de création) qui s’applique.

### <a name="labels"></a>Étiquettes 

Les étiquettes sont des paires clé-valeur de type chaîne que vous pouvez utiliser pour filtrer et grouper les déploiements. Un déploiement peut avoir plusieurs étiquettes. Les étiquettes sont facultatives et n’ont aucun impact sur la configuration des appareils IoT Edge. 

### <a name="deployment-status"></a>état du déploiement

Un déploiement peut être surveillé pour déterminer s’il est correctement appliqué pour n’importe quel appareil IoT Edge ciblé.  Un périphérique Edge ciblé apparaîtra dans une ou plusieurs des catégories d’état suivantes : 

* **Cible** affiche les périphériques IoT Edge qui correspondent à la condition de ciblage du déploiement.
* **Réel** affiche les appareils IoT Edge ciblés qui ne sont pas ciblés par un autre déploiement de priorité plus élevée.
* **Intègre** affiche les appareils IoT Edge ayant signalé au service que les modules ont été déployés correctement. 
* **Défectueux** affiche les appareils IoT Edge ayant signalé au service qu’un ou plusieurs modules n’ont pas été déployés correctement. Pour examiner l’erreur plus en détail, connectez-vous à distance à ces appareils et consultez les fichiers journaux.
* **Inconnu** affiche les appareils IoT Edge qui n’ont pas signalé d’état concernant le déploiement. Pour approfondir vos recherches, consultez les informations de service et les fichiers journaux.

## <a name="phased-rollout"></a>Déploiement progressif 

Un déploiement progressif est un processus global par lequel un opérateur déploie les modifications sur un ensemble étendu d’appareils IoT Edge. L’objectif est d’apporter des modifications progressivement afin de réduire le risque d’étendre les modifications avec rupture à une plus grande échelle.  

Un déploiement progressif est exécuté dans les phases et étapes suivantes : 

1. Établissez un environnement de tests d’appareils IoT Edge en les approvisionnant et en paramétrant une balise de jumeau d’appareil comme `tag.environment='test'`. L’environnement de test doit refléter l’environnement de production que le déploiement va cibler. 
2. Créez un déploiement comprenant les modules et les configurations souhaités. La condition de ciblage doit cibler l’environnement de test des appareils IoT Edge.   
3. Validez la nouvelle configuration du module dans l’environnement de test.
4. Mettez à jour le déploiement pour inclure un sous-ensemble d’appareils de production IoT Edge en ajoutant une nouvelle balise à la condition de ciblage. En outre, assurez-vous que la priorité pour le déploiement est supérieure aux autres déploiements actuellement ciblés sur ces appareils. 
5. Vérifiez que le déploiement a réussi sur les appareils IoT ciblés en consultant l’état du déploiement.
6. Mettez à jour le déploiement afin de cibler tous les appareils de production IoT Edge restants.

## <a name="rollback"></a>Restauration

Les déploiements peuvent être restaurés en cas d’erreur ou de mauvaise configuration.  Étant donné qu’un déploiement définit la configuration de module absolue pour un appareil IoT Edge, un déploiement supplémentaire doit cibler le même appareil à une priorité inférieure, même si l’objectif est de supprimer tous les modules.  

Effectuez des restaurations dans l’ordre suivant : 

1. Confirmez qu’un deuxième déploiement cible également le même ensemble d’appareils. Si l’objectif de la restauration est de supprimer tous les modules, le deuxième déploiement ne doit pas contenir de module. 
2. Modifiez ou supprimez l’expression de la condition cible du déploiement que vous souhaitez restaurer de façon à ce que les appareils ne répondent pas à la condition de ciblage.
3. Vérifiez que la restauration a réussi en consultant l’état du déploiement.
   * Le déploiement restauré ne doit plus afficher d’état pour les appareils qui ont été restaurés.
   * Le deuxième déploiement doit désormais inclure l’état de déploiement pour les appareils qui ont été restaurés.


## <a name="next-steps"></a>Étapes suivantes

* Suivez les étapes pour créer, mettre à jour ou supprimer un déploiement dans [Déployer et surveiller les modules IoT Edge à grande échelle](how-to-deploy-monitor.md).
* En savoir plus sur d’autres concepts IoT Edge tels que le [runtime IoT Edge](iot-edge-runtime.md) et les [modules IoT Edge](iot-edge-modules.md).

