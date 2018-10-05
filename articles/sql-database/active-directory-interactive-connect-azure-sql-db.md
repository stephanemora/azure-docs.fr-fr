---
title: Se connecter à SQL en mode ActiveDirectoryInteractive | Microsoft Docs
description: Exemple de code C# accompagné d’explications, pour établir une connexion à Azure SQL Database en utilisant le mode SqlAuthenticationMethod.ActiveDirectoryInteractive.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: active directory
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: MirekS
ms.reviewer: GeneMi
ms.date: 04/06/2018
manager: craigg
ms.openlocfilehash: 80944e73f21d75943d4fa71c7ac9500e47bab250
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47055524"
---
# <a name="use-activedirectoryinteractive-mode-to-connect-to-azure-sql-database"></a>Utiliser le mode ActiveDirectoryInteractive pour se connecter à Azure SQL Database

Cet article contient un exemple de code C# exécutable qui vous permet de vous connecter à Microsoft Azure SQL Database. Le programme C# utilise le mode interactif d’authentification, qui prend en charge l’authentification multifacteur Azure (MFA). Par exemple, une tentative de connexion peut inclure un code de vérification envoyé sur votre téléphone mobile.

Pour plus d’informations sur la prise en charge de l’authentification multifacteur (MFA) pour les outils SQL, consultez [Prise en charge d’Azure Active Directory dans SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory).




## <a name="sqlauthenticationmethod-activedirectoryinteractive-enum-value"></a>Valeur enum .ActiveDirectoryInteractive de SqlAuthenticationMethod

Depuis .NET Framework version 4.7.2, l’énumération [**SqlAuthenticationMethod**](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) possède une nouvelle valeur **.ActiveDirectoryInteractive**. Lorsqu’elle est utilisée par un programme client C#, cette valeur enum indique au système d’utiliser le mode interactif d’Azure AD prenant en charge MFA pour l’authentification auprès d’Azure SQL Database. L’utilisateur qui exécute le programme voit alors les boîtes de dialogue suivantes :

1. Une boîte de dialogue qui affiche un nom d’utilisateur Azure AD et qui demande le mot de passe de l’utilisateur Azure AD.
    - Cette boîte de dialogue ne s’affiche pas si aucun mot de passe n’est nécessaire. Aucun mot de passe n’est nécessaire si le domaine de l’utilisateur est fédéré avec Azure AD.

    Si l’authentification multifacteur est imposée à l’utilisateur par la stratégie définie dans Azure AD, les boîtes de dialogue suivantes s’affichent ensuite.

2. Ce n’est que la toute première fois que l’utilisateur rencontre le scénario de l’authentification multifacteur et que le système affiche une boîte de dialogue supplémentaire. Cette boîte de dialogue demande un numéro de téléphone mobile auquel les messages texte seront envoyés. Chaque message contient le *code de vérification* que l’utilisateur doit entrer dans la boîte de dialogue suivante.

3. Une autre boîte de dialogue qui demande le code de vérification MFA, que le système a envoyé sur un téléphone mobile.

Pour plus d’informations sur la configuration d’Azure AD pour demander l’authentification multifacteur, consultez [Prise en main avec Azure Multi-Factor Authentication dans le cloud](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud).

