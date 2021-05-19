---
title: Contrôler l’accès à IoT Hub à l’aide de jetons SAS | Microsoft Docs
description: Comment contrôler l’accès à IoT Hub pour les applications d’appareil et les applications principales à l’aide de jetons de signature d’accès partagé.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/21/2021
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Operations'
- devx-track-js
- devx-track-csharp
ms.openlocfilehash: 27ab47be439b83af4297330c2b85fdc844bfcf3a
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109489690"
---
# <a name="control-access-to-iot-hub-using-shared-access-signatures-and-security-tokens"></a>Contrôler l’accès à IoT Hub à l’aide de signatures d’accès partagé et de jetons de sécurité

Cet article décrit les options de sécurisation de votre hub IoT. IoT Hub utilise des *autorisations* pour accorder l’accès à chaque point de terminaison IoT Hub. Les autorisations limitent l’accès à un hub IoT selon la fonctionnalité.

Cet article présente :

* Les différentes autorisations que vous pouvez accorder à un client pour accéder à votre hub IoT.
* Les jetons qu’IoT Hub utilise pour vérifier les autorisations.
* Le mode de définition de l’étendue des informations d’identification pour limiter l’accès à des ressources spécifiques.
* Les mécanismes d’authentification d’appareil personnalisés qui utilisent des registres d’identités d’appareil ou des schémas d’authentification existants.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Pour accéder à tout point de terminaison IoT Hub, vous devez disposer des autorisations appropriées. Par exemple, un appareil doit inclure un jeton contenant des informations d’identification de sécurité dans chaque message envoyé à IoT Hub. Toutefois, les clés de signature, comme les clés symétriques d’appareil, ne sont jamais envoyées sur le réseau.

## <a name="access-control-and-permissions"></a>Contrôle d’accès et autorisations

Utilisez des stratégies d’accès partagé pour l’accès au niveau de l’instance IoT Hub et utilisez les informations d’identification de l’appareil individuel pour limiter l’accès à cet appareil uniquement.

### <a name="iot-hub-level-shared-access-policies"></a>Stratégies d’accès partagé au niveau d’IoT Hub

