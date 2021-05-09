---
title: Démarrage rapide - Utiliser une clé symétrique pour provisionner un appareil auprès d’Azure IoT Hub avec C#
description: Dans ce guide de démarrage rapide, vous allez utiliser le kit SDK d’appareil C# pour le service Device Provisioning (DPS) afin de provisionner un appareil à clé symétrique sur un hub IoT.
author: wesmc7777
ms.author: wesmc
ms.date: 04/23/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom: mvc
ms.openlocfilehash: e67616c2c92676c3af79e3040bc09d3b1b87a11b
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107988295"
---
# <a name="quickstart-provision-a-symmetric-key-device-using-c"></a>Démarrage rapide : Provisionner un appareil à clé symétrique avec C#

Dans ce guide de démarrage rapide, vous allez apprendre à provisionner un ordinateur de développement Windows en tant qu’appareil sur un hub IoT à l’aide de C#. Cet appareil utilise une clé symétrique et une inscription individuelle pour s’authentifier auprès d’une instance du service Device Provisioning afin d’être attribué à un hub IoT. L’exemple de code issu des [exemples Azure IoT pour C#](https://github.com/Azure-Samples/azure-iot-samples-csharp) est utilisé pour provisionner l’appareil. 

Bien que cet article illustre le provisionnement avec une seule inscription, vous pouvez aussi utiliser des groupes d’inscription. Il existe cependant quelques différences lorsque vous utilisez des groupes d’inscription. Par exemple, vous devez utiliser une clé d’appareil dérivée avec un ID d’inscription unique pour l’appareil. [Provisionner des appareils avec des clés symétriques](how-to-legacy-device-symm-key.md) fournit un exemple de groupe d’inscription. Pour plus d’informations sur les groupes d’inscription, consultez [Inscriptions de groupe pour l’attestation de clé symétrique](concepts-symmetric-key-attestation.md#group-enrollments).

Si vous ne connaissez pas le processus d’approvisionnement automatique, consultez la vue d’ensemble de l’[approvisionnement](about-iot-dps.md#provisioning-process). 

Vérifiez également que vous avez suivi la procédure décrite dans [Configurer le service IoT Hub Device Provisioning avec le portail Azure](./quick-setup-auto-provision.md) avant de poursuivre ce démarrage rapide. Ce guide de démarrage rapide nécessite au préalable la création d’une instance du service Device Provisioning.

Cet article traite d’une station de travail Windows. Toutefois, vous pouvez effectuer les procédures sur Linux. Pour obtenir un exemple sur Linux, consultez [Provisionnement pour la multilocation](how-to-provision-multitenant.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Prérequis

* Vérifiez que le kit [SDK .NET Core 2.1](https://dotnet.microsoft.com/download) ou version ultérieure est installé sur votre ordinateur Windows.

* Dernière version de [Git](https://git-scm.com/download/) installée.

<a id="setupdevbox"></a>


## <a name="create-a-device-enrollment"></a>Créer une inscription d’appareil

1. Connectez-vous au [portail Azure](https://portal.azure.com), sélectionnez le bouton **Toutes les ressources** dans le menu de gauche et ouvrez votre instance de service Device Provisioning (DPS).

2. Sélectionnez l’onglet **Gérer les inscriptions**, puis le bouton **Ajouter une inscription individuelle** dans la partie supérieure. 

3. Dans le volet **Ajouter une inscription**, entrez les informations suivantes, puis appuyez sur le bouton **Enregistrer**.

   - **Mécanisme** : sélectionnez **Clé symétrique** comme *mécanisme* d’attestation d’identité.

   - **Générer automatiquement les clés** : cochez cette case.

   - **ID d’inscription** : entrez un ID d’inscription pour identifier l’inscription. Utilisez uniquement des caractères alphanumériques en minuscules et des tirets (« - »). Par exemple, **symm-key-csharp-device-01**.

   - **ID de l’appareil IoT Hub** : entrez un identificateur d’appareil. Par exemple, **csharp-device-01**.

     ![Ajouter une inscription individuelle pour l’attestation de clé symétrique dans le portail](./media/quick-create-device-symmetric-key-csharp/create-individual-enrollment-csharp.png)

4. Une fois que vous avez enregistré votre inscription, la **Clé primaire** et la **Clé secondaire** sont générées et ajoutées à l’entrée d’inscription. Votre inscription d’appareil à clé symétrique apparaît sous le nom **symm-key-csharp-device-01** sous la colonne *ID d’inscription* de l’onglet *Inscriptions individuelles*. 

5. Ouvrez l’inscription et copiez la valeur de votre **Clé primaire** générée. Vous allez utiliser cette valeur de clé et l’**ID d’inscription** plus tard quand vous exécuterez l’exemple de code de provisionnement d’appareil.



## <a name="prepare-the-c-environment"></a>Préparer l’environnement C# 

1. Ouvrez un environnement de ligne de commande Git CMD ou Git Bash. Clonez le dépôt GitHub d’[exemples Azure IoT pour C#](https://github.com/Azure-Samples/azure-iot-samples-csharp) à l’aide de la commande suivante :

    ```cmd
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```



<a id="firstbootsequence"></a>

## <a name="run-the-device-provisioning-code"></a>Exécuter le code de provisionnement d’appareil

Dans cette section, vous allez exécuter l’exemple de provisionnement d’appareil à l’aide de trois paramètres qui authentifieront l’exemple de code de provisionnement d’appareil en tant qu’appareil de clé symétrique pour l’inscription dans votre ressource DPS. Ces trois paramètres sont les suivants :

* Étendue de l’ID
* ID d’inscription pour une inscription individuelle
* Clé symétrique principale pour une inscription individuelle

Le code de provisionnement contacte la ressource DPS à l’aide de ces paramètres afin d’authentifier votre appareil. L’appareil est alors attribué à un hub IoT déjà lié à l’instance du service DPS en fonction de la configuration de l’inscription individuelle. Une fois provisionné, l’exemple de code envoie un message de télémétrie de test au hub IoT.

1. Dans le [portail Azure](https://portal.azure.com), dans votre menu Service Device Provisioning, sélectionnez **Vue d’ensemble** et copiez votre valeur **Étendue de l’ID**. Vous allez utiliser cette valeur pour le paramètre `IdScope` lors de l’exécution de l’exemple de code.

2. Ouvrez une invite de commandes et accédez à *SymmetricKeySample* dans le dépôt d’exemples cloné :

    ```cmd
    cd azure-iot-samples-csharp\provisioning\Samples\device\SymmetricKeySample
    ```

3. Dans le dossier *SymmetricKeySample*, ouvrez *Parameters.cs* dans un éditeur de texte. Ce fichier montre les paramètres pris en charge par l’exemple. Seuls les trois premiers paramètres requis seront utilisés dans cet article lors de l’exécution de l’exemple. Passez en revue le code dans ce fichier. Aucune modification n’est requise.
 
    | Paramètre                         | Obligatoire | Description     |
    | :-------------------------------- | :------- | :-------------- |
    | `--s` ou `--IdScope`              | Vrai     | Étendue de l’ID de l’instance DPS |
    | `--i` ou `--Id`                   | Vrai     | ID d’inscription lors de l’utilisation d’une inscription individuelle ou ID d’appareil souhaité lors de l’utilisation d’une inscription de groupe. |
    | `--p` ou `--PrimaryKey`           | Vrai     | Clé primaire de l’inscription individuelle ou de groupe. |
    | `--e` ou `--EnrollmentType`       | Faux    | Type d’inscription : `Individual` ou `Group`. La valeur par défaut est `Individual` |
    | `--g` ou `--GlobalDeviceEndpoint` | Faux    | Point de terminaison global pour les appareils auxquels se connecter. La valeur par défaut est `global.azure-devices-provisioning.net` |
    | `--t` ou `--TransportType`        | Faux    | Transport à utiliser pour communiquer avec l’instance de provisionnement d’appareil. La valeur par défaut est `Mqtt`. Les valeurs possibles incluent `Mqtt`, `Mqtt_WebSocket_Only`, `Mqtt_Tcp_Only`, `Amqp`, `Amqp_WebSocket_Only`, `Amqp_Tcp_only` et `Http1`.|
     
4. Dans le dossier *SymmetricKeySample*, ouvrez *ProvisioningDeviceClientSample.cs* dans un éditeur de texte. Ce fichier montre comment la classe [SecurityProviderSymmetricKey](/dotnet/api/microsoft.azure.devices.shared.securityprovidersymmetrickey?view=azure-dotnet&preserve-view=true) est utilisée avec la classe [ProvisioningDeviceClient](/dotnet/api/microsoft.azure.devices.provisioning.client.provisioningdeviceclient?view=azure-dotnet&preserve-view=true) pour provisionner votre appareil à clé symétrique. Passez en revue le code dans ce fichier.  Aucune modification n’est requise.
 
5. Générez et exécutez l’exemple de code à l’aide de la commande suivante après avoir remplacé les trois exemples de paramètres. Utilisez les valeurs correctes d’étendue de l’ID, d’ID d’inscription et de clé primaire d’inscription.
    
    ```console
    dotnet run --s 0ne00000A0A --i symm-key-csharp-device-01 --p sbDDeEzRuEuGKag+kQKV+T1QGakRtHpsERLP0yPjwR93TrpEgEh/Y07CXstfha6dhIPWvdD1nRxK5T0KGKA+nQ==
    ```    


6. La sortie attendue ressemble à la sortie ci-dessous qui montre le hub IoT lié auquel l’appareil a été affecté en fonction des paramètres d’inscription individuelle. Un exemple de chaîne « TestMessage » est envoyé au hub en guise de test :

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
    
7. Dans le portail Azure, accédez au hub IoT lié à votre service de provisionnement et ouvrez le panneau **Appareils IoT**. Une fois l’appareil à clé symétrique correctement configuré dans le hub, l’ID de l’appareil apparaît avec l’*ÉTAT* **activé**. Notez que vous devrez peut-être appuyer sur le bouton **Actualiser** dans la partie supérieure si vous avez déjà ouvert le panneau avant d’exécuter l’exemple de code de l’appareil. 

    ![L’appareil est inscrit avec le hub IoT](./media/quick-create-device-symmetric-key-csharp/hub-registration-csharp.png) 

> [!NOTE]
> Si vous avez modifié la valeur par défaut de l’*état du jumeau d’appareil initial* dans l’entrée d’inscription de votre appareil, l’état du jumeau souhaité peut être extrait du hub et agir en conséquence. Pour en savoir plus, consultez [Comprendre et utiliser les jumeaux d’appareil IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous envisagez de manipuler et d’explorer davantage l’exemple de client d’appareil, ne nettoyez pas les ressources créées dans ce guide de démarrage rapide. Sinon, effectuez les étapes suivantes pour supprimer toutes les ressources créées par ce guide.

1. Dans le menu de gauche du portail Azure, sélectionnez **Toutes les ressources**, puis votre service Device Provisioning. Ouvrez **Gérer les inscriptions** pour votre service, puis sélectionnez l’onglet **Inscriptions individuelles**. Cochez la case à côté de l’*ID D’INSCRIPTION* de l’appareil que vous avez inscrit dans ce guide de démarrage rapide, puis appuyez sur le bouton **Supprimer** dans la partie supérieure du volet. 
1. À partir du menu de gauche, dans le portail Azure, sélectionnez **Toutes les ressources**, puis votre hub IoT. Ouvrez **Appareils IoT** pour votre hub, cochez la case en regard de l’*ID D’APPAREIL* de l’appareil que vous avez inscrit dans ce guide de démarrage rapide, puis appuyez sur le bouton **Supprimer** dans la partie supérieure du volet.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez provisionné un appareil à clé symétrique Windows dans votre IoT Hub à l’aide du service IoT Hub Device Provisioning. Pour découvrir comment provisionner des appareils à certificat X.509 avec C#, poursuivez avec le guide de démarrage rapide ci-dessous pour les appareils X.509. 

> [!div class="nextstepaction"]
> [Démarrage rapide Azure : Provisionner des appareils X.509 à l’aide de DPS et C#](quick-create-simulated-device-x509-csharp.md)
