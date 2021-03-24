---
title: Comprendre la conformité de Device Update pour Azure IoT Hub | Microsoft Docs
description: Découvrez comment Device Update pour Azure IoT Hub mesure la conformité des mises à jour des appareils.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ac6094efde8a32b1fcc04c55bbc537afeb4166f7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101660442"
---
# <a name="device-update-compliance"></a>Conformité de Device Update

Dans Device Update pour IoT Hub, la conformité mesure le nombre d’appareils qui ont installé la mise à jour compatible de la version la plus récente. Un appareil est conforme s’il a installé la mise à jour de la version la plus récente disponible qui est compatible pour celui-ci. 

Par exemple, considérez une instance Device Update avec les mises à jour suivantes :

|Nom de la mise à jour|Version de la mise à jour|Modèle d’appareil compatible|
|-----------|--------------|-----------------------|
|Mise à jour 1    |1.0    |Modèle1|
|Update2    |1.0    |Modèle2|
|Mise_à_jour3    |2.0    |Modèle1|

Supposons que les déploiements suivants ont été créés :

|Nom du déploiement    |Nom de la mise à jour    |Groupe ciblé|
|-----------|--------------|-------------------|
|Déploiement1    |Mise à jour 1    |Groupe1|
|Déploiement2    |Update2    |Groupe2|
|Déploiement3    |Mise_à_jour3    |Group3|

Examinons maintenant les appareils suivants, avec leurs appartenances aux groupes et les versions installées :

|deviceId   |Modèle de l'appareil   |Version de la mise à jour installée|Groupe |Conformité|
|-----------|--------------|-----------------------|-----|---------|
|Device1    |Modèle1 |1.0    |Groupe1 |Nouvelles mises à jour disponibles</span>|
|Appareil2    |Modèle1 |2.0    |Group3 |A la mise à jour la plus récente|
|Device3    |Modèle2 |1.0    |Groupe2 |A la mise à jour la plus récente|
|Appareil4    |Modèle1 |1.0    |Group3 |Mise à jour en cours|

Appareil1 et Appareil4 ne sont pas conformes, car la version 1.0 y est installée, même s’il existe une mise à jour d’une version plus récente, Mise_à_jour3, compatible pour leur modèle dans l’instance Device Update. Appareil2 et Appareil3 sont tous deux conformes, car les mises à jour des versions les plus récentes compatibles pour leurs modèles y sont installées.

La conformité ne prend pas en compte le fait qu’une mise à jour est déployée ou non sur le groupe d’un appareil : elle recherche les mises à jour publiées sur Device Update. Ainsi, dans l’exemple ci-dessus, même si Appareil1 a installé la mise à jour qui y est déployée, il est considéré comme non conforme. Appareil1 continuera d’être considéré comme non conforme jusqu’à ce qu’il installe Mise_à_jour3. L’état de conformité peut vous aider à déterminer si de nouveaux déploiements sont nécessaires. 

Comme indiqué ci-dessus, il existe trois états de conformité dans Device Update pour IoT Hub :

*   **A la dernière mise à jour** : l’appareil a installé la mise à jour de la version la plus récente publiée sur Device Update.
*   **Mise à jour en cours** : un déploiement actif est en cours pour délivrer la mise à jour de la version la plus récente compatible avec l’appareil.
*   **Nouvelles mises à jour disponibles** : un appareil n’a pas encore installé la mise à jour de la version la plus récente compatible et n’est pas dans un déploiement actif pour cette mise à jour.
