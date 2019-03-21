---
title: Données boîte Edge security | Microsoft Docs
description: Décrit les fonctionnalités de sécurité et de confidentialité qui protègent vos appareils Edge de zone de données, service et de données en local et dans le cloud.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/11/2019
ms.author: alkohli
ms.openlocfilehash: e3a24117cfd01c1c0bd0f08e8eca5adddf5ee7b6
ms.sourcegitcommit: f596d88d776a3699f8c8cf98415eb874187e2a48
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2019
ms.locfileid: "58119783"
---
# <a name="data-box-edge-security-and-data-protection-preview"></a>Données boîte Edge security et protection des données (version préliminaire)

Sécurité est une préoccupation majeure lors de l’adoption d’une nouvelle technologie, surtout si cette technologie est utilisée avec des données confidentielles ou propriétaires. Solution Microsoft Azure données boîte Edge garantit que seules les entités autorisées peuvent afficher, modifier ou supprimer vos données.

Cet article décrit les fonctionnalités de sécurité Edge de zone de données qui permettent de protéger de chacun des composants de la solution et les données stockées sur ces derniers.

La solution Edge de zone de données Azure se compose de quatre composants principaux qui interagissent entre eux :

- **Données boîte Edge / service de passerelle de zone de données hébergée dans Azure** – la ressource de gestion qui vous permet de créer l’ordre de l’appareil, configurer l’appareil et puis suivre l’ordre jusqu'à la fin.
- **L’unité de bord de la zone données** – l’appareil de transfert est livré à importer vos données locales dans Azure.
- **Les clients/hôtes connectés à l’appareil** – les clients dans votre infrastructure qui se connectent à l’appareil Edge de zone de données et contiennent des données qui doivent être protégées.
- **Le stockage cloud** : emplacement dans le cloud Azure où les données sont stockées. Cet emplacement est généralement lié à la ressource de données boîte Edge que vous avez créé le compte de stockage.

