---
title: Prise en charge des plateformes par les Kits de développement logiciel (SDK) d’appareils Azure IoT | Microsoft Docs
description: Concepts - liste des plateformes prises en charge par les Kits de développement logiciel (SDK) d’appareils Azure IoT
author: yzhong94
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: yizhon
ms.openlocfilehash: cf3c80424c4626b62317bda537f9491cafc8198c
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043390"
---
# <a name="azure-iot-sdks-platform-support"></a>Prise en charge des plateformes par les Kits de développement logiciel (SDK) Azure IoT

Les [Kits de développement logiciel (SDK) Azure IoT](iot-hub-devguide-sdks.md) constituent un ensemble de bibliothèques pour interagir avec IoT Hub et le service de provisionnement des appareils avec une vaste pris en charge des langages et des plateformes.  Les Kits de développement logiciel s’exécutent sur la plupart des plateformes courantes et les développeurs peuvent porter le Kit de développement logiciel (SDK) C sur une plateforme spécifique en suivant le [guide de portage](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md). 

Microsoft prend en charge une variété de systèmes d’exploitation/plateformes/infrastructures et peut être étendu à l’aide du Kit de développement logiciel (SDK) C d’Azure IoT.  Certains sont officiellement pris en charge par l’équipe, regroupés en niveaux qui représentent le niveau de prise en charge que les utilisateurs peuvent espérer.  Les plateformes entièrement prises en charge signifient que Microsoft :
    - Crée et test de bout en bout en permanence par rapport à la ou aux versions maître et LTS prises en charge
    - Fournit un guide d’installation ou des packages le cas échéant
    - Prise en charge complète sur GitHub

En outre, une liste de partenaires a porté notre Kit de développement logiciel (SDK) C sur un plus grand nombre de plateformes et ils gèrent la couche d’abstraction de plateforme (PAL).  Le [Catalogue d’appareils certifiés Azure pour l’IoT](https://catalog.azureiotsolutions.com/) inclut également une liste de plateformes de système d’exploitation sur lesquelles les différents Kits de développement logiciel (SDK) ont été testés.  Les Kits de développement logiciel (SDK) sont également régulièrement créer sur les plateformes suivantes, avec un test et une prise en charge limités :
- MBED2
- Arduino
- Windows CE 2013 (déconseillé depuis octobre 2018)
- .NET standard 1.3 avec .NET Core 2.1 et .NET Framework 4.7
- Xamarin iOS, Android, UWP
- Android avec Java

## <a name="supported-platforms"></a>Plateformes prises en charge
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
| Windows Server 2016 | X64  | .NET Framework 4.5.1 | N/A               |

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

## <a name="partner-supported-platforms"></a>Plateformes prises en charge de partenaires
| Partenaire             | Appareils                            | Lien                     | Support |
|---------------------|------------------------------------|--------------------------|---------|
| Qualcomm            | Modem LTE IoT Qualcomm MDM9206     | [LTE Qualcomm pour le Kit de développement logiciel (SDK) IoT](https://developer.qualcomm.com/software/lte-iot-sdk) | [Forum](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| ST Microelectronics | Série STM32L4, série STM32F4      | [X-CUBE-CLOUD](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32cube-expansion-packages/x-cube-cloud.html)             | [Support](https://www.st.com/content/st_com/en/support/support-home.html) |
|                     | Série STM32F7                     | [X-CUBE-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32cube-expansion-packages/x-cube-azure.html)             |         |
|                     | Kit de découverte STM32L4 pour nœud IoT | [P-NUCLEO-AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html)          |         |
|                     |                                    | [FP-CLD-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            |         |
| Espressif           | ESP32                              | [Esp-azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  |

## <a name="next-steps"></a>Étapes suivantes
- [Kits de développement logiciel (SDK) de services et d’appareils](iot-hub-devguide-sdks.md)
- [Guide de portage](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)