---
title: Fonctionnalités de sécurité utilisables avec le service Stockage Azure | Microsoft Docs
description: Cet article fournit une vue d’ensemble des principales fonctionnalités de sécurité Azure pouvant être utilisées avec Azure Storage.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: 249380b5bd9d95e969a9c7a812102b694b9d1e3b
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726541"
---
# <a name="azure-storage-security-overview"></a>Présentation de la sécurité du Stockage Azure

Cet article fournit une vue d’ensemble des fonctionnalités de sécurité Azure pouvant être utilisées avec le Stockage Azure. Le service Stockage Azure est la solution de stockage cloud pour les applications récentes qui s’appuient sur la durabilité, la disponibilité et la scalabilité pour répondre aux besoins des clients. Le Stockage Azure fournit un ensemble complet de fonctionnalités de sécurité. Vous pouvez :

* Sécurisez le compte de stockage à l’aide du contrôle d’accès en fonction du rôle (RBAC) et d’Azure Active Directory.
* Protégez les données en transit entre une application et Azure au moyen du chiffrement côté client, de HTTPS ou de SMB 3.0.
* Définissez les données de façon à ce qu’elles soient automatiquement chiffrées du moment où elles sont écrites dans le Stockage Azure en utilisant Storage Service Encryption.
* Les disques de système d’exploitation et de données utilisés par les machines virtuelles peuvent être définis de façon à être chiffrés à l’aide d’Azure Disk Encryption.
* Accordez un accès délégué aux objets de données du Stockage Azure en utilisant des signatures d’accès partagé.
* Utilisez l’analytique pour suivre la méthode d’authentification utilisée par une personne pour l’accès au stockage.

Pour une étude plus détaillée de la sécurité dans le Stockage Azure, consultez le [Guide de sécurité du Stockage Azure](../../storage/common/storage-security-guide.md). Ce guide fournit une présentation approfondie des fonctionnalités de sécurité du Stockage Azure. Ces fonctionnalités comprennent les clés de compte de stockage, le chiffrement des données en transit et au repos et l’analyse du stockage.

## <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle

