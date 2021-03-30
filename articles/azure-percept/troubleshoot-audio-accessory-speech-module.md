---
title: Résoudre les problèmes liés au module de reconnaissance vocale et à Azure Percept Audio
description: Obtenez des conseils de dépannage pour Azure Percept Audio et azureearspeechclientmodule.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: f34013bdb14481bfe872a9b3c4234d603bc2d7ec
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102635567"
---
# <a name="azure-percept-audio-and-speech-module-troubleshooting"></a>Dépannage du module Audio et reconnaissance vocale d’Azure Percept

Utilisez les instructions ci-dessous pour résoudre les problèmes liés aux applications de l’Assistant vocal.

## <a name="collecting-speech-module-logs"></a>Collecte des journaux de module de reconnaissance vocale

Pour exécuter ces commandes, [connectez-vous au point d’accès Wi-Fi du DK Azure Percept et connectez-vous au devkit via SSH](./how-to-ssh-into-percept-dk.md), puis entrez les commandes dans le terminal SSH.

```console
sudo iotedge logs azureearspeechclientmodule
```

Pour rediriger une sortie vers un fichier .txt de façon à l’analyser de façon plus approfondie, utilisez la syntaxe suivante :

```console
sudo [command] > [file name].txt
```

Après avoir redirigé la sortie vers un fichier .txt, copiez le fichier sur votre PC hôte via SCP :

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

[local host file path] fait référence à l’emplacement sur votre ordinateur hôte où vous voulez copier le fichier .txt. [remote username] est le nom d’utilisateur SSH choisi lors de [l’expérience d’intégration](./quickstart-percept-dk-set-up.md). Si vous n’avez pas configuré de connexion SSH lors de l’expérience d’intégration Azure Percept DK, votre nom d’utilisateur distant est root.

## <a name="checking-runtime-status-of-the-speech-module"></a>Vérification de l’état d’exécution du module de reconnaissance vocale

Vérifiez si l’état d’exécution de **azureearspeechclientmodule** est **En cours d’exécution**. Pour rechercher l’état d’exécution des modules de vos appareils, ouvrez le [portail Azure](https://portal.azure.com/) et accédez à **Toutes les ressources** ->  **\<your IoT hub>**  -> **IoT Edge** ->  **\<your device ID>** . Cliquez sur l’onglet **Modules** pour voir l’état d’exécution de tous les modules installés.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Page des appareils Edge dans le portail Azure.":::

Si l’état d’exécution de **azureearspeechclientmodule** n’est pas **En cours d’exécution**, cliquez sur **Définir des modules** -> **azureearspeechclientmodule**. Dans la page **Paramètres du module**, définissez **État souhaité** sur **En cours d’exécution**, puis cliquez sur **Mettre à jour**.

## <a name="understanding-ear-som-led-indicators"></a>Fonctionnement des indicateurs LED du Ear SoM

Vous pouvez utiliser les indicateurs LED pour comprendre l’état de votre appareil. En général, il faut environ 2 minutes pour que le module s’initialise complètement après la *mise sous tension*. Au fil des étapes d’initialisation, vous allez voir :

1. 1 LED centrale blanche : l’appareil est sous tension.
2. 1 LED centrale blanche clignotante : l’authentification est en cours.
3. Les trois LED vont passer au bleu une fois que l’appareil est authentifié et prêt à être utilisé.

|LED|État du voyant|État du SoM d’écoute|
|---|---------|--------------|
|L02|1 voyant blanc statique allumé|Mise sous tension |
|L02|1 voyant blanc clignotant (0,5 Hz)|Authentification en cours |
|L01 & L02 & L03|3 voyants bleus statiques allumés|En attente du mot clé|
|L01 & L02 & L03|Matrice de voyants clignotants (20 fps) |Mode écoute ou vocal|
|L01 & L02 & L03|Matrice de voyants à clignotement rapide (20 fps)|Opération en cours|
|L01 & L02 & L03|3 voyants rouges statiques allumés |Désactiver le son|

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la résolution des problèmes Azure Percept DK, consultez le [Guide de résolution des problèmes généraux](./troubleshoot-dev-kit.md) .
