---
title: Recevoir un e-mail quand la porte est ouverte à l’aide du service SendGrid et de Azure Functions | Microsoft Docs
description: Surveillez le capteur magnétique pour détecter l’ouverture d’une porte et utilisez Azure Functions pour envoyer une notification par e-mail.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/19/2018
ms.author: liydu
ms.openlocfilehash: 25cb3ba53c663a642f0871becbfbcab39d521c67
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37437713"
---
# <a name="door-monitor"></a>Surveillance de la porte          

Le DevKit IoT MXChip contient un capteur magnétique intégré. Dans ce projet, vous détectez la présence ou l’absence d’un champ magnétique fort à proximité, provenant d’un petit aimant permanent dans ce cas précis. .

## <a name="what-you-learn"></a>Contenu

Dans ce projet, vous allez apprendre :
- L’utilisation du capteur magnétique du DevKit IoT MXChip pour détecter le déplacement d’un aimant à proximité.
- L’utilisation du service SendGrid pour envoyer une notification à votre adresse e-mail.

> [!NOTE]
> Pour une utilisation pratique de ce projet :
> - Montez un aimant sur le bord d’une porte.
> - Montez le DevKit sur le montant de porte proche de l’aimant. L’ouverture ou la fermeture de la porte déclenchera le capteur, entraînant une notification de l’événement par e-mail.

## <a name="what-you-need"></a>Ce dont vous avez besoin

Suivez le [Guide de mise en route](iot-hub-arduino-iot-devkit-az3166-get-started.md) pour :

* Connecter votre DevKit au Wi-Fi
* Préparer l’environnement de développement

Un abonnement Azure actif. Si vous n’en avez pas, vous pouvez vous inscrire via l’une de ces méthodes :

