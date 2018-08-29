---
title: Utilisation de l’approvisionnement automatique du service Azure IoT Hub Device Provisioning pour inscrire le DevKit IoT MXChip auprès d’IoT Hub | Microsoft Docs
description: Utilisation de l’approvisionnement automatique du service Azure IoT Hub Device Provisioning pour inscrire le DevKit IoT MXChip auprès d’IoT Hub.
author: liydu
ms.author: liydu
ms.date: 04/04/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: jeffya
ms.openlocfilehash: d8912a5da8c4df2069d8bc53454748b5fb3d5c39
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2018
ms.locfileid: "42141108"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>Utiliser l’approvisionnement automatique du service Azure IoT Hub Device Provisioning pour inscrire le DevKit IoT MXChip auprès d’IoT Hub

Cet article décrit la procédure d’utilisation de [l’approvisionnement automatique](concepts-auto-provisioning.md) du service Azure IoT Hub Device Provisioning pour inscrire le DevKit IoT MXChip auprès d’IoT Hub. Ce tutoriel vous montre comment effectuer les opérations suivantes :

* configurer le point de terminaison global du service Device Provisioning sur un appareil.
* utiliser un secret d’appareil unique (UDS) pour générer un certificat X.509 ;
* inscrire un appareil particulier ;
* vérifier que l’appareil est enregistré.

