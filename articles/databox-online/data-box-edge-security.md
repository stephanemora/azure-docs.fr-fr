---
title: Sécurité de périphérie de zone de données Azure | Microsoft Docs
description: Décrit les fonctionnalités de sécurité et de confidentialité qui protègent votre zone de données Azure Edge appareils, de service et de données en local et dans le cloud.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 8823aebe17a5446b3c507878833c2525c338dde1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60756177"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Protection des données et de sécurité Edge de zone de données Azure

Sécurité est une préoccupation majeure lorsque vous êtes adoptant une nouvelle technologie, surtout si cette technologie est utilisée avec des données confidentielles ou propriétaires. Azure Edge de zone de données vous aide à vous assurer que seuls les autorisés entités peuvent afficher, modifier ou supprimer vos données.

Cet article décrit les fonctionnalités de sécurité de périphérie de zone de données qui permettent de protéger de chacun des composants de la solution et les données stockées dans les.

Edge de zone de données Azure se compose de quatre composants principaux qui interagissent entre eux :

- **Service de zone de données, hébergé dans Azure**. La ressource de gestion que vous utilisez pour créer l’ordre de l’appareil, configurez l’appareil, puis suivre l’ordre jusqu'à la fin.
- **L’unité de bord de la zone données**. L’appareil de transfert est fournie pour vous afin de pouvoir importer vos données locales dans Azure.
- **Les clients/hôtes connectés à l’appareil**. Les clients dans votre infrastructure qui se connectent à l’appareil Edge de zone de données et contiennent des données qui doivent être protégées.
- **Stockage cloud**. L’emplacement dans la plateforme cloud Azure où les données sont stockées. Cet emplacement est généralement le compte de stockage lié à la ressource de données boîte Edge que vous créez.

## <a name="data-box-edge-service-protection"></a>Protection de service de données boîte Edge

Le service de données boîte Edge est un service de gestion qui est hébergé dans Azure. Le service est utilisé pour configurer et gérer l’appareil.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>Protection des appareils Edge de la zone données

Le périphérique périmétrique de zone de données est un périphérique local qui permet de transformer vos données par le traitement de localement, puis l’envoyer vers Azure. Votre appareil :

- A besoin d’une clé d’activation pour accéder au service de données boîte Edge.
- Est protégé à tout moment par un mot de passe du périphérique.
- Est un appareil verrouillé. Le périphérique BMC et BIOS sont protégés par mot de passe. Le BIOS est protégé par un utilisateur-accès limité.
- Le démarrage sécurisé est activé.
- Exécute Windows Defender Device Guard. Device Guard permet d’exécuter uniquement les applications de confiance que vous définissez dans vos stratégies d’intégrité du code.

### <a name="protect-the-device-via-activation-key"></a>Protéger l’appareil via la clé d’activation

Seul un appareil Edge de zone de données autorisé est autorisé à rejoindre le service de données boîte Edge que vous créez dans votre abonnement Azure. Pour autoriser un appareil, vous devez utiliser une clé d’activation pour activer le périphérique avec le service de données boîte Edge.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Pour plus d’informations, consultez [obtenir une clé d’activation](data-box-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Protéger l’appareil via le mot de passe

Les mots de passe vous assurer que seuls les utilisateurs autorisés peuvent accéder à vos données. Données zone appareils démarrage dans un état verrouillé.

Vous pouvez :

- Se connecter à l’interface utilisateur de l’appareil via un navigateur de web local, puis indiquez un mot de passe pour vous connecter à l’appareil.
- Se connecter à distance à l’interface PowerShell de périphérique sur HTTP. Gestion à distance est activée par défaut. Vous pouvez ensuite fournir le mot de passe pour vous connecter à l’appareil. Pour plus d’informations, consultez [connexion à distance à votre appareil Edge de zone de données](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Utilisez local web UI à [modifier le mot de passe](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access). Si vous modifiez le mot de passe, veillez à informer tous les utilisateurs de l’accès à distance afin qu’ils n’aient des problèmes de connexion.

## <a name="protect-your-data"></a>Protéger les données

Cette section décrit les fonctionnalités de sécurité de périphérie de zone de données qui protègent les données en transit et stockées.

### <a name="protect-data-at-rest"></a>Protection des données au repos

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Protéger les données en vol

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Protection des données par les comptes de stockage

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Faire pivoter, puis [synchroniser vos clés de compte de stockage](data-box-edge-manage-shares.md#sync-storage-keys) régulièrement pour vous aider à protéger votre compte de stockage des utilisateurs non autorisés.

## <a name="manage-personal-information"></a>Gérer les informations personnelles

Le service de données boîte Edge collecte des informations personnelles dans les scénarios suivants :

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Pour afficher la liste des utilisateurs qui peuvent accéder ou supprimer un partage, suivez les étapes de [gérer des partages sur le bord de la zone de données](data-box-edge-manage-shares.md).

Pour plus d’informations, consultez la politique de confidentialité de Microsoft sur le [centre de confidentialité](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Étapes suivantes

[Déployer votre appareil Edge de zone de données](data-box-edge-deploy-prep.md)
