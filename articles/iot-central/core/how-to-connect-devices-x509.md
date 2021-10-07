---
title: Connecter des appareils avec des certificats X.509 dans une application Azure IoT Central
description: Comment connecter des appareils avec des certificats X.509 à l’aide du kit de développement logiciel (SDK) d’appareil Node.js pour une application IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 06/30/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: 308cd5186d97e87ff044db496809b04def41265b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128667118"
---
# <a name="how-to-connect-devices-with-x509-certificates-to-iot-central-application"></a>Guide pratique pour connecter des appareils avec des certificats X.509 à une application IoT Central

Pour sécuriser la communication entre un appareil et votre application, IoT Central prend en charge les signatures d’accès partagé (SAP) et les certificats X.509. Le didacticiel [Créer et connecter une application cliente à votre application Azure IoT Central](./tutorial-connect-device.md) utilise SAS. Dans cet article, vous allez apprendre à modifier l’exemple de code pour utiliser des certificats X.509. Les certificats X.509 sont recommandés dans les environnements de production. Pour plus d’informations, consultez [Se connecter à Azure IoT Central](./concepts-get-connected.md).

Ce guide montre deux façons d’utiliser des certificats X.509 : les [inscriptions de groupe](how-to-connect-devices-x509.md#use-group-enrollment), généralement utilisées dans un environnement de production, et les [inscriptions individuelles](how-to-connect-devices-x509.md#use-individual-enrollment), utiles pour les tests. Cet article explique également comment [renouveler des certificats d’appareil](#roll-x509-device-certificates) pour maintenir la connectivité quand les certificats expirent.

Ce guide s’appuie sur les exemples présentés dans le tutoriel [Créer et connecter une application cliente à votre application Azure IoT Central](tutorial-connect-device.md) qui utilise C#, Java, JavaScript et Python. Pour obtenir un exemple qui utilise le langage de programmation C, consultez la page [Provisionner plusieurs appareils X.509 à l’aide de groupes d’inscriptions](../../iot-dps/tutorial-custom-hsm-enrollment-group-x509.md).

## <a name="prerequisites"></a>Configuration requise

Suivez le tutoriel [Créer une application cliente et la connecter à votre application Azure IoT Central](./tutorial-connect-device.md). Cela comprend l’installation des prérequis pour le langage de programmation que vous choisissez.

Dans ce guide pratique, vous allez générer des certificats de test X.509. Pour pouvoir générer ces certificats, vous avez besoin des éléments suivants :

- Une machine de développement où [Node.js](https://nodejs.org/) version 6 ou ultérieure est installé. Vous pouvez exécuter `node --version` sur la ligne de commande pour vérifier la version. Les instructions de ce tutoriel supposent que vous exécutez la commande **node** à l’invite de commandes Windows. Toutefois, vous pouvez utiliser Node.js sur de nombreux autres systèmes d’exploitation.
- Une copie locale du dépôt GitHub [SDK Microsoft Azure IoT pour Node.js](https://github.com/Azure/azure-iot-sdk-node) qui contient les scripts permettant de générer les certificats de test X.509. Utilisez ce lien pour télécharger une copie du dépôt : [Télécharger le fichier ZIP](https://github.com/Azure/azure-iot-sdk-node/archive/master.zip). Décompressez ensuite le fichier dans un emplacement approprié de votre ordinateur local.

## <a name="use-group-enrollment"></a>Utiliser l’inscription de groupe

Utilisez des certificats X.509 avec une inscription de groupe dans un environnement de production. Dans une inscription de groupe, vous ajoutez un certificat X.509 racine ou intermédiaire à votre application IoT Central. Les appareils avec des certificats feuilles dérivés du certificat racine ou intermédiaire peuvent se connecter à votre application.

### <a name="generate-root-and-device-certificates"></a>Générer des certificats racines et d’appareil

Dans cette section, vous utilisez un certificat X.509 pour connecter un appareil avec un certificat dérivé du certificat du groupe d’inscription IoT Central.

> [!WARNING]
> Cette façon de générer des certificats X.509 est destinée aux tests uniquement. Dans le cas d’un environnement de production, vous devez utiliser votre mécanisme sécurisé officiel pour la génération de certificats.

1. Accédez au script du générateur de certificats dans le SDK Microsoft Azure IoT pour Node.js que vous avez téléchargé. Installez les packages nécessaires :

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

1. Créez un certificat racine, puis dérivez un certificat d’appareil en exécutant le script :

    ```cmd/sh
    node create_test_cert.js root mytestrootcert
    node create_test_cert.js device sample-device-01 mytestrootcert
    ```

    > [!TIP]
    > Un ID d’appareil peut contenir des lettres, des chiffres et le caractère `-`.

Ces commandes génèrent le certificat racine et le certificat d’appareil

| filename | contenu |
| -------- | -------- |
| mytestrootcert_cert.pem | Partie publique du certificat X.509 racine |
| mytestrootcert_key.pem | Clé privée pour le certificat X.509 racine |
| mytestrootcert_fullchain.pem | Trousseau complet pour le certificat X.509 racine. |
| sampleDevice01_cert.pem | Partie publique du certificat X.509 de l’appareil |
| sampleDevice01_key.pem | Clé privée pour le certificat X.509 de l’appareil |
| sampleDevice01_fullchain.pem | Trousseau complet pour le certificat X.509 de l’appareil. |

Notez l’emplacement de ces fichiers. Vous en aurez besoin ultérieurement.

### <a name="create-a-group-enrollment"></a>Créer une inscription de groupe

1. Ouvrez votre application IoT Central et accédez à **Administration** dans le volet gauche, puis sélectionnez **Connexion de l’appareil**.

1. Sélectionnez **+ Nouveau**, puis créez un nouveau groupe d’inscriptions appelé _MyX509Group_ avec un type d’attestation **Certificats (X.509)** .

1. Ouvrez le groupe d’inscriptions que vous avez créé, puis sélectionnez **Gérer le principal**.

1. Sélectionnez l’option de fichier souhaitée pour charger le fichier de certificat racine appelé _mytestrootcert_cert.pem_ que vous avez généré précédemment :

    ![Chargement d’un certificat](./media/how-to-connect-devices-x509/certificate-upload.png)

1. Pour effectuer la vérification, générez le code de vérification, copiez-le, puis utilisez-le pour créer un certificat de vérification X.509 à l’invite de commandes :

    ```cmd/sh
    node create_test_cert.js verification --ca mytestrootcert_cert.pem --key mytestrootcert_key.pem --nonce  {verification-code}
    ```

1. Sélectionnez **Vérifier** afin de charger le certificat de vérification signé _verification_cert.pem_ pour terminer la vérification :

    ![Certificat vérifié](./media/how-to-connect-devices-x509/verified.png)

Vous pouvez maintenant connecter des appareils qui ont un certificat X.509 dérivé de ce certificat racine principal.

Après avoir enregistré le groupe d’inscriptions, prenez note de l’étendue d’ID.

### <a name="run-sample-device-code&quot;></a>Exécuter l’exemple de code d’appareil

:::zone pivot="programming-language-csharp"

Si vous utilisez Windows, les certificats X.509 doivent se trouver dans le magasin de certificats Windows pour que l’exemple fonctionne. Pour ajouter des certificats dans le magasin :

1. Utilisez `openssl` pour créer des fichiers PFX à partir des fichiers PEM. Quand vous exécutez ces commandes, vous êtes invité à créer un mot de passe. Notez le mot de passe ; vous en aurez besoin à l’étape suivante :

    ```bash
    openssl pkcs12 -inkey sampleDevice001_key.pem -in sampleDevice001_cert.pem -export -out sampledevice001.pfx
    openssl pkcs12 -inkey mytestrootcert_key.pem -in mytestrootcert_cert.pem -export -out mytestrootcert.pfx
    ```

1. Dans l’Explorateur Windows, double-cliquez sur chaque fichier PFX. Dans l’**Assistant Importation de certificat**, sélectionnez **Utilisateur actuel** comme emplacement du magasin, entrez le mot de passe de l’étape précédente, puis laissez l’Assistant choisir automatiquement le magasin de certificats. L’Assistant importe les certificats dans le magasin personnel de l’utilisateur actuel.

Pour modifier l’exemple de code afin d’utiliser les certificats :

1. Dans la solution Visual Studio **IoTHubDeviceSamples**, ouvrez le fichier *Parameter.cs* dans le projet **TemperatureController**.

1. Ajoutez les deux définitions de paramètre suivantes à la classe :

    ```csharp
    [Option(
        'x',
        &quot;CertificatePath&quot;,
        HelpText = &quot;(Required if DeviceSecurityType is \"dps\"). \nThe device PFX file to use during device provisioning." +
        "\nDefaults to environment variable \"IOTHUB_DEVICE_X509_CERT\".")]
    public string CertificatePath { get; set; } = Environment.GetEnvironmentVariable("IOTHUB_DEVICE_X509_CERT");

    [Option(
        'p',
        "CertificatePassword",
        HelpText = "(Required if DeviceSecurityType is \"dps\"). \nThe password of the PFX certificate file." +
        "\nDefaults to environment variable \"IOTHUB_DEVICE_X509_PASSWORD\".")]
    public string CertificatePassword { get; set; } = Environment.GetEnvironmentVariable("IOTHUB_DEVICE_X509_PASSWORD");
    ```

    Enregistrez les modifications.

1. Dans la solution Visual Studio **IoTHubDeviceSamples**, ouvrez le fichier *Program.cs* dans le projet **TemperatureController**.

1. Ajoutez les instructions `using` suivantes :

    ```csharp
    using System.Security.Cryptography.X509Certificates;
    using System.IO;
    ```

1. Ajoutez la méthode suivante à la classe :

    ```csharp
    private static X509Certificate2 LoadProvisioningCertificate(Parameters parameters)
    {
        var certificateCollection = new X509Certificate2Collection();
        certificateCollection.Import(
            parameters.CertificatePath,
            parameters.CertificatePassword,
            X509KeyStorageFlags.UserKeySet);

        X509Certificate2 certificate = null;

        foreach (X509Certificate2 element in certificateCollection)
        {
            Console.WriteLine($"Found certificate: {element?.Thumbprint} {element?.Subject}; PrivateKey: {element?.HasPrivateKey}");
            if (certificate == null && element.HasPrivateKey)
            {
                certificate = element;
            }
            else
            {
                element.Dispose();
            }
        }

        if (certificate == null)
        {
            throw new FileNotFoundException($"{parameters.CertificatePath} did not contain any certificate with a private key.");
        }

        Console.WriteLine($"Using certificate {certificate.Thumbprint} {certificate.Subject}");

        return certificate;
    }
    ```

1. Dans la méthode `SetupDeviceClientAsync`, remplacez le bloc de code pour `case "dps"` par le code suivant :

    ```csharp
    case "dps":
        s_logger.LogDebug($"Initializing via DPS");
        Console.WriteLine($"Loading the certificate...");
        X509Certificate2 certificate = LoadProvisioningCertificate(parameters);
        DeviceRegistrationResult dpsRegistrationResult = await ProvisionDeviceAsync(parameters, certificate, cancellationToken);
        var authMethod = new DeviceAuthenticationWithX509Certificate(dpsRegistrationResult.DeviceId, certificate);
        deviceClient = InitializeDeviceClient(dpsRegistrationResult.AssignedHub, authMethod);
        break;
    ```

1. Remplacez la méthode `ProvisionDeviceAsync` par le code suivant :

    ```csharp
    private static async Task<DeviceRegistrationResult> ProvisionDeviceAsync(Parameters parameters, X509Certificate2 certificate, CancellationToken cancellationToken)
    {
        SecurityProvider security = new SecurityProviderX509Certificate(certificate);
        ProvisioningTransportHandler mqttTransportHandler = new ProvisioningTransportHandlerMqtt();
        ProvisioningDeviceClient pdc = ProvisioningDeviceClient.Create(parameters.DpsEndpoint, parameters.DpsIdScope, security, mqttTransportHandler);

        var pnpPayload = new ProvisioningRegistrationAdditionalData
        {
            JsonData = PnpConvention.CreateDpsPayload(ModelId),
        };
        return await pdc.RegisterAsync(pnpPayload, cancellationToken);
    }
    ```

    Enregistrez les modifications.

1. Ajoutez les variables d’environnement suivantes au projet :

    - `IOTHUB_DEVICE_X509_CERT`: `<full path to folder that contains PFX files>sampleDevice01.pfx`
    - `IOTHUB_DEVICE_X509_PASSWORD` : mot de passe que vous avez utilisé lors de la création du fichier *sampleDevice01.pfx*.

1. Générez et exécutez l’application. Vérifiez que l’appareil est correctement provisionné.

:::zone-end

:::zone pivot="programming-language-java"

1. Accédez au dossier _azure-iot-sdk-java/device/iot-device-samples/pnp-device-sample/temperature-controller-device-sample_ qui contient le fichier *pom.xml* et le dossier *src* pour l’exemple d’appareil contrôleur de température.

1. Modifiez le fichier *pom.xml* pour ajouter la configuration des dépendances suivante au nœud `<dependencies>` :

    ```xml
    <dependency>
        <groupId>com.microsoft.azure.sdk.iot.provisioning.security</groupId>
        <artifactId>${x509-provider-artifact-id}</artifactId>
        <version>${x509-provider-version}</version>
    </dependency>
    ```

    Enregistrez les modifications.

1. Ouvrez le fichier *src/main/java/samples/com/microsoft/azure/sdk/iot/device/TemperatureController.java* dans votre éditeur de texte.

1. Remplacez l’importation `SecurityProviderSymmetricKey` par les importations suivantes :

    ```java
    import com.microsoft.azure.sdk.iot.provisioning.security.SecurityProvider;
    import com.microsoft.azure.sdk.iot.provisioning.security.hsm.SecurityProviderX509Cert;
    import com.microsoft.azure.sdk.iot.provisioning.security.exceptions.SecurityProviderException;
    ```

1. Ajoutez l’importation suivante :

    ```java
    import java.nio.file.*;
    ```

1. Ajoutez `SecurityProviderException` à la liste des exceptions levées par la méthode `main` :

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException, ProvisioningDeviceClientException, InterruptedException, SecurityProviderException {
    ```

1. Remplacez la méthode `initializeAndProvisionDevice` par le code suivant :

    ```java
    private static void initializeAndProvisionDevice() throws ProvisioningDeviceClientException, IOException, URISyntaxException, InterruptedException, SecurityProviderException {
        String deviceX509Key = new String(Files.readAllBytes(Paths.get(System.getenv("IOTHUB_DEVICE_X509_KEY"))));
        String deviceX509Cert = new String(Files.readAllBytes(Paths.get(System.getenv("IOTHUB_DEVICE_X509_CERT"))));
        SecurityProvider securityProviderX509 = new SecurityProviderX509Cert(deviceX509Cert, deviceX509Key, null);
        ProvisioningDeviceClient provisioningDeviceClient;
        ProvisioningStatus provisioningStatus = new ProvisioningStatus();

        provisioningDeviceClient = ProvisioningDeviceClient.create(globalEndpoint, scopeId, provisioningProtocol, securityProviderX509);

        AdditionalData additionalData = new AdditionalData();
        additionalData.setProvisioningPayload(com.microsoft.azure.sdk.iot.provisioning.device.plugandplay.PnpHelper.createDpsPayload(MODEL_ID));

        provisioningDeviceClient.registerDevice(new ProvisioningDeviceClientRegistrationCallbackImpl(), provisioningStatus, additionalData);

        while (provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() != ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_ASSIGNED)
        {
            if (provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_ERROR ||
                    provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_DISABLED ||
                    provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_FAILED)
            {
                provisioningStatus.exception.printStackTrace();
                System.out.println("Registration error, bailing out");
                break;
            }
            System.out.println("Waiting for Provisioning Service to register");
            Thread.sleep(MAX_TIME_TO_WAIT_FOR_REGISTRATION);
        }

        ClientOptions options = new ClientOptions();
        options.setModelId(MODEL_ID);

        if (provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_ASSIGNED) {
            System.out.println("IotHUb Uri : " + provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getIothubUri());
            System.out.println("Device ID : " + provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getDeviceId());

            String iotHubUri = provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getIothubUri();
            String deviceId = provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getDeviceId();

            log.debug("Opening the device client.");
            deviceClient = DeviceClient.createFromSecurityProvider(iotHubUri, deviceId, securityProviderX509, IotHubClientProtocol.MQTT, options);
            deviceClient.open();
        }
    }
    ```

    Enregistrez les modifications.

1. Dans votre environnement d’interpréteur de commandes, ajoutez les deux variables d’environnement suivantes. Veillez à fournir le chemin complet des fichiers PEM et utilisez le délimiteur de chemin approprié pour votre système d’exploitation :

    ```cmd/sh
    set IOTHUB_DEVICE_X509_CERT=<full path to folder that contains PEM files>sampleDevice01_cert.pem
    set IOTHUB_DEVICE_X509_KEY=<full path to folder that contains PEM files>sampleDevice01_key.pem
    ```

    > [!TIP]
    > Vous définissez les autres variables d’environnement requises lorsque vous avez terminé le tutoriel [Créer et connecter une application cliente à votre application Azure IoT Central](./tutorial-connect-device.md).

1. Générez et exécutez l’application. Vérifiez que l’appareil est correctement provisionné.

:::zone-end

:::zone pivot="programming-language-javascript"

1. Accédez au dossier _azure-iot-sdk-node/device/samples/pnp_ contenant l’application **pnpTemperatureController.js**, puis exécutez la commande suivante pour installer le package X.509 :

    ```cmd/sh
    npm install azure-iot-security-x509 --save
    ```

1. Ouvrez le fichier **pnpTemperatureController.js** dans un éditeur de texte.

1. Modifiez les instructions `require` pour inclure le code suivant :

    ```javascript
    const fs = require('fs');
    const X509Security = require('azure-iot-security-x509').X509Security;
    ```

1. Ajoutez les quatre lignes suivantes à la section « Informations de connexion DPS » pour initialiser la variable `deviceCert` :

    ```javascript
    const deviceCert = {
      cert: fs.readFileSync(process.env.IOTHUB_DEVICE_X509_CERT).toString(),
      key: fs.readFileSync(process.env.IOTHUB_DEVICE_X509_KEY).toString()
    };
    ```

1. Modifiez la fonction `provisionDevice` qui crée le client en remplaçant la première ligne par le code suivant :

    ```javascript
    var provSecurityClient = new X509Security(registrationId, deviceCert);
    ```

1. Dans la même fonction, modifiez la ligne qui définit la variable `deviceConnectionString` comme suit :

    ```javascript
    deviceConnectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';x509=true';
    ```

1. Dans la fonction `main` , ajoutez la ligne suivante après la ligne qui appelle `Client.fromConnectionString` :

    ```javascript
    client.setOptions(deviceCert);
    ```

    Enregistrez les modifications.

1. Dans votre environnement d’interpréteur de commandes, ajoutez les deux variables d’environnement suivantes. Veillez à fournir le chemin complet des fichiers PEM et utilisez le délimiteur de chemin approprié pour votre système d’exploitation :

    ```cmd/sh
    set IOTHUB_DEVICE_X509_CERT=<full path to folder that contains PEM files>sampleDevice01_cert.pem
    set IOTHUB_DEVICE_X509_KEY=<full path to folder that contains PEM files>sampleDevice01_key.pem
    ```

    > [!TIP]
    > Vous définissez les autres variables d’environnement requises lorsque vous avez terminé le tutoriel [Créer et connecter une application cliente à votre application Azure IoT Central](./tutorial-connect-device.md).

1. Exécutez le script et vérifiez que l’appareil est correctement provisionné :

    ```cmd/sh
    node pnpTemperatureController.js
    ```

:::zone-end

:::zone pivot="programming-language-python"

1. Accédez au dossier _azure-iot-device/samples/pnp_ et ouvrez le fichier **temp_controller_with_thermostats.py** dans un éditeur de texte.

1. Utilisez l’instruction `from` suivante pour importer la fonctionnalité X.509 :

    ```python
    from azure.iot.device import X509
    ```

1. Modifiez la première partie de la fonction `provision_device` comme suit :

    ```python
    async def provision_device(provisioning_host, id_scope, registration_id, x509, model_id):
        provisioning_device_client = ProvisioningDeviceClient.create_from_x509_certificate(
            provisioning_host=provisioning_host,
            registration_id=registration_id,
            id_scope=id_scope,
            x509=x509,
        )
    ```

1. Dans la fonction `main`, remplacez la ligne qui définit la variable `symmetric_key` par le code suivant :

    ```python
    x509 = X509(
        cert_file=os.getenv("IOTHUB_DEVICE_X509_CERT"),
        key_file=os.getenv("IOTHUB_DEVICE_X509_KEY"),
    )
    ```

1. Dans la fonction `main`, remplacez l’appel à la fonction `provision_device` par le code suivant :

    ```python
    registration_result = await provision_device(
        provisioning_host, id_scope, registration_id, x509, model_id
    )
    ```

1. Dans la fonction `main`, remplacez l’appel à la fonction `IoTHubDeviceClient.create_from_symmetric_key` par le code suivant :

    ```python
    device_client = IoTHubDeviceClient.create_from_x509_certificate(
        x509=x509,
        hostname=registration_result.registration_state.assigned_hub,
        device_id=registration_result.registration_state.device_id,
        product_info=model_id,
    )
    ```

    Enregistrez les modifications.

1. Dans votre environnement d’interpréteur de commandes, ajoutez les deux variables d’environnement suivantes. Veillez à fournir le chemin complet des fichiers PEM et utilisez le délimiteur de chemin approprié pour votre système d’exploitation :

    ```cmd/sh
    set IOTHUB_DEVICE_X509_CERT=<full path to folder that contains PEM files>sampleDevice01_cert.pem
    set IOTHUB_DEVICE_X509_KEY=<full path to folder that contains PEM files>sampleDevice01_key.pem
    ```

    > [!TIP]
    > Vous définissez les autres variables d’environnement requises lorsque vous avez terminé le tutoriel [Créer et connecter une application cliente à votre application Azure IoT Central](./tutorial-connect-device.md).

1. Exécutez le script et vérifiez que l’appareil est correctement provisionné :

    ```cmd/sh
    python temp_controller_with_thermostats.py
    ```

:::zone-end

Vérifiez que la télémétrie s’affiche sur la vue de l’appareil dans votre application IoT Central :

![Capture d’écran qui montre la télémétrie arrivant dans votre application IoT Central.](./media/how-to-connect-devices-x509/telemetry.png)

## <a name="use-individual-enrollment"></a>Utiliser une inscription individuelle

Utilisez des certificats X.509 avec une inscription individuelle pour tester votre appareil et votre solution. Dans le cas d’une inscription individuelle, il n’existe aucun certificat X.509 racine ou intermédiaire dans votre application IoT Central. Les appareils utilisent un certificat X.509 auto-signé pour se connecter à votre application.

### <a name="generate-self-signed-device-certificate"></a>Générer un certificat d’appareil auto-signé

Dans cette section, vous utilisez un certificat X.509 auto-signé pour connecter des appareils pour une inscription individuelle, afin d’inscrire un seul appareil. Les certificats auto-signés sont destinés aux tests uniquement.

> [!WARNING]
> Cette façon de générer des certificats X.509 est destinée aux tests uniquement. Dans le cas d’un environnement de production, vous devez utiliser votre mécanisme sécurisé officiel pour la génération de certificats.

Créez un certificat d’appareil X.509 auto-signé en exécutant les commandes suivantes :

```cmd/sh
  cd azure-iot-sdk-node/provisioning/tools
  node create_test_cert.js device mytestselfcertprimary
  node create_test_cert.js device mytestselfcertsecondary 
```

> [!TIP]
> Un ID d’appareil peut contenir des lettres, des chiffres et le caractère `-`.

### <a name="create-individual-enrollment"></a>Créer une inscription individuelle

1. Dans l’application Azure IoT Central, sélectionnez **Appareils**, puis créez un appareil avec **ID d’appareil** comme _mytestselfcertprimary_ à partir du modèle d’appareil thermostat. Prenez note de l’**Étendue d’ID**, vous l’utiliserez plus tard.

1. Ouvrez l’appareil que vous avez créé, puis sélectionnez **Connecter**.

1. Sélectionnez **Inscriptions individuelles** comme **Méthode de connexion** et **Certificats (X.509)** comme mécanisme :

    ![Inscription individuelle](./media/how-to-connect-devices-x509/individual-device-connect.png)

1. Sélectionnez l’option Fichier sous Principal et chargez le fichier de certificat appelé _mytestselfcertprimary_cert.pem_ que vous avez généré précédemment.

1. Sélectionnez l’option Fichier pour le certificat secondaire et chargez le fichier de certificat appelé _mytestselfcertsecondary_cert.pem._ Sélectionnez ensuite **Enregistrer** :

    ![Chargement d’un certificat d’inscription individuelle](./media/how-to-connect-devices-x509/individual-enrollment.png)

L’appareil est maintenant configuré avec le certificat X.509.

### <a name="run-a-sample-individual-enrollment-device"></a>Exécuter un exemple d’appareil d’inscription individuelle

1. Copiez les fichiers _mytestselfcertprimary_key.pem_ et _mytestselfcertprimary_cert.pem_ dans le dossier _azure-iot-sdk-node/device/samples/pnp_ contenant l’application **pnpTemperatureController.js**. Vous avez utilisé cette application quand que vous avez effectué le tutoriel [Connecter un appareil (JavaScript)](./tutorial-connect-device.md).

1. Modifiez les variables d’environnement que vous avez utilisées dans l’exemple ci-dessus comme suit :

    ```cmd/sh
    set IOTHUB_DEVICE_DPS_DEVICE_ID=mytestselfcertprimary
    set IOTHUB_DEVICE_X509_CERT=mytestselfcertprimary_cert.pem
    set IOTHUB_DEVICE_X509_KEY=mytestselfcertprimary_key.pem
    ```

1. Exécutez le script et vérifiez que l’appareil est provisionné correctement :

    ```cmd/sh
    node environmentalSensor.js
    ```

    Vous pouvez également vérifier que la télémétrie s’affiche sur la vue de l’appareil.

    ![Inscription individuelle à la télémétrie](./media/how-to-connect-devices-x509/telemetry-primary.png)

Vous pouvez également répéter les étapes ci-dessus pour le certificat _mytestselfcertsecondary_.

## <a name="connect-an-iot-edge-device"></a>Connecter un appareil IoT Edge

Cette section suppose que vous utilisez une inscription de groupe pour connecter votre appareil IoT Edge. Effectuez les étapes mentionnées dans les sections précédentes pour :

- [Générer des certificats racines et d’appareil](#generate-root-and-device-certificates)
- [Créer une inscription de groupe](#create-a-group-enrollment) <!-- No slightly different type of enrollment group - UPDATE!! -->

Pour connecter l’appareil IoT Edge à IoT Central à l’aide du certificat d’appareil X.509 :

- Copiez le certificat d’appareil et les fichiers de clés sur votre appareil IoT Edge. Dans l’exemple d’inscription de groupe précédent, ces fichiers étaient appelés **sampleDevice01_key.pem** et **sampleDevice01_cert.pem**.
- Sur l’appareil IoT Edge, modifiez la section `provisioning` dans le fichier de configuration **/etc/iotedge/config.yaml** comme suit :

    ```yaml
    # DPS X.509 provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "<SCOPE_ID>"
      attestation:
        method: "x509"
    #   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
        identity_cert: "file:///<path>/sampleDevice01_cert.pem"
        identity_pk: "file:///<path>/sampleDevice01_key.pem"
    #  always_reprovision_on_startup: true
    #  dynamic_reprovisioning: false
    ```

    > [!TIP]
    > Vous n’avez pas besoin d’ajouter une valeur pour `registration_id`. IoT Edge peut utiliser la valeur **CN** du certificat X.509.

- Exécutez la commande suivante pour redémarrer le runtime IoT Edge :

    ```bash
    sudo systemctl restart iotedge
    ```

Pour en savoir plus, consultez [Créer et configurer des appareils IoT Edge à grande échelle sur Linux à l’aide de certificats X.509](../../iot-edge/how-to-provision-devices-at-scale-linux-x509.md).

## <a name="connect-an-iot-edge-leaf-device"></a>Connecter un appareil de nœud terminal IoT Edge

IoT Edge utilise des certificats X.509 pour sécuriser la connexion entre les appareils de nœud terminal et un appareil IoT Edge agissant comme passerelle. Pour en savoir plus sur la configuration de ce scénario, consultez [Connecter un appareil en aval à une passerelle Azure IoT Edge](../../iot-edge/how-to-connect-downstream-device.md).

## <a name="roll-x509-device-certificates"></a>Renouveler des certificats d’appareil X.509

Pendant le cycle de vie de votre application IoT Central, vous devrez renouveler vos certificats X.509. Par exemple :

- Si vous êtes victime d’une violation de la sécurité, le renouvellement des certificats est une bonne pratique en matière de sécurité qui contribue à sécuriser votre système.
- Les certificats X.509 ont des dates d’expiration. La fréquence à laquelle vous renouvelez vos certificats dépend des besoins de sécurité de votre solution. Les clients qui disposent de solutions impliquant des données extrêmement sensibles peuvent renouveler leurs certificats quotidiennement, tandis que d’autres clients renouvellent leurs certificats tous les deux ans.

Pour une connectivité ininterrompue, IoT Central vous permet de configurer des certificats X.509 primaires et secondaires. Si les certificats principaux et secondaires ont des dates d’expiration différentes, vous pouvez renouveler le certificat expiré pendant que les appareils continuent de se connecter à l’autre certificat.

Pour en savoir plus, consultez [Méthodologie « Assume Breach » (Envisager les failles)](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf).

Cette section explique comment renouveler les certificats dans IoT Central. Quand vous renouvelez un certificat dans IoT Central, vous devez également copier le nouveau certificat d’appareil sur vos appareils.

### <a name="obtain-new-x509-certificates"></a>Obtenir de nouveaux certificats X.509

Obtenez de nouveaux certificats X.509 auprès de votre fournisseur de certificats. Vous ne pouvez pas créer vos propres certificats X.509 à l’aide d’un outil tel qu’OpenSSL. Cette approche est utile pour tester les certificats X.509, mais offre peu de garanties de sécurité. Utilisez cette approche uniquement des fins de test, sauf si vous êtes votre propre fournisseur d’autorité de certification.

### <a name="enrollment-groups-and-security-breaches"></a>Groupes d’inscription et violations de la sécurité

Pour mettre à jour une inscription de groupe à la suite d’une violation de la sécurité, vous devez utiliser l’approche suivante pour mettre immédiatement à jour le certificat actuel. Effectuez ces étapes pour le certificat principal et le certificat secondaire s’ils sont tous deux compromis :

1. Accédez à **Administration** dans le volet gauche et sélectionnez **Connexion de l’appareil**.

2. Sélectionnez **Groupes d’inscription** et le nom du groupe dans la liste.

3. Pour mettre à jour des certificats, sélectionnez **Gérer le principal** ou **Gérer le secondaire**.

4. Ajoutez et vérifiez un certificat X.509 racine dans le groupe d’inscription.

### <a name="individual-enrollments-and-security-breaches"></a>Inscriptions individuelles et violations de la sécurité

Si vous renouvelez des certificats à la suite d’une violation de la sécurité, utilisez l’approche suivante pour mettre immédiatement à jour le certificat actuel. Effectuez ces étapes pour le certificat principal et le certificat secondaire s’ils sont tous deux compromis :

1. Sélectionnez **Appareils**, puis sélectionnez l’appareil.

1. Sélectionnez **Connecter**, puis sélectionnez la méthode de connexion comme **Inscription individuelle**

1. Sélectionnez **Certificats (X.509)** en tant que mécanisme.

1. Pour mettre à jour les certificats, sélectionnez l’icône de dossier afin de sélectionner le nouveau certificat à charger pour l’entrée d’inscription. Sélectionnez **Enregistrer**.

### <a name="enrollment-groups-and-certificate-expiration"></a>Groupes d’inscription et expiration des certificats

Pour gérer les expirations de certificats, utilisez l’approche suivante afin de mettre immédiatement à jour le certificat actuel :

1. Accédez à **Administration** dans le volet gauche et sélectionnez **Connexion de l’appareil**.

2. Sélectionnez **Groupes d’inscription** et le nom du groupe dans la liste.

3. Pour mettre à jour des certificats, sélectionnez **Gérer le principal**.

4. Ajoutez et vérifiez un certificat X.509 racine dans le groupe d’inscription.

5. Plus tard, quand le certificat secondaire aura expiré, revenez en arrière et mettez-le à jour.

### <a name="individual-enrollments-and-certificate-expiration"></a>Inscriptions individuelles et expiration des certificats

Si vous renouvelez des certificats afin de gérer les expirations de certificats, vous devez utiliser la configuration de certificat secondaire comme suit pour minimiser le temps d’arrêt lié à la tentative de provisionnement des appareils.

Lorsque le certificat secondaire approche de sa date d’expiration et doit être renouvelé, vous pouvez basculer vers la configuration principale. Cette alternance entre le certificat principal et le certificat secondaire réduit le temps d’arrêt découlant de la tentative de provisionnement des appareils.

1. Sélectionnez **Appareils**, puis sélectionnez l’appareil.

2. Sélectionnez **Connecter**, puis sélectionnez la méthode de connexion comme **Inscription individuelle**

3. Sélectionnez **Certificats (X.509)** en tant que mécanisme.

4. Pour mettre à jour le certificat secondaire, sélectionnez l’icône de dossier afin de sélectionner le nouveau certificat à charger pour l’entrée d’inscription. Sélectionnez **Enregistrer**.

5. Plus tard, quand le certificat principal aura expiré, revenez et mettez-le à jour.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à connecter des appareils à l’aide de certificats X.509, nous vous suggérons de découvrir comment [superviser la connectivité des appareils à l’aide d’Azure CLI](howto-monitor-devices-azure-cli.md).
