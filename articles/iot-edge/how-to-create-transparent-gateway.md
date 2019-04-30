---
title: Créer un appareil de passerelle transparent - Azure IoT Edge | Microsoft Docs
description: Utiliser un appareil Azure IoT Edge en tant que passerelle transparente pouvant traiter des informations d’appareils en aval
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 722ee6197b467454818026c960e1ce0e5b39efb4
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766309"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Configurer un appareil IoT Edge en tant que passerelle transparente

Cet article donne des instructions détaillées pour configurer des appareils IoT Edge en tant que passerelles transparentes permettant à d’autres appareils de communiquer avec IoT Hub. Dans cet article, le terme *passerelle IoT Edge* fait référence à un appareil IoT Edge utilisé comme passerelle transparente. Pour plus d’informations, consultez [comment un appareil IoT Edge peut être utilisé en tant que passerelle](./iot-edge-as-gateway.md).

>[!NOTE]
>Actuellement :
> * Si la passerelle est déconnectée d’IoT Hub, les appareils en aval ne peuvent pas s’authentifier auprès de la passerelle.
> * Les appareils Edge ne peuvent pas se connecter aux passerelles IoT Edge. 
> * Les appareils en aval ne peuvent pas utiliser le chargement de fichier.

Pour qu’un appareil de fonctionne comme une passerelle, il doit être capable de se connecter en toute sécurité aux appareils en aval. Azure IoT Edge vous permet d’utiliser une infrastructure à clé publique (PKI) pour configurer des connexions sécurisées entre les appareils. Dans ce cas, nous autorisons un appareil en aval à se connecter à un appareil IoT Edge faisant office de passerelle transparente. Pour maintenir la sécurité raisonnable, l’appareil en aval doit confirmer l’identité de l’appareil IoT Edge. Vous souhaitez que vos appareils se connectant à vos passerelles uniquement, pas les passerelles potentiellement malveillants.

Un appareil en aval est une application ou une plateforme dont l’identité a été créée avec le service cloud [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub). Dans de nombreux cas, ces applications utilisent [Azure IoT device SDK](../iot-hub/iot-hub-devguide-sdks.md). Pour des raisons pratiques, un appareil en aval peut même être une application fonctionnant sur l’appareil de passerelle IoT Edge proprement dit. 

Vous pouvez créer n’importe quelle infrastructure de certificat permettant l’approbation requise pour votre topologie de l’appareil à la passerelle. Dans cet article, nous partons du principe que vous utiliseriez pour activer la même configuration de certificat [sécurité de l’autorité de certification X.509](../iot-hub/iot-hub-x509ca-overview.md) dans IoT Hub, ce qui implique un certificat d’autorité de certification X.509 associé à un hub IoT spécifique (l’AC propriétaire IoT hub) et une série de certificats, signés avec cette autorité de certification et une autorité de certification pour l’appareil IoT Edge.

![Configuration du certificat de la passerelle](./media/how-to-create-transparent-gateway/gateway-setup.png)

La passerelle présente ses IoT Edge certificat d’autorité de certification de périphérique à l’appareil en aval au cours de l’ouverture de la connexion. L’appareil en aval vérifie pour vous assurer que le certificat d’autorité de certification d’appareil IoT Edge est signé par le certificat d’autorité de certification de propriétaire. Ce processus permet à l’appareil en aval de vérifier que la passerelle provient d’une source approuvée.

Les étapes suivantes vous guident tout au long du processus de création des certificats et de leur installation au bon endroit.

## <a name="prerequisites"></a>Conditions préalables

Un appareil Azure IoT Edge à configurer comme passerelle. Utilisez les étapes d’installation de IoT Edge pour les systèmes d’exploitation suivants :
* [Windows](./how-to-install-iot-edge-windows.md)
* [Linux x64](./how-to-install-iot-edge-linux.md)
* [Linux ARM32](./how-to-install-iot-edge-linux-arm.md)

Vous pouvez utiliser n’importe quel ordinateur pour générer les certificats, puis les copier sur votre appareil IoT Edge.

>[!NOTE]
>Le « nom de passerelle » permet de créer les certificats dans cette instruction, doit être le même nom que celles utilisées en tant que nom d’hôte dans votre fichier de config.yaml IoT Edge et GatewayHostName dans la chaîne de connexion de l’appareil en aval. Le « nom de passerelle » doit être résolu en une adresse IP, à l’aide de DNS ou une entrée de fichier hôte. Communication basée sur le protocole utilisé (MQTTS:8883 / AMQPS:5671 / HTTPS:433) doit être possible entre l’appareil en aval et le soit transparent IoT Edge. Si un pare-feu est entre les deux, le port correspondant doit être ouvert.

