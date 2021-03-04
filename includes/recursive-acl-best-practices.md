---
title: Fichier include
description: Fichier include
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 02/03/2021
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: b0be074c995fcc62f63a4a2ebf4149d3040e7b6b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750233"
---
## <a name="best-practices"></a>Meilleures pratiques

Cette section décrit certaines des meilleures pratiques pour définir des listes de contrôle d’accès (ACL) de manière récursive. 

#### <a name="handling-runtime-errors"></a>Traitement des erreurs d’exécution

Une erreur d’exécution peut se produire pour de nombreuses raisons (par exemple, une panne ou un problème de connectivité client). Si vous rencontrez une erreur d’exécution, redémarrez le processus ACL récursif. Les listes de contrôle d’accès peuvent être réappliquées à des éléments sans impact négatif. 

#### <a name="handling-permission-errors-403"></a>Traitement des erreurs d’autorisation (403)

Si vous rencontrez une exception de contrôle d’accès lors de l’exécution d’un processus ACL récursif, votre [principal de sécurité](../articles/role-based-access-control/overview.md#security-principal) AD peut ne pas disposer des autorisations suffisantes pour appliquer une liste de contrôle d’accès à un ou plusieurs des éléments enfants dans la hiérarchie de répertoires. Lorsqu’une erreur d’autorisation se produit, le processus s’arrête et un jeton de continuation est fourni. Corrigez le problème d’autorisation, puis utilisez le jeton de continuation pour traiter le jeu de données restant. Les répertoires et les fichiers qui ont déjà été traités correctement n’ont pas besoin d’être retraités. Vous pouvez également choisir de redémarrer le processus ACL récursif. Les listes de contrôle d’accès peuvent être réappliquées à des éléments sans impact négatif. 

#### <a name="credentials"></a>Informations d'identification 

Nous vous recommandons de provisionner un principal de sécurité Azure AD auquel a été attribué le rôle [Propriétaire des données Blob de stockage](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner) dans l’étendue du conteneur ou du compte de stockage cible. 

#### <a name="performance"></a>Performances 

Pour réduire la latence, nous vous recommandons d’exécuter le processus ACL récursif dans une machine virtuelle Azure située dans la même région que votre compte de stockage. 

#### <a name="acl-limits"></a>Limites des listes de contrôle d’accès (ACL)

Le nombre maximal de listes ACL que vous pouvez appliquer à un répertoire ou à un fichier est de 32 ACL d’accès et de 32 ACL par défaut. Pour plus d’informations, consultez [Contrôle d’accès dans Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).