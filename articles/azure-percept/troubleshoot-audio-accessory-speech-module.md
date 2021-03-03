---
title: Résoudre les problèmes liés aux modules audio et reconnaissance vocale d’Azure Percept
description: Découvrez des conseils pour le dépannage pour certains des problèmes les plus courants rencontrés lors de l’expérience d’intégration
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 1e2ad920afb55066f07430568f976154f6d7cae1
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101678385"
---
# <a name="azure-percept-audio-and-speech-module-troubleshooting"></a>Dépannage du module Audio et reconnaissance vocale d’Azure Percept

Utilisez les instructions ci-dessous pour résoudre les problèmes liés aux applications de l’Assistant vocal.

## <a name="collecting-speech-module-logs"></a>Collecte des journaux de module de reconnaissance vocale

Pour exécuter ces commandes, [connectez-vous au point d’accès Wi-Fi du DK Azure Percept et connectez-vous au devkit via SSH](./how-to-ssh-into-percept-dk.md), puis entrez les commandes dans le terminal SSH.

```console
 iotedge logs azureearspeechclientmodule
```

Pour rediriger une sortie vers un fichier .txt de façon à l’analyser de façon plus approfondie, utilisez la syntaxe suivante :

```console
[command] > [file name].txt
```

Après avoir redirigé la sortie vers un fichier .txt, copiez le fichier sur votre PC hôte via SCP :

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

[local host file path] fait référence à l’emplacement sur votre ordinateur hôte où vous voulez copier le fichier .txt. [remote username] est le nom d’utilisateur SSH choisi lors de l’[expérience de configuration](./quickstart-percept-dk-set-up.md). Si vous n’avez pas configuré une connexion SSH lors de l’expérience OOBE, votre nom d’utilisateur distant est « root ».

## <a name="checking-runtime-status-of-the-speech-module"></a>Vérification de l’état d’exécution du module de reconnaissance vocale

Vérifiez si l’état d’exécution de **azureearspeechclientmodule** est **En cours d’exécution**. Pour rechercher l’état d’exécution des modules de vos appareils, ouvrez le [portail Azure](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) et accédez à **Toutes les ressources** ->  **\<your IoT hub>**  -> **IoT Edge** ->  **\<your device ID>** . Cliquez sur l’onglet **Modules** pour voir l’état d’exécution de tous les modules installés.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Page des appareils Edge dans le portail Azure.":::

Si l’état d’exécution de **azureearspeechclientmodule** n’est pas **En cours d’exécution**, cliquez sur **Définir des modules** -> **azureearspeechclientmodule**. Dans la page **Paramètres du module**, définissez **État souhaité** sur **En cours d’exécution**, puis cliquez sur **Mettre à jour**.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/firmware-desired-status-stopped.png" alt-text="Écran Définir des modules dans le portail Azure.":::

## <a name="understanding-ear-som-led-indicators"></a>Fonctionnement des indicateurs LED du Ear SoM

Vous pouvez utiliser les indicateurs LED pour comprendre l’état de votre appareil. En général, il faut environ 2 minutes pour que le module s’initialise complètement après la *mise sous tension*. Au fil des étapes d’initialisation, vous allez voir :

1. 1 voyant vert à gauche - l’appareil est allumé. 
2. 1 voyant vert à gauche et une LED clignotante au centre - l’authentification est en cours. 
3. Les trois LED vont passer au bleu une fois que l’appareil est authentifié et prêt à être utilisé.

|État de la LED                  |État du Ear SoM            |
|----------------------------|---------------------------|
|1x verte (LED gauche)         |Sous tension |
|1x verte (LED gauche) <br> 1x verte clignotante (LED centrale) |authentification en cours |
|3x éteinte                      |initialisation terminée |
|3x bleue                     |prêt à être utilisé |
|3x bleue clignotante            |mot clé reconnu |
|3x bleue avec clignotement rapide              |traitement |
|3x rouge                      |muet |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la résolution des problèmes Azure Percept DK, consultez le [Guide de résolution des problèmes généraux](./troubleshoot-dev-kit.md) .