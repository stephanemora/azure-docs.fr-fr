---
title: Authentifier des appareils en aval - Azure IoT Edge | Microsoft Docs
description: Explique comment authentifier les appareils en aval ou « appareils de nœud terminal » dans IoT Hub, et comment router leur connexion via des appareils de passerelle Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1ca066729a81ff51d87c5d8063c94be86366811c
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548762"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Authentifier un appareil en aval auprès d’Azure IoT Hub

Dans un scénario de passerelle transparente, les appareils en aval (également appelés appareils de nœud terminal ou appareils enfants) ont besoin d’identités dans IoT Hub, comme tous les autres appareils. Cet article décrit les options permettant d’authentifier un appareil en aval dans IoT Hub, puis il montre comment déclarer la connexion de passerelle.

La configuration d’une connexion de passerelle transparente s’effectue en trois grandes étapes. Cet article concerne la deuxième étape :

1. L’appareil de passerelle doit être en mesure de se connecter aux appareils en aval, de recevoir des communications de ces appareils et de router les messages vers la destination appropriée, tout cela de façon sécurisée. Pour plus d’informations, consultez [Configurer un appareil IoT Edge en tant que passerelle transparente](how-to-create-transparent-gateway.md).
2. **L’appareil en aval doit avoir une identité d’appareil pour s’authentifier auprès d’IoT Hub et savoir comment communiquer par le biais de l’appareil de passerelle associé.**
3. L’appareil en aval doit pouvoir se connecter de façon sécurisée à son appareil de passerelle. Pour plus d’informations, voir [Connecter un appareil en aval à une passerelle Azure IoT Edge](how-to-connect-downstream-device.md).

Pour s’authentifier auprès d’IoT Hub, les appareils en aval disposent de trois méthodes d’authentification : par clé symétrique (ou « clé d’accès partagé »), par certificat X.509 autosigné et par certificat X.509 signé par l’autorité de certification. Les étapes d’authentification sont similaires à celles utilisées pour configurer n’importe quel appareil IoT Edge avec IoT Hub, avec toutefois quelques différences au niveau de la déclaration de la relation de passerelle.

Les étapes décrites dans cet article Montrent comment provisionner des appareils manuellement. Elles n’expliquent pas comment effectuer un approvisionnement automatique à l’aide du service Azure IoT Hub Device Provisioning (DPS). L’approvisionnement des appareils en aval avec DPS n’est pas pris en charge.

## <a name="prerequisites"></a>Conditions préalables requises

Suivez les étapes décrites dans [Configurer un appareil IoT Edge en tant que passerelle transparente](how-to-create-transparent-gateway.md). Si vous utilisez l’authentification X.509 pour votre appareil en aval, vous devez utiliser le même script de génération de certificat que celui que vous avez configuré dans l’article relatif à la passerelle transparente.

Cet article fait référence au *nom d’hôte de passerelle* à plusieurs endroits. Le nom d’hôte de passerelle est déclaré avec le paramètre **hostname** dans le fichier config.yaml sur l’appareil de passerelle IoT Edge. Il est référencé dans la chaîne de connexion de l’appareil en aval. Le nom d’hôte de passerelle doit être résolu en une adresse IP, à l’aide du DNS ou d’une entrée de fichier hôte.

## <a name="register-device-symmetric-key"></a>Inscrire l’appareil (clé symétrique)

L’authentification par clé symétrique (ou authentification par clé d’accès partagé) est la méthode la plus simple pour s’authentifier auprès d’IoT Hub. Avec l’authentification par clé symétrique, une clé base64 est associée à l’ID de votre appareil IoT dans IoT Hub. Vous devez inclure cette clé dans vos applications IoT pour que votre appareil puisse la présenter lorsqu’il se connecte à IoT Hub.

### <a name="create-the-device-identity"></a>Créer une identité d’appareil

Ajoutez un nouvel appareil IoT dans votre hub IoT à l’aide du portail Azure, d’Azure CLI ou de l’extension IoT pour Visual Studio Code. N’oubliez pas que les appareils en aval doivent être identifiés dans IoT Hub comme des appareils IoT standard, et non comme des appareils IoT Edge.

