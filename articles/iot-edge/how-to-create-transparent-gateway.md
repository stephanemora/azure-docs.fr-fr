---
title: Créer un appareil de passerelle transparent - Azure IoT Edge | Microsoft Docs
description: Utiliser un appareil Azure IoT Edge en tant que passerelle transparente pouvant traiter des informations d’appareils en aval
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c005dcd91412552e2b10c27a7809ca4bc46d4709
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792343"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Configurer un appareil IoT Edge en tant que passerelle transparente

Cet article donne des instructions détaillées pour configurer un appareil IoT Edge comme passerelle transparente permettant à d’autres appareils de communiquer avec IoT Hub. Dans cet article, le terme *passerelle IoT Edge* fait référence à un appareil IoT Edge utilisé comme passerelle transparente. Pour plus d’informations, consultez [Guide pratique pour utiliser un appareil IoT Edge en tant que passerelle](./iot-edge-as-gateway.md).

>[!NOTE]
>Actuellement :
> * Les appareils Edge ne peuvent pas se connecter aux passerelles IoT Edge. 
> * Les appareils en aval ne peuvent pas utiliser le chargement de fichier.

La configuration d’une connexion de passerelle transparente s’effectue en trois grandes étapes. Cet article décrit la première étape :

1. **L’appareil de passerelle doit être en mesure de se connecter aux appareils en aval, de recevoir des communications de ces appareils et de router les messages vers la destination appropriée, tout cela en toute sécurité.**
2. L’appareil en aval doit avoir une identité d’appareil pour s’authentifier auprès d’IoT Hub et savoir comment communiquer par le biais de l’appareil de passerelle associé. Pour plus d’informations, consultez [Authentifier un appareil en aval auprès d’Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. L’appareil en aval doit pouvoir se connecter en toute sécurité à son appareil de passerelle. Pour plus d’informations, voir [Connecter un appareil en aval à une passerelle Azure IoT Edge](how-to-connect-downstream-device.md).


Pour qu’un appareil fonctionne comme une passerelle, il doit être capable de se connecter de manière sécurisée aux appareils en aval associés. Azure IoT Edge vous permet d’utiliser une infrastructure à clé publique (PKI) pour configurer des connexions sécurisées entre les appareils. Dans ce cas, nous autorisons un appareil en aval à se connecter à un appareil IoT Edge faisant office de passerelle transparente. Pour assurer un niveau raisonnable de sécurité, l’appareil en aval doit confirmer l’identité de l’appareil de passerelle. Cette vérification d’identité empêche vos appareils de se connecter à des passerelles potentiellement malveillantes.

Un appareil en aval dans un scénario de passerelle transparente peut être n’importe quelle application ou plateforme dont l’identité a été créée avec le service cloud [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub). Dans de nombreux cas, ces applications utilisent [Azure IoT device SDK](../iot-hub/iot-hub-devguide-sdks.md). Pour des raisons pratiques, un appareil en aval peut même être une application fonctionnant sur l’appareil de passerelle IoT Edge proprement dit. Cependant, un appareil IoT Edge ne peut pas se trouver en aval d’une passerelle IoT Edge. 

Vous pouvez créer n’importe quelle infrastructure de certificat permettant l’approbation requise pour votre topologie de l’appareil à la passerelle. Dans cet article, nous nous basons sur la même configuration de certificat que vous utiliseriez pour activer la [sécurité d’autorité de certification X.509](../iot-hub/iot-hub-x509ca-overview.md) dans IoT Hub, ce qui implique d’avoir un certificat d’autorité de certification X.509 associé à un hub IoT spécifique (l’autorité de certification racine du hub IoT), une série de certificats signés par cette autorité de certification ainsi qu’une autorité de certification pour l’appareil IoT Edge.

![Configuration du certificat de la passerelle](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>Le terme « autorité de certification racine » utilisé dans cet article désigne le certificat public de l’autorité la plus haute dans la hiérarchie de certificats PKI, et pas obligatoirement le certificat racine d’une autorité de certification syndiquée. La plupart du temps, il s’agit en fait d’un certificat public d’une autorité de certification intermédiaire. 

La passerelle présente son certificat d’autorité de certification d’appareil IoT Edge à l’appareil en aval au lancement de la connexion. L’appareil en aval vérifie que le certificat d’autorité de certification d’appareil IoT Edge est signé par le certificat d’autorité de certification racine. Ce processus permet à l’appareil en aval de garantir que la passerelle provient d’une source approuvée.

Les étapes suivantes vous guident tout au long du processus de création des certificats et de leur installation aux bons emplacements sur la passerelle. Vous pouvez utiliser n’importe quel ordinateur pour générer les certificats, puis les copier sur votre appareil IoT Edge. 

## <a name="prerequisites"></a>Prérequis

* Une machine de développement pour créer des certificats. 
* Un appareil Azure IoT Edge à configurer comme passerelle. Effectuez les étapes d’installation IoT Edge pour l’un des systèmes d’exploitation suivants :
  * [Windows](how-to-install-iot-edge-windows.md)
  * [Linux](how-to-install-iot-edge-linux.md)

## <a name="generate-certificates-with-windows"></a>Générer des certificats avec Windows

Suivez les étapes de cette section pour générer des certificats de test sur Windows. Vous pouvez générer les certificats sur une machine Windows, puis les copier sur n’importe quel appareil IoT Edge s’exécutant sur un système d’exploitation pris en charge. 

Les certificats générés dans cette section ne doivent servir qu’à des fins de test. 

### <a name="install-openssl"></a>Installer OpenSSL

Installez OpenSSL pour Windows sur l’ordinateur que vous utilisez pour générer les certificats. Si vous avez déjà installé OpenSSL sur votre appareil Windows, vous pouvez ignorer cette étape. Vérifiez toutefois que le fichier openssl.exe est disponible dans la variable d’environnement PATH. 

Il existe plusieurs méthodes d’installation d’OpenSSL, à savoir :

* **Plus facile :** Téléchargez et installez les [binaires OpenSSL de fournisseurs tiers](https://wiki.openssl.org/index.php/Binaries), par exemple, à partir d’[OpenSSL sur SourceForge](https://sourceforge.net/projects/openssl/). Ajoutez le chemin d’accès complet à openssl.exe à votre variable d’environnement PATH. 
   
* **Recommandé :** Téléchargez le code source OpenSSL et générez les fichiers binaires sur votre machine vous-même ou en utilisant [vcpkg](https://github.com/Microsoft/vcpkg). Les instructions ci-dessous utilisent vcpkg pour télécharger le code source, compiler et installer OpenSSL sur votre machine Windows en suivant des étapes simples.

   1. Accédez au répertoire où vous souhaitez installer vcpkg. Nous appellerons ce répertoire *\<VCPKGDIR>* . Suivez les instructions pour télécharger et installer [vcpkg](https://github.com/Microsoft/vcpkg).
   
   2. Une fois que vcpkg est installé, exécutez la commande suivante à partir d’une invite PowerShell afin d’installer le package OpenSSL pour Windows x64. L’installation prend généralement 5 minutes environ.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. Ajoutez `<VCPKGDIR>\installed\x64-windows\tools\openssl` à votre variable d’environnement PATH afin que le fichier openssl.exe soit appelable.

### <a name="prepare-creation-scripts"></a>Préparer les scripts de création

Le dépôt Git Azure IoT Edge contient des scripts permettant de générer des certificats de test. Dans cette section, vous clonez le dépôt IoT Edge et exécutez les scripts. 

1. Ouvrez une fenêtre PowerShell en mode administrateur. 

2. Clonez le référentiel git contenant les scripts de génération de certificats qui ne sont pas en production. Ces scripts vous permettent de créer les certificats nécessaires pour configurer une passerelle transparente. Utilisez la commande `git clone` ou [téléchargez le fichier zip](https://github.com/Azure/iotedge/archive/master.zip). 

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Accédez au répertoire dans lequel vous souhaitez travailler. Ce répertoire de travail sera appelé *\<WRKDIR>* dans tout cet article. L’ensemble des certificats et des clés seront créés dans ce répertoire de travail.

4. Copiez les fichiers de configuration et de script du dépôt cloné dans votre répertoire de travail. 

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Si vous avez téléchargé le dépôt sous forme de fichier zip, le nom du dossier est `iotedge-master` et le reste du chemin est le même. 
<!--
5. Set environment variable OPENSSL_CONF to use the openssl_root_ca.cnf configuration file.

    ```powershell
    $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
    ```
-->
5. Activez PowerShell pour exécuter les scripts.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

7. Intégrez les fonctions utilisées par les scripts à l’espace de noms global de PowerShell.
   
   ```powershell
   . .\ca-certs.ps1
   ```

   La fenêtre PowerShell affiche un avertissement signalant que les certificats générés par ce script sont destinés aux tests uniquement et ne doivent pas être utilisés dans les scénarios de production.

8. Vérifiez qu’OpenSSL est correctement installé et qu’il n’y aura pas de collisions de noms avec des certificats existants. Si des problèmes se produisent, le script doit décrire comment les corriger sur votre système.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>Créer des certificats

Cette section vise à créer trois certificats, puis à les connecter dans une chaîne. Le fait de placer les certificats dans un fichier de chaîne permet de les installer facilement sur un appareil de passerelle IoT Edge et tous les appareils en aval.  

1. Créez le certificat d’autorité de certification racine et faites-le signer un certificat intermédiaire. Les certificats sont tous placés dans le répertoire de travail.

   ```powershell
   New-CACertsCertChain rsa
   ```

   Cette commande de script crée plusieurs fichiers de clés et de certificats, mais nous parlerons d’un de ces fichiers en particulier plus loin dans cet article :
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. Créez la clé privée et le certificat d’autorité de certification d’appareil IoT Edge avec la commande suivante. Donnez un nom au certificat d’autorité de certification, par exemple **MyEdgeDeviceCA**. Le nom est utilisé pour nommer les fichiers et pendant la génération des certificats. 

   ```powershell
   New-CACertsEdgeDeviceCA "MyEdgeDeviceCA"
   ```

   Cette commande de script crée plusieurs fichiers de clés et de certificats, dont ces deux fichiers que nous utiliserons plus loin dans cet article :
   * `<WRKDIR>\certs\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-ca-MyEdgeDeviceCA.key.pem`

   >[!TIP]
   >Si vous fournissez un nom autre que **MyEdgeDeviceCA**, les certificats et les clés créés par cette commande refléteront ce nom. 

Maintenant que vous avez créé les certificats, passez à l’étape suivante pour [installer les certificats sur la passerelle](#install-certificates-on-the-gateway)

## <a name="generate-certificates-with-linux"></a>Générer des certificats avec Linux

Suivez les étapes de cette section pour générer des certificats de test sur Linux. Vous pouvez générer les certificats sur une machine Linux, puis les copier sur n’importe quel appareil IoT Edge s’exécutant sur un système d’exploitation pris en charge. 

Les certificats générés dans cette section ne doivent servir qu’à des fins de test. 

### <a name="prepare-creation-scripts"></a>Préparer les scripts de création

Le dépôt Git Azure IoT Edge contient des scripts permettant de générer des certificats de test. Dans cette section, vous clonez le dépôt IoT Edge et exécutez les scripts. 

1. Clonez le référentiel git contenant les scripts de génération de certificats qui ne sont pas en production. Ces scripts vous permettent de créer les certificats nécessaires pour configurer une passerelle transparente. 

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Accédez au répertoire dans lequel vous souhaitez travailler. Ce répertoire de travail sera appelé *\<WRKDIR>* dans tout cet article. Tous les fichiers de clés et de certificats seront créés dans ce répertoire.
  
3. Copiez les fichiers de configuration et de script du dépôt IoT Edge cloné dans votre répertoire de travail.

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

<!--
4. Configure OpenSSL to generate certificates using the provided script. 

   ```bash
   chmod 700 certGen.sh 
   ```
-->

### <a name="create-certificates"></a>Créer des certificats

Cette section vise à créer trois certificats, puis à les connecter dans une chaîne. Le fait de placer les certificats dans un fichier de chaîne permet de les installer facilement sur un appareil de passerelle IoT Edge et tous les appareils en aval.  

1. Créez le certificat d’autorité de certification racine et un certificat intermédiaire. Ces certificats sont placés dans *\<WRKDIR>* .

   Si vous avez déjà créé des certificats racines et intermédiaires dans ce répertoire de travail, ne réexécutez pas ce script. La réexécution de ce script remplace les certificats existants. Au lieu de cela, passez à l’étape suivante. 

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Le script crée plusieurs fichiers de clés et de certificats. Nous utiliserons ce fichier en particulier dans la section suivante :
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`

2. Créez la clé privée et le certificat d’autorité de certification d’appareil IoT Edge avec la commande suivante. Donnez un nom au certificat d’autorité de certification, par exemple **MyEdgeDeviceCA**. Le nom est utilisé pour nommer les fichiers et pendant la génération des certificats. 

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "MyEdgeDeviceCA"
   ```

   Le script crée plusieurs fichiers de clés et de certificats. Nous utiliserons ces deux fichiers en particulier dans la section suivante : 
   * `<WRKDIR>/certs/iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-ca-MyEdgeDeviceCA.key.pem`

   >[!TIP]
   >Si vous fournissez un nom autre que **MyEdgeDeviceCA**, les certificats et les clés créés par cette commande refléteront ce nom. 

## <a name="install-certificates-on-the-gateway"></a>Installer des certificats sur la passerelle

Maintenant que vous avez créé une chaîne de certification, il vous reste à l’installer sur l’appareil de passerelle IoT Edge et à configurer le runtime IoT Edge de façon à ce qu’il référence les nouveaux certificats. 

1. Copiez les fichiers suivants à partir de *\<WRKDIR>* . Enregistrez-les à l’emplacement de votre choix sur votre appareil IoT Edge. Nous appellerons ce répertoire de destination *\<CERTDIR>* . 

   * Certificat d’autorité de certification d’appareil : `<WRKDIR>\certs\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * Clé privée d’autorité de certification d’appareil : `<WRKDIR>\private\iot-edge-device-ca-MyEdgeDeviceCA.key.pem`
   * Autorité de certification racine : `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

   Vous pouvez utiliser un service comme [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) ou une fonction comme [SCP (Secure Copy Protocol)](https://www.ssh.com/ssh/scp/) pour déplacer les fichiers de certificats.  Si vous avez généré les certificats directement sur l’appareil IoT Edge, vous pouvez ignorer cette étape et utiliser le chemin du répertoire de travail.

2. Ouvrez le fichier config de démon de sécurité IoT Edge. 

   * Windows : `C:\ProgramData\iotedge\config.yaml`
   * Linux : `/etc/iotedge/config.yaml`

3. Définissez les propriétés **certificates** dans le fichier config.yaml sur le chemin complet où vous avez placé les fichiers de certificats et de clés sur l’appareil IoT Edge. Supprimez le caractère `#` devant les propriétés certificates pour décommenter les quatre lignes. N’oubliez pas que les mises en retrait dans le fichier yaml sont de deux espaces.

   * Windows :

      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>\\certs\\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>\\private\\iot-edge-device-ca-MyEdgeDeviceCA.key.pem"
        trusted_ca_certs: "<CERTDIR>\\certs\\azure-iot-test-only.root.ca.cert.pem"
      ```
   
   * Linux : 
      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>/certs/iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>/private/iot-edge-device-ca-MyEdgeDeviceCA.key.pem"
        trusted_ca_certs: "<CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem"
      ```

4. Sur les appareils Linux, assurez-vous que l’utilisateur **iotedge** a les autorisations de lecture sur le répertoire contenant les certificats. 

## <a name="deploy-edgehub-to-the-gateway"></a>Déployer EdgeHub sur la passerelle

À la première installation d’IoT Edge sur un appareil, un seul module système démarre automatiquement : l’agent IoT Edge. Pour que votre appareil fonctionne comme une passerelle, il vous faut les deux modules système. Si vous n’avez jusqu’ici déployé aucun module sur votre appareil de passerelle, créez un déploiement initial pour que votre appareil démarre le deuxième module système, le hub IoT Edge. Le déploiement apparaîtra vide, car vous n’ajoutez aucun module dans l’Assistant, mais il garantit que les deux modules système sont démarrés. 

Pour savoir quels modules sont en cours d’exécution sur un appareil, utilisez la commande `iotedge list`. Si la liste retourne uniquement le module **edgeAgent** sans **edgeHub**, procédez comme suit :

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

## <a name="open-ports-on-gateway-device"></a>Ouvrir des ports sur l’appareil de passerelle

Les appareils IoT Edge standard peuvent fonctionner sans connexions entrantes, car toutes les communications avec le hub IoT s’effectuent par le biais de connexions sortantes. Les appareils de passerelle fonctionnent différemment, car ils doivent recevoir les messages de leurs appareils en aval. S’il y a un pare-feu entre les appareils en aval et l’appareil de passerelle, les communications doivent également pouvoir se faire par le pare-feu.

Dans un scénario de passerelle opérationnel, au moins un des protocoles pris en charge par le hub IoT Edge doit être ouvert pour recevoir le trafic entrant des appareils en aval. Les protocoles pris en charge sont les suivants : MQTT, AMQP, HTTPS, MQTT sur Websockets et AMQP sur WebSockets. 

| Port | Protocol |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT + WS <br> AMQP + WS | 

## <a name="route-messages-from-downstream-devices"></a>Acheminer les messages à partir des appareils en aval
Le runtime IoT Edge peut acheminer les messages envoyés à partir des appareils en aval comme les messages envoyés par les modules. Cette fonctionnalité vous permet d’effectuer une analytique dans un module s’exécutant sur la passerelle avant d’envoyer des données vers le cloud. 

Actuellement, la façon d’acheminer les messages envoyés par les appareils en aval consiste à les différencier des messages envoyés par les modules. Les messages envoyés par les modules contiennent tous une propriété système appelée **connectionModuleId** mais pas les messages envoyés par les appareils en aval. Vous pouvez utiliser la clause WHERE de l’itinéraire pour exclure tout message contenant cette propriété système. 

L’exemple de route ci-dessous envoie des messages d’un appareil en aval à un module nommé `ai_insights`, puis du module `ai_insights` à IoT Hub.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

Pour plus d’informations sur le routage des messages, consultez [Déployer des modules et établir des itinéraires](./module-composition.md#declare-routes).


## <a name="enable-extended-offline-operation"></a>Activer des opérations hors connexion étendues

Depuis la [version v1.0.4](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) du runtime IoT Edge, l’appareil de passerelle et les appareils en aval qui y sont connectés peuvent être configurés pour permettre les opérations hors connexion étendues. 

Avec cette fonctionnalité, les modules locaux et les appareils en aval peuvent se réauthentifier auprès de l’appareil IoT Edge et communiquer entre eux à l’aide de messages et de méthodes, même lorsqu’ils sont déconnectés du hub IoT. Pour plus d’informations, consultez [Introduction aux fonctionnalités hors connexion étendues pour les appareils, modules et appareils enfants IoT Edge](offline-capabilities.md).

Pour activer les fonctionnalités hors connexion étendues, établissez une relation parent-enfant entre un appareil de passerelle IoT Edge et les appareils en aval qui y seront connectés. Ces étapes sont expliquées plus en détail dans [Authentifier un appareil en aval auprès d’Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que votre appareil IoT Edge fonctionne comme une passerelle transparente, il vous faut configurer vos appareils en aval de façon à ce qu’ils approuvent la passerelle et lui envoient des messages. Pour poursuivre la configuration de votre scénario de passerelle transparente, suivez les étapes décrites dans [Authentifier un appareil en aval auprès d’Azure IoT Hub](how-to-authenticate-downstream-device.md). 
