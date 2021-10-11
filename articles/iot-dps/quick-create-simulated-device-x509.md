---
title: Démarrage rapide - Provisionner un appareil simulé avec certificat X.509 dans Microsoft Azure IoT Hub
description: Découvrir comment provisionner un appareil simulé qui s’authentifie avec un certificat X.509 dans le service Azure IoT Hub Device Provisioning
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/07/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: lizross
ms.custom: mvc
zone_pivot_groups: iot-dps-set1
ms.openlocfilehash: c0c3d486e2a886a49c51e7ed78ed935c1a1c5c22
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129276631"
---
# <a name="quickstart-provision-an-x509-certificate-simulated-device"></a>Démarrage rapide : Provisionner un appareil simulé avec certificat X.509

Dans ce guide de démarrage rapide, vous allez créer un appareil simulé sur votre machine Windows. L’appareil simulé sera configuré pour utiliser le mécanisme d’[attestation de certificat X.509](concepts-x509-attestation.md) pour l’authentification. Une fois que vous aurez configuré votre appareil, vous le configurerez sur votre hub IoT à l’aide du service Azure IoT Hub Device Provisioning.

Si vous ne connaissez pas le processus de provisionnement, passez en revue la vue d’ensemble du [provisionnement](about-iot-dps.md#provisioning-process).  Vérifiez également que vous avez suivi la procédure décrite dans [Configurer le service d’approvisionnement d’appareil IoT Hub avec le portail Azure](./quick-setup-auto-provision.md) avant de continuer.

Ce guide de démarrage rapide présente une solution pour une station de travail Windows. Vous pouvez toutefois également effectuer les procédures sur Linux. Pour obtenir un exemple sur Linux, consultez [Guide pratique du provisionnement pour la multilocation](how-to-provision-multitenant.md).

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.

* Effectuez les étapes décrites dans [Configurer le service IoT Hub Device Provisioning avec le portail Azure](./quick-setup-auto-provision.md).

Les prérequis suivants s’appliquent à un environnement de développement Windows. Pour Linux ou macOS, consultez la section appropriée de [Préparer votre environnement de développement](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) dans la documentation du kit de développement logiciel (SDK).

::: zone pivot="programming-language-ansi-c"

* Installez [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 avec la charge de travail [« Développement Desktop en C++ »](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) activée. Visual Studio 2015 et Visual Studio 2017 sont également pris en charge. Pour Linux ou macOS, consultez la section appropriée de [Préparer votre environnement de développement](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) dans la documentation du kit de développement logiciel (SDK).

::: zone-end

::: zone pivot="programming-language-csharp"

* Installez le [kit SDK .NET Core 3.1 ou une version ultérieure](https://dotnet.microsoft.com/download) sur votre machine Windows. Vous pouvez exécuter la commande suivante pour vérifier votre version.

    ```bash
    dotnet --info
    ```

::: zone-end

::: zone pivot="programming-language-nodejs"

* Installez [Node.js v4.0 ou une version ultérieure](https://nodejs.org) sur votre machine.

* Installez [OpenSSL](https://www.openssl.org/) sur votre machine et ajoutez-le aux variables d’environnement accessibles à la fenêtre de commande. Cette bibliothèque peut être générée et installée à partir d’une source ou téléchargée et installée à partir d’un [tiers](https://wiki.openssl.org/index.php/Binaries), par exemple [celui-ci](https://sourceforge.net/projects/openssl/).

::: zone-end

::: zone pivot="programming-language-python"

* [Python 3.6 ou une version ultérieure](https://www.python.org/downloads/) sur votre machine.

* Installez [OpenSSL](https://www.openssl.org/) sur votre machine et ajoutez-le aux variables d’environnement accessibles à la fenêtre de commande. Cette bibliothèque peut être générée et installée à partir d’une source ou téléchargée et installée à partir d’un [tiers](https://wiki.openssl.org/index.php/Binaries), par exemple [celui-ci](https://sourceforge.net/projects/openssl/).

::: zone-end

::: zone pivot="programming-language-java"

* Installez [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-support-on-azure) ou une version ultérieure sur votre machine.

* Téléchargez et installez [Maven](https://maven.apache.org/install.html).

::: zone-end

* Installez la dernière version de [Git](https://git-scm.com/download/). Vérifiez que Git est ajouté aux variables d’environnement accessibles à la fenêtre de commande. Consultez [Outils clients Git de Software Freedom Conservancy](https://git-scm.com/download/) pour accéder à la dernière version des outils `git` à installer, ce qui inclut *Git Bash*, l’application en ligne de commande que vous pouvez utiliser pour interagir avec votre dépôt Git local.

## <a name="prepare-your-development-environment"></a>Préparer votre environnement de développement

::: zone pivot="programming-language-ansi-c"

Dans cette section, vous allez préparer un environnement de développement qui est utilisé pour générer le [kit SDK C Azure IoT ](https://github.com/Azure/azure-iot-sdk-c). L’exemple de code tente de provisionner l’appareil pendant la séquence de démarrage de ce dernier.

1. Téléchargez le dernier [système de génération CMake](https://cmake.org/download/).

    >[!IMPORTANT]
    >Vérifiez que les prérequis de Visual Studio (Visual Studio et la charge de travail « Développement Desktop en C++ ») sont installés sur votre machine **avant** de commencer l’installation de `CMake`. Une fois les composants requis en place et le téléchargement effectué, installez le système de génération de CMake. Sachez également que les versions antérieures du système de génération CMake ne permettent pas de générer le fichier solution utilisé dans cet article. Veillez à utiliser la dernière version de CMake.

2. Ouvrez un navigateur web, puis accédez à la [page des versions du kit SDK C Azure IoT](https://github.com/Azure/azure-iot-sdk-c/releases/latest).

3. Sélectionnez l’onglet **Étiquettes** en haut de la page.

4. Copiez le nom d’étiquette de la version la plus récente du SDK C Azure IoT.

5. Ouvrez une invite de commandes ou l’interpréteur de commandes Git Bash. Exécutez les commandes suivantes pour cloner la dernière version du dépôt GitHub du [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). (Remplacez `<release-tag>` par l’étiquette que vous avez copiée à l’étape précédente.)

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

7. L’exemple de code utilise un certificat X.509 pour fournir l’attestation via l’authentification X.509. Exécutez la commande suivante pour générer une version du SDK propre à votre plateforme de développement qui inclut le client de provisionnement des appareils. Une solution Visual Studio pour l’appareil simulé est générée dans le répertoire `cmake`.

    ```cmd
    cmake -Duse_prov_client:BOOL=ON ..
    ```

    >[!TIP]
    >Si `cmake` ne trouve pas votre compilateur C++, vous risquez de rencontrer des erreurs de génération lors de l’exécution de la commande ci-dessus. Si cela se produit, essayez d’exécuter la commande dans [l’invite de commandes de Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs).

8. Quand la génération aboutit, les dernières lignes de la sortie ressemblent à la sortie suivante :

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON ..
    -- Building for: Visual Studio 16 2019
    -- The C compiler identification is MSVC 19.23.28107.0
    -- The CXX compiler identification is MSVC 19.23.28107.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: C:/code/azure-iot-sdk-c/cmake
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

2. Clonez le dépôt GitHub [Exemples Azure IoT pour Node.js](https://github.com/Azure/azure-iot-sdk-node.git) à l’aide de la commande suivante :

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-node.git
    ```

::: zone-end

::: zone pivot="programming-language-python"

1. Ouvrez un environnement de ligne de commande Git CMD ou Git Bash.

2. Clonez le dépôt GitHub [Exemples Azure IoT pour Python](https://github.com/Azure/azure-iot-sdk-node.git) à l’aide de la commande suivante :

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```

::: zone-end

::: zone pivot="programming-language-java"

1. Ouvrez un environnement de ligne de commande Git CMD ou Git Bash.

2. Clonez le dépôt GitHub [Exemples Azure IoT pour Java](https://github.com/Azure/azure-iot-sdk-java.git) à l’aide de la commande suivante :

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

3. Accédez au répertoire `azure-iot-sdk-`java racine, puis générez le projet pour télécharger tous les packages nécessaires.

   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```

4. Accédez au projet de générateur de certificats, puis générez le projet.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator
    mvn clean install
    ```

::: zone-end

## <a name="create-a-self-signed-x509-device-certificate"></a>Créer un certificat auto-signé d’appareil X.509

Dans cette section, vous allez utiliser l’exemple de code du SDK Azure IoT pour créer un certificat X.509 auto-signé. Ce certificat doit être chargé vers votre service d’approvisionnement, puis vérifié par le service.

> [!CAUTION]
> Utilisez les certificats créés avec les outils du SDK seulement pour le test de développements.
> N’utilisez pas ces certificats en production.
> Les certificats générés par le SDK contiennent des mots de passe codés en dur, comme *1234*, et expirent au bout de 30 jours.
> Pour en savoir plus sur l’obtention de certificats appropriés pour la production, consultez [Guide pratique pour obtenir un certificat d’autorité de certification X.509](../iot-hub/iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate) dans la documentation Azure IoT Hub.
>

::: zone pivot="programming-language-csharp"

L’ID d’appareil de l’appareil IoT sera le nom commun de l’objet dans le certificat. Veillez à utiliser un nom d’objet conforme aux [exigences relatives aux chaînes d’ID d’appareil](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties).

::: zone-end

Pour créer le certificat X.509 :

::: zone pivot="programming-language-ansi-c"

### <a name="clone-the-azure-iot-c-sdk"></a>Cloner le SDK C Azure IoT

Le [SDK C Azure IoT](https://github.com/Azure/azure-iot-sdk-c) contient des outils de test qui peuvent vous aider à créer une chaîne de certificats X.509, à charger un certificat racine ou intermédiaire à partir de cette chaîne et à effectuer une preuve de possession avec le service pour vérifier le certificat.

Si vous avez déjà cloné la dernière version du dépôt GitHub [SDK C Azure IoT](https://github.com/Azure/azure-iot-sdk-c), passez à la [section suivante](#create-a-test-certificate).

1. Ouvrez un navigateur web, puis accédez à la [page des versions du kit SDK C Azure IoT](https://github.com/Azure/azure-iot-sdk-c/releases/latest).

2. Copiez le nom d’étiquette de la version la plus récente du SDK C Azure IoT.

3. Ouvrez une invite de commandes ou l’interpréteur de commandes Git Bash. Exécutez les commandes suivantes pour cloner la dernière version du dépôt GitHub du [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). (Remplacez `<release-tag>` par l’étiquette que vous avez copiée à l’étape précédente.)

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Cette opération peut prendre plusieurs minutes.

4. Les outils de test doivent maintenant se trouver dans le dossier *azure-iot-sdk-c/tools/CACertificates* du dépôt que vous avez cloné.

### <a name="create-a-test-certificate"></a>Créer un certificat de test

Suivez les étapes dans [Gestion de certificats d’autorité de certification de test pour des exemples et tutoriels](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

En plus des outils du SDK C, l’[Exemple de vérification de certificat de groupe](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample) du *SDK Microsoft Azure IoT pour .NET* montre comment effectuer une preuve de possession en C# avec un certificat racine ou intermédiaire d’autorité de certification X.509 existant.

::: zone-end

::: zone pivot="programming-language-csharp"

1. À partir d’une invite PowerShell, accédez au répertoire du projet contenant l’exemple de provisionnement d’appareil X.509.

    ```powershell
    cd .\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample
    ```

2. Cet exemple de code est configuré pour utiliser des certificats X.509 qui sont stockés dans un fichier PKCS12 protégé par mot de passe (`certificate.pfx`). En outre, vous aurez besoin d’un fichier de certificat de clé publique (`certificate.cer`) pour créer, ultérieurement dans ce guide de démarrage rapide, une inscription individuelle. Pour générer le certificat auto-signé et ses fichiers `.cer` et `.pfx` associés, exécutez la commande suivante :

    ```powershell
    PS D:\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample> .\GenerateTestCertificate.ps1 iothubx509device1
    ```

3. Le script vous invite à saisir un mot de passe PFX. Notez bien ce mot de passe, car vous l’utiliserez plus tard pour exécuter l’exemple. Vous pouvez éventuellement exécuter `certutil` pour sauvegarder le certificat et vérifier le nom de l’objet.

    ```powershell
    PS D:\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample> certutil .\certificate.pfx
    Enter PFX password:
    ================ Certificate 0 ================
    ================ Begin Nesting Level 1 ================
    Element 0:
    Serial Number: 7b4a0e2af6f40eae4d91b3b7ff05a4ce
    Issuer: CN=iothubx509device1, O=TEST, C=US
     NotBefore: 2/1/2021 6:18 PM
     NotAfter: 2/1/2022 6:28 PM
    Subject: CN=iothubx509device1, O=TEST, C=US
    Signature matches Public Key
    Root Certificate: Subject matches Issuer
    Cert Hash(sha1): e3eb7b7cc1e2b601486bf8a733887a54cdab8ed6
    ----------------  End Nesting Level 1  ----------------
      Provider = Microsoft Strong Cryptographic Provider
    Signature test passed
    CertUtil: -dump command completed successfully.    
    ```

::: zone-end

::: zone pivot="programming-language-nodejs"

1. Ouvrez une invite de commandes, puis accédez au script du générateur de certificats et générez le projet :

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

2. Créez un certificat X.509 _feuille_ en exécutant le script à l’aide de votre _nom-certificat_. Le nom commun du certificat feuille devient alors [l’ID d’inscription](./concepts-service.md#registration-id). Veillez donc à utiliser uniquement des caractères alphanumériques minuscules et des traits d’union.

    ```cmd/sh
    node create_test_cert.js device {certificate-name}
    ```

::: zone-end

::: zone pivot="programming-language-python"

1. Dans l’invite Git Bash, exécutez la commande suivante :

    # <a name="windows"></a>[Windows](#tab/windows)

    ```bash
    winpty openssl req -outform PEM -x509 -sha256 -newkey rsa:4096 -keyout ./python-device.key.pem -out ./python-device.pem -days 365 -extensions usr_cert -subj "//CN=Python-device-01"
    ```

    > [!IMPORTANT]
    > La barre oblique supplémentaire spécifiée dans le nom d’objet (`//CN=Python-device-01`) est nécessaire uniquement pour l’échappement de la chaîne avec Git sur les plateformes Windows.

    # <a name="linux"></a>[Linux](#tab/linux)

    ```bash
    openssl req -outform PEM -x509 -sha256 -newkey rsa:4096 -keyout ./python-device.key.pem -out ./python-device.pem -days 365 -extensions usr_cert -subj "/CN=Python-device-01"
    ```

    ---

2. Quand vous êtes invité à **entrer la phrase secrète PEM**, utilisez la phrase secrète `1234`.

3. Quand vous êtes invité à **vérifier la phrase secrète PEM**, réutilisez la phrase secrète `1234`.

Un fichier de certificat de test (*python-device.pem*) et un fichier de clé privée (*python-device.key.pem*) doivent maintenant être générés dans le répertoire où vous avez exécuté la commande `openssl`.

::: zone-end

::: zone pivot="programming-language-java"

1. À l’aide de l’invite de commandes des étapes précédentes, accédez au dossier `target`.

2. Exécutez le fichier .jar créé dans la section précédente.

    ```cmd/sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

3. Entrez **N** pour _Do you want to input common name_ (Voulez-vous saisir le nom commun ?). 

4. Copiez la sortie de `Client Cert` dans le Presse-papiers, de *-----BEGIN CERTIFICATE-----* à *-----END CERTIFICATE-----* .

   ![Générateur de certificats individuels](./media/quick-create-simulated-device-x509/cert-generator-java.png)

5. Créez un fichier nommé *_X509individual.pem_* sur votre machine Windows.

6. Ouvrez *_X509individual.pem_* dans l’éditeur de votre choix, puis copiez le contenu du Presse-papiers dans ce fichier. 

7. Enregistrez le fichier et fermez votre éditeur.

8. À l’invite de commandes, entrez **N** pour _Do you want to input Verification Code_ (Voulez-vous entrer le code de validation ?), puis gardez la sortie du programme ouverte pour vous en servir plus tard dans ce guide de démarrage rapide. Copiez les valeurs `Client Cert` et `Client Cert Private Key` pour les utiliser dans la prochaine section.

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

::: zone pivot="programming-language-ansi-c"

6. Dans la page **Ajouter une inscription**, entrez les informations suivantes.

    * **Mécanisme :** Sélectionnez **X.509** comme *mécanisme* d’attestation d’identité.
    * **Fichier .pem ou .cer du certificat principal :** choisissez **Sélectionner un fichier** pour sélectionner le fichier de certificat *X509testcert.pem* que vous avez créé dans la section précédente.
    * **ID de l’appareil IoT Hub :** Entrez *test-docs-cert-device* pour donner un ID à l’appareil.

    :::image type="content" source="./media/quick-create-simulated-device-x509/device-enrollment.png" alt-text="Ajouter un appareil comme inscription individuelle avec l’attestation X.509.":::

::: zone-end

::: zone pivot="programming-language-csharp"

6. Dans la page **Ajouter une inscription**, entrez les informations suivantes.

    * **Mécanisme :** Sélectionnez **X.509** comme *mécanisme* d’attestation d’identité.
    * **Fichier .pem ou .cer du certificat principal :** choisissez **Sélectionner un fichier** pour sélectionner le fichier de certificat *certificate.cer* que vous avez créé dans la section précédente.
    * Laissez **ID de l’appareil IoT Hub :** vide. Votre appareil va être approvisionné. Son ID est défini sur le nom commun (CN) dans le certificat X.509, soit *iothubx509device1*. Ce nom commun sera également le nom utilisé pour l’ID d’inscription de l’entrée d’inscription individuelle.
    * Si vous le souhaitez, vous pouvez fournir les informations suivantes :
        * Sélectionnez un hub IoT lié à votre service d’approvisionnement.
        * Mettez à jour l’**état du jumeau d’appareil initial** à l’aide de la configuration initiale de votre choix pour l’appareil.

    :::image type="content" source="./media/quick-create-simulated-device-x509/device-enrollment.png" alt-text="Ajouter un appareil comme inscription individuelle avec l’attestation X.509.":::

::: zone-end

::: zone pivot="programming-language-nodejs"

6. Dans la page **Ajouter une inscription**, entrez les informations suivantes.

    * **Mécanisme :** Sélectionnez **X.509** comme *mécanisme* d’attestation d’identité.
    * **Fichier .pem ou .cer du certificat principal :** choisissez **Sélectionner un fichier** pour sélectionner le fichier de certificat *{certificate-name}_cert.pem* que vous avez créé dans la section précédente.
    * Si vous le souhaitez, vous pouvez fournir les informations suivantes :
        * Sélectionnez un hub IoT lié à votre service d’approvisionnement.
        * Entrez un ID d’appareil unique. Veillez à éviter les données sensibles lorsque vous affectez un nom à votre appareil.
        * Mettez à jour l’**état du jumeau d’appareil initial** à l’aide de la configuration initiale de votre choix pour l’appareil.
    :::image type="content" source="./media/quick-create-simulated-device-x509/device-enrollment.png" alt-text="Ajouter un appareil comme inscription individuelle avec l’attestation X.509.":::

::: zone-end

::: zone pivot="programming-language-python"

6. Dans la page **Ajouter une inscription**, entrez les informations suivantes.

    * **Mécanisme :** Sélectionnez **X.509** comme *mécanisme* d’attestation d’identité.
    * **Fichier .pem ou .cer du certificat principal :** choisissez **Sélectionner un fichier** pour sélectionner le fichier de certificat *python-device.pem* si vous utilisez le certificat de test créé précédemment.
    * Si vous le souhaitez, vous pouvez fournir les informations suivantes :
        * Sélectionnez un hub IoT lié à votre service d’approvisionnement.
        * Mettez à jour l’**état du jumeau d’appareil initial** à l’aide de la configuration initiale de votre choix pour l’appareil.

    :::image type="content" source="./media/quick-create-simulated-device-x509/device-enrollment.png" alt-text="Ajouter un appareil comme inscription individuelle avec l’attestation X.509.":::

::: zone-end

::: zone pivot="programming-language-java"

6. Dans le volet **Ajouter une inscription**, entrez les informations suivantes :
   * Sélectionnez **X.509** comme *mécanisme* d’attestation d’identité.
   * Sous le *fichier .pem ou .cer du certificat principal*, choisissez *Select a file* (Sélectionner un fichier) pour sélectionner le fichier de certificat *X509individual.pem* créé au cours des étapes précédentes.  
   * Si vous le souhaitez, vous pouvez fournir les informations suivantes :
     * Sélectionnez un hub IoT lié à votre service d’approvisionnement.
     * Entrez un ID d’appareil unique. Veillez à éviter les données sensibles lorsque vous affectez un nom à votre appareil. 
     * Mettez à jour l’**état du jumeau d’appareil initial** à l’aide de la configuration initiale de votre choix pour l’appareil.
    
    :::image type="content" source="./media/quick-create-simulated-device-x509/device-enrollment.png" alt-text="Ajouter un appareil comme inscription individuelle avec l’attestation X.509.":::

::: zone-end
    
7. Sélectionnez **Enregistrer**. Vous êtes redirigé vers la page **Gérer les inscriptions**.

8. Sélectionnez **Inscriptions individuelles**. Votre entrée d’inscription X.509 doit apparaître dans la table d’inscription.

## <a name="prepare-and-run-the-device-provisioning-code"></a>Préparer et exécuter le code de provisionnement d’un appareil

::: zone pivot="programming-language-ansi-c"

Dans cette section, nous allons mettre à jour l’exemple de code pour envoyer la séquence de démarrage de l’appareil à votre instance de service Device Provisioning. Cette séquence de démarrage entraîne la reconnaissance et l’affectation de l’appareil à un hub IoT lié à l’instance de service Device Provisioning.

1. Dans le portail Azure, sélectionnez l’onglet **Vue d’ensemble** de votre service Device Provisioning.

2. Copiez la valeur **_Étendue de l’ID_**.

    :::image type="content" source="./media/quick-create-simulated-device-x509/copy-id-scope.png" alt-text="Copier l’étendue de l’ID à partir du portail.":::

3. Dans la fenêtre *Explorateur de solutions* de Visual Studio, accédez au dossier **Provision\_Samples**. Développez l’exemple de projet nommé **prov\_dev\_client\_sample**. Développez **Fichiers sources**, puis ouvrez **prov\_dev\_client\_sample.c**.

4. Recherchez la constante `id_scope` et remplacez la valeur par la valeur **Étendue de l’ID** que vous avez copiée précédemment. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Recherchez la définition de la fonction `main()` dans le même fichier. Assurez-vous que la variable `hsm_type` est définie sur `SECURE_DEVICE_TYPE_X509` au lieu de `SECURE_DEVICE_TYPE_TPM` comme indiqué ci-dessous.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

6. Cliquez avec le bouton droit sur le projet **prov\_dev\_client\_sample** et sélectionnez **Définir comme projet de démarrage**.

7. Dans le menu Visual Studio, sélectionnez **Déboguer** > **Exécuter sans débogage** pour exécuter la solution. Dans l’invite pour régénérer le projet, sélectionnez **Oui** pour régénérer le projet avant de l’exécuter.

    La sortie suivante illustre un exemple d’approvisionnement d’un client d’appareil, son démarrage réussi, sa connexion à une instance de service Device Provisioning pour obtenir des informations sur le hub IoT et son inscription :

    ```cmd
    Provisioning API Version: 1.2.7

    Registering... Press enter key to interrupt.

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service:
    test-docs-hub.azure-devices.net, deviceId: test-docs-cert-device
    ```

::: zone-end

::: zone pivot="programming-language-csharp"

1. Dans le portail Azure, sélectionnez l’onglet **Vue d’ensemble** de votre service Device Provisioning.

2. Copiez la valeur **_Étendue de l’ID_**.

    :::image type="content" source="./media/quick-create-simulated-device-x509/copy-id-scope.png" alt-text="Copier l’étendue de l’ID à partir du portail.":::

3. Ouvrir une fenêtre d’invite de commandes.

4. Tapez la commande suivante pour générer et exécuter l’exemple de provisionnement d’appareil X.509 (en remplaçant la valeur `<IDScope>` par l’étendue de l’ID que vous avez copiée dans la section précédente). Par défaut, le fichier de certificat est *./certificate.pfx* et vous invite à entrer le mot de passe .pfx. Tapez votre mot de passe.

    ```powershell
    dotnet run -- -s <IDScope>
    ```

    Si vous souhaitez tout passer en paramètre, utilisez le format d’exemple suivant.

    ```powershell
    dotnet run -- -s 0ne00000A0A -c certificate.pfx -p 1234
    ```

5. L’appareil se connecte maintenant au service DPS et est affecté à un hub IoT. Ensuite, l’appareil envoie un message de télémétrie au hub.

    ```output
    Loading the certificate...
    Found certificate: 10952E59D13A3E388F88E534444484F52CD3D9E4 CN=iothubx509device1, O=TEST, C=US; PrivateKey: True
    Using certificate 10952E59D13A3E388F88E534444484F52CD3D9E4 CN=iothubx509device1, O=TEST, C=US
    Initializing the device provisioning client...
    Initialized for registration Id iothubx509device1.
    Registering with the device provisioning service...
    Registration status: Assigned.
    Device iothubx509device2 registered to sample-iot-hub1.azure-devices.net.
    Creating X509 authentication for IoT Hub...
    Testing the provisioned device with IoT Hub...
    Sending a telemetry message...
    Finished.
    ```

::: zone-end

::: zone pivot="programming-language-nodejs"

1. Dans le portail Azure, sélectionnez l’onglet **Vue d’ensemble** de votre service Device Provisioning. 

2. Copiez les valeurs **_Étendue de l’ID_** et **Point de terminaison d’appareil global**.

    :::image type="content" source="./media/quick-create-simulated-device-x509/copy-id-scope-and-global-device-endpoint.png" alt-text="Copier l’étendue de l’ID à partir du portail.":::

3. Copiez votre _certificat_ et votre _clé_ dans le dossier d’exemples.

    ```cmd/sh
    copy .\{certificate-name}_cert.pem ..\device\samples\{certificate-name}_cert.pem
    copy .\{certificate-name}_key.pem ..\device\samples\{certificate-name}_key.pem
    ```

4. Accédez au script de test d’appareil et générez le projet.

    ```cmd/sh
    cd ..\device\samples
    npm install
    ```

5. Modifiez le fichier **register\_x509.js** avec les changements suivants :

    * Remplacez `provisioning host` par la valeur **_Point de terminaison d’appareil global_** notée à **l’étape 1** ci-dessus.
    * Remplacez `id scope` par la valeur **_Étendue de l’ID_** notée à l’**étape 1** ci-dessus. 
    * Remplacez `registration id` par la valeur **_ID d’inscription_** notée dans la section précédente.
    * Remplacez `cert filename` et `key filename` par les fichiers que vous avez copiés à **l’étape 2** ci-dessus.

6. Enregistrez le fichier .

7. Exécutez le script et vérifiez que l’appareil a été provisionné correctement.

    ```cmd/sh
    node register_x509.js
    ``` 

>[!TIP]
>Le [Kit de développement logiciel (SDK) Azure IoT Hub Node.js Device SDK](https://github.com/Azure/azure-iot-sdk-node) permet de simuler facilement un appareil. Pour plus d’informations, consultez [Concepts de l’appareil](./concepts-service.md).

::: zone-end

::: zone pivot="programming-language-python"

L’exemple de provisionnement Python, [provision_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/provision_x509.py) se trouve dans le répertoire `azure-iot-sdk-python/azure-iot-device/samples/async-hub-scenarios`. Cet exemple utilise six variables d’environnement pour authentifier et provisionner un appareil IoT à l’aide du service DPS. Ces variables d’environnement sont :

| Nom de la variable              | Description                                     |
| :------------------------- | :---------------------------------------------- |
| `PROVISIONING_HOST`        |  Cette valeur est le point de terminaison global utilisé pour la connexion à votre ressource DPS |    
| `PROVISIONING_IDSCOPE`     |  Cette valeur est l’étendue de l’ID de votre ressource DPS |    
| `DPS_X509_REGISTRATION_ID` |  Cette valeur est l’ID de votre appareil. Elle doit également correspondre au nom d’objet sur le certificat de l’appareil |    
| `X509_CERT_FILE`           |  Nom de fichier du certificat de l’appareil |    
| `X509_KEY_FILE`            |  Nom de fichier de la clé privée du certificat de votre appareil |
| `PASS_PHRASE`              |  Phrase secrète utilisée pour chiffrer le certificat et le fichier de clé privée (`1234`). |    

1. Dans le portail Azure, sélectionnez l’onglet **Vue d’ensemble** de votre service Device Provisioning.

2. Copiez les valeurs **_Étendue de l’ID_** et **Point de terminaison d’appareil global**.

    :::image type="content" source="./media/quick-create-simulated-device-x509/copy-id-scope-and-global-device-endpoint.png" alt-text="Copier l’étendue de l’ID à partir du portail.":::

3. Dans votre invite Git Bash, utilisez les commandes suivantes pour ajouter les variables d’environnement du point de terminaison d’appareil global et l’étendue de l’ID.

    ```bash
    $export PROVISIONING_HOST=global.azure-devices-provisioning.net
    $export PROVISIONING_IDSCOPE=<ID scope for your DPS resource>
    ```

4. L’ID d’inscription de l’appareil IoT doit correspondre au nom d’objet sur son certificat d’appareil. Si vous avez généré un certificat de test autosigné, `Python-device-01` correspond à la fois au nom d’objet et à l’ID d’inscription de l’appareil.

    Si vous disposez déjà d’un certificat d’appareil, vous pouvez utiliser `certutil` pour vérifier le nom commun d’objet utilisé pour votre appareil, comme indiqué ci-dessous :

    ```bash
    $ certutil python-device.pem
    X509 Certificate:
    Version: 3
    Serial Number: fa33152fe1140dc8
    Signature Algorithm:
        Algorithm ObjectId: 1.2.840.113549.1.1.11 sha256RSA
        Algorithm Parameters:
        05 00
    Issuer:
        CN=Python-device-01
      Name Hash(sha1): 1dd88de40e9501fb64892b698afe12d027011000
      Name Hash(md5): a62c784820daa931b9d3977739b30d12
    
     NotBefore: 1/29/2021 7:05 PM
     NotAfter: 1/29/2022 7:05 PM
    
    Subject:
        ===> CN=Python-device-01 <===
      Name Hash(sha1): 1dd88de40e9501fb64892b698afe12d027011000
      Name Hash(md5): a62c784820daa931b9d3977739b30d12
    ```

5. Dans l’invite Git Bash, définissez la variable d’environnement de l’ID d’inscription de la façon suivante :

    ```bash
    $export DPS_X509_REGISTRATION_ID=Python-device-01
    ```

6. Dans l’invite Git Bash, définissez les variables d’environnement du fichier de certificat, du fichier de clé privée et de la phrase secrète.

    ```bash
    $export X509_CERT_FILE=./python-device.pem
    $export X509_KEY_FILE=./python-device.key.pem
    $export PASS_PHRASE=1234
    ```

7. Passez en revue le code pour [provision_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/provision_x509.py). Si vous n’utilisez pas **Python version 3.7** ou une version ultérieure, changez le [code indiqué ici](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios#advanced-iot-hub-scenario-samples-for-the-azure-iot-hub-device-sdk) pour remplacer `asyncio.run(main())`.

8. Enregistrez vos modifications.

9. Exécutez l’exemple. L’exemple permet de se connecter, de provisionner l’appareil auprès d’un hub et d’envoyer des messages de test au hub.

    ```bash
    $ winpty python azure-iot-sdk-python/azure-iot-device/samples/async-hub-scenarios/provision_x509.py
    RegistrationStage(RequestAndResponseOperation): Op will transition into polling after interval 2.  Setting timer.
    The complete registration result is
    Python-device-01
    TestHub12345.azure-devices.net
    initialAssignment
    null
    Will send telemetry from the provisioned device
    sending message #4
    sending message #7
    sending message #2
    sending message #8
    sending message #5
    sending message #9
    sending message #1
    sending message #6
    sending message #10
    sending message #3
    done sending message #4
    done sending message #7
    done sending message #2
    done sending message #8
    done sending message #5
    done sending message #9
    done sending message #1
    done sending message #6
    done sending message #10
    done sending message #3
    ```

::: zone-end

::: zone pivot="programming-language-java"

1. Dans le portail Azure, sélectionnez l’onglet **Vue d’ensemble** de votre service Device Provisioning.

2. Copiez les valeurs **_Étendue de l’ID_** et **Point de terminaison d’appareil global**.

    :::image type="content" source="./media/quick-create-simulated-device-x509/copy-id-scope-and-global-device-endpoint.png" alt-text="Copier l’étendue de l’ID à partir du portail.":::

3. Ouvrez une invite de commandes. Accédez à l’exemple de dossier de projet du référentiel du Kit de développement logiciel (SDK) Java.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

4. Entrez les informations relatives à l’identité X.509 et au service d’approvisionnement dans votre code. Elles sont utilisées lors du provisionnement, pour l’attestation de l’appareil simulé, avant l’inscription de l’appareil :

   * Modifiez le fichier `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java` pour inclure les valeurs _Étendue de l’ID_ et _Provisioning Service Global Endpoint_ (Point de terminaison global du service d’approvisionnement) notées précédemment. Insérez également les valeurs _Client Cert_ (Certificat client) et _Client Cert Private Key_ (Clé privée du certificat client) déjà utilisées.

      ```java
      private static final String idScope = "[Your ID scope here]";
      private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
      private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
      private static final String leafPublicPem = "<Your Public PEM Certificate here>";
      private static final String leafPrivateKey = "<Your Private PEM Key here>";
      ```

   * Utilisez le format suivant lorsque vous copiez/collez votre certificat et votre clé privée :
        
      ```java
      private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
      private static final String leafPrivateKey = "-----BEGIN PRIVATE KEY-----\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXX\n" +
            "-----END PRIVATE KEY-----\n";
      ```

5. Générez l’exemple, puis accédez au dossier `target` et exécutez le fichier .jar créé.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

::: zone-end

## <a name="confirm-your-device-provisioning-registration"></a>Confirmer l’inscription du provisionnement de votre appareil

1. Accédez au [portail Azure](https://portal.azure.com).

2. Dans le menu de gauche ou dans la page du portail, sélectionnez **Toutes les ressources**.

3. Sélectionnez le hub IoT auquel votre appareil a été attribué.

4. Dans le menu **Explorateurs**, sélectionnez **Appareils IoT**.

5. Si votre appareil a été provisionné correctement, l’ID de l’appareil doit apparaître dans la liste, avec l’**État** défini sur *Activé*. Si vous ne voyez pas votre appareil, sélectionnez **Actualiser** en haut de la page.

   :::zone pivot="programming-language-ansi-c"

    :::image type="content" source="./media/quick-create-simulated-device-x509/hub-registration.png" alt-text="L’appareil est inscrit avec le hub IoT":::

    ::: zone-end
    :::zone pivot="programming-language-csharp"

    :::image type="content" source="./media/quick-create-simulated-device-x509/hub-registration-csharp.png" alt-text="L’appareil CSharp est inscrit auprès du hub IoT":::

    ::: zone-end

    :::zone pivot="programming-language-nodejs"

    :::image type="content" source="./media/quick-create-simulated-device-x509/hub-registration-nodejs.png" alt-text="L’appareil Node.js est inscrit auprès du hub IoT":::

    ::: zone-end

    :::zone pivot="programming-language-python"

    :::image type="content" source="./media/quick-create-simulated-device-x509/hub-registration-python.png" alt-text="L’appareil Python est inscrit auprès du hub IoT":::

    ::: zone-end

    ::: zone pivot="programming-language-java"

    :::image type="content" source="./media/quick-create-simulated-device-x509/hub-registration-java.png" alt-text="L’appareil Java est inscrit auprès du hub IoT":::

    ::: zone-end


::: zone pivot="programming-language-csharp,programming-language-nodejs,programming-language-python,programming-language-java"

>[!IMPORTANT]
>Si vous avez modifié la valeur par défaut de l’*état du jumeau d’appareil initial* dans l’entrée d’inscription de votre appareil, l’état du jumeau souhaité peut être extrait du hub et agir en conséquence. Pour en savoir plus, consultez [Comprendre et utiliser les jumeaux d’appareil IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md)

::: zone-end

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

Pour savoir comment inscrire votre appareil X.509 par programmation :

> [!div class="nextstepaction"]
> [Démarrage rapide Azure - Inscrire des appareils X.509 auprès du service Azure IoT Hub Device Provisioning](quick-enroll-device-x509.md)