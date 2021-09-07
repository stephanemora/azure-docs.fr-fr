---
title: Résoudre les problèmes relatifs à Azure Percept Audio et au module Speech
description: Obtenez des conseils de dépannage pour Azure Percept Audio et azureearspeechclientmodule.
author: tsampige
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 08/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 00f3694201d3aaf876d642e67e5b523f6fb50547
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222710"
---
# <a name="troubleshoot-azure-percept-audio-and-speech-module"></a>Résoudre les problèmes relatifs à Azure Percept Audio et au module Speech

Utilisez les instructions ci-dessous pour résoudre les problèmes liés aux applications de l’Assistant vocal.

## <a name="checking-runtime-status-of-the-speech-module"></a>Vérification de l’état d’exécution du module de reconnaissance vocale

Vérifiez si l’état d’exécution de **azureearspeechclientmodule** est **En cours d’exécution**. Pour rechercher l’état d’exécution des modules de vos appareils, ouvrez le [portail Azure](https://portal.azure.com/) et accédez à **Toutes les ressources** ->  **[votre hub IoT]**  -> **IoT Edge** ->  **[votre identité d’appareil]** . Sélectionnez l’onglet **Modules** pour voir l’état d’exécution de tous les modules installés.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Page des appareils Edge dans le portail Azure.":::

Si l’état d’exécution de **azureearspeechclientmodule** n’est pas **En cours d’exécution**, sélectionnez **Définir des modules** -> **azureearspeechclientmodule**. Dans la page **Paramètres du module**, définissez **État souhaité** sur **En cours d’exécution**, puis sélectionnez **Mettre à jour**.

## <a name="voice-assistant-application-doesnt-load"></a>L’application Assistant vocal ne charge pas
Essayez de [déployer l’un des modèles d’Assistant vocal](./tutorial-no-code-speech.md). Le déploiement d’un modèle garantit la création de toutes les ressources de prise en charge nécessaires pour les applications d’Assistant vocal.

## <a name="voice-assistant-template-doesnt-get-created"></a>Le modèle d’Assistant vocal n’est pas créé
L’échec de la création d’un modèle d’Assistant vocal est généralement un problème lié à l’une des ressources de prise en charge.
1. [Supprimez toutes les ressources d’Assistant vocal créées précédemment](./delete-voice-assistant-application.md).
1. Déployez un nouveau [modèle d’Assistant vocal](./tutorial-no-code-speech.md).

## <a name="voice-assistant-was-created-but-doesnt-respond-to-commands"></a>L’Assistant vocal a été créé mais ne répond pas aux commandes
Suivez les instructions dans [Comportement des voyants et guide de résolution des problèmes](audio-button-led-behavior.md) pour résoudre ce problème.

## <a name="voice-assistant-doesnt-respond-to-custom-keywords-created-in-speech-studio"></a>L’Assistant vocal ne répond pas à des mots clés personnalisés créés dans Speech Studio
Cela peut se produire si le module de reconnaissance vocale est obsolète. Effectuez les étapes suivantes pour mettre à jour le module Speech vers la dernière version :

1. Dans le menu de gauche de la page d’accueil Azure Percept Studio, sélectionnez **Appareils**.
1. Recherchez puis sélectionnez votre appareil.

    :::image type="content" source="./media/tutorial-no-code-speech/devices.png" alt-text="Capture d’écran de la liste des appareils dans Azure Percept Studio.":::
1. Dans la fenêtre Appareils, sélectionnez l’onglet **Speech**.
1. Vérifiez la version du module Speech. Si une mise à jour est disponible, un bouton **Mettre à jour** s’affiche à côté du numéro de version.
1. Sélectionnez **Mettre à jour** pour déployer la mise à jour du module Speech. Le processus de mise à jour prend généralement de 2 à 3 minutes.

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
- [Comment revenir à une application d’Assistant vocal créée précédemment](return-to-voice-assistant-application-window.md)
