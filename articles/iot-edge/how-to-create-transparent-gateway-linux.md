---
title: Créer une passerelle transparente avec Azure IoT Edge - Linux | Microsoft Docs
description: Utiliser Azure IoT Edge pour créer une passerelle transparente pouvant traiter des informations de plusieurs appareils
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 6/20/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 079a22ebaa7abfec7e8db142bc8f277ff12ab77e
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394967"
---
# <a name="create-a-linux-iot-edge-device-that-acts-as-a-transparent-gateway"></a>Créer un appareil IoT Edge Linux servant de passerelle transparente

Cet article fournit des instructions détaillées pour utiliser un appareil IoT Edge en tant que passerelle transparente. Dans le cadre de cet article, le terme *passerelle IoT Edge* fait référence à un appareil IoT Edge utilisé en tant que passerelle transparente. Pour plus d’informations, consultez [Guide pratique pour utiliser un appareil IoT Edge en tant que passerelle](./iot-edge-as-gateway.md), qui propose une vue d’ensemble conceptuelle.

>[!NOTE]
>Actuellement :
> * Si la passerelle est déconnectée d’IoT Hub, les appareils en aval ne peuvent pas s’authentifier auprès de la passerelle.
> * Les appareils Edge ne peuvent pas se connecter aux passerelles IoT Edge. 
> * Les appareils en aval ne peuvent pas utiliser le chargement de fichier.

La partie la plus complexe de la création d’une passerelle transparente consiste à connecter de manière sécurisée la passerelle aux appareils en aval. Azure IoT Edge vous permet d’utiliser l’infrastructure à clé publique pour configurer des connexions TLS sécurisées entre ces appareils. Dans ce cas, nous autorisons un appareil en aval à se connecter à un appareil IoT Edge faisant office de passerelle transparente.  Pour préserver une sécurité raisonnable, l’appareil en aval doit confirmer l’identité de l’appareil Edge, car vous souhaitez que vos appareils ne se connectent qu’à vos passerelles, et non pas à une passerelle potentiellement malveillante.

Vous pouvez créer n’importe quelle infrastructure de certificat permettant l’approbation requise pour votre topologie de l’appareil à la passerelle. Dans cet article, nous nous basons sur la même configuration de certificat que vous utiliseriez pour activer [la sécurité AC X.509](../iot-hub/iot-hub-x509ca-overview.md) dans IoT Hub, ce qui implique un certificat AC X.509 associé à un hub IoT spécifique (l’autorité de certification propriétaire du hub IoT), ainsi qu’une série de certificats, signés par cette autorité de certification, et une autorité de certification pour l’appareil Edge.

![Configuration de la passerelle](./media/how-to-create-transparent-gateway/gateway-setup.png)

La passerelle présente son certificat d’autorité de certification d’appareil Edge à l’appareil en aval au cours de l’ouverture de la connexion. L’appareil en aval vérifie que le certificat d’autorité de certification d’appareil Edge est signé par le certificat d’autorité de certification propriétaire. Ce processus permet à l’appareil en aval de vérifier que la passerelle provient d’une source approuvée.

Les étapes suivantes vous guident tout au long du processus de création des certificats et de leur installation au bon endroit.

## <a name="prerequisites"></a>Prérequis
1.  Installez le runtime Azure IoT Edge sur un appareil Linux que vous souhaitez utiliser comme passerelle transparente.
   * [Linux x64](./how-to-install-iot-edge-linux.md)
   * [Linux ARM32](./how-to-install-iot-edge-linux-arm.md)