Les stratégies d’accès partagé peuvent accorder n’importe quelle combinaison d’autorisations. Vous pouvez définir des stratégies dans le [portail Azure](https://portal.azure.com), par programmation à l’aide des [API REST de ressources IoT Hub](/rest/api/iothub/iothubresource) ou en utilisant l’interface CLI [az iot hub policy](/cli/azure/iot/hub/policy). Un hub IoT qui vient d’être créé a les stratégies par défaut suivantes :
  
| Stratégie d’accès partagé | Autorisations |
| -------------------- | ----------- |
| iothubowner | Toutes les autorisations |
| service | Autorisations **ServiceConnect** |
| device | Autorisations **DeviceConnect** |
| registryRead | Autorisations **RegistryRead** |
| registryReadWrite | Autorisations **RegistryRead** et **RegistryWrite** |

Le tableau suivant répertorie les autorisations que vous pouvez utiliser pour contrôler l’accès à votre IoT Hub.

| Autorisation | Notes |
| --- | --- |
| **ServiceConnect**. |Accorde l’accès à la communication de services cloud et à la surveillance des points de terminaison. <br/>Accorde l’autorisation de recevoir des messages appareil-à-cloud, d’envoyer des messages cloud-à-appareil et de récupérer les accusés de remise correspondants. <br/>Accorde l’autorisation de récupérer les accusés de remise pour les chargements de fichiers. <br/>Accorde l’autorisation d’accéder aux jumeaux pour mettre à jour les balises et les propriétés voulues, de récupérer les propriétés signalées et d’exécuter des requêtes. <br/>Cette autorisation est utilisée par les services cloud principaux. |
| **DeviceConnect**. |Accorde l’accès aux points de terminaison côté appareil. <br/>Accorde l’autorisation d’envoyer des messages appareil-à-cloud et de recevoir des messages cloud-à-appareil. <br/>Accorde l’autorisation d’effectuer un chargement de fichiers à partir d’un appareil. <br/>Accorde l’autorisation de recevoir des notifications de propriétés voulues pour les jumeaux d’appareil et de mettre à jour les propriétés signalées pour les jumeaux d’appareil. <br/>Accorde l’autorisation d’effectuer des chargements de fichiers. <br/>Cette autorisation est utilisée par les appareils. |
| **RegistryRead**. |Accorde l’accès en lecture au registre des identités. Pour plus d’informations, consultez [Registre des identités](iot-hub-devguide-identity-registry.md). <br/>Cette autorisation est utilisée par les services cloud principaux. |
| **RegistryReadWrite**. |Accorde l’accès en lecture et en écriture au registre des identités. Pour plus d’informations, consultez [Registre des identités](iot-hub-devguide-identity-registry.md). <br/>Cette autorisation est utilisée par les services cloud principaux. |

### <a name="per-device-security-credentials"></a>Informations d’identification de sécurité par appareil

Chaque IoT Hub contient un [registre des identités](iot-hub-devguide-identity-registry.md). Pour chaque appareil dans ce registre des identités, vous pouvez configurer des informations d’identification de sécurité qui accordent des autorisations **DeviceConnect** incluses dans l’étendue des points de terminaison des appareils correspondants.

## <a name="security-tokens"></a>Jetons de sécurité

IoT Hub utilise des jetons de sécurité pour authentifier les appareils et les services afin d’éviter d’envoyer des clés sur le réseau. En outre, la validité et la portée des jetons sont limitées dans le temps. Ces jetons de sécurité sont aussi appelés jetons d’accès partagé (SAP). Les [kits de développement logiciel (SDK) Azure IoT](iot-hub-devguide-sdks.md) génèrent automatiquement des jetons, sans nécessiter de configuration particulière. Certains scénarios nécessitent toutefois que vous génériez et utilisiez directement des jetons de sécurité. Il s’agit entre autres des scénarios suivants :

* Utilisation directe des surfaces MQTT, AMQP ou HTTPS.

* Implémentation du modèle de service de jeton, comme indiqué dans [Authentification d’appareil personnalisée](iot-hub-dev-guide-sas.md#create-a-token-service-to-integrate-existing-devices).

Vous utilisez des jetons de sécurité pour accorder un accès limité dans le temps aux appareils et aux services à des fonctionnalités spécifiques dans IoT Hub. Pour obtenir l’autorisation de se connecter à IoT Hub, les appareils et services doivent envoyer des jetons de sécurité signés avec une clé symétrique ou d’accès partagé. Ces clés sont stockées avec l’identité de l’appareil dans le registre de l’identité.

### <a name="security-token-structure"></a>Structure du jeton de sécurité

Un jeton signé avec une clé d’accès partagé accorde un accès à toutes les fonctionnalités associées aux autorisations de stratégie d’accès partagé. Un jeton signé avec la clé symétrique de l’identité de l’appareil accorde uniquement l’autorisation **DeviceConnect** à l’identité de l’appareil associée.

Le jeton de sécurité présente le format suivant :

`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Voici les valeurs attendues :

| Valeur | Description |
| --- | --- |
| {signature} |Une chaîne de signature HMAC-SHA256 sous la forme : `{URL-encoded-resourceURI} + "\n" + expiry`. **Important !** la clé est décodée à partir de base64 et utilisée comme clé pour effectuer le calcul HMAC-SHA256. |
| {resourceURI} |Préfixe URI (par segment) des points de terminaison qui sont accessibles avec ce jeton, en commençant par le nom d’hôte IoT Hub (sans protocole). Par exemple : `myHub.azure-devices.net/devices/device1` |
| {expiry} |Chaînes UTF8 pour le nombre de secondes depuis l’époque 00:00:00 UTC 1er janvier 1970. |
| {URL-encoded-resourceURI} |Encodage en URL minuscules de l’URI de ressource en minuscules |
| {policyName} |Le nom de la stratégie d’accès partagé à laquelle ce jeton fait référence. Il n’y en a pas si le jeton fait référence aux informations d’identification de registre des appareils. |

le préfixe URI est calculé par segment et non par caractère. Par exemple `/a/b` est un préfixe de `/a/b/c`, mais pas de `/a/bc`.

### <a name="nodejs"></a>[Node.JS](#tab/node)

L’extrait de code Node.js suivant illustre une fonction appelée **generateSasToken** qui calcule le jeton à partir des entrées `resourceUri, signingKey, policyName, expiresInMins`. Les sections suivantes décrivent en détail comment initialiser les différentes entrées pour les différents cas d’utilisation des jetons.

```javascript
var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
    resourceUri = encodeURIComponent(resourceUri);

    // Set expiration in seconds
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    expires = Math.ceil(expires);
    var toSign = resourceUri + '\n' + expires;

    // Use crypto
    var hmac = crypto.createHmac('sha256', Buffer.from(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
};
```

### <a name="python"></a>[Python](#tab/python)

À titre de comparaison, le code Python équivalent pour générer un jeton de sécurité est :

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import parse
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((parse.quote_plus(uri)), int(ttl))
    print(sign_key)
    signature = b64encode(HMAC(b64decode(key), sign_key.encode('utf-8'), sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl))
    }

    if policy_name is not None:
        rawtoken['skn'] = policy_name

    return 'SharedAccessSignature ' + parse.urlencode(rawtoken)
```
### <a name="c"></a>[C#](#tab/csharp)

La fonctionnalité en C# pour générer un jeton de sécurité est la suivante :

```csharp
using System;
using System.Globalization;
using System.Net;
using System.Net.Http;
using System.Security.Cryptography;
using System.Text;

public static string generateSasToken(string resourceUri, string key, string policyName, int expiryInSeconds = 3600)
{
    TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
    string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + expiryInSeconds);

    string stringToSign = WebUtility.UrlEncode(resourceUri) + "\n" + expiry;

    HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(key));
    string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));

    string token = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}", WebUtility.UrlEncode(resourceUri), WebUtility.UrlEncode(signature), expiry);

    if (!String.IsNullOrEmpty(policyName))
    {
        token += "&skn=" + policyName;
    }

    return token;
}
```
### <a name="java"></a>[Java](#tab/java)

Pour Java :
```java
    public static String generateSasToken(String resourceUri, String key) throws Exception {
        // Token will expire in one hour
        var expiry = Instant.now().getEpochSecond() + 3600;

        String stringToSign = URLEncoder.encode(resourceUri, StandardCharsets.UTF_8) + "\n" + expiry;
        byte[] decodedKey = Base64.getDecoder().decode(key);

        Mac sha256HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secretKey = new SecretKeySpec(decodedKey, "HmacSHA256");
        sha256HMAC.init(secretKey);
        Base64.Encoder encoder = Base64.getEncoder();

        String signature = new String(encoder.encode(
            sha256HMAC.doFinal(stringToSign.getBytes(StandardCharsets.UTF_8))), StandardCharsets.UTF_8);

        String token = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, StandardCharsets.UTF_8)
                + "&sig=" + URLEncoder.encode(signature, StandardCharsets.UTF_8.name()) + "&se=" + expiry;
            
        return token;
    }
```
---

### <a name="protocol-specifics"></a>Spécifications de protocole

Chaque protocole pris en charge (par exemple, MQTT, AMQP et HTTPS) transporte des jetons de différentes façons.

Quand vous utilisez MQTT, le paquet CONNECT utilise deviceid en tant que ClientId, `{iothubhostname}/{deviceId}` dans le champ Nom d’utilisateur et un jeton SAP dans le champ Mot de passe. `{iothubhostname}` doit être le nom canonique (CNAME) complet du hub IoT (par exemple, contoso.azure-devices.net).

Lorsque vous utilisez [AMQP](https://www.amqp.org/), IoT Hub prend en charge [SASL PLAIN](https://tools.ietf.org/html/rfc4616) et la [sécurité basée sur des revendications AMQP](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc).

Si vous utilisez une sécurité basée sur des revendications AMQP, la norme indique comment transmettre les jetons répertoriés ci-dessus.

Pour SASL PLAIN, le **nom d’utilisateur** peut être :

* `{policyName}@sas.root.{iothubName}` si vous utilisez des jetons au niveau du hub IoT.
* `{deviceId}@sas.{iothubname}` si vous utilisez des jetons à l’échelle de l’appareil.

Dans les deux cas, le champ de mot de passe contient le jeton, comme indiqué dans [Jetons de sécurité IoT Hub](iot-hub-dev-guide-sas.md#security-tokens).

Le protocole HTTPS implémente l’authentification en incluant un jeton valide dans l’en-tête de requête **Authorization**.

Par exemple, nom d’utilisateur (DeviceId respecte la casse) : `iothubname.azure-devices.net/DeviceId`

Mot de passe (vous pouvez générer un jeton SAS à l'aide de la commande d'extension CLI [az iot hub generate-sas-token](/cli/azure/iot/hub#az_iot_hub_generate_sas_token) ou d'[Azure IoT Tools pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)) :

`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> Les [kis de développement logiciel (SDK) Azure IoT](iot-hub-devguide-sdks.md) génèrent automatiquement des jetons lors de la connexion au service. Dans certains cas, les kits Azure IoT SDK ne prennent pas en charge l’ensemble des protocoles ou méthodes d’authentification.

### <a name="special-considerations-for-sasl-plain"></a>Considérations spécifiques concernant SASL PLAIN

Lorsque vous utilisez SASL PLAIN avec AMQP, un client qui se connecte à un IoT Hub peut utiliser un jeton unique pour chaque connexion TCP. Quand le jeton expire, la connexion TCP est déconnectée du service, ce qui déclenche une reconnexion. Bien que non problématique pour une application principale, ce comportement peut créer des dommages pour une application d’appareil pour les motifs suivants :

* Les passerelles se connectent généralement au nom de nombreux appareils. Lorsque vous utilisez SASL PLAIN, elles doivent créer une connexion TCP distincte pour chaque appareil se connectant à un hub IoT. Ce scénario augmente considérablement la consommation des ressources d’alimentation et de mise en réseau, ainsi que la latence de chaque connexion d’appareil.

* Les appareils avec des contraintes de ressources sont affectés par l’utilisation accrue des ressources pour se reconnecter après chaque expiration du jeton.

## <a name="use-security-tokens-from-service-components"></a>Utilisation de jetons de sécurité de composants du service

Les composants de service peuvent uniquement créer des jetons de sécurité utilisant des stratégies d’accès partagé pour accorder les autorisations adaptées, comme expliqué précédemment.

Voici les fonctions de service exposées sur les points de terminaison :

| Point de terminaison | Fonctionnalités |
| --- | --- |
| `{iot hub host name}/devices` |Créer, mettre à jour, récupérer et supprimer les identités des appareils. |
| `{iot hub host name}/messages/events` |Recevoir des messages appareil-à-cloud. |
| `{iot hub host name}/servicebound/feedback` |Recevoir des commentaires pour les messages cloud-à-appareil. |
| `{iot hub host name}/devicebound` |Envoyer des messages cloud-à-appareil. |

Par exemple, un service qui génère à l’aide de la stratégie d’accès partagé précréée appelée **registryRead** crée un jeton avec les paramètres suivants :

* URI de ressource : `{IoT hub name}.azure-devices.net/devices`,
* clé de signature : une des clés de la stratégie `registryRead` ,
* nom de la stratégie : `registryRead`,
* n’importe quelle heure d’expiration.

```javascript
var endpoint ="myhub.azure-devices.net/devices";
var policyName = 'registryRead';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Le résultat, qui revient à accorder l’accès en lecture à toutes les identités des appareils, est alors :

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead`

## <a name="authenticating-a-device-to-iot-hub"></a>Authentifier un appareil avec IoT Hub

### <a name="supported-x509-certificates"></a>Certificats X.509 pris en charge

Vous pouvez utiliser n’importe quel certificat X.509 pour authentifier un appareil avec IoT Hub en chargeant une empreinte de certificat ou une autorité de certification (CA) sur Azure IoT Hub. Pour plus d’informations, consultez [Authentification des appareils à l’aide de certificats d’autorité de certification X.509](iot-hub-x509ca-overview.md). Pour plus d’informations sur le chargement et la vérification d’une autorité de certification avec votre IoT Hub, consultez [Configurer la sécurité X.509 dans votre Azure IoT Hub](iot-hub-security-x509-get-started.md).

### <a name="use-sas-tokens-as-a-device"></a>Utilisation de jetons SAP comme appareil

Il existe deux façons d’obtenir des autorisations **DeviceConnect** avec IoT Hub au moyen de jetons de sécurité : utiliser une [clé d’appareil symétrique extraite du registre des identités](#use-a-symmetric-key-in-the-identity-registry) ou une [clé d’accès partagé](#use-a-shared-access-policy-to-access-on-behalf-of-a-device).

N’oubliez pas que toutes les fonctionnalités accessibles à partir des appareils sont exposées par définition sur les points de terminaison avec le préfixe `/devices/{deviceId}`.

Les points de terminaison côté appareil sont (quel que soit le protocole) :

| Point de terminaison | Fonctionnalités |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |Envoyer des messages appareil-à-cloud. |
| `{iot hub host name}/devices/{deviceId}/messages/devicebound` |Recevoir des messages cloud-à-appareil. |

#### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Utilisation d’une clé symétrique dans le registre d’identité

Lorsqu’une clé symétrique d’identité de l’appareil est utilisée pour générer un jeton, l’élément PolicyName (`skn`) du jeton est omis.

Par exemple, un jeton créé pour accéder à toutes les fonctionnalités de l’appareil doit avoir les paramètres suivants :

* URI de ressource : `{IoT hub name}.azure-devices.net/devices/{device id}`,
* clé de signature : n’importe quelle clé symétrique pour l’identité `{device id}` ,
* pas de nom de stratégie,
* n’importe quelle heure d’expiration.

Un exemple d’utilisation de la fonction Node.js précédente serait :

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var deviceKey ="...";

var token = generateSasToken(endpoint, deviceKey, null, 60);
```

Le résultat, qui accorde l’accès à toutes les fonctionnalités de device1, est alors :

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697`

> [!NOTE]
> Il est possible de générer un jeton SAS à l'aide de la commande d'extension CLI [az iot hub generate-sas-token](/cli/azure/iot/hub#az_iot_hub_generate_sas_token) ou d'[Azure IoT Tools pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

#### <a name="use-a-shared-access-policy-to-access-on-behalf-of-a-device"></a>Utiliser une stratégie d’accès partagé pour accéder au nom d’un appareil

Quand vous créez un jeton à partir d’une stratégie d’accès partagé, spécifiez le nom de la stratégie dans le champ `skn`. Cette stratégie doit accorder l’autorisation **DeviceConnect**.

Les deux principaux scénarios pour l’utilisation de stratégies d’accès partagé pour accéder aux fonctionnalités de l’appareil sont :

* [passerelles de protocole cloud](iot-hub-devguide-endpoints.md),
* [services de jeton](iot-hub-dev-guide-sas.md#create-a-token-service-to-integrate-existing-devices) utilisés pour implémenter des schémas d’authentification personnalisés.

Étant donné que la stratégie d’accès partagé peut potentiellement accorder un accès pour se connecter comme n’importe quel appareil, il est important d’utiliser l’URI de ressource correct lors de la création de jetons de sécurité. Ce paramètre est particulièrement important pour les services de jeton dont il faut définir l’étendue pour un appareil spécifique à l’aide de l’URI de ressource. Ce point est moins important pour les passerelles de protocole, car elles interceptent déjà le trafic pour tous les appareils.

Par exemple, un service de jeton utilisant la stratégie d’accès partagé précréée appelée **device** crée un jeton avec les paramètres suivants :

* URI de ressource : `{IoT hub name}.azure-devices.net/devices/{device id}`,
* clé de signature : une des clés de la stratégie `device` ,
* nom de la stratégie : `device`,
* n’importe quelle heure d’expiration.

Un exemple d’utilisation de la fonction Node.js précédente serait :

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Le résultat, qui accorde l’accès à toutes les fonctionnalités de device1, est alors :

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device`

Une passerelle de protocole peut utiliser le même jeton pour tous les appareils en définissant simplement l’URI de ressource sur `myhub.azure-devices.net/devices`.

## <a name="create-a-token-service-to-integrate-existing-devices"></a>Créer un service de jeton pour intégrer des appareils existants

Vous pouvez utiliser le [registre des identités](iot-hub-devguide-identity-registry.md) IoT Hub pour configurer les informations d’identification de sécurité et le contrôle d’accès par appareil/module à l’aide de [jetons](iot-hub-dev-guide-sas.md#security-tokens). Si une solution IoT a déjà un registre des identités et/ou un schéma d’authentification personnalisé, vous pouvez créer un *service de jeton* pour intégrer cette infrastructure existante à IoT Hub. De cette façon, vous pouvez utiliser d'autres fonctionnalités IoT dans votre solution.

Un service de jeton est un service cloud personnalisé. Il utilise une *stratégie d’accès partagé* IoT Hub avec des autorisations **DeviceConnect** ou **ModuleConnect** pour créer des jetons *device-scoped* ou *module-scoped*. Ces jetons permettent à un appareil et à un module de se connecter à votre hub IoT.

![Étapes du modèle de service de jeton](./media/iot-hub-devguide-security/tokenservice.png)

Voici les principales étapes du schéma de service de jeton :

1. Créez une stratégie d’accès partagé IoT Hub avec des autorisations **DeviceConnect** ou **ModuleConnect** pour votre hub IoT. Vous pouvez créer cette stratégie dans le [Portail Azure](https://portal.azure.com) ou par programme. Le service de jetons utilise cette stratégie pour signer les jetons qu'elle crée.

2. Quand un appareil/module doit accéder à votre hub IoT, il demande à votre service de jetons un jeton signé. L’appareil peut s’authentifier avec votre registre des identités personnalisé/schéma d’authentification pour déterminer l’identité d’appareil/de module que le service de jeton utilise pour créer le jeton.

3. Le service de jeton renvoie un jeton. Le jeton est créé à partir de `/devices/{deviceId}` ou `/devices/{deviceId}/module/{moduleId}` en tant que `resourceURI`, avec `deviceId` en tant qu’appareil en cours d’authentification ou `moduleId` en tant que module en cours d’authentification. Le service de jeton utilise la stratégie d'accès partagé pour construire le jeton.

4. L’appareil/le module utilise le jeton directement avec le hub IoT.

> [!NOTE]
> Vous pouvez utiliser la classe .NET [SharedAccessSignatureBuilder](/dotnet/api/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder) ou la classe Java [IotHubServiceSasToken](/java/api/com.microsoft.azure.sdk.iot.service.auth.iothubservicesastoken) pour créer un jeton dans votre service de jeton.

Le service de jetons peut définir l’expiration du jeton comme vous le souhaitez. Quand le jeton expire, le hub IoT interrompt la connexion. L’appareil/le /module doit ensuite demander un nouveau jeton au service de jeton. Un délai d’expiration court accroît la charge de l’appareil/du module et du service de jeton.

Pour qu’un appareil/module se connecte à votre hub, vous devez l’ajouter au registre des identités IoT Hub, même s’il utilise un jeton et non une clé pour se connecter. Ainsi, vous pouvez continuer d’utiliser le contrôle d’accès par appareil/par module en activant ou désactivant les identités des appareils/modules dans le [registre des identités](iot-hub-devguide-identity-registry.md). Cette approche réduit les risques liés à l'utilisation de jetons avec des délais d'expiration longs.

### <a name="comparison-with-a-custom-gateway"></a>Comparaison avec une passerelle personnalisée

Le modèle de service de jeton est la méthode recommandée pour implémenter un schéma de registre d’identité/d’authentification avec IoT Hub. Ce modèle est recommandé car il laisse IoT Hub gérer la plupart du trafic de la solution. Toutefois, si l’entrelacement entre le schéma d’authentification personnalisé et le protocole SSL est conséquent, vous aurez peut-être besoin d’une *passerelle personnalisée* pour traiter tout le trafic. Le [protocole TLS (Transport Layer Security) et les clés prépartagées (PSK)](https://tools.ietf.org/html/rfc4279) en sont un exemple. Pour plus d’informations, consultez l’article [Prise en charge de protocoles supplémentaires pour IoT Hub](iot-hub-protocol-gateway.md).


## <a name="additional-reference-material"></a>Matériel de référence supplémentaire

Les autres rubriques de référence dans le Guide du développeur IoT Hub comprennent :

* La rubrique [Points de terminaison IoT Hub](iot-hub-devguide-endpoints.md), qui décrit les différents points de terminaison que chaque hub IoT expose pour les opérations d’exécution et de gestion.

* La rubrique [Quotas et limitation IoT Hub](iot-hub-devguide-quotas-throttling.md) décrit les quotas et le comportement de limitation qui s’appliquent au service IoT Hub.

* La rubrique [SDK des services et appareils Azure IoT](iot-hub-devguide-sdks.md), qui répertorie les SDK en différents langages que vous pouvez utiliser pour le développement d’applications d’appareil et de service qui interagissent avec IoT Hub.

* La rubrique [Langage de requête IoT Hub](iot-hub-devguide-query-language.md) décrit le langage de requête permettant de récupérer à partir d’IoT Hub des informations sur les jumeaux d’appareil et les travaux.

* La rubrique [Prise en charge de MQTT au niveau d’IoT Hub](iot-hub-mqtt-support.md), qui fournit des informations supplémentaires sur la prise en charge du protocole MQTT par IoT Hub.

* [RFC 5246 – Le protocole TLS version 1.2](https://tools.ietf.org/html/rfc5246/) fournit plus d’informations sur l’authentification TLS.

* Pour plus d’informations sur l’authentification à l’aide de l’autorité de certification, consultez [Authentification des appareils à l’aide de certificats d’autorité de certification X.509](iot-hub-x509ca-overview.md)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment contrôler l’accès à IoT Hub, vous serez peut-être intéressé par les rubriques suivantes du Guide du développeur IoT Hub :

* [Utiliser des jumeaux d’appareil pour synchroniser les données d’état et de configuration](iot-hub-devguide-device-twins.md)
* [Appeler une méthode directe sur un appareil](iot-hub-devguide-direct-methods.md)
* [Planifier des travaux sur plusieurs appareils](iot-hub-devguide-jobs.md)

Si vous souhaitez tenter de mettre en pratique certains des concepts décrits dans cet article, consultez les didacticiels IoT Hub suivants :

* [Mise en route de Azure IoT Hub](quickstart-send-telemetry-node.md)
* [Envoyer des messages du cloud à votre appareil avec IoT Hub](iot-hub-csharp-csharp-c2d.md)
* [Configurer le routage des messages avec IoT Hub](tutorial-routing.md)