Pour consulter les captures d’écran de ces boîtes de dialogue, consultez [Configurer l’authentification multifacteur pour SQL Server Management Studio et Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!TIP]
> Notre page de recherche générale qui permet de rechercher tous les types d’API .NET Framework est disponible à l’adresse suivante avec notre outil **Navigateur d’API .NET** très pratique :
>
> [https://docs.microsoft.com/dotnet/api/](https://docs.microsoft.com/dotnet/api/)
>
> En ajoutant le nom de type au paramètre facultatif ajouté **?term=**, nous pouvons mettre à notre disposition la page de recherche contenant notre résultat :
>
> [https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod)


## <a name="preparations-for-c-by-using-the-azure-portal"></a>Préparations pour le langage C# à l’aide du portail Azure

Nous partons du principe que vous avez déjà [créé un serveur Azure SQL Database](sql-database-get-started-portal.md) et qu’il est disponible.


### <a name="a-create-an-app-registration"></a>R. Créer une inscription d’application

Pour utiliser l’authentification Azure AD, votre programme client C# doit fournir un GUID en tant *qu’ID client* lorsque votre programme tente de se connecter. L’achèvement de l’inscription d’une application génère et affiche le GUID dans le portail Azure, appelé **ID d’application**. Les étapes de navigation sont les suivantes :

1. Portail Azure &gt; **Azure Active Directory** &gt; **Inscription d’application**

    ![Inscription d'application](media\active-directory-interactive-connect-azure-sql-db\sshot-create-app-registration-b20.png)

2. La valeur **ID d’application** est générée et affichée.

    ![ID d’application affiché](media\active-directory-interactive-connect-azure-sql-db\sshot-application-id-app-regis-mk49.png)

3. **Application inscrite** &gt; **Paramètres** &gt; **Autorisations requises** &gt; **Ajouter**

    ![Paramètres des autorisations de l’application inscrite](media\active-directory-interactive-connect-azure-sql-db\sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

4. **Autorisations requises** &gt; **Ajouter un accès d’API** &gt; **Sélectionner une API** &gt; **Azure SQL Database**

    ![Ajouter un accès à l’API d’Azure SQL Database](media\active-directory-interactive-connect-azure-sql-db\sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

5. **Accès à l’API** &gt; **Sélectionner des autorisations** &gt; **Autorisations déléguées**

    ![Déléguer des autorisations pour l’API d’Azure SQL Database](media\active-directory-interactive-connect-azure-sql-db\sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)


### <a name="b-set-azure-ad-admin-on-your-sql-database-server"></a>B. Définir un administrateur Azure AD sur votre serveur Azure SQL Database

Chaque serveur Azure SQL Database possède son propre serveur logique SQL d’Azure AD. Pour notre scénario relatif au langage C#, vous devez définir un administrateur Azure AD pour votre serveur SQL Azure.

1. **SQL Server** &gt; **Administrateur Active Directory** &gt; **Définir l’administrateur**

    - Pour plus d’informations sur les administrateurs Azure AD et les utilisateurs d’Azure SQL Database, consultez les captures d’écran de l’article [Configurer et gérer l’authentification Azure Active Directory avec SQL Database, Managed Instance ou SQL Data Warehouse](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server), section **Approvisionner un administrateur Azure Active Directory pour votre serveur Azure SQL Database**.


### <a name="c-prepare-an-azure-ad-user-to-connect-to-a-specific-database"></a>C. Préparer un utilisateur Azure AD à se connecter à une base de données spécifique

Dans l’instance d’Azure AD propre à votre serveur Azure SQL Database, vous pouvez ajouter un utilisateur qui aura accès à une base de données particulière.

Pour plus d’informations, consultez [Utiliser l’authentification Azure Active Directory pour l’authentification auprès de SQL Database, de Managed Instance ou de SQL Data Warehouse](sql-database-aad-authentication.md).


### <a name="d-add-a-non-admin-user-to-azure-ad"></a>D. Ajouter un utilisateur non administrateur à Azure AD

L’administrateur Azure AD du serveur Azure SQL Database vous permet de vous connecter à votre serveur Azure SQL Database. Toutefois, il est conseillé plus généralement d’ajouter un utilisateur non administrateur à Azure AD. Lorsque l’utilisateur non administrateur se connecte, la séquence de l’authentification multifacteur est appelée si l’authentification MFA est imposée pour cet utilisateur par Azure AD.




## <a name="azure-active-directory-authentication-library-adal"></a>Azure ADAL (Active Directory Authentication Library)

Le programme C# utilise l’espace de noms **Microsoft.IdentityModel.Clients.ActiveDirectory**. Les classes de cet espace de noms se trouvent dans l’assembly portant le même nom.

- Utilisez NuGet pour télécharger et installer l’assembly ADAL.
    - [https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)

- Ajoutez une référence à l’assembly pour prendre en charge une compilation du programme C#.




## <a name="sqlauthenticationmethod-enum"></a>Enum SqlAuthenticationMethod

**System.Data.SqlClient** est l’un des espaces de noms utilisés par l’exemple en C#. L’enum **SqlAuthenticationMethod** est particulièrement intéressante. Cette enum a les valeurs suivantes :

- **SqlAuthenticationMethod.ActiveDirectory *Interactive*** :&nbsp; utilisez cette valeur avec un nom d’utilisateur Azure AD pour obtenir l’authentification multifacteur MFA.
    - Cet article traite essentiellement de cette valeur. Cette valeur génère une expérience interactive en affichant des boîtes de dialogue pour le mot de passe de l’utilisateur, puis pour la validation de l’authentification multifacteur si celle-ci est appliquée pour cet utilisateur.
    - Cette valeur est disponible depuis .NET Framework version 4.7.2.

- **SqlAuthenticationMethod.ActiveDirectory *Integrated*** :&nbsp; utilisez cette valeur pour un compte *fédéré*. Pour un compte fédéré, le nom d’utilisateur est connu du domaine Windows. Cette méthode ne prend pas en charge MFA.

- **SqlAuthenticationMethod.ActiveDirectory *Password*** :&nbsp; utilisez cette valeur pour l’authentification qui nécessite un utilisateur Azure AD et le mot de passe de l’utilisateur. Azure SQL Database effectue l’authentification. Cette méthode ne prend pas en charge MFA.




## <a name="prepare-c-parameter-values-from-the-azure-portal"></a>Préparer les valeurs des paramètres C# à partir du portail Azure

Pour que le programme C# s’exécute correctement, vous devez attribuer les valeurs appropriées aux champs statiques suivants. Ces champs statiques servent de paramètres dans le programme. Ils sont affichés ici avec des valeurs fictives. Les emplacements dans le portail Azure où vous pouvez obtenir les valeurs appropriées sont également affichés :


| Nom de champ statique | Valeur fictive | Emplacement dans le portail Azure |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | « my-favorite-sqldb-svr.database.windows.net » | **Serveurs SQL** &gt; **Filtrer par nom** |
| AzureAD_UserID | "user9@abc.onmicrosoft.com" | **Azure Active Directory** &gt; **Utilisateur** &gt; **Nouvel utilisateur invité** |
| Initial_DatabaseName | « master » | **Serveurs SQL** &gt; **Bases de données SQL** |
| ClientApplicationID | « a94f9c62-97fe-4d19-b06d-111111111111 » | **Azure Active Directory** &gt; **Inscriptions des applications**<br /> &nbsp; &nbsp; &gt; **Rechercher par nom** &gt; **ID d’application** |
| RedirectUri | new Uri(« https://bing.com/ ») | **Azure Active Directory** &gt; **Inscriptions des applications**<br /> &nbsp; &nbsp; &gt; **Rechercher par nom** &gt; *[Application inscrite]* &gt;<br /> &nbsp; &nbsp; **Paramètres** &gt; **RedirectURIs**<br /><br />Pour cet article, n’importe quelle valeur valide convient à RedirectUri. Cette valeur n’est pas réellement utilisée dans notre cas. |
| &nbsp; | &nbsp; | &nbsp; |


Selon votre scénario particulier, vous n’avez peut-être pas besoin de valeurs pour tous les paramètres contenus dans le tableau précédent.




## <a name="run-ssms-to-verify"></a>Exécutez SSMS pour procéder à une vérification

Il est utile d’exécuter SQL Server Management Studio (SSMS) avant le programme C#. L’exécution de SSMS permet de vérifier que les différentes configurations sont correctes. Tout échec du programme C# peut ensuite être restreint à son code source uniquement.


#### <a name="verify-sql-database-firewall-ip-addresses"></a>Vérifier les adresses IP de pare-feu d’Azure SQL Database

Exécutez SSMS sur l’ordinateur sur lequel vous exécuterez ultérieurement le programme C#. Vous pouvez utiliser tout mode **d’authentification** que vous jugez le plus simple. Si un message indique que le pare-feu du serveur de base de données n’accepte pas votre adresse IP, vous pouvez résoudre ce problème en suivant les recommandations présentées dans [Règles de pare-feu au niveau du serveur et de la base de données d’Azure SQL Database](sql-database-firewall-configure.md).


#### <a name="verify-multi-factor-authentication-mfa-for-azure-ad"></a>Vérifier l’authentification multifacteur (MFA) pour Azure AD

Réexécutez SSMS, cette fois en définissant **Authentification** sur **Active Directory - Authentification universelle avec MFA**. Pour cette option, vous devez disposer de SSMS 17.5 ou ultérieur.

Pour plus d’informations, consultez [Configurer l’authentification multifacteur pour SQL Server Management Studio et Azure AD](sql-database-ssms-mfa-authentication-configure.md).




## <a name="c-code-example"></a>Exemple de code C#

Pour compiler cet exemple en C#, vous devez ajouter une référence à l’assembly DLL nommé **Microsoft.IdentityModel.Clients.ActiveDirectory**.


#### <a name="reference-documentation"></a>Documentation de référence

- Espace de noms **System.Data.SqlClient** :
    - Rechercher : &nbsp; [https://docs.microsoft.com/dotnet/api/?term=System.Data.SqlClient](https://docs.microsoft.com/dotnet/api/?term=System.Data.SqlClient)
    - Direct : &nbsp; [System.Data.Client](https://docs.microsoft.com/dotnet/api/system.data.sqlclient)

- Espace de noms **Microsoft.IdentityModel.Clients.ActiveDirectory** :
    - Rechercher : &nbsp; [https://docs.microsoft.com/dotnet/api/?term=Microsoft.IdentityModel.Clients.ActiveDirectory](https://docs.microsoft.com/dotnet/api/?term=Microsoft.IdentityModel.Clients.ActiveDirectory)
    - Direct : &nbsp; [Microsoft.IdentityModel.Clients.ActiveDirectory](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory)


#### <a name="c-source-code-in-two-parts"></a>Code source C#, en deux parties

&nbsp;

```csharp

using System;    // C# ,  part 1 of 2.

// Add a reference to assembly:  Microsoft.IdentityModel.Clients.ActiveDirectory.DLL
using Microsoft.IdentityModel.Clients.ActiveDirectory;

using DA = System.Data;
using SC = System.Data.SqlClient;
using AD = Microsoft.IdentityModel.Clients.ActiveDirectory;
using TX = System.Text;
using TT = System.Threading.Tasks;

namespace ADInteractive5
{
    class Program
    {
        // ASSIGN YOUR VALUES TO THESE STATIC FIELDS !!
        static public string Az_SQLDB_svrName = "<YOUR VALUE HERE>";
        static public string AzureAD_UserID = "<YOUR VALUE HERE>";
        static public string Initial_DatabaseName = "master";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<YOUR VALUE HERE>";
        static public readonly Uri RedirectUri = new Uri("<YOUR VALUE HERE>");

        public static void Main(string[] args)
        {
            var provider = new ActiveDirectoryAuthProvider();

            SC.SqlAuthenticationProvider.SetProvider(
                SC.SqlAuthenticationMethod.ActiveDirectoryInteractive,
                //SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated,  // Alternatives.
                //SC.SqlAuthenticationMethod.ActiveDirectoryPassword,
                provider);

            Program.Connection();
        }

        public static void Connection()
        {
            SC.SqlConnectionStringBuilder builder = new SC.SqlConnectionStringBuilder();

            // Program._  static values that you set earlier.
            builder["Data Source"] = Program.Az_SQLDB_svrName;
            builder.UserID = Program.AzureAD_UserID;
            builder["Initial Catalog"] = Program.Initial_DatabaseName;

            // This "Password" is not used with .ActiveDirectoryInteractive.
            //builder["Password"] = "<YOUR PASSWORD HERE>";

            builder["Connect Timeout"] = 15;
            builder["TrustServerCertificate"] = true;
            builder.Pooling = false;

            // Assigned enum value must match the enum given to .SetProvider().
            builder.Authentication = SC.SqlAuthenticationMethod.ActiveDirectoryInteractive;
            SC.SqlConnection sqlConnection = new SC.SqlConnection(builder.ConnectionString);

            SC.SqlCommand cmd = new SC.SqlCommand(
                "SELECT '******** MY QUERY RAN SUCCESSFULLY!! ********';",
                sqlConnection);

            try
            {
                sqlConnection.Open();
                if (sqlConnection.State == DA.ConnectionState.Open)
                {
                    var rdr = cmd.ExecuteReader();
                    var msg = new TX.StringBuilder();
                    while (rdr.Read())
                    {
                        msg.AppendLine(rdr.GetString(0));
                    }
                    Console.WriteLine(msg.ToString());
                    Console.WriteLine(":Success");
                }
                else
                {
                    Console.WriteLine(":Failed");
                }
                sqlConnection.Close();
            }
            catch (Exception ex)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("Connection failed with the following exception...");
                Console.WriteLine(ex.ToString());
                Console.ResetColor();
            }
        }
    } // EOClass Program .

```

&nbsp;

#### <a name="second-half-of-the-c-program"></a>Seconde moitié du programme C#

Pour permettre une meilleure visualisation, le programme C# est fractionné en deux blocs de code. Pour l’exécuter, collez les deux blocs de code ensemble.

&nbsp;

```csharp

    // C# ,  part 2 of 2 ,  to concatenate below part 1.

    /// <summary>
    /// SqlAuthenticationProvider - Is a public class that defines 3 different Azure AD
    /// authentication methods.  The methods are supported in the new .NET 4.7.2 .
    ///  . 
    /// 1. Interactive,  2. Integrated,  3. Password
    ///  . 
    /// All 3 authentication methods are based on the Azure
    /// Active Directory Authentication Library (ADAL) managed library.
    /// </summary>
    public class ActiveDirectoryAuthProvider : SC.SqlAuthenticationProvider
    {
        // Program._ more static values that you set!
        private readonly string _clientId = Program.ClientApplicationID;
        private readonly Uri _redirectUri = Program.RedirectUri;

        public override async TT.Task<SC.SqlAuthenticationToken>
            AcquireTokenAsync(SC.SqlAuthenticationParameters parameters)
        {
            AD.AuthenticationContext authContext =
                new AD.AuthenticationContext(parameters.Authority);
            authContext.CorrelationId = parameters.ConnectionId;
            AD.AuthenticationResult result;

            switch (parameters.AuthenticationMethod)
            {
                case SC.SqlAuthenticationMethod.ActiveDirectoryInteractive:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,  // "https://database.windows.net/"
                        _clientId,
                        _redirectUri,
                        new AD.PlatformParameters(AD.PromptBehavior.Auto),
                        new AD.UserIdentifier(
                            parameters.UserId,
                            AD.UserIdentifierType.RequiredDisplayableId));
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_1 == '.ActiveDirectoryIntegrated'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserCredential());
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryPassword:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_2 == '.ActiveDirectoryPassword'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserPasswordCredential(
                            parameters.UserId,
                            parameters.Password));
                    break;

                default: throw new InvalidOperationException();
            }
            return new SC.SqlAuthenticationToken(result.AccessToken, result.ExpiresOn);
        }

        public override bool IsSupported(SC.SqlAuthenticationMethod authenticationMethod)
        {
            return authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryInteractive
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryPassword;
        }
    } // EOClass ActiveDirectoryAuthProvider .
} // EONamespace.  End of entire program source code.

```

&nbsp;

#### <a name="actual-test-output-from-c"></a>Sortie du test réel en C#

```
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

&nbsp;


## <a name="next-steps"></a>Étapes suivantes

- [Get-AzureRmSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlserveractivedirectoryadministrator)

