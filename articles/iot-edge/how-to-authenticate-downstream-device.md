---
title: Authentifier des appareils en aval - Azure IoT Edge | Microsoft Docs
description: Explique comment authentifier les appareils en aval ou « appareils de nœud terminal » dans IoT Hub, et comment router leur connexion via des appareils de passerelle Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 1634d7cd3dfe8d118e220fa8620ef6467c15ea2c
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69983018"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Authentifier un appareil en aval auprès d’Azure IoT Hub

Dans un scénario de passerelle transparente, les appareils en aval (également appelés appareils de nœud terminal ou appareils enfants) ont besoin d’identités dans IoT Hub, comme tous les autres appareils. Cet article décrit les options permettant d’authentifier un appareil en aval dans IoT Hub, puis il montre comment déclarer la connexion de passerelle.

La configuration d’une connexion de passerelle transparente s’effectue en trois grandes étapes. Cet article concerne la deuxième étape :

1. L’appareil de passerelle doit être en mesure de se connecter aux appareils en aval, de recevoir des communications de ces appareils et de router les messages vers la destination appropriée, tout cela de façon sécurisée. Pour plus d’informations, consultez [Configurer un appareil IoT Edge en tant que passerelle transparente](how-to-create-transparent-gateway.md).
2. **L’appareil en aval doit avoir une identité d’appareil pour s’authentifier auprès d’IoT Hub et savoir comment communiquer par le biais de l’appareil de passerelle associé.**
3. L’appareil en aval doit pouvoir se connecter de façon sécurisée à son appareil de passerelle. Pour plus d’informations, voir [Connecter un appareil en aval à une passerelle Azure IoT Edge](how-to-connect-downstream-device.md).

Pour s’authentifier auprès d’IoT Hub, les appareils en aval disposent de trois méthodes d’authentification : par clé symétrique (ou « clé d’accès partagé »), par certificat X.509 autosigné et par certificat X.509 signé par l’autorité de certification. Les étapes d’authentification sont similaires à celles utilisées pour configurer n’importe quel appareil IoT Edge avec IoT Hub, avec toutefois quelques différences au niveau de la déclaration de la relation de passerelle.

Les étapes décrites dans cet article montrent comment provisionner manuellement les appareils. Elles n’expliquent pas comment effectuer un provisionnement automatique à l’aide du service Azure IoT Hub Device Provisioning. 

## <a name="prerequisites"></a>Prérequis

Suivez les étapes décrites dans [Configurer un appareil IoT Edge en tant que passerelle transparente](how-to-create-transparent-gateway.md).

Cet article fait référence au *nom d’hôte de passerelle* à plusieurs endroits. Le nom d’hôte de passerelle est déclaré avec le paramètre **hostname** dans le fichier config.yaml sur l’appareil de passerelle IoT Edge. Il est utilisé pour créer les certificats dans cet article et est référencé dans la chaîne de connexion des appareils en aval. Le nom d’hôte de passerelle doit être résolu en une adresse IP, à l’aide du DNS ou d’une entrée de fichier hôte.

## <a name="symmetric-key-authentication"></a>Authentification par clé symétrique

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
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID}
```

Pour plus d’informations sur les commandes Azure CLI permettant de créer des appareils et de gérer les relations parent/enfant, consultez la documentation de référence des commandes [az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest).

### <a name="connect-to-iot-hub-through-a-gateway"></a>Se connecter à IoT Hub via une passerelle

Le processus qui est utilisé pour authentifier les appareils IoT habituels auprès d’IoT Hub avec des clés symétriques est le même que celui qui est utilisé pour les appareils en aval. La seule différence est que vous devez ajouter un pointeur vers l’appareil de passerelle pour router la connexion ou, dans les scénarios hors connexion, pour gérer l’authentification pour le compte d’IoT Hub. 

Pour l’authentification par clé symétrique, aucune étape supplémentaire n’est nécessaire pour permettre à votre appareil de s’authentifier auprès d’IoT Hub. Vous avez toujours besoin de certificats pour que votre appareil en aval puisse se connecter à son appareil de passerelle, comme décrit dans [Connecter un appareil en aval à une passerelle Azure IoT Edge](how-to-connect-downstream-device.md).

Après avoir créé une identité d’appareil IoT dans le portail, vous pouvez récupérer ses clés primaires ou secondaires. L’une de ces clés doit être incluse dans la chaîne de connexion que vous ajoutez à l’application qui communique avec IoT Hub. Avec l’authentification par clé symétrique, IoT Hub fournit la chaîne de connexion intégralement formée dans les détails de l’appareil. Vous devez ajouter des informations supplémentaires concernant l’appareil de passerelle dans la chaîne de connexion. 

Pour les appareils en aval, les chaînes de connexion de clé symétrique ont besoin des composants suivants : 

* Le hub IoT auquel l’appareil se connecte : `Hostname={iothub name}.azure-devices.net`
* L’ID d’appareil qui est inscrit auprès du hub : `DeviceID={device ID}`
* La clé primaire ou la clé secondaire : `SharedAccessKey={key}`
* L’appareil de passerelle via lequel l’appareil se connecte. Fournissez la valeur **hostname** qui figure dans le fichier config.yaml de l’appareil de passerelle IoT Edge : `GatewayHostName={gateway hostname}`

Dans son intégralité, une chaîne de connexion ressemble à ceci :

``` 
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Si vous avez établi une relation parent/enfant pour l’appareil en aval, vous pouvez simplifier la chaîne de connexion en appelant la passerelle directement en tant qu’hôte de connexion. Par exemple : 

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

## <a name="x509-authentication"></a>Authentification X.509 

Il existe deux manières d’authentifier un appareil IoT à l’aide de certificats X.509. Quelle que soit la méthode d’authentification choisie, les étapes permettant de connecter votre appareil à IoT Hub sont les mêmes. Choisissez des certificats autosignés ou signés par l’autorité de certification pour l’authentification, puis lisez comment vous connecter à IoT Hub. 

Pour plus d’informations sur la façon dont IoT Hub utilise l’authentification X.509, consultez les articles suivants : 
* [Authentification des appareils à l’aide de certificats d’autorité de certification X.509](../iot-hub/iot-hub-x509ca-overview.md)
* [Informations conceptuelles sur les certificats X.509 signés par l’autorité de certification dans le secteur IoT](../iot-hub/iot-hub-x509ca-concept.md)

### <a name="create-the-device-identity-with-x509-self-signed-certificates"></a>Créer l’identité de l’appareil avec des certificats X.509 autosignés

Pour l’authentification par certificat X.509 autosigné, parfois appelée authentification par empreinte numérique, vous devez créer de nouveaux certificats et les placer sur votre appareil IoT. Ces certificats contiennent une empreinte numérique que vous partagez avec IoT Hub pour l’authentification. 

Pour tester ce scénario, le plus simple est d’utiliser le même ordinateur que celui que vous avez utilisé pour créer les certificats dans [Configurer un appareil IoT Edge en tant que passerelle transparente](how-to-create-transparent-gateway.md). Cet ordinateur doit déjà être configuré avec l’outil, le certificat d’autorité de certification racine et le certificat d’autorité de certification intermédiaire nécessaires à la création des certificats d’appareils IoT. Vous pourrez ensuite copier les certificats finaux et leurs clés privées sur votre appareil en aval. En suivant les étapes de l’article sur la passerelle, vous avez configuré openssl sur votre ordinateur, puis vous avez cloné le référentiel IoT Edge pour accéder aux scripts de création des certificats. Ensuite, vous avez créé un répertoire de travail nommé **\<WRKDIR>** dans lequel stocker les certificats. Les certificats par défaut sont destinés au développement et aux tests, et expirent donc au bout de 30 jours. Vous devez avoir créé un certificat d’autorité de certification racine et un certificat intermédiaire. 

1. Accédez à votre répertoire de travail à partir d’un interpréteur de commandes ou d’une fenêtre PowerShell. 

2. Créez deux certificats pour l’appareil en aval (un primaire et un secondaire). Fournissez le nom de votre appareil, puis l’étiquette primaire ou secondaire. Ces informations sont utilisées pour nommer les fichiers afin que vous puissiez effectuer le suivi des certificats sur plusieurs appareils. 

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

3. Récupérez l’empreinte digitale SHA1 (ou « empreinte numérique » dans l’interface IoT Hub) de chaque certificat (il s’agit d’une chaîne de 40 caractères hexadécimaux). Pour afficher le certificat et trouver l’empreinte digitale, utilisez la commande openssl suivante :

   ```PowerShell/bash
   openssl x509 -in <WORKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

4. Accédez à votre hub IoT dans le portail Azure et créez une nouvelle identité d’appareil IoT avec les valeurs suivantes : 

   * Sélectionnez **X.509 autosigné** comme type d’authentification.
   * Collez les chaînes hexadécimales que vous avez copiées à partir du certificat primaire et du certificat secondaire de votre appareil.
   * Sélectionnez **Définir un appareil parent**, puis choisissez l’appareil de passerelle IoT Edge via lequel cet appareil en aval doit se connecter. Un appareil parent est nécessaire pour l’authentification X.509 d’un appareil en aval. 

   ![Créer un ID d’appareil avec une authentification par certificat X.509 autosigné dans le portail](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

5. Copiez les fichiers suivants dans l’un des répertoires de votre appareil en aval :

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device name>*.key.pem`

   Vous allez référencer ces fichiers dans les applications d’appareil de nœud terminal qui se connectent à IoT Hub. Vous pouvez utiliser un service comme [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) ou une fonction comme [SCP (Secure Copy Protocol)](https://www.ssh.com/ssh/scp/) pour déplacer les fichiers de certificats.

Vous pouvez utiliser l’[extension IoT pour Azure CLI](https://github.com/Azure/azure-iot-cli-extension) afin d’effectuer la même opération de création d’appareil. L’exemple suivant crée un appareil IoT avec l’authentification par certificat X.509 autosigné et affecte un appareil parent : 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Pour plus d’informations sur les commandes Azure CLI permettant de créer des appareils, de générer des certificats et de gérer les relations parent/enfant, consultez la documentation de référence des commandes [az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest).

### <a name="create-the-device-identity-with-x509-ca-signed-certificates"></a>Créer l’identité de l’appareil avec des certificats X.509 signés par l’autorité de certification

Pour l’authentification par certificat X.509 signés par l’autorité de certification, vous avez besoin d’un certificat d’autorité de certification racine inscrit dans IoT Hub, que vous utilisez pour signer les certificats de votre appareil IoT. Tous les appareils qui utilisent un certificat émis par le certificat d’autorité de certification racine ou l’un de ses certificats intermédiaires sont autorisés à s’authentifier. 

Cette section est basée sur les instructions de l’article IoT Hub [Configurer la sécurité X.509 dans votre Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md). Suivez les étapes décrites dans cette section pour connaître les valeurs à utiliser pour configurer un appareil en aval qui se connecte via une passerelle. 

Pour tester ce scénario, le plus simple est d’utiliser le même ordinateur que celui que vous avez utilisé pour créer les certificats dans [Configurer un appareil IoT Edge en tant que passerelle transparente](how-to-create-transparent-gateway.md). Cet ordinateur doit déjà être configuré avec l’outil, le certificat d’autorité de certification racine et le certificat d’autorité de certification intermédiaire nécessaires à la création des certificats d’appareils IoT. Vous pourrez ensuite copier les certificats finaux et leurs clés privées sur votre appareil en aval. En suivant les étapes de l’article sur la passerelle, vous avez configuré openssl sur votre ordinateur, puis vous avez cloné le référentiel IoT Edge pour accéder aux scripts de création des certificats. Ensuite, vous avez créé un répertoire de travail nommé **\<WRKDIR>** dans lequel stocker les certificats. Les certificats par défaut sont destinés au développement et aux tests, et expirent donc au bout de 30 jours. Vous devez avoir créé un certificat d’autorité de certification racine et un certificat intermédiaire. 

1. Suivez les instructions de la section [Inscrire des certificats d’autorité de certification X.509 auprès de votre IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) dans l’article *Configurer la sécurité X.509 dans votre Azure IoT Hub*. Dans cette section, vous allez effectuer les étapes suivantes : 

   1. Chargez un certificat d’autorité de certification racine. Si vous utilisez des certificats que vous avez créés dans l’article sur la passerelle transparente, chargez **\<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem** comme fichier de certificat racine. 
   2. Vérifiez que vous disposez de votre propre certificat d’autorité de certification racine. Vous pouvez le vérifier à l’aide des outils de certificat dans \<WRKDIR>. 

      ```powershell
      New-CACertsVerificationCert "<verification code from Azure portal>"
      ```

      ```bash
      ./certGen.sh create_verification_certificate <verification code from Azure portal>"
      ```

2. Suivez les instructions de la section [Créer un appareil X.509 pour votre IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) dans l’article *Configurer la sécurité X.509 dans votre Azure IoT Hub*. Dans cette section, vous allez effectuer les étapes suivantes : 

   1. Ajoutez un nouvel appareil. Dans **ID d’appareil**, fournissez un nom en minuscules, puis choisissez le type d’authentification **Autorité de certification X.509 signée**. 
   2. Définissez un appareil parent. Pour les appareils en aval, sélectionnez **Définir un appareil parent**, puis choisissez l’appareil de passerelle IoT Edge qui doit fournir la connexion à IoT Hub. 

3. Créez une chaîne de certificats pour votre appareil en aval. Utilisez le certificat d’autorité de certification racine que vous avez chargé dans IoT Hub pour créer cette chaîne. Utilisez l’ID d’appareil en minuscules que vous avez attribué à votre identité d’appareil dans le portail.

   ```powershell
   New-CACertsDevice "<device id>"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

4. Copiez les fichiers suivants dans l’un des répertoires de votre appareil en aval : 

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device id>*.key.pem`

   Vous allez référencer ces fichiers dans les applications d’appareil de nœud terminal qui se connectent à IoT Hub. Vous pouvez utiliser un service comme [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) ou une fonction comme [SCP (Secure Copy Protocol)](https://www.ssh.com/ssh/scp/) pour déplacer les fichiers de certificats.

Vous pouvez utiliser l’[extension IoT pour Azure CLI](https://github.com/Azure/azure-iot-cli-extension) afin d’effectuer la même opération de création d’appareil. L’exemple suivant crée un appareil IoT avec l’authentification par certificat X.509 signé par l’autorité de certification, et affecte un appareil parent : 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Pour plus d’informations sur les commandes Azure CLI permettant de créer des appareils et de gérer les relations parent/enfant, consultez la documentation de référence des commandes [az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest).


### <a name="connect-to-iot-hub-through-a-gateway"></a>Se connecter à IoT Hub via une passerelle

Chaque SDK Azure IoT gère l’authentification X.509 de manière légèrement différente. Toutefois, le processus qui est utilisé pour authentifier les appareils IoT habituels auprès d’IoT Hub avec des certificats X.509 est le même que celui qui est utilisé pour les appareils en aval. La seule différence est que vous devez ajouter un pointeur vers l’appareil de passerelle pour router la connexion ou, dans les scénarios hors connexion, pour gérer l’authentification pour le compte d’IoT Hub. En règle générale, vous pouvez suivre les mêmes étapes d’authentification X.509 pour tous les appareils IoT Hub, puis remplacer la valeur **Hostname** de la chaîne de connexion par le nom d’hôte de votre appareil de passerelle. 

Les sections suivantes présentent quelques exemples pour différents langages de SDK. 

>[!IMPORTANT]
>Les exemples suivants montrent comment les SDK IoT Hub utilisent des certificats pour authentifier les appareils. Dans un déploiement de production, vous devez stocker tous les secrets, tels que les clés de signature d’accès partagé et les clés privées, dans un module de sécurité matériel (HSM). 

#### <a name="net"></a>.NET

Pour obtenir un exemple de programme C# qui s’authentifie auprès d’IoT Hub avec des certificats X.509, consultez [Configurer la sécurité X.509 dans votre Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates). Certaines des lignes les plus importantes de cet exemple sont incluses ici pour illustrer le processus d’authentification.

Lorsque vous déclarez le nom d’hôte de votre instance DeviceClient, utilisez le nom d’hôte de l’appareil de passerelle IoT Edge. Le nom d’hôte se trouve dans le fichier config.yaml de l’appareil de passerelle. 

Si vous utilisez les certificats de test fournis par le dépôt Git IoT Edge, la clé des certificats est **1234**.

```csharp
try
{
    var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "1234");
    var auth = new DeviceAuthenticationWithX509Certificate("<device-id>", cert);
    var deviceClient = DeviceClient.Create("<gateway hostname>", auth, TransportType.Amqp_Tcp_Only);

    if (deviceClient == null)
    {
        Console.WriteLine("Failed to create DeviceClient!");
    }
    else
    {
        Console.WriteLine("Successfully created DeviceClient!");
        SendEvent(deviceClient).Wait();
    }

    Console.WriteLine("Exiting...\n");
}
catch (Exception ex)
{
    Console.WriteLine("Error in sample: {0}", ex.Message);
}
```

#### <a name="c"></a>C

Pour obtenir un exemple de programme C s’authentifiant auprès d’IoT Hub avec des certificats X.509, consultez l’exemple [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample) du SDK IoT pour le langage C. Certaines des lignes les plus importantes de cet exemple sont incluses ici pour illustrer le processus d’authentification.

Lorsque vous définissez la chaîne de connexion pour votre appareil en aval, utilisez le nom d’hôte de l’appareil de passerelle IoT Edge pour le paramètre **HostName**. Le nom d’hôte se trouve dans le fichier config.yaml de l’appareil de passerelle. 

```C
// If your downstream device uses X.509 authentication (self signed or X.509 CA) then
// resulting connection string should look like the following:
// "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
static const char* connectionString = "[Downstream device IoT Edge connection string]";

// Path to the Edge "owner" root CA certificate
static const char* edge_ca_cert_path = "[Path to root CA certificate]";

// When the downstream device uses X.509 authentication, a certificate and key 
// in PRM format must be provided.
static const char * x509_device_cert_path = "[Path to primary or secondary device cert]";
static const char * x509_device_key_path = "[Path to primary or secondary device key]";

int main(void)
{
    // Create the iothub handle here
    device_handle = IoTHubDeviceClient_CreateFromConnectionString(connectionString, protocol);

    // Provide the Azure IoT device client with the same root
    // X509 CA certificate that was used to set up the IoT Edge gateway runtime
    if (edge_ca_cert_path != NULL)
    {
        cert_string = obtain_edge_ca_certificate();
        (void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
    }

    if ((x509_device_cert_path != NULL) && (x509_device_key_path != NULL))
    {
        const char *x509certificate = obtain_file_contents(x509_device_cert_path);
        const char *x509privatekey = obtain_file_contents(x509_device_key_path);
        if ((IoTHubDeviceClient_SetOption(device_handle, OPTION_X509_CERT, x509certificate) != IOTHUB_CLIENT_OK) ||
            (IoTHubDeviceClient_SetOption(device_handle, OPTION_X509_PRIVATE_KEY, x509privatekey) != IOTHUB_CLIENT_OK)
            )
        {
            printf("failure to set options for x509, aborting\r\n");
            exit(1);
        }
    }
}
```

#### <a name="nodejs"></a>Node.js

Pour obtenir un exemple de programme Node.js s’authentifiant auprès d’IoT Hub avec des certificats X.509, consultez l’exemple [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js) du SDK IoT pour le langage Node.js. Certaines des lignes les plus importantes de cet exemple sont incluses ici pour illustrer le processus d’authentification.

Lorsque vous définissez la chaîne de connexion pour votre appareil en aval, utilisez le nom d’hôte de l’appareil de passerelle IoT Edge pour le paramètre **HostName**. Le nom d’hôte se trouve dans le fichier config.yaml de l’appareil de passerelle. 

Si vous utilisez les certificats de test fournis par le dépôt Git IoT Edge, la clé des certificats est **1234**.

```node
// String containing Hostname and Device Id in the following format:
//  "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
var connectionString = '<DEVICE CONNECTION STRING WITH x509=true>';
var certFile = '<PATH-TO-CERTIFICATE-FILE>';
var keyFile = '<PATH-TO-KEY-FILE>';
var passphrase = '<KEY PASSPHRASE IF ANY>';

// fromConnectionString must specify a transport constructor, coming from any transport package.
var client = Client.fromConnectionString(connectionString, Protocol);

var options = {
   cert : fs.readFileSync(certFile, 'utf-8').toString(),
   key : fs.readFileSync(keyFile, 'utf-8').toString(),
   passphrase: passphrase
 };

// Calling setOptions with the x509 certificate and key (and optionally, passphrase) will configure the client transport to use x509 when connecting to IoT Hub
client.setOptions(options);
```

#### <a name="python"></a>Python

Pour obtenir un exemple de programme Python s’authentifiant auprès d’IoT Hub avec des certificats X.509, consultez l’exemple [iothub_client_sample_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py) du SDK IoT pour le langage Java. Certaines des lignes les plus importantes de cet exemple sont incluses ici pour illustrer le processus d’authentification.

Lorsque vous définissez la chaîne de connexion pour votre appareil en aval, utilisez le nom d’hôte de l’appareil de passerelle IoT Edge pour le paramètre **HostName**. Le nom d’hôte se trouve dans le fichier config.yaml de l’appareil de passerelle. 

```python
# String containing Hostname, Device Id in the format:
# "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
CONNECTION_STRING = "[Device Connection String]"

X509_CERTIFICATE = (
    "-----BEGIN CERTIFICATE-----""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "...""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXX""\n"
    "-----END CERTIFICATE-----"
)

X509_PRIVATEKEY = (
    "-----BEGIN RSA PRIVATE KEY-----""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "...""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
    "-----END RSA PRIVATE KEY-----"
)


def iothub_client_init():
    # prepare iothub client
    client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

    # this brings in x509 privateKey and certificate
    client.set_option("x509certificate", X509_CERTIFICATE)
    client.set_option("x509privatekey", X509_PRIVATEKEY)

    return client
```

#### <a name="java"></a>Java

Pour obtenir un exemple de programme Java s’authentifiant auprès d’IoT Hub avec des certificats X.509, consultez l’exemple [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py) du SDK IoT pour le langage Java. Certaines des lignes les plus importantes de cet exemple sont incluses ici pour illustrer le processus d’authentification.

Lorsque vous définissez la chaîne de connexion pour votre appareil en aval, utilisez le nom d’hôte de l’appareil de passerelle IoT Edge pour le paramètre **HostName**. Le nom d’hôte se trouve dans le fichier config.yaml de l’appareil de passerelle. 

```java
//PEM encoded representation of the public key certificate
private static String publicKeyCertificateString =
    "-----BEGIN CERTIFICATE-----\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "-----END CERTIFICATE-----\n";

//PEM encoded representation of the private key
private static String privateKeyString =
    "-----BEGIN EC PRIVATE KEY-----\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "-----END EC PRIVATE KEY-----\n";

DeviceClient client = new DeviceClient(connectionString, protocol, publicKeyCertificateString, false, privateKeyString, false);
```

## <a name="next-steps"></a>Étapes suivantes

À présent, vous devez disposer d’un appareil IoT Edge qui fonctionne comme une passerelle transparente, ainsi que d’un appareil en aval inscrit dans IoT hub. Maintenant, vous devez configurer vos appareils en aval pour qu’ils approuvent l’appareil de passerelle et lui envoient des messages. Pour plus d’informations, voir [Connecter un appareil en aval à une passerelle Azure IoT Edge](how-to-connect-downstream-device.md).
