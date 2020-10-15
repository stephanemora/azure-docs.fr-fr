---
title: Fichier Include
description: Fichier Include
services: storage
author: twooley
ms.service: storage
ms.topic: include
ms.date: 09/30/2020
ms.author: twooley
ms.custom: include file
ms.openlocfilehash: 358de5761f3535b5f1e75d82539ee8e3ecdbd289
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91665597"
---
**Azure Data Lake Storage Gen2** n’est ni un type de compte de stockage ni un service dédié. Il s’agit de la dernière version des fonctionnalités qui sont dédiées à l’analytique du Big Data.  Ces fonctionnalités sont disponibles dans un compte de stockage universel v2 ou BlockBlobStorage, que vous pouvez obtenir en activant la fonctionnalité **Espace de noms hiérarchique** du compte. Pour les cibles de mise à l’échelle, consultez ces articles. 

- [Cibles de scalabilité pour Stockage Blob](https://docs.microsoft.com/azure/storage/blobs/scalability-targets#scale-targets-for-blob-storage).
- [Cibles de scalabilité pour les comptes de stockage standard](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account?toc=/azure/storage/blobs/toc.json#scale-targets-for-standard-storage-accounts).

**Azure Data Lake Storage Gen1** est un service dédié. C’est un référentiel hyperscale de niveau entreprise pour les charges de travail d’analytique du Big Data. Vous pouvez utiliser Data Lake Storage Gen1 pour capturer des données indépendamment de leur taille, leur type et leur vitesse d’ingestion au même endroit pour les analyser d’un point de vue opérationnel et exploratoire. Aucune limite ne s’applique à la quantité de données que vous pouvez stocker dans un compte Data Lake Storage Gen1.

| **Ressource** | **Limite** | **Commentaires** |
| --- | --- | --- |
| Nombre maximal de comptes Data Lake Storage Gen1 par abonnement, par région |10 | Pour demander une augmentation de cette limite, contactez le support. |
| Nombre maximal de listes de contrôle d’accès (ACL) par fichier ou dossier |32 | Il s’agit d’une limite inconditionnelle. Utilisez des groupes pour gérer l’accès avec moins d’entrées. |
| Nombre maximal de listes de contrôle d’accès (ACL) par défaut, par fichier ou dossier |32 | Il s’agit d’une limite inconditionnelle. Utilisez des groupes pour gérer l’accès avec moins d’entrées. |
