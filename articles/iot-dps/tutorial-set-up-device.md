---
title: Configurer un appareil pour le service Azure IoT Hub Device Provisioning Service
description: Configurer un appareil à provisionner par le biais du service IoT Hub Device Provisioning pendant le processus de fabrication de l’appareil
services: iot-dps
keywords: ''
author: dsk-2015
ms.author: dkshir
ms.date: 04/02/2018
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: c885e4d5d747d913eaf0b7137b240950e920e7ff
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="set-up-a-device-to-provision-using-the-azure-iot-hub-device-provisioning-service"></a>Configurer un appareil à provisionner à l’aide du service IoT Hub Device Provisioning

Dans le didacticiel précédent, vous avez appris à configurer le service IoT Hub Device Provisioning afin de provisionner automatiquement vos appareils pour votre hub IoT. Ce didacticiel vous montre comment configurer votre appareil pendant le processus de fabrication, pour lui permettre d’être approvisionné automatiquement avec IoT Hub. Votre appareil est approvisionné en fonction de son [mécanisme d’attestation](concepts-device.md#attestation-mechanism), au premier démarrage et à la première connexion au service d’approvisionnement. Ce didacticiel présente les processus correspondant aux opérations suivantes :

> [!div class="checklist"]
> * Générer un Kit de développement logiciel (SDK) Device Provisioning Service Client spécifique à une plateforme
> * Extraire les artefacts de sécurité
> * Créer le logiciel d’inscription d’appareils

## <a name="prerequisites"></a>Prérequis


Avant de continuer, créez votre instance du service Device Provisioning Service et un IoT Hub en suivant les instructions indiquées dans le didacticiel précédent [1 - Configurer les ressources de cloud pour l’approvisionnement d’appareils avec le service IoT Hub Device Provisioning](./tutorial-set-up-cloud.md).

Ce didacticiel utilise le [référentiel Azure IoT SDKs and libraries for C](https://github.com/Azure/azure-iot-sdk-c), qui contient le Kit de développement logiciel (SDK) Device Provisioning Service Client pour C. Le Kit de développement logiciel (SDK) offre actuellement une prise en charge TPM et X.509 pour les appareils s’exécutant sur des implémentations Windows ou Ubuntu. Ce didacticiel repose sur l’utilisation d’un client de développement Windows, ce qui suppose également que vous ayez des compétences basiques sur Visual Studio 2017. 

Si vous ne connaissez pas le processus d’approvisionnement automatique, pensez à consulter l’article [Concepts de provisionnement automatique](concepts-auto-provisioning.md) avant de continuer. 

## <a name="build-a-platform-specific-version-of-the-sdk"></a>Générer une version spécifique à la plateforme du Kit de développement (SDK)

Le Kit de développement logiciel (SDK) Device Provisioning Service Client vous permet d’implémenter votre logiciel d’inscription d’appareil. Mais avant de pouvoir l’utiliser, vous devez générer une version du Kit de développement logiciel (SDK) spécifique à votre mécanisme d’attestation et plateforme cliente de développement. Dans ce didacticiel, vous générez un Kit de développement logiciel (SDK) qui utilise Visual Studio 2017 sur une plateforme de développement Windows, pour un type d’attestation pris en charge :

1. Installez les outils nécessaires et clonez le référentiel GitHub qui contient le Kit de développement logiciel (SDK) Provisioning Service Client pour C :

   a. Assurez-vous que Visual Studio 2015 ou [Visual Studio 2017](https://www.visualstudio.com/vs/) est installé sur votre ordinateur. La charge de travail [« Développement Desktop en C++ »](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) doit être activée pour l’installation de Visual Studio.

   b. Téléchargez et installez le [système de génération de CMake](https://cmake.org/download/). Il est important que Visual Studio avec la charge de travail « Développement Desktop en C++ » soit installé sur votre machine, **avant** l’installation de CMake.

   c. Assurez-vous que l’élément `git` est installé sur votre machine et est ajouté aux variables d’environnement accessibles à la fenêtre de commande. Consultez la page présentant [les outils du client Git de Software Freedom Conservancy](https://git-scm.com/download/) pour accéder aux derniers `git` outils, y compris **Git Bash**, un interpréteur de commandes Bash pour interagir avec votre référentiel Git local. 

   d. Ouvrez Git Bash et clonez le référentiel « Azure IoT SDKs and libraries for C ». Plusieurs minutes peuvent être nécessaires pour l’exécution de la commande clone, car elle télécharge également divers sous-modules dépendants :
    
   ```cmd/sh
   git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
   ```

   e. Créez un sous-répertoire `cmake` dans le sous-répertoire du référentiel nouvellement créé.

   ```cmd/sh
   mkdir azure-iot-sdk-c/cmake
   ``` 

2. À partir de l’invite de commandes Git Bash, modifiez le sous-répertoire `cmake` dans le référentiel azure-iot-sdk-c :

   ```cmd/sh
   cd azure-iot-sdk-c/cmake
   ```

3. Générez le Kit de développement logiciel (SDK) pour votre plateforme de développement et l’un des mécanismes d’attestation pris en charge, à l’aide d’une des commandes suivantes (notez également les deux points à la fin). Une fois l’opération terminée, CMake génère le sous-répertoire `/cmake` avec du contenu spécifique à votre appareil :
    - Pour les appareils qui utilisent un TPM/HSM physique ou un certificat X.509 simulé pour l’attestation :
        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON ..
        ```

    - Pour les appareils qui utilisent le simulateur TPM pour l’attestation :
        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
        ```

Vous êtes maintenant prêt à utiliser le Kit de développement logiciel (SDK) pour générer votre code d’inscription d’appareil. 
 
<a id="extractsecurity"></a> 

## <a name="extract-the-security-artifacts"></a>Extraire les artefacts de sécurité 

L’étape suivante consiste à extraire les artefacts de sécurité pour le mécanisme d’attestation utilisé par votre appareil. 

### <a name="physical-device"></a>Appareil physique 

Si vous avez généré le Kit de développement logiciel (SDK) pour utiliser l’attestation d’un TPM/HSM physique :

- Pour un appareil TPM, vous devez récupérer la **paire de clés de type EK** qui lui est associée auprès du fabricant du processeur TPM. Vous pouvez dériver un **ID d’inscription** unique pour votre appareil TPM en hachant la paire de clés de type EK.  

- Pour un appareil X.509, vous devez obtenir les certificats délivrés à vos appareils : certificats d’entité finale pour les inscriptions d’appareils individuels ou certificats racines pour les inscriptions de groupe d’appareils. 

### <a name="simulated-device"></a>Appareil simulé

Si vous avez généré le Kit de développement logiciel (SDK) pour utiliser l’attestation d’un certificat X.509 ou d’un TPM simulé :

- Pour un appareil TPM simulé :
   1. Dans une invite de commandes distincte/nouvelle, accédez au sous-répertoire `azure-iot-sdk-c` et exécutez le simulateur TPM. Il écoute un socket sur les ports 2321 et 2322. Ne fermez pas cette fenêtre de commande ; vous devez laisser ce simulateur s’exécuter jusqu’à la fin de ce Démarrage rapide. 

      Dans le sous-répertoire `azure-iot-sdk-c`, exécutez la commande suivante pour démarrer le simulateur :

      ```cmd/sh
      .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
      ```

   2. À l’aide de Visual Studio, ouvrez la solution générée dans le dossier *cmake* nommé `azure_iot_sdks.sln` et générez-la à l’aide de la commande « Build solution » dans le menu « Générer ».

   3. Dans le volet *Explorateur de solutions* de Visual Studio, accédez au dossier **Provision\_Outils**. Cliquez avec le bouton droit sur le projet **tpm_device_provision** et sélectionnez **Définir comme projet de démarrage**. 

   4. Exécutez la solution à l’aide des commandes « Start » dans le menu « Déboguer ». La fenêtre de sortie affiche **_l’ID d’inscription_** et la **_paire de clés de type EK_** du simulateur TPM nécessaires à l’inscription et à l’enregistrement de l’appareil. Copiez ces valeurs pour une utilisation ultérieure. Vous pouvez fermer cette fenêtre (avec l’ID d’inscription et la paire de clés de type EK), mais laissez s’exécuter la fenêtre du simulateur TPM que vous avez démarré à l’étape 1.

- Pour un appareil X.509 simulé :
  1. À l’aide de Visual Studio, ouvrez la solution générée dans le dossier *cmake* nommée `azure_iot_sdks.sln` et générez-la à l’aide de la commande « Build solution » dans le menu « Générer ».

  2. Dans le volet *Explorateur de solutions* de Visual Studio, accédez au dossier **Provision\_Outils**. Cliquez avec le bouton droit sur le projet**dice\_device\_enrollment** et sélectionnez **Définir comme projet de démarrage**. 
  
  3. Exécutez la solution à l’aide des commandes « Start » dans le menu « Déboguer ». Dans la fenêtre Sortie, entrez **i** pour l’inscription individuelle lorsque vous y êtes invité. La fenêtre Sortie affiche un certificat X.509 généré localement pour votre appareil simulé. Copiez dans le Presse-papiers la sortie débutant par *-----BEGIN CERTIFICATE-----* et se terminant par *-----END CERTIFICATE-----*, en faisant bien attention à inclure également ces deux lignes. Notez que n’avez besoin que du premier certificat dans la fenêtre Sortie.
 
  4. Créez un fichier nommé **_X509testcert.pem_**, ouvrez-le dans l’éditeur de texte de votre choix et copiez le contenu du Presse-papiers dans ce fichier. Enregistrez le fichier car vous allez l’utiliser pour l’inscription d’appareil. Lorsque votre logiciel d’inscription s’exécute, il utilise le même certificat au cours de l’approvisionnement automatique.    

Ces artefacts de sécurité sont obligatoires pour l’inscription de votre appareil auprès du service d’approvisionnement des appareils. Le service d’approvisionnement attend que l’appareil démarre et s’y connecte plus tard. Au premier démarrage de votre appareil, la logique du Kit de développement logiciel (SDK) client interagit avec votre processeur (ou simulateur) pour extraire les artefacts de sécurité de l’appareil et vérifie l’inscription auprès du service Device Provisioning Service. 

## <a name="create-the-device-registration-software"></a>Créer le logiciel d’inscription d’appareils

La dernière étape consiste à écrire une application d’inscription qui utilise le Kit de développement logiciel (SDK) Device Provisioning Service Client pour inscrire l’appareil auprès du service IoT Hub. 

> [!NOTE]
> Pour cette étape, nous allons utiliser un appareil simulé, obtenu en exécutant un exemple d’application d’inscription de Kit de développement logiciel (SDK) à partir de votre station de travail. Toutefois, les mêmes concepts s’appliquent si vous générez une application d’inscription pour un déploiement vers un appareil physique. 

1. Dans le portail Azure, sélectionnez le panneau **Vue d’ensemble** de votre service Device Provisioning Service et copiez la valeur de **_Étendue de l’ID_**. *L’étendue de l’ID* est générée par le service et garantit l’unicité. Elle est immuable et sert à identifier les ID d’inscription.

    ![Extraire des informations du point de terminaison DPS à partir du panneau du portail](./media/tutorial-set-up-device/extract-dps-endpoints.png) 

2. Dans *l’Explorateur de solutions* de Visual Studio sur votre machine, accédez au dossier **Provision\_Exemples**. Sélectionnez l’exemple de projet nommé **prov\_dev\_client\_sample** et ouvrez le fichier source **prov\_dev\_client\_sample.c**.

3. Affectez la valeur _Étendue de l’ID_ obtenue à l’étape 1, à la variable `id_scope` (suppression des crochets de gauche /`[` et de droite /`]`) : 

    ```c
    static const char* global_prov_uri = "global.azure-devices-provisioning.net";
    static const char* id_scope = "[ID Scope]";
    ```

    Pour référence, la variable `global_prov_uri`, qui permet à l’API d’inscription du client IoT Hub `IoTHubClient_LL_CreateFromDeviceAuth` de se connecter à l’instance Device Provisioning Service indiquée.

4. Dans la fonction **main()** du même fichier, commentez/supprimez les commentaires de la variable `hsm_type` qui correspond au mécanisme d’attestation utilisé par le logiciel d’inscription de votre appareil (TPM ou X.509) : 

    ```c
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

5. Enregistrez vos modifications et régénérez l’exemple **prov\_dev\_client\_sample** en sélectionnant la commande « Build solution » à partir du menu « Générer ». 

6. Cliquez avec le bouton droit sur le projet **prov\_dev\_client\_sample** sous le dossier **Provision\_Samples**, et sélectionnez **Définir comme projet de démarrage**. N’EXÉCUTEZ PAS l’exemple d’application pour le moment.

> [!IMPORTANT]
> N’exécutez pas/ne démarrez pas l’appareil pour le moment ! D’abord, vous devez terminer le processus en inscrivant l’appareil auprès du service d’approvisionnement des appareils avant de démarrer l’appareil. La section Étapes suivantes ci-dessous vous guide vers l’article suivant.

### <a name="sdk-apis-used-during-registration-for-reference-only"></a>API du Kit de développement logiciel (SDK) utilisées lors de l’inscription (pour référence uniquement)

Pour référence, le Kit de développement logiciel (SDK) fournit les API suivantes pour votre application à utiliser lors de l’inscription. Ces API permettent à votre appareil de se connecter et d’être inscrit auprès du service d’approvisionnement des appareils au démarrage. En retour, votre appareil reçoit les informations requises pour établir une connexion à votre instance IoT Hub :

```C
// Creates a Provisioning Client for communications with the Device Provisioning Client Service.  
PROV_DEVICE_LL_HANDLE Prov_Device_LL_Create(const char* uri, const char* scope_id, PROV_DEVICE_TRANSPORT_PROVIDER_FUNCTION protocol)

// Disposes of resources allocated by the provisioning Client.
void Prov_Device_LL_Destroy(PROV_DEVICE_LL_HANDLE handle)

// Asynchronous call initiates the registration of a device.
PROV_DEVICE_RESULT Prov_Device_LL_Register_Device(PROV_DEVICE_LL_HANDLE handle, PROV_DEVICE_CLIENT_REGISTER_DEVICE_CALLBACK register_callback, void* user_context, PROV_DEVICE_CLIENT_REGISTER_STATUS_CALLBACK reg_status_cb, void* status_user_ctext)

// Api to be called by user when work (registering device) can be done
void Prov_Device_LL_DoWork(PROV_DEVICE_LL_HANDLE handle)

// API sets a runtime option identified by parameter optionName to a value pointed to by value
PROV_DEVICE_RESULT Prov_Device_LL_SetOption(PROV_DEVICE_LL_HANDLE handle, const char* optionName, const void* value)
```

Vous pouvez également estimer qu’un affinement de votre application d’enregistrement Device Provisioning Service Client est nécessaire à l’aide d’un appareil simulé dans un premier temps et d’une configuration de service de test. Une fois que votre application fonctionne dans l’environnement de test, vous pouvez la générer pour votre appareil et copier le fichier exécutable sur l’image de votre appareil. 

## <a name="clean-up-resources"></a>Supprimer des ressources

À ce stade, vous pouvez avoir les services IoT Hub et Device Provisioning Service qui s’exécutent dans le portail. Si vous souhaitez abandonner la configuration de l’approvisionnement d’appareils et/ou retarder la fin de cette série de didacticiels, nous vous recommandons d’arrêter ces services pour éviter des coûts inutiles.

1. Dans le menu de gauche du portail Azure, cliquez sur **Toutes les ressources**, puis sélectionnez votre service Device Provisioning. Dans la partie supérieure du panneau **Toutes les ressources**, cliquez sur **Supprimer**.  
2. À partir du menu de gauche, dans le portail Azure, cliquez sur **Toutes les ressources**, puis sélectionnez votre IoT Hub. Dans la partie supérieure du panneau **Toutes les ressources**, cliquez sur **Supprimer**.  

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Générer un Kit de développement logiciel (SDK) Device Provisioning Service Client spécifique à une plateforme
> * Extraire les artefacts de sécurité
> * Créer le logiciel d’inscription d’appareils

Passez au didacticiel suivant afin d’apprendre à provisionner l’appareil pour votre hub IoT en l’inscrivant auprès du service IoT Hub Device Provisioning à des fins de provisionnement automatique.

> [!div class="nextstepaction"]
> [Provisionner l’appareil pour votre hub IoT](tutorial-provision-device-to-hub.md)

