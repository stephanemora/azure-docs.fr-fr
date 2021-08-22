---
title: Inscrire un nouvel appareil Azure IoT Edge | Microsoft Docs
description: Inscrire un seul appareil IoT Edge dans IoT Hub pour l’approvisionnement manuel avec des clés symétriques ou certificats X.509
author: kgremban
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: c617b4c3558be2409c8bf2272e805a8b8cb9d349
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524153"
---
# <a name="register-an-iot-edge-device-in-iot-hub"></a>Inscrire un appareil IoT Edge dans IoT Hub

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Cet article décrit les étapes à suivre pour inscrire un nouvel appareil IoT Edge dans IoT Hub.

Chaque appareil qui se connecte à un hub IoT possède un ID d’appareil qui sert au suivi des communications cloud-à-appareil ou appareil-à-cloud. Vous configurez un appareil avec ses informations de connexion, qui comprennent le nom d’hôte du hub IoT, l’ID d’appareil ainsi que les informations que l’appareil utilise pour s’authentifier auprès d’IoT Hub.

Les étapes de cet article suivent un processus appelé approvisionnement manuel, qui consiste à connecter un seul appareil à son hub IoT. Avec le provisionnement manuel, vous avez le choix entre deux méthodes d’authentification des appareils IoT Edge :

* **Par clé symétrique** : quand vous créez une identité d’appareil dans IoT Hub, le service crée deux clés. Vous placez l’une des clés sur l’appareil, lequel présente la clé à IoT Hub au moment de l’authentification.

  Cette méthode d’authentification est plus rapide pour commencer, mais moins sûre que l’autre.

* **Par certificat autosigné X.509** : vous créez deux certificats d’identité X.509, que vous placez sur l’appareil. Quand vous créez une identité d’appareil dans IoT Hub, vous fournissez les empreintes numériques des deux certificats. Quand l’appareil s’authentifie auprès de IoT Hub, il présente un certificat et IoT Hub vérifie que le certificat correspond à son empreinte numérique.

  Cette méthode d’authentification étant plus sécurisée, elle est recommandée dans les scénarios de production.

Cet article présente les deux méthodes d’authentification.

Si vous avez de nombreux appareils à configurer et que vous ne souhaitez pas les approvisionner manuellement, consultez l’un des articles suivants pour découvrir comment IoT Edge fonctionne avec le service IoT Hub Device Provisioning :

* [Créer et approvisionner des appareils IoT Edge à l’aide de certificats X.509](how-to-auto-provision-x509-certs.md)
* [Créer et approvisionner des appareils IoT Edge à l’aide de TPM](how-to-auto-provision-simulated-device-linux.md)
* [Créer et approvisionner des appareils IoT Edge à l’aide de clés symétriques](how-to-auto-provision-symmetric-keys.md)

## <a name="prerequisites"></a>Prérequis

# <a name="portal"></a>[Portail](#tab/azure-portal)

Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) gratuit ou standard dans votre abonnement Azure.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) gratuit ou standard dans votre abonnement Azure
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) pour Visual Studio Code.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* Un [hub IoT](../iot-hub/iot-hub-create-using-cli.md) gratuit ou standard dans votre abonnement Azure.
* [Azure CLI](/cli/azure/install-azure-cli) dans votre environnement. Vous devez utiliser Azure CLI version 2.0.70 ou ultérieure. Utilisez `az --version` pour valider. Cette version prend en charge les commandes d’extension az et introduit l’infrastructure de la commande Knack.

---

## <a name="option-1-register-with-symmetric-keys"></a>Option 1 : Enregistrer avec des clés symétriques

Selon vos préférences, vous avez le choix entre plusieurs outils pour inscrire un nouvel appareil IoT Edge dans IoT Hub et récupérer sa chaîne de connexion.

# <a name="portal"></a>[Portail](#tab/azure-portal)

Dans votre hub IoT dans le portail Azure, les appareils IoT Edge sont créés et managés séparément des appareils IoT qui ne sont pas compatibles avec Edge.

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre IoT Hub.

