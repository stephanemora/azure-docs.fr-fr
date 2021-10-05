---
title: Points de terminaison de sécurité du service de provisionnement d’appareils Microsoft Azure IoT
description: 'Concepts : comment contrôler l’accès au service IoT Device Provisioning Service (DPS) pour les applications back-end. Inclut des informations sur les jetons de sécurité.'
author: anastasia-ms
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: v-stharr
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: cf2934c57441176034d28a7b60e33c639977e62d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124779594"
---
# <a name="control-access-to-azure-iot-hub-device-provisioning-service"></a>Contrôler l’accès au service de provisionnement d’appareils Azure IoT Hub

Cet article décrit les options disponibles permettant de sécuriser votre service de provisionnement des appareils IoT. Le service de provisionnement utilise une *authentification* et des *autorisations* pour accorder l’accès à chaque point de terminaison. Les autorisations permettent le processus d’authentification pour limiter l’accès à une instance de service en fonction des fonctionnalités.

Cet article aborde ce qui suit :

* Le processus d’authentification et les jetons utilisés par le service d’approvisionnement pour vérifier les autorisations sur les [API REST Service et de messagerie des appareils](/rest/api/iot-dps/).

* Les différentes autorisations que vous pouvez accorder à une application principale pour lui permettre d’accéder à l’API Service.

## <a name="authentication"></a>Authentification

L’API de messagerie des appareils prend en charge l’authentification des appareils basée sur les certificats basés sur les clés et X.509.  

L’API Service prend en charge l’authentification basée sur les clés pour les applications principales.  

Lors de l’utilisation basée sur les clés, le service de provisionnement d’appareils utilise des jetons de sécurité pour authentifier les services et éviter l’envoi de clés. En outre, la validité et la portée des jetons sont limitées dans le temps. Les kits SDK du service Azure IoT Device Provisioning génèrent automatiquement des jetons sans nécessiter de configuration particulière.  

Dans certains cas, vous pouvez avoir besoin d'utiliser les API REST HTTP Device Provisioning Service directement sans les SDK. Les sections suivantes décrivent comment s’authentifier directement sur les API REST.

## <a name="device-api-authentication"></a>Authentification avec l’API Service

L’[API de messagerie des appareils](/rest/api/iot-dps/device/runtime-registration) est utilisée par les appareils pour attester auprès du service Device Provisioning et recevoir une connexion IoT Hub.

>[!NOTE]
>Pour pouvoir recevoir une connexion authentifiée, les appareils doivent d’abord être inscrits auprès du service Device Provisioning par le biais d’une inscription. Utilisez l’API Service pour inscrire un appareil de façon programmatique par le biais d’une inscription.

Un appareil doit s’authentifier auprès de l’API de messagerie des appareils dans le cadre du processus de configuration. La méthode utilisée par un appareil pour l’authentification est définie lorsque vous configurez un groupe d’inscription ou une inscription individuelle. Quelle que soit la méthode d’authentification, l’appareil doit émettre une requête PUT HTTPS adjointe à l’URL suivante pour s’approvisionner lui-même.

```http
    https://global.azure-devices-provisioning.net/[ID_Scope]/registrations/[registration_id]/register?api-version=2021-06-01
```
Si vous utilisez l’authentification basée sur les clés, un jeton de sécurité est transmis à l’en-tête de demande HTTP **Authorization**  dans le format suivant :

```bash
    SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI} 
```

### <a name="security-token-structure-for-key-based-authentication"></a>Structure de jeton de sécurité pour l’authentification basée sur les clés

Le jeton de sécurité est transmis à l’en-tête de demande HTTP **Authorization**  dans le format suivant :

```bash
    SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI} 
```

Les valeurs attendues sont les suivantes :

| Valeur  | Description |
|:-------|:------------|
| `{signature}`  | Chaîne de signature HMAC-SHA256 de la forme : `{URL-encoded-resourceURI} + "\n" + expiry`. **Important** : la clé est décodée à partir de SHA256 et utilisée comme clé pour effectuer le calcul HMAC-base64. |
| `{expiry}`  | Chaînes UTF8 pour le nombre de secondes depuis l’époque 00:00:00 UTC 1er janvier 1970.  |
| `{URL-encoded-resourceURI}`   |Encodage d’URL en minuscules de `{ID_Scope}/registrations/{registration_id}` |
| `{policyName}`  | Pour l’API de messagerie des appareils, cette stratégie est toujours « inscription ». |

L'extrait Python suivant montre une fonction appelée  `generate_sas_token`  qui calcule le jeton à partir des entrées  `uri`, `key`, `policy_name`, `expiry` pour une inscription individuelle utilisant un type d'authentification à clé symétrique.

