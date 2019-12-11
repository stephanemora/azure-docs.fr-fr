---
title: Autorisation d’accès au Stockage Azure | Microsoft Docs
description: Découvrez plus d’informations sur les différentes façons d’autoriser l’accès au Stockage Azure, notamment Azure Active Directory, l’authentification de clé partagée ou les signatures d’accès partagé.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b7669f9ec804a8fd2801474a845af7e029ee5235
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2019
ms.locfileid: "74664044"
---
# <a name="authorizing-access-to-azure-storage"></a>Autorisation d’accès au Stockage Azure

Chaque fois que vous accédez aux données de votre compte de stockage, le client envoie une demande sur HTTP/HTTPS au Stockage Azure. Chaque demande adressée à une ressource sécurisée doit être autorisée, pour que le service vérifie que le client a les autorisations nécessaires pour accéder aux données.

Le tableau suivant décrit les options offertes par Stockage Azure pour autoriser l’accès aux ressources :

|  |Clé partagée (clé de compte de stockage)  |Signature d’accès partagé (SAP)  |Azure Active Directory (Azure AD)  |Accès en lecture public anonyme  |
|---------|---------|---------|---------|---------|
|Objets blob Azure     |[Pris en charge](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[Pris en charge](storage-sas-overview.md)         |[Pris en charge](storage-auth-aad.md)         |[Pris en charge](../blobs/storage-manage-access-to-resources.md)         |
|Azure Files (SMB)     |[Pris en charge](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |Non pris en charge         |[Pris en charge, uniquement avec les services de domaine AAD](../files/storage-files-active-directory-overview.md)         |Non pris en charge         |
|Azure Files (REST)     |[Pris en charge](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[Pris en charge](storage-sas-overview.md)         |Non pris en charge         |Non pris en charge         |
|Files d'attente Azure     |[Pris en charge](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[Pris en charge](storage-sas-overview.md)         |[Pris en charge](storage-auth-aad.md)         |Non pris en charge         |
|Tables Azure     |[Pris en charge](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[Pris en charge](storage-sas-overview.md)         |Non pris en charge         |Non pris en charge         |

Chaque option d’autorisation est décrite brièvement ci-dessous :

- **Intégration d’Azure Active Directory (Azure AD)** pour les objets blob et les files d’attente. Azure AD fournit un contrôle d’accès basé sur un rôle (RBAC) pour contrôler l’accès d’un client aux ressources dans un compte de stockage. Pour plus d’informations sur l’intégration d’Azure AD pour les objets blob et les files d’attente, consultez [Authentification des demandes à Stockage Azure avec Azure Active Directory](storage-auth-aad.md).

- **Intégration d’Azure AD Domain Services (préversion)** pour les fichiers. Azure Files prend en charge l’authentification basée sur l’identité sur SMB (Server Message Block) via Azure AD DS. Ceci fournit un contrôle d’accès basé sur un rôle (RBAC) pour contrôler l’accès d’un client aux ressources dans un compte de stockage. Pour plus d’informations sur l’intégration d’Azure AD pour les fichiers avec des services de domaine, consultez [Vue d’ensemble de la prise en charge de l’authentification Azure Active Directory Domain Services (AAD DS) d’Azure Files pour l’accès SMB (préversion)](../files/storage-files-active-directory-overview.md).

- **Autorisation de clé partagée** pour les objets blob, les fichiers, les files d’attente et les tables. Un client qui utilise une clé partagée transmet avec chaque demande un en-tête signé à l’aide de la clé d’accès au compte de stockage. Pour plus d’informations, consultez [Autoriser avec une clé partagée](/rest/api/storageservices/authenticate-with-shared-key/).
- **Signatures d’accès partagé** pour les objets blob, les fichiers, les files d’attente et les tables. Les signatures d’accès partagé (SAP) fournissent un accès délégué limité aux ressources de votre compte de stockage. L’ajout de contraintes à la période de validité de la signature ou aux autorisations qu’elle accorde garantit la souplesse de la gestion de l’accès. Pour plus d’informations, consultez la page [Utiliser des signatures d’accès partagé (SAP)](storage-sas-overview.md).
- **Accès en lecture public anonyme** pour les conteneurs et les objets blob. L’autorisation n’est pas obligatoire. Pour plus d’informations, consultez [Gestion de l’accès en lecture anonyme aux conteneurs et aux objets blob](../blobs/storage-manage-access-to-resources.md).  

Par défaut, toutes les ressources dans le Stockage Azure sont sécurisées et disponibles uniquement pour le propriétaire du compte. Vous pouvez utiliser n’importe quelle stratégie d’autorisation décrite ci-dessus pour accorder aux clients l’accès aux ressources de votre compte de stockage, toutefois, Microsoft recommande d’utiliser Azure AD quand cela est possible pour optimiser la sécurité et la facilité d’utilisation.

## <a name="next-steps"></a>Étapes suivantes

- [Documentation Azure Active Directory](/azure/active-directory/)
- [Évolution de la plateforme d’identités Microsoft](/azure/active-directory/develop/about-microsoft-identity-platform)
