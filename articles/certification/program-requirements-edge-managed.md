---
title: Conditions requises pour la certification Edge Managed
description: Conditions requises pour la certification Edge Managed
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: Edge Managed Certification Requirements
ms.service: certification
ms.openlocfilehash: 7d3cd36dd0f03db7cc701c618b40f58dc9873659
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111982013"
---
# <a name="edge-managed-certification-requirements"></a>Conditions requises pour la certification Edge Managed

Ce document décrit les fonctionnalités spécifiques à l’appareil qui seront représentées dans le catalogue Azure Certified Device. Une fonctionnalité est un attribut d’appareil singulier qui peut décrire l’appareil. 

## <a name="program-purpose"></a>Objet du programme

La certification Edge Managed est une certification incrémentielle au-delà de la certification de ligne de base Azure Certified Device. Edge Managed est axé sur les normes de gestion des périphériques pour les appareils connectés Azure et valide la compatibilité de runtime IoT Edge pour le déploiement et la gestion des modules. (Avant, ce programme s’appelait programme de certification IoT Edge.) 

La certification Edge Managed valide la compatibilité de runtime IoT Edge pour le déploiement et la gestion des modules. Ce programme assure la confiance dans la gestion des appareils IoT connectés Azure.

## <a name="requirements"></a>Spécifications

La certification Edge Managed requiert que toutes les exigences du [programme de ligne de base Azure Certified Device](.\program-requirements-azure-certified-device.md) soient respectées.

**DPS : L’objectif du test est de vérifier que l’appareil implémente et prend en charge Service IoT Hub Device Provisioning avec l’une des trois méthodes d’attestation.**

| **Nom**                | AzureReady.DPS                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilité de la cible** | Déjà disponible                                                |
| **S’applique à**          | N’importe quel appareil                                                   |
| **SE**                  | Sans dépendance                                                     |
| **Type de validation**     | Automatisé                                                    |
| **Validation**          | AICS valident le DPS pour la prise en charge du code de l’appareil. **1.** L’utilisateur doit sélectionner l’une des méthodes d’attestation (X.509, TPM et clé SAP). **2.** Selon la méthode d’attestation, l’utilisateur doit effectuer une action correspondante, telle que **a)** charger le certificat X.509 dans l’étendue gérée par AICS ; **b)** implémenter la clé SAP ou la paire de clés de type EK (Endorsement Key) dans l’appareil. **3.** Ensuite, l’utilisateur clique sur le bouton « Se connecter » pour se connecter à AICS managé IoT Hub via DPS                                                    |
| **Ressources**           |                                                      |
| **Recommandé par Azure :**     | N/A                                                    |

## <a name="iot-edge"></a>IoT Edge

**Le runtime Edge existe : L’objectif du test est de s’assurer que l’appareil contient un runtime IoT Edge ($edgehub et $edgeagent) qui fonctionne correctement.**

| **Nom**                | EdgeManaged.EdgeRT                                           |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilité de la cible** | Déjà disponible                                                |
| **S’applique à**          | Appareil IoT Edge                                              |
| **SE**                  | [Système d’exploitation de niveau1 et niveau2](../iot-edge/support.md)                 |
| **Type de validation**     | Automatisé                                                    |
| **Validation**          | AICS valide la capacité de déploiement du runtime IoT Edge installé. **1.** L’utilisateur doit spécifier un système d’exploitation spécifique (le système d’exploitation ne se trouvant pas dans la liste de niveau 1/2 n’est pas accepté) **2.** AICS génère son fichier config.yaml et déploie le [module de périphérie de capteur Temp simulé](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/azure-iot.simulated-temperature-sensor?tab=Overview) canonique **3.** AICS confirme que le sous-système de conteneur compatible avec l’ancrage (Moby) est installé sur l’appareil **4.** Le résultat de test est déterminé en fonction de la réussite du déploiement du module de périphérie de capteur temporaire simulé et de la fonctionnalité du sous-système de conteneur compatible avec l’ancrage                                                    |
| **Ressources**           | **a)** [Blog AICS](https://azure.microsoft.com/en-in/blog/expanding-azure-iot-certification-service-to-support-azure-iot-edge-device-certification/), **b)** [Étapes de certification](./overview.md) (a toutes les ressources supplémentaires), **c)** [Exigences](./program-requirements-azure-certified-device.md) |
| **Recommandé par Azure :**     | N/A                                                    |

### <a name="capability-template"></a>Modèle de capacité :

**Configuration IoT Edge facile : L’objectif du test est de s’assurer que l’appareil IoT Edge est facile à configurer et confirme que le runtime IoT Edge est préinstallé lors de la validation de l’appareil physique**

| **Nom**                | EdgeManaged.PhysicalDevice                                             |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilité de la cible** | Disponible maintenant (actuellement en attente en raison de la COVID-19)                                            |
| **S’applique à**          | Appareil IoT Edge                                                   |
| **SE**                  | [Système d’exploitation de niveau1 et niveau2](../iot-edge/support.md)                                                     |
| **Type de validation**     | Manuel/labo vérifié                                                    |
| **Validation**          | OEM doit expédier l’appareil physique à l’administration IoT (HCL). HCL effectue une validation manuelle sur l’appareil physique à vérifier : **1.** EdgeRT utilise le sous-système Moby (version de redistribution autorisée). Pas docker **2.** Choisissez le dernier module Edge pour valider la possibilité de déployer Edge.                                                     |
| **Ressources**           | **a)** [Blog AICS](https://azure.microsoft.com/en-in/blog/expanding-azure-iot-certification-service-to-support-azure-iot-edge-device-certification/), **b)** [Étapes de certification](./overview.md), **c)** [Exigences](./program-requirements-azure-certified-device.md) |
| **Recommandé par Azure :**     | N/A                                                    |