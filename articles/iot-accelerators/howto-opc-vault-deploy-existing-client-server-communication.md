---
title: Sécuriser une application OPC UA cliente et OPC UA serveur avec OPC Vault - Azure | Microsoft Docs
description: Sécurisez une application OPC UA cliente et OPC UA serveur avec une nouvelle paire de clés et un certificat en utilisant OPC Vault.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 5ba2dba02585598b3797dd1b490976ebe34b489e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61450662"
---
# <a name="secure-opc-ua-client-and-opc-ua-server-application"></a>Sécuriser une application OPC UA cliente et OPC UA serveur 
OPC Vault est un microservice qui peut configurer, inscrire et gérer le cycle de vie des certificats pour les applications serveur et clientes OPC UA dans le cloud. Cet article vous montre comment sécuriser une application OPC UA cliente et OPC UA serveur avec une nouvelle paire de clés et un certificat en utilisant OPC Vault.

Dans la configuration suivante, le client OPC teste la connectivité au contrôleur PLC OPC. Par défaut, la connectivité n’est pas possible, car les deux composants n’ont pas encore été provisionnés avec les certificats appropriés. Dans ce workflow, nous n’utilisons pas les certificats auto-signés des composants OPC UA et nous les signons via OPC Vault. Consultez le [banc de test](howto-opc-vault-deploy-existing-client-plc-communication.md) précédent. Au lieu de cela, ce banc de test provisionne les composants avec un nouveau certificat et avec une nouvelle clé privée qui sont tous deux générés par OPC Vault. Vous trouverez des informations générales sur la sécurité d’OPC UA dans ce [livre blanc](https://opcfoundation.org/wp-content/uploads/2014/05/OPC-UA_Security_Model_for_Administrators_V1.00.pdf). Vous trouverez les informations complètes dans la spécification OPC UA.

Banc de test : L’environnement suivant est configuré pour le test.

Scripts OPC Vault :
- Sécurisez des applications OPC UA clientes et OPC UA serveur avec une nouvelle paire de clés et un certificat en utilisant OPC Vault.

> [!NOTE]
> Pour plus d’informations, consultez le [dépôt](https://github.com/Azure-Samples/iot-edge-industrial-configs#testbeds) GitHub.

## <a name="generate-a-new-certificate-and-private-key"></a>Générer un nouveau certificat et une clé privée 
**Préparation**
- Vérifiez que les variables d’environnement `$env:_PLC_OPT` et `$env:_CLIENT_OPT` ne sont pas définies. Par exemple, `$env:_PLC_OPT=""` dans votre PowerShell
- Définissez la variable d’environnement `$env:_OPCVAULTID` sur une chaîne qui vous permet de retrouver vos données dans OPC Vault. Nous vous recommandons de la définir sur un nombre à 6 chiffres. Dans notre exemple, « 123456 » a été utilisé comme valeur pour la variable.
- Vérifiez qu’il n’existe pas de volume docker `opcclient` ou `opcplc`. Vérifiez avec `docker volume ls` et supprimez-les avec `docker volume rm <volumename>`. Il peut être nécessaire de supprimer également les conteneurs avec `docker rm <containerid>` si les volumes sont toujours utilisés par un conteneur.

**Démarrage rapide**
1. Accédez au [site web OPC Vault](https://opcvault.azurewebsites.net/)

1. Sélectionnez `Register New`.

1. Entrez les informations du contrôleur PLC OPC, telles qu’elles apparaissaient dans la zone `CreateSigningRequest information` de la sortie du journal du banc de test précédent, dans les champs d’entrée de la page `Register New OPC UA Application`, puis sélectionnez `Server` pour ApplicationType.

1. Sélectionnez `Register`.

1. Dans la page suivante, `Request New Certificate for OPC UA Application`, sélectionnez `Request new KeyPair and Certificate`

1. Dans la page suivante, `Generate a new Certificate with a Signing Request`, collez la chaîne `CSR (base64 encoded)` depuis la sortie du journal dans le champ d’entrée `CreateRequest`. Veillez à copier la chaîne complète.

1. Dans la page suivante, `Request New Certificate for OPC UA Application`, sélectionnez `Request new Certificate with Signing Request`

1. Dans la page suivante, `Generate a new KeyPair and for an OPC UA Application`, entrez `CN=OpcPlc` pour SubjectName, `opcplc-<_OPCVAULTID>` (remplacez `<_OPCVAULTID>` par le vôtre) pour DomainName, sélectionnez `PEM` pour PrivateKeyFormat et entrez un mot de passe (nous le désignons plus loin par `<certpassword-string>`)

1. Sélectionnez `Generate New KeyPair`.

1. Vous passez maintenant à `View Certificate Request Details`. Dans cette page, vous pouvez télécharger toutes les informations nécessaires pour provisionner les magasins de certificats de `opc-plc`.

1. Dans cette page :  
    - Sélectionnez `Certificate` dans `Download as Base64`, puis copiez la chaîne de texte présente dans le champ `EncodedBase64` et stockez-la pour une utilisation ultérieure. Nous la désignons plus loin par `<applicationcertbase64-string>`. Sélectionnez `Back`.
    - Sélectionnez `PrivateKey` dans `Download as Base64`, puis copiez la chaîne de texte présente dans le champ `EncodedBase64` et stockez-la pour une utilisation ultérieure. Nous la désignons plus loin par `<privatekeybase64-string>`. Sélectionnez `Back`.
    - Sélectionnez `Issuer` dans `Download as Base64`, puis copiez la chaîne de texte présente dans le champ `EncodedBase64` et stockez-la pour une utilisation ultérieure. Nous la désignons plus loin par `<addissuercertbase64-string>`. Sélectionnez `Back`.
    - Sélectionnez `Crl` dans `Download as Base64`, puis copiez la chaîne de texte présente dans le champ `EncodedBase64` et stockez-la pour une utilisation ultérieure. Nous la désignons plus loin par `<updatecrlbase64-string>`. Sélectionnez `Back`.

1. Définissez maintenant dans votre PowerShell une variable nommée `$env:_PLC_OPT` :

   `$env:_PLC_OPT="--applicationcertbase64 <applicationcertbase64-string> --privatekeybase64 <privatekeybase64-string> --certpassword <certpassword-string> --addtrustedcertbase64 <addissuercertbase64-string> --addissuercertbase64 <addissuercertbase64-string> --updatecrlbase64 <updatecrlbase64-string>"`  

    Remplacez les chaînes passées en tant que chaînes Base64 pour les valeurs d’option que vous extraites depuis le site web.  

1. Répétez le processus complet en commençant par `Register New` pour le client OPC. Notez seulement les différences suivantes :
    - Utilisez la sortie du journal provenant du `opcclient`.
    - Sélectionnez `Client` pour ApplicationType lors de l’inscription.
    - Utilisez `$env:_CLIENT_OPT` comme nom de la variable PowerShell.

    > [!NOTE] 
    > Dans ce scénario, vous avez peut-être noté que les valeurs de `<addissuercertbase64-string>` et de `<updatecrlbase64-string>` sont identiques pour `opcplc` et `opcclient`. Ceci est vrai pour notre cas d’utilisation et peut vous faire gagner du temps quand vous effectuez les étapes.

**Démarrage rapide**

Exécutez les commandes PowerShell suivantes dans la racine du dépôt :

```
docker-compose -f connecttest.yml up
```

**Vérification**

Vérifiez que les deux composants n’ont pas eu un certificat d’application existant. Consultez la sortie du journal. Voici la sortie du contrôleur PLC OPC ; le client OPC a une sortie de journal similaire.

```
opcplc-123456 | [13:40:08 INF] There is no existing application certificate.
```
S’il existe un certificat d’application, supprimez les volumes docker comme expliqué dans les étapes de préparation.

Dans le journal, vérifiez que le certificat d’autorité de certification d’OPC Vault a été installé dans le magasin de certificats de l’émetteur ainsi que dans le magasin de certificats des pairs approuvés. Voici la sortie du journal du contrôleur PLC OPC ; le client OPC a une sortie de journal similaire. 

```
opcplc-123456 | [13:40:09 INF] Trusted issuer store contains 1 certs
opcplc-123456 | [13:40:09 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [13:40:09 INF] Trusted issuer store has 1 CRLs.
opcplc-123456 | [13:40:09 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [13:40:09 INF] Trusted peer store contains 1 certs
opcplc-123456 | [13:40:09 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [13:40:09 INF] Trusted peer store has 1 CRLs.
opcplc-123456 | [13:40:09 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [13:40:09 INF] Rejected certificate store contains 0 certs
```
Le contrôleur PLC OPC approuve désormais tous les clients OPC UA avec des certificats signés par OPC Vault.

Vérifiez dans le journal que le format de clé privée est reconnu comme PEM et que le nouveau certificat d’application est installé. Voici la sortie du journal du contrôleur PLC OPC ; le client OPC a une sortie de journal similaire. 

```
opcplc-123456 | [13:40:09 INF] The private key for the new certificate was passed in using PEM format.
opcplc-123456 | [13:40:09 INF] Remove the existing application certificate.
opcplc-123456 | [13:40:09 INF] The new application certificate 'CN=OpcPlc' and thumbprint 'A3CB288FC1D2B7A5C08AACF531CF4A85E44A6C4C' was added to the application certificate store.
opcplc-123456 | [13:40:09 INF] Activating the new application certificate with thumbprint 'A3CB288FC1D2B7A5C08AACF531CF4A85E44A6C4C'.
```

Le certificat d’application et la clé privée sont maintenant installés dans le magasin de certificats d’application et utilisés par l’application OPC UA.

Vérifiez que la connexion entre le client OPC et le contrôleur PLC OPC peut être établie avec succès, et que le client OPC peut lire correctement les données provenant du contrôleur PLC OPC. Vous devez ceci dans la sortie du journal du client OPC :
```
opcclient-123456 | [13:40:12 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [13:40:12 INF] Session successfully created with Id ns=3;i=941910499.
opcclient-123456 | [13:40:12 INF] The session to endpoint 'opc.tcp://opcplc-123456:50000/' has 4 entries in its namespace array:
opcclient-123456 | [13:40:12 INF] Namespace index 0: http://opcfoundation.org/UA/
opcclient-123456 | [13:40:12 INF] Namespace index 1: urn:OpcPlc:opcplc-123456
opcclient-123456 | [13:40:12 INF] Namespace index 2: http://microsoft.com/Opc/OpcPlc/
opcclient-123456 | [13:40:12 INF] Namespace index 3: http://opcfoundation.org/UA/Diagnostics
opcclient-123456 | [13:40:12 INF] The server on endpoint 'opc.tcp://opcplc-123456:50000/' supports a minimal sampling interval of 0 ms.
opcclient-123456 | [13:40:12 INF] Execute 'OpcClient.OpcTestAction' action on node 'i=2258' on endpoint 'opc.tcp://opcplc-123456:50000/' with security.
opcclient-123456 | [13:40:12 INF] Action (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258') completed successfully
opcclient-123456 | [13:40:12 INF] Value (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258'): 10/21/2018 13:40:12
```
Si vous voyez cette sortie, c’est que le contrôleur PLC OPC fait désormais confiance au client OPC et inversement, car ils ont désormais tous deux des certificats signés par une autorité de certification et les deux certificats de confiance qui ont été signés par cette autorité de certification.

### <a name="a-testbed-for-opc-publisher"></a>Un banc de test pour le serveur de publication OPC ###

**Démarrage rapide**

Exécutez les commandes PowerShell suivantes dans la racine du dépôt :
```
docker-compose -f testbed.yml up
```

**Vérification**
- Vérifiez que les données sont envoyées au hub IoT que vous avez configuré en définissant `_HUB_CS` avec l’[Explorateur d’appareils](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) ou [iothub-explorer](https://github.com/Azure/iothub-explorer).
- Le client de test OPC va utiliser des appels de méthode directe IoT Hub et des appels de méthode OPC pour configurer le serveur de publication OPC pour publier/annuler la publication de nœuds à partir du serveur de test OPC.
- Regardez s’il y a des messages d’erreur dans la sortie.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert comment déployer OPC Vault sur un projet existant, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Déployer OPC Twin sur un projet existant](howto-opc-twin-deploy-existing.md)