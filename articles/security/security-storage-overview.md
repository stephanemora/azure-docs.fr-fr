---
title: Fonctionnalités de sécurité utilisables avec le service Stockage Azure | Microsoft Docs
description: Cet article fournit une vue d’ensemble des principales fonctionnalités de sécurité Azure pouvant être utilisées avec Azure Storage.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: 9558f1ec0d8ccd83da764a0967fa83d93e1e6a02
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365361"
---
# <a name="azure-storage-security-overview"></a>Présentation de la sécurité du Stockage Azure
Le service Stockage Azure est la solution de stockage cloud pour les applications récentes qui s’appuient sur la durabilité, la disponibilité et la scalabilité pour répondre aux besoins des clients. Le Stockage Azure fournit un ensemble complet de fonctionnalités de sécurité. Vous pouvez :

* Sécurisez le compte de stockage à l’aide du contrôle d’accès en fonction du rôle (RBAC) et d’Azure Active Directory.
* Protégez les données en transit entre une application et Azure au moyen du chiffrement côté client, de HTTPS ou de SMB 3.0.
* Définissez les données de façon à ce qu’elles soient automatiquement chiffrées du moment où elles sont écrites dans le Stockage Azure en utilisant Storage Service Encryption.
* Les disques de système d’exploitation et de données utilisés par les machines virtuelles peuvent être définis de façon à être chiffrés à l’aide d’Azure Disk Encryption.
* Accordez un accès délégué aux objets de données du Stockage Azure en utilisant des signatures d’accès partagé.
* Utilisez l’analytique pour suivre la méthode d’authentification utilisée par une personne pour l’accès au stockage.

Pour une étude plus détaillée de la sécurité dans le Stockage Azure, consultez le [Guide de sécurité du Stockage Azure](../storage/common/storage-security-guide.md). Ce guide fournit une présentation approfondie des fonctionnalités de sécurité du Stockage Azure. Ces fonctionnalités comprennent les clés de compte de stockage, le chiffrement des données en transit et au repos et l’analyse du stockage.

Cet article fournit une vue d’ensemble des fonctionnalités de sécurité Azure pouvant être utilisées avec le Stockage Azure. Les liens vers les articles fournissent des informations complémentaires sur chaque fonctionnalité.

