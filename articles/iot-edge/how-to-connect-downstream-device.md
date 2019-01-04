---
title: Connecter des appareils en aval - Azure IoT Edge | Microsoft Docs
description: Guide pratique pour configurer des appareils en aval ou de nœud terminal afin de les connecter via des appareils de passerelle Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/01/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 0797952641efb9fe876d72f2dce0418ff5771d40
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53083332"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>Connecter un appareil en aval à une passerelle Azure IoT Edge

Azure IoT Edge permet des scénarios de passerelle transparente, dans lesquels un ou plusieurs appareils peuvent transmettre leurs messages à partir d’un appareil de passerelle unique qui gère la connexion vers IoT Hub. Après avoir configuré l’appareil de passerelle, vous devez savoir comment connecter en toute sécurité les appareils en aval. 

Cet article identifie les problèmes courants liés aux connexions d’appareils en aval et vous guide dans la configuration de vos appareils en aval de la façon suivante : 

* Présente le protocole TLS (Transport Layer Security) et les principes de base des certificats. 
* Présente le fonctionnement des bibliothèques TLS sur différents systèmes d’exploitation et comment chaque système d’exploitation gère les certificats.
* Présente différents exemples Azure IoT dans plusieurs langues pour vous aider à démarrer. 

Dans cet article, les termes *passerelle* et *passerelle IoT Edge* font référence à un appareil IoT Edge configuré comme passerelle transparente. 

## <a name="prerequisites"></a>Prérequis

Avant de suivre les étapes décrites dans cet article, vous devez avoir deux appareils opérationnels :

1. Un appareil IoT Edge configuré comme passerelle transparente. 
    [Configurer un appareil IoT Edge en tant que passerelle transparente](how-to-create-transparent-gateway.md)

    Après voir configuré votre appareil de passerelle, copiez le certificat **azure-iot-test-only.root.ca.cert.pem** à partir de la passerelle et mettez-le à disposition n’importe où sur votre appareil en aval. 

