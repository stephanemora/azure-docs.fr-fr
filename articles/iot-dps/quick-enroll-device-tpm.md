---
title: Démarrage rapide - Inscrire un appareil individuel auprès du service Azure Device Provisioning avec l’attestation TPM
description: Démarrage rapide - Inscrire un appareil individuel auprès du service Azure IoT Hub Device Provisioning (DPS) avec l’attestation TPM.
author: anastasia-ms
ms.author: v-stharr
ms.date: 08/20/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
zone_pivot_groups: iot-dps-set2
ms.openlocfilehash: d13a4b8224fc5ebe3eccf5f569af0efaed9b4101
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129293714"
---
# <a name="quickstart-enroll-individual-device-to-iot-hub-device-provisioning-service-using-tpm-attestation"></a>Démarrage rapide : Inscrire un appareil individuel auprès du service IoT Hub Device Provisioning avec l’attestation TPM

Cet article montre comment créer programmatiquement une inscription individuelle pour un appareil TPM auprès du service Azure IoT Hub Device Provisioning en utilisant le [kit Microsoft Azure IoT SDK](../iot-hub/iot-hub-devguide-sdks.md) et un exemple d’application. Vous pouvez aussi inscrire un appareil TPM simulé auprès du service de provisionnement en utilisant cette entrée d’inscription individuelle. 

Bien que ces étapes fonctionnent sur les ordinateurs Windows et Linux, cet article utilise un ordinateur de développement Windows.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Effectuez les étapes décrites dans [Configurer le service IoT Hub Device Provisioning avec le portail Azure](./quick-setup-auto-provision.md).

:::zone pivot="programming-language-csharp"

