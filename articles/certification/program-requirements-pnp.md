---
title: Critères de certification IoT Plug-and-Play
description: Critères du programme de certification IoT Plug-and-Play
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: IoT Plug and Play Certification Requirements
ms.service: certification
ms.openlocfilehash: 06c09527556c92cc3a1bd4bb2784084aeb373d97
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108131744"
---
# <a name="iot-plug-and-play-certification-requirements"></a>Critères de certification IoT Plug-and-Play

Ce document décrit les capacités spécifiques des appareils qui seront représentées dans le catalogue d’appareils Azure IoT. Une capacité est un attribut unique de l’appareil qui peut être une implémentation logicielle ou une combinaison d’implémentations logicielles et matérielles.

## <a name="program-purpose"></a>Objet du programme

IoT Plug-and-Play Preview permet aux créateurs de solutions d’intégrer des appareils intelligents dans leurs solutions sans configuration manuelle. Au cœur de l’IoT Plug-and-Play est un modèle d’appareil qu’un appareil utilise pour publier ses fonctionnalités dans une application IoT Plug-and-Play. Ce modèle est structuré comme un ensemble d’éléments : Télémétrie, Propriétés et Commandes.

Les promesses de la certification IoT Plug-and-Play sont les suivantes :

1.  Les modèles et interfaces d’appareil définis sont conformes au [Langage DTDL (Digital Twin Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl)  
2.  Approvisionnement sécurisé et transfert facile de la propriété d’étendue de l’ID dans les services Device Provisioning
3.  Intégration facile avec les solutions Azure IoT à l’aide des [API de jumeau numérique](../iot-pnp/concepts-digital-twin.md) : Azure IOT Hub et Azure IOT central
4.  Véracité du produit validée sur les appareils certifiés

## <a name="requirements"></a>Configuration requise

**[Obligatoire] Appareil-à-cloud : l’objectif du test est de s’assurer que les appareils qui envoient la télémétrie fonctionnent avec IoT Hub**

| **Nom**                | IoTPnP.D2C                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilité de la cible** | Déjà disponible                                                |
| **S’applique à**          | Appareil de nœud terminal / de périphérie                                      |
| **SE**                  | Sans dépendance                                                     |
| **Type de validation**     | Automatisé                                                    |
| **Validation**          | L’appareil doit envoyer tous les schémas de télémétrie à IoT Hub. Microsoft fournit le [workflow du portail](https://certify.azure.com) pour exécuter les tests. Appareil-à-cloud (obligatoire) : **1.** Valide que l’appareil peut envoyer un message à IoT Hub géré par AICS. **2.** L’utilisateur doit spécifier le nombre et la fréquence des messages. **3.** AICS valide la réception de la télémétrie par l’instance du hub |
| **Ressources**           | [Étapes de certification](./overview.md) (dispose de toutes les ressources supplémentaires) |

**[Obligatoire] DPS : l’objectif du test est de s’assurer que l’appareil implémente et prend en charge Service IoT Hub Device Provisioning avec l’une des trois méthodes d’attestation**

| **Nom**                | IoTPnP.DPS                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilité de la cible** | Déjà disponible                                                |
| **S’applique à**          | N’importe quel appareil                                                   |
| **SE**                  | Sans dépendance                                                     |
| **Type de validation**     | Automatisé                                                    |
| **Validation**          | L’appareil doit implémenter un transfert facile de la propriété d’étendue d’ID DPS sans avoir à recompiler le code incorporé. Microsoft fournit le [workflow du portail](https://certify.azure.com) pour exécuter les tests afin de valider que l’appareil prend en charge DPS : **1.** L’utilisateur doit sélectionner l’une des méthodes d’attestation (X.509, TPM et clé SAP) **2.** Selon la méthode d’attestation, l’utilisateur doit effectuer une action correspondante, telle que **a)** charger le certificat X.509 dans l’étendue gérée par AICS ; **b)** implémenter la clé SAP ou la paire de clés de type EK (Endorsement Key) dans l’appareil |
| **Ressources**           | **a)** [Vue d’ensemble du service Device Provisioning](../iot-dps/about-iot-dps.md), **b)** [Exemple de fichier de configuration pour le transfert d’étendue d’ID DPS](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview-pnp/serializer/samples/devicetwin_simplesample) |

**[Obligatoire] DTDL v2 : le but du test est de s’assurer que les modèles et interfaces d’appareil définis sont conformes au Langage DTDL (Digital Twins Definition Language) v2.**                                                              

| **Nom**                | IoTPnP.DTDL                                                  |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilité de la cible** | Déjà disponible                                                |
| **S’applique à**          | N’importe quel appareil                                                   |
| **SE**                  | Sans dépendance                                                     |
| **Type de validation**     | Automatisé                                                    |
| **Validation**          | Le [flux de travail du portail](https://certify.azure.com) valide : **1.** Annonce de l’ID de modèle et vérification que l’appareil est connecté à l’aide du protocole MQTT ou MQTT sur WebSockets **2.** Les modèles sont conformes au langage DTDL v2 **3.** La télémétrie, les propriétés et les commandes sont correctement implémentées et interagissent entre le jumeau numérique IoT Hub et le jumeau numérique sur l’appareil |
| **Ressources**           | [Mises à jour d’actualisation en préversion publique](../iot-pnp/overview-iot-plug-and-play.md) |

**[Obligatoire] Les modèles d’appareil sont publiés dans le référentiel de modèles publics**

| **Nom**                | IoTPnP.ModelRepo                                             |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilité de la cible** | Déjà disponible                                                |
| **S’applique à**          | N’importe quel appareil                                                   |
| **SE**                  | Sans dépendance                                                     |
| **Type de validation**     | Automatisé                                                    |
| **Validation**          | Tous les modèles d’appareil doivent être publiés dans un référentiel public. Les modèles d’appareil sont résolus via des modèles disponibles dans le référentiel public **1.** L’utilisateur doit publier manuellement les modèles dans le référentiel public avant de demander la certification. **2.** Notez qu’une fois les modèles publiés, ils sont immuables. Nous vous recommandons vivement de ne publier que lorsque les modèles et le code d’appareil incorporé sont finalisés.* 1 * 1 L’utilisateur doit contacter le support Microsoft pour révoquer les modèles une fois ceux-ci publiés dans le référentiel de modèles **3.** Le [flux de travail du portail](https://certify.azure.com) vérifie l’existence des modèles dans le référentiel public lorsque l’appareil est connecté au service de certification |
| **Ressources**           | [Référentiel de modèles](../iot-pnp/overview-iot-plug-and-play.md) |

**[Obligatoire] Validation de l’appareil physique à l’aide du guide de prise en main**

| **Nom**                                  | IoTPnP.Physicaldevice                                      |
| ----------------------------------------- | ------------------------------------------------------------ |
| **Disponibilité de la cible**                   | Déjà disponible                                                |
| **S’applique à**                            | N’importe quel appareil                                                   |
| **SE**                                    | Sans dépendance                                                     |
| **Type de validation**                       | Manuel                                                       |
| **Validation**                            | Les partenaires doivent contacter Microsoft contact ([iotcert@microsoft.com](mailto:iotcert@microsoft.com)) afin de prendre les dispositions nécessaires en vue des validations supplémentaires de l’appareil physique. En raison de la situation de COVID-19, nous explorons différentes façons d’effectuer une validation de l’appareil physique sans envoi de celui-ci à Microsoft. |
| **Ressources**                             | Des détails seront fournis ultérieurement                                 |
| **Recommandé par Azure**       | N/A    |

**[Si implémenté] Interface d’informations sur l’appareil : l’objectif du test est de s’assurer que l’interface d’informations sur l’appareil est correctement implémentée dans le code de celui-ci**

| **Nom**                | IoTPnP.DeviceInfoInterface                                   |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilité de la cible** | Déjà disponible                                                |
| **S’applique à**          | N’importe quel appareil                                                   |
| **SE**                  | Sans dépendance                                                     |
| **Type de validation**     | Automatisé                                                    |
| **Validation**          | Le [flux de travail du portail](https://certify.azure.com) vérifie que le code de l’appareil implémente l’interface d’informations sur l’appareil **1.** Vérifie que les valeurs sont envoyées par le code de l’appareil à IoT Hub **2.** Vérifie que l’interface est implémentée dans le DCM (cette implémentation changera en DTDL v2) **3.** Vérifie que les propriétés sont inaccessibles en écriture (lecture seule) **4.** Vérifie que le type de schéma est string et/ou long et non null |
| **Ressources**           | [Interface définie par Microsoft](../iot-pnp/overview-iot-plug-and-play.md) |
| **Recommandé par Azure**  | N/A                                                          |

**[Si implémenté] Cloud-à-appareil : l’objectif du test est de s’assurer que des messages peuvent être envoyés du cloud aux appareils**

| **Nom**                | IoTPnP.C2D                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilité de la cible** | Déjà disponible                                                |
| **S’applique à**          | Appareil de nœud terminal / de périphérie                                      |
| **SE**                  | Sans dépendance                                                     |
| **Type de validation**     | Automatisé                                                    |
| **Validation**          | L’appareil doit pouvoir accéder aux messages cloud-à-appareil d’IoT Hub. Microsoft fournit le [flux de travail du portail](https://certify.azure.com) pour exécuter ces tests. Cloud-à-appareil (si implémenté) : **1.** Vérifie que l’appareil peut recevoir un message d’IoT Hub. **2.** AICS envoie un message aléatoire et le valide via le message ACK de l’appareil |
| **Ressources**           | **1.** [étapes de certification](./overview.md) (dispose de toutes les ressources supplémentaires), **2.** [Envoyer des messages cloud-à-appareil à partir d’IoT Hub](../iot-hub/iot-hub-devguide-messages-c2d.md) |

**[Si implémenté] Méthodes directes : L’objectif du test est de s’assurer que les appareils fonctionnent avec IoT Hub et prennent en charge les méthodes directes**

| **Nom**                | IoTPnP.DirectMethods                                     |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilité de la cible** | Déjà disponible                                                |
| **S’applique à**          | Appareil de nœud terminal / de périphérie                                      |
| **SE**                  | Sans dépendance                                                     |
| **Type de validation**     | Automatisé                                                    |
| **Validation**          | L’appareil doit être en mesure de recevoir des demandes de commandes d’IoT Hub et d’y répondre. Microsoft fournit le [workflow du portail](https://certify.azure.com) pour exécuter les tests. Méthodes directes (si implémentées) : **1.** L’utilisateur doit spécifier la charge utile de la méthode directe. **2.** AICS valide la demande de charge utile spécifiée envoyée par le hub et le message ACK reçu par l’appareil |
| **Ressources**           | **1.** [étapes de certification](./overview.md) (dispose de toutes les ressources supplémentaires), **2.** [Comprendre des méthodes directes à partir d’IoT Hub](../iot-hub/iot-hub-devguide-direct-methods.md) |

**[Si implémenté] Propriété de jumeau d’appareil : L’objectif du test est de s’assurer que les appareils qui envoient la télémétrie fonctionnent avec IoT Hub et prennent en charge certaines des capacités d’IoT Hub telles que les méthodes directes et la propriété de jumeau d’appareil**

| **Nom**                | IoTPnP.DeviceTwin                                        |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilité de la cible** | Déjà disponible                                                |
| **S’applique à**          | Appareil de nœud terminal / de périphérie                                      |
| **SE**                  | Sans dépendance                                                     |
| **Type de validation**     | Automatisé                                                    |
| **Validation**          | L’appareil doit envoyer tous les schémas de télémétrie à IoT Hub. Microsoft fournit le [workflow du portail](https://certify.azure.com) pour exécuter les tests. Propriété de jumeau d’appareil (si implémentée) : **1.** AICS valide la propriété accessible en lecture/écriture dans le JSON du jumeau d’appareil. **2.** L’utilisateur doit spécifier la charge utile JSON à modifier. **3.** AICS valide les propriétés spécifiées envoyées à partir d’IoT Hub et le message ACK reçu par l’appareil |
| **Ressources**           | **1.** [étapes de certification](./overview.md) (dispose de toutes les ressources supplémentaires), **2.** [Utiliser des jumeaux d’appareil avec IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) |