## <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle
Vous pouvez sécuriser un compte de stockage en utilisant le contrôle d’accès en fonction du rôle. Restreindre l’accès en fonction des principes du [besoin de connaître](https://en.wikipedia.org/wiki/Need_to_know) et du [privilège minimum](https://en.wikipedia.org/wiki/Principle_of_least_privilege) est impératif pour les organisations désireuses d’appliquer des stratégies de sécurité pour l’accès aux données. Ces droits d’accès sont octroyés en affectant le rôle RBAC approprié aux groupes et aux applications, dans une étendue donnée. Vous pouvez utiliser les [rôles RBAC intégrés](../role-based-access-control/built-in-roles.md), comme Collaborateur de compte de stockage, pour affecter des privilèges aux utilisateurs.

En savoir plus :

* [Contrôle d’accès en fonction du rôle Azure Active Directory](../role-based-access-control/role-assignments-portal.md)

## <a name="delegated-access-to-storage-objects"></a>Accès délégué aux objets de stockage
Une signature d'accès partagé fournit un accès délégué aux ressources de votre compte de stockage. La SAP vous permet d’octroyer à un client des autorisations d’accès limité à des objets de votre compte de stockage pendant une période donnée et avec un ensemble défini d’autorisations. Vous pouvez accorder ces autorisations limitées sans partager les clés d’accès de votre compte. 

La SAP est un URI qui englobe dans ses paramètres de requête toutes les informations nécessaires pour obtenir un accès authentifié à une ressource de stockage. Pour accéder aux ressources de stockage avec la signature d’accès partagé, il suffit au client de transmettre cette dernière à la méthode ou au constructeur appropriés.

En savoir plus :

* [Présentation du modèle SAP](../storage/common/storage-dotnet-shared-access-signature-part-1.md)
* [Créer et utiliser une signature d’accès partagé avec le Stockage Blob](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md)

## <a name="encryption-in-transit"></a>Chiffrement en transit
Le chiffrement en transit est un mécanisme de protection des données transmises sur des réseaux. Le Stockage Azure vous permet de sécuriser les données à l’aide de diverses fonctionnalités :

* Le [chiffrement au niveau du transport](../storage/common/storage-security-guide.md#encryption-in-transit) (HTTPS, par exemple) lorsque vous transférez des données vers ou à partir de Stockage Azure.
* Le [chiffrement câblé](../storage/common/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), par exemple le chiffrement SMB 3.0 pour les partages de fichiers Azure.
* Le [chiffrement côté client](../storage/common/storage-security-guide.md#using-client-side-encryption-to-secure-data-that-you-send-to-storage), pour chiffrer les données avant leur transfert vers le Stockage et les déchiffrer après leur transfert à partir du Stockage.

En savoir plus sur le chiffrement côté client :

* [Client-Side Encryption for Microsoft Azure Storage (Chiffrement côté client pour Microsoft Azure Storage)](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
* [Cloud security controls series: Encrypting Data in Transit (Série consacrée aux contrôles de sécurité dans le cloud : chiffrement des données en transit)](http://blogs.microsoft.com/cybertrust/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## <a name="encryption-at-rest"></a>Chiffrement au repos
Pour de nombreuses organisations, le [chiffrement des données au repos](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) est une étape obligatoire du processus de gestion de la confidentialité, de la conformité et de la souveraineté des données. Trois fonctionnalités Azure fournissent un chiffrement des données au repos :

* [Storage Service Encryption](../storage/common/storage-security-guide.md#encryption-at-rest) vous permet de demander que le service de stockage chiffre automatiquement les données lors de leur écriture dans le Stockage Azure.
* Le [chiffrement côté client](../storage/common/storage-security-guide.md#client-side-encryption) fournit également la fonctionnalité de chiffrement au repos.
* [Azure Disk Encryption](../storage/common/storage-security-guide.md#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) vous permet de chiffrer les disques de données et de système d’exploitation utilisés par une machine virtuelle IaaS.

En savoir plus sur Storage Service Encryption :

* [Azure Storage Service Encryption](https://azure.microsoft.com/services/storage/) est disponible pour le [Stockage Blob Azure](https://azure.microsoft.com/services/storage/blobs/). Pour plus d’informations sur les autres types de stockage Azure, consultez [Fichiers Azure](https://azure.microsoft.com/services/storage/files/), [Stockage sur disque Premium](https://azure.microsoft.com/services/storage/premium-storage/), [Stockage Table](https://azure.microsoft.com/services/storage/tables/) et [Stockage File d’attente](https://azure.microsoft.com/services/storage/queues/).
* [Azure Storage Service Encryption pour les données au repos](../storage/common/storage-service-encryption.md)

## <a name="azure-disk-encryption"></a>Azure Disk Encryption
Azure Disk Encryption pour les machines virtuelles vous permet de satisfaire les exigences de sécurité et de conformité de l’organisation. Elle chiffre vos disques de machines virtuelles (y compris les disques d’amorçage et de données) à l’aide de clés et de stratégies que vous contrôlez dans [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Azure Disk Encryption pour les machines virtuelles est compatible avec les systèmes d’exploitation Linux et Windows. Cette fonctionnalité utilise également le coffre de clés pour vous aider à protéger, gérer et contrôler l’utilisation de vos clés de chiffrement de disque. Toutes les données contenues dans les disques de vos machines virtuelles sont chiffrées au repos à l’aide d’une technologie de chiffrement standard dans vos comptes de Stockage Azure. La solution Disk Encryption pour Windows est basée sur le [chiffrement de lecteur Microsoft BitLocker](https://technet.microsoft.com/library/cc732774.aspx) et la solution Linux est basée sur le chiffrement [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

En savoir plus :

* [Azure Disk Encryption pour des machines virtuelles IaaS Windows et Linux](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## <a name="azure-key-vault"></a>Azure Key Vault
Azure Disk Encryption utilise [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), ce qui vous permet de contrôler et de gérer les clés et les secrets de chiffrement de disque dans votre abonnement Key Vault. Elle garantit également que toutes les données sur les disques de vos machines virtuelles sont chiffrées au repos dans le stockage Azure. Nous vous recommandons d’utiliser Key Vault pour auditer les clés et l’utilisation de la stratégie.

En savoir plus :

* [Qu’est-ce qu’Azure Key Vault ?](../key-vault/key-vault-whatis.md)
* [Bien démarrer avec Azure Key Vault](../key-vault/key-vault-get-started.md)