2.  À l’aide de la commande suivante, récupérez les scripts servant à générer les certificats de non-production requis. Ces scripts vous permettent de créer les certificats nécessaires pour configurer une passerelle transparente. 

   ```cmd
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

3. Ces scripts utilisent OpenSSL pour générer les certificats requis, moyennant certains ajustements.
   
   1. Accédez au répertoire dans lequel vous souhaitez travailler. Nous appellerons cet emplacement $WRKDIR.  Tous les fichiers seront créés dans ce répertoire.

      cd $WRKDIR
   
   1. Copiez les fichiers de configuration et de script dans votre répertoire de travail.

      ```cmd
      cp azure-iot-sdk-c/tools/CACertificates/*.cnf .
      cp azure-iot-sdk-c/tools/CACertificates/certGen.sh .
      chmod 700 certGen.sh 
      ```

## <a name="certificate-creation"></a>Création du certificat
1.  Créez le certificat d’autorité de certification propriétaire et un certificat intermédiaire. Ces certificats sont placés dans `$WRKDIR`.

   ```cmd
   ./certGen.sh create_root_and_intermediate
   ```

   Les sorties de l’exécution des scripts sont les certificats et les clés suivants :
   * Certificats
      * `$WRKDIR/certs/azure-iot-test-only.root.ca.cert.pem`
      * `$WRKDIR/certs/azure-iot-test-only.intermediate.cert.pem`
   * Clés
      * `$WRKDIR/private/azure-iot-test-only.root.ca.key.pem`
      * `$WRKDIR/private/azure-iot-test-only.intermediate.key.pem`

2.  À l’aide de la commande ci-dessous, créez la clé privée et le certificat d’autorité de certification d’appareil Edge.

   >[!NOTE]
   > **N’utilisez pas** un nom identique au nom d’hôte DNS de la passerelle. Cela provoquerait l’échec de la certification cliente sur ces certificats.

   ```cmd
   ./certGen.sh create_edge_device_certificate "<gateway device name>"
   ```

   Les sorties de l’exécution des scripts sont les certificats et la clé suivants :
   * `$WRKDIR/certs/new-edge-device.*`
   * `$WRKDIR/private/new-edge-device.key.pem`

## <a name="certificate-chain-creation"></a>Création de la chaîne d’approbation
À l’aide de la commande ci-dessous, créez une chaîne d’approbation à partir du certificat d’autorité de certification propriétaire, du certificat intermédiaire et du certificat d’autorité de certification d’appareil Edge. Son placement dans un fichier de chaîne vous permet de l’installer facilement sur votre appareil Edge faisant office de passerelle transparente.

   ```cmd
   cat ./certs/new-edge-device.cert.pem ./certs/azure-iot-test-only.intermediate.cert.pem ./certs/azure-iot-test-only.root.ca.cert.pem > ./certs/new-edge-device-full-chain.cert.pem
   ```

## <a name="installation-on-the-gateway"></a>Installation sur la passerelle
1.  Copiez les fichiers suivants à partir de $WRKDIR n’importe où sur votre appareil Edge ; nous appellerons cet emplacement $CERTDIR. Si vous avez généré les certificats sur votre appareil Edge, ignorez cette étape.

   * Certificat d’autorité de certification d’appareil : `$WRKDIR/certs/new-edge-device-full-chain.cert.pem`
   * Clé privée d’autorité de certification d’appareil : `$WRKDIR/private/new-edge-device.key.pem`
   * Autorité de certification propriétaire : `$WRKDIR/certs/azure-iot-test-only.root.ca.cert.pem`
   
2. Ouvrez le fichier de configuration IoT Edge. Comme il s’agit d’un fichier protégé, vous devrez peut-être utiliser des privilèges élevés pour y accéder.
   
   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

3.  Définissez les propriétés `certificate` dans le fichier yaml de configuration du démon Iot Edge sur le chemin où vous avez placé les fichiers de certificat et de clé.

   ```yaml
   certificates:
     device_ca_cert: "$CERTDIR/certs/new-edge-device-full-chain.cert.pem"
     device_ca_pk: "$CERTDIR/private/new-edge-device.key.pem"
     trusted_ca_certs: "$CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem"
   ```

## <a name="deploy-edgehub-to-the-gateway"></a>Déployer EdgeHub sur la passerelle
Une des fonctionnalités clés d’Azure IoT Edge est la capacité de déployer des modules sur vos appareils IoT Edge à partir du cloud. Dans cette section, vous créez un déploiement apparemment vide ; toutefois, Edge Hub est automatiquement ajouté à tous les déploiements, même si aucun autre module n’est présent. Edge Hub étant le seul module dont vous avez besoin sur un appareil Edge pour qu’il fasse office de passerelle transparente, la création d’un déploiement vide est suffisante. 
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
Un appareil en aval peut être n’importe quelle application utilisant le kit [Azure IoT device SDK](../iot-hub/iot-hub-devguide-sdks.md), telle que l’application simple décrite dans [Connecter votre appareil à votre hub IoT à l’aide de .NET](../iot-hub/quickstart-send-telemetry-dotnet.md). Une application d’appareil en aval doit approuver le certificat **de l’autorité de certification propriétaire** afin de valider les connexions TLS aux appareils de la passerelle. Cette étape peut généralement être franchie de deux façons : au niveau du système d’exploitation, ou (pour certaines langues) au niveau de l’application.

### <a name="os-level"></a>Niveau du système d’exploitation
L’installation de ce certificat dans le magasin de certificats du système d’exploitation permet à toutes les applications d’utiliser le certificat d’autorité de certification propriétaire en tant que certificat approuvé.

* Ubuntu : voici un exemple d’installation d’un certificat d’autorité de certification sur un hôte Ubuntu.

   ```cmd
   sudo cp $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem  /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```
 
    Vous devez voir un message indiquant « Updating certificates in /etc/ssl/certs... 1 added, 0 removed; done. » (Mise à jour des certificats dans /etc/ssl/certs... 1 ajouté, 0 supprimé ; terminé.)

* Windows : voici un exemple d’installation d’un certificat d’autorité de certification sur un hôte Windows.
  1. Dans le menu Démarrer, tapez « Gérer les certificats d’ordinateur ». Cette action ouvre un utilitaire appelé `certlm`.
  2. Accédez à **Certificats - Ordinateur Local** > **Autorités de certification racines de confiance** > **Certificats** > Clic droit > **Toutes les tâches** > **Importer** pour lancer l’Assistant Importation du certificat.
  3. Suivez les étapes comme indiqué et importez le fichier de certificat $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem.
  4. Une fois terminé, vous devez voir un message « Importation réussie ».

### <a name="application-level"></a>Niveau d’application
Pour les applications .NET, vous pouvez ajouter l’extrait de code suivant afin d’approuver un certificat au format PEM. Initialisez la variable `certPath` avec `$CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem`.

   ```
   using System.Security.Cryptography.X509Certificates;

   ...

   X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
   store.Open(OpenFlags.ReadWrite);
   store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
   store.Close();
   ```

## <a name="connect-the-downstream-device-to-the-gateway"></a>Connecter l’appareil en aval à la passerelle
Initialisez le kit IoT Hub device SDK avec une chaîne de connexion faisant référence au nom d’hôte de l’appareil de passerelle. Pour ce faire, il faut ajouter la propriété `GatewayHostName` à votre chaîne de connexion d’appareil. Voici un exemple de chaîne de connexion d’un périphérique à laquelle nous avons ajouté la propriété `GatewayHostName` :

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

Reportez-vous à [l’article de composition de module](./module-composition.md) pour plus d’informations sur le routage des messages.

[!INCLUDE [iot-edge-offline-preview](../../includes/iot-edge-extended-offline-preview.md)]

## <a name="next-steps"></a>Étapes suivantes
[Comprendre les exigences et outils de développement de modules IoT Edge](module-development.md).
