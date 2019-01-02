---
title: Moteurs de conteneur, systèmes d’exploitation pris en charge - Azure IoT Edge | Microsoft Docs
description: Découvrez quels systèmes d’exploitation peuvent exécuter le runtime et le démon d’Azure IoT Edge, ainsi que les moteurs de conteneur pris en charge pour vos appareils de production
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/07/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 3c75f50e40f83a4dc6765ae422fbb2458f40d770
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53100177"
---
# <a name="azure-iot-edge-supported-systems"></a>Systèmes pris en charge Azure IoT Edge

Il existe plusieurs façons d’obtenir un support pour le produit Azure IoT Edge.

**Signalement de bogues** : la plus grande partie du développement du produit Azure IoT Edge intervient dans le projet open source IoT Edge. Vous pouvez signaler les bogues dans la [page des problèmes](https://github.com/azure/iotedge/issues) du projet. Les correctifs apportés au projet sont rapidement intégrés aux mises à jour du produit.

**Équipe de support technique Microsoft** : les utilisateurs qui ont un [plan de support](https://azure.microsoft.com/support/plans/) peuvent solliciter l’équipe de support technique Microsoft en créant un ticket de support directement à partir du [portail Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Demandes de fonctionnalités** : le produit Azure IoT Edge effectue le suivi des demandes de fonctionnalités par le biais de sa [page User Voice](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="operating-systems"></a>Systèmes d’exploitation
Azure IoT Edge s’exécute sur la plupart des systèmes d’exploitation qui peuvent exécuter des conteneurs ; toutefois, ces derniers ne sont pas pris en charge uniformément. Les systèmes d’exploitation sont regroupés en niveaux qui représentent le niveau de prise en charge que les utilisateurs peuvent espérer.

### <a name="tier-1"></a>Niveau 1
Les systèmes de niveau 1 peuvent être considérés comme officiellement pris en charge. Cela signifie que Microsoft :
* intègre ces systèmes d’exploitation à des tests automatisés
* fournit des packages d’installation pour ces systèmes

Mise à la disposition générale
| Système d’exploitation | AMD64 | ARM32 |
| ---------------- | ----- | ----- |
| Raspbian-stretch | Non  | Oui|
| Ubuntu Server 16.04 | Oui | Non  |
| Ubuntu Server 18.04 | Oui | Non  |

Version préliminaire publique
| Système d’exploitation | AMD64 | ARM32 |
| ---------------- | ----- | ----- |
| Windows 10 IoT Standard (mise à jour d’avril 2018) | Oui | Non  |
| Windows 10 IoT Entreprise (mise à jour d’avril 2018) | Oui | Non  |
| Windows 10 Server 1803 | Oui | Non  |

### <a name="tier-2"></a>Niveau 2
Les systèmes de niveau 2 peuvent être considérés comme étant compatibles avec Azure IoT Edge et peuvent être utilisés relativement facilement. Cela signifie que :
* Microsoft a effectué des tests ad hoc sur les plateformes ou a connaissance d’un partenaire qui exécute Azure IoT Edge correctement sur la plateforme
* Les packages d’installation pour d’autres plateformes peuvent fonctionner sur ces plateformes

| Système d’exploitation | AMD64 | ARM32 |
| ---------------- | ----- | ----- |
| CentOS 7.5 | Oui | Oui |
| Debian 8 | Oui | Oui |
| Debian 9 | Oui | Oui |
| RHEL 7.5 | Oui | Oui |
| Ubuntu 18.04 | Oui | Oui |
| Ubuntu 16.04 | Oui | Oui |
| Wind River 8 | Oui | Non  |
| Yocto | Oui | Non  |

## <a name="container-engines"></a>Moteurs de conteneur
Azure IoT Edge a besoin d’un moteur de conteneur pour lancer les modules, quel que soit le système d’exploitation sur lequel il est en cours d’exécution. Microsoft fournit un moteur de conteneur, moby-engine, pour satisfaire à cette exigence. Il est basé sur le projet open source Moby. Docker CE et Docker EE sont d’autres moteurs de conteneur populaires. Ils sont également basés sur le projet open source Moby et sont compatibles avec Azure IoT Edge. Microsoft s’efforce de fournir un support pour les systèmes qui utilisent ces moteurs de conteneur, mais n’est toutefois pas en mesure de fournir des correctifs pour les problèmes qui les affectent. C’est la raison pour laquelle Microsoft recommande d’utiliser Moby Engine sur les systèmes en production.

