---
title: Client d'auto-test pour prévalider une machine virtuelle | Place de marché Azure
description: Comment créer un client d’auto-test pour prévalider une image de machine virtuelle pour la Place de marché Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: dsindona
ms.openlocfilehash: af42476f9d04f7f2bfc275c731b02aa5a9b8ecf6
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273152"
---
# <a name="create-a-self-test-client-to-pre-validate-an-azure-virtual-machine-image"></a>Créer un client d’auto-test pour prévalider une image de machine virtuelle Azure

> [!IMPORTANT]
> Depuis le 13 avril 2020, nous avons commencé à déplacer la gestion de vos offres de machines virtuelles Azure vers l'Espace partenaires. Après la migration, vous créerez et gérerez vos offres dans l’Espace partenaires. Suivez les instructions fournies dans l'article [Certification d'une image de machine virtuelle Azure](https://aks.ms/CertifyVMimage) pour gérer vos offres migrées.

Utilisez cet article comme guide pour la création d’un service client qui consomme l’API d’auto-test. Vous pouvez utiliser l’API d’auto-test pour prévalider une machine virtuelle afin de vérifier qu’elle répond aux dernières exigences de publication sur la Place de marché Azure. Ce service client vous permet de tester une machine virtuelle avant de soumettre votre offre à la certification Microsoft.

## <a name="development-and-testing-overview"></a>Vue d’ensemble du développement et du test

Dans le cadre du processus d’auto-test, vous allez créer un client local qui se connecte à la Place de marché Azure pour valider une machine virtuelle en cours d’exécution dans votre abonnement Azure. La machine virtuelle peut exécuter le système d’exploitation Windows ou Linux.

Le client local exécute un script qui s’authentifie auprès de l’API d’auto-test, envoie des informations de connexion et reçoit les résultats des tests.

Les étapes principales de la création d’un client d’auto-test sont les suivantes :

1. Choisissez le locataire Azure Active Directory (AD) pour votre application.
2. Inscrivez l’application cliente.
3. Créez un jeton pour l’application Azure AD cliente.
4. Transmettez le jeton à l’API d’auto-test.

Après avoir créé le client, vous pouvez le tester sur votre machine virtuelle.

### <a name="self-test-client-authorization"></a>Autorisation du client d’auto-test

Le diagramme suivant illustre le fonctionnement de l’autorisation pour les appels de service à service à l’aide des informations d’identification du client (secret partagé ou certificat).

![Processus d’autorisation du client](./media/stclient-dev-process.png)

## <a name="the-self-test-client-api"></a>API du client d’auto-test

L’API d’auto-test contient un point de terminaison unique qui prend en charge uniquement la méthode POST.  Sa structure est la suivante.

```
Uri:             https://isvapp.azurewebsites.net/selftest-vm
Method:          Post
Request Header:  Content-Type: "application/json"
Authorization:   "Bearer xxxx-xxxx-xxxx-xxxxx"
Request body:    The Request body parameters should use the following JSON format:
                 {
                   "DNSName":"XXXX.westus.cloudapp.azure.com",
                   "User":"XXX",
                   "Password":"XXX@1234567",
                   "OS":"XXX",
                   "PortNo":"22",
                   "CompanyName":"ABCD",
                 }
```

Le tableau suivant décrit les champs de l’API.


|      Champ         |    Description    |
|  ---------------   |  ---------------  |
|  Autorisation     |  La chaîne « Bearer xxxx-xxxx-xxxx-xxxxx » contient le jeton client Azure Active Directory (AD), qui peut être créé à l’aide de PowerShell.          |
|  DNSName           |  Nom DNS de la machine virtuelle à tester    |
|  Utilisateur              |  Nom d’utilisateur pour la connexion à la machine virtuelle         |
|  Mot de passe          |  Mot de passe pour la connexion à la machine virtuelle          |
|  Système d''exploitation                |  Système d’exploitation de la machine virtuelle : soit `Linux` ou `Windows`          |
|  PortNo            |  Numéro du port ouvert pour la connexion à la machine virtuelle. Le numéro de port est généralement `22` pour Linux et `5986` pour Windows.          |
|  |  |

## <a name="consuming-the-api"></a>Consommation de l’API

Vous pouvez consommer l’API d’auto-test à l’aide de PowerShell ou de cURL.

### <a name="use-powershell-to-consume-the-api-on-the-linux-os"></a>Utiliser PowerShell pour consommer l’API sur le système d’exploitation Linux

Pour appeler l’API dans PowerShell, effectuez les étapes suivantes :

1. Utilisez la commande `Invoke-WebRequest` pour appeler l’API.
2. La méthode est POST et le type de contenu est JSON, comme illustré dans la capture d’écran et l’exemple de code suivants.
3. Spécifiez les paramètres de corps au format JSON.

L’exemple de code suivant montre un appel PowerShell à l’API.

```powershell
$accesstoken = "Get token for your Client AAD App"
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body = @{
    "DNSName" = "XXXX.westus.cloudapp.azure.com"
    "User" = "XXX"
    "Password" = "XXX@123456"
    "OS" = "Linux"
    "PortNo" = "22"
    "CompanyName" = "ABCD"

} | ConvertTo-Json
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" -Headers $headers;
$Content = $res | ConvertFrom-Json
```
La capture d’écran suivante montre un exemple d’appel de l’API dans PowerShell.

![Appeler l’API avec PowerShell pour le système d’exploitation Linux](./media/stclient-call-api-ps-linuxvm.png)

À l’aide de l’exemple précédent, vous pouvez récupérer le fichier JSON et l’analyser pour obtenir les détails suivants :

```powershell
$testresult = ConvertFrom-Json -InputObject (ConvertFrom-Json -InputObject $res)

  Write-Host "OSName: $($testresult.OSName)"
  Write-Host "OSVersion: $($testresult.OSVersion)"
  Write-Host "Overall Test Result: $($testresult.TestResult)"

For ($i=0; $i -lt $testresult.Tests.Length; $i++)
{
    Write-Host "TestID: $($testresult.Tests[$i].TestID)"
    Write-Host "TestCaseName: $($testresult.Tests[$i].TestCaseName)"
    Write-Host "Description: $($testresult.Tests[$i].Description)"
    Write-Host "Result: $($testresult.Tests[$i].Result)"
    Write-Host "ActualValue: $($testresult.Tests[$i].ActualValue)"
}
```

La capture d’écran suivante, qui montre `$res.Content`, vous donne les détails de vos résultats de tests au format JSON.

![Résultats JSON de l’appel PowerShell à Linux](./media/stclient-pslinux-rescontent-json.png)

La capture d’écran suivante montre un exemple de résultats des tests JSON affichés dans une visionneuse JSON en ligne (par exemple, [Code Beautify](https://codebeautify.org/jsonviewer) ou [Visionneuse JSON](https://jsonformatter.org/json-viewer)).

![Résultats JSON de l’appel PowerShell à la machine virtuelle Linux](./media/stclient-consume-api-pslinux-json.png)

### <a name="use-powershell-to-consume-the-api-on-the-windows-os"></a>Utiliser PowerShell pour consommer l’API sur le système d’exploitation Windows

Pour appeler l’API dans PowerShell, effectuez les étapes suivantes :

1. Utilisez la commande `Invoke-WebRequest` pour appeler l’API.
2. La méthode est POST et le type de contenu est JSON, comme illustré dans la capture d’écran et l’exemple de code suivants.
3. Créez les paramètres de corps au format JSON.

L’exemple de code suivant montre un appel PowerShell à l’API.

```powershell
$accesstoken = "Get token for your Client AAD App"
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body = @{
    "DNSName" = "XXXX.westus.cloudapp.azure.com"
    "User" = "XXX"
    "Password" = "XXX@123456"
    "OS" = "Windows"
    "PortNo" = "5986"
    "CompanyName" = "ABCD"

} | ConvertTo-Json
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" -Headers $headers;
$Content = $res | ConvertFrom-Json
```

La capture d’écran suivante montre un exemple d’appel de l’API dans PowerShell.

![Appeler l’API avec PowerShell pour le système d’exploitation Windows](./media/stclient-call-api-ps-windowsvm.png)

À l’aide de l’exemple précédent, vous pouvez récupérer le fichier JSON et l’analyser pour obtenir les détails suivants :

```powershell
$testresult = ConvertFrom-Json -InputObject (ConvertFrom-Json -InputObject $res)

  Write-Host "OSName: $($testresult.OSName)"
  Write-Host "OSVersion: $($testresult.OSVersion)"
  Write-Host "Overall Test Result: $($testresult.TestResult)"

For ($i=0; $i -lt $testresult.Tests.Length; $i++)
{
    Write-Host "TestID: $($testresult.Tests[$i].TestID)"
    Write-Host "TestCaseName: $($testresult.Tests[$i].TestCaseName)"
    Write-Host "Description: $($testresult.Tests[$i].Description)"
    Write-Host "Result: $($testresult.Tests[$i].Result)"
    Write-Host "ActualValue: $($testresult.Tests[$i].ActualValue)"
}
```

La capture d’écran suivante, qui montre `$res.Content`, vous donne les détails de vos résultats de tests au format JSON.

![Résultats JSON de l’appel PowerShell à Windows](./media/stclient-pswindows-rescontent-json.png)

La capture d’écran suivante montre des résultats de tests affichés dans une visionneuse JSON en ligne.
(Par exemple, [Code Beautify](https://codebeautify.org/jsonviewer), [Visionneuse JSON](https://jsonformatter.org/json-viewer))

![Résultats JSON de l’appel PowerShell à une machine virtuelle Windows](./media/stclient-consume-api-pswindows-json.png)

### <a name="use-curl-to-consume-the-api-on-the-linux-os"></a>Utiliser cURL pour consommer l’API sur le système d’exploitation Linux

Pour appeler l’API avec cURL, effectuez les étapes suivantes :

1. Utilisez la commande curl pour appeler l’API.
2. La méthode est POST et le type de contenu est JSON, comme illustré dans l’extrait de code suivant.

```
CURL POST -H "Content-Type:application/json"
-H "Authorization: Bearer XXXXXX-Token-XXXXXXXX"
https://isvapp.azurewebsites.net/selftest-vm
-d '{ "DNSName":"XXXX.westus.cloudapp.azure.com", "User":"XXX", "Password":"XXXX@123456", "OS":"Linux", "PortNo":"22", "CompanyName":"ABCD"}'
```

L’écran suivant montre un exemple d’utilisation de curl pour appeler l’API.

![Appeler l’API à l’aide de la commande curl](./media/stclient-consume-api-curl.png)

La capture d’écran suivante montre les résultats JSON de l’appel de curl.

![Résultats JSON de l’appel de curl](./media/stclient-consume-api-curl-json.png)


## <a name="choose-the-azure-ad-tenant-for-the-app"></a>Choisir le locataire Azure AD pour l’application

Utilisez les étapes suivantes pour choisir le locataire Azure AD où vous voulez créer votre application.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans la barre de menus du haut, sélectionnez votre compte puis, dans la liste Répertoire, choisissez le locataire Active Directory auprès duquel vous voulez inscrire votre application. Vous pouvez aussi sélectionner l’icône **Répertoire + abonnement** pour afficher le filtre d’abonnement global. La capture d’écran suivante présente un exemple de ce filtre.

   ![Sélectionner le filtre d’abonnement](./media/stclient-subscription-filter.png)

3. Dans la barre de navigation de gauche, sélectionnez **Tous les services**, puis **Azure Active Directory**.

   Dans les étapes suivantes, vous aurez peut-être besoin du nom du locataire (ou nom d’annuaire) ou de l’ID du locataire (ou ID d’annuaire).

   **Pour obtenir des informations sur le locataire :**

   Dans **Vue d’ensemble d’Azure Active Directory**, recherchez « Propriétés », puis sélectionnez **Propriétés**. Utilisez la capture d’écran suivante comme exemple :

   - **Nom** : nom du locataire ou nom de l’annuaire
   - **ID d’annuaire** : ID du locataire ou ID de l’annuaire. Vous pouvez aussi utiliser la barre de défilement pour rechercher Propriétés.

   ![Page de propriétés Azure Active Directory](./media/stclient-aad-properties.png)

## <a name="register-the-client-app"></a>Inscrire l’application cliente

Utilisez les étapes suivantes pour inscrire l’application cliente.

1. Dans la barre de navigation de gauche, sélectionnez **Tous les services**, puis **Inscriptions des applications**.
2. Sous **Inscription des applications**, sélectionnez **+ Nouvelle inscription d’application**.
3. Sous **Créer**, fournissez les informations nécessaires pour les champs suivants :

   - **Nom** : entrez un nom convivial pour l’application. Par exemple, « SelfTestClient » (client d’auto-test).
   - **Type d’application** : sélectionnez **Application web/API**
   - **URL de connexion** : entrez « https:\//isvapp.azurewebsites.net/selftest-vm »

4. Sélectionnez **Create** (Créer).
5. Sous **Inscriptions des applications** ou **Application inscrite** , copiez l’**ID de l’application**.

   ![Récupérer l’ID de l’application](./media/stclient-app-id.png)

6. Dans la barre d’outils de l’application inscrite, sélectionnez **Paramètres**.
7. Sélectionnez **Autorisations nécessaires** pour configurer des autorisations pour votre application.
8. Sous **Autorisations nécessaires**, sélectionnez **+ Ajouter**.
9. Sous **Ajouter un accès d’API**, choisissez **Sélectionner une API**.
10. Sous **Sélectionner une API**, tapez « Modèle de déploiement classique Windows Azure » pour rechercher l’API.
11. Dans les résultats de la recherche, choisissez **Modèle de déploiement classique Windows Azure**, puis cliquez sur **Sélectionner**.

    ![Configurer un multilocataire pour une application](./media/stclient-select-api.png)

12. Sous **Ajouter un accès d’API**, choisissez **Sélectionner des autorisations**.
13. Sélectionnez **Accéder à « API Gestion des services Windows Azure »** .

    ![Activer l’accès d’API pour une application](./media/stclient-enable-api-access.png)

14. Cliquez sur **Sélectionner**.
15. Sélectionnez **Terminé**.
16. Sous **Paramètres**, sélectionnez **Propriétés**.
17. Sous **Propriétés**, faites défiler l’écran jusqu’à **Locataires multiples**. Sélectionnez **Oui**.

    ![Configurer un multilocataire pour une application](./media/stclient-yes-multitenant.png)

18. Sélectionnez **Enregistrer**.
19. Sous **Paramètres**, sélectionnez **Clés**.
20. Créez une clé secrète en sélectionnant la zone de texte **DESCRIPTION** de la clé. Configurez les champs suivants :

    - Tapez un nom de clé. Par exemple, « selftestclient » (client d’auto-test)
    - Dans la liste déroulante **EXPIRE**, sélectionnez « Dans 1 an ».
    - Sélectionnez **Enregistrer** pour générer la clé.
    - Sous **VALEUR**, copiez la clé.

      >[!Important]
      >Vous ne pourrez pas voir la valeur de la clé une fois que vous aurez quitté le formulaire **Clés**.

    ![Formulaire de valeur de la clé](./media/stclient-create-key.png)

## <a name="create-the-token-for-the-client-app"></a>Créer le jeton pour l’application cliente

Vous pouvez utiliser l’un des programmes suivants pour créer et obtenir un jeton à l’aide de l’API REST OAuth :

- postman
- cURL dans Linux
- C&#35;
- PowerShell

### <a name="to-create-and-get-a-token-using-postman"></a>Pour créer et obtenir un jeton à l’aide de Postman

 Pour demander des jetons à Auth0 pour toutes vos applications autorisées, effectuez une opération POST sur le point de terminaison [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token) avec une charge utile au format suivant :

```
Method Type : POST
Base Url: https://login.microsoftonline.com/common/oauth2/token
```

Transmettez les paramètres suivants dans le corps de la requête :

```
Body Content-Type: x-www-form-urlencoded
client_id: XXX (Paste your Application ID of Web App/API Type client AD App)
grant_type: client_credentials
client_secret: XXX (Paste your Secret Key of Web App/API Type client AD App)
resource: https://management.core.windows.net
```

Transmettez les paramètres suivants dans l’en-tête de la requête :

```
Content-Type: application/x-www-form-urlencoded
```

La capture d’écran suivante présente un exemple d’utilisation de Postman pour obtenir un jeton :

![Obtenir un jeton avec Postman](./media/stclient-postman-get-token.png)

### <a name="to-create-and-get-a-token-using-curl-in-linux"></a>Pour créer et obtenir un jeton à l’aide de cURL dans Linux

Pour demander des jetons à Auth0 pour toutes vos applications autorisées, effectuez une opération POST sur le point de terminaison [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token) avec une charge utile au format suivant :

```
Request:

curl --request POST \
                --url 'https://login.microsoftonline.com/common/oauth2/token' \
                --header "Content-Type:application/x-www-form-urlencoded" \
                --data "grant_type=client_credentials&client_id=XXXXX-XXXX-XXXX-XXXXXXX&client_secret=XXXXXXXXX&resource=https://management.core.windows.net"

Response:

{"token":"UClCUUKxUlkdbhE1cHLz3kyjbIZYVh9eB34A5Q21Y3FPqKGSJs","expires":"2014-02-17 18:46:08"}
```

La capture d’écran suivante présente un exemple d’utilisation de la commande curl pour obtenir un jeton.

![Obtenir un jeton avec la commande curl](./media/stclient-curl-get-token.png)

### <a name="to-create-and-get-a-token-using-c35"></a>Pour créer et obtenir un jeton à l’aide de C&#35;

Pour demander des jetons à Auth0 pour toutes vos applications autorisées, effectuez une opération POST sur le point de terminaison https:\//soamtenant.auth0.com/oauth/token avec une charge utile au format suivant :

```csharp
string clientId = "Your Application Id";
string clientSecret = "Your Application Secret";
string audience = "https://management.core.windows.net";
string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, "https://login.microsoftonline.com/common/oauth2/token");
string grantType = "client_credentials";
var client = new RestClient(authority);
var request = new RestRequest(Method.POST);
       request.AddHeader("content-type", "application/x-www-form- urlencoded");

string requestBody = "grant_type=" + grantType + "&" + "client_id=" +   clientId + "&" + "client_secret=" + clientSecret + "&" + "resource=" +  audience;

request.AddParameter("application/x-www-form-urlencoded", requestBody,  ParameterType.RequestBody);

IRestResponse response = client.Execute(request);
var content = response.Content;
var token = JObject.Parse(content)["access_token"];
```

### <a name="to-create-and-get-a-token-using-powershell"></a>Pour créer et obtenir un jeton à l’aide de PowerShell

Pour demander des jetons à Auth0 pour toutes vos applications autorisées, effectuez une opération POST sur le point de terminaison https:\//soamtenant.auth0.com/oauth/token avec une charge utile au format suivant :

```powershell
$clientId = "Application Id of AD Client APP";
$clientSecret = "Secret Key of AD Client APP "
$audience = "https://management.core.windows.net";
$authority = "https://login.microsoftonline.com/common/oauth2/token"
$grantType = "client_credentials";

$requestBody = "grant_type=" + $grantType + "&" + "client_id=" +  $clientId   + "&" + "client_secret=" + $clientSecret + "&" + "resource=" + $audience;

$headers = New-Object  "System.Collections.Generic.Dictionary[[String],[String]]"
             $headers.Add("ContentType", "application/x-www-form-urlencoded")
resp = Invoke-WebRequest -Method Post -Uri $authority -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $requestBody

$token = $resp.Content | ConvertFrom-Json
$token.AccessToken
```

## <a name="pass-the-client-app-token-to-the-api"></a>Transmettez le jeton de l’application cliente à l’API

Transmettez le jeton à l’API d’auto-test en utilisant le code suivant dans l’en-tête d’autorisation :

```powershell
$redirectUri = 'https://isvapp.azurewebsites.net/selftest-vm'
$accesstoken = 'place your token here'

$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body =
@{
      'DNSName'="XXXX.cloudapp.azure.com";
      'User'="XXX";
      'Password'="XXXX@12345";
      'OS'="Linux";
      'PortNo'="22"
  } | ConvertTo-Json

$result=Invoke-WebRequest -Method Post -Uri $redirectUri -Headers $headers -ContentType 'application/json' -Body $Body
$result
Write-Output 'Test Results:'
$result.Content
```

## <a name="test-your-self-test-client"></a>Tester votre client d’auto-test

Pour tester le client, effectuez les étapes suivantes :

1. Déployez la machine virtuelle que vous voulez tester.
2. Appelez l’API d’auto-test à l’aide de votre jeton d’application cliente pour autorisation.
3. Obtenez les résultats des tests au format JSON.

### <a name="test-result-examples"></a>Exemples de résultats de tests

Les extraits de code suivants montrent des résultats de tests au format JSON.

**Résultats de tests pour une machine virtuelle Windows :**

```json
{
  "SchemaVersion": 1,
  "AppCertificationCategory": "Microsoft Single VM Certification",
  "ProviderID": "050DE427-2A99-46D4-817C-5354D3BF2AE8",
  "OSName": "Microsoft Windows Server 2016 Datacenter",
  "OSVersion": "10.0.14393",
  "CreatedDate": "06/01/2018 07:48:04",
  "TestResult": "Pass",
  "APIVersion": "1.1",
  "Tests": [
    {
      "Tes tID": "1.1.4",
      "TestCaseName": "OS Architecture",
      "Description": "Azure supports 64bit Operating System Only.",
      "Result": "Passed",
      "ActualValue": "64 Bit",
      "Re quiredValue": "OS Should be 64 bit"
    },
    {
      "TestID": "1.1.5",
      "TestCaseName": "User account dependency",
      "Description": "Application execution should not have de pendency on administrator account.",
      "Result": "Passed",
      "ActualValue": "isv",
      "RequiredValue": "Logged in user should not be an administrator"
    },
    {
      "TestID": " 1.1.6",
      "TestCaseName": "Failover Cluster",
      "Description": "Windows Server Failover Clustering feature is not yet supported, Application should not have dependency on this feature.",
      "Result": "Passed",
      "ActualValue": "Disabled",
      "RequiredValue": "Failover Clustering feature is disabled"
    },
```

**Résultats de tests pour une machine virtuelle Linux :**

```json
{
  "SchemaVersion": 1,
  "AppCertificationCategory": "Microsoft Single VM Certification",
  "ProviderID": "050DE427-2A99-46D4-817C-5354D3BF2AE8",
  "OSName": "Ubuntu  16.04",
  "OSVersion": "16.04",
  "CreatedDate": "06/01/2018 07:04:24",
  "TestResult": "Pass",
  "APIVersion": "1.1",
  "Tests": [
    {
      "TestID": "48",
      "TestCaseName": "Bash Hi story",
      "Description": "Bash history files should be cleared before creating the VM image.",
      "Result": "Passed",
      "ActualValue": "No file Exist",
      "RequiredVal ue": "1024"
    },
    {
      "TestID": "39",
      "TestCaseName": "Linux Agent Version",
      "Description": "Azure Linux Agent 2.2.10 and above should be installed. ",
      "Result": "Pas sed",
      "ActualValue": "2.2.20",
      "RequiredValue": "2.2.10"
    },
    {
      "TestID": "40",
      "TestCaseName": "Required Kernel Parameters",
      "Description": "Verifies the following  kernel parameters are set console=ttyS0, earlyprintk=ttyS0, rootdelay=300",
      "Result": "Passed",
      "ActualValue": "Matched Parameter: console=ttyS0,earlypri ntk=ttyS0,rootdelay=300",
      "RequiredValue": "console=ttyS0#earlyprintk=ttyS0#rootdelay=300"
    },
```

## <a name="next-steps"></a>Étapes suivantes

Après avoir testé avec succès votre machine virtuelle Azure, vous pouvez [publier l’offre](./cpp-publish-offer.md).
