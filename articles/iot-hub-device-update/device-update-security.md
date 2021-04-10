---
title: Sécurité du service Device Update pour Azure IoT Hub | Microsoft Docs
description: Découvrez comment Device Update pour IoT Hub assure une mise à jour sûre des appareils.
author: lichris
ms.author: lichris
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub
ms.openlocfilehash: 86b2dbe6a28d1440f93788eb40e133d9b62d3f0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102489427"
---
# <a name="device-update-security-model"></a>Modèle de sécurité de Device Update

Device Update pour IoT Hub offre une méthode sécurisée pour déployer des mises à jour de microprogrammes, images et applications sur vos appareils IoT. Le workflow offre un canal sécurisé de bout en bout avec un modèle de chaîne de responsabilité complet permettant à l’appareil de s’assurer qu’une mise à jour est approuvée, non modifiée et intentionnelle.

Chaque étape du workflow Device Update est protégée par le biais de divers processus et fonctionnalités de sécurité pour garantir que les transferts entre chaque étape du pipeline sont sécurisés. Le client Device Update identifie et gère correctement toutes les demandes de mise à jour illégitimes. Le client vérifie également chaque téléchargement pour s’assurer que le contenu est approuvé, non modifié et qu’il est intentionnel.

## <a name="for-solution-operators"></a>Pour les opérateurs de solution

Quand les opérateurs de solution importent des mises à jour dans leur instance Device Update, le service charge et vérifie les fichiers binaires de mise à jour pour s’assurer qu’ils n’ont pas été modifiés ou remplacés par un utilisateur malveillant. Après cette vérification, le service Device Update génère un [manifeste de mise à jour](./update-manifest.md) interne avec des codes de hachage de fichier à partir du manifeste d’importation et d’autres métadonnées. Ce manifeste de mise à jour est ensuite signé par le service Device Update.

Quand l’opérateur de solution demande la mise à jour d’un appareil, un message signé est envoyé à l’appareil sur le canal IoT Hub protégé. L’agent Device Update de l’appareil vérifie que la signature de la demande est authentique. 

Tout téléchargement de fichier binaire qui en résulte est sécurisé par vérification de la signature du manifeste de mise à jour. Le manifeste de mise à jour contient les codes de hachage de fichiers binaires. Ainsi, quand le manifeste est approuvé, l’agent Device Update approuve les codes de hachage et les met en correspondance avec les fichiers binaires. Quand le fichier binaire de mise à jour a été téléchargé et vérifié, il est transféré au programme d’installation sur l’appareil.

## <a name="for-device-builders"></a>Pour les fabricants d’appareils

Pour garantir que le service Device Update effectue un scale-down pour adapter les capacités aux appareils simples aux performances faibles, le modèle de sécurité utilise des clés asymétriques brutes et des signatures brutes. Elles utilisent des formats JSON, par exemple des jetons JSON Web Token et des clés web JSON.

### <a name="securing-update-content-via-the-update-manifest"></a>Sécurisation du contenu des mises à jour avec le manifeste de mise à jour

Le manifeste de mise à jour est vérifié à l’aide de deux signatures. Les signatures sont créées avec une structure associant clés de *signature* et clés *racine*.

L’agent Device Update a des clés publiques incorporées qui sont utilisées pour tous les appareils compatibles avec Device Update. Il s’agit des clés *racine*. Les clés privées correspondantes sont contrôlées par Microsoft.

Microsoft génère également une paire de clés publique/privée qui n’est pas incluse dans l’agent Device Update ni stockée sur l’appareil. Il s’agit de la clé de *signature*.

Quand une mise à jour est importée dans le service Device Update pour IoT Hub et que le manifeste de mise à jour est généré par le service, le service signe le manifeste à l’aide de la clé de signature et inclut la clé de signature elle-même, signée par une clé racine. Quand le manifeste de mise à jour est envoyé à l’appareil, l’agent Device Update reçoit les données de signature suivantes :

1. La valeur de signature elle-même.
2. L’algorithme utilisé pour générer #1.
3. Les informations de clé publique de la clé de signature utilisées pour générer #1.
4. La signature de la clé de signature publique dans #3.
5. L’ID de clé publique de la clé racine utilisé pour générer #3.
6. L’algorithme utilisé pour générer #4.

L’agent Device Update utilise les informations définies ci-dessus pour vérifier que la clé de signature publique est signée par la clé racine. Ensuite, l’agent Device Update vérifie que le manifeste de mise à jour est signé par la clé de signature. Si toutes les signatures sont correctes, le manifeste de mise à jour est approuvé par l’agent Device Update. Étant donné que le manifeste de mise à jour comprend les codes de hachage de fichier correspondant aux fichiers de mise à jour eux-mêmes, les fichiers de mise à jour peuvent également être approuvés si ces codes correspondent.

L’utilisation de clés racine et de signature permet à Microsoft de déployer périodiquement la clé de signature, ce qui s’inscrit dans les bonnes pratiques de sécurité.

### <a name="json-web-signature-jws"></a>Signature web JSON (JWS)

