---
title: Résoudre les problèmes liés au module de reconnaissance vocale et à Azure Percept Audio
description: Obtenez des conseils de dépannage pour Azure Percept Audio et azureearspeechclientmodule.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: c4fc7d7564ecd30326fbec832639b2a81d55e6d5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605652"
---
# <a name="azure-percept-audio-and-speech-module-troubleshooting"></a>Dépannage du module Audio et reconnaissance vocale d’Azure Percept

Utilisez les instructions ci-dessous pour résoudre les problèmes liés aux applications de l’Assistant vocal.

## <a name="collecting-speech-module-logs"></a>Collecte des journaux de module de reconnaissance vocale

Pour exécuter ces commandes, connectez-vous via [SSH au kit de développement](./how-to-ssh-into-percept-dk.md), puis entrez les commandes dans l’invite du client SSH.

Collectez les journaux du module de reconnaissance vocale :

```console
sudo iotedge logs azureearspeechclientmodule
```

Pour rediriger une sortie vers un fichier .txt de façon à l’analyser de façon plus approfondie, utilisez la syntaxe suivante :

```console
sudo [command] > [file name].txt
```

Modifiez les autorisations du fichier .txt pour qu’il puisse être copié :

```console
sudo chmod 666 [file name].txt
```

Après avoir redirigé la sortie vers un fichier .txt, copiez le fichier sur votre PC hôte via SCP :

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

[local host file path] fait référence à l’emplacement sur votre ordinateur hôte où vous voulez copier le fichier .txt. [remote username] est le nom d’utilisateur SSH choisi lors de l’[expérience de configuration](./quickstart-percept-dk-set-up.md).

## <a name="checking-runtime-status-of-the-speech-module"></a>Vérification de l’état d’exécution du module de reconnaissance vocale

Vérifiez si l’état d’exécution de **azureearspeechclientmodule** est **En cours d’exécution**. Pour rechercher l’état d’exécution des modules de vos appareils, ouvrez le [portail Azure](https://portal.azure.com/) et accédez à **Toutes les ressources** ->  **[votre hub IoT]**  -> **IoT Edge** ->  **[votre identité d’appareil]** . Cliquez sur l’onglet **Modules** pour voir l’état d’exécution de tous les modules installés.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Page des appareils Edge dans le portail Azure.":::

Si l’état d’exécution de **azureearspeechclientmodule** n’est pas **En cours d’exécution**, cliquez sur **Définir des modules** -> **azureearspeechclientmodule**. Dans la page **Paramètres du module**, définissez **État souhaité** sur **En cours d’exécution**, puis cliquez sur **Mettre à jour**.

## <a name="understanding-ear-som-led-indicators"></a>Fonctionnement des indicateurs LED du Ear SoM

Vous pouvez utiliser les indicateurs LED pour comprendre l’état de votre appareil. En général, il faut environ deux minutes pour que le module s’initialise complètement après la mise sous tension de l’appareil. Au fil des étapes d’initialisation, vous allez voir :

1. LED blanche centrale allumée (statique) : l’appareil est sous tension.
2. LED blanche centrale allumée (clignotante) : l’authentification est en cours.
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
