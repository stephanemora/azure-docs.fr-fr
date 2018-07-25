---
title: Approvisionner un appareil X.509 simulé auprès du service Azure IoT Hub à l’aide de Java | Microsoft Docs
description: 'Démarrage rapide d’Azure : Créer et approvisionner un appareil X.509 simulé auprès du service IoT Hub Device Provisioning à l’aide du Java Device SDK'
author: wesmc7777
ms.author: wesmc
ms.date: 04/09/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: 0c5eefbd6d7758ad2a7640a1fbff3435fcd1d315
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39091695"
---
# <a name="create-and-provision-a-simulated-x509-device-using-java-device-sdk-for-iot-hub-device-provisioning-service"></a>Créer et approvisionner un appareil X.509 simulé auprès du service IoT Hub Device Provisioning à l’aide du Java Device SDK
[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Ces étapes indiquent comment simuler un appareil X.509 sur votre ordinateur de développement exécutant le système d’exploitation Windows et comment utiliser l’exemple de code pour connecter cet appareil simulé au service Device Provisioning et à votre IoT hub. 

Si vous ne connaissez pas le processus d’approvisionnement automatique, pensez à consulter également [Concepts de provisionnement automatique](concepts-auto-provisioning.md). Vérifiez également que vous avez suivi la procédure décrite dans [Configurer le service d’approvisionnement d’appareil IoT Hub avec le portail Azure](./quick-setup-auto-provision.md) avant de continuer. 

## <a name="prepare-the-environment"></a>Préparer l’environnement 

1. Assurez-vous que le [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) est bien installé sur votre ordinateur.

2. Téléchargez et installez [Maven](https://maven.apache.org/install.html).

3. Assurez-vous que Git est installé sur votre machine et ajouté aux variables d’environnement accessibles à la fenêtre de commande. Consultez la section relative aux [outils clients de Software Freedom Conservancy](https://git-scm.com/download/) pour accéder à la dernière version des outils `git` à installer, qui inclut **Git Bash**, l’application de ligne de commande que vous pouvez utiliser pour interagir avec votre référentiel Git local. 

4. Ouvrez une invite de commandes. Clonez le référentiel GitHub pour l’exemple de code de simulation d’appareil :
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```
5. Accédez au répertoire `azure-iot-sdk-`java racine et créez le projet pour télécharger tous les packages nécessaires.
   
   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```
6. Accédez au projet de générateur de certificats et générez le projet. 

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator
    mvn clean install
    ```

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Créer un certificat d’appareil X.509 auto-signé et une entrée d’inscription individuelle

Dans cette section, vous allez utiliser un certificat X.509 auto-signé pour lequel il est important de garder à l’esprit les éléments suivants :

* Les certificats auto-signés sont destinés aux tests uniquement et ne doivent pas être utilisés en production.
* La date d’expiration par défaut d’un certificat auto-signé est de 1 an.

Vous allez utiliser l’exemple de code du [Kit de développement logiciel (SDK) Azure IoT pour Java](https://github.com/Azure/azure-iot-sdk-java.git) pour créer le certificat à utiliser avec l’entrée d’inscription individuelle de l’appareil simulé.


1. À l’aide de l’invite de commandes des étapes précédentes, accédez au dossier `target`, puis exécutez le fichier jar créé à l’étape précédente.

    ```cmd/sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

2. Entrez **N** pour _Do you want to input common name_ (Voulez-vous saisir le nom commun ?). Copiez dans le Presse-papiers la sortie de `Client Cert`, de *-----BEGIN CERTIFICATE-----* à *-----END CERTIFICATE-----*.

   ![Générateur de certificats individuels](./media/java-quick-create-simulated-device-x509/individual.png)

3. Créez un fichier nommé **_X509individual.pem_** sur votre ordinateur Windows, ouvrez-le dans l’éditeur de votre choix, puis copiez le contenu du Presse-papiers dans ce fichier. Enregistrez le fichier et fermez votre éditeur.

4. Dans l’invite de commandes, entrez **N** pour _Do you want to input Verification Code_ (Voulez-vous saisir le code de validation ?), puis gardez la sortie du programme ouverte pour vous en servir plus tard dans ce Démarrage rapide. Vous copierez plus tard les valeurs `Client Cert` et `Client Cert Private Key` pour les utiliser dans la section suivante.

5. Connectez-vous au [portail Azure](https://portal.azure.com), cliquez sur le bouton **Toutes les ressources** dans le menu de gauche et ouvrez votre instance Device Provisioning Service.

6. Dans le panneau de résumé du service Device Provisioning, sélectionnez **Gérer les inscriptions**. Sélectionnez l’onglet **Inscriptions individuelles** et cliquez sur le bouton **Ajouter** dans la partie supérieure. 

7. Dans le volet **Add enrollment** (Ajouter une inscription), entrez les informations suivantes :
    - Sélectionnez **X.509** comme *mécanisme* d’attestation d’identité.
    - Sous le *fichier .pem ou .cer du certificat principal*, cliquez sur *Select a file* (Sélectionner un fichier) pour sélectionner le fichier de certificat **X509individual.pem** créé au cours des étapes précédentes.  
    - Si vous le souhaitez, vous pouvez fournir les informations suivantes :
      - Sélectionnez un hub IoT lié à votre service d’approvisionnement.
      - Entrez un ID d’appareil unique. Veillez à éviter les données sensibles lorsque vous affectez un nom à votre appareil. 
      - Mettez à jour l’**état du jumeau d’appareil initial** à l’aide de la configuration initiale de votre choix pour l’appareil.
   - Cela fait, cliquez sur le bouton **Enregistrer**. 

    [![Ajouter une inscription individuelle pour l’attestation X.509 dans le portail](./media/quick-create-simulated-device-x509-csharp/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

     Lorsque l’inscription aboutit, votre appareil X.509 apparaît en tant que **microsoftriotcore** sous la colonne *ID d’inscription* de l’onglet *Inscriptions individuelles*. 



## <a name="simulate-the-device"></a>Simuler l’appareil

1. Dans le panneau de résumé du service Device Provisioning, sélectionnez **Vue d’ensemble**, puis notez les valeurs _ID Scope_ (Étendue d’ID) et _Provisioning Service Global Endpoint_ (Point de terminaison global du service d’approvisionnement).

    ![Informations sur le service](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Ouvrez une invite de commandes. Accédez à l’exemple de dossier de projet du référentiel du Kit de développement logiciel (SDK) Java.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

3. Entrez les informations relatives à l’identité X.509 et au service d’approvisionnement dans votre code. Elles sont utilisées lors de l’approvisionnement automatique, pour l’attestation de l’appareil simulé, avant l’inscription de l’appareil :

   - Modifiez le fichier `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java` pour inclure les valeurs _Étendue de l’ID_ et _Provisioning Service Global Endpoint_ (Point de terminaison global du service d’approvisionnement) notées précédemment. Insérez également les valeurs _Client Cert_ (Certificat client) et _Client Cert Private Key_ (Clé privée du certificat client) déjà utilisées.

      ```java
      private static final String idScope = "[Your ID scope here]";
      private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
      private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
      private static final String leafPublicPem = "<Your Public PEM Certificate here>";
      private static final String leafPrivateKey = "<Your Private PEM Key here>";
      ```

   - Utilisez le format suivant lorsque vous copiez/collez votre certificat et votre clé privée :
        
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

4. Générez l’exemple. Accédez au dossier `target` et exécutez le fichier jar créé.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

5. Dans le portail Azure, accédez au IoT Hub lié à votre service d’approvisionnement et ouvrez le panneau **Device Explorer**. En cas de réussite de l’approvisionnement de l’appareil simulé X.509 sur le Hub, son ID d’appareil s’affiche sur le panneau **Device Explorer**, avec un *ÉTAT* **activé**.  Notez que vous devrez peut-être cliquer sur le bouton **Actualiser** dans la partie supérieure si vous avez déjà ouvert le panneau avant d’exécuter l’exemple d’application de l’appareil. 

    ![L’appareil est inscrit avec le hub IoT](./media/java-quick-create-simulated-device-x509/hub-registration.png) 

> [!NOTE]
> Si vous avez modifié la valeur par défaut de l’*état du jumeau d’appareil initial* dans l’entrée d’inscription de votre appareil, l’état du jumeau souhaité peut être extrait du hub et agir en conséquence. Pour en savoir plus, consultez [Comprendre et utiliser les jumeaux d’appareil IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Supprimer les ressources

Si vous envisagez de continuer à manipuler et explorer l’exemple de client d’appareil, ne nettoyez pas les ressources créées lors de ce démarrage rapide. Sinon, procédez aux étapes suivantes pour supprimer toutes les ressources créées lors de ce démarrage rapide.

1. Fermez la fenêtre de sortie de l’exemple de client d’appareil sur votre machine.
2. Dans le menu de gauche du portail Azure, cliquez sur **Toutes les ressources**, puis sélectionnez votre service Device Provisioning. Ouvrez le panneau **Gérer les inscriptions** pour votre service, puis cliquez sur l’onglet **Inscriptions individuelles**. Sélectionnez l’*ID D’INSCRIPTION* de l’appareil inscrit dans ce démarrage rapide, puis cliquez sur le bouton **Supprimer** dans la partie supérieure. 
3. À partir du menu de gauche, dans le portail Azure, cliquez sur **Toutes les ressources**, puis sélectionnez votre IoT Hub. Ouvrez le panneau **IoT Devices** (Appareils IoT) pour votre hub, sélectionnez *l’ID D’APPAREIL* de l’appareil inscrit dans ce démarrage rapide, puis cliquez sur le bouton **Supprimer** dans la partie supérieure.


## <a name="next-steps"></a>Étapes suivantes

Dans ce Démarrage rapide, vous avez créé un appareil X.509 simulé sur votre machine Windows. Vous avez configuré son inscription dans votre instance Azure IoT Hub Device Provisioning Service, puis approvisionné automatiquement l’appareil sur votre IoT Hub. Pour savoir comment inscrire un appareil X.509 au moyen d’un programme, poursuivez avec le démarrage rapide correspondant. 

> [!div class="nextstepaction"]
> [Démarrage rapide d’Azure : Inscrire des appareils X.509 auprès du service Azure IoT Hub Device Provisioning](quick-enroll-device-x509-java.md)
