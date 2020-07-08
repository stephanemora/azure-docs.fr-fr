---
title: Autorisation des opérations de données
titleSuffix: Azure Storage
description: Familiarisez-vous avec les différentes façons d’autoriser l’accès au Stockage Azure, notamment Azure Active Directory, l’autorisation de clé partagée ou les signatures d’accès partagé (SAS).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 1db26e6e1308299325e15b4c856fc2ebbaed8326
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84807610"
---
# <a name="authorizing-access-to-data-in-azure-storage"></a>Autorisation de l’accès aux données dans le Stockage Azure

Chaque fois que vous accédez aux données de votre compte de stockage, le client envoie une demande sur HTTP/HTTPS au Stockage Azure. Chaque demande adressée à une ressource sécurisée doit être autorisée, pour que le service vérifie que le client a les autorisations nécessaires pour accéder aux données.

Le tableau suivant décrit les options offertes par Stockage Azure pour autoriser l’accès aux ressources :

|  |Clé partagée (clé de compte de stockage)  |Signature d’accès partagé (SAP)  |Azure Active Directory (Azure AD)  |Active Directory Domain Services en local (préversion) |Accès en lecture public anonyme  |
|---------|---------|---------|---------|---------|---------|
|Objets blob Azure     |[Pris en charge](/rest/api/storageservices/authorize-with-shared-key/)         |[Pris en charge](storage-sas-overview.md)         |[Pris en charge](storage-auth-aad.md)         |Non pris en charge|[Pris en charge](../blobs/storage-manage-access-to-resources.md)         |
|Azure Files (SMB)     |[Pris en charge](/rest/api/storageservices/authorize-with-shared-key/)         |Non pris en charge         |[Pris en charge, uniquement avec les services de domaine AAD](../files/storage-files-active-directory-overview.md)         |[Pris en charge, les informations d'identification doivent être synchronisées avec Azure AD](../files/storage-files-active-directory-overview.md)|Non pris en charge         |
|Azure Files (REST)     |[Pris en charge](/rest/api/storageservices/authorize-with-shared-key/)         |[Pris en charge](storage-sas-overview.md)         |Non pris en charge         |Non pris en charge |Non pris en charge         |
|Files d'attente Azure     |[Pris en charge](/rest/api/storageservices/authorize-with-shared-key/)         |[Pris en charge](storage-sas-overview.md)         |[Pris en charge](storage-auth-aad.md)         |Non pris en charge | Non pris en charge         |
|Tables Azure     |[Pris en charge](/rest/api/storageservices/authorize-with-shared-key/)         |[Pris en charge](storage-sas-overview.md)         |Non pris en charge         |Non pris en charge| Non pris en charge         |

Chaque option d’autorisation est décrite brièvement ci-dessous :

- **Intégration d’Azure Active Directory (Azure AD)** pour les objets blob et les files d’attente. Azure AD fournit un contrôle d’accès basé sur un rôle (RBAC) pour contrôler l’accès d’un client aux ressources dans un compte de stockage. Pour plus d’informations sur l’intégration d’Azure AD pour les objets blob et les files d’attente, consultez [Autoriser l’accès aux objets blob et aux files d’attente avec Azure Active Directory](storage-auth-aad.md).

- **Authentification Azure Active Directory Domain Services (Azure AD DS)** pour Azure Files. Azure Files prend en charge l’autorisation basée sur l’identité sur SMB (Server Message Block) via Azure AD DS. Vous pouvez utiliser le contrôle d’accès en fonction du rôle (RBAC) pour contrôler l’accès d’un client aux ressources Azure Files dans un compte de stockage. Pour plus d’informations concernant l’authentification Azure Files à l’aide de services de domaine, veuillez consulter la [présentation](../files/storage-files-active-directory-overview.md).

- **Authentification Active Directory Domain Services en local (AD DS ou AD DS en local) [préversion]** pour Azure Files. Azure Files prend en charge l’autorisation basée sur l’identité sur SMB via AD DS. Votre environnement AD DS peut être hébergé sur des machines locales ou des machines virtuelles Azure. L’accès SMB à Files est pris en charge en utilisant les informations d’identification AD DS des machines jointes à un domaine, localement ou dans Azure. Vous pouvez utiliser une combinaison de RBAC, pour le contrôle d’accès au niveau du partage, et de listes de contrôle d’accès discrétionnaire NTFS, pour l’application des autorisations de niveau répertoire/fichier. Pour plus d’informations concernant l’authentification Azure Files à l’aide de services de domaine, veuillez consulter la [présentation](../files/storage-files-active-directory-overview.md).

- **Autorisation de clé partagée** pour les objets blob, les fichiers, les files d’attente et les tables. Un client qui utilise une clé partagée transmet avec chaque demande un en-tête signé à l’aide de la clé d’accès au compte de stockage. Pour plus d’informations, consultez [Autoriser avec une clé partagée](/rest/api/storageservices/authorize-with-shared-key/).
- **Signatures d’accès partagé** pour les objets blob, les fichiers, les files d’attente et les tables. Les signatures d’accès partagé (SAP) fournissent un accès délégué limité aux ressources de votre compte de stockage. L’ajout de contraintes à la période de validité de la signature ou aux autorisations qu’elle accorde garantit la souplesse de la gestion de l’accès. Pour plus d’informations, consultez la page [Utiliser des signatures d’accès partagé (SAP)](storage-sas-overview.md).
- **Accès en lecture public anonyme** pour les conteneurs et les objets blob. L’autorisation n’est pas obligatoire. Pour plus d’informations, consultez [Gestion de l’accès en lecture anonyme aux conteneurs et aux objets blob](../blobs/storage-manage-access-to-resources.md).  

Par défaut, toutes les ressources dans le Stockage Azure sont sécurisées et disponibles uniquement pour le propriétaire du compte. Vous pouvez utiliser n’importe quelle stratégie d’autorisation décrite ci-dessus pour accorder aux clients l’accès aux ressources de votre compte de stockage, toutefois, Microsoft recommande d’utiliser Azure AD quand cela est possible pour optimiser la sécurité et la facilité d’utilisation.

## <a name="next-steps"></a>Étapes suivantes

- [Autoriser l’accès aux objets blob et aux files d’attente Azure avec Azure Active Directory](storage-auth-aad.md)
- [Autoriser avec une clé partagée](/rest/api/storageservices/authorize-with-shared-key/)
- [Accorder un accès limité aux ressources du Stockage Azure à l’aide des signatures d’accès partagé (SAP)](storage-sas-overview.md)
