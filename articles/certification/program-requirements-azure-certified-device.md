---
title: Exigences de certification Azure Certified Device
description: Exigences de certification Azure Certified Device
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: Azure Certified Device Certification Requirements
ms.service: certification
ms.openlocfilehash: 8e68cf180927e000c93c4c2d73d25d4ae6ccf0f8
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2021
ms.locfileid: "111538143"
---
# <a name="azure-certified-device-certification-requirements"></a>Exigences de certification Azure Certified Device 
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
| **Validation**          | L’appareil prend en charge la saisie facile de la propriété d’étendue de l’ID DPS cible. Microsoft fournit le [workflow du portail](https://certify.azure.com) pour exécuter les tests afin de valider que l’appareil prend en charge DPS : **1.** L’utilisateur doit sélectionner l’une des méthodes d’attestation (X.509, TPM et clé SAP) **2.** Selon la méthode d’attestation, l’utilisateur doit effectuer une action correspondante, telle que **a)** charger le certificat X.509 dans l’étendue gérée par AICS ; **b)** implémenter la clé SAP ou la paire de clés de type EK (Endorsement Key) dans l’appareil |
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

**[Obligatoire] Limiter la recompilation : L’objectif de cette stratégie est d’éviter aux utilisateurs de recompiler par défaut le code pour déployer l’appareil.**

| **Nom**                                  | AzureCertified.Policy.LimitRecompile                                      |
| ----------------------------------------- | ------------------------------------------------------------ |
| **Disponibilité de la cible**                   | Policy                                            |
| **S’applique à**                            | N’importe quel appareil                                                   |
| **SE**                                    | Sans dépendance                                                     |
| **Type de validation**                       | Policy                                                       |
| **Validation**                            | Pour simplifier la configuration des appareils pour les utilisateurs, tous les appareils doivent être configurés pour se connecter à Azure sans nécessiter la recompilation et le déploiement du code source de l’appareil. Vous devez ainsi définir les informations DPS, comme l’ID d’étendue, en tant que paramètres de configuration sans les compiler. Toutefois, si votre appareil contient un composant matériel sécurisé ou si l’utilisateur s’attend à compiler et à déployer du code en raison de circonstances atténuantes, contactez l’équipe de certification pour demander une exception. |
| **Ressources**                             | **a)** [Vue d’ensemble du service de provisionnement des appareils](../iot-dps/about-iot-dps.md)  **b)** [Exemple de fichier config pour le transfert d’étendue d’ID DPS](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview-pnp/serializer/samples/devicetwin_simplesample) |
