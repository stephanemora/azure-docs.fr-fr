---
title: Résoudre les problèmes liés au module de reconnaissance vocale et à Azure Percept Audio
description: Obtenez des conseils de dépannage pour Azure Percept Audio et azureearspeechclientmodule.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: c02c93acd23b71f73d5e27d8914d08fb1a1f99f9
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110071395"
---
# <a name="azure-percept-audio-and-speech-module-troubleshooting"></a>Dépannage du module Audio et reconnaissance vocale d’Azure Percept

Utilisez les instructions ci-dessous pour résoudre les problèmes liés aux applications de l’Assistant vocal.

## <a name="understanding-ear-som-led-indicators"></a>Fonctionnement des indicateurs LED du Ear SoM

Vous pouvez utiliser les voyants (LED) pour comprendre dans quel l’état se trouve votre appareil. Il faut environ 4-5 minutes pour que l’appareil s’allume complètement après la mise sous tension. Au fil des étapes d’initialisation, vous allez voir :

1. LED blanche centrale allumée (statique) : l’appareil est sous tension.
1. LED blanche centrale allumée (clignotante) : l’authentification est en cours.
1. LED blanche au centre (statique) : l’appareil est authentifié mais le mot clé n’est pas configuré.
1. Les trois voyants LED deviennent bleus une fois qu’une démonstration a été déployée et que l’appareil est prêt à être utilisé.

Pour plus d’informations, consultez cet article sur le [bouton Azure Percept Audio et le comportement des LED](./audio-button-led-behavior.md).

### <a name="troubleshooting-led-issues"></a>Résolution des problèmes liés aux LED
- **Si la LED centrale est blanche**, essayez [d’utiliser un modèle pour créer un Assistant vocal](./tutorial-no-code-speech.md).
- **Si la LED centrale clignote toujours**, cela indique un problème d’authentification. Essayez ces étapes de résolution des problèmes :
    - Assurez-vous que vos connexions USB-A et micro USB sont sécurisées 
    - Vérifier si le [module de reconnaissance vocale est en cours d’exécution](./troubleshoot-audio-accessory-speech-module.md#checking-runtime-status-of-the-speech-module)
    - Redémarrer l’appareil
    - [Collecter les journaux](./troubleshoot-audio-accessory-speech-module.md#collecting-speech-module-logs) et les joindre à une demande de support
    - Vérifiez si votre kit de développement exécute les derniers logiciels et appliquez une mise à jour le cas échéant.

## <a name="checking-runtime-status-of-the-speech-module"></a>Vérification de l’état d’exécution du module de reconnaissance vocale

Vérifiez si l’état d’exécution de **azureearspeechclientmodule** est **En cours d’exécution**. Pour rechercher l’état d’exécution des modules de vos appareils, ouvrez le [portail Azure](https://portal.azure.com/) et accédez à **Toutes les ressources** ->  **[votre hub IoT]**  -> **IoT Edge** ->  **[votre identité d’appareil]** . Cliquez sur l’onglet **Modules** pour voir l’état d’exécution de tous les modules installés.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Page des appareils Edge dans le portail Azure.":::

Si l’état d’exécution de **azureearspeechclientmodule** n’est pas **En cours d’exécution**, cliquez sur **Définir des modules** -> **azureearspeechclientmodule**. Dans la page **Paramètres du module**, définissez **État souhaité** sur **En cours d’exécution**, puis cliquez sur **Mettre à jour**.

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

[local host file path] fait référence à l’emplacement sur votre ordinateur hôte où vous voulez copier le fichier.txt. [remote username] est le nom d’utilisateur SSH choisi lors de l’[expérience de configuration](./quickstart-percept-dk-set-up.md).

## <a name="known-issues"></a>Problèmes connus
- Si vous utilisez une version d’évaluation gratuite, le modèle de reconnaissance vocale peut dépasser le plan de tarification de l’essai gratuit. Dans ce cas, le modèle cessera de fonctionner sans message d’erreur.
- Si plus de 5 appareils IoT Edge sont connectés, le rapport (le texte envoyé via la télémétrie à IoT Hub et Speech Studio) peut être bloqué.
- Si l’appareil se trouve dans une autre région que les ressources, le message du rapport peut être retardé. 

## <a name="useful-links"></a>Liens utiles
- [Installation d’Azure Percept Audio](./quickstart-percept-audio-setup.md)
- [Comportement des boutons et des voyants d’Azure Percept Audio](./audio-button-led-behavior.md)
- [Créer un Assistant vocal avec le DK Azure Percept et Azure Percept Audio](./tutorial-no-code-speech.md)
- [Guide de résolution des problèmes d’Azure Percept DK](./troubleshoot-dev-kit.md)
