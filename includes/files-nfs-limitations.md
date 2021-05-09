---
title: Fichier include
description: Fichier include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/04/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6e819e1078ac90ef16070702e7961122b06c1d6f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107880535"
---
En préversion, NFS présente les limitations suivantes :

- NFS 4.1 ne prend actuellement en charge que la plupart des fonctionnalités de la [spécification du protocole](https://tools.ietf.org/html/rfc5661). Certaines fonctionnalités, comme les délégations et les rappels de toutes sortes, les mises à niveau et les rétrogradations des verrous, ainsi que l’authentification Kerberos et le chiffrement, ne sont pas prises en charge.
- Si la majorité de vos demandes sont centrées sur les métadonnées, la latence sera plus grande en comparaison avec les opérations de lecture/d’écriture/de mise à jour.
- Les partages NFS ne peuvent être activés/créés que sur les nouveaux comptes de stockage et non sur les comptes existants.
- Seules les API REST du plan de gestion sont prises en charge. Les API REST du plan de données ne sont pas disponibles, ce qui signifie que des outils comme l’Explorateur de stockage ne fonctionneront pas avec les partages NFS. Vous ne pourrez pas non plus parcourir les données de partage NFS dans le Portail Azure.
- AzCopy n’est actuellement pas pris en charge.
- Disponible uniquement pour le niveau Premium.
- Les partages NFS acceptent uniquement les UID/GID numériques. Pour éviter que vos clients envoient des UID/GID alphanumériques, vous devez désactiver le mappage des ID.
- Les partages peuvent uniquement être montés à partir d’un compte de stockage sur une machine virtuelle individuelle, en utilisant des liaisons privées. Tenter de monter des partages à partir d’autres comptes de stockage échouera.
- Il est préférable de s’appuyer sur les autorisations affectées au groupe principal. Parfois, les autorisations allouées au groupe non principal de l’utilisateur peuvent entraîner le refus de l’accès en raison d’un bogue connu.

### <a name="azure-storage-features-not-yet-supported"></a>Fonctionnalités de stockage Azure pas encore prises en charge

En outre, les fonctionnalités Azure Files suivantes ne sont pas disponibles avec les partages NFS :

- Authentification basée sur l’identité
- Support Sauvegarde Azure
- Instantanés
- Suppression réversible
- Prise en charge complète du chiffrement en transit (pour plus d’informations, consultez [Sécurité NFS](../articles/storage/files/storage-files-compare-protocols.md#security))
- Azure File Sync (disponible uniquement pour les clients Windows, que NFS 4.1 ne prend pas en charge)
