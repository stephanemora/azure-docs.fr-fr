---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 04/28/2021
ms.openlocfilehash: f37ef6f968542c1f8fc1ef2f328cd2a78c024852
ms.sourcegitcommit: 43be2ce9bf6d1186795609c99b6b8f6bb4676f47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2021
ms.locfileid: "108278137"
---
Si vous développez pour des *appareils avec contraintes*, vous pouvez utiliser IoT Plug-and-Play avec :

- Le [SDK Azure pour les bibliothèques clientes IoT Embedded C](https://aka.ms/embeddedcsdk).
- [Azure RTOS](/azure/rtos/overview-rtos).

Cet article contient des liens et des ressources pour ces scénarios limités.

## <a name="prerequisites"></a>Prérequis

La plupart des exemples ci-dessous nécessitent un appareil matériel spécifique et les prérequis sont différents pour chacun de ces exemples. Suivez le lien vers l’exemple approprié pour obtenir des informations détaillées sur les prérequis, la configuration et les instructions de génération.

## <a name="use-the-sdk-for-embedded-c"></a>Utiliser le kit de développement logiciel (SDK) pour Embedded C

Le kit de développement logiciel (SDK) pour Embedded C offre une solution légère pour connecter des appareils limités aux services Azure IoT, y compris à l’aide des [conventions d’IoT Plug-and-Play](../articles/iot-pnp/concepts-convention.md). Les liens suivants incluent des exemples d’appareil basé sur une MCU, à titre éducatif et à des fins de débogage.

### <a name="use-an-mcu-based-device"></a>Utiliser un appareil basé sur une MCU

Pour obtenir un tutoriel complet de bout en bout sur l’utilisation du SDK pour Embedded C, le service Device Provisioning et IoT Plug-and-Play sur une MCU, consultez [Reprogrammer PIC-IoT WX Development Board pour se connecter à Azure via le service IoT Hub Device Provisioning](https://github.com/Azure-Samples/Microchip-PIC-IoT-Wx).

### <a name="introductory-samples"></a>Exemple d’introduction

Le référentiel du kit de développement logiciel (SDK) pour Embedded C contient [plusieurs exemples](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/samples/iot#iot-hub-plug-and-play-sample) qui vous montrent comment utiliser IoT Plug-and-Play :

> [!NOTE]
> L’exécution de ces exemples est présentée sur Windows et Linux à des fins éducatives et de débogage. Dans un scénario de production, les exemples sont destinés aux appareils limités uniquement.

- [Exemple de thermostat avec le kit de développement logiciel (SDK) pour Embedded C](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_sample.c)

- [Exemple de contrôleur de température avec le kit de développement logiciel (SDK) pour Embedded C](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_component_sample.c)

## <a name="using-azure-rtos"></a>Utilisation d’Azure RTOS

Azure RTOS inclut une couche légère qui ajoute une connectivité native aux services cloud Azure IoT. Cette couche offre un mécanisme simple pour connecter des appareils limités à Azure IoT tout en utilisant les fonctionnalités avancées d’Azure RTOS. Pour en savoir plus, consultez [Qu’est-ce que Microsoft Azure RTOS](/azure/rtos/overview-rtos).

### <a name="toolchains"></a>Chaînes d’outils

Les exemples Azure RTOS sont proposés avec différents types de combinaisons d’IDE et de chaîne d’outils, comme notamment :

- IAR : IDE [Embedded Workbench](https://www.iar.com/iar-embedded-workbench/) d’IAR
- GCC/CMake : Générer sur le système de build [CMake](https://cmake.org/) open source et la [chaîne d’outils GNU pour processeurs Arm embarqués](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm).
- MCUExpresso : [IDE MCUXpresso](https://www.nxp.com/design/software/development-software/mcuxpresso-software-and-tools-/mcuxpresso-integrated-development-environment-ide:MCUXpresso-IDE) de NXP
- STM32Cube : [IDE STM32Cube](https://www.st.com/en/development-tools/stm32cubeide.html) de STMicroelectronics
- MPLAB : [IDE MPLAB X](https://www.microchip.com/mplab/mplab-x-ide) de Microchip

### <a name="samples"></a>Exemples

Le tableau suivant liste des exemples qui vous montrent comment démarrer sur différents appareils avec Azure RTOS et IoT Plug-and-Play :

Fabricant | Appareil | Exemples |
| --- | --- | --- |
| Microchip | [ATSAME54-XPRO](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/Microchip/ATSAME54-XPRO) • [IAR](https://aka.ms/azrtos-sample/e54-iar) • [MPLAB](https://aka.ms/azrtos-sample/e54-mplab)
| MXCHIP | [AZ3166](https://aka.ms/iot-devkit) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)
| NXP | [MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/NXP/MIMXRT1060-EVK) • [IAR](https://aka.ms/azrtos-sample/rt1060-iar) • [MCUXpresso](https://aka.ms/azrtos-sample/rt1060-mcuxpresso)
| STMicroelectronics | [32F746GDISCOVERY](https://www.st.com/en/evaluation-tools/32f746gdiscovery.html) | [IAR](https://aka.ms/azrtos-sample/f746g-iar) • [STM32Cube](https://aka.ms/azrtos-sample/f746g-cubeide)
| STMicroelectronics | [B-L475E-IOT01](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)
| STMicroelectronics | [B-L4S5I-IOT01](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)
