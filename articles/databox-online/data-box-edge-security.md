---
title: Limites de la sécurité d’Azure Data Box Edge | Microsoft Docs
description: Décrit les fonctionnalités de sécurité et de confidentialité qui protègent votre service, votre appareil et vos données Azure Data Box Edge en local et dans le cloud.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 375576dd4a7897c48474fd2af00a99084292d854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "69970882"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Protection des données et sécurité Azure Data Box Edge

La sécurité est une préoccupation majeure pour toute nouvelle technologie, surtout si cette technologie est utilisée avec des données confidentielles ou propriétaires. Azure Data Box Edge vous assure que seuls les entités autorisées peuvent consulter, modifier ou supprimer vos données.

Cet article décrit les fonctionnalités de sécurité Data Box Edge qui permettent de protéger chacun des composants de la solution et les données stockées sur ces derniers.

Azure Data Box Edge repose sur l’interaction de quatre composants principaux :

- **Service Data Box Edge, hébergé dans Azure** : la ressource de gestion qui vous permet de créer la commande de l’appareil, de configurer l’appareil et de suivre la commande jusqu’à son achèvement.
- **Appareil Data Box Edge** : appareil de transfert qui vous est livré pour que vous puissiez importer vos données locales dans Azure.
- **Clients/hôtes connectés à l’appareil** : les clients de votre infrastructure se connectant à l’appareil Data Box Edge et contenant des données devant être protégées.
- **Stockage cloud** : emplacement dans la plateforme cloud Azure où les données sont stockées. Il s’agit généralement du compte de stockage lié à la ressource Data Box Edge que vous avez créée.

## <a name="data-box-edge-service-protection"></a>Protection du service Edge Data Box

Le service Data Box Edge est un service de gestion hébergé dans Azure. Ce service est utilisé pour configurer et gérer l’appareil.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>Protection de l’appareil Data Box Edge

L’appareil Data Box Edge est un appareil local qui vous permet de transformer vos données en les traitant localement, avant de les envoyer vers Azure. Votre appareil :

- À besoin d’une clé d’activation pour accéder au service Data Box Edge.
- Est protégé à tout moment par un mot de passe d’appareil.
- Est un appareil verrouillé. L’appareil BMC et BIOS sont protégés par mot de passe. Le BIOS est protégé par accès utilisateur limité.
- Le démarrage sécurisé est activé.
- Exécute Windows Defender Device Guard. Device Guard permet d’exécuter uniquement les applications de confiance que vous définissez dans vos stratégies d’intégrité du code.

### <a name="protect-the-device-via-activation-key"></a>Protéger l’appareil via une clé d’activation

Seul les appareils Data Box Edge autorisés sont autorisés à rejoindre le service Data Box Edge que vous avez créé dans votre abonnement Azure. Pour autoriser un appareil, vous devez utiliser une clé d’activation pour activer l’appareil auprès du service Data Box Edge.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Pour plus d’informations, consultez [Obtenir une clé d’activation](data-box-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Protéger l’appareil via un mot de passe

Les mots de passe garantissent que seuls les utilisateurs autorisés accèdent à vos données. Les appareils Data Box Edge démarrent en étant verrouillés.

Vous pouvez :

- Vous connecter à l’interface utilisateur web locale de l’appareil via un navigateur, puis indiquer un mot de passe pour vous connecter à l’appareil.
- Vous connecter à distance à l’interface PowerShell de l’appareil via HTTP. La gestion à distance est désactivée par défaut. Vous pouvez ensuite fournir le mot de passe pour vous connecter à l’appareil. Pour plus d’informations, consultez [Connexion à distance à votre appareil Data Box Edge](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Utiliser l’interface utilisateur web locale pour [modifier le mot de passe](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access). Si vous modifiez le mot de passe, veillez à informer tous les utilisateurs à distance afin qu’ils ne connaissent pas de problème de connexion.

## <a name="protect-your-data"></a>Protéger vos données

Cette section décrit les fonctionnalités de sécurité Data Box Edge visant à protéger les données transmises et stockées.

### <a name="protect-data-at-rest"></a>Protection des données au repos

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]
- Le chiffrement BitLocker XTS-AES de 256 bits est utilisé pour protéger les données locales.


### <a name="protect-data-in-flight"></a>Protection des données à la volée

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Protection des données par les comptes de stockage

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Faites tourner puis [synchronisez vos clés de compte de stockage](data-box-edge-manage-shares.md#sync-storage-keys) régulièrement pour mieux protéger votre compte de stockage des utilisateurs non autorisés.

## <a name="manage-personal-information"></a>Gestion des informations personnelles

Le service Data Box Edge collecte des informations personnelles dans les scénarios suivants :

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Pour afficher la liste des utilisateurs qui ont accès à un partage ou qui peuvent supprimer un partage, suivez les étapes de la page [Gérer les partages sur Data Box Edge](data-box-edge-manage-shares.md).

Pour plus d’informations, consultez la Politique de confidentialité Microsoft dans le [Centre de gestion de la confidentialité](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Étapes suivantes

[Déployer votre appareil Data Box Edge](data-box-edge-deploy-prep.md)
