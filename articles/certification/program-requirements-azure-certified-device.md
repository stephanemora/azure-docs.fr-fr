---
title: Exigences relatives à Azure Certified Device
description: Exigences du programme Azure Certified Device
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: Azure Certified Device Certification Requirements
ms.service: certification
ms.openlocfilehash: 497ffa4b3026491d6aa95df87708b3b1f2f1619e
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308283"
---
# <a name="azure-certified-device-requirements"></a>Exigences relatives à Azure Certified Device 
(anciennement IoT Hub)

Ce document décrit les capacités spécifiques des appareils qui seront représentées dans le catalogue Azure Certified Device. Une capacité est un attribut unique de l’appareil qui peut être une implémentation logicielle ou une combinaison d’implémentations logicielles et matérielles. 

## <a name="program-purpose"></a>Objet du programme

Microsoft simplifie l’IoT, et la certification Azure Certified Device est un programme de certification de base pour garantir que tous les types de périphériques sont approvisionnés dans Azure IoT Hub en toute sécurité.

Les promesses de la certification Azure Certified Device sont les suivantes :

1. L’appareil prend en charge la télémétrie qui fonctionne avec IoT Hub.
2.  L’appareil prend en charge Service IoT Hub Device Provisioning (DPS) pour un approvisionnement sécurisé vers Azure IoT Hub.
3.  L’appareil permet de saisir facilement le transfert de l’étendue de l’ID DPS cible sans obliger l’utilisateur à recompiler le code incorporé.
4.  Validation facultative d’autres éléments tels que les messages cloud-à-appareil, les méthodes directes et le jumeau d’appareil. 

## <a name="requirements"></a>Spécifications

**[Obligatoire] Appareil-à-cloud : L’objectif de test est de s’assurer que les appareils qui envoient la télémétrie fonctionnent avec IoT Hub.**

