---
title: Sécurité de périphérie de zone de données Azure | Microsoft Docs
description: Décrit les fonctionnalités de sécurité et de confidentialité qui protègent vos appareils Edge de zone de données Azure, service et de données en local et dans le cloud.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 5316ddf9d456731f2789241434926366f732993a
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682098"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Protection des données et de sécurité Edge de zone de données Azure

Sécurité est une préoccupation majeure lors de l’adoption d’une nouvelle technologie, surtout si cette technologie est utilisée avec des données confidentielles ou propriétaires. Solution Microsoft Azure données boîte Edge garantit que seules les entités autorisées peuvent afficher, modifier ou supprimer vos données.

Cet article décrit les fonctionnalités de sécurité Edge de zone de données qui permettent de protéger de chacun des composants de la solution et les données stockées sur ces derniers.

La solution Edge de zone de données Azure se compose de quatre composants principaux qui interagissent entre eux :

- **Service de zone de données hébergé dans Azure** – la ressource de gestion qui vous permet de créer l’ordre de l’appareil, configurer l’appareil et puis suivre l’ordre jusqu'à la fin.
- **L’unité de bord de la zone données** – l’appareil de transfert est livré à importer vos données locales dans Azure.
- **Les clients/hôtes connectés à l’appareil** – les clients dans votre infrastructure qui se connectent à l’appareil Edge de zone de données et contiennent des données qui doivent être protégées.
- **Le stockage cloud** : emplacement dans le cloud Azure où les données sont stockées. Cet emplacement est généralement lié à la ressource de données boîte Edge que vous avez créé le compte de stockage.

## <a name="data-box-edge-service-protection"></a>Protection de service de données boîte Edge

Le service de données boîte Edge est un service de gestion hébergé dans Microsoft Azure. Le service est utilisé pour configurer et gérer l’appareil.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>Protection des appareils Edge de la zone données

Le périphérique périmétrique de zone de données est un périphérique local qui permet de transformer les données par le traitement de localement, puis l’envoyer vers Azure. Votre appareil :

- A besoin d’une clé d’activation pour accéder au service de données boîte Edge.
- Est protégé à tout moment par un mot de passe du périphérique.
- Est un appareil verrouillé. Le périphérique BMC et BIOS sont protégés par mot de passe avec un utilisateur-accès limité pour le BIOS.
- Le démarrage sécurisé est activé.
- Exécute Windows Defender Device Guard. Device Guard vous autorise à exécuter uniquement les applications de confiance que vous définissez dans vos stratégies d’intégrité du code.

### <a name="protect-the-device-via-activation-key"></a>Protéger l’appareil via la clé d’activation

Seul un appareil Edge de zone de données autorisé est autorisé à rejoindre le service de données boîte Edge que vous avez créé dans votre abonnement Azure. Pour autoriser un appareil, vous devez utiliser une clé d’activation pour activer le périphérique avec le service de données boîte Edge.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Pour plus d’informations, accédez à [obtenir une clé d’activation](data-box-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Protéger l’appareil via le mot de passe

Les mots de passe vous assurer que vos données sont accessibles aux seuls utilisateurs autorisés. Données zone appareils démarrage dans un état verrouillé.

Vous pouvez :

- Se connecter à l’interface utilisateur de l’appareil via un navigateur de web local, puis indiquez un mot de passe pour se connecter à l’appareil.
- Se connecter à distance à l’interface PowerShell de périphérique sur HTTP. Gestion à distance est activée par défaut. Vous pouvez ensuite fournir le mot de passe pour se connecter à l’appareil. Pour plus d’informations, accédez à [connexion à distance à votre appareil Edge de zone de données](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Utilisez local web UI à [modifier le mot de passe](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access). Si vous modifiez le mot de passe, veillez à informer tous les utilisateurs de l’accès à distance afin qu’ils ne connaissent pas un échec de connexion.

## <a name="protect-the-data"></a>Protéger les données

Cette section décrit les fonctionnalités de sécurité de périphérie de zone de données qui protègent les données en transit et les données stockées.

### <a name="protect-data-at-rest"></a>Protection des données au repos

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Protéger les données en vol

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Protection des données par les comptes de stockage

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Faire pivoter, puis [synchroniser vos clés de compte de stockage](data-box-edge-manage-shares.md#sync-storage-keys) régulièrement afin de garantir que votre compte de stockage n’est pas accessible par les utilisateurs non autorisés.

## <a name="manage-personal-information"></a>Gérer les informations personnelles

Le service de données boîte Edge collecte des informations personnelles dans les instances de la clé suivantes :

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Pour afficher la liste des utilisateurs qui peuvent accéder ou supprimer un partage, suivez les étapes de [gérer des partages sur le bord de la zone de données](data-box-edge-manage-shares.md).

Pour plus d’informations, consultez la [Politique de confidentialité Microsoft sur le Centre de gestion de la confidentialité](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Étapes suivantes

[Déployer votre appareil Edge de zone de données](data-box-edge-deploy-prep.md).
