---
title: Résolution des problèmes des démarrages rapides des appareils Azure RTOS intégrés
description: Étapes pour vous aider à résoudre les problèmes courants liés à l’utilisation des démarrages rapides des appareils Azure RTOS intégrés
author: JimacoMS4
ms.author: v-jbrannian
ms.service: iot-develop
ms.topic: troubleshooting
ms.date: 06/10/2021
ms.openlocfilehash: 26353064f929c382559216c28651757c7b4ee498
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112124749"
---
# <a name="troubleshooting-the-azure-rtos-embedded-device-quickstarts"></a>Résolution des problèmes des démarrages rapides des appareils Azure RTOS intégrés

En effectuant les [Démarrages rapides du développement d’appareils intégrés](quickstart-devkit-mxchip-az3166.md), vous pouvez rencontrer certains problèmes courants. En général, les problèmes se produisent dans l’une des sources suivantes :

* **Votre environnement**. Votre ordinateur, logiciel ou la connexion et la configuration réseau.
* **Vos ressources Azure IoT**. Le hub IoT et l’appareil que vous avez créés pour vous connecter à Azure IoT.
* **Votre appareil**. La carte physique et sa configuration.

Cet article fournit des suggestions de résolutions pour les problèmes les plus courants qui peuvent se produire lors des démarrages rapides.

## <a name="prerequisites"></a>Configuration requise

Toutes les étapes de résolution des problèmes requièrent que vous remplissiez les prérequis suivants pour le démarrage rapide que vous utilisez :

* Vous avez installé ou acquis tous les prérequis et les outils logiciels pour le démarrage rapide.
* Vous avez créé un hub Azure IoT ou une application Azure IoT Central, et vous avez inscrit un appareil, comme indiqué dans le guide de démarrage rapide.
* Vous avez créé une image pour l’appareil, comme indiqué dans le guide de démarrage rapide.

## <a name="issue-the-source-directory-doesnt-contain-cmakeliststxt-file"></a>Problème : le répertoire source ne contient aucun fichier CMakeLists.txt
### <a name="description"></a>Description
Ce problème peut se produire quand vous essayez de créer le projet. Cela est dû au fait que le projet a été incorrectement cloné à partir de GitHub. Le projet contient plusieurs sous-modules qui ne sont pas clonés par défaut, sauf si l’indicateur **--recursive** est utilisé.

### <a name="resolution"></a>Résolution
* Quand vous clonez le référentiel à l’aide de Git, vérifiez que l’option **--recursive** est présente.

## <a name="issue-the-build-fails"></a>Problème : échec de la génération

### <a name="description"></a>Description

Le problème peut se produire car le chemin d’accès à un fichier objet dépasse la longueur maximale du chemin par défaut dans Windows. Examinez la sortie de la génération d’un message similaire à ce qui suit :

```output
-- Configuring done
CMake Warning in C:/embedded quickstarts/areallyreallyreallylongpath/getting-started/core/lib/netxduo/addons/azure_iot/azure_iot_security_module/iot-security-module-core/CMakeLists.txt:
  The object file directory

    C:/embedded quickstarts/areallyreallyreallylongpath/getting-started/NXP/MIMXRT1060-EVK/build/lib/netxduo/addons/azure_iot/azure_iot_security_module/iot-security-module-core/CMakeFiles/asc_security_core.dir/./

  has 208 characters.  The maximum full path to an object file is 250
  characters (see CMAKE_OBJECT_PATH_MAX).  Object file

    src/serializer/extensions/custom_builder_allocator.c.obj

  cannot be safely placed under this directory.  The build may not work
  correctly.


-- Generating done
```

### <a name="resolution"></a>Résolution

Vous pouvez essayer l’une des options suivantes pour résoudre ce problème :
* Clonez le référentiel dans un répertoire dont le chemin est plus court et réessayez.
* Suivez les instructions dans [Limitation de la longueur maximale du chemin](/windows/win32/fileio/maximum-file-path-limitation) pour autoriser des chemins longs dans Windows 10, version 1607 et ultérieures.

## <a name="issue-device-cant-connect-to-iot-hub"></a>Problème : connexion impossible de l’appareil au hub IoT

### <a name="description"></a>Description

Le problème peut se produire une fois que vous avez créé des ressources Azure et flashé votre appareil. Quand vous essayez de connecter votre appareil qui vient d’être flashé sur Azure IoT, un message de console semblable au message suivant s’affiche :

```output
Unable to resolve DNS for MQTT Server
```

### <a name="resolution"></a>Résolution

* Vérifiez l’orthographe et la casse des valeurs de configuration que vous avez entrées pour votre configuration IoT dans le fichier *azure_config.h*. Les valeurs de certains attributs de ressource IoT, comme `deviceID` et `primaryKey`, respectent la casse.

## <a name="issue--wi-fi-is-unable-to-connect"></a>Problème : connexion impossible du Wi-Fi

### <a name="description"></a>Description

Une fois que vous avez flashé un appareil qui utilise une connexion Wi-Fi et avez tenté de vous connecter à votre réseau Wi-Fi, vous recevez un message d’erreur indiquant que le Wi-Fi ne peut pas se connecter.

