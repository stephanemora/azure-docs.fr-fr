---
title: Sécuriser la communication du client OPC et du contrôleur PLC OPC avec OPC Vault - Azure | Microsoft Docs
description: Sécurisez la communication du client OPC et du contrôleur PLC OPC en signant leurs certificats avec l’autorité de certification OPC Vault.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 30eedd982fa0536ce45506c159de6d04132e9a14
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61451087"
---
# <a name="secure-the-communication-of-opc-client-and-opc-plc"></a>Sécuriser la communication du client OPC et du contrôleur PLC OPC

OPC Vault est un microservice qui peut configurer, inscrire et gérer le cycle de vie des certificats pour les applications serveur et clientes OPC UA dans le cloud. Cet article vous montre comment sécuriser la communication du client OPC et du contrôleur PLC OPC en signant leurs certificats avec l’autorité de certification OPC Vault.

Dans la configuration suivante, le client OPC teste la connectivité au contrôleur PLC OPC. Par défaut, la connectivité n’est pas possible, car les deux composants ne sont pas provisionnés avec les certificats appropriés. Si un composant OPC UA n’était pas encore provisionné avec un certificat, il va générer un certificat auto-signé au démarrage. Cependant, le certificat peut être signé par une autorité de certification et installé dans le composant OPC UA. Une fois cette opération effectuée pour le client OPC et pour le contrôleur PLC OPC, la connectivité est activée. Le workflow ci-dessous décrit le processus. Vous trouverez des informations générales sur la sécurité d’OPC UA dans ce [livre blanc](https://opcfoundation.org/wp-content/uploads/2014/05/OPC-UA_Security_Model_for_Administrators_V1.00.pdf). Vous trouverez les informations complètes dans la spécification OPC UA.

Banc de test : L’environnement suivant est configuré pour le test.

Scripts OPC Vault :
- Sécurisez la communication du client OPC et du contrôleur PLC OPC en signant leurs certificats avec l’autorité de certification OPC Vault.

> [!NOTE]
> Pour plus d’informations, consultez le [dépôt](https://github.com/Azure-Samples/iot-edge-industrial-configs#testbeds) GitHub.

## <a name="generate-a-self-signed-certificate-on-startup"></a>Générer un certificat auto-signé au démarrage

**Préparation**

- Vérifiez que les variables d’environnement `$env:_PLC_OPT` et `$env:_CLIENT_OPT` ne sont pas définies, par exemple `$env:_PLC_OPT=""` dans votre PowerShell.

- Définissez la variable d’environnement `$env:_OPCVAULTID` sur une chaîne qui vous permet de retrouver vos données dans OPC Vault. Seuls les caractères alphanumériques sont autorisés. Dans notre exemple, « 123456 » a été utilisé comme valeur pour cette variable.

- Vérifiez qu’il n’existe pas de volumes docker `opcclient` ou `opcplc`. Vérifiez avec `docker volume ls` et supprimez-les avec `docker volume rm <volumename>`. Il peut être nécessaire de supprimer également les conteneurs avec `docker rm <containerid>` si les volumes sont toujours utilisés par un conteneur.

**Démarrage rapide**

Exécutez les commandes PowerShell suivantes dans la racine du dépôt :

```
docker-compose -f connecttest.yml up
```

**Vérification**

Dans le journal, vérifiez qu’aucun certificat n’est installé au premier démarrage. Voici la sortie du journal du contrôleur PLC OPC (similaire à celui du client OPC) :
```
opcplc-123456 | [20:51:32 INF] Trusted issuer store contains 0 certs
opcplc-123456 | [20:51:32 INF] Trusted issuer store has 0 CRLs.
opcplc-123456 | [20:51:32 INF] Trusted peer store contains 0 certs
opcplc-123456 | [20:51:32 INF] Trusted peer store has 0 CRLs.
opcplc-123456 | [20:51:32 INF] Rejected certificate store contains 0 certs
```
Si vous voyez des certificats signalés, suivez les étapes de préparation ci-dessus et supprimez les volumes docker.

Vérifiez que la connexion au contrôleur PLC OPC a échoué. Vous devez ceci dans la sortie du journal du client OPC :

```
opcclient-123456 | [20:51:35 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [20:51:36 ERR] Session creation to endpoint 'opc.tcp://opcplc-123456:50000/' failed 1 time(s). Please verify if server is up and OpcClient configuration is correct.
opcclient-123456 | Opc.Ua.ServiceResultException: Certificate is not trusted.
```
La raison de l’échec est que le certificat n’est pas approuvé. Cela signifie que `opc-client` a tenté de se connecter à `opc-plc` et a reçu une réponse en retour, indiquant que `opc-plc` ne fait pas confiance à `opc-client`, ce qui est dû au fait que `opc-plc` ne peut pas valider le certificat que `opc-client` a fourni. Il s’agit sur un certificat auto-signé sans autre configuration de certificat sur `opc-plc` : la connexion a donc échoué.

## <a name="sign-and-install-certificates-in-opc-ua-components"></a>Se connecter et installer des certificats dans des composants OPC UA

**Préparation**
1. Examinez la sortie du journal de l’étape 1 et extrayez « CreateSigningRequest information » pour le contrôleur PLC OPC et le client OPC. Ici, la sortie est montrée seulement pour le contrôleur PLC OPC :

    ```
    opcplc-123456 | [20:51:32 INF] ----------------------- CreateSigningRequest information ------------------
    opcplc-123456 | [20:51:32 INF] ApplicationUri: urn:OpcPlc:opcplc-123456
    opcplc-123456 | [20:51:32 INF] ApplicationName: OpcPlc
    opcplc-123456 | [20:51:32 INF] ApplicationType: Server
    opcplc-123456 | [20:51:32 INF] ProductUri: https://github.com/azure-samples/iot-edge-opc-plc
    opcplc-123456 | [20:51:32 INF] DiscoveryUrl[0]: opc.tcp://opcplc-123456:50000
    opcplc-123456 | [20:51:32 INF] ServerCapabilities: DA
    opcplc-123456 | [20:51:32 INF] CSR (base64 encoded):
    opcplc-123456 | MIICmzCCAYMCAQAwETEPMA0GA1UEAwwGT3BjUGxjMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAwTvlbinAPWPxR9Lw1ndGsrLGy8GiqVOxyGaDpPUcMchX0k0/ncg28h7xrB2H1PThdiZxUJuUwsNM74HrVgt
    ofmXhA4dLM1cTxZHyJVFjl2L3vK5M58NNf6UNdKcB0x3LyuoT6mAIMXmCioqymFCk1TMzIMzbAe7JVAdUaSRBP1vuqQ1rV/cfNAe35dKQW4aPYgl7pR5f1hqprcDu/oca67X8L4kTl4oN0/bCYTk+Ibcd9cG462oAN+bSwbHn8a2jNky8rGsofA
    o9DOT+0ALPhk6CApCYIP2yxoI/kT188eqUUxzAFF9nyU79AyCkpGPuY8DGMyf56pDofgtGpfY3wQIDAQABoEUwQwYJKoZIhvcNAQkOMTYwNDAyBgNVHREEKzAphhh1cm46T3BjUGxjOm9wY3BsYy0xMjM0NTaCDW9wY3BsYy0xMjM0NTYwDQYJK
    oZIhvcNAQELBQADggEBAAsZLoOLzS2VhDcQRu0QhRbG7CGAxX19l7fDCG2WjU7lTFnCvYVTWTYyaY61ljmrWc7IbCaQdMJM8GRnAnvAzUh/PBDxkOX7NqI2+8F1yQOHgs/AfKuppOd6DIP8EzFAHnc0H85jay6zFdmIDWoWwpy0ACqOVooOTKST
    7uty0mT87bj8Cdy1yf4mvBNQx+nsuTbKgxWCBxGYAyg9dIL2uKL0aeB/ROW5Gkelz5sCEzQ1fFDokUA4oC5QiATQBN3cY7EmvRbPgdToY7CpRN3iiO7J+7bC7BP9YKfuE34E8xOFpskHPHAPf3r002/L0S67HyuVSXLUj1+Jc0LeAAF9Bw0=
    opcplc-123456 | [20:51:32 INF] ---------------------------------------------------------------------------
    ```
    
1. Accédez au [site web OPC Vault](https://opcvault.azurewebsites.net/).

1. Sélectionnez `Register New`.

1. Entrez les informations du contrôleur PLC OPC, provenant de la zone `CreateSigningRequest information` des sorties du journal, dans les champs d’entrée de la page `Register New OPC UA Application`, puis sélectionnez `Server` pour ApplicationType.

1. Sélectionnez `Register`.

1. Dans la page suivante, `Request New Certificate for OPC UA Application`, sélectionnez `Request new Certificate with Signing Request`.

1. Dans la page suivante, `Generate a new Certificate with a Signing Request`, collez la chaîne `CSR (base64 encoded)` depuis la sortie du journal dans le champ d’entrée `CreateRequest`. Veillez à copier la chaîne complète.

1. Sélectionnez `Generate New Certificate`.

1. Vous passez maintenant à `View Certificate Request Details`. Dans cette page, vous pouvez télécharger toutes les informations nécessaires pour provisionner les magasins de certificats de `opc-plc`.

1. Dans cette page :  
    - Sélectionnez `Certificate` dans `Download as Base64`, puis copiez la chaîne de texte présente dans le champ `EncodedBase64` et stockez-la pour une utilisation ultérieure. Nous la désignons plus loin par `<applicationcertbase64-string>`. Sélectionnez `Back`.

    - Sélectionnez `Issuer` dans `Download as Base64`, puis copiez la chaîne de texte présente dans le champ `EncodedBase64` et stockez-la pour une utilisation ultérieure. Nous la désignons plus loin par `<addissuercertbase64-string>`. Sélectionnez `Back`.

    - Sélectionnez `Crl` dans `Download as Base64`, puis copiez la chaîne de texte présente dans le champ `EncodedBase64` et stockez-la pour une utilisation ultérieure. Nous la désignons plus loin par `<updatecrlbase64-string>`. Sélectionnez `Back`.

1. Définissez maintenant dans votre PowerShell une variable nommée `$env:_PLC_OPT` :

    ```
    `$env:_PLC_OPT="--applicationcertbase64 <applicationcertbase64-string> --addtrustedcertbase64 <addissuercertbase64-string> --addissuercertbase64 <addissuercertbase64-string> --updatecrlbase64 <updatecrlbase64-string>"`  
    ```
    > [!NOTE] 
    > Remplacez les chaînes passées en tant que chaînes Base64 pour les valeurs d’option que vous extraites depuis le site web.

Répétez le processus complet en commençant par `Register New` (étape 3 ci-dessus) pour le client OPC. Notez seulement les différences suivantes :

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

**Vérification** : vérifiez que les deux composants ont maintenant un certificat d’application signé. Vérifiez la sortie du journal pour la sortie suivante :

```
opcplc-123456 | [20:54:38 INF] Starting to add certificate(s) to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Certificate 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' and thumbprint 'BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83' was added to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Starting to add certificate(s) to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Certificate 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' and thumbprint 'BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83' was added to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Starting to update the current CRL.
opcplc-123456 | [20:54:38 INF] Remove the current CRL from the trusted peer store.
opcplc-123456 | [20:54:38 INF] The new CRL issued by 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' was added to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Remove the current CRL from the trusted issuer store.
opcplc-123456 | [20:54:38 INF] The new CRL issued by 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' was added to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Start updating the current application certificate.
opcplc-123456 | [20:54:38 INF] The current application certificate has SubjectName 'CN=OpcPlc' and thumbprint '8FD43F66479398BDA3AAF5B193199A6657632B49'.
opcplc-123456 | [20:54:39 INF] Remove the existing application certificate with thumbprint '8FD43F66479398BDA3AAF5B193199A6657632B49'.
opcplc-123456 | [20:54:39 INF] The new application certificate 'CN=OpcPlc' and thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586' was added to the application certificate store.
opcplc-123456 | [20:54:39 INF] Activating the new application certificate with thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586'.
opcplc-123456 | [20:54:39 INF] Application certificate with thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586' found in the application certificate store.
opcplc-123456 | [20:54:39 INF] Application certificate is for ApplicationUri 'urn:OpcPlc:opcplc-123456', ApplicationName 'OpcPlc' and Subject is 'OpcPlc'
 ```
Le certificat d’application s’y trouve et est signé par une autorité de certification.

Dans le journal, vérifiez que des certificats sont maintenant installés. Voici la sortie du journal du contrôleur PLC OPC ; le client OPC a une sortie similaire.
```
opcplc-123456 | [20:54:39 INF] Trusted issuer store contains 1 certs
opcplc-123456 | [20:54:39 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [20:54:39 INF] Trusted issuer store has 1 CRLs.
opcplc-123456 | [20:54:39 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [20:54:39 INF] Trusted peer store contains 1 certs
opcplc-123456 | [20:54:39 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [20:54:39 INF] Trusted peer store has 1 CRLs.
opcplc-123456 | [20:54:39 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [20:54:39 INF] Rejected certificate store contains 0 certs
```
L’émetteur du certificat d’application est l’autorité de certification `CN=Azure IoT OPC Vault CA, O=Microsoft Corp.` et le contrôleur PLC OPC fait également confiance à tous les certificats signés par cette autorité de certification.


Vérifiez que la connexion au contrôleur PLC OPC a été créée avec succès et que le client OPC peut lire les données provenant du contrôleur PLC OPC. Vous devez ceci dans la sortie du journal du client OPC :
```
opcclient-123456 | [20:54:42 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [20:54:42 INF] Session successfully created with Id ns=3;i=1085867946.
opcclient-123456 | [20:54:42 INF] The session to endpoint 'opc.tcp://opcplc-123456:50000/' has 4 entries in its namespace array:
opcclient-123456 | [20:54:42 INF] Namespace index 0: http://opcfoundation.org/UA/
opcclient-123456 | [20:54:42 INF] Namespace index 1: urn:OpcPlc:opcplc-123456
opcclient-123456 | [20:54:42 INF] Namespace index 2: http://microsoft.com/Opc/OpcPlc/
opcclient-123456 | [20:54:42 INF] Namespace index 3: http://opcfoundation.org/UA/Diagnostics
opcclient-123456 | [20:54:42 INF] The server on endpoint 'opc.tcp://opcplc-123456:50000/' supports a minimal sampling interval of 0 ms.
opcclient-123456 | [20:54:42 INF] Execute 'OpcClient.OpcTestAction' action on node 'i=2258' on endpoint 'opc.tcp://opcplc-123456:50000/' with security.
opcclient-123456 | [20:54:42 INF] Action (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258') completed successfully
opcclient-123456 | [20:54:42 INF] Value (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258'): 10/20/2018 20:54:42
```
Si vous voyez cette sortie, c’est que le contrôleur PLC OPC fait désormais confiance au client OPC et inversement, car ils ont désormais tous deux des certificats signés par une autorité de certification et les deux certificats de confiance qui ont été signés par cette autorité de certification.

> [!NOTE] 
> Nous avons montré les deux premières étapes de vérification seulement pour le contrôleur PLC OPC, mais ces étapes doivent être appliquées aussi au client OPC.


## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert comment déployer OPC Vault sur un projet existant, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Déployer OPC Twin sur un projet existant](howto-opc-twin-deploy-existing.md)