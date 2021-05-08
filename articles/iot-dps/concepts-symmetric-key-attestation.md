---
title: Service Azure IoT Hub Device Provisioning - Attestation de clé symétrique
description: Cet article donne une vue d’ensemble conceptuelle des attestations de clé symétrique avec le service IoT Device Provisioning (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 04/23/2021
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: devx-track-csharp
ms.openlocfilehash: 0455fe634b44465b4b16d48145fcf51f733f121d
ms.sourcegitcommit: bd1a4e4df613ff24e954eb3876aebff533b317ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2021
ms.locfileid: "107929360"
---
# <a name="symmetric-key-attestation"></a>Attestation de clé symétrique

Cet article décrit le processus d’attestation d’identité lors de l’utilisation de clés symétriques avec le service Device Provisioning. 

L’attestation de clé symétrique est une approche simple pour authentifier un appareil avec une instance du service Device Provisioning. Cette méthode d’attestation représente une expérience « Hello world » pour les développeurs qui découvrent le provisionnement d’appareils ou n’ont pas d’exigences de sécurité strictes. L’attestation d’appareil avec un [Module de plateforme sécurisée (TPM)](concepts-tpm-attestation.md) ou un [certificat X.509](concepts-x509-attestation.md) est plus sécurisée, et elle doit être utilisée là où les exigences de sécurité sont plus strictes.

Les inscriptions de clés symétriques constituent aussi un excellent moyen de démarrer sur le cloud via Azure IoT pour les appareils hérités sont les fonctionnalités de sécurité sont limitées. Pour plus d’informations sur l’attestation de clé symétrique avec des appareils hérités, consultez [Guide pratique pour utiliser des clés symétriques avec des appareils hérités](how-to-legacy-device-symm-key.md).


## <a name="symmetric-key-creation"></a>Création d’une clé symétrique

Par défaut, le service Device Provisioning crée des clés symétriques avec une longueur par défaut de 64 octets quand de nouvelles inscriptions sont enregistrées avec l’option **Générer automatiquement les clés** activée.

![Générer automatiquement des clés symétriques](./media/concepts-symmetric-key-attestation/auto-generate-keys.png)

Vous pouvez également fournir vos propres clés symétriques pour les inscriptions en désactivant cette option. Quand vous spécifiez vos propres clés symétriques, vos clés doivent avoir une longueur de clé comprise entre 16 et 64 octets. En outre, les clés symétriques doivent être fournies dans un format Base64 valide.



## <a name="detailed-attestation-process"></a>Processus d’attestation détaillé

L’attestation de clé symétrique avec le service Device Provisioning est effectuée en utilisant les mêmes [jetons de sécurité](../iot-hub/iot-hub-devguide-security.md#security-token-structure) que ceux pris en charge par les hubs IoT pour identifier les appareils. Ces jetons de sécurité sont [des jetons de signature d’accès partagé (SAP)](../service-bus-messaging/service-bus-sas.md). 

Les jetons SAP ont une *signature* hachée créée en utilisant la clé symétrique. La signature est recréée par le service Device Provisioning pour vérifier si un jeton de sécurité présenté lors de l’attestation est authentique ou non.

Les jetons SAP ont le format suivant :

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Voici les composants de chaque jeton :

| Valeur | Description |
| --- | --- |
| {signature} |Chaîne de signature HMAC-SHA256. Pour les inscriptions individuelles, cette signature est produite avec la clé symétrique (principale ou secondaire) pour effectuer le hachage. Pour les groupes d’inscription, une clé dérivée de la clé d’inscription du groupe est utilisée pour effectuer le hachage. Le hachage est effectué sur un message de la forme : `URL-encoded-resourceURI + "\n" + expiry`. **Important** : La clé doit être décodée depuis base64 avec d’être utilisée pour effectuer le calcul HMAC-SHA256. En outre, le résultat de la signature doit être encodé sous forme d’URL. |
| {resourceURI} |URI du point de terminaison d’inscription qui est accessible avec ce jeton, commençant par l’ID d’étendue pour l’instance du service Device Provisioning. Par exemple : `{Scope ID}/registrations/{Registration ID}` |
| {expiry} |Chaînes UTF8 pour le nombre de secondes depuis l’époque 00:00:00 UTC 1er janvier 1970. |
| {URL-encoded-resourceURI} |Encodage en URL minuscules de l’URI de ressource en minuscules |
| {policyName} |Le nom de la stratégie d’accès partagé à laquelle ce jeton fait référence. Le nom de la stratégie utilisé lors du provisionnement avec l’attestation de clé symétrique est **inscription**. |

Quand un appareil atteste avec une inscription individuelle, il utilise la clé symétrique définie dans l’entrée d’inscription individuelle pour créer la signature hachée pour le jeton SAP.

Pour obtenir des exemples de code qui créent un jeton SAP, consultez [Jetons de sécurité](../iot-hub/iot-hub-devguide-security.md#security-token-structure).

La création de jetons de sécurité pour l’attestation de clé symétrique est prise en charge par le SDK C d’Azure IoT. Pour obtenir un exemple utilisant le SDK C d’Azure IoT pour attester avec une inscription individuelle, consultez [Provisionner un appareil simulé avec des clés symétriques](quick-create-simulated-device-symm-key.md).


## <a name="group-enrollments"></a>Inscriptions de groupe

Les clés symétriques pour les inscriptions de groupe ne sont pas utilisées directement par les appareils lors du provisionnement. Au lieu de cela, les appareils qui appartiennent à un groupe d’inscription provisionnent avec une clé d’appareil dérivée. 

Pour commencer, un ID d’inscription unique est défini pour chaque appareil attestant avec un groupe d’inscription. Les caractères valides pour l’ID d’inscription sont des caractères alphanumériques minuscules et des tirets («- »). Cet ID d’inscription doit être quelque chose d’unique qui identifie l’appareil. Par exemple, un appareil hérité peut ne pas prendre en charge beaucoup de fonctionnalités de sécurité. Seule une adresse MAC ou un numéro de série peut être disponible pour identifier l’appareil hérité. Dans ce cas, un ID d’inscription peut être composé de l’adresse MAC et du numéro de série, d’une façon similaire à ceci :

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Cet exemple est utilisé tel quel dans l’article [Guide pratique pour provisionner des appareils hérités avec des clés symétriques](how-to-legacy-device-symm-key.md).

Une fois qu’un ID d’inscription a été défini pour l’appareil, la clé symétrique pour le groupe d’inscription est utilisée pour calculer un hachage [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) de l’ID d’inscription, pour produire une clé d’appareil dérivée. Des exemples d’approches pour le calcul de la clé dérivée de l’appareil sont fournis dans les onglets ci-dessous.  


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

L’extension IoT pour Azure CLI fournit la commande [`compute-device-key`](/cli/azure/iot/dps?view=azure-cli-latest&preserve-view=true#az_iot_dps_compute_device_key) permettant de générer des clés d’appareil dérivées. Cette commande peut être utilisée à partir d’un système Windows ou Linux, dans PowerShell ou un interpréteur de commandes Bash.

Remplacez la valeur de l’argument `--key` par la **clé primaire** de votre groupe d’inscription.

Remplacez la valeur de l’argument `--registration-id` par votre ID d’inscription.

```azurecli
az iot dps compute-device-key --key 8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw== --registration-id sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Exemple de résultat :

```azurecli
"Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc="
```

# <a name="windows"></a>[Windows](#tab/windows)

Si vous utilisez une station de travail Windows, utilisez PowerShell pour générer votre clé d’appareil dérivée, comme indiqué dans l’exemple suivant.

Remplacez la valeur de l’argument **KEY** par la **clé primaire** de votre groupe d’inscription.

Remplacez la valeur de **REG_ID** avec votre ID d’inscription.

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

# <a name="linux"></a>[Linux](#tab/linux)

Si vous utilisez une station de travail Linux, vous pouvez utiliser openssl pour générer votre clé d’appareil dérivée, comme indiqué dans l’exemple suivant.

Remplacez la valeur de l’argument **KEY** par la **clé primaire** de votre groupe d’inscription.

Remplacez la valeur de **REG_ID** avec votre ID d’inscription.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

# <a name="csharp"></a>[CSharp](#tab/csharp)

Le hachage de l’ID d’inscription peut être effectué avec le code C# suivant :

```csharp
using System; 
using System.Security.Cryptography; 
using System.Text;  

public static class Utils 
{ 
    public static string ComputeDerivedSymmetricKey(byte[] masterKey, string registrationId) 
    { 
        using (var hmac = new HMACSHA256(masterKey)) 
        { 
            return Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(registrationId))); 
        } 
    } 
} 
```

```csharp
String deviceKey = Utils.ComputeDerivedSymmetricKey(Convert.FromBase64String(masterKey), registrationId);
```

---

La clé d’appareil résultante est ensuite utilisée pour générer un jeton SAP à utiliser pour l’attestation. Chaque appareil d’un groupe d’inscription doit attester avec un jeton de sécurité généré à partir d’une clé dérivée unique. La clé symétrique du groupe d’inscription ne peut pas être utilisée directement pour l’attestation.

#### <a name="installation-of-the-derived-device-key"></a>Installation de la clé d’appareil dérivée

Idéalement, les clés d’appareil sont dérivées et installées en usine. Cette méthode garantit que la clé du groupe n’est jamais incluse dans aucun logiciel déployé sur l’appareil. Quand une adresse MAC ou un numéro de série est affecté à l’appareil, la clé peut être dérivée et injectée dans l’appareil, quelle que soit la façon dont le fabricant choisit de la stocker.

Considérez le diagramme suivant, qui montre un tableau de clés d’appareil générées dans une fabrique en hachant chaque ID d’inscription d’appareil avec la clé d’inscription du groupe (**K**). 

![Clés d’appareil affectées en usine](./media/concepts-symmetric-key-attestation/key-diversification.png)

L’identité de chaque appareil est représentée par l’ID d’inscription et par la clé d’appareil dérivée qui est installée en usine. La clé d’appareil n’est jamais copiée vers un autre emplacement et la clé du groupe n’est jamais stockée sur un appareil.

Si les clés d’appareil ne sont pas installés en usine, un [module de sécurité matériel (HSM)](concepts-service.md#hardware-security-module) doit être utilisé pour stocker l’identité de l’appareil de façon sécurisée.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez une meilleure compréhension de l’attestation de clé symétrique, consultez les articles suivants pour découvrir plus d’informations :

* [Démarrage rapide : provisionner un appareil simulé avec des clés symétriques](quick-create-simulated-device-symm-key.md)
* [Découvrir plus d’informations sur les concepts du provisionnement](about-iot-dps.md#provisioning-process)
* [Bien démarrer avec le provisionnement automatique](./quick-setup-auto-provision.md) 