> [!IMPORTANT]
> Data Box Edge est en préversion. Avant de commander et déployer cette solution, lisez les [conditions d’utilisation de la préversion Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 


## <a name="data-box-edgedata-box-gateway-service-protection"></a>Protection de service de zone Edge/données zone passerelle de données

Le service de passerelle de zone de données boîte Edge/Data est un service de gestion hébergé dans Microsoft Azure. Le service est utilisé pour configurer et gérer l’appareil.

- L’accès au service de passerelle de données boîte Edge/données zone requiert votre organisation a un contrat entreprise (EA) ou un abonnement de fournisseur de solutions Cloud (CSP). Pour plus d’informations, accédez à [souscrire un abonnement Azure](https://azure.microsoft.com/resources/videos/sign-up-for-microsoft-azure/)!
- Étant donné que votre service de gestion est hébergé dans Azure, il est protégé par les fonctionnalités de sécurité Azure. Pour plus d’informations sur les fonctionnalités de sécurité fournies par Microsoft Azure, accédez au [Centre de confidentialité Microsoft Azure](https://azure.microsoft.com/support/trust-center/security/).

## <a name="data-box-edge-device-protection"></a>Protection des appareils Edge de la zone données

Le périphérique périmétrique de zone de données est un périphérique local qui permet de transformer les données par le traitement de localement, puis l’envoyer vers Azure. Votre appareil :

- A besoin d’une clé d’activation pour accéder au service de passerelle de données boîte Edge/données boîte.
- Est protégé à tout moment par un mot de passe d’administrateur.
- Est un appareil verrouillé. Le périphérique BMC et BIOS sont protégés par mot de passe avec un utilisateur-accès limité pour le BIOS.
- Le démarrage sécurisé est activé.
- Exécute Windows Defender Device Guard. Device Guard vous autorise à exécuter uniquement les applications de confiance que vous définissez dans vos stratégies d’intégrité du code. 

### <a name="protect-the-device-via-activation-key"></a>Protéger l’appareil via la clé d’activation

Seul un appareil Edge de zone de données autorisé est autorisé à rejoindre le service de passerelle de données boîte Edge/données zone que vous avez créé dans votre abonnement Azure. Pour autoriser un appareil, vous devez utiliser une clé d’activation pour activer le périphérique avec le service de passerelle de données boîte Edge/données boîte. Pour plus d’informations, accédez à [obtenir une clé d’activation](data-box-edge-deploy-prep.md#get-the-activation-key).

La clé d’activation que vous utilisez :

- Est une clé de l’authentification basée sur Azure Active Directory (AAD).
- Expire au bout de trois jours.
- N’est pas utilisé après l’activation de l’appareil.
 
Une fois un appareil est activé, il utilise des jetons pour communiquer avec Microsoft Azure.

### <a name="protect-the-device-via-password"></a>Protéger l’appareil via le mot de passe

Les mots de passe vous assurer que vos données sont accessibles aux seuls utilisateurs autorisés. Edge de zone de données et de la passerelle de données boîte appareils démarrage dans un état verrouillé.

Vous pouvez :

- Se connecter à l’interface utilisateur de l’appareil via un navigateur de web local, puis indiquez un mot de passe pour se connecter à l’appareil.
- Se connecter à distance à l’interface PowerShell de périphérique sur HTTP. Gestion à distance est activée par défaut. Vous pouvez ensuite fournir le mot de passe administrateur pour vous connecter à l’appareil. Pour plus d’informations, accédez à [connexion à distance à votre appareil Edge de zone de données](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

Gardez à l'esprit les meilleures pratiques suivantes :

- Le service de données boîte Edge ne peut pas récupérer les mots de passe existants : il peut uniquement réinitialiser via le portail Azure. Nous vous recommandons de stocker tous les mots de passe dans un endroit sûr, afin de ne pas être obligé de réinitialiser un mot de passe en cas d’oubli. Si vous réinitialisez un mot de passe, veillez à informer tous les utilisateurs avant que vous le réinitialisez.
- Utilisez local web UI à [modifier le mot de passe](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access). Si vous modifiez le mot de passe, veillez à informer tous les utilisateurs de l’accès à distance afin qu’ils ne connaissent pas un échec de connexion.
- Vous pouvez accéder à distance à l’interface Windows PowerShell de votre appareil via HTTP. Pour des raisons de sécurité, vous devez utiliser HTTP uniquement sur des réseaux approuvés.
- Assurez-vous que les mots de passe administrateur d’appareil sont forts et bien protégés. Suivez le [meilleures pratiques de mot de passe](https://docs.microsoft.com/azure/security/azure-security-identity-management-best-practices#enable-password-management).

## <a name="protect-the-data"></a>Protéger les données

Cette section décrit les fonctionnalités de sécurité de périphérie de zone de données qui protègent les données en transit et les données stockées.

### <a name="protect-data-at-rest"></a>Protection des données au repos

Pour le données au repos :

- Pour le données au repos, Edge de zone de données utilise le chiffrement BitLocker XTS AES-256 pour protéger les données locales.
- Pour les données qui résident dans des partages, l’accès aux partages est restreint.

    - Pour les clients SMB qui accèdent à partager des données, dont ils ont besoin d’informations d’identification utilisateur associées au partage. Ces informations d’identification sont définies au moment de la création du partage.
    - Pour les clients NFS accéder aux partages, les adresses IP des clients doivent être ajoutés au moment de la création du partage.


### <a name="protect-data-in-flight"></a>Protéger les données en vol

Pour les données en transit :

- Pour les données qui sont placées entre l’appareil et Azure, standard TLS 1.2 est utilisé. Il n’existe aucun élément de secours à TLS 1.1 et versions antérieures. La communication de l’agent sera bloquée si TLS 1.2 n’est pas pris en charge. Le TLS 1.2 est également requise pour portail et les opérations de gestion de kit de développement logiciel.
- Lorsque les clients accèdent à votre appareil via l’interface utilisateur web locale dans un navigateur, standard TLS 1.2 est utilisé comme le protocole sécurisé par défaut.

    - La meilleure pratique consiste à configurer votre navigateur pour utiliser TLS 1.2.
    - Si le navigateur ne prend pas en charge TLS 1.2, vous pouvez utiliser TLS 1.1 ou TLS 1.0.
- Pour protéger les données lorsque vous la copiez à partir de vos serveurs de données, nous vous recommandons d’utiliser SMB 3.0 avec chiffrement.

### <a name="protect-data-via-storage-accounts"></a>Protection des données par les comptes de stockage

Votre appareil est associé à un compte de stockage qui est utilisé en tant que destination pour vos données dans Azure. Accès au compte de stockage est contrôlé par l’abonnement et de stockage de 512 bits deux associés à ce compte de stockage de clés d’accès.

Une des clés est utilisée pour l’authentification lors de l’appareil de périphérie de zone de données accède au compte de stockage. L’autre clé est gardée en réserve, ce qui permet une rotation régulière des clés.

Pour des raisons de sécurité, nombreux centres de données nécessitent la rotation des clés. Nous vous recommandons de suivre ces méthodes recommandées pour la rotation des clés :

- Votre clé de compte de stockage est similaire au mot de passe racine pour votre compte de stockage. Protégez soigneusement votre clé de compte. Ne pas distribuer le mot de passe à d’autres utilisateurs, dur coder ou enregistrer n’importe où dans le texte en clair est accessible à d’autres personnes.
- [Régénérez votre clé de compte](../storage/common/storage-account-manage.md#regenerate-access-keys) à l’aide du portail Azure si vous pensez qu’il a peut-être été compromise.
- Faire pivoter, puis [synchroniser vos clés de compte de stockage](data-box-gateway-manage-shares.md#sync-storage-keys) régulièrement afin de garantir que votre compte de stockage n’est pas accessible par les utilisateurs non autorisés.
- Votre administrateur Azure doit changer ou régénérer régulièrement la clé primaire ou secondaire à l’aide de la section Stockage du portail Azure pour accéder directement au compte de stockage.


## <a name="manage-personal-information"></a>Gérer les informations personnelles

Le bord de la zone de données / service de passerelle de zone de données de collecte des informations personnelles dans les instances de la clé suivantes :

- **Détails de la commande** : une fois la commande créée, l’adresse de livraison, l’adresse e-mail, et les informations de contact des utilisateurs sont stockées dans le portail Azure. Les informations enregistrées incluent :
  - Nom du contact
  - Numéro de téléphone
  - Email
  - Adresse postale
  - City
  - Code postal
  - État
  - Pays/Province/Région
  - Numéro de suivi d’expédition

    Les détails de commande sont chiffrées et stockées dans le service. Le service conserve les informations jusqu'à ce que vous supprimiez explicitement la ressource ou l’ordre. En outre, la suppression de ressource et de la commande correspondante est bloquée à partir du moment que l’appareil est livré jusqu'à ce que l’appareil retourne à Microsoft.

- **Adresse de livraison** – une fois que la commande est passée, service Data Box fournit l’adresse d’expédition aux transporteurs tiers tels que des sauvegardes.

- **Les utilisateurs de partager** -accessibles aux utilisateurs sur votre appareil également les données résidant sur les partages. La liste de ces utilisateurs s’affiche et peut être consultée. Cette liste est également supprimée lorsque les partages sont supprimés. Pour afficher la liste des utilisateurs qui peuvent accéder ou supprimer un partage, suivez les étapes de [gérer des partages sur le bord de la zone de données](data-box-gateway-manage-shares.md).

Pour plus d’informations, consultez la [Politique de confidentialité Microsoft sur le Centre de gestion de la confidentialité](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Étapes suivantes

[Déployer votre appareil Edge de zone de données](data-box-edge-deploy-prep.md).

