---
title: Prise en charge des plateformes par les Kits de développement logiciel (SDK) d’appareils Azure IoT | Microsoft Docs
description: Concepts - liste des plateformes prises en charge par les Kits de développement logiciel (SDK) d’appareils Azure IoT
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: yizhon
ms.openlocfilehash: 7bcc1bf6b734abe202c5fec5d515604f4bf8e4a7
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669361"
---
# <a name="azure-iot-sdks-platform-support"></a>Prise en charge des plateformes par les Kits de développement logiciel (SDK) Azure IoT

Les [Kits de développement logiciel (SDK) Azure IoT](iot-hub-devguide-sdks.md) constituent un ensemble de bibliothèques pour interagir avec IoT Hub et le service de provisionnement des appareils avec une vaste prise en charge des langages et des plateformes. Les Kits de développement logiciel s’exécutent sur la plupart des plateformes courantes et les développeurs peuvent porter le Kit de développement logiciel (SDK) C sur une plateforme spécifique en suivant le [guide de portage](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md). 

Microsoft prend en charge une variété de systèmes d’exploitation/plateformes/infrastructures et peut être étendu à l’aide du Kit de développement logiciel (SDK) C d’Azure IoT. Certains sont officiellement pris en charge par l’équipe, regroupés en niveaux qui représentent le niveau de prise en charge que les utilisateurs peuvent espérer. Les *plateformes entièrement prises en charge* signifient que Microsoft :

- Génère et teste de bout en bout en permanence ses produits par rapport aux versions maître et LTS prises en charge.  Pour que les tests couvrent différentes versions, nous les effectuons généralement sur la version LTS la plus récente et la version la plus populaire.  Les autres versions de la même plateforme peuvent être prises en charge en fonction de la compatibilité des versions de plateforme.
- Fournit des packages ou un guide d’installation, le cas échéant.
- Prend entièrement en charge les plateformes sur GitHub.

En outre, une liste de partenaires a porté notre Kit de développement logiciel (SDK) C sur un plus grand nombre de plateformes et ils gèrent la couche d’abstraction de plateforme (PAL). Le [Catalogue d’appareils certifiés Azure pour l’IoT](https://catalog.azureiotsolutions.com/) inclut également une liste de plateformes de système d’exploitation sur lesquelles les différents Kits de développement logiciel (SDK) ont été testés. Les kits SDK s’appuient régulièrement sur les plateformes suivantes, avec un test et une prise en charge limités :

* MBED2
* Arduino
* Windows CE 2013 (déconseillé depuis octobre 2018)
* .NET standard 1.3 avec .NET Core 2.1 et .NET Framework 4.7
* Xamarin iOS, Android, UWP

## <a name="supported-platforms"></a>Plateformes prises en charge

Il existe plusieurs plateformes prises en charge.

### <a name="c-sdk"></a>Kit de développement logiciel (SDK) C

| SE                  | Arch | Compilateur             | Bibliothèque TLS       |
|---------------------|------|----------------------|-------------------|
| Ubuntu 16.04 LTS    | X64  | gcc-5.4.0            | openssl  - 1.0.2g |
| Ubuntu 18.04 LTS    | X64  | gcc-7.3              | WolfSSL – 1.13    |
| Ubuntu 18.04 LTS    | X64  | Clang 6.0.X          | Openssl – 1.1.0g  |
| OSX 10.13.4         | x64  | XCode 9.4.1          | OSX natif        |
| Windows Server 2016 | x64  | Visual Studio 14.0.X | SChannel          |
| Windows Server 2016 | x86  | Visual Studio 14.0.X | SChannel          |
| Debian 9 Stretch    | x64  | gcc-7.3              | Openssl – 1.1.0f  |

### <a name="python-sdk"></a>Kit de développement logiciel (SDK) Python

| SE                  | Arch | Compilateur   | Bibliothèque TLS |
|---------------------|------|------------|-------------|
| Windows Server 2016 | x86  | Python 2.7 | openssl     |
| Windows Server 2016 | x64  | Python 2.7 | openssl     |
| Windows Server 2016 | x86  | Python 3.5 | openssl     |
| Windows Server 2016 | x64  | Python 3.5 | openssl     |
| Ubuntu 18.04 LTS    | x86  | Python 2.7 | openssl     |
| Ubuntu 18.04 LTS    | x86  | Python 3.4 | openssl     |
| MacOS High Sierra   | x64  | Python 2.7 | openssl     |

### <a name="net-sdk"></a>Kit de développement logiciel (SDK) .NET

| SE                  | Arch | Framework            | standard          |
|---------------------|------|----------------------|-------------------|
| Ubuntu 16.04 LTS    | X64  | .NET Core 2.1        | .NET standard 2.0 |
| Windows Server 2016 | X64  | .NET Core 2.1        | .NET standard 2.0 |
| Windows Server 2016 | X64  | .NET Framework 4.7   | .NET standard 2.0 |
| Windows Server 2016 | X64  | .NET Framework 4.5.1 | S.O.               |

### <a name="nodejs-sdk"></a>Kit de développement logiciel (SDK) Node.js

| SE                                           | Arch | Version de nœud |
|----------------------------------------------|------|--------------|
| Ubuntu 16.04 LTS (avec une image Docker de nœud 6) | X64  | Nœud 6       |
| Windows Server 2016                          | X64  | Nœud 6       |

### <a name="java-sdk"></a>Kit de développement logiciel (SDK) Java

| SE                  | Arch | Version de Java |
|---------------------|------|--------------|
| Ubuntu 16.04 LTS    | X64  | Java 8       |
| Windows Server 2016 | X64  | Java 8       |
| API Android 28 | X64  | Java 8       |
| Choses Android | X64  | Java 8      |

## <a name="partner-supported-platforms"></a>Plateformes prises en charge de partenaires

Les clients peuvent étendre la prise en charge de la plateforme en portant le SDK Azure IoT C pour créer spécialement la couche d’abstraction de plateforme (PAL) du SDK. Microsoft travaille avec les partenaires pour fournir la prise en charge étendue. Une liste de partenaires a porté le SDK C vers plus de plateformes et gèrent la couche PAL.

| Partenaire             | Appareils                            | Lien                     | Support |
|---------------------|------------------------------------|--------------------------|---------|
| Espressif           | ESP32 <br/> ESP8266                              | [Esp-azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  
| Qualcomm            | Modem LTE IoT Qualcomm MDM9206     | [LTE Qualcomm pour le Kit de développement logiciel (SDK) IoT](https://developer.qualcomm.com/software/lte-iot-sdk) | [Forum](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| ST Microelectronics | Série STM32L4 <br/> Série STM32F4 <br/>  Série STM32F7 <br/>  Kit de découverte STM32L4 pour nœud IoT    | [X-CUBE-CLOUD](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32cube-expansion-packages/x-cube-cloud.html) <br/> [X-CUBE-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32cube-expansion-packages/x-cube-azure.html) <br/> [P-NUCLEO-AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [FP-CLD-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [Support](https://www.st.com/content/st_com/en/support/support-home.html)
| Texas Instruments   | CC3220SF Launchpad <br/> CC3220S Launchpad <br/> MSP432E4 Launchpad      | [Plug-in Azure IoT pour SimpleLink](https://github.com/TexasInstruments/azure-iot-pal-simplelink) | [Forum TI E2E](https://e2e.ti.com) <br/> [Forum TI E2E pour CC3220](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [Forum TI E2E pour MSP432E4](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="next-steps"></a>Étapes suivantes

* [Kits de développement logiciel (SDK) de services et d’appareils](iot-hub-devguide-sdks.md)
* [Guide de portage](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
