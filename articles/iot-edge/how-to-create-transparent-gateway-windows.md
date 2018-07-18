---
title: Créer une passerelle transparente avec Azure IoT Edge - Windows | Microsoft Docs
description: Utiliser Azure IoT Edge pour créer une passerelle transparente pouvant traiter des informations de plusieurs appareils
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 6/20/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: edc44f0ab2d2cc737807dd8ad543997cdd75bd43
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37034440"
---
# <a name="create-a-windows-iot-edge-device-that-acts-as-a-transparent-gateway"></a>Créer un appareil IoT Edge Windows servant de passerelle transparente

Cet article fournit des instructions détaillées pour utiliser un appareil IoT Edge en tant que passerelle transparente. Dans le cadre de cet article, le terme *passerelle IoT Edge* fait référence à un appareil IoT Edge utilisé en tant que passerelle transparente. Pour plus d’informations, consultez la rubrique [Comment un appareil IoT Edge peut être utilisé en tant que passerelle][lnk-edge-as-gateway], qui propose une vue d’ensemble conceptuelle.

>[!NOTE]
>Actuellement :
> * Si la passerelle est déconnectée d’IoT Hub, les appareils en aval ne peuvent pas s’authentifier auprès de la passerelle.
> * les appareils IoT Edge ne peuvent pas se connecter aux passerelles IoT Edge.
> * Les appareils en aval ne peuvent pas utiliser de chargement de fichier.

La partie la plus complexe de la création d’une passerelle transparente consiste à connecter de manière sécurisée la passerelle aux appareils en aval. Azure IoT Edge vous permet d’utiliser l’infrastructure à clé publique pour configurer des connexions TLS sécurisées entre ces appareils. Dans ce cas, nous autorisons un appareil en aval à se connecter à un appareil IoT Edge faisant office de passerelle transparente.  Pour préserver une sécurité raisonnable, l’appareil en aval doit confirmer l’identité de l’appareil Edge, car vous souhaitez que vos appareils ne se connectent qu’à vos passerelles, et non pas à une passerelle potentiellement malveillante.

Vous pouvez créer n’importe quelle infrastructure de certificat permettant l’approbation requise pour votre topologie de l’appareil à la passerelle. Dans cet article, nous nous basons sur la même configuration de certificat que vous utiliseriez pour activer [la sécurité AC X.509][lnk-iothub-x509] dans IoT Hub, ce qui implique un certificat AC X.509 associé à un hub IoT spécifique (l’autorité de certification propriétaire du hub IoT), ainsi qu’une série de certificats, signés par cette autorité de certification, et une autorité de certification pour l’appareil Edge.

![Configuration de la passerelle][1]

La passerelle présente son certificat d’autorité de certification d’appareil Edge à l’appareil en aval au cours de l’ouverture de la connexion. L’appareil en aval vérifie que le certificat d’autorité de certification d’appareil Edge est signé par le certificat d’autorité de certification propriétaire. Ce processus permet à l’appareil en aval de vérifier que la passerelle provient d’une source approuvée.

Les étapes suivantes vous guident tout au long du processus de création des certificats et de leur installation au bon endroit.

## <a name="prerequisites"></a>Prérequis
1.  [Installez le runtime Azure IoT Edge][lnk-install-windows-x64] sur un appareil Windows que vous souhaitez utiliser comme passerelle transparente.

1. Obtenez OpenSSL pour Windows. Vous pouvez installer OpenSSL de nombreuses façons. Pour cela, les instructions présentées ici utilisent vcpkg.
   1. Téléchargez et installez vcpkg en exécutant les commandes suivantes dans une fenêtre PowerShell d’administrateur. Accédez au répertoire où vous souhaitez installer OpenSSL ; nous appellerons ce répertoire `$VCPKGDIR`.

   ```PowerShell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg integrate install
   .\vcpkg install openssl:x64-windows
   ```

   1. Définissez la variable d’environnement `OPENSSL_ROOT_DIR` sur `$VCPKGDIR\vcpkg\packages\openssl_x64-windows` et ajoutez `$VCPKGDIR\vcpkg\packages\openssl_x64-windows\tools\openssl` à votre variable d’environnement `PATH`.

1.  À l’aide de la commande suivante, récupérez les scripts servant à générer les certificats de non-production requis. Ces scripts vous permettent de créer les certificats nécessaires pour configurer une passerelle transparente.

   ```PowerShell
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

1. Accédez au répertoire dans lequel vous souhaitez travailler. Nous appellerons cet emplacement $WRKDIR.  Tous les fichiers seront créés dans ce répertoire.

   cd $WRKDIR

1. Copiez les fichiers de configuration et de script dans votre répertoire de travail.
   ```PowerShell
   copy azure-iot-sdk-c\tools\CACertificates\*.cnf .
   copy azure-iot-sdk-c\tools\CACertificates\ca-certs.ps1 .
   ```

1. Autorisez PowerShell à exécuter les scripts en exécutant la commande suivante.
   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted
   ```