La `updateManifestSignature` est utilisée pour vérifier que les informations contenues dans le `updateManifest` n’ont pas été falsifiées. La `updateManifestSignature` est générée à l’aide d’une signature web JSON avec des clés web JSON, ce qui permet de vérifier la source. La signature est une chaîne encodée en Base64Url avec trois sections délimitées par « . ».  Reportez-vous aux [méthodes d’assistance jws_util.h](https://github.com/Azure/iot-hub-device-update/tree/main/src/utils/jws_utils) pour analyser et vérifier les clés et jetons JSON.

La signature web JSON est une [norme proposée par l’IETF](https://tools.ietf.org/html/rfc7515) largement utilisée pour la signature de contenu à l’aide de structures de données JSON. Elle offre un moyen de garantir l’intégrité des données en vérifiant la signature des données. Pour plus d’informations, consultez le document [RFC 7515](https://www.rfc-editor.org/info/rfc7515) sur la signature web JSON (JWS).

### <a name="json-web-token"></a>JSON Web Token

Les jetons JSON Web Token offrent une méthode [standard](https://tools.ietf.org/html/rfc7519) ouverte pour représenter les revendications de manière sécurisée entre deux parties.

### <a name="root-keys"></a>Clés racine

Chaque appareil Device Update contient un ensemble de clés racine. Ces clés sont la racine de confiance pour toutes les signatures de Device Update. Toute signature doit être chaînée par le biais de l’une de ces clés racine pour être considérée comme légitime.

L’ensemble de clés racine change au fil du temps. En effet, la rotation périodique des clés de signature est une bonne pratique de sécurité. Ainsi, le logiciel de l’agent Device Update devra se mettre à jour avec le dernier ensemble de clés racine. 

### <a name="signatures"></a>Signatures

Toutes les signatures s’appuient sur une clé de signature (publique) signée par l’une des clés racine. La signature identifie la clé racine qui a été utilisée pour signer la clé de signature. 

Un agent Device Update doit vérifier les signatures en commençant par vérifier que la signature de la clé de signature (publique) est correcte et valide et que cette clé est signée par l’une des clés racine approuvées. Quand la clé de signature a été vérifiée, la signature elle-même peut être vérifiée à l’aide de la clé publique de signature désormais approuvée.

La cadence de rotation des clés de signature est bien supérieure à celle des clés racine. Vous devez donc vous attendre à ce que les messages soient signés par différentes clés de signature. 

La révocation d’une clé de signature est gérée par le service Device Update. Les utilisateurs ne doivent donc pas tenter de mettre en cache les clés de signature. Utilisez toujours la clé de signature qui accompagne une signature.

### <a name="receiving-updates"></a>Réception des mises à jour

Les demandes de mise à jour reçues par un agent Device Update contiennent un document de manifeste de mise à jour signé. L’agent doit vérifier que la signature du manifeste de mise à jour est correcte et intacte. Pour cela, il vérifie que la clé de signature de la signature du manifeste de mise à jour a été signée par une clé racine appropriée. Ensuite, l’agent vérifie la signature du manifeste de mise à jour par rapport à la clé de signature.

Une fois la signature de ce document vérifiée, l’agent Device Update peut l’approuver comme « source de vérité ». Toutes les autres approbations de sécurité proviennent de cette source. 

Le manifeste de mise à jour contient les URL et les codes de hachage de fichier du contenu à télécharger et installer. Quand l’agent a téléchargé un fichier binaire de mise à jour, il doit vérifier la mise à jour par rapport au code de hachage de fichier trouvé dans le manifeste de mise à jour. Ceci offre un modèle d’approbation transitive pour la vérification du téléchargement. Ce modèle garantit non seulement que le contenu est intact (non modifié), mais également que ce qui a été téléchargé est bien ce qui était prévu. 

### <a name="securing-the-device"></a>Sécurisation de l’appareil

Il est important de s’assurer que les ressources de sécurité liées au service Device Update sont correctement sécurisées et protégées sur votre appareil. Les ressources telles que les clés racine doivent être protégées contre toute modification. Il existe plusieurs façons de procéder. Par exemple, vous pouvez utiliser des dispositifs de sécurité (TPM, SGX, HSM, etc.) ou même les coder de manière irréversible dans l’agent Device Update. Dans ce dernier cas, le code de l’agent Device Update doit être signé numériquement et la prise en charge de l’intégrité du code du système doit être activée pour empêcher toute modification du code de l’agent par des utilisateurs malveillants.

Des mesures de sécurité supplémentaires peuvent être justifiées. Vous pouvez notamment vous assurer qu’un transfert d’un composant à un autre est effectué de façon sécurisée. Vous pouvez, par exemple, inscrire un compte isolé spécifique pour exécuter les différents composants. Vous pouvez également limiter les communications réseau (par exemple, les appels d’API REST) à localhost uniquement.

**[Étape suivante : Apprenez-en davantage sur la façon dont le service Device Update utilise Azure RBAC](.\device-update-control-access.md)**