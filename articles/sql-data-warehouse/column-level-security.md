---
title: Sécurité au niveau des colonnes dans Azure SQL Data Warehouse | Microsoft Docs
description: La sécurité au niveau des colonnes permet aux clients de contrôler l’accès aux colonnes de table de base de données selon le contexte d’exécution de l’utilisateur ou leur appartenance au groupe. La sécurité au niveau des colonnes simplifie la conception et le codage de la sécurité dans votre application. Elle vous permet d’implémenter des restrictions sur l’accès aux colonnes.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 06/15/2018
ms.author: kavithaj
ms.reviewer: igorstan, carlrab
ms.openlocfilehash: 1765c92ad10fa35af98e7c7314eb44c3a119f422
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43301053"
---
# <a name="column-level-security"></a>Sécurité au niveau des colonnes 
La sécurité au niveau des colonnes permet aux clients de contrôler l’accès aux colonnes de table de base de données selon le contexte d’exécution de l’utilisateur ou leur appartenance au groupe.  

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]

La sécurité au niveau des colonnes simplifie la conception et le codage de la sécurité dans votre application. Elle vous permet d’implémenter des restrictions sur l’accès aux colonnes pour protéger les données sensibles. Vous pouvez, par exemple, vous assurer que des utilisateurs spécifiques peuvent accéder uniquement à certaines colonnes d’une table qui sont pertinentes par rapport à leur service. La logique de restriction d’accès se situe dans la couche de base de données plutôt qu’à l’écart des données d’une autre couche d’application. La base de données applique les restrictions d’accès chaque fois qu’un accès aux données est tenté à partir d’un quelconque niveau. Cette restriction rend votre système de sécurité plus fiable et robuste en réduisant sa surface d’exposition. En outre, la sécurité au niveau des colonnes élimine la nécessité d’ajouter des vues pour filtrer les colonnes afin d’imposer des restrictions d’accès aux utilisateurs. 

Vous pouvez implémenter la sécurité au niveau des colonnes avec l’instruction T-SQL [GRANT](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql). Avec ce mécanisme, l’authentification SQL et AAD (Azure Active Directory) sont toutes deux prises en charge.

![sécurité au niveau des colonnes](./media/column-level-security/cls.png)

## <a name="syntax"></a>Syntaxe 

```sql
GRANT <permission> [ ,...n ] ON    
    [ OBJECT :: ][ schema_name ]. object_name [ ( column [ ,...n ] ) ]   
    TO <database_principal> [ ,...n ]    
    [ WITH GRANT OPTION ]   
    [ AS <database_principal> ]   
<permission> ::=   
    SELECT 
  | UPDATE  
<database_principal> ::=    
      Database_user    
    | Database_role    
    | Database_user_mapped_to_Windows_User    
    | Database_user_mapped_to_Windows_Group    
```

## <a name="example"></a>Exemples 
L’exemple suivant montre comment empêcher l’utilisateur TestUser d’accéder à la colonne SSN de la table Membership : 

Créez la table Membership avec la colonne SSN utilisée pour stocker les numéros de sécurité sociale :

```sql
CREATE TABLE Membership   
  (MemberID int IDENTITY,   
   FirstName varchar(100) NULL,   
   SSN char(9) NOT NULL, 
   LastName varchar(100) NOT NULL,   
   Phone varchar(12) NULL,   
   Email varchar(100) NULL);  
```

Autorisez TestUser à accéder à toutes les colonnes à l’exception de la colonne SSN qui contient des données sensibles : 

```sql  
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;   
``` 

Les requêtes exécutées sous le nom TestUser échouent si elles comprennent la colonne SSN :

```sql  
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'. 
``` 

## <a name="use-cases"></a>Cas d'utilisation
Voici quelques exemples d’utilisation de la sécurité au niveau des colonnes : 
- Une entreprise de services financiers autorise uniquement les gestionnaires de comptes à accéder aux numéros de sécurité sociale, numéros de téléphone et autres informations d’identification personnelle des clients.
- Un prestataire de soins de santé autorise uniquement les médecins et infirmières à accéder aux dossiers médicaux sensibles tout en interdisant aux membres du service de facturation de voir ces données.
