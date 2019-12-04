---
title: Tutoriel `:` Utiliser une identité managée pour accéder à Azure SQL - Windows - Azure AD
description: Ce didacticiel vous guide tout au long de l’utilisation d’une identité managée attribuée par le système de machine virtuelle Windows pour accéder à Azure SQL.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: bryanla
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/16/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0a743df545450f87a01785f6f8a15fe08b8eafe
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74181177"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-sql"></a>Tutoriel : Utiliser une identité managée de machine virtuelle Windows attribuée par le système pour accéder à Azure SQL

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Ce didacticiel vous indique comment utiliser une identité attribuée par le système pour une machine virtuelle Windows afin d’accéder à un serveur Azure SQL. Les identités MSI sont gérées automatiquement par Azure et vous permettent de vous authentifier auprès des services prenant en charge l’authentification Azure AD sans avoir à insérer des informations d’identification dans votre code. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Accorder à votre machine virtuelle l’accès à un serveur Azure SQL
> * Activer Azure AD Authentication pour le serveur SQL
> * Créer un utilisateur contenu dans la base de données représentant l’identité attribuée du système de la machine virtuelle
> * Obtenir un jeton d’accès à l’aide de l’identité de machine virtuelle, et l’utiliser pour interroger un serveur Azure SQL

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="grant-your-vm-access-to-a-database-in-an-azure-sql-server"></a>Accorder à votre machine virtuelle l’accès à une base de données sur un serveur Azure SQL