## <a name="generate-certificates-with-windows"></a>Générer des certificats avec Windows

Suivez les étapes de cette section pour générer des certificats de test sur un appareil Windows. Vous pouvez utiliser ces étapes pour générer les certificats sur un appareil Windows IoT Edge. Ou bien, vous pouvez générer les certificats sur votre ordinateur de développement Windows et puis les copier sur n’importe quel appareil IoT Edge. 

Les certificats générés dans cette section ne doivent servir qu’à des fins de test. 

### <a name="install-openssl"></a>Installer OpenSSL

Installez OpenSSL pour Windows sur l’ordinateur que vous utilisez pour générer les certificats. Il existe plusieurs moyens d’installer OpenSSL :

   >[!NOTE]
   >Si vous avez déjà installé OpenSSL sur votre appareil Windows, vous pouvez ignorer cette étape. Vérifiez en revanche que openssl.exe est disponible dans votre variable d’environnement PATH.

* **Plus facile :** Téléchargez et installez des [fichiers binaires OpenSSL tiers](https://wiki.openssl.org/index.php/Binaries), par exemple, à partir de [ce projet sur SourceForge](https://sourceforge.net/projects/openssl/). Ajoutez le chemin d’accès complet à openssl.exe à votre variable d’environnement PATH. 
   
* **Recommandé :** Téléchargez le code source OpenSSL et générez les fichiers binaires sur votre machine vous-même ou en utilisant [vcpkg](https://github.com/Microsoft/vcpkg). Les instructions ci-dessous utilisent vcpkg pour télécharger le code source, compiler et installer OpenSSL sur votre machine Windows en suivant des étapes simples.

   1. Accédez au répertoire où vous souhaitez installer vcpkg. Nous appellerons ce répertoire *\<VCPKGDIR>*. Suivez les instructions pour télécharger et installer [vcpkg](https://github.com/Microsoft/vcpkg).
   
   2. Une fois que vcpkg est installé, à partir d’une invite de Powershell, exécutez la commande suivante pour installer le package OpenSSL pour Windows x64. L’installation prend généralement 5 minutes environ.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. Ajoutez `<VCPKGDIR>\installed\x64-windows\tools\openssl` à votre variable d’environnement PATH afin que le fichier openssl.exe soit appelable.

### <a name="prepare-creation-scripts"></a>Préparer les scripts de création

Azure IoT device SDK pour C contient des scripts permettant de générer des certificats de test. Cette section a pour objectif de cloner le Kit SDK et de configurer PowerShell.

1. Ouvrez une fenêtre PowerShell en mode administrateur. 

2. Clonez le référentiel git contenant les scripts de génération de certificats qui ne sont pas en production. Ces scripts vous permettent de créer les certificats nécessaires pour configurer une passerelle transparente. Utilisez la commande `git clone` ou [téléchargez le fichier zip](https://github.com/Azure/azure-iot-sdk-c/archive/master.zip). 

   ```powershell
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

3. Accédez au répertoire dans lequel vous souhaitez travailler. Nous appellerons ce répertoire *\<WRKDIR>*.  Tous les fichiers seront créés dans ce répertoire.

4. Copiez les fichiers de configuration et de script dans votre répertoire de travail. 

   ```powershell
   copy <path>\azure-iot-sdk-c\tools\CACertificates\*.cnf .
   copy <path>\azure-iot-sdk-c\tools\CACertificates\ca-certs.ps1 .
   ```

   Si vous avez téléchargé le Kit SDK sous forme de fichier zip, le nom du dossier est `azure-iot-sdk-c-master` et le reste du chemin d’accès est le même. 

5. Définissez la variable d’environnement OPENSSL_CONF pour utiliser le fichier de configuration openssl_root_ca.cnf.

    ```powershell
    $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
    ```

6. Activez PowerShell pour exécuter les scripts.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

7. Intégrez les fonctions, utilisées par les scripts, à l’espace de noms global de PowerShell.
   
   ```powershell
   . .\ca-certs.ps1
   ```

8. Vérifiez qu’OpenSSL est correctement installé et qu’il n’y aura pas de collisions de noms avec des certificats existants. Si des problèmes se produisent, le script doit décrire comment les corriger sur votre système.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>Créer des certificats

Cette section vise à créer trois certificats, puis à les connecter dans une chaîne. Le fait de placer les certificats dans un fichier de chaîne permet de les installer facilement sur un appareil de passerelle IoT Edge et tous les appareils en aval.  

1. Créez le certificat d’autorité de certification propriétaire et faites-le signer un certificat intermédiaire. Les certificats sont tous placés dans *\<WRKDIR>*.

      ```powershell
      New-CACertsCertChain rsa
      ```

2. Créer des IoT Edge de certificat d’autorité de certification de périphérique et la clé privée avec la commande suivante. Donnez un nom à l’appareil de passerelle, qui sera utilisé pour nommer les fichiers et lors de la génération du certificat. 

   ```powershell
   New-CACertsEdgeDevice "<gateway name>"
   ```

3. Créer une chaîne de certificats à partir du certificat d’autorité de certification de propriétaire, un certificat intermédiaire et un certificat d’autorité de certification de périphérique IoT Edge avec la commande suivante. 

   ```powershell
   Write-CACertsCertificatesForEdgeDevice "<gateway name>"
   ```

   Le script crée la clé et les certificats ci-dessous :
   * `<WRKDIR>\certs\new-edge-device.*`
   * `<WRKDIR>\private\new-edge-device.key.pem`
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

## <a name="generate-certificates-with-linux"></a>Générer des certificats avec Linux

Suivez les étapes de cette section pour générer des certificats de test sur un appareil Linux. Vous pouvez générer directement les certificats sur votre appareil IoT Edge, ou utiliser un ordinateur distinct et copier les certificats finaux sur n’importe quel appareil IoT Edge, à condition que son système d’exploitation soit pris en charge. 

### <a name="prepare-creation-scripts"></a>Préparer les scripts de création

1. Clonez le référentiel git contenant les scripts de génération de certificats qui ne sont pas en production. Ces scripts vous permettent de créer les certificats nécessaires pour configurer une passerelle transparente. 

   ```bash
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

2. Accédez au répertoire dans lequel vous souhaitez travailler. Nous appellerons ce répertoire *\<WRKDIR>*.  Tous les fichiers seront créés dans ce répertoire.
  
3. Copiez les fichiers de configuration et de script dans votre répertoire de travail.

   ```bash
   cp <path>/azure-iot-sdk-c/tools/CACertificates/*.cnf .
   cp <path>/azure-iot-sdk-c/tools/CACertificates/certGen.sh .
   ```

4. Configurez OpenSSL pour générer des certificats à l’aide du script fourni. 

   ```bash
   chmod 700 certGen.sh 
   ```

### <a name="create-certificates"></a>Créer des certificats

Cette section vise à créer trois certificats, puis à les connecter dans une chaîne. Le fait de placer les certificats dans un fichier de chaîne permet de les installer facilement sur un appareil de passerelle IoT Edge et tous les appareils en aval.  

1. Créez le certificat d’autorité de certification propriétaire et un certificat intermédiaire. Ces certificats sont placés dans *\<WRKDIR>*.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Le script crée les certificats et les clés suivants :
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>/certs/azure-iot-test-only.intermediate.cert.pem`
   * `<WRKDIR>/private/azure-iot-test-only.root.ca.key.pem`
   * `<WRKDIR>/private/azure-iot-test-only.intermediate.key.pem`

2. Créer des IoT Edge de certificat d’autorité de certification de périphérique et la clé privée avec la commande suivante. Donnez un nom à l’appareil de passerelle, qui sera utilisé pour nommer les fichiers et lors de la génération du certificat. 

   ```bash
   ./certGen.sh create_edge_device_certificate "<gateway name>"
   ```

   Le script crée la clé et les certificats ci-dessous :
   * `<WRKDIR>/certs/new-edge-device.*`
   * `<WRKDIR>/private/new-edge-device.key.pem`

3. Créer une chaîne de certificats appelée **nouveau-edge-device-intégral-chain.cert.pem** à partir du certificat d’autorité de certification de propriétaire, un certificat intermédiaire et un certificat d’autorité de certification d’appareil IoT Edge.

   ```bash
   cat ./certs/new-edge-device.cert.pem ./certs/azure-iot-test-only.intermediate.cert.pem ./certs/azure-iot-test-only.root.ca.cert.pem > ./certs/new-edge-device-full-chain.cert.pem
   ```

## <a name="install-certificates-on-the-gateway"></a>Installer des certificats sur la passerelle

Maintenant que vous avez créé une chaîne de certification, il vous reste à l’installer sur l’appareil de passerelle IoT Edge et à configurer le runtime IoT Edge de façon à ce qu’il référence les nouveaux certificats. 

1. Copiez les fichiers suivants à partir de *\<WRKDIR>*. Enregistrez-les à l’emplacement de votre choix sur votre appareil IoT Edge. Nous appellerons ce répertoire de destination *\<CERTDIR>*. 

   Si vous avez généré les certificats sur l’appareil IoT Edge proprement dit, vous pouvez ignorer cette étape et utiliser le chemin d’accès au répertoire de travail.

   * Certificat d’autorité de certification d’appareil : `<WRKDIR>\certs\new-edge-device-full-chain.cert.pem`
   * Clé privée d’autorité de certification d’appareil : `<WRKDIR>\private\new-edge-device.key.pem`
   * Autorité de certification propriétaire : `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. Ouvrez le fichier config de démon de sécurité IoT Edge. 

   * Windows : `C:\ProgramData\iotedge\config.yaml`
   * Linux : `/etc/iotedge/config.yaml`

3. Définissez les propriétés **certificates** du fichier config.yaml sur le chemin d’accès où vous avez placé les fichiers de certificat et de clé sur l’appareil IoT Edge.

   * Windows :

      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>\\certs\\new-edge-device-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>\\private\\new-edge-device.key.pem"
        trusted_ca_certs: "<CERTDIR>\\certs\\azure-iot-test-only.root.ca.cert.pem"
      ```
   
   * Linux : 
      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>/certs/new-edge-device-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>/private/new-edge-device.key.pem"
        trusted_ca_certs: "<CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem"
      ```

4. Sur les appareils Linux, assurez-vous que l’utilisateur **iotedge** autorisations de lecture pour le répertoire contenant les certificats. 

## <a name="deploy-edgehub-to-the-gateway"></a>Déployer EdgeHub sur la passerelle

Lorsque vous installez tout d’abord IoT Edge sur un appareil, le module qu’un seul système démarre automatiquement : l’agent IoT Edge. Pour que votre appareil fonctionne comme une passerelle, il vous faut les deux modules système. Si vous n’avez pas déployé tous les modules sur votre périphérique de passerelle avant, créez un déploiement pour votre appareil démarrer le deuxième module système, le hub IoT Edge. Le déploiement apparaîtra vide, car vous n’ajoutez aucun module dans l’Assistant, mais il déploiera les deux modules système. 

Pour savoir quels modules sont en cours d’exécution sur un appareil, utilisez la commande `iotedge list`.

1. Accédez à votre hub IoT dans le portail Azure.

2. Accédez à **IoT Edge** et sélectionnez l’appareil IoT Edge à utiliser comme passerelle.

3. Sélectionnez **Définir modules**.

4. Sélectionnez **Suivant**.

5. La page **Spécifier des itinéraires** devrait comporter un itinéraire par défaut qui envoie à IoT Hub tous les messages de tous les modules. Si ce n’est pas le cas, ajoutez le code suivant, puis sélectionnez **Suivant**.

   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```

6. Sur la page **Vérifier le modèle**, sélectionnez **Envoyer**.

## <a name="open-ports-on-gateway-device"></a>Ports ouverts sur le périphérique de passerelle

Les appareils IoT Edge standards n’avez pas besoin les connexions entrantes de la fonction, étant donné que toutes les communications avec IoT Hub s’effectue via les connexions sortantes. Toutefois, les périphériques de passerelle sont différents, car ils doivent être en mesure de recevoir des messages à partir de leurs appareils en aval.

Pour un scénario de passerelle fonctionne, au moins un des protocoles pris en charge du hub IoT Edge doit être ouvert pour le trafic entrant à partir d’appareils en aval. Les protocoles pris en charge sont MQTT, AMQP et HTTPS.

| Port | Protocol |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT + WS <br> AMQP + WS | 

## <a name="route-messages-from-downstream-devices"></a>Acheminer les messages à partir des appareils en aval
Le runtime IoT Edge peut acheminer les messages envoyés à partir des appareils en aval comme les messages envoyés par les modules. Cette fonctionnalité vous permet de vous permettent d’effectuer l’analytique dans un module en cours d’exécution sur la passerelle avant d’envoyer des données vers le cloud. 

Actuellement, la façon d’acheminer les messages envoyés par les appareils en aval consiste à les différencier des messages envoyés par les modules. Les messages envoyés par les modules contiennent tous une propriété système appelée **connectionModuleId** mais pas les messages envoyés par les appareils en aval. Vous pouvez utiliser la clause WHERE de l’itinéraire pour exclure tout message contenant cette propriété système. 

L’itinéraire ci-dessous sert à envoyer des messages depuis un appareil en aval vers un nom de module `ai_insights`.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

Pour plus d’informations sur le routage des messages, consultez [Déployer des modules et établir des itinéraires](./module-composition.md#declare-routes).

[!INCLUDE [iot-edge-extended-ofline-preview](../../includes/iot-edge-extended-offline-preview.md)]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que votre appareil IoT Edge fonctionne comme une passerelle transparente, il vous faut configurer vos appareils en aval de façon à ce qu’ils approuvent la passerelle et lui envoient des messages. Pour plus d’informations, voir [Connecter un appareil en aval à une passerelle Azure IoT Edge](how-to-connect-downstream-device.md).