Vous pouvez sécuriser un compte de stockage en utilisant le contrôle d’accès en fonction du rôle. Restreindre l’accès en fonction des principes du [besoin de connaître](https://en.wikipedia.org/wiki/Need_to_know) et du [privilège minimum](https://en.wikipedia.org/wiki/Principle_of_least_privilege) est impératif pour les organisations désireuses d’appliquer des stratégies de sécurité pour l’accès aux données. Ces droits d’accès sont octroyés en affectant le rôle RBAC approprié aux groupes et aux applications, dans une étendue donnée. Vous pouvez utiliser les [rôles RBAC intégrés](/azure/role-based-access-control/built-in-roles), comme Collaborateur de compte de stockage, pour affecter des privilèges aux utilisateurs.

En savoir plus :

* [Contrôle d’accès en fonction du rôle Azure Active Directory](/azure/role-based-access-control/role-assignments-portal)

## <a name="delegated-access-to-storage-objects"></a>Accès délégué aux objets de stockage

Une signature d'accès partagé fournit un accès délégué aux ressources de votre compte de stockage. La SAP vous permet d’octroyer à un client des autorisations d’accès limité à des objets de votre compte de stockage pendant une période donnée et avec un ensemble défini d’autorisations. Vous pouvez accorder ces autorisations limitées sans partager les clés d’accès de votre compte.

La SAP est un URI qui englobe dans ses paramètres de requête toutes les informations nécessaires pour obtenir un accès authentifié à une ressource de stockage. Pour accéder aux ressources de stockage avec la signature d’accès partagé, il suffit au client de transmettre cette dernière à la méthode ou au constructeur appropriés.

En savoir plus :

* [Présentation du modèle SAP](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
* [Créer et utiliser une signature d’accès partagé avec le Stockage Blob](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)

## <a name="encryption-in-transit"></a>Chiffrement en transit

Le chiffrement en transit est un mécanisme de protection des données transmises sur des réseaux. Le Stockage Azure vous permet de sécuriser les données à l’aide de diverses fonctionnalités :

* Le [chiffrement au niveau du transport](/azure/storage/common/storage-security-guide#encryption-in-transit) (HTTPS, par exemple) lorsque vous transférez des données vers ou à partir de Stockage Azure.
* Le [chiffrement câblé](/azure/storage/common/storage-security-guide#using-encryption-during-transit-with-azure-file-shares), par exemple le chiffrement SMB 3.0 pour les partages de fichiers Azure.
* Le [chiffrement côté client](/azure/storage/common/storage-security-guide#using-client-side-encryption-to-secure-data-that-you-send-to-storage), pour chiffrer les données avant leur transfert vers le Stockage et les déchiffrer après leur transfert à partir du Stockage.

En savoir plus sur le chiffrement côté client :

* [Client-Side Encryption for Microsoft Azure Storage (Chiffrement côté client pour Microsoft Azure Storage)](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
* [Cloud security controls series: Encrypting Data in Transit](https://cloudblogs.microsoft.com/microsoftsecure/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## <a name="encryption-at-rest"></a>Chiffrement au repos

Pour de nombreuses organisations, le [chiffrement des données au repos](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) est une étape obligatoire du processus de gestion de la confidentialité, de la conformité et de la souveraineté des données. Trois fonctionnalités Azure fournissent un chiffrement des données au repos :

* [Storage Service Encryption](/azure/storage/common/storage-security-guide#encryption-at-rest) est toujours activé et chiffre automatiquement les données du service de stockage lors de leur écriture dans le Stockage Azure.
* Le [chiffrement côté client](/azure/storage/common/storage-security-guide#client-side-encryption) fournit également la fonctionnalité de chiffrement au repos.
* [Azure Disk Encryption](/azure/storage/common/storage-security-guide#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) vous permet de chiffrer les disques de données et de système d’exploitation utilisés par une machine virtuelle IaaS.

En savoir plus sur Storage Service Encryption :

* [Azure Storage Service Encryption](https://azure.microsoft.com/services/storage/) est disponible pour le [Stockage Blob Azure](https://azure.microsoft.com/services/storage/blobs/). Pour plus d'informations sur les autres types de stockage Azure, consultez [Azure Files](https://azure.microsoft.com/services/storage/files/), [Stockage Table](https://azure.microsoft.com/services/storage/tables/) et [Stockage File d'attente](https://azure.microsoft.com/services/storage/queues/).
* [Azure Storage Service Encryption pour les données au repos](/azure/storage/common/storage-service-encryption)

## <a name="azure-disk-encryption"></a>Azure Disk Encryption

Azure Disk Encryption pour les machines virtuelles vous permet de satisfaire les exigences de sécurité et de conformité de l’organisation. Elle chiffre vos disques de machines virtuelles (y compris les disques d’amorçage et de données) à l’aide de clés et de stratégies que vous contrôlez dans [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Azure Disk Encryption pour les machines virtuelles est compatible avec les systèmes d’exploitation Linux et Windows. Cette fonctionnalité utilise également le coffre de clés pour vous aider à protéger, gérer et contrôler l’utilisation de vos clés de chiffrement de disque. Toutes les données contenues dans les disques de vos machines virtuelles sont chiffrées au repos à l’aide d’une technologie de chiffrement standard dans vos comptes de Stockage Azure. La solution Disk Encryption pour Windows est basée sur le [chiffrement de lecteur Microsoft BitLocker](https://technet.microsoft.com/library/cc732774.aspx) et la solution Linux est basée sur le chiffrement [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

En savoir plus

* [Azure Disk Encryption pour des machines virtuelles IaaS Windows et Linux](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## <a name="firewalls-and-virtual-networks"></a>Pare-feux et réseaux virtuels

Le stockage Azure vous permet d’activer les règles de pare-feu pour vos comptes de stockage. Une fois activé, elles bloquent les demandes de données entrantes, y compris les demandes provenant d’autres services Azure. Vous pouvez configurer des exceptions pour autoriser le trafic. Les règles de pare-feu peuvent être activées sur des comptes de stockage existants ou lors de la création.

Vous devriez utiliser cette fonctionnalité pour sécuriser vos comptes de stockage sur un ensemble spécifique de réseaux autorisés.

Pour plus d’informations sur les pare-feux et les réseaux virtuels de stockage Azure, consultez l’article [Configurer des pare-feux et des réseaux virtuels du stockage Azure](/azure/storage/common/storage-network-security)

## <a name="azure-data-box"></a>Azure Data Box

Les appareils Data Box, Data Box Disk et Data Box Heavy vous permettent de transférer de grandes quantités de données vers Azure quand l’utilisation du réseau n’est pas envisageable. Ces appareils de transfert de données hors connexion sont échangés entre votre organisation et le centre de données Azure. Ils utilisent un chiffrement AES pour protéger vos données en transit et font l’objet d’un processus complet d’assainissement après téléchargement, destiné à supprimer vos données.

Data Box Edge et Data Box Gateway sont des produits de transfert de données en ligne, qui font office de passerelles de stockage réseau pour gérer les données transitant entre votre site et Azure. Data Box Edge est un appareil réseau local, qui échange des données avec Azure et utilise le computing en périphérie basé sur l’intelligence artificielle pour traiter les données. Data Box Gateway est une appliance virtuelle dotée de fonctionnalités de passerelle de stockage.

En savoir plus :

* [Azure Data Box](https://azure.microsoft.com/services/storage/databox/)
* [Azure Data Box Edge](/azure/databox-online/data-box-edge-overview)
* [Azure Data Box Gateway](/azure/databox-online/data-box-gateway-overview)

## <a name="advanced-threat-protection"></a>Protection avancée contre les menaces

Le service Stockage Azure inclut la protection avancée contre les menaces, offrant une couche supplémentaire de sécurité intelligente qui détecte les tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses de votre compte de stockage. La protection avancée contre les menaces analyse les journaux de diagnostic de Stockage Azure pour détecter les requêtes suspectes de lecture, d’écriture ou de suppression de données dans le stockage Blob.

Les alertes de la protection avancée contre les menaces sont consultables dans [Azure Security Center](https://azure.microsoft.com/services/security-center/). Azure Security Center fournit des détails sur chaque activité suspecte détectée et suggère des actions pour examiner et corriger la menace potentielle.

En savoir plus :

* [Vue d’ensemble de la protection avancée contre les menaces dans le stockage Azure](/azure/storage/common/storage-advanced-threat-protection)

## <a name="azure-key-vault"></a>Azure Key Vault

Azure Disk Encryption utilise [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), ce qui vous permet de contrôler et de gérer les clés et les secrets de chiffrement de disque dans votre abonnement Key Vault. Elle garantit également que toutes les données sur les disques de vos machines virtuelles sont chiffrées au repos dans le stockage Azure. Nous vous recommandons d’utiliser Key Vault pour auditer les clés et l’utilisation de la stratégie.

En savoir plus

* [Qu’est-ce qu’Azure Key Vault ?](/azure/key-vault/key-vault-overview)