* Activez un [compte Microsoft Azure pour un essai gratuit de 30 jours](https://azure.microsoft.com/free/).
* Réclamez votre [crédit Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) si vous êtes un abonné à MSDN ou Visual Studio.

## <a name="deploy-sendgrid-service-in-azure"></a>Déployer un service SendGrid dans Azure

[SendGrid](https://sendgrid.com/) est une plateforme de livraison d’e-mails basée sur le cloud. Ce service sera utilisé pour envoyer des notifications par e-mail.

> [!NOTE]
> Si vous avez déjà déployé un service SendGrid, vous pouvez passer directement à [Déployer IoT Hub dans Azure](#deploy-iot-hub-in-azure).

### <a name="sendgrid-deployment"></a>Déploiement de SendGrid

Pour attribuer les services Azure, utilisez le bouton **Déployer vers Azure**. Ce bouton permet un déploiement simple et rapide de vos projets open source sur Microsoft Azure.

Cliquez sur le bouton **Déployer dans Azure** ci-dessous. 

[![Déploiement sur Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

La page suivante s’affiche alors.

> [!NOTE]
> Si vous ne voyez pas la page suivante, vous devez d’abord vous connecter à votre compte Azure.

Remplissez le formulaire d’inscription :

  * **Groupe de ressources** : créez un groupe de ressources pour héberger le service SendGrid ou utilisez-en un existant. Pour plus d’informations, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

  * **Nom** : le nom de votre service SendGrid. Choisissez un nom unique, différent des autres services dont vous disposez.

  * **Mot de passe** : le service requiert un mot de passe, qui ne sera pour rien dans ce projet.

  * **E-mail** : le service SendGrid enverra une vérification à cette adresse e-mail.

  > [!NOTE]
  > Cochez l’option **Épingler au tableau de bord** pour rendre cette application plus facile à trouver dans le futur.
 
![Déploiement de SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

### <a name="sendgrid-api-key-creation"></a>Création de la clé API SendGrid

Une fois le déploiement réussi, cliquez dessus puis cliquez sur le bouton **Gérer**. Vous accédez à votre page SendGrid et vous devez vérifier votre adresse e-mail.

![Gérer SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

Sur la page SendGrid, cliquez sur **Paramètres** > **Clés API** > **Créer une clé API**. Saisissez le **nom de la clé API** et cliquez sur **Créer et voir**.

![SendGrid Créer une API Première](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

![SendGrid Créer une API Deuxième](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

Votre clé API ne s’affiche qu’une seule fois. Assurez-vous de la copier et de la stocker en toute sécurité, car elle est utilisée à l’étape suivante.

## <a name="deploy-iot-hub-in-azure"></a>Déployer IoT Hub dans Azure

Les étapes suivantes configurent les autres services liés à Azure IoT et déploient Azure Functions pour ce projet.

Cliquez sur le bouton **Déployer dans Azure** ci-dessous. 

[![Déploiement sur Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

La page suivante s’affiche alors.

> [!NOTE]
> Si vous ne voyez pas la page suivante, vous devez d’abord vous connecter à votre compte Azure.

Remplissez le formulaire d’inscription :

  * **Groupe de ressources** : créez un groupe de ressources pour héberger le service SendGrid ou utilisez-en un existant. Pour plus d’informations, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

  * **Nom de IoT Hub** : nom de votre IoT Hub. Choisissez un nom unique, différent des autres services dont vous disposez.

  * **Référence SKU de IOT Hub**: F1 (limitée à une par abonnement) est disponible. Vous pouvez voir plus d’informations sur la tarification dans [Niveau de tarification et de mise à l’échelle](https://azure.microsoft.com/pricing/details/iot-hub/).

  * **E-mail d’expédition** : cela doit être la même adresse e-mail que celle utilisée lors de la configuration du service SendGrid.

  > [!NOTE]
  > Cochez l’option **Épingler au tableau de bord** pour rendre cette application plus facile à trouver dans le futur.
 
![Déploiement de IoTHub](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

## <a name="build-and-upload-the-code"></a>Générer et charger le code

### <a name="start-vs-code"></a>Démarrer VS Code

- Assurez-vous que votre DevKit n’est **pas** connecté à votre ordinateur.
- Démarrez VS Code.
- Connectez le kit DevKit à votre ordinateur.

> [!NOTE]
> Quand vous lancez VS Code, vous pouvez recevoir un message d’erreur indiquant que l’IDE Arduino ou le package de la carte associée ne peut pas être trouvé. Si vous recevez cette erreur, fermez VS Code et relancez l’IDE Arduino ; VS Code devrait localiser correctement le chemin d’accès de l’IDE Arduino.

### <a name="open-arduino-examples-folder"></a>Ouvrir le dossier des exemples Arduino

Développez la section **EXEMPLES ARDUINO** à gauche, accédez à **Exemples pour MXCHIP AZ3166 > AzureIoT** et sélectionnez **DoorMonitor**. Cette action ouvre une nouvelle fenêtre VS Code avec le dossier de projet qu’elle contient.

![mini-solution-exemples](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

> [!NOTE]
> Vous pouvez également ouvrir l’exemple à partir de la palette de commandes. Utilisez `Ctrl+Shift+P` (macOS : `Cmd+Shift+P`) pour ouvrir la palette de commandes, tapez **Arduino**, puis recherchez et sélectionnez **Arduino : Exemples**.

### <a name="provision-azure-services"></a>Approvisionner les services Azure

Dans la fenêtre de la solution, exécutez la tâche d’approvisionnement du cloud :
- Tapez `Ctrl+P` (macOS: `Cmd+P`).
- Entrez `task cloud-provision` dans la zone de texte fournie.

Dans le terminal VS Code, une ligne de commande interactive vous guide dans l’approvisionnement des services Azure nécessaires. Sélectionnez tous les éléments identiques dans la liste demandée que vous avez configurée précédemment dans [Déployer IoT Hub dans Azure](#deploy-iot-hub-in-azure).

![Approvisionnement du cloud](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> Si la page se bloque dans l’état de chargement lorsque vous tentez de vous connecter à Azure, consultez la [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure) pour résoudre ce problème. 

### <a name="build-and-upload-the-device-code"></a>Générer et charger le code de l’appareil

#### <a name="windows"></a>Windows

1. Utilisez `Ctrl+P` pour exécuter `task device-upload`.
2. Le terminal vous invite à passer en mode de configuration. Pour cela, maintenez le bouton A enfoncé, puis appuyez sur le bouton de réinitialisation et relâchez-le. L’écran affiche le numéro d’identification du DevKit et le mot *Configuration*.

Cette procédure définit la chaîne de connexion extraite de l’étape [Approvisionner des services Azure](#provision-azure-services).

VS Code commence ensuite à vérifier et charger l’ébauche de projet Arduino sur le DevKit :

![device-upload](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

Le DevKit redémarre et commence à exécuter le code.

> [!NOTE]
> Parfois, vous pouvez recevoir un message d’erreur « Error: AZ3166: Unknown package » (Erreur : AZ3166 : Package inconnu). Cette erreur se produit lorsque l’index du package de la carte n’est pas actualisé correctement. Pour corriger cette erreur, reportez-vous à cette [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

#### <a name="macos"></a>macOS

1. Placez le DevKit en mode de configuration : maintenez enfoncé le bouton A, puis enfoncez et relâchez le bouton de réinitialisation. L’écran affiche « Configuration ».
2. Utilisez `Cmd+P` pour exécuter `task device-upload`.

Cette procédure définit la chaîne de connexion extraite de l’étape [Approvisionner des services Azure](#provision-azure-services).

VS Code commence ensuite à vérifier et charger l’ébauche de projet Arduino sur le DevKit :

![device-upload](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

Le DevKit redémarre et commence à exécuter le code.

> [!NOTE]
> Parfois, vous pouvez recevoir un message d’erreur « Error: AZ3166: Unknown package » (Erreur : AZ3166 : Package inconnu). Cette erreur se produit lorsque l’index du package de la carte n’est pas actualisé correctement. Pour corriger cette erreur, reportez-vous à cette [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Tester le projet

Tout d’abord, le programme s’initialise lorsque le DevKit est en présence d’un champ magnétique stable.

Après l’initialisation, `Door closed` s’affiche sur l’écran. Lorsque le champ magnétique varie, l’état passe à `Door opened`. Chaque fois que l’état de la porte change, vous recevez une notification par e-mail. (Ces e-mails peuvent prendre jusqu’à cinq minutes avant d’être reçus.)

![Aimant proche du capteur : porte fermée](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "Aimant proche du capteur : porte fermée")

![Aimant éloigné du capteur : porte ouverte](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "Aimant éloigné du capteur : porte ouverte")

## <a name="problems-and-feedback"></a>Problèmes et commentaires

Si vous rencontrez des problèmes, consultez les [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou connectez-vous en utilisant les canaux suivants :

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Étapes suivantes

Vous avez appris à connecter un appareil DevKit à votre accélérateur de solution de surveillance à distance Azure IoT et à utiliser le service SendGrid pour envoyer un e-mail. Voici les prochaines étapes suggérées :

* [Vue d’ensemble des accélérateurs de solution de surveillance à distance Azure IoT](https://docs.microsoft.com/azure/iot-suite/)
* [Connecter un appareil DevKit IoT MXChip à votre application Azure IoT Central](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
