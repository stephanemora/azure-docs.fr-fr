---
title: Moteurs de conteneur, systèmes d’exploitation pris en charge - Azure IoT Edge
description: Découvrez quels systèmes d’exploitation peuvent exécuter le runtime et le démon d’Azure IoT Edge, ainsi que les moteurs de conteneur pris en charge pour vos appareils de production
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/09/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b17f1f32a3e49e9161afe92d62b85a162affcd9f
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98630528"
---
# <a name="azure-iot-edge-supported-systems"></a>Systèmes pris en charge Azure IoT Edge

Cet article fournit des détails sur les systèmes et les composants pris en charge par IoT Edge, qu’ils soient officiels ou en préversion.

Si vous rencontrez des problèmes lors de l’utilisation du service Azure IoT Edge, plusieurs méthodes de recherche de support sont possibles. Essayez l’un des canaux suivants pour obtenir un support :

**Signalement de bogues** : la plus grande partie du développement du produit Azure IoT Edge intervient dans le projet open source IoT Edge. Vous pouvez signaler les bogues dans la [page des problèmes](https://github.com/azure/iotedge/issues) du projet. Les correctifs apportés au projet sont rapidement intégrés aux mises à jour du produit.

**Équipe de support technique Microsoft** : les utilisateurs qui ont un [plan de support](https://azure.microsoft.com/support/plans/) peuvent solliciter l’équipe de support technique Microsoft en créant un ticket de support directement à partir du [portail Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Demandes de fonctionnalités** : le produit Azure IoT Edge effectue le suivi des demandes de fonctionnalités par le biais de sa [page User Voice](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="container-engines"></a>Moteurs de conteneur

Les modules Azure IoT Edge sont implémentés en tant que conteneurs, IoT Edge a ainsi besoin d’un moteur de conteneur pour les lancer. Microsoft fournit un moteur de conteneur, moby-engine, pour satisfaire à cette exigence. Ce moteur de conteneur est basé sur le projet open source Moby. Docker CE et Docker EE sont d’autres moteurs de conteneur populaires. Ils sont également basés sur le projet open source Moby et sont compatibles avec Azure IoT Edge. Microsoft s’efforce de fournir un support pour les systèmes qui utilisent ces moteurs de conteneur, mais ne peut toutefois pas fournir des correctifs pour les problèmes qui les affectent. C’est la raison pour laquelle Microsoft recommande d’utiliser Moby Engine sur les systèmes en production.

<br>
<center>

![Moteur Moby en tant que runtime de conteneurs](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Systèmes d’exploitation

Azure IoT Edge s’exécute sur la plupart des systèmes d’exploitation capables d'exécuter des conteneurs. Cela étant, tous ces systèmes ne sont pas pris en charge de la même façon. Les systèmes d’exploitation sont regroupés en niveaux qui représentent le niveau de prise en charge que les utilisateurs peuvent espérer.

* Les systèmes de niveau 1 sont pris en charge. Pour les systèmes de niveau 1, Microsoft :
  * intègre ce système d’exploitation à des tests automatisés
  * fournit des packages d’installation pour ces systèmes
* Les systèmes de niveau 2 sont compatibles avec Azure IoT Edge et peuvent être utilisés relativement facilement. Pour les systèmes de niveau 2 :
  * Microsoft a réalisé des tests informels sur les plateformes ou a connaissance d’un partenaire qui exécute Azure IoT Edge correctement sur la plateforme
  * Les packages d’installation pour d’autres plateformes peuvent fonctionner sur ces plateformes

La famille du système d’exploitation hôte doit toujours correspondre à la famille du système d’exploitation invité utilisé dans le conteneur d’un module. En d’autres termes, vous ne pouvez utiliser que des conteneurs Linux sur Linux et des conteneurs Windows sur Windows. Lorsque vous utilisez Windows, seuls les conteneurs isolés du processus sont pris en charge, pas les conteneurs isolés Hyper-V.  

IoT Edge pour Linux sur Windows utilise IoT Edge sur une machine virtuelle Linux qui s’exécute sur un hôte Windows. Il est ainsi possible d’exécuter des modules Linux sur un appareil Windows.

### <a name="tier-1"></a>Niveau 1

Les systèmes qui figurent dans les tableaux ci-dessous sont pris en charge par Microsoft, soit en disponibilité générale soit en préversion publique, et sont testés avec chaque nouvelle version.

Azure IoT Edge prend en charge les modules créés en tant que conteneurs Linux ou Windows. Les conteneurs Linux peuvent être déployés sur des appareils Linux ou bien sur des appareils Windows avec IoT Edge pour Linux sur Windows. Les conteneurs Windows ne peuvent être déployés que sur des appareils Windows.

#### <a name="linux-containers"></a>Conteneurs Linux

Les modules créés en tant que conteneurs Linux peuvent être déployés sur des appareils Linux ou Windows. Pour les appareils Linux, le runtime IoT Edge est installé directement sur l’appareil hôte. Pour les appareils Windows, une machine virtuelle Linux précréée avec le runtime IoT Edge s’exécute sur l’appareil hôte.

Même si IoT Edge pour Linux sur Windows est actuellement en préversion publique, il s’agit de la méthode recommandée pour exécuter IoT Edge sur des appareils Windows.

| Système d’exploitation | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspberry Pi OS Stretch |  | ![Raspberry Pi OS Stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| Ubuntu Server 16.04 | ![Ubuntu Server 16.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Préversion publique  |
| Ubuntu Server 18.04 | ![Ubuntu Server 18.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Préversion publique |
| Windows 10 Pro | Préversion publique |  |  |
| Windows 10 Entreprise | Préversion publique |  |  |
| Windows 10 IoT Entreprise | Préversion publique |  |  |
| Windows Server 2019 | Préversion publique |  |  |

Tous les systèmes d’exploitation Windows doivent être de la version 1809 (build 17763) ou d’une version ultérieure.

#### <a name="windows-containers"></a>Conteneurs Windows

Les modules générés en tant que conteneurs Windows ne peuvent être déployés que sur des appareils Windows.

| Système d’exploitation | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Windows 10 IoT Entreprise | ![check1](./media/tutorial-c-module/green-check.png) |  |  |
| Windows 10 IoT Core<sup>1</sup><br> | ![check1](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server 2019  | ![check1](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server IoT 2019<br> | ![check1](./media/tutorial-c-module/green-check.png) |  |  |

<sup>1</sup> Windows 10 IoT Core ne sera pas pris en charge après la version 1.0.10.

Tous les systèmes d’exploitation Windows doivent être de la version 1809 (build 17763). La build spécifique de Windows est requise pour IoT Edge sur Windows, car la version des conteneurs Windows doit correspondre exactement à celle de l’appareil Windows hôte. Les conteneurs Windows n’utilisent actuellement que la version 17763.

### <a name="tier-2"></a>Niveau 2

Les systèmes listés dans le tableau ci-dessous sont considérés comme compatibles avec Azure IoT Edge, mais ne sont pas activement testés ni gérés par Microsoft.

| Système d’exploitation | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 8](https://www.debian.org/releases/jessie/) | ![Debian 8 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Système d’exploitation Mentor Embedded Linux Flex](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Système d’exploitation Mentor Embedded Linux Flex + AMD64](./media/tutorial-c-module/green-check.png) | ![Système d’exploitation Mentor Embedded Linux Flex + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Système d’exploitation Mentor Embedded Linux Flex + ARM64](./media/tutorial-c-module/green-check.png) |
| [Système d’exploitation Mentor Embedded Linux Omni](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Système d’exploitation Mentor Embedded Linux Omni + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Système d’exploitation Mentor Embedded Linux Omni + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7.5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu 16.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Wind River 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspberry Pi OS Buster |  | ![Raspberry Pi OS Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspberry Pi OS Buster + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 20.04 <sup>1</sup>](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes) | ![Ubuntu 20.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20.04 + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> Les packages Debian 9 du [référentiel de versions d’Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases) fonctionnent directement avec Ubuntu 20.04.

## <a name="releases"></a>Publications

Les ressources de mise en production et les notes de publication d’IoT Edge sont disponibles sur la page [Azure-iotedge](https://github.com/Azure/azure-iotedge/releases). Cette section reflète les informations de ces notes de publication pour vous aider à voir plus facilement quels sont les composants de chaque version.

Les composants IoT Edge peuvent être installés ou mis à jour individuellement et sont rétrocompatibles avec les composants des versions antérieures. Le tableau suivant répertorie les composants inclus dans chaque version :

| Libérer | Démon de sécurité | Hub Edge<br>Agent Edge | Libiothsm | Moby |
|--|--|--|--|--|
| **1.0.10** | 1.0.10 | 1.0.10 | 1.0.10 |  |
| **1.0.9** | 1.0.9.5<br>1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 | 1.0.9.5<br>1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 | 1.0.9.5<br>1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 |  |
| **1.0.8** | 1.0.8 | 1.0.8.5<br>1.0.8.4<br>1.0.8.3<br>1.0.8.2<br>1.0.8.1<br>1.0.8 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7 | 3.0.5<br>3.0.4 (ARMv7hl, CentOS) |
| **1.0.6** | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6 |  |
| **1.0.5** | 1.0.5 | 1.0.5 | 1.0.5 | 3.0.2 |

IoT Edge utilise le kit SDK Microsoft.Azure.Devices.Client. Pour plus d’informations, consultez le [référentiel C# Azure IOT SDK GitHub](https://github.com/Azure/azure-iot-sdk-csharp) ou le [contenu de référence du kit de développement logiciel (SDK) Azure pour .NET](/dotnet/api/overview/azure/iot/client). La liste suivante indique la version du kit de développement logiciel (SDK) client pour laquelle chaque version est testée :

* **IoT Edge 1.0.10** : Kit de développement logiciel (SDK) client 1.28.0
* **IoT Edge 1.0.9** : Kit de développement logiciel (SDK) client 1.21.1
* **IoT Edge 1.0.8** : Kit de développement logiciel (SDK) client 1.20.3
* **IoT Edge 1.0.7** : Kit de développement logiciel (SDK) client 1.20.1
* **IoT Edge 1.0.6** : Kit de développement logiciel (SDK) client 1.17.1
* **IoT Edge 1.0.5** : Kit de développement logiciel (SDK) client 1.17.1

## <a name="virtual-machines"></a>Virtual Machines

Azure IoT Edge peut être exécuté sur des machines virtuelles. Il est courant d’utiliser une machine virtuelle comme appareil IoT Edge quand les clients souhaitent doter l’infrastructure existante de l’intelligence Edge. La famille du système d’exploitation de la machine virtuelle hôte doit correspondre à la famille du système d’exploitation invité utilisé dans le conteneur d’un module. Cette exigence est la même que lorsqu’Azure IoT Edge est exécuté directement sur un appareil. Azure IoT Edge est indépendant de la technologie de virtualisation sous-jacente et fonctionne sur les machines virtuelles alimentées par des plateformes telles que Hyper-V et vSphere.

<br>
<center>

![Azure IoT Edge sur une machine virtuelle](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Configuration système minimale requise

Azure IoT Edge fonctionne parfaitement sur les appareils aussi petits qu’un Raspberry Pi3 ou du matériel de niveau serveur. Choisissez le matériel adapté à votre scénario en fonction des charges de travail que vous planifiez d’exécuter. La décision finale peut être complexe. Toutefois, vous pouvez facilement créer le prototype d’une solution sur des ordinateurs portables ou de bureau traditionnels.

Vous exercer tout en créant le prototype vous guidera dans votre sélection finale de l’appareil. Voici quelques questions à vous poser :

* Combien de modules compte votre charge de travail ?
* Combien de couches partagent vos conteneurs de modules ?
* Dans quel langage vos modules sont écrits ?
* Quelle quantité de données vos modules auront à traiter ?
* Vos modules ont-ils besoin de matériel spécialisé pour accélérer leurs charges de travail ?
* Quelles sont les caractéristiques de performances souhaitées de votre solution ?
* Quel est votre budget pour le matériel ?