### <a name="resolution"></a>Résolution

* Vérifiez la fréquence et les paramètres de votre réseau Wi-Fi. Les appareils utilisés dans les démarrages rapides des appareils intégrés utilisent tous 2,4 GHz. Vérifiez que votre routeur Wi-Fi est configuré pour prendre en charge un réseau 2,4 GHz.
* Vérifiez le mode Wi-Fi. Confirmez le paramètre que vous avez utilisé pour la constante WIFI_MODE dans le fichier *azure_config.h*. Vérifiez vos paramètres d’authentification ou de sécurité réseau Wi-Fi pour vérifier que le mode de sécurité Wi-Fi correspond au fichier de configuration.

## <a name="issue-flashing-the-board-fails"></a>Problème : échec pour flasher la carte

### <a name="description"></a>Description

Vous ne pouvez pas effectuer le processus pour flasher votre appareil. Vous le saurez si vous rencontrez l’un des problèmes suivants :

* Le fichier d’image * *.bin* que vous créez n’est pas copié sur l’appareil.
* L’utilitaire que vous utilisez pour flasher l’appareil émet un avertissement ou une erreur.
* L’utilitaire que vous utilisez pour flasher l’appareil n’indique pas que la programmation s’est terminée avec succès.

### <a name="resolution"></a>Résolution

* Vérifiez que vous êtes connecté au port USB correct sur l’appareil. Certains appareils ont plus d’un port.
* Essayez d’utiliser un autre câble micro USB. Certains appareils et câbles sont incompatibles.
* Essayez de vous connecter à un port USB différent sur votre ordinateur. Un port USB peut être déconnecté en interne, désactivé dans le logiciel ou être temporairement dans un état inutilisable.
* Redémarrez votre ordinateur.

## <a name="issue-device-fails-to-connect-to-port"></a>Problème : échec de la connexion de l’appareil au port

### <a name="description"></a>Description

Une fois que vous avez flashé votre appareil et que vous l’avez connecté à votre ordinateur, vous recevez un message semblable au suivant dans votre logiciel terminal :

```output
Failed to initialize the port.
Please verify the COM port settings.
```

### <a name="resolution"></a>Résolution

* Dans les paramètres de votre logiciel terminal, vérifiez le paramètre **Port** pour confirmer que le port correct est sélectionné. Si plusieurs ports sont affichés, vous pouvez ouvrir le Gestionnaire de périphériques Windows et sélectionner le nœud **Ports** pour trouver le port approprié pour votre appareil connecté.

## <a name="issue-terminal-output-shows-garbled-text"></a>Problème : la sortie du terminal affiche un texte altéré

### <a name="description"></a>Description

Une fois que vous avez correctement flashé votre appareil et que vous l’avez connecté à votre ordinateur, une sortie de texte altéré s’affiche dans votre logiciel terminal.

### <a name="resolution"></a>Résolution

* Dans les paramètres de votre logiciel terminal, confirmez que le paramètre de **vitesse en bauds** est défini sur *115 200*.

## <a name="issue-terminal-output-shows-no-text"></a>Problème : la sortie du terminal n’affiche aucun texte

### <a name="description"></a>Description

Une fois que vous avez correctement flashé votre appareil et que vous l’avez connecté à votre ordinateur, aucune sortie ne s’affiche dans votre logiciel terminal.

### <a name="resolution"></a>Résolution

* Vérifiez que les paramètres de votre logiciel terminal correspondent aux paramètres du guide de démarrage rapide.
* Redémarrez votre logiciel terminal.
* Appuyez sur le bouton de **réinitialisation** de votre appareil.
* Vérifiez que votre câble USB est correctement connecté.

## <a name="issue-communication-between-device-and-iot-hub-fails"></a>Problème : échec de la communication entre l’appareil et IoT Hub

### <a name="description"></a>Description

Une fois que vous avez flashé votre appareil et que vous l’avez connecté à votre ordinateur, vous recevez un message répété semblable au message suivant dans votre fenêtre de terminal :

```output
Failed to publish temperature
```

### <a name="resolution"></a>Résolution

* Vérifiez que *Tarification et niveau de mise à l’échelle* est défini sur *Gratuit* ou *Standard*. Le niveau **De base n’est pas pris en charge**, car il ne prend pas en charge la communication cloud-à-appareil et jumeau d’appareil.

## <a name="next-steps"></a>Étapes suivantes

Si vous ne pouvez toujours pas superviser votre appareil dans un terminal ni vous connecter à Azure IoT après avoir passé en revue les problèmes décrits dans cet article, il peut s’agir d’un problème de configuration matérielle ou physique de votre appareil. Pour obtenir la documentation et les options de support, consultez la page du fabricant de votre appareil.

* [STMicroelectronics B-L475E-IOT01](https://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mcu-mpu-eval-tools/stm32-mcu-mpu-eval-tools/stm32-discovery-kits/b-l475e-iot01a.html)
* [NXP MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK)
* [Microchip ATSAME54-XPro](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro)