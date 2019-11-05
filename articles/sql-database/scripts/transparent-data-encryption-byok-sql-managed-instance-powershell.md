---
title: 'PowerShell : Activer TDE avec BYOK - Azure SQL Database Managed Instance | Microsoft Docs'
description: Découvrez comment configurer une entité Azure SQL Managed Instance pour qu’elle commence à utiliser Transparent Data Encryption (TDE) avec BYOK pour le chiffrement au repos à l’aide de PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 11/04/2019
ms.openlocfilehash: 0413216bc666aff504193d6723d46a6ee26be8ee
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500053"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Gérer Transparent Data Encryption dans une instance gérée à l’aide de votre propre clé Azure Key Vault

Cet exemple de script PowerShell configure Transparent Data Encryption (TDE) dans le scénario Bring Your Own Key (préversion) pour Azure SQL Managed Instance, à l’aide d’une clé Azure Key Vault. Pour en savoir plus sur la prise en charge TDE avec Bring Your Own Key (BYOK), consultez [Azure SQL Transparent Data Encryption : prise en charge du service Bring Your Own Key](../transparent-data-encryption-byok-azure-sql.md).

## <a name="prerequisites"></a>Prérequis

- Une instance managée existante. Consultez [Utiliser PowerShell pour créer une instance managée Azure SQL Database](sql-database-create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

L’utilisation de PowerShell localement ou d’Azure Cloud Shell requiert AZ PowerShell 1.1.1 en préversion ou une préversion ultérieure. Si une mise à niveau est nécessaire, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps), ou exécutez l’exemple de script ci-dessous pour installer le module.

`Install-Module -Name Az.Sql -RequiredVersion 1.1.1-preview -AllowPrerelease -Force`

Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzAccount` pour créer une connexion avec Azure.

## <a name="sample-scripts"></a>Exemples de scripts

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts SQL Database PowerShell dans [Scripts PowerShell Azure SQL Database](../sql-database-powershell-samples.md).
