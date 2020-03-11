---
title: Accès conditionnel
description: Découvrez comment configurer l’accès conditionnel pour Azure SQL Database et Azure Synapse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sql-data-warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 02/06/2020
tag: azure-synpase
ms.openlocfilehash: f2431ee7c62079a3691a5ea99e562460df8f9309
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197570"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-azure-synapse-analytics"></a>Accès conditionnel (MFA) avec Azure SQL Database et Azure Synapse Analytics

Azure [SQL Database](sql-database-technical-overview.md), [Managed Instance](sql-database-managed-instance.md) et [Azure Synapse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) prennent tous deux en charge l’accès conditionnel Microsoft. 

> [!NOTE]
> Cette rubrique s’applique au serveur Azure SQL ainsi qu’aux instances de SQL Database et Azure Synapse créées sur le serveur Azure SQL. Par souci de simplicité, le nom « SQL Database » est utilisé pour faire référence à SQL Database et à Azure Synapse.

Les étapes suivantes montrent comment configurer SQL Database pour appliquer une stratégie d’accès conditionnel.  

## <a name="prerequisites"></a>Prérequis  
- Vous devez configurer SQL Database ou le pool SQL dans Azure Synapse pour prendre en charge l’authentification Azure Active Directory. Pour connaître la procédure spécifique, consultez [Configurer et gérer l’authentification Azure Active Directory avec SQL Database ou Azure Synapse](sql-database-aad-authentication-configure.md).  
- Quand Multi-Factor Authentication est activé, vous devez vous connecter avec un outil pris en charge, tel que la dernière version de SSMS. Pour plus d’informations, consultez [Configurer l’authentification multifacteur Azure SQL Database pour SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Configurer l’accès conditionnel pour Azure SQL DB/DW  
1. Connectez-vous au portail, sélectionnez **Azure Active Directory**, puis **Accès conditionnel**. Pour plus d’informations, consultez [Référence technique de l’accès conditionnel Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
   ![Panneau d’accès conditionnel](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2. Dans le panneau des **stratégies d’accès conditionnel**, cliquez sur **Nouvelle stratégie**, fournissez un nom, puis cliquez sur **Configure rules** (Configurer les règles).  
3. Sous **Affectations**, sélectionnez **Utilisateurs et groupes**, cochez **Sélectionner des utilisateurs et des groupes**, puis sélectionnez l’utilisateur ou le groupe pour l’accès conditionnel. Cliquez sur **Sélectionner**, puis sur **Terminé** pour valider la sélection.  
   ![sélectionner des utilisateurs et des groupes](./media/sql-database-conditional-access/select-users-and-groups.png)  

4. Sélectionnez **Applications cloud**, puis cliquez sur **Sélectionner des applications**. Vous voyez toutes les applications disponibles pour l’accès conditionnel. Sélectionnez **Azure SQL Database**, cliquez en bas sur **Sélectionner**, puis sur **Terminé**.  
   ![sélectionner SQL Database](./media/sql-database-conditional-access/select-sql-database.png)  
   Si vous ne trouvez pas **Azure SQL Database** répertorié dans la troisième capture d’écran ci-dessous, effectuez les étapes suivantes :   
   - Connectez-vous à votre instance Azure SQL DB/DW à l’aide de SSMS avec un compte d’administrateur AAD.  
   - Exécutez `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
   - Connectez-vous à AAD et vérifiez qu’Azure SQL Database et Azure Synapse sont listés dans AAD.  

5. Sélectionnez **Contrôles d’accès**, **Accorder**, puis cochez la stratégie que vous souhaitez appliquer. Pour cet exemple, nous sélectionnons **Imposer l’authentification multifacteur**.  
   ![sélectionner Accorder l’accès](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Résumé  
L’application sélectionnée (Azure SQL Database), qui permet de se connecter à Azure SQL DB/DW à l’aide d’Azure AD Premium, applique maintenant la stratégie d’accès conditionnel sélectionnée **Imposer l’authentification multifacteur**.  
Pour toute question sur Azure SQL Database et Azure Synapse concernant l’authentification multifacteur, contactez MFAforSQLDB@microsoft.com.  

## <a name="next-steps"></a>Étapes suivantes  

Pour un tutoriel, consultez [Sécuriser votre base de données Azure SQL](sql-database-security-tutorial.md).