1. Intégrez les fonctions, utilisées par les scripts, à l’espace de noms global de PowerShell en effectuant un appel de source de type « dot sourcing » à l’aide de la commande suivante.
   ```PowerShell
   . .\ca-certs.ps1
   ```

1. Vérifiez qu’OpenSSL a été installé correctement et assurez-vous de l’absence de collisions de noms avec des certificats existants en exécutant la commande suivante.
   ```PowerShell
   Test-CACertsPrerequisites
   ```

## <a name="certificate-creation"></a>Création du certificat
1.  Créez le certificat d’autorité de certification propriétaire et un certificat intermédiaire. Ils sont tous placés dans `$WRKDIR`.

   ```PowerShell
   New-CACertsCertChain rsa
   ```

   Les sorties de l’exécution des scripts sont les certificats et les clés suivants :
   * Certificats
      * `$WRKDIR\certs\azure-iot-test-only.root.ca.cert.pem`
      * `$WRKDIR\certs\azure-iot-test-only.intermediate.cert.pem`
   * Clés
      * `$WRKDIR\private\azure-iot-test-only.root.ca.key.pem`
      * `$WRKDIR\private\azure-iot-test-only.intermediate.key.pem`

1.  À l’aide de la commande ci-dessous, créez la clé privée et le certificat d’autorité de certification d’appareil Edge.

   >[!NOTE]
   > **N’utilisez pas** un nom identique au nom d’hôte DNS de la passerelle. Cela provoquerait l’échec de la certification cliente sur ces certificats.

      ```PowerShell
      New-CACertsEdgeDevice "<gateway device name>"
      ```

   Les sorties de l’exécution des scripts sont les certificats et la clé suivants :
   * `$WRKDIR\certs\new-edge-device.*`
   * `$WRKDIR\private\new-edge-device.key.pem`

## <a name="certificate-chain-creation"></a>Création de la chaîne d’approbation
À l’aide de la commande ci-dessous, créez une chaîne d’approbation à partir du certificat d’autorité de certification propriétaire, du certificat intermédiaire et du certificat d’autorité de certification d’appareil Edge. Son placement dans un fichier de chaîne vous permet de l’installer facilement sur votre appareil Edge faisant office de passerelle transparente.

   ```PowerShell
   Write-CACertsCertificatesForEdgeDevice "<gateway device name>"
   ```

## <a name="installation-on-the-gateway"></a>Installation sur la passerelle
1.  Copiez les fichiers suivants à partir de $WRKDIR n’importe où sur votre appareil Edge ; nous appellerons cet emplacement $CERTDIR. Si vous avez généré les certificats sur votre appareil Edge, ignorez cette étape.

   * Certificat d’autorité de certification d’appareil : `$WRKDIR\certs\new-edge-device-full-chain.cert.pem`
   * Clé privée d’autorité de certification d’appareil : `$WRKDIR\private\new-edge-device.key.pem`
   * Autorité de certification propriétaire : `$WRKDIR\certs\azure-iot-test-only.root.ca.cert.pem`

2.  Définissez les propriétés `certificate` dans le fichier yaml de configuration du démon de sécurité sur le chemin où vous avez placé les fichiers de certificat et de clé.

