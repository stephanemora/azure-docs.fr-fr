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
ms.openlocfilehash: 14de5283197778464993b80b312a690662e066ab
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443740"
---
Le stockage Azure offre plusieurs types de comptes de stockage. Chaque type prend en charge différentes fonctionnalités et a son propre modèle tarifaire. Avant de créer un compte de stockage, tenez compte de ces différences pour déterminer l’option qui convient le mieux à vos applications. Les types de comptes de stockage proposés sont les suivants :

- **Comptes de stockage à usage général v2** : Type de compte de stockage de base pour les objets blob, les fichiers, les files d’attente et les tables. Recommandé pour la plupart des scénarios utilisant Stockage Azure.
- **Comptes v1 universels** : Type de compte de stockage hérité pour les objets blob, les fichiers, les files d’attente et les tables. Utilisez ce type de compte à la place des comptes v2 universels lorsque cela est possible.
- **Comptes de stockage d’objets blob de blocs** : comptes de stockage d’objets BLOB uniquement ayant des caractéristiques de performances Premium. Recommandé pour les scénarios aux taux élevés de transactions, utilisant des objets plus petits ou nécessitant une latence de stockage faible.
- **Comptes de stockage FileStorage** : comptes de stockage de fichiers uniquement ayant des caractéristiques de performances Premium. Recommandé pour l’entreprise ou des applications de mise à l’échelle hautes performances.
- **Comptes de stockage d’objets blob** : Comptes de stockage Blob uniquement. Utilisez ce type de compte à la place des comptes v2 universels lorsque cela est possible.

Le tableau suivant répertorie les types de comptes de stockage disponibles et leurs fonctionnalités :

| Type de compte de stockage | Services pris en charge                       | Niveaux de performances pris en charge      | Niveaux d’accès pris en charge         | Options de réplication               | Modèle de déploiement<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Chiffrement<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Universel v2   | Objets blob, fichiers, files d’attente, tables et disques       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Chaud, froid, archive<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Gestionnaire de ressources             | Chiffré              |
| Universel v1   | Objets blob, fichiers, files d’attente, tables et disques       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | N/A                            | LRS, GRS, RA-GRS                  | Resource Manager, Classic    | Chiffré              |
| Stockage d’objets blob de blocs   | Objets blob (objets blob de blocs et objets blob d’ajout uniquement) | Premium                       | N/A                            | LRS                               | Gestionnaire de ressources             | Chiffré              |
| FileStorage   | Fichiers uniquement | Premium                       | N/A                            | LRS                               | Gestionnaire de ressources             | Chiffré              |
| Stockage d'objets blob         | Objets blob (objets blob de blocs et objets blob d’ajout uniquement) | standard                      | Chaud, froid, archive<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Gestionnaire de ressources             | Chiffré              |

<div id="deployment-model"><sup>1</sup>L’utilisation du modèle de déploiement Azure Resource Manager est recommandée. Les comptes de stockage qui utilisent le modèle de déploiement classique peuvent toujours être créés à certains emplacements, et les comptes classiques existants continuent d’être pris en charge. Pour plus d’informations, consultez <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Déploiement Azure Resource Manager et déploiement Classic : comprendre les modèles de déploiement et l’état de vos ressources</a>.</div>

<div id="encryption"><sup>2</sup>Dans tous les comptes de stockage, les données au repos sont chiffrées à l’aide de Storage Service Encryption (SSE). Pour plus d’informations, consultez <a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">Azure Storage Service Encryption pour les données au repos</a>.</div>

<div id="archive"><sup>3</sup>L’archivage est disponible uniquement au niveau de chaque objet blob, et n’est pas disponible au niveau du compte de stockage. Seuls les objets blob de blocs et d’ajout peuvent être archivés. Pour plus d’informations, consultez <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">Stockage Blob Azure : Niveaux de stockage chaud, à froid et archivage</a>.</div>

<div id="zone-redundant-storage"><sup>4</sup>Le stockage redondant dans une zone (ZRS) est disponible uniquement pour les comptes de stockage universels v2 standard. Pour plus d’informations sur le stockage redondant interzone (ZRS), consultez l’article <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs">Stockage redondant interzone : applications Stockage Azure hautement disponibles</a>. Pour plus d’informations sur les autres options de réplication, consultez <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">Réplication de Stockage Azure</a>.</div>

<div id="premium-performance"><sup>5</sup>Performances Premium pour les comptes v1 et v2 à usage général est disponible uniquement pour les objets blob de page et de disque.</div>
