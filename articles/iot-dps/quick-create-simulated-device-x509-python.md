---
title: Démarrage rapide – Provisionner un appareil X.509 simulé auprès d’Azure IoT Hub à l’aide de Python
description: 'Démarrage rapide : Créer et provisionner un appareil X.509 simulé auprès du service IoT Hub Device Provisioning (DPS) à l’aide du SDK d’appareil Python. Ce démarrage rapide utilise des inscriptions individuelles.'
author: wesmc7777
ms.author: wesmc
ms.date: 01/29/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: python
ms.custom: mvc, devx-track-python
ms.openlocfilehash: c151f78c6164cc62aac618a141a26eb1da574e3c
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218359"
---
# <a name="quickstart-create-and-provision-a-simulated-x509-device-using-python-device-sdk-for-iot-hub-device-provisioning-service"></a>Démarrage rapide : Créer et approvisionner un appareil X.509 simulé auprès du service IoT Hub Device Provisioning à l’aide du Python Device SDK

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Dans ce guide de démarrage rapide, vous provisionnez une machine de développement en tant qu’appareil Python X.509. Vous utilisez un exemple de code d’appareil du [kit SDK Azure IoT Python](https://github.com/Azure/azure-iot-sdk-python) pour connecter l’appareil à votre hub IoT. Une inscription individuelle est utilisée avec le service DPS (Device Provisioning) dans cet exemple.

## <a name="prerequisites"></a>Prérequis

- Connaissance des concepts liés au [provisionnement](about-iot-dps.md#provisioning-process).
- Exécution des étapes décrites dans [Configurer le service IoT Hub Device Provisioning avec le portail Azure](./quick-setup-auto-provision.md).
- Compte Azure avec un abonnement actif. [Créez-en un gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Python 3.5.3 ou version ultérieure](https://www.python.org/downloads/)
- [Git](https://git-scm.com/download/).


[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>Préparer l’environnement 

1. Assurez-vous que l’élément `git` est installé sur votre machine et est ajouté aux variables d’environnement accessibles à la fenêtre de commande. Consultez la section relative aux [outils clients de Software Freedom Conservancy](https://git-scm.com/download/) pour accéder à la dernière version des outils `git` à installer, qui inclut **Git Bash**, l’application de ligne de commande que vous pouvez utiliser pour interagir avec votre référentiel Git local. 

2. Ouvrez une invite Git Bash. Clonez le dépôt GitHub pour le [kit SDK Azure IoT Python](https://github.com/Azure/azure-iot-sdk-python).
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```


## <a name="create-a-self-signed-x509-device-certificate"></a>Créer un certificat auto-signé d’appareil X.509 

Dans cette section, vous allez créer un certificat X.509 autosigné. Il est important de garder à l’esprit les points suivants :

* Les certificats auto-signés sont destinés aux tests uniquement et ne doivent pas être utilisés en production.
* La date d’expiration par défaut d’un certificat auto-signé est d’un an.

Si vous ne disposez pas déjà de vos certificats d’appareil pour authentifier un appareil, vous pouvez créer un certificat autosigné avec OpenSSL à des fins de test dans le cadre de cet article.  OpenSSL est inclus dans l’installation de Git. 

1. Exécutez la commande suivante dans l’invite Git Bash.

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
    
2. Quand vous serez invité à **entrer la phrase secrète PEM**, utilisez la phrase secrète `1234` à des fins de test dans le cadre de cet article.    

3. Quand vous serez invité à **vérifier la phrase secrète PEM**, réutilisez la phrase secrète `1234`.    

Un fichier de certificat de test (*python-device.pem*) et un fichier de clé privée (*python-device.key.pem*) sont générés dans le répertoire où vous avez exécuté la commande `openssl`.


## <a name="create-an-individual-enrollment-entry-in-dps"></a>Créer une entrée d’inscription individuelle dans le service DPS


Le service Azure IoT Device Provisioning prend en charge deux types d’inscriptions :

- [Groupes d’inscription](concepts-service.md#enrollment-group) : utilisés pour inscrire plusieurs appareils connexes.
- [Inscriptions individuelles](concepts-service.md#individual-enrollment) : utilisées pour inscrire un seul appareil.

Cet article décrit l’inscription individuelle d’un appareil à provisionner auprès d’un hub IoT.

1. Connectez-vous au portail Azure, sélectionnez **Toutes les ressources** dans le menu de gauche et ouvrez votre service de provisionnement.

2. Dans le menu du service Device Provisioning, sélectionnez **Gérer les inscriptions**. Sélectionnez l’onglet **Inscriptions individuelles**, puis le bouton **Ajouter une inscription individuelle** dans la partie supérieure. 

3. Dans le volet **Ajouter une inscription**, entrez les informations suivantes :
   - Sélectionnez **X.509** comme *mécanisme* d’attestation d’identité.
   - Sous *Fichier .pem ou .cer du certificat principal*, choisissez *Sélectionner un fichier* pour sélectionner le fichier de certificat **python-device.pem**, si vous utilisez le certificat de test créé auparavant.
   - Si vous le souhaitez, vous pouvez fournir les informations suivantes :
     - Sélectionnez un hub IoT lié à votre service d’approvisionnement.
     - Mettez à jour l’**état du jumeau d’appareil initial** à l’aide de la configuration initiale de votre choix pour l’appareil.
   - Cela fait, appuyez sur le bouton **Enregistrer**. 

     [![Ajouter une inscription individuelle pour l’attestation X.509 dans le portail](./media/python-quick-create-simulated-device-x509/device-enrollment.png)](./media/python-quick-create-simulated-device-x509/device-enrollment.png#lightbox)

   Une fois l’inscription réussie, votre appareil X.509 apparaît en tant que **Python-device-01** dans la colonne *ID d’inscription* sous l’onglet *Inscriptions individuelles*. Cette valeur d’inscription provient du nom d’objet sur le certificat de l’appareil. 

## <a name="simulate-the-device"></a>Simuler l’appareil

L’exemple de provisionnement Python, [provision_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/provision_x509.py) se trouve dans le répertoire `azure-iot-sdk-python/azure-iot-device/samples/async-hub-scenarios`. Cet exemple utilise six variables d’environnement pour authentifier et provisionner un appareil IoT à l’aide du service DPS. Ces variables d’environnement sont :

| Nom de la variable              | Description                                     |
| :------------------------- | :---------------------------------------------- |
| `PROVISIONING_HOST`        |  Cette valeur est le point de terminaison global utilisé pour la connexion à votre ressource DPS |    
| `PROVISIONING_IDSCOPE`     |  Cette valeur est l’étendue de l’ID de votre ressource DPS |    
| `DPS_X509_REGISTRATION_ID` |  Cette valeur est l’ID de votre appareil. Elle doit également correspondre au nom d’objet sur le certificat de l’appareil |    
| `X509_CERT_FILE`           |  Nom de fichier du certificat de l’appareil |    
| `X509_KEY_FILE`            |  Nom de fichier de la clé privée du certificat de votre appareil |
| `PASS_PHRASE`              |  Phrase secrète utilisée pour chiffrer le certificat et le fichier de clé privée (`1234`). |    

1. Dans le menu du service Device Provisioning, sélectionnez **Vue d’ensemble**. Notez les valeurs _Étendue de l’ID_ et _Point de terminaison d’appareil global_.

    ![Informations sur le service](./media/python-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. À l’invite Git Bash, utilisez les commandes suivantes pour ajouter les variables d’environnement du point de terminaison d’appareil global et de l’étendue de l’ID.

    ```bash
    $export PROVISIONING_HOST=global.azure-devices-provisioning.net
    $export PROVISIONING_IDSCOPE=<ID scope for your DPS resource>
    ```

3. L’ID d’inscription de l’appareil IoT doit correspondre au nom d’objet sur son certificat d’appareil. Si vous avez généré un certificat de test autosigné, `Python-device-01` correspond au nom d’objet et à l’ID d’inscription de l’appareil. 

    Si vous disposez déjà d’un certificat d’appareil, vous pouvez utiliser `certutil` afin de vérifier le nom commun d’objet utilisé pour votre appareil, comme indiqué ci-dessous dans le cas d’un certificat de test autosigné :

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

    Dans l’invite Git Bash, définissez la variable d’environnement de l’ID d’inscription de la façon suivante :

    ```bash
    $export DPS_X509_REGISTRATION_ID=Python-device-01
    ```

4. Dans l’invite Git Bash, définissez les variables d’environnement du fichier de certificat, du fichier de clé privée et de la phrase secrète.

    ```bash
    $export X509_CERT_FILE=./python-device.pem
    $export X509_KEY_FILE=./python-device.key.pem
    $export PASS_PHRASE=1234
    ```

5. Passez en revue le code pour [provision_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/provision_x509.py). Si vous n’utilisez pas **Python version 3.7** ou une version ultérieure, [changez le code comme indiqué ici](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios#advanced-iot-hub-scenario-samples-for-the-azure-iot-hub-device-sdk) pour remplacer `asyncio.run(main())`, puis enregistrez la modification. 

6. Exécutez l’exemple. L’exemple permet de se connecter, de provisionner l’appareil auprès d’un hub et d’envoyer des messages de test au hub.

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

7. Dans le portail, accédez au hub IoT lié à votre service de provisionnement, puis ouvrez le panneau **Appareils IoT** situé dans le menu de gauche, sous la section **Explorateurs**. En cas de réussite de l’approvisionnement de l’appareil simulé X.509 sur le Hub, son ID de périphérique s’affiche sur le panneau **Device Explorer**, avec un *ÉTAT* **activé**. Notez que vous devrez peut-être appuyer sur le bouton **Actualiser** dans la partie supérieure si vous avez déjà ouvert le panneau avant d’exécuter l’exemple d’application de l’appareil. 

    ![L’appareil est inscrit avec le hub IoT](./media/python-quick-create-simulated-device-x509/registration.png) 

> [!NOTE]
> Si vous avez modifié la valeur par défaut de l’*état du jumeau d’appareil initial* dans l’entrée d’inscription de votre appareil, l’état du jumeau souhaité peut être extrait du hub et agir en conséquence. Pour en savoir plus, consultez [Comprendre et utiliser les jumeaux d’appareil IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).
>

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous envisagez de manipuler et d’explorer davantage l’exemple de client d’appareil, ne nettoyez pas les ressources créées dans ce guide de démarrage rapide. Sinon, effectuez les étapes suivantes pour supprimer toutes les ressources créées par ce guide.

1. Fermez la fenêtre de sortie de l’exemple de client d’appareil sur votre machine.
2. Dans le menu de gauche du portail Azure, sélectionnez **Toutes les ressources**, puis votre service Device Provisioning. Ouvrez le panneau **Gérer les inscriptions** pour votre service, puis sélectionnez l’onglet **Inscriptions individuelles**. Cochez la case à côté de l’*ID D’INSCRIPTION* de l’appareil que vous avez inscrit dans ce guide de démarrage rapide, puis appuyez sur le bouton **Supprimer** dans la partie supérieure du volet. 
3. À partir du menu de gauche, dans le portail Azure, sélectionnez **Toutes les ressources**, puis votre hub IoT. Ouvrez le panneau **Appareils IoT** pour votre hub, cochez la case en regard de l’*ID D’APPAREIL* de l’appareil que vous avez inscrit dans ce guide de démarrage rapide, puis appuyez sur le bouton **Supprimer** dans la partie supérieure du volet.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un appareil X.509 simulé sur votre machine de développement. Vous avez provisionné cet appareil auprès de votre hub IoT en utilisant le service Azure IoT Hub Device Provisioning sur le portail. Pour apprendre à inscrire un appareil X.509 programmatiquement, passez au guide de démarrage rapide correspondant. 

> [!div class="nextstepaction"]
> [Démarrage rapide Azure - Inscrire des appareils X.509 auprès du service Azure IoT Hub Device Provisioning](quick-enroll-device-x509-python.md)