```yaml
certificates:
  device_ca_cert: "$CERTDIR\certs\new-edge-device-full-chain.cert.pem"
  device_ca_pk: "$CERTDIR\private\new-edge-device.key.pem"
  trusted_ca_certs: "$CERTDIR\certs\azure-iot-test-only.root.ca.cert.pem"
```
## <a name="deploy-edgehub-to-the-gateway"></a>Déployer EdgeHub sur la passerelle
Une des fonctionnalités clés d’Azure IoT Edge est la capacité de déployer des modules sur vos appareils IoT Edge à partir du cloud. Dans cette section, vous créez un déploiement apparemment vide ; Toutefois Edge Hub est automatiquement ajouté à tous les déploiements, même si aucun autre module n’est présent. Edge Hub étant le seul module dont vous avez besoin sur un appareil Edge pour qu’il fasse office de passerelle transparente, la création d’un déploiement vide est suffisante. 
1. Accédez à votre hub IoT dans le portail Azure.
2. Accédez à **IoT Edge** et sélectionnez l’appareil IoT Edge à utiliser comme passerelle.
3. Sélectionnez **Définir modules**.
4. Sélectionnez **Suivant**.
5. À l’étape **Spécifier des routes**, vous devez avoir un itinéraire par défaut qui envoie tous les messages depuis tous les modules vers IoT Hub. Si ce n’est pas le cas, ajoutez le code suivant, puis sélectionnez **Suivant**.
   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```
6. À l’étape Vérifier le modèle, sélectionnez **Envoyer**.

## <a name="installation-on-the-downstream-device"></a>Installation sur l’appareil en aval
Un appareil en aval peut être n’importe quelle application utilisant [Azure IoT device SDK][lnk-devicesdk], telle que l’application simple décrite dans [Connecter votre appareil à votre IoT Hub à l’aide de .NET] [ lnk-iothub-getstarted]. Une application d’appareil en aval doit approuver le certificat **de l’autorité de certification propriétaire** afin de valider les connexions TLS aux appareils de la passerelle. Cette étape peut généralement être franchie de deux façons : au niveau du système d’exploitation, ou (pour certaines langues) au niveau de l’application.

### <a name="os-level"></a>Niveau du système d’exploitation
L’installation de ce certificat dans le magasin de certificats du système d’exploitation permet à toutes les applications d’utiliser le certificat d’autorité de certification propriétaire en tant que certificat approuvé.

* Ubuntu : voici un exemple d’installation d’un certificat d’autorité de certification sur un hôte Ubuntu.

   ```cmd
   sudo cp $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem  /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```
 
    Vous devez voir un message indiquant « Updating certificates in /etc/ssl/certs... 1 added, 0 removed; done. » (Mise à jour des certificats dans /etc/ssl/certs... 1 ajouté, 0 supprimé ; terminé.)

* Windows : [cet article](https://msdn.microsoft.com/en-us/library/cc750534.aspx) explique comment effectuer cette opération sur un appareil Windows à l’aide de l’Assistant Importation de certificat.

### <a name="application-level"></a>Niveau d’application
Pour les applications .NET, vous pouvez ajouter l’extrait de code suivant afin d’approuver un certificat au format PEM. Initialisez la variable `certPath` avec `$CERTDIR\certs\azure-iot-test-only.root.ca.cert.pem`.

   ```
   using System.Security.Cryptography.X509Certificates;

   ...

   X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
   store.Open(OpenFlags.ReadWrite);
   store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
   store.Close();
   ```

## <a name="connect-the-downstream-device-to-the-gateway"></a>Connecter l’appareil en aval à la passerelle
Vous devez initialiser le Kit IoT Hub device SDK avec une chaîne de connexion faisant référence au nom d’hôte de l’appareil de passerelle. Pour ce faire, il faut ajouter la propriété `GatewayHostName` à votre chaîne de connexion d’appareil. Voici un exemple de chaîne de connexion d’un périphérique à laquelle nous avons ajouté la propriété `GatewayHostName` :

   ```
   HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;GatewayHostName=mygateway.contoso.com
   ```

   >[!NOTE]
   >Voici un exemple de commande qui vérifie que tout a été configuré correctement. Vous devez voir un message indiquant « verified OK » (vérifié, OK).
   >
   >openssl s_client -connect mygateway.contoso.com:8883 -CAfile $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem -showcerts

## <a name="routing-messages-from-downstream-devices"></a>Routage des messages à partir des appareils en aval
Le runtime IoT Edge peut acheminer les messages envoyés à partir des appareils en aval comme les messages envoyés par les modules. Ainsi, vous pouvez effectuer une analytique dans un module s’exécutant sur la passerelle avant d’envoyer des données vers le cloud. L’itinéraire ci-dessous sert à envoyer des messages depuis un appareil en aval nommé `sensor` vers un module nommé `ai_insights`.

   ```json
   { "routes":{ "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" } }
   ```

Reportez-vous à [l’article de composition de module][lnk-module-composition] pour plus d’informations sur le routage des messages.

## <a name="next-steps"></a>Étapes suivantes
[Comprendre les exigences et outils de développement de modules IoT Edge][lnk-module-dev].

<!-- Images -->
[1]: ./media/how-to-create-transparent-gateway/gateway-setup.png

<!-- Links -->
[lnk-install-windows-x64]: ./how-to-install-iot-edge-windows-with-windows.md
[lnk-module-composition]: ./module-composition.md
[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-edge-as-gateway]: ./iot-edge-as-gateway.md
[lnk-module-dev]: module-development.md
[lnk-iothub-getstarted]: ../iot-hub/iot-hub-csharp-csharp-getstarted.md
[lnk-iothub-x509]: ../iot-hub/iot-hub-x509ca-overview.md
[lnk-iothub-secure-deployment]: ../iot-hub/iot-hub-security-deployment.md
[lnk-iothub-tokens]: ../iot-hub/iot-hub-devguide-security.md#security-tokens
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md
[lnk-iothub-devicetwins]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-c2d]: ../iot-hub/iot-hub-devguide-messages-c2d.md
[lnk-ca-scripts]: https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md
[lnk-modbus-module]: https://github.com/Azure/iot-edge-modbus