1. Dans le volet de gauche, sélectionnez **IoT Edge** dans le menu, puis sélectionnez **Ajouter un appareil IoT Edge**.

   ![Ajouter un appareil IoT Edge à partir du portail Azure](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. Dans la page **Créer un appareil**, fournissez les informations suivantes :

   * Créez un ID d’appareil descriptif.
   * Sélectionnez **Clé symétrique** comme type d’authentification.
   * Utilisez les paramètres par défaut pour générer automatiquement les clés d’authentification et connecter le nouvel appareil à votre hub.

1. Sélectionnez **Enregistrer**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="sign-in-to-access-your-iot-hub"></a>Se connecter pour accéder à votre hub ioT

Vous pouvez utiliser les extensions Azure IoT pour Visual Studio Code afin d’effectuer des opérations avec votre hub IoT. Pour que ces opérations fonctionnent, vous devez vous connecter à votre compte Azure et sélectionner votre hub.

1. Dans Visual Studio Code, ouvrez la vue **Explorateur**.
1. Au bas de l’Explorateur, développez la section **Azure IoT Hub**.

   ![Développer la section Appareils Azure IoT Hub](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Cliquez sur **...** dans l’en-tête de section **Azure IoT Hub**. Si vous ne voyez pas les points de suspension, cliquez ou pointez sur l’en-tête.
1. Choisissez **Sélectionner un hub IoT**.
1. Si vous n’êtes pas connecté à votre compte Azure, suivez les invites de connexion.
1. Sélectionnez votre abonnement Azure.
1. Sélectionnez votre hub IoT.

### <a name="register-a-new-device-with-visual-studio-code"></a>Inscrire un nouvel appareil avec Visual Studio Code

1. Dans l’explorateur Visual Studio Code, développez la section **Azure IoT Hub**.
1. Cliquez sur **...** dans l’en-tête de section **Azure IoT Hub**. Si vous ne voyez pas les points de suspension, cliquez ou pointez sur l’en-tête.
1. Sélectionnez **Créer un appareil IoT Edge**.
1. Dans la zone de texte qui s’ouvre, donnez un ID à votre appareil.

Dans l’écran de sortie, vous voyez le résultat de la commande. Les informations de l’appareil apparaissent, notamment l’ID d’appareil (**deviceId**) que vous avez fourni et la chaîne de connexion (**connectionString**) qui vous permet de connecter votre appareil physique à votre hub IoT.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Utilisez la commande [az iot hub device-identity create](/cli/azure/iot/hub/device-identity) pour créer une identité d’appareil dans votre hub IoT. Par exemple :

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Cette commande inclut trois paramètres :

* `--device-id` ou `-d` : Fournissez un nom descriptif unique dans votre hub IoT.
* `--hub-name` ou `-n` : indiquez le nom de votre hub IoT.
* `--edge-enabled` ou `--ee` : déclarez que l’appareil est un appareil IoT Edge.

   ![az iot hub device-identity create output](./media/how-to-register-device/create-edge-device-cli.png)

---

Maintenant que vous avez un appareil inscrit dans IoT Hub, récupérez la chaîne de connexion que vous utilisez pour terminer l’installation et l’approvisionnement du runtime IoT Edge. Suivez les étapes décrites plus loin dans cet article pour [Afficher les appareils inscrits et récupérer les chaînes de connexion](#view-registered-devices-and-retrieve-connection-strings).

## <a name="option-2-register-with-x509-certificates"></a>Option n°2 : Inscrire avec des certificats X.509

L’approvisionnement manuel avec des certificats X.509 requiert IoT Edge version 1.0.10 ou ultérieure.

Pour l’authentification par certificat X.509, les informations d’authentification de chaque appareil sont fournies sous la forme d’*empreintes numériques* extraites de vos certificats d’identité d’appareil. Ces empreintes numériques, fournies à IoT Hub au moment de l’inscription de l’appareil, permettent au service de reconnaître l’appareil qui se connecte.

### <a name="create-certificates-and-thumbprints"></a>Créer des certificats et des empreintes numériques

Lorsque vous configurez un appareil IoT Edge avec des certificats X.509, vous utilisez ce que l’on appelle un *certificat d’identité d’appareil*. Ce certificat est utilisé uniquement pour approvisionner un appareil IoT Edge et l’authentifier avec Azure IoT Hub. Il s’agit d’un certificat feuille qui ne signe pas d’autres certificats. Le certificat d’identité d’appareil est distinct des certificats d’autorité de certification que l’appareil IoT Edge présente aux modules ou aux appareils en aval à des fins de vérification. Pour plus d’informations sur l’utilisation des certificats d’autorité de certification dans les appareils IoT Edge, consultez [Comprendre comment Azure IoT Edge utilise les certificats](iot-edge-certs.md).

Les fichiers suivants sont nécessaires pour le provisionnement manuel avec X.509 :

* Deux des certificats d’identité d’appareil avec leurs certificats de clé privée correspondants au format .cer ou .pem.

  Un ensemble de fichiers de certificat/clé est fourni au runtime IoT Edge. Quand vous créez des certificats d’identité d’appareil, définissez le nom commun du certificat (CN) avec l’ID d’appareil que l’appareil doit avoir dans votre hub IoT.

* Empreintes numériques tirées des deux certificats d’identité d’appareil.

  Les valeurs d’empreinte numérique sont constituées de 40 caractères hexadécimaux pour les hachages SHA-1 ou de 64 caractères hexadécimaux pour les hachages SHA-256. Les deux empreintes numériques sont fournies pour IoT Hub lors de l’enregistrement de l’appareil.

Si vous n’avez pas de certificats disponibles, vous pouvez [créer des certificats de démonstration pour tester les fonctionnalités de l’appareil IoT Edge](how-to-create-test-certificates.md). Suivez les instructions de cet article pour configurer des scripts de création de certificat, créer un certificat d’autorité de certification racine, puis créer deux certificats d’identité d’appareil IoT Edge.

L’une des méthodes permettant de récupérer l’empreinte numérique d’un certificat consiste à utiliser la commande openssl suivante :

```cmd
openssl x509 -in <certificate filename>.pem -text -fingerprint
```

### <a name="register-a-new-device"></a>Inscrire un nouvel appareil

Vous avez le choix entre plusieurs outils pour inscrire un nouvel appareil IoT Edge dans IoT Hub et charger ses empreintes numériques de certificat.

# <a name="portal"></a>[Portail](#tab/azure-portal)

Dans votre hub IoT dans le portail Azure, les appareils IoT Edge sont créés et managés séparément des appareils IoT qui ne sont pas compatibles avec Edge.

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre IoT Hub.

1. Dans le volet de gauche, sélectionnez **IoT Edge** dans le menu, puis sélectionnez **Ajouter un appareil IoT Edge**.

   ![Ajouter un appareil IoT Edge à partir du portail Azure](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. Dans la page **Créer un appareil**, fournissez les informations suivantes :

   * Créez un ID d’appareil descriptif. Notez cet ID d’appareil, car vous en aurez besoin dans la prochaine section.
   * Sélectionnez **X.509 autosigné** comme type d’authentification.
   * Fournissez les empreintes numériques des certificats d’identité principal et secondaire. Les valeurs d’empreinte numérique sont constituées de 40 caractères hexadécimaux pour les hachages SHA-1 ou de 64 caractères hexadécimaux pour les hachages SHA-256.

1. Sélectionnez **Enregistrer**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Actuellement, l’extension Azure IoT pour Visual Studio Code ne prend pas en charge l’inscription des appareils avec des certificats X.509.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Utilisez la commande [az iot hub device-identity create](/cli/azure/iot/hub/device-identity) pour créer une identité d’appareil dans votre hub IoT. Par exemple :

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled --auth-method x509_thumbprint --primary-thumbprint [SHA thumbprint] --secondary-thumbprint [SHA thumbprint]
   ```

Cette commande comprend plusieurs paramètres :

* `--device-id` ou `-d` : fournissez un nom descriptif unique à votre hub IoT. Notez cet ID d’appareil, car vous en aurez besoin dans la prochaine section.
* `hub-name` ou `-n` : indiquez le nom de votre hub IoT.
* `--edge-enabled` ou `--ee` : déclarez que l’appareil est un appareil IoT Edge.
* `--auth-method` ou `--am` : déclarez le type d’autorisation que l’appareil utilisera. Ici, nous utilisons des empreintes numériques de certificat X.509.
* `--primary-thumbprint` ou `--ptp` : fournissez une empreinte numérique de certificat X.509 à utiliser comme clé primaire.
* `--secondary-thumbprint` ou `--stp` : fournissez une empreinte numérique de certificat X.509 à utiliser comme clé secondaire.

---

Maintenant que vous avez un appareil inscrit dans IoT Hub, vous êtes prêt à installer et approvisionner le runtime IoT Edge sur votre appareil. Les appareils IoT Edge qui s’authentifient avec des certificats X.509 n’utilisent pas de chaînes de connexion, vous pouvez donc passer à l’étape suivante :

* [Installer ou désinstaller Azure IoT Edge pour Linux](how-to-install-iot-edge.md)
* [Installer ou désinstaller Azure IoT Edge pour Windows](how-to-install-iot-edge-windows-on-windows.md)

## <a name="view-registered-devices-and-retrieve-connection-strings"></a>Afficher les appareils inscrits et récupérer les chaînes de connexion

Les appareils qui utilisent l’authentification de clé symétrique ont besoin de leurs chaînes de connexion pour terminer l’installation et l’approvisionnement du runtime IoT Edge.

Les appareils qui utilisent l’authentification par certificat X.509 n’ont pas besoin de chaînes de connexion. Au lieu de cela, ces appareils ont besoin du nom de hub IoT, du nom de l’appareil et des fichiers de certificat pour terminer l’installation et l’approvisionnement du runtime IoT Edge.

# <a name="portal"></a>[Portail](#tab/azure-portal)

Tous les appareils compatibles avec Edge qui se connectent à votre hub IoT sont répertoriés dans la page **IoT Edge**.

![Utiliser le portail Azure pour voir tous les appareils IoT Edge dans votre hub IoT](./media/how-to-register-device/portal-view-devices.png)

Pour configurer votre appareil, vous avez besoin de la chaîne de connexion qui établit un lien entre votre appareil physique et son identité dans le hub IoT.

Les appareils qui s’authentifient avec des clés symétriques disposent des chaînes de connexion pouvant être copiées dans le portail.

1. Dans la page **IoT Edge** du portail, cliquez sur l’ID de l’appareil dans la liste des appareils IoT Edge.
2. Copiez la valeur de **Chaîne de connexion principale** ou **Chaîne de connexion secondaire**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Afficher des appareils IoT Edge avec Visual Studio Code

Tous les appareils qui se connectent à votre hub IoT sont listés dans la section **Azure IoT Hub** de l’Explorateur Visual Studio Code. Les appareils IoT Edge se distinguent des appareils non Edge par leur icône et par le fait que les modules **$edgeAgent** et **$edgeHub** sont développés sur chacun d’eux.

![Utiliser VS Code pour voir tous les appareils IoT Edge dans votre hub IoT](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Récupérer la chaîne de connexion avec Visual Studio Code

Pour configurer votre appareil, vous avez besoin de la chaîne de connexion qui établit un lien entre votre appareil physique et son identité dans le hub IoT.

1. Cliquez avec le bouton droit sur l’ID de votre appareil dans la section **Azure IoT Hub**.
1. Sélectionnez **Copier la chaîne de connexion de l’appareil**.

   La chaîne de connexion est copiée dans le Presse-papiers.

Vous pouvez également sélectionner **Obtenir les informations de l’appareil** dans le menu contextuel pour voir les informations de l’appareil, notamment la chaîne de connexion, dans la fenêtre de sortie.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Affichez des appareils IoT Edge avec Azure CLI

Utilisez la commande [az iot hub device-identity list](/cli/azure/iot/hub/device-identity) pour voir tous les appareils dans votre hub IoT. Par exemple :

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Tout appareil inscrit en tant qu’appareil IoT Edge a la propriété **capabilities.iotEdge** définie sur **true**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Récupérer la chaîne de connexion avec Azure CLI

Pour configurer votre appareil, vous avez besoin de la chaîne de connexion qui établit un lien entre votre appareil physique et son identité dans le hub IoT. Utilisez la commande [az iot hub device-identity connection-string show](/cli/azure/iot/hub/device-identity/connection-string) pour retourner la chaîne de connexion d’un appareil unique :

   ```azurecli
   az iot hub device-identity connection-string show --device-id [device id] --hub-name [hub name]
   ```

>[!TIP]
>La commande `connection-string show` a été introduite dans la version 0.9.8 de l’extension Azure IoT, remplaçant la commande `show-connection-string` déconseillée. Si une erreur survient lors de l’exécution de cette commande, assurez-vous que votre version de l’extension est mise à jour vers 0.9.8 ou une version ultérieure. Pour plus d’informations et pour connaître les dernières mises à jour, consultez [Extension Microsoft Azure IoT pour Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

La valeur du paramètre `device-id` respecte la casse.

Quand vous copiez la chaîne de connexion à utiliser sur un appareil, n’incluez pas les guillemets autour de la chaîne de connexion.

---

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez un appareil inscrit dans IoT Hub, vous êtes prêt à installer et approvisionner le runtime IoT Edge sur votre appareil.

* [Installer ou désinstaller Azure IoT Edge pour Linux](how-to-install-iot-edge.md)
* [Installer ou désinstaller Azure IoT Edge pour Windows](how-to-install-iot-edge-windows-on-windows.md)