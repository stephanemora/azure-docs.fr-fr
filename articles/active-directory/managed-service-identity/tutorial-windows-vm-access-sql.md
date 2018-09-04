---
title: Utiliser une machine virtuelle Windows pour accéder à Azure SQL
description: Ce didacticiel vous guide tout au long du processus d’utilisation d’une MSI (Managed Service Identity) de machine virtuelle Windows pour accéder à Azure SQL.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: bryanla
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: b6b2985bf72d9ecb2041d51852b5a4230e11d8be
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42886051"
---
# <a name="tutorial-use-a-windows-vm-managed-service-identity-to-access-azure-sql"></a>Didacticiel : Utiliser une MSI (Managed Service Identity) de machine virtuelle Windows pour accéder à Azure SQL

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Ce didacticiel vous indique comment utiliser une identité affectée par le système pour une machine virtuelle Windows afin d’accéder à un serveur Azure SQL. Les identités MSI sont gérées automatiquement par Azure et vous permettent de vous authentifier auprès des services prenant en charge l’authentification Azure AD sans avoir à insérer des informations d’identification dans votre code. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Accorder à votre machine virtuelle l’accès à un serveur Azure SQL
> * Créer un groupe dans Azure AD et faire de la MSI de machine virtuelle un membre du groupe
> * Activer Azure AD Authentication pour le serveur SQL
> * Créer un utilisateur contenu dans la base de données représentant le groupe Azure AD
> * Obtenir un jeton d’accès à l’aide de l’identité de machine virtuelle, et l’utiliser pour interroger un serveur Azure SQL

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [Connectez-vous au Portail Azure](https://portal.azure.com).

- [Créez une machine virtuelle Windows](/azure/virtual-machines/windows/quick-create-portal).

- [Activez l’identité affectée par le système sur votre machine virtuelle](/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm#enable-system-assigned-identity-on-an-existing-vm).

## <a name="grant-your-vm-access-to-a-database-in-an-azure-sql-server"></a>Accorder à votre machine virtuelle l’accès à une base de données sur un serveur Azure SQL

Vous pouvez désormais accorder à votre machine virtuelle l’accès à une base de données sur un serveur Azure SQL.  Pour cette étape, vous pouvez utiliser un serveur SQL existant ou en créer un.  Pour créer un serveur et une base de données à l’aide du portail Azure, procédez de la manière décrite dans cette [procédure de démarrage rapide d’Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal). Des procédures de démarrage rapide utilisant Azure CLI et Azure PowerShell sont également décrites dans la [Documentation Azure SQL Database](https://docs.microsoft.com/azure/sql-database/).

Pour accorder à votre machine virtuelle l’accès à une base de données, trois étapes sont nécessaires :
1.  Créez un groupe dans Azure AD et faites de la MSI de machine virtuelle un membre du groupe.
2.  Activez Azure AD Authentication pour le serveur SQL.
3.  Créez un **utilisateur contenu** dans la base de données représentant le groupe Azure AD.

> [!NOTE]
> Normalement, vous devriez créer un utilisateur contenu qui mappe directement à la MSI de la machine virtuelle.  À l’heure actuelle, Azure SQL ne permet pas au principal du service Azure AD, qui représente la MSI de machine virtuelle, d’être mappé à un utilisateur contenu.  En guise de solution de contournement prise en charge, vous faites de la MSI de machine virtuelle un membre d’un groupe Azure AD, puis vous créez un utilisateur contenu dans la base de données représentant le groupe.


## <a name="create-a-group-in-azure-ad-and-make-the-vm-managed-service-identity-a-member-of-the-group"></a>Créer un groupe dans Azure AD et faire de la MSI de machine virtuelle un membre du groupe

Vous pouvez utiliser un groupe Azure AD existant ou en créer un à l’aide d’Azure AD PowerShell.  

Commencez par installer le module [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2). Ensuite, connectez-vous à l’aide de l’applet de commande `Connect-AzureAD`, puis exécutez la commande suivante pour créer le groupe et l’enregistrer dans une variable :

```powershell
$Group = New-AzureADGroup -DisplayName "VM Managed Service Identity access to SQL" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
```

La sortie ressemble à la suivante, qui examine également la valeur de la variable :

```powershell
$Group = New-AzureADGroup -DisplayName "VM Managed Service Identity access to SQL" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
$Group
ObjectId                             DisplayName          Description
--------                             -----------          -----------
6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 VM Managed Service Identity access to SQL
```

Ensuite, ajoutez la MSI de la machine virtuelle au groupe.  Vous avez besoin de l’**ObjectId** de la MSI, que vous pouvez obtenir à l’aide d’Azure PowerShell.  Commencez par télécharger [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Ensuite, connectez-vous à l’aide de l’applet de commande `Connect-AzureRmAccount`, puis exécutez les commandes suivantes pour :
- vérifier que votre contexte de session est défini sur l’abonnement Azure souhaité si vous en avez plusieurs ;
- répertorier les ressources disponibles dans votre abonnement Azure, et vérifier que les noms de groupe de ressources et de machine virtuelle sont corrects ;
- Obtenez les propriétés de la MSI de la machine virtuelle en utilisant les valeurs appropriées pour `<RESOURCE-GROUP>` et `<VM-NAME>`.

```powershell
Set-AzureRMContext -subscription "bdc79274-6bb9-48a8-bfd8-00c140fxxxx"
Get-AzureRmResource
$VM = Get-AzureRmVm -ResourceGroup <RESOURCE-GROUP> -Name <VM-NAME>
```

La sortie ressemble à la suivante, qui examine également l’ID d’objet de principal du service de la MSI de la machine virtuelle :
```powershell
$VM = Get-AzureRmVm -ResourceGroup DevTestGroup -Name DevTestWinVM
$VM.Identity.PrincipalId
b83305de-f496-49ca-9427-e77512f6cc64
```

Ensuite, ajoutez la MSI de la machine virtuelle au groupe.  Vous pouvez uniquement ajouter un principal du service à un groupe à l’aide d’Azure AD PowerShell.  Exécutez cette commande :
```powershell
Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId $VM.Identity.PrincipalId
```

Si vous examinez également l’appartenance au groupe par la suite, la sortie ressemble à la suivante :

```powershell
Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId $VM.Identity.PrincipalId
Get-AzureAdGroupMember -ObjectId $Group.ObjectId

ObjectId                             AppId                                DisplayName
--------                             -----                                -----------
b83305de-f496-49ca-9427-e77512f6cc64 0b67a6d6-6090-4ab4-b423-d6edda8e5d9f DevTestWinVM
```

## <a name="enable-azure-ad-authentication-for-the-sql-server"></a>Activer Azure AD Authentication pour le serveur SQL

À présent que vous avez créé le groupe et ajouté la MSI de machine virtuelle en tant que membre du groupe, vous pouvez [configurer Azure AD Authentication pour le serveur SQL](/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-azure-sql-server) en procédant comme suit :

1.  Dans le portail Azure, dans le volet de navigation gauche, sélectionnez **Serveurs SQL**.
2.  Cliquez sur le serveur SQL à activer pour Azure AD Authentication.
3.  Dans la section **Paramètres** du panneau, cliquez sur **Administrateur Active Directory**.
4.  Dans la barre de commandes, cliquez sur **Définir l’administrateur**.
5.  Sélectionnez un compte d’utilisateur Azure AD à désigner comme administrateur du serveur, puis cliquez sur **Sélectionner**.
6.  Dans la barre de commandes, cliquez sur **Enregistrer**.

## <a name="create-a-contained-user-in-the-database-that-represents-the-azure-ad-group"></a>Créer un utilisateur contenu dans la base de données représentant le groupe Azure AD

Pour cette nouvelle étape, vous avez besoin de [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). Avant de commencer, il peut également être utile de lire les articles suivants pour acquérir une compréhension de l’intégration d’Azure AD :

- [Authentification universelle avec SQL Database et SQL Data Warehouse (prise en charge de SSMS pour MFA)](/azure/sql-database/sql-database-ssms-mfa-authentication)
- [Configurer et gérer l’authentification Azure Active Directory avec SQL Database ou SQL Data Warehouse](/azure/sql-database/sql-database-aad-authentication-configure)

1.  Exécutez SQL Server Management Studio.
2.  Dans la boîte de dialogue **Se connecter au serveur**, dans le champ **Nom du serveur**, entrez le nom de votre serveur SQL.
3.  Dans le champ **Authentification**, sélectionnez **Active Directory - Authentification universelle avec prise en charge de MFA**.
4.  Dans le champ **Nom d’utilisateur**, entrez le nom du compte Azure AD que vous avez défini en tant qu’administrateur du serveur, par exemple,helen@woodgroveonline.com.
5.  Cliquez sur **Options**.
6.  Dans le champ **Connexion à une base de données**, entrez le nom de la base de données non-système que vous souhaitez configurer.
7.  Cliquez sur **Connecter**.  Terminez le processus de connexion.
8.  Dans l’**Explorateur d’objets**, développez le dossier **Bases de données**.
9.  Cliquez avec le bouton droit sur une base de données utilisateur, puis cliquez sur **Nouvelle requête**.
10.  Dans la fenêtre de requête, entrez la ligne suivante, puis, dans la barre d’outils, cliquez sur **Exécuter** :
    
     ```
     CREATE USER [VM Managed Service Identity access to SQL] FROM EXTERNAL PROVIDER
     ```
    
     La commande doit s’exécuter correctement, en créant l’utilisateur contenu pour le groupe.
11.  Effacez le contenu de la fenêtre de requête, entrez la ligne suivante, puis, dans la barre d’outils, cliquez sur **Exécuter** :
     
     ```
     ALTER ROLE db_datareader ADD MEMBER [VM Managed Service Identity access to SQL]
     ```

     La commande doit s’exécuter correctement, en octroyant à l’utilisateur contenu la possibilité de lire la base de données entière.

Le code qui s’exécute dans la machine virtuelle peut désormais obtenir un jeton à partir de la MSI et utiliser celui-ci pour s’authentifier auprès du serveur SQL.

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-sql"></a>Obtenir un jeton d’accès à l’aide de l’identité de machine virtuelle, et l’utiliser pour appeler Azure SQL 

Azure SQL prenant en charge Azure AD Authentication en mode natif, il peut accepter directement des jetons d’accès obtenus à l’aide de la MSI.  Vous utilisez la méthode de **jeton d’accès** pour créer une connexion à SQL.  Cela fait partie de l’intégration d’Azure SQL avec Azure AD, et diffère de la fourniture d’informations d’identification sur la chaîne de connexion.

Voici un exemple de code .Net pour l’ouverture d’une connexion à SQL à l’aide d’un jeton d’accès.  Pour permettre l’accès au point de terminaison de la MSI de machine virtuelle, ce code doit s’exécuter sur la machine virtuelle.  Pour pouvoir utiliser la méthode de jeton d’accès, **.NET framework 4.6** ou version ultérieure est requis.  Remplacez les valeurs AZURE-SQL-SERVERNAME et DATABASE en conséquence.  Notez l’ID de ressource pour SQL Azure est « https://database.windows.net/ ».

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
    // Call Managed Service Identity endpoint.
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
4.  À l’aide de `Invoke-WebRequest` de Powershell, adressez une requête au point de terminaison de la MSI locale pour obtenir un jeton d’accès pour Azure SQL.

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

5.  Ouvrez une connexion au serveur SQL. N’oubliez pas de remplacer les valeurs de AZURE-SQL-SERVERNAME et DATABASE.
    
    ```powershell
    $SqlConnection = New-Object System.Data.SqlClient.SqlConnection
    $SqlConnection.ConnectionString = "Data Source = <AZURE-SQL-SERVERNAME>; Initial Catalog = <DATABASE>"
    $SqlConnection.AccessToken = $AccessToken
    $SqlConnection.Open()
    ```

    Ensuite, créez et envoyez une requête au serveur.  N’oubliez pas de remplacer la valeur de TABLE.

    ```powershell
    $SqlCmd = New-Object System.Data.SqlClient.SqlCommand
    $SqlCmd.CommandText = "SELECT * from <TABLE>;"
    $SqlCmd.Connection = $SqlConnection
    $SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $SqlAdapter.SelectCommand = $SqlCmd
    $DataSet = New-Object System.Data.DataSet
    $SqlAdapter.Fill($DataSet)
    ```

Examinez la valeur de `$DataSet.Tables[0]` pour voir les résultats de la requête.  Félicitations, vous avez interrogé la base de données à l’aide d’une MSI de machine virtuelle sans avoir eu besoin de fournir d’informations d’identification.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à créer une identité de service managée pour accéder à un serveur Azure SQL.  Pour en savoir plus sur Azure SQL Server, consultez :

> [!div class="nextstepaction"]
>[Service Base de données SQL Azure](/azure/sql-database/sql-database-technical-overview)
