---
title: Générer le micro-agent Defender à partir du code source (préversion)
description: Le micro-agent comprend une infrastructure qui peut être utilisée pour personnaliser votre distribution.
ms.date: 1/18/2021
ms.topic: quickstart
ms.openlocfilehash: aac9bf224064dd8393acfeb2928f5ed2d1f84381
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781107"
---
# <a name="build-the-defender-micro-agent-from-source-code-preview"></a>Générer le micro-agent Defender à partir du code source (préversion)

Le micro-agent comprend une infrastructure qui peut être utilisée pour personnaliser votre distribution. Pour voir la liste des paramètres de configuration disponibles, consultez le fichier `configs/LINUX_BASE.conf`.

Pour une seule distribution, modifiez le fichier `.conf` de base. 

Si vous avez besoin de plusieurs distributions, vous pouvez hériter de la configuration de base et remplacer ses valeurs. 

Pour remplacer les valeurs :

1. Créez un fichier `.dist`.

1. Ajoutez `CONF_DEFINE_BASE(${g_plat_config_path} LINUX_BASE.conf)` en haut.
 
1. Définissez les nouvelles valeurs en fonction de vos besoins, par exemple : 

    `set(ASC_LOW_PRIORITY_INTERVAL 60*60*24)` 

1. Donnez au fichier `.dist` une référence lors de la génération. Par exemple, 

    `cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGET=UBUNTU1804 ..` 

## <a name="baseline-configuration-signing"></a>Signature de configuration de référence 

Par défaut, l’agent vérifie l’authenticité des fichiers de configuration qui sont placés sur le disque afin de limiter la falsification.

Vous pouvez arrêter ce processus en définissant l’indicateur de préprocesseur `ASC_BASELINE_CONF_SIGN_CHECK_DISABLE`.

Nous vous déconseillons de désactiver la vérification de la signature pour les environnements de production. 

Si vous avez besoin d’une configuration différente pour des scénarios de production, contactez l’équipe Defender pour IoT. 

## <a name="prerequisites"></a>Prérequis 

1. Contactez votre responsable de compte pour demander l’accès au code source Defender pour IoT.
 
1. Clonez ou extrayez le code source dans un dossier sur le disque.

1. Accédez à ce répertoire.

1. Extrayez les sous-modules à l’aide du code suivant :

    ```bash
    git submodule update --init
    ```
    
1. Ensuite, extrayez les sous-modules pour le SDK Azure IoT avec le code suivant : 

    ```bash
    git -C deps/azure-iot-sdk-c/ submodule update –init
    ```
 

1. Ajoutez une autorisation d’exécution, puis exécutez le script d’installation de l’environnement de développement :

    ```bash
    chmod +x scripts/install_development_environment.sh && ./scripts/install_development_environment.sh 
    ```

1. Créez un répertoire pour les sorties de build : 

    ```bash
    mkdir cmake 
    ```

1. (Facultatif) Téléchargez et installez [VSCode](https://code.visualstudio.com/download ). 

1. (Facultatif) Installez l’[extension C/C++](https://code.visualstudio.com/docs/languages/cpp ) pour VSCode.

## <a name="building-the-defender-iot-micro-agent"></a>Génération du micro-agent Defender pour IoT 

1. Ouvrez le répertoire avec VSCode. 

1. Accédez au répertoire `cmake`. 

1. Exécutez la commande suivante : 

    ```bash
    cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGET<the appropriate distro configuration file name> .. 
    
    cmake --build . -- -j${env:NPROC}
    ```

    Par exemple : 

    ```bash
    cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGETUBUNTU1804 ..
    
    cmake --build . -- -j${env:NPROC}
    ```

## <a name="next-steps"></a>Étapes suivantes

[Configurez votre solution Azure Defender pour IoT](quickstart-configure-your-solution.md).