* Installez [Visual Studio 2019](https://www.visualstudio.com/vs/).

* Installez le [kit SDK .NET Core 3.1 ou ultérieur](https://dotnet.microsoft.com/download) sur votre ordinateur Windows. Vous pouvez exécuter la commande suivante pour vérifier votre version.

    ```bash
    dotnet --info
    ```

* (Facultatif) Si vous voulez inscrire un appareil simulé à la fin de ce guide de démarrage rapide, suivez la procédure décrite dans [Créer et provisionner un appareil TPM simulé](quick-create-simulated-device-tpm.md) jusqu’à l’étape où vous obtenez une paire de clés de type EK (Endorsement Key) pour l’appareil. Enregistrez la paire de clés de type EK et, éventuellement, l’ID de l’appareil.

:::zone-end

:::zone pivot="programming-language-nodejs"

* Installez [Node.js v4.0+](https://nodejs.org).

* (Facultatif) Créez une paire de clés de type EK. Suivez les étapes décrites dans [Créer et provisionner un appareil simulé](quick-create-simulated-device-tpm.md) jusqu’à ce que vous obteniez la clé.

:::zone-end

:::zone pivot="programming-language-python"

* Téléchargez et installez [Python 2.x ou 3.x](https://www.python.org/downloads/). Veillez à utiliser l’installation 32 bits ou 64 bits comme requis par votre programme d’installation. Lorsque vous y êtes invité pendant l’installation, veillez à ajouter Python à votre variable d’environnement propre à la plateforme. 

* Installez [pip](https://pip.pypa.io/en/stable/installing/) s’il n’est pas fourni avec votre distribution de Python.

> [!IMPORTANT]
> Cet article s’applique uniquement au kit SDK Python V1 déprécié. Les clients d’appareil et de service pour le service IoT Hub Device Provisioning ne sont pas encore disponibles dans la V2. L’équipe travaille actuellement sur la parité des fonctionnalités pour la V2.

:::zone-end

:::zone pivot="programming-language-java"

* Installez le [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-support-on-azure). Ce guide de démarrage rapide installe le kit [Java Service SDK](https://azure.github.io/azure-iot-sdk-java/master/service/) ci-dessous. Il fonctionne à la fois sur Windows et Linux. Ce guide de démarrage rapide utilise Windows.

* Installez [Maven 3](https://maven.apache.org/download.cgi).

* Installez [Git](https://git-scm.com/download/) en veillant à ce que le chemin soit ajouté à la variable d’environnement `PATH`.


:::zone-end

> [!NOTE]
> Ne suivez pas les étapes de création d’une inscription individuelle avec le portail Azure.


:::zone pivot="programming-language-python"

<a id="prepareenvironment"></a>

## <a name="prepare-the-development-environment"></a>Préparer l’environnement de développement

* Pour télécharger et installer le [kit SDK du service de provisionnement Python](https://github.com/Azure/azure-iot-sdk-python/tree/v1-deprecated/provisioning_service_client), choisissez l’une des options suivantes :
    
    - Générer et compiler le **Kit de développement logiciel (SDK) Python Azure IoT**. Suivez [ces instructions](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md) pour générer les packages Python. Si vous utilisez le système d’exploitation Windows, installez également le [package Visual C++ Redistributable](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) pour autoriser l’utilisation de DLL natives à partir de Python.
    
    - [Installer ou mettre à niveau *pip*, le système de gestion des packages Python](https://pip.pypa.io/en/stable/installing/) et installer le package via la commande suivante :
        
        ```cmd/sh
        pip install azure-iothub-provisioningserviceclient
        ```

### <a name="copy-symmetric-and-tpm-enrollment-keys"></a>Copier les clés d’inscription symétriques et TPM

Ce guide démarrage rapide présente à la fois les inscriptions avec une clé symétrique et TPM. Vous devrez soit créer votre propre clé, soit utiliser les clés de test fournies :
 
# <a name="symmetric-key"></a>[Clé symétrique](#tab/symmetrickey)

Pour les inscriptions d’appareils simulés avec une clé symétrique, vous avez besoin d’une clé primaire et d’une clé secondaire pour votre appareil. Si vous n’avez pas de clé symétrique valide, vous pouvez utiliser les clés de test suivantes pour cet exemple :

*Clé symétrique principale*

```
UmorGiEVPNIQuaWGXXbe8v9gWayS7XtOZmNMo6DEaEXP65GvhuK3OeRf8RVZ9BymBCHxNg3oRTey0pUHUwwYKQ==
```

*Clé symétrique secondaire*

```
Zx8/eE7PUBmnouB1qlNQxI7fcQ2HbJX+y96F1uCVQvDj88jFL+q6L9YWLLi4jqTmkRPOulHlSbSv2uFgj4vKtw==
```

# <a name="tpm"></a>[Module de plateforme sécurisée](#tab/tpm)

Pour les inscriptions TPM, vous avez besoin de la clé de type EK (Endorsement Key) pour votre appareil. Si vous avez suivi le démarrage rapide [Créer et configurer un appareil simulé](quick-create-simulated-device-tpm.md) pour créer un appareil TPM simulé, utilisez la clé créée pour cet appareil. Sinon, vous pouvez utiliser la paire de clés de type EK (Endorsement Key) suivante fournie avec le Kit de développement logiciel (SDK) :

```
AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAtW6MOyCu/Nih47atIIoZtlYkhLeCTiSrtRN3q6hqgOllA979No4BOcDWF90OyzJvjQknMfXS/Dx/IJIBnORgCg1YX/j4EEtO7Ase29Xd63HjvG8M94+u2XINu79rkTxeueqW7gPeRZQPnl1xYmqawYcyzJS6GKWKdoIdS+UWu6bJr58V3xwvOQI4NibXKD7htvz07jLItWTFhsWnTdZbJ7PnmfCa2vbRH/9pZIow+CcAL9mNTNNN4FdzYwapNVO+6SY/W4XU0Q+dLMCKYarqVNH5GzAWDfKT8nKzg69yQejJM8oeUWag/8odWOfbszA+iFjw3wVNrA5n8grUieRkPQ==
```

---

:::zone-end


:::zone pivot="programming-language-java"

<a id="setupdevbox"></a>

## <a name="prepare-the-development-environment"></a>Préparer l’environnement de développement 

### <a name="set-up-environment-variables"></a>Définir les variables d’environnement

Pour configurer les variables d’environnement :

1. La variable `PATH` doit inclure le chemin d’accès complet au répertoire *jdk1.8.x\bin*. S’il s’agit de la première installation de Java sur cet ordinateur, vous devez alors créer une variable d’environnement nommée `JAVA_HOME` et la faire pointer vers le chemin complet du répertoire *jdk1.8.x*. Sur une machine Windows, ce répertoire se trouve dans le dossier *C:\\Program Files\\Java\\* . De plus, vous pouvez créer ou modifier les variables d’environnement en recherchant **Modifier les variables d’environnement du système** dans le **Panneau de configuration** de votre machine Windows.

    Pour vérifier que Java est correctement configuré sur votre ordinateur, exécutez la commande suivante dans la fenêtre de commande :

    ```cmd\sh
    java -version
    ```

2. Modifiez la variable d’environnement `PATH` pour qu’elle pointe vers le sous-dossier *apache-maven-3.x.x\\bin* du dossier dans lequel Maven est extrait. Pour vérifier que Maven est correctement installé, exécutez la commande suivante dans la fenêtre de commande :

    ```cmd\sh
    mvn --version
    ```

3. Assurez-vous que l’élément [git](https://git-scm.com/download/) est installé sur votre ordinateur et qu’il est ajouté à la variable d’environnement `PATH`.

### <a name="clone-git-repository-for-azure-iot-java-sdk"></a>Cloner le dépôt Git pour le kit Azure IoT Java SDK

Pour cloner le kit Azure IoT Java SDK :

1. Ouvrez une invite de commandes. 

2. Clonez le dépôt GitHub pour l’exemple de code d’inscription d’appareil à l’aide du kit [Java Service SDK](https://azure.github.io/azure-iot-sdk-java/master/service/) :

    ```cmd\sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

:::zone-end

## <a name="get-the-connection-string-for-your-provisioning-service"></a>Obtenir la chaîne de connexion de votre service d’approvisionnement

Pour l’exemple dans ce guide de démarrage rapide, vous devez copier la chaîne de connexion de votre service de provisionnement.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans le menu de gauche ou dans la page du portail, sélectionnez **Toutes les ressources**.

3. Sélectionnez votre service Device Provisioning.

4. Dans le menu **Paramètres**, sélectionnez **Stratégies d’accès partagées**.

5. Sélectionnez la stratégie d’accès que vous souhaitez utiliser.

6. Dans le panneau **Stratégie d’accès**, copiez et enregistrez la chaîne de connexion de la clé primaire.

    :::image type="content" source="./media/quick-enroll-device-tpm/get-service-connection-string.png" alt-text="Obtenir la chaîne de connexion du service de provisionnement à partir du portail.":::

## <a name="create-the-individual-enrollment-sample"></a>Créer l’exemple d’inscription individuelle

:::zone pivot="programming-language-csharp"

Cette section montre comment créer une application console .NET Core qui ajoute une inscription individuelle pour un appareil TPM à votre service de provisionnement. Avec quelques modifications, vous pouvez également suivre ces étapes pour créer une application console [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot) pour ajouter l’inscription individuelle. Pour en savoir plus sur le développement avec IoT Core, consultez la [Documentation Windows IoT Core pour les développeurs](/windows/iot-core/).

1. Ouvrez Visual Studio et sélectionnez **Créer un projet**.

2. Dans le panneau **Créer un projet**, sélectionnez **Application console*.

3. Sélectionnez **Suivant**.

4. Pour **Nom du projet**, tapez *CreateEnrollmentGroup*.

5. Sélectionnez **Suivant**. Conservez le **Framework cible** par défaut.

6. Sélectionnez **Create** (Créer).

7. Quand la solution s’ouvre, dans le volet **Explorateur de solutions**, cliquez avec le bouton droit sur le projet **CreateEnrollmentGroup**, puis sélectionnez **Gérer les packages NuGet**.

8. Dans le **Gestionnaire de package NuGet**, sélectionnez **Parcourir**.

9. Tapez et sélectionnez *Microsoft.Azure.Devices.Provisioning.Service*.

10. Sélectionnez **Installer**.

    ![Fenêtre du gestionnaire de package NuGet](media//quick-enroll-device-tpm/add-nuget.png)

    Cette étape télécharge, installe et ajoute une référence au package NuGet [Azure IoT Provisioning Service Client SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) et à ses dépendances.

11. Ajoutez les instructions `using` suivantes après les autres instructions `using` en haut de `Program.cs` :
  
    ```csharp
    using System.Threading.Tasks;
    using Microsoft.Azure.Devices.Provisioning.Service;
    ```

12. Ajoutez les champs suivants à la classe `Program` et effectuez les modifications mentionnées.  

    ```csharp
    private static string ProvisioningConnectionString = "{ProvisioningServiceConnectionString}";
    private const string RegistrationId = "sample-registrationid-csharp";
    private const string TpmEndorsementKey =
        "AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUS" +
        "cTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3d" +
        "yKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKR" +
        "dln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFe" +
        "WlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==";
        
    // Optional parameters
    private const string OptionalDeviceId = "myCSharpDevice";
    private const ProvisioningStatus OptionalProvisioningStatus = ProvisioningStatus.Enabled;
    ```

13. Remplacez la valeur de l’espace réservé `ProvisioningServiceConnectionString` par la chaîne de connexion du service de provisionnement que vous avez copiée dans la section précédente.

14. Si vous utilisez ce guide de démarrage rapide avec le guide de démarrage rapide [Créer et provisionner un appareil TPM simulé](quick-create-simulated-device-tpm.md) pour provisionner un appareil simulé, remplacez la paire de clés de type EK et l’ID d’inscription par les valeurs dont vous avez pris note dans ce guide. Vous pouvez remplacer l’ID d’appareil par la valeur suggérée dans ce guide de démarrage rapide, utiliser votre propre valeur ou utiliser la valeur par défaut dans cet exemple.

15. Ajoutez la méthode suivante à la classe `Program`.  Ce code crée une entrée d’inscription individuelle, puis appelle la méthode `CreateOrUpdateIndividualEnrollmentAsync` sur `ProvisioningServiceClient` pour ajouter l’inscription individuelle au service de provisionnement.

    ```csharp
    public static async Task RunSample()
    {
        Console.WriteLine("Starting sample...");
    
        using (ProvisioningServiceClient provisioningServiceClient =
                ProvisioningServiceClient.CreateFromConnectionString(ProvisioningConnectionString))
        {
            #region Create a new individualEnrollment config
            Console.WriteLine("\nCreating a new individualEnrollment...");
            Attestation attestation = new TpmAttestation(TpmEndorsementKey);
            IndividualEnrollment individualEnrollment =
                    new IndividualEnrollment(
                            RegistrationId,
                            attestation);
    
            // The following parameters are optional. Remove them if you don't need them.
            individualEnrollment.DeviceId = OptionalDeviceId;
            individualEnrollment.ProvisioningStatus = OptionalProvisioningStatus;
            #endregion
    
            #region Create the individualEnrollment
            Console.WriteLine("\nAdding new individualEnrollment...");
            IndividualEnrollment individualEnrollmentResult =
                await provisioningServiceClient.CreateOrUpdateIndividualEnrollmentAsync(individualEnrollment).ConfigureAwait(false);
            Console.WriteLine("\nIndividualEnrollment created with success.");
            Console.WriteLine(individualEnrollmentResult);
            #endregion
        
        }
    }
    ```

16. Enfin, remplacez la méthode `Main` par les lignes suivantes :

    ```csharp
    static async Task Main(string[] args)
    {
        await RunSample();
        Console.WriteLine("\nHit <Enter> to exit ...");
        Console.ReadLine();
    }
    ```

17. Générez la solution.

:::zone-end

:::zone pivot="programming-language-nodejs"

1. À partir d’une fenêtre de commande dans votre dossier de travail, exécutez :
  
    ```cmd\sh
    npm install azure-iot-provisioning-service
    ```  

2. À l’aide d’un éditeur de texte, créez un fichier _create_individual_enrollment.js_ dans votre dossier de travail. Ajoutez le code suivant au fichier :

    ```Java
    'use strict';

    var provisioningServiceClient = require('azure-iot-provisioning-service').ProvisioningServiceClient;

    var serviceClient = provisioningServiceClient.fromConnectionString(process.argv[2]);
    var endorsementKey = process.argv[3];

    var enrollment = {
      registrationId: 'first',
      attestation: {
        type: 'tpm',
        tpm: {
          endorsementKey: endorsementKey
        }
      }
    };

    serviceClient.createOrUpdateIndividualEnrollment(enrollment, function(err, enrollmentResponse) {
      if (err) {
        console.log('error creating the individual enrollment: ' + err);
      } else {
        console.log("enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
      }
    });
    ```

3. Enregistrez le fichier .

:::zone-end

:::zone pivot="programming-language-python"

1. Dans un éditeur de texte, créez un fichier *Enrollment.py*.

2. Ajoutez les instructions et variables `import` suivantes au début du fichier *Enrollment.py* (en remplaçant `{dpsConnectionString}` par la chaîne de connexion copiée précédemment, l’espace réservé du certificat par le certificat créé dans [Copier les clés d’inscription symétriques et TPM](#copy-symmetric-and-tpm-enrollment-keys), et `{registrationid}` par un `registrationid` unique composé exclusivement de caractères alphanumériques en minuscules et de traits d’union) :


    # <a name="symmetric-key"></a>[Clé symétrique](#tab/symmetrickey)

    ```python
    from provisioningserviceclient import ProvisioningServiceClient
    from provisioningserviceclient.models import IndividualEnrollment, AttestationMechanism
    from provisioningserviceclient.protocol.models import SymmetricKeyAttestation

    CONNECTION_STRING = "Enter your DPS connection string"
    PRIMARY_KEY = "Add a valid key"
    SECONDARY_KEY = "Add a valid key"
    REGISTRATION_ID = "Enter a registration ID"
    ```

    # <a name="tpm"></a>[Module de plateforme sécurisée](#tab/tpm)
   
    ```python
    from provisioningserviceclient import ProvisioningServiceClient
    from provisioningserviceclient.models import IndividualEnrollment, AttestationMechanism

    CONNECTION_STRING = "Enter your DPS connection string"
    ENDORSEMENT_KEY = "Enter the endorsement key for your device"
    REGISTRATION_ID = "Enter a registration ID"
    ```

    ---

3. Ajoutez le code Python suivant pour implémenter la création de l’inscription individuelle :

     # <a name="symmetric-key"></a>[Clé symétrique](#tab/symmetrickey)

    ```python
    def main():
        print ( "Starting individual enrollment..." )
    
        psc = ProvisioningServiceClient.create_from_connection_string(CONNECTION_STRING)
    
        symAtt = SymmetricKeyAttestation(primary_key=PRIMARY_KEY, secondary_key=SECONDARY_KEY)
        att = AttestationMechanism(type="symmetricKey", symmetric_key=symAtt)
        ie = IndividualEnrollment.create(REGISTRATION_ID, att)
    
        ie = psc.create_or_update(ie)
    
        print ( "Individual enrollment successful." )
    
    if __name__ == '__main__':
        main()
    ```

    # <a name="tpm"></a>[Module de plateforme sécurisée](#tab/tpm)

    ```python
    def main():
        print ( "Starting individual enrollment..." )
    
        psc = ProvisioningServiceClient.create_from_connection_string(CONNECTION_STRING)
    
        att = AttestationMechanism.create_with_tpm(ENDORSEMENT_KEY)
        ie = IndividualEnrollment.create(REGISTRATION_ID, att)
    
        ie = psc.create_or_update(ie)
    
        print ( "Individual enrollment successful." )
    
    if __name__ == '__main__':
        main()
    ```

    ---

4. Enregistrez et fermez le fichier *Enrollment.py*.

:::zone-end

:::zone pivot="programming-language-java"

1. Dans le code source téléchargé, accédez au dossier d’exemples *_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-sample_* . Ouvrez le fichier *_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentSample.java_*.

2. Remplacez `[Provisioning Connection String]` par la chaîne de connexion que vous avez copiée dans [Obtenir la chaîne de connexion de votre service de provisionnement](#get-the-connection-string-for-your-provisioning-service).

    ```Java
    private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
    ```

   2. Ajoutez les détails de l’appareil TPM :
       1. Obtenez *l’ID d’inscription* et la *paire de clés de type EK du TPM* pour une simulation de l’appareil TPM. Reportez-vous pour cela aux étapes nécessaires pour accéder à la section [Simuler un appareil TPM](quick-create-simulated-device-tpm.md#simulatetpm).
       2. Utilisez **_l’ID d’inscription_** et la **_paire de clés de type EK_** provenant de la sortie de l’étape précédente, afin de remplacer `[RegistrationId]` et `[TPM Endorsement Key]` dans l’exemple de fichier de code **_ServiceEnrollmentSample.java_** :
    
           ```Java
           private static final String REGISTRATION_ID = "[RegistrationId]";
           private static final String TPM_ENDORSEMENT_KEY = "[TPM Endorsement Key]";
           ```

3. Pour configurer votre service de provisionnement à partir de l’exemple de code, passez à l’étape suivante. Si vous ne souhaitez pas le configurer, veillez à commenter ou à supprimer les instructions suivantes dans le fichier _ServiceEnrollmentSample.java_ :

    ```Java
    / / The following parameters are optional. Remove it if you don't need.
    individualEnrollment.setDeviceId(DEVICE_ID);
    individualEnrollment.setIotHubHostName(IOTHUB_HOST_NAME);
    individualEnrollment.setProvisioningStatus(PROVISIONING_STATUS);
    ```

4. Cette étape vous montre comment configurer votre service de provisionnement dans l’exemple de code.

    1. Accédez au [portail Azure](https://portal.azure.com).

    2. Dans le menu de gauche ou dans la page du portail, sélectionnez **Toutes les ressources**.

    3. Sélectionnez votre service Device Provisioning.

    4. Dans le panneau **Vue d’ensemble**, copiez le nom d’hôte du *Point de terminaison de service*.  Dans l’exemple de code source, remplacez `[Host name]` par le nom d’hôte copié.

    ```Java
    private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
    ```

5. Étudiez l’exemple de code. Il crée, met à jour, interroge et supprime l’inscription d’un appareil TPM. Pour vérifier la validité de l’inscription sur le portail, commentez temporairement les lignes suivantes de code à la fin du fichier _ServiceEnrollmentSample.java_ :

    ```Java
    // *********************************** Delete info of individualEnrollment ************************************
    System.out.println("\nDelete the individualEnrollment...");
    provisioningServiceClient.deleteIndividualEnrollment(REGISTRATION_ID);
    ```

6. Enregistrez le fichier _ServiceEnrollmentSample.java_.

:::zone-end

## <a name="run-the-individual-enrollment-sample"></a>Exécuter l’exemple d’inscription individuelle

:::zone pivot="programming-language-csharp"

1. Exécutez l’exemple dans Visual Studio pour créer l’inscription. Une fenêtre de commande s’ouvre et affiche des messages de confirmation.

2. Une fois la création terminée, la fenêtre de commande affiche les propriétés de la nouvelle inscription.

:::zone-end

:::zone pivot="programming-language-nodejs"

Pour exécuter l’exemple, vous devez disposer de la chaîne de connexion du service de provisionnement copiée dans la section précédente ainsi que de la paire de clés de type EK pour l’appareil. Si vous avez suivi le guide de démarrage rapide [Créer et provisionner un appareil simulé](quick-create-simulated-device-tpm.md) pour créer un appareil TPM simulé, utilisez la clé créée pour cet appareil. Sinon, pour créer un exemple d’inscription individuelle, vous pouvez utiliser la paire de clés de type EK (Endorsement Key) suivante fournie avec le kit [Node.js Service SDK](https://github.com/Azure/azure-iot-sdk-node) :

```bash
AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUScTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3dyKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKRdln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFeWlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==
```

1. Afin de créer une inscription individuelle pour votre appareil TPM, exécutez la commande suivante (sans oublier les guillemets autour des arguments de commande) :

     ```cmd\sh
     node create_individual_enrollment.js "<the connection string for your provisioning service>" "<endorsement key>"
     ```

2. Une fois la création terminée, la fenêtre de commande affiche les propriétés de la nouvelle inscription.

:::zone-end

:::zone pivot="programming-language-python"

1. Ouvrez une invite de commandes et exécutez le script suivant.

    ```cmd/sh
    python Enrollment.py
    ```

2. Une fois la création terminée, la fenêtre de commande affiche les propriétés de la nouvelle inscription.

:::zone-end

:::zone pivot="programming-language-java"

1. Ouvrez une fenêtre de commande en mode Administrateur, puis accédez au dossier *_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_*.

2. À l’invite de commandes, utilisez la commande suivante :

    ```cmd\sh
    mvn install -DskipTests
    ```

    Cette commande télécharge le package Maven [`com.microsoft.azure.sdk.iot.provisioning.service`](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client) sur l’ordinateur. Ce package inclut les fichiers binaires du Java Service SDK dont l’exemple de code a besoin. Si vous avez exécuté l’outil _Générateur de certificat X.509_ dans la section précédente, ce package est déjà téléchargé sur votre machine.

3. À l’invite de commandes, exécutez le script suivant :

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-group-sample-{version}-with-deps.jar
    ```

4. Une fois la création terminée, la fenêtre de commande affiche les propriétés de la nouvelle inscription.


:::zone-end

Pour vérifier que le groupe d’inscription a bien été créé :

1. Dans le portail Azure, sélectionnez votre service Device Provisioning.

2. Dans le menu **Paramètres**, sélectionnez **Gérer les inscriptions**.

3. Sélectionnez **Inscriptions individuelles**. Vous devez voir une nouvelle entrée d’inscription qui correspond à l’ID d’inscription que vous avez utilisé dans l’exemple.

:::zone pivot="programming-language-csharp"

:::image type="content" source="./media/quick-enroll-device-tpm/verify-enrollment-csharp.png" alt-text="Vérifier l’inscription d’un appareil individuel dans le portail (C#).":::

:::zone-end

:::zone pivot="programming-language-nodejs"

:::image type="content" source="./media/quick-enroll-device-tpm/verify-enrollment-nodejs.png" alt-text="Vérifier l’inscription d’un appareil individuel dans le portail (Node.js).":::

:::zone-end

:::zone pivot="programming-language-python"

:::image type="content" source="./media/quick-enroll-device-tpm/verify-enrollment-python.png" alt-text="Vérifier l’inscription d’un appareil individuel dans le portail (Python).":::

:::zone-end

:::zone pivot="programming-language-java"

:::image type="content" source="./media/quick-enroll-device-tpm/verify-enrollment-java.png" alt-text="Vérifier l’inscription d’un appareil individuel dans le portail (Java).":::

:::zone-end


## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous envisagez d’explorer les [tutoriels relatifs au service IoT Hub Device Provisioning](./tutorial-set-up-cloud.md), ne nettoyez pas les ressources créées dans ce guide de démarrage rapide. Sinon, utilisez les étapes suivantes pour supprimer toutes les ressources créées par ce guide de démarrage rapide.

1. Fermez la fenêtre de sortie de l’exemple sur votre ordinateur.

2. Dans le menu de gauche du portail Azure, sélectionnez **Toutes les ressources**.

3. Sélectionnez votre service Device Provisioning.

4. Dans le menu **Paramètres**, sélectionnez **Gérer les inscriptions**.

5. Sélectionnez l’onglet **Inscriptions individuelles**.

6. Cochez la case à côté de l’*ID D’INSCRIPTION* de l’appareil que vous avez inscrit dans ce guide de démarrage rapide.

7. En haut de la page, sélectionnez **Supprimer**.

8. À partir du service Device Provisioning dans le portail Azure, sélectionnez **Certificats**.

9. Sélectionnez le certificat que vous avez chargé pour ce guide de démarrage rapide.

10. En haut du volet **Détails du certificat**, sélectionnez **Supprimer**.  

11. Si vous avez suivi les étapes décrites dans [Créer et provisionner un appareil TPM simulé](quick-create-simulated-device-tpm.md) pour créer un appareil TPM simulé, effectuez les étapes suivantes :

    1. Fermez la fenêtre du simulateur TPM et la fenêtre d’exemple de sortie pour l’appareil simulé.

    2. Dans le portail Azure, accédez au IoT Hub au sein duquel votre appareil était approvisionné. 

    3. Dans le menu sous **Explorateurs**, sélectionnez **Appareils IoT**.

    4. Cochez la case à côté de l’*ID D’APPAREIL* de l’appareil que vous avez inscrit dans ce guide de démarrage rapide.

    5. En haut du volet, sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé programmatiquement une entrée d’inscription individuelle pour un appareil TPM. Si vous avez choisi de le faire, vous avez créé un appareil simulé TPM sur votre ordinateur et vous l’avez provisionné sur votre hub IoT avec le service Azure IoT Hub Device Provisioning. Pour en savoir plus sur l’approvisionnement de l’appareil en profondeur, référez-vous au didacticiel relatif à l’installation du service d’approvisionnement d’appareil dans le portail Azure.

> [!div class="nextstepaction"]
> [Didacticiels relatifs au service d’approvisionnement d’appareil Azure IoT Hub](./tutorial-set-up-cloud.md)