```python

from base64 import b64encode, b64decode, encode 
from hashlib import sha256 
from time import time 
from urllib.parse import quote_plus, urlencode 
from hmac import HMAC 

 def generate_sas_token(uri, key, policy_name, expiry=3600): 
    ttl = time() + expiry 
    sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl)) 
    signature = b64encode(HMAC(b64decode(key), sign_key.encode('utf-8'), sha256).digest()) 

    rawtoken = { 
        'sr' :  uri, 
        'sig': signature, 
        'se' : str(int(ttl)), 
        'skn' : policy_name 
    } 

    return 'SharedAccessSignature ' + urlencode(rawtoken) 

print(generate_sas_token("myIdScope/registrations/mydeviceregistrationid", "00mysymmetrickey", "registration"))

```

Le résultat doit ressembler à l'exemple suivant :

```bash

SharedAccessSignature sr=myIdScope%2Fregistrations%2Fmydeviceregistrationid&sig=SDpdbUNk%2F1DSjEpeb29BLVe6gRDZI7T41Y4BPsHHoUg%3D&se=1630175722&skn=registration 
```

L’exemple suivant montre comment la signature d’accès partagé est ensuite utilisée pour s’authentifier auprès de l’API de messagerie des appareils.  

```python

curl -L -i -X PUT -H 'Content-Type: application/json' -H 'Content-Encoding:  utf-8' -H 'Authorization: [token]' -d '{"registrationId": "[registration_id]"}' https://global.azure-devices-provisioning.net/[ID_Scope]/registrations/[registration_id]/register?api-version=2021-06-01 

```

Si vous utilisez un groupe d’inscription basé sur une clé symétrique, vous devez d’abord générer une clé `device symmetric` à l’aide de la clé de groupe d’inscription. Utilisez la clé primaire ou secondaire du groupe d’inscription pour calculer un HMAC-SHA256 de l’ID d’inscription de l’appareil. Le résultat est ensuite converti au format Base64 pour obtenir la clé de l’appareil dérivée. Pour afficher des exemples de code, consultez [Comment approvisionner des appareils à l’aide de groupes d’inscription avec des clés symétriques](how-to-legacy-device-symm-key.md?tabs=linux%22%20%5Cl%20%22derive-a-device-key). Une fois la clé symétrique de l’appareil déduite, vous pouvez inscrire l’appareil à l’aide des exemples précédents.

>[!WARNING]
>Pour éviter d’inclure la clé principale de groupe dans le code de votre appareil, le processus de déduction de la clé d’appareil doit être effectué à partir de l’appareil.

### <a name="certificate-based-authentication"></a>Authentification par certificat 

Si vous avez configuré un groupe d’inscription ou d’inscription individuel pour l’authentification basée sur un certificat X.509, l’appareil doit utiliser son certificat X.509 émis pour attester de l’API de messagerie des appareils. Reportez-vous aux articles suivants sur la configuration de l’inscription et la génération du certificat de l’appareil.

* Démarrage rapide – [Provisionner un appareil X.509 simulé auprès d’Azure IoT Hub à l’aide de Python](quick-create-simulated-device-x509-python.md?tabs=linux)

* Démarrage rapide – [Provisionner un appareil X.509 simulé auprès d’Azure IoT Hub à l’aide de Node.js](quick-create-simulated-device-x509-node.md)

* Démarrage rapide – [Inscrire des appareils X.509 auprès du service Azure Device Provisioning à l’aide de Python](quick-enroll-device-x509-python.md)

* Démarrage rapide – [Inscrire des appareils X.509 auprès du service Azure Device Provisioning à l’aide de Node.js](quick-enroll-device-x509-node.md)

Une fois l’inscription configurée et le certificat de l’appareil émis, l’exemple suivant montre comment s’authentifier auprès de l’API de messagerie des appareils avec le certificat X.509 de l’appareil.

```bash

curl -L -i -X PUT –cert ./[device_cert].pem –key ./[device_cert_private_key].pem -H 'Content-Type: application/json' -H 'Content-Encoding:  utf-8' -d '{"registrationId": "[registration_id]"}' https://global.azure-devices-provisioning.net/[ID_Scope]/registrations/[registration_id]/register?api-version=2021-06-01 

```

## <a name="service-api-authentication"></a>Authentification avec l’API Service

