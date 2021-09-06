---
title: Autoriser les opérations de données
titleSuffix: Azure Storage
description: Familiarisez-vous avec les différentes façons d’autoriser l’accès au Stockage Azure, notamment Azure Active Directory, l’autorisation de clé partagée ou les signatures d’accès partagé (SAS).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 9896cdaf82ca508c5e6a1a444564403ef0f7e1eb
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113765454"
---
# <a name="authorize-access-to-data-in-azure-storage"></a>Autorisation de l’accès aux données dans le stockage Azure

Chaque fois que vous accédez aux données de votre compte de stockage, le client envoie une demande sur HTTP/HTTPS au Stockage Azure. Chaque demande adressée à une ressource sécurisée doit être autorisée, pour que le service vérifie que le client a les autorisations nécessaires pour accéder aux données.

Le tableau suivant décrit les options offertes par Stockage Azure pour autoriser l’accès aux ressources :

| Artefact Azure | Clé partagée (clé de compte de stockage) | Signature d’accès partagé (SAP) | Azure Active Directory (Azure AD) | Active Directory Domain Services en local | Accès en lecture public anonyme |
|--|--|--|--|--|--|
| Objets blob Azure | [Pris en charge](/rest/api/storageservices/authorize-with-shared-key/) | [Pris en charge](storage-sas-overview.md) | [Pris en charge](authorize-data-access.md) | Non pris en charge | [Pris en charge](../blobs/anonymous-read-access-configure.md) |
| Azure Files (SMB) | [Pris en charge](/rest/api/storageservices/authorize-with-shared-key/) | Non pris en charge | [Pris en charge, uniquement avec les services de domaine AAD](../files/storage-files-active-directory-overview.md) | [Pris en charge, les informations d'identification doivent être synchronisées avec Azure AD](../files/storage-files-active-directory-overview.md) | Non pris en charge |
| Azure Files (REST) | [Pris en charge](/rest/api/storageservices/authorize-with-shared-key/) | [Pris en charge](storage-sas-overview.md) | Non pris en charge | Non prise en charge | Non prise en charge |
| Files d'attente Azure | [Pris en charge](/rest/api/storageservices/authorize-with-shared-key/) | [Pris en charge](storage-sas-overview.md) | [Pris en charge](authorize-data-access.md) | Non pris en charge | Non pris en charge |
| Tables Azure | [Pris en charge](/rest/api/storageservices/authorize-with-shared-key/) | [Pris en charge](storage-sas-overview.md) | [Prise en charge](../tables/authorize-access-azure-active-directory.md) (préversion) | Non prise en charge | Non pris en charge |

Chaque option d’autorisation est décrite brièvement ci-dessous :

- **Intégration d’Azure Active Directory (Azure AD)** pour les objets blob, les files d’attente et les tables. Azure fournit un contrôle d’accès en fonction du rôle Azure (RBAC Azure) pour contrôler l’accès d’un client aux ressources d’un compte de stockage. Chaque fois que possible, Microsoft recommande d’utiliser Azure AD pour un niveau optimal de sécurité et de facilité d’utilisation. Pour plus d’informations concernant l’intégration d’Azure AD, consultez [Autoriser l’accès aux données dans le stockage Azure](authorize-data-access.md).

- **Authentification Azure Active Directory Domain Services (Azure AD DS)** pour Azure Files. Azure Files prend en charge l’autorisation basée sur l’identité sur SMB (Server Message Block) via Azure AD DS. Vous pouvez utiliser le contrôle d’accès en fonction du rôle (RBAC) Azure pour contrôler l’accès d’un client aux ressources Azure Files d’un compte de stockage. Pour plus d’informations concernant l’authentification Azure Files à l’aide de services de domaine, veuillez consulter la [présentation](../files/storage-files-active-directory-overview.md).

- **Authentification Active Directory Domain Services en local (AD DS ou AD DS en local)** pour Azure Files. Azure Files prend en charge l’autorisation basée sur l’identité sur SMB via AD DS. Votre environnement AD DS peut être hébergé sur des machines locales ou des machines virtuelles Azure. L’accès SMB à Files est pris en charge en utilisant les informations d’identification AD DS des machines jointes à un domaine, localement ou dans Azure. Vous pouvez utiliser une combinaison du contrôle RBAC Azure pour le contrôle d’accès au niveau du partage et de listes DACL NTFS pour l’application d’autorisations de niveau répertoire/fichier. Pour plus d’informations concernant l’authentification Azure Files à l’aide de services de domaine, veuillez consulter la [présentation](../files/storage-files-active-directory-overview.md).

- **Autorisation de clé partagée** pour les objets blob, les fichiers, les files d’attente et les tables. Un client qui utilise une clé partagée transmet avec chaque demande un en-tête signé à l’aide de la clé d’accès au compte de stockage. Pour plus d’informations, consultez [Autoriser avec une clé partagée](/rest/api/storageservices/authorize-with-shared-key/).

- **Signatures d’accès partagé** pour les objets blob, les fichiers, les files d’attente et les tables. Les signatures d’accès partagé (SAP) fournissent un accès délégué limité aux ressources de votre compte de stockage. L’ajout de contraintes à la période de validité de la signature ou aux autorisations qu’elle accorde garantit la souplesse de la gestion de l’accès. Pour plus d’informations, consultez la page [Utiliser des signatures d’accès partagé (SAP)](storage-sas-overview.md).
- **Accès en lecture public anonyme** pour les conteneurs et les objets blob. L’autorisation n’est pas obligatoire. Pour plus d’informations, consultez [Gestion de l’accès en lecture anonyme aux conteneurs et aux objets blob](../blobs/anonymous-read-access-configure.md).  

## <a name="next-steps"></a>Étapes suivantes

- [Autorisation de l’accès aux données dans le stockage Azure](authorize-data-access.md)
- [Autoriser avec une clé partagée](/rest/api/storageservices/authorize-with-shared-key/)
- [Accorder un accès limité aux ressources du Stockage Azure à l’aide des signatures d’accès partagé (SAP)](storage-sas-overview.md)
