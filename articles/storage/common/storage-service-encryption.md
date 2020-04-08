---
title: Chiffrement du Stockage Azure pour les données au repos
description: Le Stockage Azure protège vos données en les chiffrant automatiquement avant de les rendre persistantes dans le cloud. Vous pouvez vous reposer sur les clés managées par Microsoft pour le chiffrement des données de votre compte de stockage, ou vous pouvez gérer le chiffrement avec vos propres clés.
services: storage
author: tamram
ms.service: storage
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f8f6f40f8ce8297b3cbfe6b3afcbf10df4db6572
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409828"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Chiffrement du Stockage Azure pour les données au repos

Le Stockage Azure chiffre automatiquement vos données lors de leur conservation dans le cloud. Le chiffrement de Stockage Azure protège vos données et vous aide à répondre aux engagements de votre entreprise en matière de sécurité et de conformité.

## <a name="about-azure-storage-encryption"></a>À propos du chiffrement de Stockage Azure

Les données dans Stockage Azure sont chiffrées et déchiffrées en toute transparence à l’aide du [chiffrement AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 bits, un des chiffrements par blocs les plus puissants actuellement disponibles, et sont conformes à la norme FIPS 140-2. Le chiffrement du Stockage Azure est similaire au chiffrement BitLocker sur Windows.

Le chiffrement de Stockage Azure est activé pour tous les comptes de stockage, y compris les comptes de stockage classiques et Resource Manager. Le chiffrement de Stockage Azure ne peut pas être désactivé. Étant donné que vos données sont sécurisées par défaut, vous n’avez pas besoin de modifier votre code ou vos applications pour tirer parti du chiffrement du Stockage Azure.

Les données d’un compte de stockage sont chiffrées, quel que soit le niveau de performance (Standard ou Premium), le niveau d’accès (chaud ou froid) ou le modèle de déploiement (Azure Resource Manager ou Classique). Tous les objets blob du niveau Archive sont également chiffrés. Toutes les options de redondance de Stockage Azure prennent en charge le chiffrement, et toutes les données dans les régions primaire et secondaire sont chiffrées quand la géoréplication est activée. Toutes les ressources de Stockage Azure sont chiffrées, y compris les objets blob, disques, fichiers, files d’attente et tables. Toutes les métadonnées d’objet sont également chiffrées. Le chiffrement de Stockage Azure n’implique aucun coût supplémentaire.

Chaque objet blob de blocs, objet blob d’ajout ou objet blob de pages qui a été écrit dans le Stockage Azure après le 20 octobre 2017 est chiffré. Les objets blob créés avant cette date continuent à être chiffrés à l’aide d’un processus en arrière-plan. Pour forcer le chiffrement d’un objet blob qui a été créé avant le 20 octobre 2017, vous pouvez réécrire l’objet. Pour savoir comment vérifier l’état de chiffrement d’un objet blob, consultez [Vérifier l’état de chiffrement d’un objet blob](../blobs/storage-blob-encryption-status.md).

Pour plus d’informations sur les modules cryptographiques de chiffrement de Stockage Azure, consultez [API de chiffrement : nouvelle génération](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="about-encryption-key-management"></a>À propos de la gestion des clés de chiffrement

Par défaut, les données d’un compte de stockage sont chiffrées à l’aide de clés managées par Microsoft. Vous pouvez vous reposer sur les clés managées par Microsoft pour le chiffrement des vos données, ou vous pouvez gérer le chiffrement avec vos propres clés. Si vous choisissez de gérer le chiffrement avec vos propres clés, deux options s’offrent à vous :

- Vous pouvez spécifier une *clé gérée par le client* avec Azure Key Vault à utiliser pour le chiffrement et le déchiffrement des données dans le stockage d’objets blob et dans Azure Files.<sup>1,2</sup> Pour plus d’informations sur les clés gérées par le client, consultez [Utiliser des clés gérées par le client avec Azure Key Vault pour gérer le chiffrement du Stockage Azure](encryption-customer-managed-keys.md).
- Vous pouvez spécifier une *clé fournie par le client* sur les opérations de stockage Blob. Un client qui effectue une requête de lecture ou d’écriture sur le stockage Blob peut inclure une clé de chiffrement dans la requête afin de contrôler la précision avec laquelle les données blob sont chiffrées et déchiffrées. Pour plus d’informations sur les clés fournies par le client, consultez [Fournir une clé de chiffrement lors d’une requête au stockage d’objets blob (préversion)](encryption-customer-provided-keys.md).

Le tableau suivant compare les options de gestion de clés pour le chiffrement de Stockage Azure.

|                                        |    Clés managées par Microsoft                             |    Clés managées par le client                                                                                                                        |    Clés fournies par le client                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    Opérations de chiffrement/déchiffrement    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    Services de stockage Azure pris en charge    |    Tous                                                |    Stockage Blob, Azure Files<sup>1,2</sup>                                                                                                               |    Stockage d'objets blob                                                                  |
|    Stockage des clés                         |    Magasin de clés Microsoft    |    Azure Key Vault                                                                                                                              |    Azure Key Vault ou tout autre magasin de clés                                                                 |
|    Responsabilité de la permutation des clés         |    Microsoft                                          |    Customer                                                                                                                                     |    Customer                                                                      |
|    Utilisation de la clé                           |    Microsoft                                          |    Portail Azure, API REST de fournisseur de ressources de stockage, bibliothèques de gestion de Stockage Azure, PowerShell, CLI        |    API REST de Stockage Azure (stockage d’objets blob), bibliothèques de client Stockage Azure    |
|    Accès aux clés                          |    Microsoft uniquement                                     |    Microsoft, client                                                                                                                    |    Client uniquement                                                                 |

<sup>1</sup> Pour plus d’informations sur la création d’un compte qui prend en charge l’utilisation de clés gérées par le client avec Stockage File d’attente, consultez [Créer un compte qui prend en charge les clés gérées par le client pour les files d’attente](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).<br />
<sup>2</sup> Pour plus d’informations sur la création d’un compte qui prend en charge l’utilisation de clés gérées par le client avec Stockage Table, consultez [Créer un compte qui prend en charge les clés gérées par le client pour les tables](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).

## <a name="next-steps"></a>Étapes suivantes

- [Qu’est-ce qu’Azure Key Vault ?](../../key-vault/key-vault-overview.md)
- [Configurer les clés managées par le client pour le chiffrement du Stockage Azure depuis le Portail Azure](storage-encryption-keys-portal.md)
- [Configurer les clés managées par le client pour le chiffrement du Stockage Azure depuis PowerShell](storage-encryption-keys-powershell.md)
- [Configurer des clés gérées par le client pour le chiffrement du stockage Azure depuis Azure CLI](storage-encryption-keys-cli.md)