L’[API Service](/rest/api/iot-dps/service/device-registration-state) permet de récupérer l’état d’inscription et de supprimer les inscriptions d’appareils à distance. Le service est également utilisé par les applications principales pour gérer par programmation les [groupes individuels](/rest/api/iot-dps/service/individual-enrollment) et les [groupes d’inscriptions](/rest/api/iot-dps/service/enrollment-group). L’API Service prend en charge l’authentification basée sur les clés pour les applications principales.  

Pour accéder à tout point de terminaison API Service, vous devez disposer des autorisations appropriées. Par exemple, une application principale doit ajouter un jeton contenant des informations d’identification de sécurité à chaque message qu’elle envoie au service.

Le Service IoT Hub Device Provisioning accorde l’accès aux points de terminaison en vérifiant la validité du jeton par rapport aux stratégies d’accès partagé. Les informations d’identification de sécurité telles que les clés symétriques ne sont jamais envoyées sur le réseau.

### <a name="access-control-and-permissions"></a>Contrôle d’accès et autorisations

Vous pouvez accorder des [autorisations](#device-provisioning-service-permissions) de différentes manières :

* **Stratégies d’autorisation d’accès partagé**. Les stratégies d’accès partagé peuvent accorder n’importe quelle combinaison d’[autorisations](#device-provisioning-service-permissions). Vous pouvez définir des stratégies dans le [portail Azure][lnk-management-portal], ou programmatiquement avec des [API REST du service Device Provisioning][lnk-resource-provider-apis]. Tout service de provisionnement créé comporte la stratégie par défaut suivante :

* **provisioningserviceowner** : stratégie avec toutes les autorisations. Pour plus d’informations, consultez la page [Autorisations](#device-provisioning-service-permissions).

> [!NOTE]
> Le fournisseur de ressources du service de provisionnement d’appareils est sécurisé via votre abonnement Azure, comme le sont tous les fournisseurs, dans [Azure Resource Manager][lnk-azure-resource-manager].

Pour plus d’informations sur la façon de construire et d’utiliser les jetons de sécurité, consultez la section suivante.

HTTP est le seul protocole pris en charge. Il implémente l’authentification en incluant un jeton valide dans l’en-tête de demande d’**autorisation**.

#### <a name="example"></a>Exemple

```bash
SharedAccessSignature sr = 
   mydps.azure-devices-provisioning.net&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501&skn=provisioningserviceowner`\
```

> [!NOTE]
> Les [SDK du service de provisionnement d’appareils Azure IoT][lnk-sdks] génèrent automatiquement des jetons durant la connexion au service.

### <a name="security-tokens"></a>Jetons de sécurité

Le service de provisionnement d’appareils utilise des jetons de sécurité pour authentifier les services et éviter l’envoi de clés. En outre, la validité et la portée des jetons sont limitées dans le temps. Les [SDK du service de provisionnement d’appareils Azure IoT][lnk-sdks] génèrent automatiquement des jetons sans nécessiter de configuration particulière. Certains scénarios nécessitent toutefois que vous génériez et utilisiez directement des jetons de sécurité. Ces scénarios incluent l’utilisation directe du protocole HTTP.

### <a name="security-token-structure"></a>Structure du jeton de sécurité

Vous utilisez des jetons de sécurité pour permettre aux services d’accéder de manière limitée dans le temps à des caractéristiques spécifiques du service de provisionnement d’appareils IoT. Pour obtenir l’autorisation de se connecter au service de provisionnement, les services doivent envoyer des jetons de sécurité signés avec une clé d’accès partagé ou une clé symétrique.

Un jeton signé avec une clé d’accès partagé accorde un accès à toutes les fonctionnalités associées aux autorisations de stratégie d’accès partagé. 

Le jeton de sécurité présente le format suivant :

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Voici les valeurs attendues :

| Valeur | Description |
| --- | --- |
| {signature} |Une chaîne de signature HMAC-SHA256 sous la forme : `{URL-encoded-resourceURI} + "\n" + expiry`. **Important** : la clé est décodée à partir de base64 et utilisée comme clé pour effectuer le calcul HMAC-SHA256.|
| {expiry} |Chaînes UTF8 pour le nombre de secondes depuis l’époque 00:00:00 UTC 1er janvier 1970. |
| {URL-encoded-resourceURI} | Encodage de l’URL en minuscules à partir de l’URI de ressource en minuscules. Préfixe URI (par segment) des points de terminaison accessibles avec ce jeton, en commençant par le nom d’hôte du service de provisionnement d’appareils IoT (sans protocole). Par exemple : `mydps.azure-devices-provisioning.net`. |
| {policyName} |Le nom de la stratégie d’accès partagé à laquelle ce jeton fait référence. |

>[!NOTE]
>le préfixe URI est calculé par segment et non par caractère. Par exemple `/a/b` est un préfixe de `/a/b/c`, mais pas de `/a/bc`.

L’extrait de code Node.js suivant illustre une fonction appelée **generateSasToken** qui calcule le jeton à partir des entrées `resourceUri, signingKey, policyName, expiresInMins`. Les sections suivantes décrivent en détail comment initialiser les différentes entrées pour les différents cas d’utilisation des jetons.

```javascript
var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
    resourceUri = encodeURIComponent(resourceUri);

    // Set expiration in seconds
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    expires = Math.ceil(expires);
    var toSign = resourceUri + '\n' + expires;

    // Use crypto
    var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires + "&skn="+ policyName;
    return token;
};
```

À titre de comparaison, le code Python équivalent pour générer un jeton de sécurité est :

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import quote_plus, urlencode
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl))
    print sign_key
    signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl)),
        'skn' : policy_name
    }

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

> [!NOTE]
> Dans la mesure où la validité temporelle du jeton est vérifiée sur les machines du service de provisionnement d’appareils IoT, la dérive de l’horloge de la machine qui génère le jeton doit être minimale.

### <a name="use-security-tokens-from-service-components"></a>Utilisation de jetons de sécurité de composants du service

Les composants de service peuvent uniquement créer des jetons de sécurité utilisant des stratégies d’accès partagé pour accorder les autorisations adaptées, comme expliqué précédemment.

Voici les fonctions de service exposées sur les points de terminaison :

| Point de terminaison | Fonctionnalités |
| --- | --- |
| `{your-service}.azure-devices-provisioning.net/enrollments` |Fournit les opérations relatives à l’inscription d’appareils auprès du service de provisionnement d’appareils. |
| `{your-service}.azure-devices-provisioning.net/enrollmentGroups` |Fournit les opérations relatives à la gestion des groupes d’inscription d’appareils. |
| `{your-service}.azure-devices-provisioning.net/registrations/{id}` |Fournit les opérations relatives à la récupération et à la gestion de l’état des inscriptions d’appareils. |


Par exemple, un service généré à l’aide d’une stratégie d’accès partagé précréée appelée `enrollmentread` crée un jeton avec les paramètres suivants :

* URI de ressource : `{mydps}.azure-devices-provisioning.net`,
* clé de signature : une des clés de la stratégie `enrollmentread` ,
* nom de la stratégie : `enrollmentread`,
* n’importe quel délai d’expiration time.backn

![Créer une stratégie d’accès partagé pour votre instance de service de provisionnement des appareils sur le portail][img-add-shared-access-policy]

```javascript
var endpoint ="mydps.azure-devices-provisioning.net";
var policyName = 'enrollmentread'; 
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Le résultat, qui revient à accorder l’accès en lecture à tous les dossiers d’inscription, est le suivant :

`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=enrollmentread`

## <a name="reference-topics"></a>Rubriques de référence :

Les rubriques de référence suivantes fournissent des informations supplémentaires sur le contrôle de l’accès à votre service de provisionnement d’appareils IoT.

### <a name="device-provisioning-service-permissions"></a>Autorisations liées au service de provisionnement d’appareils

Le tableau suivant répertorie les autorisations qui vous permettent de contrôler l’accès à votre service de provisionnement d’appareils IoT.

| Autorisation | Notes |
| --- | --- |
| **ServiceConfig** |Accorde l’accès pour changer les configurations du service. <br/>Cette autorisation est utilisée par les services cloud principaux. |
| **EnrollmentRead** |Accorde l’accès en lecture aux inscriptions d’appareils et aux groupes d’inscription. <br/>Cette autorisation est utilisée par les services cloud principaux. |
| **EnrollmentWrite** |Accorde l’accès en écriture aux inscriptions d’appareils et aux groupes d’inscription. <br/>Cette autorisation est utilisée par les services cloud principaux. |
| **RegistrationStatusRead** |Accorde l’accès en lecture à l’état d’inscription de l’appareil. <br/>Cette autorisation est utilisée par les services cloud principaux. |
| **RegistrationStatusWrite**  |Accorde l’accès permettant de supprimer l’état d’inscription de l’appareil. <br/>Cette autorisation est utilisée par les services cloud principaux. |

<!-- links and images -->

[img-add-shared-access-policy]: ./media/how-to-control-access/how-to-add-shared-access-policy.PNG
[lnk-sdks]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-management-portal]: https://portal.azure.com
[lnk-azure-resource-manager]: ../azure-resource-manager/management/overview.md
[lnk-resource-provider-apis]: /rest/api/iot-dps/