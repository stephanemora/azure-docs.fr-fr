---
title: Chiffrement du Stockage Azure pour les données au repos
description: Le Stockage Azure protège vos données en les chiffrant automatiquement avant de les rendre persistantes dans le cloud. Vous pouvez vous reposer sur les clés managées par Microsoft pour le chiffrement des données de votre compte de stockage, ou vous pouvez gérer le chiffrement avec vos propres clés.
services: storage
author: tamram
ms.service: storage
ms.date: 05/11/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: efc1d1567da8a181ca8de336bf26729e3717729b
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109790772"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Chiffrement du Stockage Azure pour les données au repos

Le service Stockage Azure utilise le chiffrement côté serveur (SSE) pour chiffrer automatiquement vos données lorsqu’elles sont rendues persistantes dans le Cloud. Le chiffrement de Stockage Azure protège vos données et vous aide à répondre aux engagements de votre entreprise en matière de sécurité et de conformité.

## <a name="about-azure-storage-encryption"></a>À propos du chiffrement de Stockage Azure

Les données dans Stockage Azure sont chiffrées et déchiffrées en toute transparence à l’aide du [chiffrement AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 bits, un des chiffrements par blocs les plus puissants actuellement disponibles, et sont conformes à la norme FIPS 140-2. Le chiffrement du Stockage Azure est similaire au chiffrement BitLocker sur Windows.

Le chiffrement de Stockage Azure est activé pour tous les comptes de stockage, y compris les comptes de stockage classiques et Resource Manager. Le chiffrement de Stockage Azure ne peut pas être désactivé. Étant donné que vos données sont sécurisées par défaut, vous n’avez pas besoin de modifier votre code ou vos applications pour tirer parti du chiffrement du Stockage Azure.

Les données d’un compte de stockage sont chiffrées, quel que soit le niveau de performance (Standard ou Premium), le niveau d’accès (chaud ou froid) ou le modèle de déploiement (Azure Resource Manager ou Classique). Tous les objets blob du niveau Archive sont également chiffrés. Toutes les options de redondance de Stockage Azure prennent en charge le chiffrement, et toutes les données dans les régions primaire et secondaire sont chiffrées quand la géoréplication est activée. Toutes les ressources de Stockage Azure sont chiffrées, y compris les objets blob, disques, fichiers, files d’attente et tables. Toutes les métadonnées d’objet sont également chiffrées. Le chiffrement de Stockage Azure n’implique aucun coût supplémentaire.

Chaque objet blob de blocs, objet blob d’ajout ou objet blob de pages qui a été écrit dans le Stockage Azure après le 20 octobre 2017 est chiffré. Les objets blob créés avant cette date continuent à être chiffrés à l’aide d’un processus en arrière-plan. Pour forcer le chiffrement d’un objet blob qui a été créé avant le 20 octobre 2017, vous pouvez réécrire l’objet. Pour savoir comment vérifier l’état de chiffrement d’un objet blob, consultez [Vérifier l’état de chiffrement d’un objet blob](../blobs/storage-blob-encryption-status.md).

Pour plus d’informations sur les modules cryptographiques de chiffrement de Stockage Azure, consultez [API de chiffrement : nouvelle génération](/windows/desktop/seccng/cng-portal).

Pour plus d’informations sur le chiffrement et la gestion des clés pour les disques managés Azure, consultez [Chiffrement côté serveur de disques managés Azure](../../virtual-machines/disk-encryption.md).

## <a name="about-encryption-key-management"></a>À propos de la gestion des clés de chiffrement

Par défaut, les données d'un nouveau compte de stockage sont chiffrées à l'aide de clés managées par Microsoft. Vous pouvez continuer à vous reposer sur les clés managées par Microsoft pour le chiffrement des vos données, ou vous pouvez gérer le chiffrement avec vos propres clés. Si vous choisissez de gérer le chiffrement avec vos propres clés, deux options s’offrent à vous. Vous pouvez utiliser l'un ou l'autre type de gestion des clés, ou les deux :

- Vous pouvez spécifier une *clé gérée par le client* à utiliser pour le chiffrement et le déchiffrement des données dans le stockage d’objets blob et dans Azure Files. <sup>1, 2</sup> clés gérées par le client doivent être stockées dans Azure Key Vault ou le modèle de sécurité matérielle (HSM) Azure Key Vault géré (préversion). Pour plus d’informations sur les clés gérées par le client, consultez [Utiliser des clés gérées par le client pour gérer le chiffrement du stockage Azure](./customer-managed-keys-overview.md).
- Vous pouvez spécifier une *clé fournie par le client* sur les opérations de stockage Blob. Un client qui effectue une requête de lecture ou d’écriture sur le stockage Blob peut inclure une clé de chiffrement dans la requête afin de contrôler la précision avec laquelle les données blob sont chiffrées et déchiffrées. Pour plus d’informations sur les clés fournies par le client, consultez [Fournir une clé de chiffrement lors d’une requête au stockage d’objets blob](../blobs/encryption-customer-provided-keys.md).

Le tableau suivant compare les options de gestion de clés pour le chiffrement de Stockage Azure.

| Paramètre de gestion des clés | Clés managées par Microsoft | Clés gérées par le client | Clés fournies par le client |
|--|--|--|--|
| Opérations de chiffrement/déchiffrement | Azure | Azure | Azure |
| Services de stockage Azure pris en charge | Tous | Stockage Blob, Azure Files<sup>1,2</sup> | Stockage d'objets blob |
| Stockage des clés | Magasin de clés Microsoft | Azure Key Vault ou HSM Key Vault | Magasin de clés du client |
| Responsabilité de la permutation des clés | Microsoft | Customer | Customer |
| Contrôle des clés | Microsoft | Customer | Customer |

<sup>1</sup> Pour plus d’informations sur la création d’un compte qui prend en charge l’utilisation de clés gérées par le client avec Stockage File d’attente, consultez [Créer un compte qui prend en charge les clés gérées par le client pour les files d’attente](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).<br />
<sup>2</sup> Pour plus d’informations sur la création d’un compte qui prend en charge l’utilisation de clés gérées par le client avec Stockage Table, consultez [Créer un compte qui prend en charge les clés gérées par le client pour les tables](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).

> [!NOTE]
> Les clés gérées par Microsoft font l'objet d'une rotation appropriée en fonction des exigences de conformité. Si vous avez des exigences spécifiques en matière de rotation des clés, Microsoft vous recommande de passer aux clés gérées par le client afin de pouvoir gérer et vérifier vous-même la rotation.

## <a name="doubly-encrypt-data-with-infrastructure-encryption"></a>Chiffrer les données doublement avec le chiffrement de l’infrastructure

Les clients qui doivent s’assurer que leurs données sont sécurisées peuvent également activer le chiffrement AES 256 bits au niveau de l’infrastructure Stockage Azure. Lorsque le chiffrement d’infrastructure est activé, les données d’un compte de stockage sont chiffrées deux fois &mdash; une fois au niveau du service et une fois au niveau de l’infrastructure &mdash; avec deux algorithmes de chiffrement et deux clés différents. Le double chiffrement des données Stockage Azure permet d’éviter un scénario impliquant une possible compromission d’un algorithme ou d’une clé de chiffrement. Dans un tel scénario, la couche de chiffrement supplémentaire continue de protéger vos données.

Le chiffrement au niveau du service prend en charge l’utilisation de clés gérées par Microsoft ou de clés gérées par le client avec Azure Key Vault. Le chiffrement au niveau de l’infrastructure s’appuie sur des clés gérées par Microsoft et utilise systématiquement une clé distincte.

Pour plus d’informations sur la création d’un compte de stockage qui permet le chiffrement de l’infrastructure, consultez [Créer un compte de stockage avec le chiffrement d’infrastructure activé à des fins de double chiffrement des données](infrastructure-encryption-enable.md).

## <a name="next-steps"></a>Étapes suivantes

- [Qu’est-ce qu’Azure Key Vault ?](../../key-vault/general/overview.md)
- [Clés gérées par le client pour le chiffrement du Stockage Azure](customer-managed-keys-overview.md)
- [Étendues de chiffrement pour le stockage d’objets blob](../blobs/encryption-scope-overview.md)
- [Fournir une clé de chiffrement lors d’une requête au stockage d’objets blob](../blobs/encryption-customer-provided-keys.md)
