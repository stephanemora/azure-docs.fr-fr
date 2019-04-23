---
title: Fichier Include
description: Fichier Include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/23/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: d4f57eca89cbb68d61546c6d5ce5bcd04f9256e7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799505"
---
Stockage Azure offre plusieurs types de comptes de stockage. Chaque type prend en charge différentes fonctionnalités et a son propre modèle tarifaire. Avant de créer un compte de stockage, tenez compte de ces différences pour déterminer l’option qui convient le mieux à vos applications. Les types de comptes de stockage proposés sont les suivants :

- **Comptes de stockage à usage général v2** : Type de compte de stockage de base pour les objets blob, les fichiers, les files d’attente et les tables. Recommandé pour la plupart des scénarios utilisant Stockage Azure.
- **Comptes v1 universels** : Type de compte de stockage hérité pour les objets blob, les fichiers, les files d’attente et les tables. Utilisez ce type de compte à la place des comptes v2 universels lorsque cela est possible.
- **Bloquer les comptes de stockage blob**: Comptes de stockage d’objets BLOB uniquement avec les caractéristiques de performances premium. Recommandé pour les scénarios avec des taux élevé de transactions, à l’aide d’objets plus petits ou nécessitant une latence de stockage régulièrement faible.
- **Les comptes de stockage FileStorage (version préliminaire)**: Comptes de stockage de fichiers uniquement avec les caractéristiques de performances premium. Recommandé pour l’entreprise ou des applications à l’échelle de hautes performances.
- **Comptes de stockage d’objets blob** : Comptes de stockage Blob uniquement. Utilisez ce type de compte à la place des comptes v2 universels lorsque cela est possible.

Le tableau suivant répertorie les types de comptes de stockage disponibles et leurs fonctionnalités :

| Type de compte de stockage | Services pris en charge                       | Niveaux de performances pris en charge      | Niveaux d’accès pris en charge         | Options de réplication               | Modèle de déploiement<sup>1</sup> | Chiffrement<sup>2</sup> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Universel v2   | Objets blob, fichiers, files d’attente, tables et disques       | Standard, Premium<sup>5</sup> | Chaud, froid, archive<sup>3</sup> | LRS, ZRS<sup>4</sup>, GRS, RA-GRS | Gestionnaire de ressources             | Chiffré              |
| Universel v1   | Objets blob, fichiers, files d’attente, tables et disques       | Standard, Premium<sup>5</sup> | S.O.                            | LRS, GRS, RA-GRS                  | Resource Manager, Classic    | Chiffré              |
| Stockage d’objets blob de bloc   | Objets blob (objets blob de blocs et objets blob d’ajout uniquement) | Premium                       | S.O.                            | LRS                               | Gestionnaire de ressources             | Chiffré              |
| FileStorage (version préliminaire)   | Fichiers uniquement | Premium                       | S.O.                            | LRS                               | Gestionnaire de ressources             | Chiffré              |
| Stockage d'objets blob         | Objets blob (objets blob de blocs et objets blob d’ajout uniquement) | standard                      | Chaud, froid, archive<sup>3</sup> | LRS, GRS, RA-GRS                  | Gestionnaire de ressources             | Chiffré              |

<sup>1</sup>L’utilisation du modèle de déploiement Azure Resource Manager est recommandée. Les comptes de stockage qui utilisent le modèle de déploiement classique peuvent toujours être créés à certains emplacements, et les comptes classiques existants continuent d’être pris en charge. Pour plus d’informations, consultez [Déploiement Azure Resource Manager et déploiement Classic : comprendre les modèles de déploiement et l’état de vos ressources](../articles/azure-resource-manager/resource-manager-deployment-model.md).

<sup>2</sup>Dans tous les comptes de stockage, les données au repos sont chiffrées à l’aide de Storage Service Encryption (SSE). Pour plus d’informations, consultez [Azure Storage Service Encryption pour les données au repos](../articles/storage/common/storage-service-encryption.md).

<sup>3</sup>L’archivage est disponible uniquement au niveau de chaque objet blob, et n’est pas disponible au niveau du compte de stockage. Seuls les objets blob de blocs et d’ajout peuvent être archivés. Pour plus d’informations, consultez [Stockage Blob Azure : Niveaux de stockage chaud, à froid et archivage](../articles/storage/blobs/storage-blob-storage-tiers.md).

<sup>4</sup>Le stockage redondant dans une zone (ZRS) est disponible uniquement pour les comptes de stockage universels v2 standard. Pour plus d’informations sur le stockage redondant interzone (ZRS), consultez l’article [Stockage redondant interzone : applications Stockage Azure hautement disponibles](../articles/storage/common/storage-redundancy-zrs.md). Pour plus d’informations sur les autres options de réplication, consultez [Réplication de Stockage Azure](../articles/storage/common/storage-redundancy.md).

<sup>5</sup> performances premium pour v2 à usage général et comptes v1 à usage général est disponible pour l’objet blob disque et de la page.