Lorsque vous créez une identité d’appareil, fournissez les informations suivantes :

* Créez un ID pour votre appareil.

* Sélectionnez **Clé symétrique** comme type d’authentification.

* Si vous le souhaitez, vous pouvez choisir de **Définir un appareil parent**, puis sélectionner l’appareil de passerelle IoT Edge via lequel cet appareil en aval doit se connecter. Cette étape est facultative pour l’authentification par clé symétrique. Toutefois, elle est recommandée, car la configuration d’un appareil parent permet d’utiliser des [fonctionnalités hors connexion](offline-capabilities.md) sur votre appareil en aval. Vous pourrez ajouter ou modifier les informations concernant le parent ultérieurement.

   ![Créer un ID d’appareil avec une authentification par clé symétrique dans le portail](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

Vous pouvez utiliser l’[extension IoT pour Azure CLI](https://github.com/Azure/azure-iot-cli-extension) afin d’effectuer la même opération. L’exemple suivant crée un appareil IoT avec l’authentification par clé symétrique et affecte un appareil parent :

```cli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

Pour plus d’informations sur les commandes Azure CLI permettant de créer des appareils et de gérer les relations parent/enfant, consultez la documentation de référence des commandes [az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest).


Ensuite, [récupérez et Modifiez la chaîne de connexion](#retrieve-and-modify-connection-string) de sorte que votre appareil sache qu’il se connecte via sa passerelle.

## <a name="register-device-x509-self-signed"></a>Inscrire l’appareil (X. 509 autosigné)

Pour l’authentification par certificat X.509 autosigné, parfois appelée authentification par empreinte numérique, vous devez créer de nouveaux certificats et les placer sur votre appareil IoT. Ces certificats contiennent une empreinte numérique que vous partagez avec IoT Hub pour l’authentification.

Si vous n’avez pas d’autorité de certification pour créer les certificats X.509, vous pouvez [Créer des certificats de démonstration pour tester les fonctionnalités de l’appareil IoT Edge](how-to-create-test-certificates.md). Lors de la génération de certificats de test pour votre appareil en aval, utilisez le même certificat d’autorité de certification racine qui a généré les certificats pour votre appareil de passerelle.

1. Avec votre certificat d’autorité de certification, créez deux certificats d’appareil (un principal et un secondaire) pour l’appareil en aval.

   Sur le certificat d’appareil, le nom du sujet doit être défini sur l’ID d’appareil que vous allez utiliser lors de l’inscription de l’appareil IoT dans IoT Hub. Ce paramètre est requis pour l’authentification.

2. Récupérez l’empreinte digitale SHA1 (ou « empreinte numérique » dans l’interface IoT Hub) de chaque certificat (il s’agit d’une chaîne de 40 caractères hexadécimaux). Pour afficher le certificat et trouver l’empreinte digitale, utilisez la commande openssl suivante :

   ```PowerShell/bash
   openssl x509 -in <primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Exécutez cette commande deux fois, une fois pour le certificat principal et une fois pour le certificat secondaire. Vous fournissez des empreintes digitales pour les deux certificats lorsque vous inscrivez un nouvel appareil IoT à l’aide de certificats X.509 auto-signés.

3. Accédez à votre hub IoT dans le portail Azure et créez une nouvelle identité d’appareil IoT avec les valeurs suivantes :

   * Fournissez l’**ID d’appareil** qui correspond au nom du sujet de vos certificats d’appareil.
   * Sélectionnez **X.509 autosigné** comme type d’authentification.
   * Collez les chaînes hexadécimales que vous avez copiées à partir du certificat primaire et du certificat secondaire de votre appareil.
   * Sélectionnez **Définir un appareil parent**, puis choisissez l’appareil de passerelle IoT Edge via lequel cet appareil en aval doit se connecter. Un appareil parent est nécessaire pour l’authentification X.509 d’un appareil en aval.

   ![Créer un ID d’appareil avec une authentification par certificat X.509 autosigné dans le portail](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. Copiez le certificat et les clés d’appareil dans n’importe quel emplacement sur l’appareil en aval. Déplacez également une copie du certificat d’autorité de certification racine partagée qui a généré le certificat de périphérique d’appareil de passerelle et les certificats d’appareil en aval.

   Vous allez référencer ces fichiers dans les applications d’appareil de nœud terminal qui se connectent à IoT Hub. Vous pouvez utiliser un service comme [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) ou une fonction comme [SCP (Secure Copy Protocol)](https://www.ssh.com/ssh/scp/) pour déplacer les fichiers de certificats.

5. Selon votre langue par défaut, consultez des exemples de référencement de certificats X.509 dans des applications IoT :

   * C# : [Configurer la sécurité X.509 dans votre Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C : [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js : [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java : [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py)
   * Python : [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/advanced-hub-scenarios/send_message_x509.py)

Vous pouvez utiliser l’[extension IoT pour Azure CLI](https://github.com/Azure/azure-iot-cli-extension) afin d’effectuer la même opération de création d’appareil. L’exemple suivant crée un appareil IoT avec l’authentification par certificat X.509 autosigné et affecte un appareil parent :

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Pour plus d’informations sur les commandes Azure CLI permettant de créer des appareils, de générer des certificats et de gérer les relations parent/enfant, consultez la documentation de référence des commandes [az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest).

Ensuite, [récupérez et Modifiez la chaîne de connexion](#retrieve-and-modify-connection-string) de sorte que votre appareil sache qu’il se connecte via sa passerelle.

## <a name="register-device-x509-ca-signed"></a>Inscrire l’appareil (Autorité de certification X. 509 signée)

Pour l’authentification par certificat X.509 signés par l’autorité de certification, vous avez besoin d’un certificat d’autorité de certification racine inscrit dans IoT Hub, que vous utilisez pour signer les certificats de votre appareil IoT. Tous les appareils qui utilisent un certificat émis par le certificat d’autorité de certification racine ou l’un de ses certificats intermédiaires sont autorisés à s’authentifier.

Cette section est basée sur les instructions de l’article IoT Hub [Configurer la sécurité X.509 dans votre Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md). Suivez les étapes décrites dans cette section pour connaître les valeurs à utiliser pour configurer un appareil en aval qui se connecte via une passerelle.

Si vous n’avez pas d’autorité de certification pour créer les certificats X.509, vous pouvez [Créer des certificats de démonstration pour tester les fonctionnalités de l’appareil IoT Edge](how-to-create-test-certificates.md). Lors de la génération de certificats de test pour votre appareil en aval, utilisez le même certificat d’autorité de certification racine qui a généré les certificats pour votre appareil de passerelle.

1. Suivez les instructions de la section [Inscrire des certificats d’autorité de certification X.509 auprès de votre IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) dans l’article *Configurer la sécurité X.509 dans votre Azure IoT Hub*. Dans cette section, vous allez effectuer les étapes suivantes :

   1. Chargez un certificat d’autorité de certification racine. Si vous utilisez les certificats de démonstration, l’autorité de certification racine est **\<chemin d’accès>/certs/azure-iot-test-only.root.ca.cert.pem**.

   2. Vérifiez que vous disposez de votre propre certificat d’autorité de certification racine.

2. Suivez les instructions de la section [Créer un appareil X.509 pour votre IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) dans l’article *Configurer la sécurité X.509 dans votre Azure IoT Hub*. Dans cette section, vous allez effectuer les étapes suivantes :

   1. Ajoutez un nouvel appareil. Dans **ID d’appareil**, fournissez un nom en minuscules, puis choisissez le type d’authentification **Autorité de certification X.509 signée**.
   2. Définissez un appareil parent. Pour les appareils en aval, sélectionnez **Définir un appareil parent**, puis choisissez l’appareil de passerelle IoT Edge qui doit fournir la connexion à IoT Hub.

3. Créez une chaîne de certificats pour votre appareil en aval. Utilisez le certificat d’autorité de certification racine que vous avez chargé dans IoT Hub pour créer cette chaîne. Utilisez l’ID d’appareil en minuscules que vous avez attribué à votre identité d’appareil dans le portail.

4. Copiez le certificat et les clés d’appareil dans n’importe quel emplacement sur l’appareil en aval. Déplacez également une copie du certificat d’autorité de certification racine partagée qui a généré le certificat de périphérique d’appareil de passerelle et les certificats d’appareil en aval.

   Vous allez référencer ces fichiers dans les applications d’appareil de nœud terminal qui se connectent à IoT Hub. Vous pouvez utiliser un service comme [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) ou une fonction comme [SCP (Secure Copy Protocol)](https://www.ssh.com/ssh/scp/) pour déplacer les fichiers de certificats.

5. Selon votre langue par défaut, consultez des exemples de référencement de certificats X.509 dans des applications IoT :

   * C# : [Configurer la sécurité X.509 dans votre Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C : [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js : [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java : [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py)
   * Python : [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/advanced-hub-scenarios/send_message_x509.py)

Vous pouvez utiliser l’[extension IoT pour Azure CLI](https://github.com/Azure/azure-iot-cli-extension) afin d’effectuer la même opération de création d’appareil. L’exemple suivant crée un appareil IoT avec l’authentification par certificat X.509 signé par l’autorité de certification, et affecte un appareil parent :

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Pour plus d’informations, consultez le contenu de référence Azure CLI relatif au commandes [az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest).

Ensuite, [récupérez et Modifiez la chaîne de connexion](#retrieve-and-modify-connection-string) de sorte que votre appareil sache qu’il se connecte via sa passerelle.

## <a name="retrieve-and-modify-connection-string"></a>Récupérer et Modifier la chaîne de connexion

Après avoir créé une identité d’appareil IoT dans le portail, vous pouvez récupérer ses clés primaires ou secondaires. L’une de ces clés doit être incluse dans la chaîne de connexion que les applications utilisent pour communiquer avec IoT Hub. Avec l’authentification par clé symétrique, IoT Hub fournit la chaîne de connexion intégralement formée dans les détails de l’appareil. Vous devez ajouter des informations supplémentaires concernant l’appareil de passerelle dans la chaîne de connexion.

Pour les appareils en aval, les chaînes de connexion ont besoin des composants suivants :

* Le hub IoT auquel l’appareil se connecte : `Hostname={iothub name}.azure-devices.net`
* L’ID d’appareil qui est inscrit auprès du hub : `DeviceID={device ID}`
* La clé primaire ou la clé secondaire : `SharedAccessKey={key}`
* L’appareil de passerelle via lequel l’appareil se connecte. Fournissez la valeur **hostname** qui figure dans le fichier config.yaml de l’appareil de passerelle IoT Edge : `GatewayHostName={gateway hostname}`

Dans son intégralité, une chaîne de connexion ressemble à ceci :

```
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Si vous avez établi une relation parent/enfant pour l’appareil en aval, vous pouvez simplifier la chaîne de connexion en appelant la passerelle directement en tant qu’hôte de connexion. Les relations parent/enfant sont obligatoires pour l’authentification X.509, mais facultatives pour l’authentification par clé symétrique. Par exemple :

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

À ce stade, vous devez disposer d’un appareil IoT Edge inscrit et configuré en tant que passerelle. Vous disposez également d’un appareil IoT en aval inscrit et pointant vers son appareil de passerelle. La dernière étape consiste à placer des certificats sur votre appareil en aval afin qu’il puisse se connecter en toute sécurité à la passerelle.

Passez à l’article suivant de la série de la passerelle, [Connecter un appareil en aval à une passerelle Azure IoT Edge](how-to-connect-downstream-device.md).

## <a name="next-steps"></a>Étapes suivantes

À présent, vous devez disposer d’un appareil IoT Edge qui fonctionne comme une passerelle transparente, ainsi que d’un appareil en aval inscrit dans IoT hub. Maintenant, vous devez configurer vos appareils en aval pour qu’ils approuvent l’appareil de passerelle et s’y connectent en toute sécurité. Pour plus d’informations, voir [Connecter un appareil en aval à une passerelle Azure IoT Edge](how-to-connect-downstream-device.md).