Pour accorder à votre machine virtuelle l’accès à un serveur SQL Azure, vous pouvez utiliser un serveur SQL existant ou en créer un. Pour créer un serveur et une base de données à l’aide du portail Azure, procédez de la manière décrite dans cette [procédure de démarrage rapide d’Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal). Des procédures de démarrage rapide utilisant Azure CLI et Azure PowerShell sont également décrites dans la [Documentation Azure SQL Database](https://docs.microsoft.com/azure/sql-database/).

Pour accorder à votre machine virtuelle l’accès à une base de données, deux étapes sont nécessaires :

1. Activez Azure AD Authentication pour le serveur SQL.
2. Créez un **utilisateur contenu** dans la base de données représentant l’identité attribuée du système de la machine virtuelle.

## <a name="enable-azure-ad-authentication-for-the-sql-server"></a>Activer Azure AD Authentication pour le serveur SQL

[Configurez l’authentification Azure AD pour le serveur SQL](/azure/sql-database/sql-database-aad-authentication-configure) en suivant les étapes suivantes :

1.  Dans le portail Azure, dans le volet de navigation gauche, sélectionnez **Serveurs SQL**.
2.  Cliquez sur le serveur SQL à activer pour Azure AD Authentication.
3.  Dans la section **Paramètres** du panneau, cliquez sur **Administrateur Active Directory**.
4.  Dans la barre de commandes, cliquez sur **Définir l’administrateur**.
5.  Sélectionnez un compte d’utilisateur Azure AD à désigner comme administrateur du serveur, puis cliquez sur **Sélectionner**.
6.  Dans la barre de commandes, cliquez sur **Enregistrer**.

## <a name="create-a-contained-user-in-the-database-that-represents-the-vms-system-assigned-identity"></a>Créer un utilisateur contenu dans la base de données représentant l’identité attribuée du système de la machine virtuelle

Pour cette nouvelle étape, vous avez besoin de [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). Avant de commencer, il peut également être utile de lire les articles suivants pour acquérir une compréhension de l’intégration d’Azure AD :

- [Authentification universelle avec SQL Database et SQL Data Warehouse (prise en charge de SSMS pour MFA)](/azure/sql-database/sql-database-ssms-mfa-authentication)
- [Configurer et gérer l’authentification Azure Active Directory avec SQL Database ou SQL Data Warehouse](/azure/sql-database/sql-database-aad-authentication-configure)

SQL DB requiert des noms d’affichage AAD uniques. Ainsi, les comptes AAD comme utilisateurs, groupes et principaux de service (applications), et noms des machines virtuelles activées pour l’identité managée doivent être définis de façon unique dans AAD en termes de noms d'affichage. SQL DB vérifie le nom d’affichage AAD lors de la création T-SQL de tels utilisateurs, et s'il n’est pas unique, la commande ne permet pas la mise à disposition d'un nom d’affichage AAD unique pour un compte donné.

1. Exécutez SQL Server Management Studio.
2. Dans la boîte de dialogue **Se connecter au serveur**, dans le champ **Nom du serveur**, entrez le nom de votre serveur SQL.
3. Dans le champ **Authentification**, sélectionnez **Active Directory - Authentification universelle avec prise en charge de MFA**.
4. Dans le champ **Nom d’utilisateur**, entrez le nom du compte Azure AD que vous avez défini en tant qu’administrateur du serveur, par exemple,helen@woodgroveonline.com.
5. Cliquez sur **Options**.
6. Dans le champ **Connexion à une base de données**, entrez le nom de la base de données non-système que vous souhaitez configurer.
7. Cliquez sur **Connecter**. Terminez le processus de connexion.
8. Dans l’**Explorateur d’objets**, développez le dossier **Bases de données**.
9. Cliquez avec le bouton droit sur une base de données utilisateur, puis cliquez sur **Nouvelle requête**.
10. Dans la fenêtre de requête, entrez la ligne suivante, puis, dans la barre d’outils, cliquez sur **Exécuter** :

    > [!NOTE]
    > `VMName` dans la commande suivante est le nom de la machine virtuelle pour laquelle vous avez activé l’identité attribuée au système sur dans la section Conditions préalables.
    
    ```
    CREATE USER [VMName] FROM EXTERNAL PROVIDER
    ```
    
    La commande doit s’exécuter correctement, en créant l’utilisateur contenu pour l’identité attribuée du système de la machine virtuelle.
11. Effacez le contenu de la fenêtre de requête, entrez la ligne suivante, puis, dans la barre d’outils, cliquez sur **Exécuter** :

    > [!NOTE]
    > `VMName` dans la commande suivante est le nom de la machine virtuelle pour laquelle vous avez activé l’identité attribuée au système sur dans la section Conditions préalables.
    
    ```
    ALTER ROLE db_datareader ADD MEMBER [VMName]
    ```

    La commande doit s’exécuter correctement, en octroyant à l’utilisateur contenu la possibilité de lire la base de données entière.

Le code qui s’exécute dans la machine virtuelle peut désormais obtenir un jeton à partir de son identité managée attribuée par le système et utiliser celui-ci pour s’authentifier auprès du serveur SQL.

## <a name="get-an-access-token-using-the-vms-system-assigned-managed-identity-and-use-it-to-call-azure-sql"></a>Obtenir un jeton d’accès par l’identité managée attribuée par le système de machine virtuelle et l’utiliser pour appeler Azure SQL

Azure SQL prenant en charge Azure AD Authentication en mode natif, il peut accepter directement des jetons d’accès obtenus à l’aide d’identités managées attribuées par le système pour les ressources Azure. Vous utilisez la méthode de **jeton d’accès** pour créer une connexion à SQL. Cela fait partie de l’intégration d’Azure SQL avec Azure AD, et diffère de la fourniture d’informations d’identification sur la chaîne de connexion.

Voici un exemple de code .NET pour l’ouverture d’une connexion à SQL à l’aide d’un jeton d’accès. Pour permettre l’accès au point de terminaison de l’identité managée attribuée par le système de machine virtuelle, ce code doit s’exécuter sur la machine virtuelle. Pour pouvoir utiliser la méthode de jeton d’accès, **.NET framework 4.6** ou version ultérieure ou **.NET Core 2.2** ou version ultérieure est requis. Remplacez les valeurs AZURE-SQL-SERVERNAME et DATABASE en conséquence. Notez l’ID de ressource pour SQL Azure est `https://database.windows.net/`.

```csharp
using System.Net;
using System.IO;
using System.Data.SqlClient;
using System.Web.Script.Serialization;

//
// Get an access token for SQL.
//
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://database.windows.net/");
request.Headers["Metadata"] = "true";
request.Method = "GET";
string accessToken = null;

try
{
    // Call managed identities for Azure resources endpoint.
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader and extract access token.
    StreamReader streamResponse = new StreamReader(response.GetResponseStream());
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

//
// Open a connection to the SQL server using the access token.
//
if (accessToken != null) {
    string connectionString = "Data Source=<AZURE-SQL-SERVERNAME>; Initial Catalog=<DATABASE>;";
    SqlConnection conn = new SqlConnection(connectionString);
    conn.AccessToken = accessToken;
    conn.Open();
}
```

Un autre moyen rapide de tester la configuration de bout en bout sans devoir écrire et déployer une application sur la machine virtuelle consiste à utiliser PowerShell.

1.  Dans le portail, accédez à **Machines virtuelles** et accédez à votre machine virtuelle Windows. Puis, dans **Vue d’ensemble**, cliquez sur **Connecter**.
2.  Entrez le **Nom d’utilisateur** et le **Mot de passe** que vous avez ajoutés lorsque vous avez créé la machine virtuelle Windows.
3.  Maintenant que vous avez créé une **Connexion au Bureau à distance** avec la machine virtuelle, ouvrez **PowerShell** dans la session à distance.
4.  À l’aide de `Invoke-WebRequest` de Powershell, adressez une requête au point de terminaison de l’identité managée locale pour obtenir un jeton d’accès pour Azure SQL.

    ```powershell
        $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatabase.windows.net%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    Convertissez la réponse d’objet JSON en objet PowerShell. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```

    Extrayez le jeton d’accès de la réponse.
    
    ```powershell
    $AccessToken = $content.access_token
    ```

5. Ouvrez une connexion au serveur SQL. N’oubliez pas de remplacer les valeurs de AZURE-SQL-SERVERNAME et DATABASE.
    
    ```powershell
    $SqlConnection = New-Object System.Data.SqlClient.SqlConnection
    $SqlConnection.ConnectionString = "Data Source = <AZURE-SQL-SERVERNAME>; Initial Catalog = <DATABASE>"
    $SqlConnection.AccessToken = $AccessToken
    $SqlConnection.Open()
    ```

    Ensuite, créez et envoyez une requête au serveur. N’oubliez pas de remplacer la valeur de TABLE.

    ```powershell
    $SqlCmd = New-Object System.Data.SqlClient.SqlCommand
    $SqlCmd.CommandText = "SELECT * from <TABLE>;"
    $SqlCmd.Connection = $SqlConnection
    $SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $SqlAdapter.SelectCommand = $SqlCmd
    $DataSet = New-Object System.Data.DataSet
    $SqlAdapter.Fill($DataSet)
    ```

Examinez la valeur de `$DataSet.Tables[0]` pour voir les résultats de la requête.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à utiliser une identité managée attribuée par le système pour accéder à Azure SQL Server. Pour en savoir plus sur Azure SQL Server, consultez :

> [!div class="nextstepaction"]
> [Service Azure SQL Database](/azure/sql-database/sql-database-technical-overview)
