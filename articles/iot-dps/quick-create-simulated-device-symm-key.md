---
title: Démarrage rapide - Provisionner un appareil à clé symétrique simulé pour Microsoft Azure IoT Hub
description: Découvrir comment provisionner un appareil qui s’authentifie avec une clé symétrique dans le service Azure IoT Hub Device Provisioning (DPS)
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/29/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: lizross
ms.custom: mvc
zone_pivot_groups: iot-dps-set1
ms.openlocfilehash: 43c6dc8e9d8a6438468c44fd2b8cc31d04a92c2f
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129276860"
---
# <a name="quickstart-provision-a-simulated-symmetric-key-device"></a>Démarrage rapide : Provisionner un appareil à clé symétrique simulé

Dans ce guide de démarrage rapide, vous allez créer un appareil simulé sur votre machine Windows. L’appareil simulé sera configuré pour utiliser le mécanisme d’[attestation de clé symétrique](concepts-symmetric-key-attestation.md) pour l’authentification. Une fois que vous aurez configuré votre appareil, vous le configurerez sur votre hub IoT à l’aide du service Azure IoT Hub Device Provisioning.

Si vous ne connaissez pas le processus de provisionnement, passez en revue la vue d’ensemble du [provisionnement](about-iot-dps.md#provisioning-process).

Ce guide de démarrage rapide présente une solution pour une station de travail Windows. Vous pouvez toutefois également effectuer les procédures sur Linux. Pour obtenir un exemple sur Linux, consultez [Guide pratique du provisionnement pour la multilocation](how-to-provision-multitenant.md).

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.

* Effectuez les étapes décrites dans [Configurer le service IoT Hub Device Provisioning avec le portail Azure](./quick-setup-auto-provision.md).
::: zone pivot="programming-language-ansi-c"

* Si vous utilisez un environnement de développement Windows, installez [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 avec la charge de travail [« Desktop Development en C++ »](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) activée. Visual Studio 2015 et Visual Studio 2017 sont également pris en charge. Pour Linux ou macOS, consultez la section appropriée de [Préparer votre environnement de développement](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) dans la documentation du kit de développement logiciel (SDK).

::: zone-end

::: zone pivot="programming-language-csharp"

* Installez le [kit SDK .NET Core 2.1](https://dotnet.microsoft.com/download) sur votre machine Windows. Vous pouvez exécuter la commande suivante pour vérifier votre version.

    ```bash
    dotnet --info
    ```

::: zone-end

::: zone pivot="programming-language-nodejs"

* Installez [Node.js v4.0+](https://nodejs.org).

::: zone-end

::: zone pivot="programming-language-python"

* Installez [Python 3.7](https://www.python.org/downloads/) ou une version ultérieure sur votre machine Windows. Vous pouvez vérifier votre version de Python en exécutant `python --version`.

::: zone-end

::: zone pivot="programming-language-java"

* Installez [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-support-on-azure) ou une version ultérieure sur votre machine.

* Téléchargez et installez [Maven](https://maven.apache.org/install.html).

::: zone-end

* Installez la dernière version de [Git](https://git-scm.com/download/). Vérifiez que Git est ajouté aux variables d’environnement accessibles à la fenêtre de commande. Consultez [Outils clients Git de Software Freedom Conservancy](https://git-scm.com/download/) pour accéder à la dernière version des outils `git` à installer, ce qui inclut *Git Bash*, l’application en ligne de commande que vous pouvez utiliser pour interagir avec votre dépôt Git local.


<a id="setupdevbox"></a>

## <a name="prepare-your-development-environment"></a>Préparer votre environnement de développement

::: zone pivot="programming-language-ansi-c"

Dans cette section, vous allez préparer un environnement de développement qui est utilisé pour générer le [kit SDK C Azure IoT ](https://github.com/Azure/azure-iot-sdk-c). L’exemple de code tente de provisionner l’appareil pendant la séquence de démarrage de ce dernier.

1. Téléchargez le dernier [système de génération CMake](https://cmake.org/download/).

    >[!IMPORTANT]
    >Vérifiez que les prérequis de Visual Studio (Visual Studio et la charge de travail « Développement Desktop en C++ ») sont installés sur votre machine **avant** de commencer l’installation de `CMake`. Une fois les composants requis en place et le téléchargement effectué, installez le système de génération de CMake. Sachez également que les versions antérieures du système de génération CMake ne permettent pas de générer le fichier solution utilisé dans cet article. Veillez à utiliser la dernière version de CMake.

2. Ouvrez un navigateur web, puis accédez à la [page des versions du kit SDK C Azure IoT](https://github.com/Azure/azure-iot-sdk-c/releases/latest).

3. Sélectionnez l’onglet **Étiquettes** en haut de la page.

4. Copiez le nom d’étiquette de la version la plus récente du SDK C Azure IoT.

5. Ouvrez une invite de commandes ou l’interpréteur de commandes Git Bash. Exécutez les commandes suivantes pour cloner la dernière version du dépôt GitHub du [SDK C Azure IoT](https://github.com/Azure/azure-iot-sdk-c) (en remplaçant `<release-tag>` par l’étiquette que vous avez copiée à l’étape précédente).

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Cette opération peut prendre plusieurs minutes.

6. Une fois l’opération terminée, exécutez les commandes suivantes à partir du répertoire `azure-iot-sdk-c` :

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

7. L’exemple de code utilise une clé symétrique pour fournir l’attestation. Exécutez la commande suivante pour générer une version du SDK spécifique à votre plateforme cliente de développement qui inclut le client de provisionnement des appareils :

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    >[!TIP]
    >Si `cmake` ne trouve pas votre compilateur C++, vous risquez de rencontrer des erreurs de génération lors de l’exécution de la commande ci-dessus. Si cela se produit, essayez d’exécuter la commande dans [l’invite de commandes de Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs).

8. Quand la build se termine correctement, les dernières lignes de la sortie ressemblent à la sortie suivante :

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 16 2019
    -- Selecting Windows SDK version 10.0.19041.0 to target Windows 10.0.19042.
    -- The C compiler identification is MSVC 19.29.30040.0
    -- The CXX compiler identification is MSVC 19.29.30040.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

::: zone-end

::: zone pivot="programming-language-csharp"

1. Ouvrez un environnement de ligne de commande Git CMD ou Git Bash.

2. Clonez le dépôt GitHub d’[exemples Azure IoT pour C#](https://github.com/Azure-Samples/azure-iot-samples-csharp) à l’aide de la commande suivante :

    ```cmd
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```

::: zone-end

::: zone pivot="programming-language-nodejs"

1. Ouvrez un environnement de ligne de commande Git CMD ou Git Bash.

2. Clonez le dépôt GitHub du kit [SDK Azure IoT pour Node.js](https://github.com/Azure/azure-iot-sdk-node.git) à l’aide de la commande suivante :

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-node.git --recursive
    ```

::: zone-end

::: zone pivot="programming-language-python"

1. Ouvrez un environnement de ligne de commande Git CMD ou Git Bash.

2. Clonez le dépôt GitHub du [SDK Azure IoT pour Python](https://github.com/Azure/azure-iot-sdk-python.git) à l’aide de la commande suivante :

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```

3. Accédez au répertoire `azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios` dans lequel se trouve l’exemple de fichier, _provision_symmetric_key.py_.

   ```console
   cd azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios
   ```

4. Installez la bibliothèque _azure-iot-device_ en exécutant la commande suivante.

    ```console
    pip install azure-iot-device
    ```

::: zone-end

::: zone pivot="programming-language-java"

1. Ouvrez un environnement de ligne de commande Git CMD ou Git Bash.

2. Clonez le dépôt GitHub du [SDK Azure IoT pour Java](https://github.com/Azure/azure-iot-sdk-java.git) à l’aide de la commande suivante :

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

3. Accédez au répertoire `azure-iot-sdk-java` racine et générez le projet pour télécharger tous les packages nécessaires.

   ```console
   cd azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios
   ```

4. Installez la bibliothèque _azure-iot-device_ en exécutant la commande suivante.

   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```

::: zone-end

## <a name="create-a-device-enrollment"></a>Créer une inscription d’appareil

Le service Azure IoT Device Provisioning prend en charge deux types d’inscriptions :

* [Groupes d’inscription](concepts-service.md#enrollment-group) : utilisés pour inscrire plusieurs appareils connexes.
* [Inscriptions individuelles](concepts-service.md#individual-enrollment) : utilisées pour inscrire un seul appareil.

Cet article décrit l’inscription individuelle d’un appareil à provisionner auprès d’un hub IoT.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans le menu de gauche ou dans la page du portail, sélectionnez **Toutes les ressources**.

3. Sélectionnez votre service Device Provisioning.

4. Dans le menu **Paramètres**, sélectionnez **Gérer les inscriptions**.

5. En haut de la page, sélectionnez **+ Ajouter une inscription individuelle**.

6. Dans la page **Ajouter une inscription**, entrez les informations suivantes.

   * **Mécanisme** : sélectionnez *Clé symétrique* comme mécanisme d’attestation d’identité.

   * **Générer automatiquement les clés** : cochez cette case.

   * **ID d’inscription** : entrez un ID d’inscription pour identifier l’inscription. Utilisez uniquement des caractères alphanumériques en minuscules et des tirets (« - »). Par exemple, *symm-key-device-007*.

   * **ID de l’appareil IoT Hub** : entrez un identificateur d’appareil.

    :::zone pivot="programming-language-ansi-c"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/create-individual-enrollment.png" alt-text="Entrer les informations d’inscription d’un appareil.":::

    ::: zone-end

    :::zone pivot="programming-language-csharp"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/create-individual-enrollment-csharp.png" alt-text="Entrer les informations d’inscription d’un appareil C#.":::

    ::: zone-end

    :::zone pivot="programming-language-nodejs"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/create-individual-enrollment-nodejs.png" alt-text="Entrer les informations d’inscription d’un appareil Node.js.":::

    ::: zone-end

    :::zone pivot="programming-language-python"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/create-individual-enrollment-python.png" alt-text="Entrer les informations d’inscription d’un appareil Python.":::

    ::: zone-end

    ::: zone pivot="programming-language-java"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/create-individual-enrollment-java.png" alt-text="Entrer les informations d’inscription d’un appareil Java.":::

    ::: zone-end

7. Sélectionnez **Enregistrer**. Une **Clé primaire** et une **Clé secondaire** sont générées et ajoutées à l’entrée d’inscription, et vous êtes redirigé vers la page **Gérer les inscriptions**.

8. Pour voir votre inscription d’appareil à clé symétrique simulée, sélectionnez l’onglet **Inscriptions individuelles**.

9. Sélectionnez votre appareil (*symm-key-device-007*).

10. Copiez la valeur de la **Clé primaire** générée.

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/copy-device-enrollment-primary-key.png" alt-text="Copier la clé primaire de l’inscription de l’appareil":::

<a id="firstbootsequence"></a>

## <a name="prepare-and-run-the-device-provisioning-code"></a>Préparer et exécuter le code de provisionnement d’un appareil

::: zone pivot="programming-language-ansi-c"

Dans cette section, vous allez mettre à jour l’exemple de code d’appareil pour envoyer la séquence de démarrage de l’appareil à votre instance du service Device Provisioning. Grâce à cette séquence de démarrage, l’appareil est reconnu, authentifié et affecté à un hub IoT lié à l’instance du service Device Provisioning.

L’exemple de code de provisionnement effectue les tâches suivantes, dans l’ordre :

1. Authentifie votre appareil avec votre ressource Device Provisioning à l’aide des trois paramètres suivants :

    * L’étendue de l’ID de votre service Device Provisioning
    * L’ID d’inscription pour l’inscription de votre appareil.
    * La clé symétrique principale pour l’inscription de votre appareil.

2. Affecte l’appareil à l’hub IoT qui est déjà lié à votre instance du service Device Provisioning.

Pour mettre à jour et exécuter l’exemple de provisionnement avec les informations de votre appareil :

1. Dans le menu principal du service Device Provisioning, sélectionnez **Vue d’ensemble**.

2. Copiez la valeur **Étendue de l’ID**.

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/extract-dps-endpoints.png" alt-text="Extraire des informations de point de terminaison du service Device Provisioning":::

3. Dans Visual Studio, ouvrez le fichier de solution *azure_iot_sdks.sln* généré en exécutant CMake. Le fichier solution doit être à l’emplacement suivant :

    ```output

    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln

    ```

    >[!TIP]
    >Si le fichier n’a pas été généré dans votre répertoire CMake, vérifiez que vous avez utilisé une version récente du système de génération CMake.

4. Dans la fenêtre *Explorateur de solutions* de Visual Studio, accédez au dossier **Provision\_Samples**. Développez l’exemple de projet nommé **prov\_dev\_client\_sample**. Développez **Fichiers sources**, puis ouvrez **prov\_dev\_client\_sample.c**.

5. Recherchez la constante `id_scope` et remplacez la valeur par la valeur **Étendue de l’ID** que vous avez copiée précédemment.

    ```c
    static const char* id_scope = "0ne00002193";
    ```

6. Recherchez la définition de la fonction `main()` dans le même fichier. Vérifiez que la variable `hsm_type` a la valeur `SECURE_DEVICE_TYPE_SYMMETRIC_KEY`, comme indiqué ci-dessous :

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

7. Recherchez l’appel à `prov_dev_set_symmetric_key_info()` dans **prov\_dev\_client\_sample.c** qui est commenté.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Supprimez les marques de commentaire de l’appel de fonction et remplacez les valeurs d’espace réservé (crochets inclus) par votre ID d’inscription et la valeur de clé primaire que vous avez copiée précédemment.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("symm-key-device-007", "your primary key here");
    ```

8. Enregistrez le fichier .

9. Cliquez avec le bouton droit sur le projet **prov\_dev\_client\_sample** et sélectionnez **Définir comme projet de démarrage**.

10. Dans le menu Visual Studio, sélectionnez **Déboguer** > **Exécuter sans débogage** pour exécuter la solution. Dans l’invite de regénération du projet, sélectionnez **Yes** (Oui) pour regénérer le projet avant de l’exécuter.

    La sortie suivante est un exemple illustrant la connexion réussie de l’appareil à l’instance de service de provisionnement à affecter à un hub IoT :

    ```cmd
    Provisioning API Version: 1.2.8

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: 
    test-docs-hub.azure-devices.net, deviceId: device-007    
    Press enter key to exit:
    ```

::: zone-end

::: zone pivot="programming-language-csharp"

L’exemple de code de provisionnement effectue les tâches suivantes :

1. Authentifie votre appareil avec votre ressource Device Provisioning à l’aide des trois paramètres suivants :

    * L’étendue de l’ID de votre service Device Provisioning
    * L’ID d’inscription pour l’inscription de votre appareil.
    * La clé symétrique principale pour l’inscription de votre appareil.

2. Affecte l’appareil à l’hub IoT qui est déjà lié à votre instance du service Device Provisioning.

3. Envoie un message de télémétrie de test au hub IoT.

Pour mettre à jour et exécuter l’exemple de provisionnement avec les informations de votre appareil :

1. Dans le menu principal de votre service Device Provisioning, sélectionnez **Vue d’ensemble**.

2. Copiez la valeur **Étendue de l’ID**.

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/extract-dps-endpoints.png" alt-text="Extraire des informations de point de terminaison du service Device Provisioning":::

3. Ouvrez une invite de commandes et accédez à *SymmetricKeySample* dans le dépôt d’exemples cloné :

    ```cmd
    cd azure-iot-samples-csharp\provisioning\Samples\device\SymmetricKeySample
    ```

4. Dans le dossier *SymmetricKeySample*, ouvrez *Parameters.cs* dans un éditeur de texte. Ce fichier montre les paramètres qui sont pris en charge par l’exemple. Seuls les trois premiers paramètres requis seront utilisés dans cet article lors de l’exécution de l’exemple. Passez en revue le code dans ce fichier. Aucune modification n’est requise.

    | Paramètre                         | Obligatoire | Description     |
    | :-------------------------------- | :------- | :-------------- |
    | `--s` ou `--IdScope`              | Vrai     | Étendue de l’ID de l’instance DPS |
    | `--i` ou `--Id`                   | Vrai     | ID d’inscription lors de l’utilisation d’une inscription individuelle ou ID d’appareil souhaité lors de l’utilisation d’une inscription de groupe. |
    | `--p` ou `--PrimaryKey`           | Vrai     | Clé primaire de l’inscription individuelle ou de groupe. |
    | `--e` ou `--EnrollmentType`       | Faux    | Type d’inscription : `Individual` ou `Group`. La valeur par défaut est `Individual` |
    | `--g` ou `--GlobalDeviceEndpoint` | Faux    | Point de terminaison global pour les appareils auxquels se connecter. La valeur par défaut est `global.azure-devices-provisioning.net` |
    | `--t` ou `--TransportType`        | Faux    | Transport à utiliser pour communiquer avec l’instance de provisionnement d’appareil. La valeur par défaut est `Mqtt`. Les valeurs possibles incluent `Mqtt`, `Mqtt_WebSocket_Only`, `Mqtt_Tcp_Only`, `Amqp`, `Amqp_WebSocket_Only`, `Amqp_Tcp_only` et `Http1`.|

5. Dans le dossier *SymmetricKeySample*, ouvrez *ProvisioningDeviceClientSample.cs* dans un éditeur de texte. Ce fichier montre comment la classe [SecurityProviderSymmetricKey](/dotnet/api/microsoft.azure.devices.shared.securityprovidersymmetrickey?view=azure-dotnet&preserve-view=true) est utilisée avec la classe [ProvisioningDeviceClient](/dotnet/api/microsoft.azure.devices.provisioning.client.provisioningdeviceclient?view=azure-dotnet&preserve-view=true) pour provisionner votre appareil à clé symétrique simulé. Passez en revue le code dans ce fichier.  Aucune modification n’est requise.

6. Générez et exécutez l’exemple de code à l’aide de la commande suivante après avoir remplacé les trois exemples de paramètres (en remplaçant `<id-scope>` par l’étendue de l’ID de votre service Device Provisioning, `<registration-id>` par l’ID d’inscription de votre appareil et `<primarykey>` par la clé primaire de votre appareil).

    ```console
    dotnet run --s <id-scope> --i <registration-id> --p <primarykey>
    ```

7. Vous devriez obtenir un résultat semblable à la sortie suivante. Une chaîne « TestMessage » est envoyée au hub en guise de message de test.

     ```output
    D:\azure-iot-samples-csharp\provisioning\Samples\device\SymmetricKeySample>dotnet run --s 0ne00000A0A --i symm-key-csharp-device-01 --p sbDDeEzRuEuGKag+kQKV+T1QGakRtHpsERLP0yPjwR93TrpEgEh/Y07CXstfha6dhIPWvdD1nRxK5T0KGKA+nQ==

    Initializing the device provisioning client...
    Initialized for registration Id symm-key-csharp-device-01.
    Registering with the device provisioning service...
    Registration status: Assigned.
    Device csharp-device-01 registered to ExampleIoTHub.azure-devices.net.
    Creating symmetric key authentication for IoT Hub...
    Testing the provisioned device with IoT Hub...
    Sending a telemetry message...
    Finished.
    Enter any key to exit.
    ```

::: zone-end

::: zone pivot="programming-language-nodejs"

L’exemple de code de provisionnement effectue les tâches suivantes, dans l’ordre :

1. Authentifie votre appareil avec votre ressource Device Provisioning à l’aide des quatre paramètres suivants :
    * `PROVISIONING_HOST`
    * `PROVISIONING_IDSCOPE`
    * `PROVISIONING_REGISTRATION_ID`
    * `PROVISIONING_SYMMETRIC_KEY`

2. Affecte l’appareil à l’hub IoT qui est déjà lié à votre instance du service Device Provisioning.

3. Envoie un message de télémétrie de test au hub IoT.

Pour mettre à jour et exécuter l’exemple de provisionnement avec les informations de votre appareil :

1. Dans le menu principal de votre service Device Provisioning, sélectionnez **Vue d’ensemble**.

2. Copiez les valeurs **Étendue de l’ID** et **Point de terminaison de service**.

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/extract-dps-endpoints-host.png" alt-text="Extraire des informations de point de terminaison du service Device Provisioning":::

3. Ouvrez une invite de commandes pour l’exécution de commandes Node.js, puis accédez au répertoire suivant :

    ```cmd
    cd azure-iot-sdk-node/provisioning/device/samples
    ```

4. Dans le dossier *provisioning/device/samples*, ouvrez *register_symkey.js* et passez en revue le code. Notez que l’exemple de code définit une charge utile personnalisée :

    ```nodejs
    provisioningClient.setProvisioningPayload({a: 'b'});
    ```

    Vous pouvez mettre ce code en commentaire, car il n’est pas nécessaire dans le cadre de ce guide de démarrage rapide. Une charge utile personnalisée est nécessaire si vous souhaitez utiliser une fonction d’allocation personnalisée pour affecter votre appareil à un hub IoT. Pour plus d’informations, consultez [Didacticiel : Utiliser des stratégies d’allocation personnalisées](tutorial-custom-allocation-policies.md).

     La méthode `provisioningClient.register()` tente d’effectuer l’inscription de votre appareil.

    Aucun autre changement n’est nécessaire.

5. Dans votre invite de commandes, vous allez maintenant définir les variables d’environnement suivantes (en remplaçant `<id-scope>` par l’étendue de l’ID de votre service Device Provisioning, `<registration-id>` par l’ID d’inscription de votre appareil, `<primarykey>` par la clé primaire de votre appareil, `<provisioning-host>` par l’URL du point de terminaison de votre service Device Provisioning) :

    ```console
    set PROVISIONING_HOST=<provisioning-host>
    ```

    ```console
    set PROVISIONING_IDSCOPE=<id-scope>
    ```

    ```console
    set PROVISIONING_REGISTRATION_ID=<registration-id>
    ```

    ```console
    set PROVISIONING_SYMMETRIC_KEY=<primarykey>
    ```

6. Générez et exécutez l’exemple de code à l’aide des commandes suivantes :

   ```console
    npm install
    ```

    ```console
    node register_symkey.js
    ```

7. Vous devriez obtenir un résultat semblable à la sortie suivante. Une chaîne « Hello World » est envoyée au hub en guise de message de test.

     ```output
    D:\azure-iot-samples-csharp\provisioning\Samples\device\SymmetricKeySample>dotnet run --s 0ne00000A0A --i symm-key-csharp-device-01 --p sbDDeEzRuEuGKag+kQKV+T1QGakRtHpsERLP0yPjwR93TrpEgEh/Y07CXstfha6dhIPWvdD1nRxK5T0KGKA+nQ==

    Initializing the device provisioning client...
    Initialized for registration Id symm-key-csharp-device-01.
    Registering with the device provisioning service...
    Registration status: Assigned.
    Device csharp-device-01 registered to ExampleIoTHub.azure-devices.net.
    Creating symmetric key authentication for IoT Hub...
    Testing the provisioned device with IoT Hub...
    Sending a telemetry message...
    Finished.
    Enter any key to exit.
    ```

::: zone-end

::: zone pivot="programming-language-python"

L’exemple de code de provisionnement effectue les tâches suivantes, dans l’ordre :

1. Authentifie votre appareil avec votre ressource Device Provisioning à l’aide des quatre paramètres suivants :

    * `PROVISIONING_HOST`
    * `PROVISIONING_IDSCOPE`
    * `PROVISIONING_REGISTRATION_ID`
    * `PROVISIONING_SYMMETRIC_KEY`

2. Affecte l’appareil à l’hub IoT qui est déjà lié à votre instance du service Device Provisioning.

3. Envoie un message de télémétrie de test au hub IoT.

Pour mettre à jour et exécuter l’exemple de provisionnement avec les informations de votre appareil :

1. Dans le menu principal de votre service Device Provisioning, sélectionnez **Vue d’ensemble**.

2. Copiez les valeurs **Étendue de l’ID** et **Point de terminaison de service**.

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/extract-dps-endpoints-host.png" alt-text="Extraire des informations de point de terminaison du service Device Provisioning":::

3. Dans votre invite de commandes, vous allez maintenant définir les variables d’environnement suivantes (en remplaçant `<id-scope>` par l’étendue de l’ID de votre service Device Provisioning, `<registration-id>` par l’ID d’inscription de votre appareil, `<primarykey>` par la clé primaire de votre appareil, `<provisioning-host>` par l’URL du point de terminaison de votre service Device Provisioning) :

    ```console
    set PROVISIONING_HOST=<provisioning-host>
    ```

    ```console
    set PROVISIONING_IDSCOPE=<id-scope>
    ```

    ```console
    set PROVISIONING_REGISTRATION_ID=<registration-id>
    ```

    ```console
    set PROVISIONING_SYMMETRIC_KEY=<primarykey>
    ```

4. Dans la fenêtre d’invite de commandes, accédez au répertoire suivant :

    ```cmd
    cd azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios
    ```

5. Exécutez l’exemple de code Python dans *_provision_symmetric_key.py_*.

    ```console
    python provision_symmetric_key.py
    ```

6. Vous devriez obtenir un résultat semblable à la sortie suivante. Des exemples de messages de télémétrie de la vitesse du vent sont également envoyés au hub en guise de test.

     ```output
    D:\azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios>python provision_symmetric_key.py
    RegistrationStage(RequestAndResponseOperation): Op will transition into polling after interval 2.  Setting timer.
    The complete registration result is
    python-device-008
    docs-test-iot-hub.azure-devices.net
    initialAssignment
    null
    Will send telemetry from the provisioned device
    sending message #8
    sending message #9
    sending message #3
    sending message #10
    sending message #4
    sending message #2
    sending message #6
    sending message #7
    sending message #1
    sending message #5
    done sending message #8
    done sending message #9
    done sending message #3
    done sending message #10
    done sending message #4
    done sending message #2
    done sending message #6
    done sending message #7
    done sending message #1
    done sending message #5
    ```

::: zone-end

::: zone pivot="programming-language-java"

L’exemple de code de provisionnement effectue les tâches suivantes, dans l’ordre :

1. Authentifie votre appareil avec votre ressource Device Provisioning à l’aide des quatre paramètres suivants :

    * `GLOBAL_ENDPOINT`
    * `SCOPE_ID`
    * `REGISTRATION_ID`
    * `SYMMETRIC_KEY`

2. Affecte l’appareil à l’hub IoT qui est déjà lié à votre instance du service Device Provisioning.

3. Envoie un message de télémétrie de test au hub IoT.

Pour mettre à jour et exécuter l’exemple de provisionnement avec les informations de votre appareil :

1. Dans le menu principal de votre service Device Provisioning, sélectionnez **Vue d’ensemble**.

2. Copiez les valeurs **Étendue de l’ID** et **Point de terminaison de service**. Il s’agit de votre `SCOPE_ID` et de votre `GLOBAL_ENDPOINT`, respectivement.

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/extract-dps-endpoints-host.png" alt-text="Extraire des informations de point de terminaison du service Device Provisioning":::

3. Ouvrez l’exemple de code d’appareil Java pour le modifier. Le chemin complet de l’exemple de code d’appareil est le suivant :

    `azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-symmetrickey-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningSymmetricKeySampleSample.java`

4. Remplacez la valeur des variables suivantes pour votre service DPS et l’inscription de votre appareil (en remplaçant `<id-scope>` par l’étendue de l’ID de votre service Device Provisioning, `<registration-id>` par l’ID d’inscription de votre appareil, `<primarykey>` par la clé primaire de votre appareil, `<provisioning-host>` par l’URL du point de terminaison de votre service Device Provisioning) :

    ```java
    private static final String SCOPE_ID = "<id-scope>";
    private static final String GLOBAL_ENDPOINT = "<provisioning-host>";
    private static final String SYMMETRIC_KEY = "<primarykey>";
    private static final String REGISTRATION_ID = "<registration-id>";
    ```

5. Ouvrez une invite de commandes pour la génération. Accédez à l’exemple de dossier de projet de provisionnement du dépôt SDK Java.

    ```cmd
    cd azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios
    ```

6. Générez l’exemple, puis accédez au dossier `target` pour exécuter le fichier `.jar` créé.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-symmetrickey-sample-{version}-with-deps.jar
    ```

7. Vous devriez obtenir un résultat semblable à la sortie suivante.

     ```cmd/sh
      Starting...
      Beginning setup.
      Waiting for Provisioning Service to register
      IotHUb Uri : <Your DPS Service Name>.azure-devices.net
      Device ID : java-device-007
      Sending message from device to IoT Hub...
      Press any key to exit...
      Message received! Response status: OK_EMPTY
    ```

::: zone-end

## <a name="confirm-your-device-provisioning-registration"></a>Confirmer l’inscription du provisionnement de votre appareil

1. Accédez au [portail Azure](https://portal.azure.com).

2. Dans le menu de gauche ou dans la page du portail, sélectionnez **Toutes les ressources**.

3. Sélectionnez le hub IoT auquel votre appareil a été attribué.

4. Dans le menu **Explorateurs**, sélectionnez **Appareils IoT**.

5. Si votre appareil a été provisionné correctement, l’ID de l’appareil doit apparaître dans la liste, avec l’**État** défini sur *Activé*. Si vous ne voyez pas votre appareil, sélectionnez **Actualiser** en haut de la page.

    :::zone pivot="programming-language-ansi-c"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/hub-registration.png" alt-text="L’appareil est inscrit avec le hub IoT":::

    ::: zone-end
    :::zone pivot="programming-language-csharp"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/hub-registration-csharp.png" alt-text="L’appareil CSharp est inscrit auprès du hub IoT":::

    ::: zone-end

    :::zone pivot="programming-language-nodejs"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/hub-registration-nodejs.png" alt-text="L’appareil Node.js est inscrit auprès du hub IoT":::

    ::: zone-end

    :::zone pivot="programming-language-python"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/hub-registration-python.png" alt-text="L’appareil Python est inscrit auprès du hub IoT":::

    ::: zone-end

    ::: zone pivot="programming-language-java"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/hub-registration-java.png" alt-text="L’appareil Java est inscrit auprès du hub IoT":::

    ::: zone-end

> [!NOTE]
> Si vous avez modifié la valeur par défaut de l’*état du jumeau d’appareil initial* dans l’entrée d’inscription de votre appareil, l’état du jumeau souhaité peut être extrait du hub et agir en conséquence. Pour en savoir plus, consultez [Comprendre et utiliser les jumeaux d’appareil IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).
>

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous envisagez de continuer à manipuler et à explorer l’exemple de client d’appareil, ne nettoyez pas les ressources créées dans ce guide de démarrage rapide. Sinon, effectuez les étapes suivantes pour supprimer toutes les ressources créées par ce guide de démarrage rapide.

### <a name="delete-your-device-enrollment"></a>Supprimer l’inscription de votre appareil

1. Fermez la fenêtre de sortie de l’exemple de client d’appareil sur votre machine.

2. Dans le menu de gauche du portail Azure, sélectionnez **Toutes les ressources**.

3. Sélectionnez votre service Device Provisioning.

4. Dans le menu **Paramètres**, sélectionnez **Gérer les inscriptions**.

5. Sélectionnez l’onglet **Inscriptions individuelles**.

6. Cochez la case à côté de l’*ID D’INSCRIPTION* de l’appareil que vous avez inscrit dans ce guide de démarrage rapide.

7. En haut de la page, sélectionnez **Supprimer**.

### <a name="delete-your-device-registration-from-iot-hub"></a>Supprimer l’inscription de votre appareil dans IoT Hub

1. Dans le menu de gauche du portail Azure, sélectionnez **Toutes les ressources**.

2. Sélectionnez votre hub IoT.

3. Dans le menu **Explorateurs**, sélectionnez **Appareils IoT**.

4. Cochez la case à côté de l’*ID D’APPAREIL* de l’appareil que vous avez inscrit dans ce guide de démarrage rapide.

5. En haut de la page, sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Provisionner un appareil à certificat X.509 :

> [!div class="nextstepaction"]
> [Démarrage rapide - Provisionner un appareil X.509 à l’aide du SDK C Azure IoT](quick-create-simulated-device-x509.md)