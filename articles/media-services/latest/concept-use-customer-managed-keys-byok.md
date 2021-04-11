---
title: Utiliser des clés BYOK (clé gérées par le client)
description: Vous pouvez utiliser des clés gérées par le client (autrement dit apporter votre propre clé) avec Media Services.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: bc7b1a0742e79c3c84bf533e75467b5def2706b4
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277723"
---
# <a name="bring-your-own-key-customer-managed-keys-with-media-services"></a>Utiliser des clés BYOK (clé gérées par le client) avec Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Bring Your Own Key (BYOK) est une initiative à l’échelle d’Azure qui aide les clients à déplacer leurs charges de travail vers le cloud. Les clés gérées par le client permettent aux clients d’adhérer aux réglementations du secteur en matière de conformité et d’améliorer l’isolation des locataires d’un service. Le fait de donner le contrôle des clés de chiffrement aux clients est un moyen de réduire l’accès et le contrôle inutiles et d’améliorer la confiance dans les services Microsoft.

## <a name="keys-and-key-management"></a>Clés et gestion des clés

Vous pouvez utiliser votre propre clé avec Media Services lorsque vous utilisez l’API Media Services 2020-05-01. Une clé de compte par défaut est créée pour tous les comptes chiffrés par une clé système appartenant à Media Services. Lorsque vous utilisez votre propre clé, la clé de compte est chiffrée avec votre clé. Les clés de contenu sont chiffrées par la clé de compte. Les URL JobInputHttp et les clés de validation de jeton symétriques sont également chiffrées.

:::image type="content" source="./media/customer-managed-key/customer-managed-key.svg" alt-text="Une clé gérée par le client remplace une clé gérée par le système":::

Media Services utilise l’identité managée du compte Media Services pour lire votre clé à partir du Key Vault dont vous êtes propriétaire. Media Services exige que le Key Vault se trouve dans la même région que le compte, et que les options de suppression réversible et de protection contre la suppression définitive soient activées.

Votre clé peut être une clé RSA 2048, 3072 ou 4096, et les clés HSM et logicielles sont toutes deux prises en charge.

> [!NOTE]
> Les clés EC ne sont pas prises en charge.

Vous pouvez spécifier un nom et une version de clé, ou simplement un nom de clé. Si vous utilisez seulement un nom de clé, Media Services utilise la dernière version de la clé. Les nouvelles versions de clés de client sont détectées automatiquement et la clé de compte est à nouveau chiffrée.

> [!WARNING]
> Media Services surveille l’accès à la clé de client. Si la clé de client devient inaccessible (par exemple, si elle-même, le Key Vault ou l’autorisation d’accès est supprimé), Media Services définit l’état de la clé de client du compte sur Inaccessible (ce qui a pour effet de désactiver le compte). Le compte peut toutefois être supprimé même dans cet état. Les seules opérations prises en charge sont les opérations d’extraction (GET), de liste (LIST) et de suppression (DELETE) de compte. Toutes les autres requêtes (encodage, diffusion en continu, etc.) échouent tant que l’accès à la clé du compte n’est pas restauré.

## <a name="double-encryption"></a>Double chiffrement

Media Services prend automatiquement en charge le chiffrement double. Pour les données au repos, la première couche de chiffrement utilise une clé gérée par le client ou une clé gérée par Microsoft en fonction du paramètre `AccountEncryption` sur le compte.  La deuxième couche de chiffrement des données au repos est fournie automatiquement à l’aide d’une clé gérée par Microsoft distincte. Pour en savoir plus sur le double chiffrement, consultez [Double chiffrement Azure](../../security/fundamentals/double-encryption.md).

> [!NOTE]
> Le chiffrement double est activé automatiquement sur le compte Media Services. Toutefois, vous devez configurer séparément la clé gérée par le client et le chiffrement double sur votre compte de stockage. Voir [Chiffrement du stockage](../../storage/common/storage-service-encryption.md).

## <a name="tutorials"></a>Tutoriels

- [Utiliser le portail Azure pour utiliser des clés managées par le client ou BYOK avec la plateforme Media Services](security-customer-managed-keys-portal-tutorial.md)
- [Utiliser des clés managées par le client ou BYOK avec l’API REST Media Services](security-customer-managed-keys-rest-postman-tutorial.md).

## <a name="next-steps"></a>Étapes suivantes

[Protéger votre contenu à l’aide du chiffrement dynamique de Media Services](drm-content-protection-concept.md)