| **Nom**                | AzureCertified.D2C                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilité cible** | Déjà disponible                                                |
| **S’applique à**          | Appareil de nœud terminal / de périphérie                                      |
| **SE**                  | Sans dépendance                                                     |
| **Type de validation**     | Automatisé                                                    |
| **Validation**          | L’appareil doit envoyer tous les schémas de télémétrie à IoT Hub. Microsoft fournit le [workflow du portail](https://certify.azure.com/) pour exécuter les tests. Appareil-à-cloud (obligatoire) : **1.** Valide que l’appareil peut envoyer un message à IoT Hub géré par AICS. **2.** L’utilisateur doit spécifier le nombre et la fréquence des messages. **3.** AICS valide la réception de la télémétrie par l’instance du hub |
| **Ressources**           | [Étapes de certification](./overview.md) (dispose de toutes les ressources supplémentaires) |

**[Obligatoire] DPS : l’objectif du test est de s’assurer que l’appareil implémente et prend en charge Service IoT Hub Device Provisioning avec l’une des trois méthodes d’attestation**

| **Nom**                | AzureCertified.DPS                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilité cible** | Nouveau                                                          |
| **S’applique à**          | N’importe quel appareil                                                   |
| **SE**                  | Sans dépendance                                                     |
| **Type de validation**     | Automatisé                                                    |
| **Validation**          | L’appareil permet de saisir facilement la propriété de l’étendue de l’ID DPS cible sans avoir à recompiler le code incorporé. Microsoft fournit le [workflow du portail](https://certify.azure.com) pour exécuter les tests afin de valider que l’appareil prend en charge DPS : **1.** L’utilisateur doit sélectionner l’une des méthodes d’attestation (X.509, TPM et clé SAP) **2.** Selon la méthode d’attestation, l’utilisateur doit effectuer une action correspondante, telle que **a)** charger le certificat X.509 dans l’étendue gérée par AICS ; **b)** implémenter la clé SAP ou la paire de clés de type EK (Endorsement Key) dans l’appareil |
| **Ressources**           | [Vue d’ensemble du service Device Provisioning](../iot-dps/about-iot-dps.md) |

**[Si implémenté] Cloud-à-appareil : L’objectif du test est de s’assurer que les messages peuvent être envoyés du cloud aux appareils.**                                                              

| **Nom**                | AzureCertified.C2D                                                  |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilité cible** | Déjà disponible                                            |
| **S’applique à**          | Appareil de nœud terminal / de périphérie                                                   |
| **SE**                  | Sans dépendance                                                     |
| **Type de validation**     | Automatisé                                                    |
| **Validation**          | L’appareil doit pouvoir accéder aux messages cloud-à-appareil à partir d’IoT Hub. Microsoft fournit le [workflow du portail](https://certify.azure.com) pour exécuter ces tests. Cloud-à-appareil (si implémenté) : **1.** Valide que l’appareil peut recevoir un message d’IoT Hub. **2.** AICS envoie un message aléatoire et le valide via le message ACK de l’appareil  |
| **Ressources**           | **a)** [Étapes de certification](./overview.md) (dispose de toutes les ressources supplémentaires) ; **b)** [Envoyer des messages cloud-à-appareil à partir d’IoT Hub](../iot-hub/iot-hub-devguide-messages-c2d.md) |

**[Si implémenté] Méthodes directes : L’objectif du test est de s’assurer que les appareils fonctionnent avec IoT Hub et prennent en charge les méthodes directes.**

| **Nom**                | AzureCertified.DirectMethods                                             |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilité cible** | Déjà disponible                                            |
| **S’applique à**          | Appareil de nœud terminal / de périphérie                                                   |
| **SE**                  | Sans dépendance                                                     |
| **Type de validation**     | Automatisé                                                    |
| **Validation**          | L’appareil doit être en mesure de recevoir des demandes de commandes d’IoT Hub et d’y répondre. Microsoft fournit le [workflow du portail](https://certify.azure.com) pour exécuter les tests. Méthodes directes (si implémentées) : **1.** L’utilisateur doit spécifier la charge utile de la méthode directe. **2.** AICS valide la demande de charge utile spécifiée envoyée par le hub et le message ACK reçu par l’appareil |
| **Ressources**           | **a)** [Étapes de certification](./overview.md) (dispose de toutes les ressources supplémentaires) ; **b)** [Comprendre et appeler des méthodes directes à partir d’IoT Hub](../iot-hub/iot-hub-devguide-direct-methods.md) |

**[Si implémenté] Propriété de jumeau d’appareil : L’objectif du test est de s’assurer que les appareils qui envoient la télémétrie fonctionnent avec IoT Hub et prennent en charge certaines des capacités d’IoT Hub telles que les méthodes directes et la propriété de jumeau d’appareil.**

| **Nom**                                  | AzureCertified.DeviceTwin                                      |
| ----------------------------------------- | ------------------------------------------------------------ |
| **Disponibilité cible**                   | Déjà disponible                                            |
| **S’applique à**                            | Appareil de nœud terminal / de périphérie                                                   |
| **SE**                                    | Sans dépendance                                                     |
| **Type de validation**                       | Automatisé                                                       |
| **Validation**                            | L’appareil doit envoyer tous les schémas de télémétrie à IoT Hub. Microsoft fournit le [workflow du portail](https://certify.azure.com) pour exécuter les tests. Propriété de jumeau d’appareil (si implémentée) : **1.** AICS valide la propriété accessible en lecture/écriture dans le JSON du jumeau d’appareil. **2.** L’utilisateur doit spécifier la charge utile JSON à modifier. **3.** AICS valide les propriétés spécifiées envoyées à partir d’IoT Hub et le message ACK reçu par l’appareil |
| **Ressources**                             | **a)** [Étapes de certification](./overview.md) (dispose de toutes les ressources supplémentaires) ; **b)** [Utiliser les jumeaux d’appareil avec IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) |
