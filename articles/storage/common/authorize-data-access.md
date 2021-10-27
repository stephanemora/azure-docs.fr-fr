---
title: Autoriser les opérations de données
titleSuffix: Azure Storage
description: Découvrez les différentes façons d’autoriser l’accès aux données dans Stockage Azure. Stockage Azure prend en charge l’autorisation avec Azure Active Directory, l’autorisation de clé partagée ou les signatures d’accès partagé (SAP), et prend également en charge l’accès anonyme aux objets blob.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/18/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 911db718fd7c88501006f89a549a371cd12adef4
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130178179"
---
# <a name="authorize-access-to-data-in-azure-storage"></a>Autorisation de l’accès aux données dans le stockage Azure

Chaque fois que vous accédez aux données de votre compte de stockage, votre application cliente envoie une requête sur HTTP/HTTPS à Stockage Azure. Par défaut, chaque ressource de Stockage Azure est sécurisée, et chaque demande à une ressource sécurisée doit être autorisée. L’autorisation garantit que l’application cliente dispose des autorisations appropriées pour accéder aux données de votre compte de stockage.

Le tableau suivant décrit les options offertes par Stockage Azure pour autoriser l’accès aux données :

| Artefact Azure | Clé partagée (clé de compte de stockage) | Signature d’accès partagé (SAP) | Azure Active Directory (Azure AD) | Active Directory Domain Services en local | Accès en lecture public anonyme |
|--|--|--|--|--|--|
| Objets blob Azure | [Pris en charge](/rest/api/storageservices/authorize-with-shared-key/) | [Pris en charge](storage-sas-overview.md) | [Pris en charge](../blobs/authorize-access-azure-active-directory.md) | Non pris en charge | [Pris en charge](../blobs/anonymous-read-access-configure.md) |
| Azure Files (SMB) | [Pris en charge](/rest/api/storageservices/authorize-with-shared-key/) | Non pris en charge | [Pris en charge, uniquement avec les services de domaine AAD](../files/storage-files-active-directory-overview.md) | [Pris en charge, les informations d'identification doivent être synchronisées avec Azure AD](../files/storage-files-active-directory-overview.md) | Non pris en charge |
| Azure Files (REST) | [Pris en charge](/rest/api/storageservices/authorize-with-shared-key/) | [Pris en charge](storage-sas-overview.md) | Non pris en charge | Non pris en charge | Non pris en charge |
| Files d'attente Azure | [Pris en charge](/rest/api/storageservices/authorize-with-shared-key/) | [Pris en charge](storage-sas-overview.md) | [Pris en charge](../queues/authorize-access-azure-active-directory.md) | Non pris en charge | Non pris en charge |
| Tables Azure | [Pris en charge](/rest/api/storageservices/authorize-with-shared-key/) | [Pris en charge](storage-sas-overview.md) | [Prise en charge](../tables/authorize-access-azure-active-directory.md) (préversion) | Non prise en charge | Non pris en charge |

Chaque option d’autorisation est décrite brièvement ci-dessous :

- **Intégration d’Azure Active Directory (Azure AD)** pour autoriser les requêtes sur les ressources Blob, File d’attente et Table. Chaque fois que possible, Microsoft recommande d’utiliser des informations d'identification Azure AD pour autoriser des requêtes sur des données pour un niveau optimal de sécurité et de facilité d’utilisation. Pour plus d’informations concernant l’intégration d’Azure AD, consultez [Autoriser l’accès aux données dans Stockage Azure](authorize-data-access.md).

    Vous pouvez utiliser le contrôle d’accès en fonction du rôle (Azure RBAC) pour gérer les autorisations d’un principal de sécurité pour les ressources Blob, File d’attente et Table dans un compte de stockage. Vous pouvez également utiliser le contrôle d’accès en fonction de l’attribut (ABAC) Azure pour ajouter des conditions aux attributions de rôle Azure pour les ressources Blob. Pour plus d’informations sur le contrôle RBAC, consultez [Qu’est-ce que le contrôle d’accès en fonction du rôle Azure (Azure RBAC) ?](../../role-based-access-control/overview.md). Pour plus d’informations sur ABAC, consultez [Qu’est-ce que le contrôle d’accès en fonction des attributs (Azure ABAC) ? (préversion)](../../role-based-access-control/conditions-overview.md).

- **Authentification Azure Active Directory Domain Services (Azure AD DS)** pour Azure Files. Azure Files prend en charge l’autorisation basée sur l’identité sur SMB (Server Message Block) via Azure AD DS. Vous pouvez utiliser le contrôle d’accès en fonction du rôle (RBAC) Azure pour contrôler l’accès d’un client aux ressources Azure Files d’un compte de stockage. Pour plus d’informations concernant l’authentification Azure Files à l’aide de services de domaine, consultez la [présentation](../files/storage-files-active-directory-overview.md).

- **Authentification Active Directory Domain Services en local (AD DS ou AD DS en local)** pour Azure Files. Azure Files prend en charge l’autorisation basée sur l’identité sur SMB via AD DS. Votre environnement AD DS peut être hébergé sur des machines locales ou des machines virtuelles Azure. L’accès SMB à Files est pris en charge en utilisant les informations d’identification AD DS des machines jointes à un domaine, localement ou dans Azure. Vous pouvez utiliser une combinaison du contrôle RBAC Azure pour le contrôle d’accès au niveau du partage et de listes DACL NTFS pour l’application d’autorisations de niveau répertoire/fichier. Pour plus d’informations concernant l’authentification Azure Files à l’aide de services de domaine, consultez la [présentation](../files/storage-files-active-directory-overview.md).

- **Autorisation de clé partagée** pour les objets blob, les fichiers, les files d’attente et les tables. Un client qui utilise une clé partagée transmet avec chaque demande un en-tête signé à l’aide de la clé d’accès au compte de stockage. Pour plus d’informations, consultez [Autoriser avec une clé partagée](/rest/api/storageservices/authorize-with-shared-key/).

    Vous pouvez interdire l’autorisation de clé partagée pour un compte de stockage. Lorsque l’autorisation de clé partagée n’est pas autorisée, les clients doivent utiliser Azure AD pour autoriser les requêtes de données dans ce compte de stockage. Pour plus d’informations, consultez [Empêcher l’autorisation avec clé partagée pour un compte de stockage Azure](shared-key-authorization-prevent.md).

- **Signatures d’accès partagé** pour les objets blob, les fichiers, les files d’attente et les tables. Les signatures d’accès partagé (SAP) fournissent un accès délégué limité aux ressources de votre compte de stockage. L’ajout de contraintes à la période de validité de la signature ou aux autorisations qu’elle accorde garantit la souplesse de la gestion de l’accès. Pour plus d’informations, consultez la page [Utiliser des signatures d’accès partagé (SAP)](storage-sas-overview.md).

- **Accès en lecture public anonyme** pour les conteneurs et les objets blob. Lorsque l’accès anonyme est configuré, les clients peuvent lire les données blob sans autorisation. Pour plus d’informations, consultez [Gestion de l’accès en lecture anonyme aux conteneurs et aux objets blob](../blobs/anonymous-read-access-configure.md).

    Vous pouvez interdire l’accès en lecture public anonyme pour un compte de stockage. Lorsque l’accès en lecture public anonyme est interdit, les utilisateurs ne peuvent pas configurer de conteneurs pour autoriser l’accès anonyme et toutes les requêtes doivent être autorisées. Pour en savoir plus, consultez la section [Configure anonymous public read access for containers and blobs](../blobs/anonymous-read-access-prevent.md) (Empêcher l’accès en lecture publique anonyme aux conteneurs et aux objets blob).

## <a name="next-steps"></a>Étapes suivantes

- [Autorisation de l’accès aux données dans le stockage Azure](authorize-data-access.md)
- [Autoriser avec une clé partagée](/rest/api/storageservices/authorize-with-shared-key/)
- [Accorder un accès limité aux ressources du Stockage Azure à l’aide des signatures d’accès partagé (SAP)](storage-sas-overview.md)
        