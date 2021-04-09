---
title: Activer Intel SGX pour votre instance Azure SQL Database
description: Découvrez comment activer Intel SGX pour Always Encrypted avec des enclaves sécurisées dans Azure SQL Database en sélectionnant une génération de matériel compatible avec SGX.
keywords: chiffrer les données, chiffrement sql, chiffrement de base de données, données sensibles, Always Encrypted, enclaves sécurisées, SGX, attestation
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: ded1406c47bb3f00c366da7a5b28319f3712f8a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98733754"
---
# <a name="enable-intel-sgx-for-your-azure-sql-database"></a>Activer Intel SGX pour votre instance Azure SQL Database 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Always Encrypted avec enclaves sécurisées pour Azure SQL Database est actuellement en **préversion publique**.

[Always Encrypted avec enclaves sécurisées](/sql/relational-databases/security/encryption/always-encrypted-enclaves) dans Azure SQL Database utilise des enclaves [Intel SGX (Software Guard Extensions)](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/). Pour qu’Intel SGX soit disponible, la base de données doit utiliser le [modèle vCore](service-tiers-vcore.md) et la génération matérielle de la [série DC](service-tiers-vcore.md#dc-series).

La configuration de la génération de matériel de série DC pour activer des enclaves Intel SGX est la responsabilité de l’administrateur Azure SQL Database. Consultez [Rôles et responsabilités lors de la configuration des enclaves SGX et de l’attestation](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation).

> [!NOTE]
> Intel SGX n’est pas disponible dans les générations matérielles autres que la série DC. Par exemple, Intel SGX n’est pas disponible pour le matériel de 5e génération et n’est pas disponible pour les bases de données utilisant le [modèle DTU](service-tiers-dtu.md).

> [!IMPORTANT]
> Avant de configurer la génération matérielle de série DC pour votre base de données, vérifiez la disponibilité régionale de la série DC et assurez-vous que vous comprenez ses limites de performances. Pour plus d’informations, consultez [Série DC](service-tiers-vcore.md#dc-series).

Pour obtenir des instructions détaillées sur la configuration d’une base de données nouvelle ou existante afin d’utiliser une génération matérielle spécifique, consultez [Choisir une génération de matériel](service-tiers-vcore.md#selecting-a-hardware-generation).
   
## <a name="next-steps"></a>Étapes suivantes

- [Configurer Azure Attestation pour votre serveur Azure SQL Database](always-encrypted-enclaves-configure-attestation.md)

## <a name="see-also"></a>Voir aussi

- [Tutoriel : Bien démarrer avec Always Encrypted avec enclaves sécurisées dans Azure SQL Database](always-encrypted-enclaves-getting-started.md)