2. Un appareil en aval qui a reçu une identité d’appareil de la part d’IoT Hub. 
    Vous ne pouvez pas utiliser un appareil IoT Edge comme appareil en aval. Utilisez plutôt un appareil inscrit comme appareil IoT standard dans IoT Hub. Dans le portail, vous pouvez inscrire un nouvel appareil dans la section **Appareils IoT**. Ou vous pouvez utiliser l’interface de ligne de commande Azure pour [inscrire un appareil](../iot-hub/quickstart-send-telemetry-c.md#register-a-device). Copiez la chaîne de connexion et mettez-la à disposition pour les sections suivantes. 

    Actuellement, seuls les appareils en aval avec authentification par clé symétrique peuvent se connecter via des passerelles IoT Edge. Les autorités de certification X.509 et les certificats auto-signés X.509 ne sont pas pris en charge actuellement. 

## <a name="prepare-a-downstream-device"></a>Préparer un appareil en aval

Un appareil en aval est une application ou une plateforme dont l’identité a été créée avec le service cloud [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub). Dans de nombreux cas, ces applications utilisent [Azure IoT device SDK](../iot-hub/iot-hub-devguide-sdks.md). Pour des raisons pratiques, un appareil en aval peut même être une application fonctionnant sur l’appareil de passerelle IoT Edge proprement dit. 

Pour connecter un appareil en aval à une passerelle Azure IoT Edge, deux éléments sont nécessaires :

1. Un appareil ou une application configurés avec une chaîne de connexion d’appareil IoT Hub ajoutée avec les informations pour se connecter à la passerelle. 

    La chaîne de connexion est formatée ainsi : `HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;`. Ajoutez la propriété **GatewayHostName** avec le nom d’hôte de l’appareil de passerelle à la fin de la chaîne de connexion. La valeur de **GatewayHostName** doit correspondre à la valeur de **hostname** dans le fichier config.yaml de l’appareil de passerelle. 

    La chaîne finale ressemble à la suivante : `HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;GatewayHostName=mygateway.contoso.com`.

2. L’appareil ou l’application doit approuver le certificat de l’**autorité de certification racine** ou de l’**autorité de certification propriétaire** de la passerelle afin de valider les connexions TLS à l’appareil de passerelle. 

    Cette étape plus complexe est expliquée en détail dans la suite de cet article. Cette étape peut être effectuée de deux manières : en installant le certificat de l’autorité de certification dans le magasin de certificats du système d’exploitation, ou (pour certaines langues) en référençant le certificat au sein des applications à l’aide de kits de développement logiciel Azure IoT.

## <a name="tls-and-certificate-fundamentals"></a>Principes de base du protocole TLS et des certificats

Le défi qu’impose la connexion en toute sécurité d’appareils en aval à IoT Edge est le même que pour toute autre communication client/serveur sécurisé survenant sur Internet. Un client et un serveur de communiquent en toute sécurité sur Internet à l’aide du protocole [Transport layer security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). Le protocole TLS est généré à l’aide de constructions [Public key infrastructure (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) standard appelées certificats. TLS est une spécification qui couvre une grande variété de sujets liés à la sécurisation de deux points de terminaison, mais la section suivante décrit de manière précise les conditions nécessaires pour connecter en toute sécurité des appareils à une passerelle IoT Edge.

Lorsqu’un client se connecte à un serveur, le serveur présente une chaîne de certificats, appelée *chaîne de certificats du serveur*. Une chaîne de certificats comprend généralement un certificat d’autorité de certification racine, un ou plusieurs certificats d’autorité de certification intermédiaires, et enfin le certificat du serveur lui-même. Un client établit une relation de confiance avec un serveur en vérifiant par chiffrement l’intégralité de la chaîne de certificats du serveur. Cette validation côté client de la chaîne de certificat du serveur est appelée *authentification du serveur*. Pour valider une chaîne de certificats du serveur, un client a besoin d’une copie du certificat de l’autorité de certification racine qui a servi à créer (ou à émettre) le certificat du serveur. Normalement, lors de la connexion à des sites web, un navigateur est préconfiguré avec des certificats d’autorité de certification couramment utilisés afin de garantir au client un processus transparent. 

Lorsqu’un appareil se connecte à Azure IoT Hub, l’appareil est client et le service cloud IoT Hub est le serveur. Le service cloud IoT Hub s’appuie sur un certificat d’autorité de certification racine appelé **Baltimore CyberTrust Root**, disponible publiquement et largement utilisé. Comme le certificat d’autorité de certification IoT Hub est déjà installé sur la plupart des appareils, de nombreuses implémentations TLS (OpenSSL, Schannel, LibreSSL) l’utilisent automatiquement lors de la validation du certificat du serveur. Un appareil qui se connecte avec succès à IoT Hub peut rencontrer des problèmes en tentant de se connecter à une passerelle IoT Edge.

Lorsqu’un appareil se connecte à une passerelle IoT Edge, l’appareil en aval est le client et l’appareil de passerelle est le serveur. Azure IoT Edge permet aux opérateurs (ou utilisateurs) de générer des chaînes de certificats de passerelle adaptées à leurs besoins. L’opérateur peut choisir d’utiliser un certificat d’autorité de certification publique, comme Baltimore, ou un certificat d’autorité de certification racine auto-signé (ou développé en interne). Les certificats d’autorité de certification publique entraînent souvent un coût et sont par conséquent généralement utilisés dans des scénarios de production. Les certificats d’autorité de certification auto-signés sont plus adaptés au développement et aux tests. Les articles présentant la configuration d’une passerelle transparente répertoriés dans la section sur les conditions préalables utilisent des certificats d’autorité de certification racine auto-signés. 

Lorsque vous utilisez un certificat d’autorité de certification racine auto-signé pour une passerelle IoT Edge, ce certificat doit être installé sur tous les appareils en aval qui tentent de se connecter à la passerelle. 

Pour en savoir plus sur les certificats IoT Edge et certaines implications en matière de production, consultez la section [Détails sur l’utilisation d’un certificat IoT Edge](iot-edge-certs.md).

## <a name="install-certificates-using-the-os"></a>Installer des certificats à l’aide du système d’exploitation

Cet article utilise le terme *autorité de certification propriétaire* pour faire référence au certificat d’autorité de certification racine, puisque c’est le terme utilisé par les scripts dans l’article sur la passerelle. 

L’installation du certificat d’autorité de certification propriétaire dans le magasin de certificats du système d’exploitation permet généralement à la plupart des applications d’utiliser le certificat d’autorité de certification propriétaire. Il existe quelques exceptions, notamment l’application NodeJS, qui n’utilisent pas le magasin de certificats du système d’exploitation, mais plutôt le magasin de certificats interne du runtime Node. Si vous ne pouvez pas installer le certificat au niveau du système d’exploitation, reportez-vous aux exemples spécifiques à une langue plus loin dans cet article pour utiliser le certificat avec le SDK Azure IoT dans des applications. 

### <a name="ubuntu"></a>Ubuntu

Les commandes suivantes montrent un exemple d’installation d’un certificat d’autorité de certification sur un hôte Ubuntu. Cet exemple suppose que vous utilisez le certificat **azure-iot-test-only.root.ca.cert.pem** à partir des articles de la configuration requise et que vous avez copié le certificat dans un emplacement sur l’appareil en aval.  

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

Vous devez voir un message indiquant « Updating certificates in /etc/ssl/certs... 1 added, 0 removed; done. » (Mise à jour des certificats dans /etc/ssl/certs... 1 ajouté, 0 supprimé ; terminé.)

### <a name="windows"></a> Windows

Les étapes suivantes montrent un exemple d’installation d’un certificat d’autorité de certification sur un hôte Windows. Cet exemple suppose que vous utilisez le certificat **azure-iot-test-only.root.ca.cert.pem** à partir des articles de la configuration requise et que vous avez copié le certificat dans un emplacement sur l’appareil en aval.  

1. Dans le menu Démarrer, recherchez et sélectionnez **Manage computer certificates** (Gérer les certificats d’ordinateur). Un utilitaire appelé **certlm** s’ouvre.
2. Accédez à **Certificats - Ordinateur local** > **Autorités de certification racines de confiance**.
3. Cliquez avec le bouton droit sur **Certificats**, puis sélectionnez **Toutes les tâches** > **Importer**. L’Assistant Importation de certificat devrait s’ouvrir. 
4. Suivez les étapes indiquées et importez le fichier de certificat `<path>/azure-iot-test-only.root.ca.cert.pem`. Une fois terminé, vous devez voir un message « Importation réussie ». 

Vous pouvez également installer des certificats par programmation à l’aide de l’API .NET, comme indiqué dans l’exemple .NET plus loin dans cet article. 

En général, les applications utilisent la pile TLS fournie par Windows et appelée [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel) pour se connecter en toute sécurité via TLS. Schannel *requiert* l’installation de tous les certificats dans le magasin de certificats Windows avant de tenter d’établir une connexion TLS.

## <a name="use-certificates-with-azure-iot-sdks"></a>Utiliser des certificats avec les kits de développement logiciel Azure IoT

Cet article fait référence au certificat d’autorité de certification racine en tant qu’*autorité de certification propriétaire* puisque c’est le terme utilisé par les scripts qui génèrent le certificat auto-signé dans les articles sur la configuration requise. 

Cette section décrit comment les kits Azure IoT SDK se connectent à un appareil IoT Edge à l’aide de simples exemples d’applications. Tous les exemples visent à se connecter au client de l’appareil et à envoyer des messages de télémétrie à la passerelle, avant de fermer la connexion et de quitter le programme. 

### <a name="common-concepts-across-all-azure-iot-sdks"></a>Concepts communs à tous les kits de développement logiciel IoT Azure

Avant d’utiliser des exemples au niveau de l’application, préparez les deux éléments suivants :

1. La chaîne de connexion IoT Hub de votre appareil en aval, modifiée pour pointer vers l’appareil de passerelle.

    La chaîne de connexion est formatée ainsi : `HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;`. Ajoutez la propriété **GatewayHostName** avec le nom d’hôte de l’appareil de passerelle à la fin de la chaîne de connexion. La valeur de **GatewayHostName** doit correspondre à la valeur de **hostname** dans le fichier config.yaml de l’appareil de passerelle. 

    La chaîne finale ressemble à la suivante : `HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;GatewayHostName=mygateway.contoso.com`.

2. Le chemin complet vers le certificat d’autorité de certification racine que vous avez copié et enregistré quelque part sur votre appareil en aval.

    Par exemple : `<path>/azure-iot-test-only.root.ca.cert.pem`. 

### <a name="nodejs"></a>NodeJS

Cette section fournit un exemple d’application pour se connecter d’un client d’appareil Azure IoT NodeJS à une passerelle IoT Edge. Pour les hôtes Linux et Windows, vous devez installer le certificat d’autorité de certification racine au niveau de l’application comme indiqué ici, car les applications NodeJS n’utilisent pas le magasin de certificats du système. 

1. Récupérez l’exemple pour **edge_downstream_device.js** à partir du [référentiel d’exemples Azure IoT device SDK pour Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples). 
2. Assurez-vous que vous remplissez toutes les conditions préalables pour exécuter l’exemple en passant en revue le fichier **readme.md**. 
3. Dans le fichier edge_downstream_device.js, mettez à jour les variables **connectionString** et **edge_ca_cert_path**. 
4. Consultez la documentation du Kit de développement logiciel pour obtenir des instructions sur la façon d’exécuter l’exemple sur votre appareil. 

Pour comprendre l’exemple que vous exécutez, l’extrait de code suivant explique comment le SDK client lit le fichier de certificat et l’utilise pour établir une connexion TLS sécurisée : 

```nodejs
// Provide the Azure IoT device client via setOptions with the X509
// Edge root CA certificate that was used to setup the Edge runtime
var options = {
    ca : fs.readFileSync(edge_ca_cert_path, 'utf-8'),
};
```

### <a name="net"></a>.NET

Cette section présente un exemple d’application permettant de connecter un client d’appareil Azure IoT .NET à une passerelle IoT Edge. Toutefois, les applications .NET peuvent automatiquement utiliser les certificats installés dans le magasin de certificats du système sur des hôtes Linux et Windows.

1. Récupérez l’exemple pour **EdgeDownstreamDevice** à partir du [dossier d’exemples IoT Edge .NET](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice). 
2. Assurez-vous que vous remplissez toutes les conditions préalables pour exécuter l’exemple en passant en revue le fichier **readme.md**. 
3. Dans le fichier **Properties / launchSettings.json**, mettez à jour les variables **DEVICE_CONNECTION_STRING** et **CA_CERTIFICATE_PATH**. Si vous souhaitez utiliser le certificat installé dans le magasin de certificats de confiance sur le système hôte, laissez vide cette variable. 
4. Consultez la documentation du Kit de développement logiciel pour obtenir des instructions sur la façon d’exécuter l’exemple sur votre appareil. 

Pour installer par programmation un certificat approuvé dans le magasin de certificats via une application .NET, reportez-vous à la fonction **InstallCACert()** dans le fichier **EdgeDownstreamDevice / Program.cs**. Cette opération est idempotente et peut donc être exécutée plusieurs fois avec les mêmes valeurs sans aucune autre incidence. 

### <a name="c"></a>C

Cette section présente un exemple d’application permettant de connecter un client d’appareil Azure IoT C à une passerelle IoT Edge. Le Kit de développement logiciel C peut fonctionner avec de nombreuses bibliothèques TLS, y compris OpenSSL et Schannel WolfSSL. Pour plus d’informations, consultez le [Kit de développement logiciel (SDK) C Azure IoT](https://github.com/Azure/azure-iot-sdk-c). 

1. Récupérez l’application **iotedge_downstream_device_sample** à partir des [exemples Azure IoT device SDK pour C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples). 
2. Assurez-vous que vous remplissez toutes les conditions préalables pour exécuter l’exemple en passant en revue le fichier **readme.md**. 
3. Dans le fichier iotedge_downstream_device_sample.c, mettez à jour les variablest**connectionString** et **edge_ca_cert_path**. 
4. Consultez la documentation du Kit de développement logiciel pour obtenir des instructions sur la façon d’exécuter l’exemple sur votre appareil. 

Azure IoT device SDK pour C fournit une option permettant d’inscrire un certificat d’autorité de certification lors de la configuration du client. Cette opération n’installe pas le certificat n’importe où, mais utilise plutôt un format de chaîne du certificat en mémoire. Le certificat enregistré est transmis à la pile TLS sous-jacente lors de l’établissement d’une connexion. 

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

Sur les hôtes Windows, si vous n’utilisez pas OpenSSL ou une autre bibliothèque TLS, le Kit de développement utilise par défaut Schannel. Pour que Schannel fonctionne, le certificat d’autorité de certification racine IoT Edge doit être installé dans le magasin de certificats Windows, qui n’est défini à l’aide de l’opération `IoTHubDeviceClient_SetOption`. 

### <a name="java"></a>Java

Cette section présente un exemple d’application permettant de connecter un client d’appareil Azure IoT Java à une passerelle IoT Edge. 

1. Récupérez l’exemple pour **Send-event** à partir des [exemples Azure IoT device SDK pour Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples). 
2. Assurez-vous que vous remplissez toutes les conditions préalables pour exécuter l’exemple en passant en revue le fichier **readme.md**. 
3. Consultez la documentation du Kit de développement logiciel pour obtenir des instructions sur la façon d’exécuter l’exemple sur votre appareil.

### <a name="python"></a>Python

Cette section présente un exemple d’application permettant de connecter un client d’appareil Azure IoT Python à une passerelle IoT Edge. 

1. Récupérez l’exemple pour **edge_downstream_client** à partir des [exemples Azure IoT device SDK pour Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples). 
2. Assurez-vous que vous remplissez toutes les conditions préalables pour exécuter l’exemple en passant en revue le fichier **readme.md**. 
3. Dans le fichier edge_downstream_client.py, mettez à jour les variables **CONNECTION_STRING** et **TRUSTED_ROOT_CA_CERTIFICATE_PATH**. 
4. Consultez la documentation du Kit de développement logiciel pour obtenir des instructions sur la façon d’exécuter l’exemple sur votre appareil. 


## <a name="test-the-gateway-connection"></a>Test de connexion à la passerelle

Voici un exemple de commande qui vérifie que tout a été configuré correctement. Vous devez voir un message indiquant « verified OK » (vérifié, OK).

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

## <a name="next-steps"></a>Étapes suivantes

Guide pratique pour étendre les [fonctionnalités hors connexion](offline-capabilities.md) IoT Edge aux appareils en aval. 