---
title: Activer Intel SGX pour Always Encrypted
description: Découvrez comment activer Intel SGX pour Always Encrypted avec des enclaves sécurisées dans Azure SQL Database en sélectionnant une génération de matériel compatible avec SGX.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 07/14/2021
ms.openlocfilehash: b967ad27b3f20b75e9b7571a865228508bca9112
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113727253"
---
# <a name="enable-intel-sgx-for-always-encrypted-for-your-azure-sql-database"></a>Activer Intel SGX pour Always Encrypted pour votre base de données Azure SQL Database 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]


[Always Encrypted avec enclaves sécurisées](/sql/relational-databases/security/encryption/always-encrypted-enclaves) dans Azure SQL Database utilise des enclaves [Intel SGX (Software Guard Extensions)](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/). Pour qu’Intel SGX soit disponible, la base de données doit utiliser le [modèle vCore](service-tiers-vcore.md) et la génération matérielle de la [série DC](service-tiers-sql-database-vcore.md#dc-series).

La configuration de la génération de matériel de série DC pour activer des enclaves Intel SGX est la responsabilité de l’administrateur Azure SQL Database. Consultez [Rôles et responsabilités lors de la configuration des enclaves SGX et de l’attestation](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation).

> [!NOTE]
> Intel SGX n’est pas disponible dans les générations matérielles autres que la série DC. Par exemple, Intel SGX n’est pas disponible pour le matériel de 5e génération et n’est pas disponible pour les bases de données utilisant le [modèle DTU](service-tiers-dtu.md).

> [!IMPORTANT]
> Avant de configurer la génération matérielle de série DC pour votre base de données, vérifiez la disponibilité régionale de la série DC et assurez-vous que vous comprenez ses limites de performances. Pour plus d’informations, consultez [Série DC](service-tiers-sql-database-vcore.md#dc-series).

Pour obtenir des instructions détaillées sur la configuration d’une base de données nouvelle ou existante afin d’utiliser une génération matérielle spécifique, consultez [Choisir une génération de matériel](service-tiers-sql-database-vcore.md#selecting-a-hardware-generation).
   
## <a name="next-steps"></a>Étapes suivantes

- [Configurer Azure Attestation pour votre serveur Azure SQL Database](always-encrypted-enclaves-configure-attestation.md)

## <a name="see-also"></a>Voir aussi

- [Tutoriel : Bien démarrer avec Always Encrypted avec enclaves sécurisées dans Azure SQL Database](always-encrypted-enclaves-getting-started.md)