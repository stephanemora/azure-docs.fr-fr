---
title: Qu’est-ce que la sécurité au niveau des colonnes pour Azure Synapse ?
description: La sécurité au niveau des colonnes permet aux clients de contrôler l'accès aux colonnes des tables de base de données en fonction du contexte d'exécution de l'utilisateur ou de son appartenance à un groupe, ce qui simplifie la conception et le codage de la sécurité de votre application et vous permet d'implémenter des restrictions d'accès aux colonnes.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/19/2020
ms.author: jrasnick
ms.reviewer: igorstan, carlrab
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: b0a783ad5db86ca783ff1cebceec8d77ab528047
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81687918"
---
# <a name="column-level-security"></a>Sécurité au niveau des colonnes

La sécurité au niveau des colonnes permet aux clients de contrôler l'accès aux colonnes des tables de base de données en fonction du contexte d'exécution de l'utilisateur ou de son appartenance à un groupe.

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
Depuis la publication de cette vidéo, la [sécurité au niveau des lignes](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) a également été mise en place pour Azure Synapse.

La sécurité au niveau des colonnes simplifie la conception et le codage de la sécurité de votre application, ce qui vous permet de limiter l'accès aux colonnes afin de protéger les données sensibles. Vous pouvez, par exemple, vous assurer que des utilisateurs spécifiques peuvent accéder uniquement à certaines colonnes d’une table qui sont pertinentes par rapport à leur service. La logique de la restriction d'accès est située dans la couche de base de données plutôt que loin des données d'une autre couche Application. La base de données applique les restrictions d'accès à chaque tentative d'accès aux données à partir d'un niveau quelconque. Cette restriction renforce la fiabilité et la robustesse de votre sécurité en réduisant la surface d'exposition de votre système de sécurité global. En outre, la sécurité au niveau des colonnes élimine la nécessité d'ajouter des vues pour filtrer les colonnes afin d'imposer des restrictions d'accès aux utilisateurs.

Vous pouvez implémenter la sécurité au niveau des colonnes à l'aide de l'instruction T-SQL [GRANT](/sql/t-sql/statements/grant-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). Avec ce mécanisme, l’authentification SQL et AAD (Azure Active Directory) sont toutes deux prises en charge.

![sécurité au niveau des colonnes](./media/column-level-security/cls.png)

## <a name="syntax"></a>Syntaxe

```syntaxsql
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

## <a name="example"></a>Exemple

L'exemple suivant montre comment empêcher l'utilisateur `TestUser` d'accéder à la colonne `SSN` de la table `Membership` :

Créez la table `Membership` avec la colonne SSN utilisée pour stocker les numéros de sécurité sociale :

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

Autorisez `TestUser` à accéder à toutes les colonnes à l'exception de la colonne SSN qui contient les données sensibles :

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

Les requêtes exécutées sous le nom `TestUser` échouent si elles comprennent la colonne SSN :

```sql
SELECT * FROM Membership;

-- Msg 230, Level 14, State 1, Line 12
-- The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Cas d'utilisation

Exemples d'utilisation actuelle de la sécurité au niveau des colonnes :

- Une entreprise de services financiers autorise uniquement les gestionnaires de comptes à accéder aux numéros de sécurité sociale, numéros de téléphone et autres informations d’identification personnelle des clients.
- Un prestataire de soins de santé autorise uniquement les médecins et infirmières à accéder aux dossiers médicaux sensibles tout en empêchant les membres du service de facturation de consulter ces données.