Le [IoT MXChip DevKit](https://aka.ms/iot-devkit) est une carte tout-en-un compatible Arduino qui est équipée de périphériques et de capteurs élaborés. Vous pouvez développer pour ce kit à l’aide de [l’extension Visual Studio Code pour Arduino](https://aka.ms/arduino). Par ailleurs, il s’accompagne d’un [catalogue de projets](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) en plein développement pour vous guider dans la réalisation de prototypes de solutions IoT (Internet des objets) qui exploitent les services Azure.

## <a name="before-you-begin"></a>Avant de commencer

Pour effectuer les étapes de ce didacticiel, commencez par exécuter les tâches suivantes :

* Préparez le DevKit en suivant les étapes contenues dans l’article [Connecter IoT DevKit AZ3166 à Azure IoT Hub dans le cloud](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).
* Procédez à la mise à niveau vers la dernière version du microprogramme (1.3.0 ou ultérieure) à l’aide du didacticiel [Update DevKit firmware](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) (Mettre à jour le microprogramme du DevKit).
* Créez et liez un IoT Hub avec une instance du service Device Provisioning en suivant les étapes décrites dans [Configurer le service IoT Hub Device Provisioning avec le portail Azure](/azure/iot-dps/quick-setup-auto-provision).

## <a name="build-and-deploy-auto-provisioning-registration-software-to-the-device"></a>Créer et déployer un logiciel d’inscription à l’approvisionnement automatique sur l’appareil

Pour connecter le DevKit à l’instance du service Device Provisioning que vous avez créée :

1. Dans le portail Azure, sélectionnez le volet **Vue d’ensemble** de votre instance du service Device Provisioning et notez les valeurs des paramètres **Point de terminaison d’appareil global** et **Étendue de l’ID**.
  ![Point de terminaison global de service Device Provisioning et étendue de l’ID](./media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

2. Vérifiez que vous avez installé `git` sur votre machine et qu’il est ajouté aux variables d’environnement accessibles à la fenêtre de commande. Consultez [Outils clients Git de Software Freedom Conservancy](https://git-scm.com/download/) pour installer la dernière version.

3. Ouvrez une invite de commandes. Clonez le référentiel GitHub pour l’exemple de code du service Device Provisioning :
  ```bash
  git clone https://github.com/DevKitExamples/DevKitDPS.git
  ```

4. Ouvrez Visual Studio Code, connectez le DevKit à l’ordinateur, puis ouvrez le dossier qui contient le code que vous avez cloné.

5. Ouvrez **DevKitDPS.ino**. Recherchez `[Global Device Endpoint]` et `[ID Scope]`, puis remplacez-les par les valeurs que vous venez de noter.
  ![Point de terminaison de service Device Provisioning](./media/how-to-connect-mxchip-iot-devkit/endpoint.png) Vous pouvez ne pas attribuer de valeur au paramètre **registrationId**. L’application génère automatiquement un ID à partir de l’adresse MAC et de la version du microprogramme. Si vous voulez personnaliser l’ID d’inscription, vous devez utiliser uniquement une combinaison de caractères alphanumériques, de minuscules et de traits d’union et ne pas dépasser 128 caractères. Pour plus d’informations, consultez [Gérer les inscriptions d’appareils avec le portail Azure](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments).

6. Utilisez Quick Open dans VS Code (Windows : `Ctrl+P`, macOS : `Cmd+P`) et tapez *task device-upload* pour générer et charger le code dans le DevKit.

7. La fenêtre Sortie indique si la tâche a été effectuée.

## <a name="save-a-unique-device-secret-on-an-stsafe-security-chip"></a>Enregistrer un secret d’appareil unique dans un processeur de sécurité STSAFE

L’approvisionnement automatique peut être configuré sur un appareil en fonction du [mécanisme d’attestation](concepts-security.md#attestation-mechanism) de l’appareil. Le MXChip IoT DevKit utilise le moteur [DICE (Device Identity Composition Engine)](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) de [Trusted Computing Group (TCG)](https://trustedcomputinggroup.org). Un *secret d’appareil unique (UDS)* enregistré dans un processeur de sécurité STSAFE du DevKit sert à générer le [certificat X.509](concepts-security.md#x509-certificates) unique de l’appareil. Ce certificat est utilisé par la suite pour le processus d’inscription du service Device Provisioning, ainsi que lors de l’inscription au moment de l’exécution.

Par défaut, un secret d’appareil unique est une chaîne de 64 caractères, comme dans l’exemple suivant :

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

La chaîne est divisée en paires de caractères qui sont utilisées dans le calcul de sécurité. L’exemple d’UDS ci-dessus est résolu : `0x19`, `0xe2`, `0x5a`, `0x25`, `0x9d`, `0x0c`, `0x2b`, `0xe0`, `0x3a`, `0x02`, `0xd4`, `0x16`, `0xc0`, `0x5c`, `0x48`, `0xcc`, `0xd0`, `0xcc`, `0x7d`, `0x17`, `0x43`, `0x45`, `0x8a`, `0xae`, `0x1c`, `0xb4`, `0x88`, `0xb0`, `0x74`, `0x99`, `0x3e`, `0xae`.

Pour enregistrer un secret d’appareil unique sur le DevKit :

1. Ouvrez le moniteur de série à l’aide d’un outil tel que Putty. Pour plus d’informations, consultez [Use configuration mode](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) (Utiliser le mode de configuration).

2. Une fois le DevKit connecté à l’ordinateur, maintenez le bouton **A** enfoncé, puis appuyez brièvement sur le bouton **Réinitialiser** pour passer en mode de configuration. L’écran affiche l’ID du kit DevKit et Configuration.

3. Prenez l’exemple de chaîne UDS et remplacez-en un ou plusieurs caractères par d’autres valeurs comprises entre `0` et `f` correspondant à votre propre UDS.

4. Dans la fenêtre du moniteur de série, tapez *set_dps_uds [votre_propre_valeur_uds]*, puis sélectionnez Entrée.
  > [!NOTE]
  > Par exemple, si vous définissez votre propre UDS en remplaçant les deux derniers caractères par `f`, vous devez entrer la commande suivante : `set_dps_uds 19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eff`.

5. Sans fermer la fenêtre du moniteur de série, appuyez sur bouton **Réinitialiser** du DevKit.

6. Notez les valeurs des paramètres **Adresse MAC de DevKit** et **Version du microprogramme de DevKit**.
  ![Version du microprogramme](./media/how-to-connect-mxchip-iot-devkit/firmware-version.png)

## <a name="generate-an-x509-certificate"></a>Générer un certificat X.509

Vous devez maintenant générer un certificat X.609. 

### <a name="windows"></a>Windows

1. Ouvrez l’Explorateur de fichiers et accédez au dossier qui contient l’exemple de code du service Device Provisioning que vous avez cloné précédemment. Dans le dossier **.build**, recherchez et copiez **DPS.ino.bin** et **DPS.ino.map**.
  ![Fichiers générés](./media/how-to-connect-mxchip-iot-devkit/generated-files.png)
  > [!NOTE]
  > Si vous avez modifié la configuration du paramètre `built.path` pour Arduino et l’avez défini sur un autre dossier, vous devez rechercher ces fichiers dans le dossier que vous avez configuré.

2. Collez ces deux fichiers dans le dossier **tools** au même niveau que le dossier **.build**.

3. Exécutez **dps_cert_gen.exe**. Vous êtes alors invité à entrer votre **UDS**, **l’adresse MAC** du DevKit et la **version du microprogramme** pour générer le certificat X.509.
  ![Exécution de dps-cert-gen.exe](./media/how-to-connect-mxchip-iot-devkit/dps-cert-gen.png)

4. Une fois le certificat X.509 généré, un certificat **.pem** est enregistré dans le même dossier.

## <a name="create-a-device-enrollment-entry-in-the-device-provisioning-service"></a>Créer une entrée d’inscription d’appareil dans le service Device Provisioning

1. Dans le Portail Azure, accédez à l’instance du service Device Provisioning. Sélectionnez **Gérer les inscriptions**, puis l’onglet **Inscriptions individuelles**. ![Inscriptions individuelles](./media/how-to-connect-mxchip-iot-devkit/individual-enrollments.png)

2. Sélectionnez **Ajouter**.

3. Dans le panneau « Add enrollment » (Ajouter une inscription) :

   - Sélectionnez **X.509** sous **Mécanisme**.
   - Cliquez sur « Sélectionner un fichier » sous **Certificat principal .pem ou fichier .cer**.
   - Dans la boîte de dialogue Ouvrir un fichier, sélectionnez et chargez le certificat **.pem** que vous venez de générer.
   - Conservez les autres valeurs par défaut, puis cliquez sur **Enregistrer**.

   ![Téléchargement d’un certificat](./media/how-to-connect-mxchip-iot-devkit/upload-cert.png)

  > [!NOTE]
  > Si vous obtenez une erreur avec ce message :
  >
  > `{"message":"BadRequest:{\r\n \"errorCode\": 400004,\r\n \"trackingId\": \"1b82d826-ccb4-4e54-91d3-0b25daee8974\",\r\n \"message\": \"The certificate is not a valid base64 string value\",\r\n \"timestampUtc\": \"2018-05-09T13:52:42.7122256Z\"\r\n}"}`
  >
  > Ouvrez le fichier de certificat **.pem** au format texte (ouvrez-le avec le Bloc-notes ou tout autre éditeur de texte), et supprimez les lignes :
  >
  > `"-----BEGIN CERTIFICATE-----"` et `"-----END CERTIFICATE-----"`.
  >

## <a name="start-the-devkit"></a>Démarrer DevKit

1. Ouvrez VS Code et le moniteur de série.

2. Appuyez sur le bouton de **réinitialisation** de DevKit.

Le DevKit démarre l’inscription auprès du service Device Provisioning.

![Sortie VS Code](./media/how-to-connect-mxchip-iot-devkit/vscode-output.png)

## <a name="verify-that-the-devkit-is-registered-with-azure-iot-hub"></a>Vérifier que le DevKit est enregistré auprès d’Azure IoT Hub

Une fois l’appareil démarré, les actions suivantes se produisent :

1. L’appareil envoie une demande d’enregistrement à votre service Device Provisioning.
2. Le service Device Provisioning retourne une vérification d’inscription à laquelle votre appareil répond.
3. Une fois l’inscription réussie, le service Device Provisioning retourne l’URI d’IoT Hub, l’ID de l’appareil et la clé chiffrée à l’appareil.
4. L’application cliente IoT Hub sur l’appareil peut se connecter à votre hub.
5. Une fois la connexion avec le hub établie, l’appareil s’affiche dans Device Explorer, dans IoT Hub.
  ![Appareil inscrit](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="problems-and-feedback"></a>Problèmes et commentaires

Si vous rencontrez des problèmes, consultez les [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) du MXChip IoT DevKit ou contactez le support via les canaux ci-dessous :

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à inscrire un appareil en toute sécurité dans le service Device Provisioning en utilisant le moteur DICE afin que l’appareil puisse s’enregistrer automatiquement auprès d’Azure IoT Hub. 

En résumé, vous avez découvert comment :

> [!div class="checklist"]
> * configurer le point de terminaison global du service Device Provisioning sur un appareil.
> * utiliser un secret d’appareil unique pour générer un certificat X.509 ;
> * inscrire un appareil particulier ;
> * vérifier que l’appareil est enregistré.

Découvrez comment [Créer et provisionner un appareil simulé](./quick-create-simulated-device.md).

