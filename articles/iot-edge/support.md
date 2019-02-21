---
title: Moteurs de conteneur, systèmes d’exploitation pris en charge - Azure IoT Edge | Microsoft Docs
description: Découvrez quels systèmes d’exploitation peuvent exécuter le runtime et le démon d’Azure IoT Edge, ainsi que les moteurs de conteneur pris en charge pour vos appareils de production
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/17/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: d8059ac4965ce5582b899ebc0d765e00ae9deb35
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55892794"
---
# <a name="azure-iot-edge-supported-systems"></a>Systèmes pris en charge Azure IoT Edge

Il existe plusieurs façons d’obtenir un support pour le produit Azure IoT Edge.

**Signalement de bogues** : la plus grande partie du développement du produit Azure IoT Edge intervient dans le projet open source IoT Edge. Vous pouvez signaler les bogues dans la [page des problèmes](https://github.com/azure/iotedge/issues) du projet. Les correctifs apportés au projet sont rapidement intégrés aux mises à jour du produit.

**Équipe de support technique Microsoft** : les utilisateurs qui ont un [plan de support](https://azure.microsoft.com/support/plans/) peuvent solliciter l’équipe de support technique Microsoft en créant un ticket de support directement à partir du [portail Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Demandes de fonctionnalités** : le produit Azure IoT Edge effectue le suivi des demandes de fonctionnalités par le biais de sa [page User Voice](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="container-engines"></a>Moteurs de conteneur
Azure IoT Edge a besoin d’un moteur de conteneur pour lancer des modules dans la mesure où ils sont implémentés en tant que conteneurs. Microsoft fournit un moteur de conteneur, moby-engine, pour satisfaire à cette exigence. Il est basé sur le projet open source Moby. Docker CE et Docker EE sont d’autres moteurs de conteneur populaires. Ils sont également basés sur le projet open source Moby et sont compatibles avec Azure IoT Edge. Microsoft s’efforce de fournir un support pour les systèmes qui utilisent ces moteurs de conteneur, mais n’est toutefois pas en mesure de fournir des correctifs pour les problèmes qui les affectent. C’est la raison pour laquelle Microsoft recommande d’utiliser Moby Engine sur les systèmes en production.

<br>
<center>
![Moby en tant que runtime de conteneurs](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Systèmes d’exploitation
Azure IoT Edge s’exécute sur la plupart des systèmes d’exploitation qui peuvent exécuter des conteneurs ; toutefois, ces derniers ne sont pas pris en charge uniformément. Les systèmes d’exploitation sont regroupés en niveaux qui représentent le niveau de prise en charge que les utilisateurs peuvent espérer.
* Les systèmes de niveau 1 peuvent être considérés comme officiellement pris en charge. Cela signifie que Microsoft :
    * intègre ce système d’exploitation à des tests automatisés
    * fournit des packages d’installation pour ces systèmes
* Les systèmes de niveau 2 peuvent être considérés comme étant compatibles avec Azure IoT Edge et peuvent être utilisés relativement facilement. Cela signifie que :
    * Microsoft a effectué des tests ad hoc sur les plateformes ou a connaissance d’un partenaire qui exécute Azure IoT Edge correctement sur la plateforme
    * Les packages d’installation pour d’autres plateformes peuvent fonctionner sur ces plateformes
    
La famille du système d’exploitation hôte doit toujours correspondre à la famille du système d’exploitation invité utilisé dans le conteneur d’un module. En d’autres termes, vous ne pouvez utiliser que des conteneurs Linux sur Linux et des conteneurs Windows sur Windows. Lorsque vous utilisez Windows, seuls les conteneurs isolés du processus sont pris en charge, pas les conteneurs isolés hyper-v.  

<br>
<center>
![Le système d’exploitation hôte correspond au système d’exploitation invité](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>Niveau 1
Mise à la disposition générale
| Système d’exploitation | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| Raspbian-stretch | Non  | OUI|
| Ubuntu Server 16.04 | OUI | Non  |
| Ubuntu Server 18.04 | OUI | Non  |

Préversion publique
| Système d’exploitation | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| Windows 10 IoT Core build 17763 | OUI | Non  |
| Windows 10 IoT Enterprise build 17763 | OUI | Non  |
| Windows Server 2019 | OUI | Non  |

Les systèmes d’exploitation Windows ci-dessus sont les conditions requises pour les appareils qui exécutent les conteneurs Windows sur Windows. C’est la seule configuration prise en charge pour la production. Les packages d’installation Azure IoT Edge pour Windows permettent l’utilisation de conteneurs Linux sur Windows ; toutefois, ce n’est qu’à des fins de développement et de test uniquement. L’utilisation de conteneurs Linux sur Windows n’est pas une configuration prise en charge pour la production. N’importe quelle version de Windows 10 build 14393 ou version ultérieure et de Windows Server 2016 ou version ultérieure peut être utilisée pour ce scénario de développement.

### <a name="tier-2"></a>Niveau 2

| Système d’exploitation | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| CentOS 7.5 | OUI | OUI |
| Debian 8 | OUI | OUI |
| Debian 9 | OUI | OUI |
| RHEL 7.5 | OUI | OUI |
| Ubuntu 18.04 | OUI | OUI |
| Ubuntu 16.04 | OUI | OUI |
| Wind River 8 | OUI | Non  |
| Yocto | OUI | Non  |


## <a name="virtual-machines"></a>Virtual Machines
Azure IoT Edge peut être exécuté sur des machines virtuelles. Cela est courant lorsque les clients souhaitent doter l’infrastructure existante de l’intelligence edge. La famille du système d’exploitation de la machine virtuelle hôte doit correspondre à la famille du système d’exploitation invité utilisé dans le conteneur d’un module. Il s’agit de la même exigence que lorsqu’Azure IoT Edge est exécuté directement sur un appareil. Azure IoT Edge est indépendant de la technologie de virtualisation sous-jacente et fonctionne sur les machines virtuelles alimentées par des plateformes telles que Hyper-V et vSphere.

<br>
<center>
![Azure IoT Edge sur une machine virtuelle](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Configuration système minimale requise
Azure IoT Edge fonctionne parfaitement sur les appareils aussi petits qu’un Raspberry Pi3 ou du matériel de niveau serveur. Choisir le matériel adapté à votre scénario dépend des charges de travail que vous souhaitez exécuter. La décision finale peut être complexe. Toutefois, vous pouvez facilement créer le prototype d’une solution sur des ordinateurs portables ou de bureau traditionnels.

Vous exercer tout en créant le prototype vous guidera dans votre sélection finale de l’appareil. Vous devez prendre en compte les éléments suivants : le nombre de modules constituant votre charge de travail, le nombre de couches que partagent vos conteneurs de modules, dans quelles langues sont écrits vos modules, la quantité de données que vos modules traiteront, vos modules ont-il besoin de matériel spécialisé pour accélérer leurs charges de travail, quelles sont les caractéristiques de performances souhaitées de votre solution, quel est votre budget en termes de matériel ?
