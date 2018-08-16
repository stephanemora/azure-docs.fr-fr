---
title: Créer un traducteur DevKit IoT utilisant Azure Functions et Cognitive Services | Microsoft Docs
description: Utiliser le microphone sur un DevKit IoT pour recevoir un message vocal, puis utiliser Azure Cognitive Services pour le traiter et afficher le texte traduit en anglais
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/28/2018
ms.author: liydu
ms.openlocfilehash: acfff95afacfa1e5c75a799ba84d64cfa0579f66
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/07/2018
ms.locfileid: "39592089"
---
# <a name="use-iot-devkit-az3166-with-azure-functions-and-cognitive-services-to-make-a-language-translator"></a>Utiliser le DevKit IoT AZ3166 avec Azure Functions et Cognitive Services pour créer un traducteur

Dans cet article, vous découvrez comment transformer IoT DevKit en traducteur à l’aide [d’Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/). Il enregistre votre voix et la convertit en un texte traduit en anglais affiché sur l’écran du DevKit.

[IoT MXChip DevKit](https://aka.ms/iot-devkit) est une carte tout-en-un compatible Arduino qui est équipée de périphériques et de capteurs élaborés. Vous pouvez développer pour ce kit à l’aide de l’[extension Visual Studio Code pour Arduino](https://aka.ms/arduino). Par ailleurs, il s’accompagne d’un [catalogue de projets](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) en plein développement pour vous guider dans la réalisation de prototypes de solutions IoT (Internet des objets) qui exploitent les services Microsoft Azure.

## <a name="what-you-need"></a>Ce dont vous avez besoin

Suivez le [Guide de mise en route](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) pour :

* Connecter votre DevKit au Wi-Fi
* Préparer l’environnement de développement

Un abonnement Azure actif. Si vous n’en avez pas, vous pouvez vous inscrire via l’une de ces deux méthodes :

* Activez un [compte d’évaluation Microsoft Azure gratuit pendant 30 jours](https://azure.microsoft.com/free/).
* Réclamez votre [crédit Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) si vous êtes abonné à MSDN ou Visual Studio

## <a name="open-the-project-folder"></a>Ouvrir le dossier de projet

Ouvrez d’abord le dossier de projet. 

### <a name="start-vs-code"></a>Démarrer VS Code

- Assurez-vous que votre DevKit est connecté à votre ordinateur.

- Démarrez VS Code.

- Connectez le kit DevKit à votre ordinateur.

### <a name="open-the-arduino-examples-folder"></a>Ouvrir le dossier des exemples Arduino

Développez la section à gauche **EXEMPLES ARDUINO > Exemples pour MXCHIP AZ3166 > AzureIoT** et sélectionnez **DevKitTranslator** (DevKitTranslator). Une nouvelle fenêtre VS Code s’ouvre, dans laquelle apparaît le dossier de projet. Si vous ne trouvez pas la section Exemples pour MXCHIP AZ3166, vérifiez que votre appareil est correctement connecté et redémarrez VS Code.  

![Exemples IoT DevKit](media/iot-hub-arduino-iot-devkit-az3166-translator/vscode_examples.png)

Vous pouvez également ouvrir l’exemple à partir de la palette de commandes. Utilisez `Ctrl+Shift+P` (macOS : `Cmd+Shift+P`) pour ouvrir la palette de commandes, tapez **Arduino**, puis recherchez et sélectionnez **Arduino : Exemples**.

## <a name="provision-azure-services"></a>Approvisionner les services Azure

Dans la fenêtre de la solution, tapez `Ctrl+P` (macOS : `Cmd+P`), puis entrez `task cloud-provision`.

Dans le terminal VS Code, une ligne de commande interactive vous guide dans l’approvisionnement de tous les services Azure nécessaires :

![Tâche d’approvisionnement du cloud](media/iot-hub-arduino-iot-devkit-az3166-translator/cloud-provision.png)

## <a name="deploy-the-azure-function"></a>Déployer la fonction Azure

Utilisez `Ctrl+P` (macOS : `Cmd+P`) pour exécuter `task cloud-deploy` et déployer le code Azure Functions. Ce processus prend généralement 2 à 5 minutes.

![Tâche de déploiement du cloud](media/iot-hub-arduino-iot-devkit-az3166-translator/cloud-deploy.png)

Une fois que la fonction Azure effectue correctement le déploiement, remplissez le fichier azure_config.h avec le nom de l’application de fonction. Vous pouvez accéder au [portail Azure](https://portal.azure.com/) pour le rechercher :

![Rechercher le nom de l’application de fonction Azure](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-function.png)

> [!NOTE]
> Si la fonction Azure ne fonctionne pas correctement, consultez la [page « erreur de complication pour la fonction Azure » dans le FAQ sur le DevKit IoT](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function) pour résoudre le problème.

## <a name="build-and-upload-the-device-code"></a>Générer et charger le code de l’appareil

1. Utilisez `Ctrl+P` (macOS : `Cmd+P`) pour exécuter `task config-device-connection`.

2. Le terminal vous demande si vous souhaitez utiliser la chaîne de connexion qui effectue une opération d’extraction à partir de l’étape `task cloud-provision`. Vous pouvez également entrer votre propre chaîne de connexion d’appareil en sélectionnant **« Créer nouveau... »**

3. Le terminal vous invite à passer en mode de configuration. Pour cela, maintenez le bouton A enfoncé, puis appuyez sur le bouton de réinitialisation et relâchez-le. L’écran affiche l’ID du kit DevKit et « Configuration ».

   ![Vérification et téléchargement de l’ébauche de projet Arduino](media/iot-hub-arduino-iot-devkit-az3166-translator/config-device-connection.png)

4. Une fois `task config-device-connection` terminé, cliquez sur `F1` pour charger les commandes VS Code et sélectionnez `Arduino: Upload`. Ensuite, VS Code démarre la vérification et le chargement de l’ébauche de projet Arduino.

   ![Vérification et téléchargement de l’ébauche de projet Arduino](media/iot-hub-arduino-iot-devkit-az3166-translator/arduino-upload.png)

Le DevKit redémarre et commence à exécuter le code.

## <a name="test-the-project"></a>Tester le projet

Après l’initialisation de l’application, suivez les instructions sur l’écran de DevKit. La langue source par défaut est le chinois.

Pour sélectionner une autre langue pour la traduction :

1. Appuyez sur le bouton A pour entrer dans le mode de configuration.

2. Appuyez sur le bouton B pour faire défiler toutes les langues source prises en charge.

3. Appuyez sur le bouton A pour confirmer votre choix de langue source.

4. Appuyez sur le bouton B et maintenez-le enfoncé tout en parlant, puis relâchez-le pour lancer la traduction.

5. Le texte traduit en anglais s’affiche à l’écran.

![Faire défiler pour sélectionner la langue](media/iot-hub-arduino-iot-devkit-az3166-translator/select-language.jpg)

![Résultat de la traduction](media/iot-hub-arduino-iot-devkit-az3166-translator/translation-result.jpg)

Sur l’écran affichant le résultat de la traduction, vous pouvez :

- Appuyez sur les boutons A et B pour faire défiler et sélectionnez la langue source.

- Appuyez sur le bouton B pour parler. Pour envoyer le message vocal et obtenir le texte traduit, relâchez le bouton B.

## <a name="how-it-works"></a>Fonctionnement

![mini-solution-voice-to-tweet-diagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

L’ébauche de projet Arduino enregistre votre voix, puis envoie une requête HTTP pour déclencher une fonction Azure. La fonction Azure appelle l’API de traducteur de discours de service cognitif pour effectuer la traduction. Quand la fonction Azure obtient le texte traduit, elle envoie un message cloud-à-appareil (C2D) à l’appareil. La traduction s’affiche ensuite sur l’écran.

## <a name="change-device-id"></a>Modifier l’ID de l’appareil

L’ID d’appareil par défaut inscrit dans Azure IoT Hub est **AZ3166**. Pour modifier l’ID de l’appareil, consultez [Personnaliser l’ID de l’appareil IoT pour le Devkit](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/).

## <a name="problems-and-feedback"></a>Problèmes et commentaires

Si vous rencontrez des problèmes, consultez le [FAQ sur le DevKit IoT](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou contactez-nous par le biais des canaux suivants :

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Étapes suivantes

Vous avez appris à utiliser le Devkit IoT comme traducteur à l’aide d’Azure Functions et de Cognitive Services. Dans ce guide pratique, vous avez appris à :

> [!div class="checklist"]
> * Utiliser une tâche Visual Studio Code pour automatiser les approvisionnements du cloud
> * Configurer la chaîne de connexion d’appareil Azure IoT
> * Déployer la fonction Azure
> * Tester la traduction d’un message vocal

Passez aux autres tutoriels pour apprendre à :

> [!div class="nextstepaction"]
> [Connecter IoT DevKit AZ3166 à l’accélérateur de solution de surveillance à distance Azure IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring)
