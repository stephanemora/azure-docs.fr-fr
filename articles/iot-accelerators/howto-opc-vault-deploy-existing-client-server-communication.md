---
title: Sécuriser OPC UA client et l’application de serveur OPC UA à l’aide d’OPC coffre - Azure | Microsoft Docs
description: Client OPC UA et OPC UA server application sécurisé avec une nouvelle paire de clés et le certificat à l’aide d’OPC coffre.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 5ba2dba02585598b3797dd1b490976ebe34b489e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59495292"
---
# <a name="secure-opc-ua-client-and-opc-ua-server-application"></a>Sécuriser OPC UA client et l’application de serveur OPC UA 
OPC Vault est un microservice qui peut configurer, inscrire et gérer le cycle de vie de certificat pour le serveur UA OPC et les applications clientes dans le cloud. Cet article vous montre comment sécuriser un client OPC UA et un OPC UA application serveur avec une nouvelle paire de clés et le certificat à l’aide d’OPC coffre.

Dans la configuration suivante, le client OPC teste la connectivité à la PLC OPC. Par défaut, la connectivité n’est pas possible, car les deux composants n’ont pas été approvisionnés avec les certificats de droite. Dans ce flux de travail, nous pas utiliser les certificats auto-signés de composants OPC UA et signez-les via OPC Vault. Consultez le précédent [banc d’essai](howto-opc-vault-deploy-existing-client-plc-communication.md). Au lieu de cela, cette banc d’essai configure les composants avec un nouveau certificat, ainsi qu’avec une nouvelle clé privée qui sont générés par OPC Vault. Vous trouverez des informations générales sur la sécurité de OPC UA dans ce [livre blanc](https://opcfoundation.org/wp-content/uploads/2014/05/OPC-UA_Security_Model_for_Administrators_V1.00.pdf). Vous trouverez les informations complètes dans la spécification OPC UA.

Banc d’essai : L’environnement suivant est configuré pour le test.

Scripts de OPC coffre :
- Sécuriser les applications serveur avec une nouvelle paire de clés et le certificat à l’aide d’OPC coffre client OPC UA et OPC UA.

> [!NOTE]
> Pour plus d’informations, consultez le GitHub [référentiel](https://github.com/Azure-Samples/iot-edge-industrial-configs#testbeds).

## <a name="generate-a-new-certificate-and-private-key"></a>Générer un nouveau certificat et la clé privée 
**Préparation**
- Vérifiez que les variables d’environnement `$env:_PLC_OPT` et `$env:_CLIENT_OPT` ne sont pas définis. Par exemple, `$env:_PLC_OPT=""` dans votre PowerShell
- Définissez la variable d’environnement `$env:_OPCVAULTID` vers une chaîne qui vous permet de retrouver vos données dans le coffre de OPC. Nous vous recommandons de définir un nombre à 6 chiffres. Dans notre exemple, « 123456 » a été utilisé en tant que valeur pour la variable.
- Vérifiez qu’il en existe aucun volume docker `opcclient` ou `opcplc`. Vérifiez auprès de `docker volume ls` et supprimez-les avec `docker volume rm <volumename>`. Vous devrez peut-être supprimer également les conteneurs avec `docker rm <containerid>` si les volumes sont toujours utilisés par un conteneur.

**Démarrage rapide**
1. Accédez à la [site Web OPC coffre](https://opcvault.azurewebsites.net/)

1. Sélectionnez `Register New`.

1. Entrez les informations de OPC PLC tel qu’il a été affiché dans la sortie du journal du banc d’essai précédente `CreateSigningRequest information` zone dans les champs d’entrée sur le `Register New OPC UA Application` page, sélectionnez `Server` comme ApplicationType.

1. Sélectionnez `Register`.

1. Sur la page suivante, `Request New Certificate for OPC UA Application` sélectionnez `Request new KeyPair and Certificate`

1. Sur la page suivante, `Generate a new Certificate with a Signing Request` collez dans le `CSR (base64 encoded)` chaîne à partir de la sortie du journal dans le `CreateRequest` champ d’entrée. Vérifiez que vous copiez la chaîne complète.

1. Sur la page suivante, `Request New Certificate for OPC UA Application` sélectionnez `Request new Certificate with Signing Request`

1. Dans la page suivante `Generate a new KeyPair and for an OPC UA Application` entrez `CN=OpcPlc` comme SubjectName, `opcplc-<_OPCVAULTID>` (remplacez `<_OPCVAULTID>` avec le vôtre) en tant que nom de domaine, sélectionnez `PEM` comme PrivateKeyFormat et entrez un mot de passe (nous faisons référence plus tard en tant que `<certpassword-string>`)

1. Sélectionnez `Generate New KeyPair`.

1. Vous sont maintenant passer à `View Certificate Request Details`. Dans cette page, vous pouvez télécharger toutes les informations requises pour approvisionner les magasins de certificats de `opc-plc`.

1. Dans cette page :  
    - Sélectionnez `Certificate` dans `Download as Base64` , puis copiez la chaîne de texte présentée dans le `EncodedBase64` champ et stockez-le pour une utilisation ultérieure. Nous faisons référence en tant que `<applicationcertbase64-string>` par la suite. Sélectionnez `Back`.
    - Sélectionnez `PrivateKey` dans `Download as Base64` , puis copiez la chaîne de texte présentée dans le `EncodedBase64` champ et stockez-le pour une utilisation ultérieure. Nous faisons référence en tant que `<privatekeybase64-string>` par la suite. Sélectionnez `Back`.
    - Sélectionnez `Issuer` dans `Download as Base64` , puis copiez la chaîne de texte présentée dans le `EncodedBase64` champ et stockez-le pour une utilisation ultérieure. Nous faisons référence en tant que `<addissuercertbase64-string>` par la suite. Sélectionnez `Back`.
    - Sélectionnez `Crl` dans `Download as Base64` , puis copiez la chaîne de texte présentée dans le `EncodedBase64` champ et stockez-le pour une utilisation ultérieure. Nous faisons référence en tant que `<updatecrlbase64-string>` par la suite. Sélectionnez `Back`.

1. Définissez maintenant dans votre PowerShell une variable nommée `$env:_PLC_OPT`:

   `$env:_PLC_OPT="--applicationcertbase64 <applicationcertbase64-string> --privatekeybase64 <privatekeybase64-string> --certpassword <certpassword-string> --addtrustedcertbase64 <addissuercertbase64-string> --addissuercertbase64 <addissuercertbase64-string> --updatecrlbase64 <updatecrlbase64-string>"`  

    Remplacez les chaînes transmis en tant que chaînes Base64 d’option valeurs que vous extraites depuis le site Web.  

1. Répétez le processus complet en commençant par `Register New` pour le client OPC. Il existe uniquement les différences suivantes que vous devez être conscient :
    - Utiliser la sortie du journal à partir de la `opcclient`.
    - Sélectionnez `Client` comme ApplicationType lors de l’inscription.
    - Utilisez `$env:_CLIENT_OPT` comme nom de la variable PowerShell.

    > [!NOTE] 
    > Lorsque vous travaillez avec ce scénario, vous pourrez être reconnu que le `<addissuercertbase64-string>` et `<updatecrlbase64-string>` valeurs sont identiques pour `opcplc` et `opcclient`. Cela est vrai pour notre cas d’usage et peut vous faire gagner du temps tout en effectuant les étapes.

**Démarrage rapide**

Dans la racine du référentiel, exécutez la commande PowerShell suivante :

```
docker-compose -f connecttest.yml up
```

**Vérification**

Vérifiez que les deux composants n’ont pas un certificat d’application existant. Vérifiez la sortie de journal. Voici la sortie de OPC PLC et client OPC a une sortie de journal similaires.

```
opcplc-123456 | [13:40:08 INF] There is no existing application certificate.
```
S’il existe un certificat d’application, supprimez les volumes docker comme expliqué dans les étapes de préparation.

Dans le journal, vérifiez que le certificat d’autorité de certification OPC coffre a été installé dans le magasin de certificats émetteur ainsi que dans le magasin de certificats de confiance homologue. Voici la sortie du journal de OPC PLC et OPC Client a une sortie de journal similaires. 

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
Le PLC OPC approuve désormais de tous les clients OPC UA avec des certificats signés par OPC Vault.

Vérifiez dans le journal que le format de clé privée est reconnu comme PEM et que le nouveau certificat de l’application est installé. Voici la sortie du journal de OPC PLC et client OPC a une sortie de journal similaires. 

```
opcplc-123456 | [13:40:09 INF] The private key for the new certificate was passed in using PEM format.
opcplc-123456 | [13:40:09 INF] Remove the existing application certificate.
opcplc-123456 | [13:40:09 INF] The new application certificate 'CN=OpcPlc' and thumbprint 'A3CB288FC1D2B7A5C08AACF531CF4A85E44A6C4C' was added to the application certificate store.
opcplc-123456 | [13:40:09 INF] Activating the new application certificate with thumbprint 'A3CB288FC1D2B7A5C08AACF531CF4A85E44A6C4C'.
```

Le certificat d’application et la clé privée sont maintenant installés dans le magasin de certificats d’application et utilisés par l’application OPC UA.

Vérifiez que la connexion entre le client d’OPC et OPC PLC peut être établie avec succès et que le client OPC peut lire correctement les données de OPC PLC. Vous devez voir la sortie suivante dans la sortie du journal client OPC :
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
Si vous voyez cette sortie, puis le PLC OPC est désormais autorisé à approuver l’OPC client et vice versa, car elles disposent désormais les certificats signés par une autorité de certification et les deux certificats de confiance qui ont été signés par cette autorité de certification.

### <a name="a-testbed-for-opc-publisher"></a>Un banc d’essai pour l’éditeur d’OPC ###

**Démarrage rapide**

Dans la racine du référentiel, exécutez la commande PowerShell suivante :
```
docker-compose -f testbed.yml up
```

**Vérification**
- Vérifiez que les données sont envoyées à la IoTHub que vous avez configuré en définissant `_HUB_CS` à l’aide de [Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) ou [iothub-explorer](https://github.com/Azure/iothub-explorer).
- Client de test OPC va utiliser les appels de méthode directe de IoTHub et appels de méthode OPC pour configurer le serveur de publication OPC pour publier/annuler la publication de nœuds à partir du serveur de test OPC.
- Regardez la sortie des messages d’erreur.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à déployer OPC coffre à un projet existant, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Déployer la représentation d’OPC à un projet existant](howto-opc-twin-deploy-existing.md)