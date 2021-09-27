---
title: Personnaliser les revendications d’application de locataire Azure AD (PowerShell)
titleSuffix: Microsoft identity platform
description: Découvrez comment personnaliser des revendications émises dans des jetons pour une application au sein d’un locataire Azure Active Directory spécifique.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 06/16/2021
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: 302ab2b6f3060213a79b88ce6358cd842f74a425
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123425551"
---
# <a name="customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant"></a>Personnaliser des revendications émises dans des jetons pour une application spécifique au sein d’un locataire

Une revendication concerne ce qu’un fournisseur d’identité déclare sur un utilisateur dans le jeton qu’il émet sur cet utilisateur. Les administrateurs de locataire utilisent la personnalisation des revendications pour personnaliser les revendications émises dans des jetons pour une application spécifique au sein de leur locataire. Vous pouvez utiliser des stratégies de mappage de revendications pour effectuer les opérations suivantes :

- sélectionner les revendications incluses dans les jetons ;
- créer des types de revendications inexistants ;
- choisir ou modifier la source des données émises dans des revendications spécifiques.

La personnalisation des revendications prend en charge la configuration des stratégies de mappage de revendications pour les protocoles WS-Fed, SAML, OAuth et OpenID Connect.

> [!NOTE]
> Cette fonctionnalité remplace la [personnalisation des revendications](active-directory-saml-claims-customization.md) proposée via le portail Azure. Sur la même application, si vous personnalisez des revendications à l’aide du portail en plus de la méthode Microsoft Graph/PowerShell détaillée dans ce document, les jetons émis pour cette application ignorent la configuration définie dans le portail. Les configurations effectuées au moyen des méthodes décrites dans ce document n’apparaissent pas dans le portail.

Dans cet article, nous décrivons quelques scénarios courants susceptibles de vous aider à comprendre comment utiliser le [type de stratégie de mappage des revendications](reference-claims-mapping-policy-type.md).

## <a name="get-started"></a>Bien démarrer

Dans les exemples suivants, vous créez, mettez à jour, liez et supprimez des stratégies pour les principaux du service. Des stratégies de mappage des revendications peuvent être attribuées uniquement à des objets de principal du service. Si vous débutez avec Azure AD, nous vous recommandons de vous [documenter sur l’obtention d’un locataire Azure Active Directory](quickstart-create-new-tenant.md) avant de continuer avec ces exemples.

Lorsque vous créez une stratégie de mappage de revendications, vous pouvez également émettre une revendication à partir d’un attribut d’extension de schéma d’annuaire dans des jetons. Utilisez *ExtensionID* pour l’attribut d’extension au lieu de *ID* dans l’élément `ClaimsSchema`.  Pour plus d’informations sur les attributs d’extension, consultez [Utiliser des attributs de l’extension de schéma d’annuaire](active-directory-schema-extensions.md).

> [!NOTE]
> La [préversion publique du module Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview) est nécessaire pour configurer les stratégies de mappage de revendications. Le module PowerShell est en préversion, tandis que le mappage des revendications et le runtime de création de jetons dans Azure sont généralement disponibles. Les mises à jour du module PowerShell en préversion peuvent vous obliger à mettre à jour ou à modifier vos scripts de configuration. 

Pour commencer, suivez les étapes ci-dessous :

1. Téléchargez la dernière [préversion publique du module Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
1. Exécutez la commande [Connect-AzureAD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0-preview) pour vous connecter à votre compte administrateur Azure AD. Exécutez cette commande chaque fois que vous démarrez une nouvelle session.

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. Pour afficher toutes les stratégies qui ont été créées dans votre organisation, exécutez la commande suivante. Nous vous conseillons d’exécuter cette commande après la plupart des opérations dans les scénarios suivants afin de vérifier que vos stratégies sont créées comme prévu.

   ``` powershell
   Get-AzureADPolicy
   ```

Ensuite, créez une stratégie de mappage des revendications et affectez-la à un principal de service.  Pour les scénarios courants, consultez les exemples suivants :
- [Omettre les revendications de base dans les jetons](#omit-the-basic-claims-from-tokens)
- [Inclure EmployeeID et TenantCountry comme des revendications dans les jetons](#include-the-employeeid-and-tenantcountry-as-claims-in-tokens)
- [Utiliser une transformation de revendications dans les jetons](#use-a-claims-transformation-in-tokens)

Après avoir créé une stratégie de mappage des revendications, configurez votre application pour reconnaitre que les jetons contiendront des revendications personnalisées.  Pour plus d'informations, lisez les [considérations relatives à la sécurité](#security-considerations).

## <a name="omit-the-basic-claims-from-tokens"></a>Omettre les revendications de base dans les jetons

Dans cet exemple, vous créez une stratégie qui supprime l’[ensemble de revendications de base](reference-claims-mapping-policy-type.md#claim-sets) dans les jetons qui sont envoyés aux principaux de service liés.

1. Créez une stratégie de mappage de revendications. Cette stratégie, liée à des principaux du service spécifiques, supprime l’ensemble de revendications de base des jetons.
   1. Pour créer la stratégie, exécutez la commande suivante :

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. Pour voir votre nouvelle stratégie et obtenir son ObjectId, exécutez la commande suivante :

      ``` powershell
      Get-AzureADPolicy
      ```
1. Affectez la stratégie au principal de service. Vous devez également obtenir l’ObjectId de votre principal du service.
   1. Pour afficher tous les principaux de service de votre organisation, vous pouvez [interroger l’API Microsoft Graph](/graph/traverse-the-graph). Ou, dans l’[afficheur Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer), connectez-vous à votre compte Azure AD.
   2. Une fois que vous disposez de l’ObjectId de votre principal du service, exécutez la commande suivante :

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="include-the-employeeid-and-tenantcountry-as-claims-in-tokens"></a>Inclure EmployeeID et TenantCountry comme des revendications dans les jetons

Dans cet exemple, vous créez une stratégie qui ajoute EmployeeID et TenantCountry à des jetons émis pour des principaux du service liés. EmployeeID est émis en tant que type de revendication de nom dans les jetons SAML et JWT. TenantCountry est émis en tant que type de revendication de pays/région dans les jetons SAML et JWT. Dans cet exemple, nous continuons à inclure les ensembles de revendications de base dans les jetons.

1. Créez une stratégie de mappage de revendications. Cette stratégie liée à des principaux du service spécifiques ajoute les revendications EmployeeID et TenantCountry aux jetons.
   1. Pour créer la stratégie, exécutez la commande suivante :

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/employeeid","JwtClaimType":"employeeid"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```

      > [!WARNING]
      > Lorsque vous définissez une stratégie de mappage des revendications pour un attribut d’extension des répertoires, utilisez la propriété `ExtensionID` plutôt que la propriété `ID` dans le corps du tableau `ClaimsSchema`.

   2. Pour voir votre nouvelle stratégie et obtenir son ObjectId, exécutez la commande suivante :

      ``` powershell
      Get-AzureADPolicy
      ```
1. Affectez la stratégie au principal de service. Vous devez également obtenir l’ObjectId de votre principal du service.
   1. Pour afficher tous les principaux de service de votre organisation, vous pouvez [interroger l’API Microsoft Graph](/graph/traverse-the-graph). Ou, dans l’[afficheur Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer), connectez-vous à votre compte Azure AD.
   2. Une fois que vous disposez de l’ObjectId de votre principal du service, exécutez la commande suivante :

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="use-a-claims-transformation-in-tokens"></a>Utiliser une transformation de revendications dans les jetons

Dans cet exemple, vous créez une stratégie qui émet une revendication personnalisée « JoinedData » pour des jetons JWT émis pour des principaux du service liés. Cette revendication contient une valeur créée en joignant les données stockées dans l’attribut extensionattribute1 sur l’objet utilisateur avec « .sandbox ». Dans cet exemple, nous excluons l’ensemble de revendications de base des jetons.

1. Créez une stratégie de mappage de revendications. Cette stratégie liée à des principaux du service spécifiques ajoute les revendications EmployeeID et TenantCountry aux jetons.
   1. Pour créer la stratégie, exécutez la commande suivante :

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. Pour voir votre nouvelle stratégie et obtenir son ObjectId, exécutez la commande suivante :

      ``` powershell
      Get-AzureADPolicy
      ```
1. Affectez la stratégie au principal de service. Vous devez également obtenir l’ObjectId de votre principal du service.
   1. Pour afficher tous les principaux de service de votre organisation, vous pouvez [interroger l’API Microsoft Graph](/graph/traverse-the-graph). Ou, dans l’[afficheur Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer), connectez-vous à votre compte Azure AD.
   2. Une fois que vous disposez de l’ObjectId de votre principal du service, exécutez la commande suivante :

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="security-considerations"></a>Considérations relatives à la sécurité

Les applications qui reçoivent des jetons s’appuient sur le fait que les valeurs de revendication sont émises par Azure AD et ne peuvent pas être falsifiées. Toutefois, lorsque vous modifiez le contenu d’un jeton via des stratégies de mappage de revendications, ces hypothèses peuvent ne plus être correctes. Les applications doivent explicitement confirmer que les jetons ont été modifiés par le créateur de la stratégie de mappage de revendications pour se protéger contre les stratégies de mappage de revendications créées par des intervenants malveillants. Cela peut être fait des manières suivantes :

- [Configurer une clé de signature personnalisée](#configure-a-custom-signing-key)
- Ou, [mettre à jour le manifeste d’application](#update-the-application-manifest) pour accepter les revendications mappées.
 
Sans cela, Azure AD renverra un code d’erreur [`AADSTS50146`](reference-aadsts-error-codes.md#aadsts-error-codes).

### <a name="configure-a-custom-signing-key"></a>Configurer une clé de signature personnalisée

Pour les applications multi-abonnés, une clé de signature personnalisée doit être utilisée.  Ne définissez pas `acceptMappedClaims` dans le manifeste de l’application. Si vous configurez une application dans le Portail Azure, vous recevez un objet d’inscription de l’application et un principal de service dans votre abonné.  Cette application utilise la clé de connexion globale Azure, qui ne peut pas être utilisée pour la personnalisation des revendications dans les jetons.  Pour obtenir des revendications personnalisées dans des jetons, créez une clé de connexion personnalisée à partir d’un certificat et ajoutez-la au principal du service.  Dans le cadre d’un test, vous pouvez utiliser un certificat auto-signé. Après la configuration de la clé de signature personnalisée, votre code d’application doit [valider la clé de signature du jeton](#validate-token-signing-key).

Ajoutez les informations suivantes au principal du service :

- Clé privée (comme des [informations d'identification de la clé](/graph/api/resources/keycredential))
- Mot de passe (comme des [informations d'identification du mot de passe](/graph/api/resources/passwordcredential))
- Clé publique (comme des [informations d'identification de la clé](/graph/api/resources/keycredential))

Extrayez la clé publique et privée de base 64 encodée à partir de l’exportation du fichier PFX de votre certificat. Vérifiez que le `keyId` pour le `keyCredential` utilisé pour « Signer » correspond au `keyId` du `passwordCredential`. Vous pouvez générer le `customkeyIdentifier` en obtenant le code de hachage de l’empreinte du certificat.

#### <a name="request"></a>Requête

L’exemple suivant montre le format de la requête HTTP PATCH pour ajouter une clé de signature personnalisée à un principal de service.  La valeur de la « clé » dans la propriété `keyCredentials` est raccourcie pour des raisons de lisibilité. La valeur est encodée en base 64. Pour la clé privée, la propriété d’utilisation est « Signer ». Pour la clé publique, la propriété d’utilisation est « Vérifier ».

```
PATCH https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e

Content-type: servicePrincipals/json
Authorization: Bearer {token}

{
    "keyCredentials":[
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "X509CertAndPassword",
            "usage": "Sign",
            "key":"MIIKIAIBAz.....HBgUrDgMCERE20nuTptI9MEFCh2Ih2jaaLZBZGeZBRFVNXeZmAAgIH0A==",
            "displayName": "CN=contoso"
        },
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "e35a7d11-fef0-49ad-9f3e-aacbe0a42c42",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "key": "MIIDJzCCAg+gAw......CTxQvJ/zN3bafeesMSueR83hlCSyg==",
            "displayName": "CN=contoso"
        }

    ],
    "passwordCredentials": [
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "endDateTime": "2022-01-27T19:40:33Z",
            "startDateTime": "2020-04-20T19:40:33Z",
            "secretText": "mypassword"
        }
    ]
}
```

#### <a name="configure-a-custom-signing-key-using-powershell"></a>Configurer une clé de signature personnalisée à l’aide de PowerShell

Utilisez PowerShell pour [initier une application cliente publique MSAL](msal-net-initializing-client-applications.md#initializing-a-public-client-application-from-code) et utilisez le flux [Octroi de code d’autorisation](v2-oauth2-auth-code-flow.md) pour obtenir un jeton d’accès d’autorisation déléguée pour Microsoft Graph. Utilisez le jeton d’accès pour appeler Microsoft Graph et configurer une clé de signature personnalisée pour le principal du service. Après la configuration de la clé de signature personnalisée, votre code d’application doit [valider la clé de signature du jeton](#validate-token-signing-key).

Pour exécuter ce script, vous devez :
1. L’ID de l’objet de votre principal de service de l’application, qui se trouve dans le panneau **Vue d’ensemble** de l’entrée de votre application dans [Applications d'entreprise](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) du Portail Azure.
2. Une inscription de l’application pour inscrire un utilisateur et obtenir un jeton d’accès pour appeler Microsoft Graph. Obtenez l’ID de l’application (cliente) de cette application dans le panneau **Vue d’ensemble** de l’entrée de votre application dans [Inscriptions d'applications](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) du Portail Azure. L’inscription de l’application doit présenter la configuration suivante :
    - Une URI de redirection de « http://localhost  » répertoriée dans la configuration de la plateforme **Applications mobiles et de bureau**
    - Dans les **autorisations API**, les autorisations déléguées Microsoft Graph **Application.ReadWrite.All** et **User.Read** (assurez-vous que vous avez octroyé le consentement administrateur à ces autorisations)
3. Un utilisateur qui se connecte pour obtenir le jeton d’accès Microsoft Graph. L’utilisateur doit être l’un des rôles d’administration Azure AD suivants (requis pour mettre à jour le principal du service) :
    - Administrateur d'applications cloud
    - Administrateur d’application
    - Administrateur général
4. Un certificat à configurer en tant que clé de signature personnalisée pour notre application. Vous pouvez soit créer un certificat auto-signé, soit en obtenir un auprès de votre autorité de certification approuvée. Les composants de certificat suivants sont utilisés dans le script :
    - clé publique (généralement un fichier .cer)
    - clé privée au format PKCS#12 (dans le fichier .pfx)
    - mot de passe de la clé privée (fichier pfx)

> [!IMPORTANT]
> La clé privée doit être au format PKCS#12, car Azure AD ne prend pas en charge d’autres types de format. L’utilisation d’un format incorrect peut entraîner l’erreur « Certificat non valide : la valeur de clé est un certificat non valide » lors de l’utilisation de Microsoft Graph pour corriger le principal du service avec un `keyCredentials` contenant les informations sur le certificat.

```powershell

$fqdn="fourthcoffeetest.onmicrosoft.com" # this is used for the 'issued to' and 'issued by' field of the certificate
$pwd="mypassword" # password for exporting the certificate private key
$location="C:\\temp" # path to folder where both the pfx and cer file will be written to

# Create a self-signed cert
$cert = New-SelfSignedCertificate -certstorelocation cert:\currentuser\my -DnsName $fqdn
$pwdSecure = ConvertTo-SecureString -String $pwd -Force -AsPlainText
$path = 'cert:\currentuser\my\' + $cert.Thumbprint
$cerFile = $location + "\\" + $fqdn + ".cer"
$pfxFile = $location + "\\" + $fqdn + ".pfx"
 
# Export the public and private keys
Export-PfxCertificate -cert $path -FilePath $pfxFile -Password $pwdSecure
Export-Certificate -cert $path -FilePath $cerFile

$ClientID = "<app-id>"
$loginURL       = "https://login.microsoftonline.com"
$tenantdomain   = "fourthcoffeetest.onmicrosoft.com"
$redirectURL = "http://localhost" # this reply URL is needed for PowerShell Core 
[string[]] $Scopes = "https://graph.microsoft.com/.default"
$pfxpath = $pfxFile # path to pfx file
$cerpath = $cerFile # path to cer file
$SPOID = "<service-principal-id>"
$graphuri = "https://graph.microsoft.com/v1.0/serviceprincipals/$SPOID"
$password = $pwd  # password for the pfx file
 
 
# choose the correct folder name for MSAL based on PowerShell version 5.1 (.Net) or PowerShell Core (.Net Core)
 
if ($PSVersionTable.PSVersion.Major -gt 5)
    { 
        $core = $true
        $foldername =  "netcoreapp2.1"
    }
else
    { 
        $core = $false
        $foldername = "net45"
    }
 
# Load the MSAL/microsoft.identity/client assembly -- needed once per PowerShell session
[System.Reflection.Assembly]::LoadFrom((Get-ChildItem C:/Users/<username>/.nuget/packages/microsoft.identity.client/4.32.1/lib/$foldername/Microsoft.Identity.Client.dll).fullname) | out-null
  
$global:app = $null
  
$ClientApplicationBuilder = [Microsoft.Identity.Client.PublicClientApplicationBuilder]::Create($ClientID)
[void]$ClientApplicationBuilder.WithAuthority($("$loginURL/$tenantdomain"))
[void]$ClientApplicationBuilder.WithRedirectUri($redirectURL)
 
$global:app = $ClientApplicationBuilder.Build()
  
Function Get-GraphAccessTokenFromMSAL {
    [Microsoft.Identity.Client.AuthenticationResult] $authResult  = $null
    $AquireTokenParameters = $global:app.AcquireTokenInteractive($Scopes)
    [IntPtr] $ParentWindow = [System.Diagnostics.Process]::GetCurrentProcess().MainWindowHandle
    if ($ParentWindow)
    {
        [void]$AquireTokenParameters.WithParentActivityOrWindow($ParentWindow)
    }
    try {
        $authResult = $AquireTokenParameters.ExecuteAsync().GetAwaiter().GetResult()
    }
    catch {
        $ErrorMessage = $_.Exception.Message
        Write-Host $ErrorMessage
    }
     
    return $authResult
}
  
$myvar = Get-GraphAccessTokenFromMSAL
if ($myvar)
{
    $GraphAccessToken = $myvar.AccessToken
    Write-Host "Access Token: " $myvar.AccessToken
    #$GraphAccessToken = "eyJ0eXAiOiJKV1QiL ... iPxstltKQ"
    
 
    #  this is for PowerShell Core
    $Secure_String_Pwd = ConvertTo-SecureString $password -AsPlainText -Force
 
    # reading certificate files and creating Certificate Object
    if ($core)
    {
        $pfx_cert = get-content $pfxpath -AsByteStream -Raw
        $cer_cert = get-content $cerpath -AsByteStream -Raw
        $cert = Get-PfxCertificate -FilePath $pfxpath -Password $Secure_String_Pwd
    }
    else
    {
        $pfx_cert = get-content $pfxpath -Encoding Byte
        $cer_cert = get-content $cerpath -Encoding Byte
        # Write-Host "Enter password for the pfx file..."
        # calling Get-PfxCertificate in PowerShell 5.1 prompts for password
        # $cert = Get-PfxCertificate -FilePath $pfxpath
        $cert = [System.Security.Cryptography.X509Certificates.X509Certificate2]::new($pfxpath, $password)
    }
 
    # base 64 encode the private key and public key
    $base64pfx = [System.Convert]::ToBase64String($pfx_cert)
    $base64cer = [System.Convert]::ToBase64String($cer_cert)
 
    # getting id for the keyCredential object
    $guid1 = New-Guid
    $guid2 = New-Guid
 
    # get the custom key identifier from the certificate thumbprint:
    $hasher = [System.Security.Cryptography.HashAlgorithm]::Create('sha256')
    $hash = $hasher.ComputeHash([System.Text.Encoding]::UTF8.GetBytes($cert.Thumbprint))
    $customKeyIdentifier = [System.Convert]::ToBase64String($hash)
 
    # get end date and start date for our keycredentials
    $endDateTime = ($cert.NotAfter).ToUniversalTime().ToString( "yyyy-MM-ddTHH:mm:ssZ" )
    $startDateTime = ($cert.NotBefore).ToUniversalTime().ToString( "yyyy-MM-ddTHH:mm:ssZ" )
 
    # building our json payload
    $object = [ordered]@{    
    keyCredentials = @(       
         [ordered]@{            
            customKeyIdentifier = $customKeyIdentifier
            endDateTime = $endDateTime
            keyId = $guid1
            startDateTime = $startDateTime 
            type = "X509CertAndPassword"
            usage = "Sign"
            key = $base64pfx
            displayName = "CN=fourthcoffeetest" 
        },
        [ordered]@{            
            customKeyIdentifier = $customKeyIdentifier
            endDateTime = $endDateTime
            keyId = $guid2
            startDateTime = $startDateTime 
            type = "AsymmetricX509Cert"
            usage = "Verify"
            key = $base64cer
            displayName = "CN=fourthcoffeetest"   
        }
        )  
    passwordCredentials = @(
        [ordered]@{
            customKeyIdentifier = $customKeyIdentifier
            keyId = $guid1           
            endDateTime = $endDateTime
            startDateTime = $startDateTime
            secretText = $password
        }
    )
    }
 
    $json = $object | ConvertTo-Json -Depth 99
    Write-Host "JSON Payload:"
    Write-Output $json
 
    # Request Header
    $Header = @{}
    $Header.Add("Authorization","Bearer $($GraphAccessToken)")
    $Header.Add("Content-Type","application/json")
 
    try 
    {
        Invoke-RestMethod -Uri $graphuri -Method "PATCH" -Headers $Header -Body $json
    } 
    catch 
    {
        # Dig into the exception to get the Response details.
        # Note that value__ is not a typo.
        Write-Host "StatusCode:" $_.Exception.Response.StatusCode.value__ 
        Write-Host "StatusDescription:" $_.Exception.Response.StatusDescription
    }
 
    Write-Host "Complete Request"
}
else
{
    Write-Host "Fail to get Access Token"
}
```

#### <a name="validate-token-signing-key"></a>Valider la clé de signature de jeton
Les applications pour lesquelles le mappage de revendications est activé doivent valider leurs clés de signature de jeton en ajoutant `appid={client_id}` à leurs [demandes de métadonnées OpenID Connect](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document). Le format du document de métadonnées OpenID Connect que vous utilisez se trouve ci-dessous :

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid={client-id}
```

### <a name="update-the-application-manifest"></a>Mettre à jour le manifeste de l’application

Pour les applications monolocataire, vous pouvez définir la propriété `acceptMappedClaims` sur `true` dans le [manifeste de l’application](reference-app-manifest.md).  Comme indiqué dans le [type de ressource apiApplication](/graph/api/resources/apiapplication#properties), cela permet à une application d’utiliser le mappage de revendications sans spécifier de clé de signature personnalisée. 

> [!WARNING]
> Ne définissez pas la propriété `acceptMappedClaims` sur `true` pour les applications multi-locataire, ce qui peut permettre à des acteurs malveillants de créer des stratégies de mappage des revendications pour votre application.

Cela nécessite que l’audience du jeton demandé utilise un nom de domaine vérifié de votre locataire Azure AD, ce qui signifie que vous devez vous assurer de définir `Application ID URI` (représenté par `identifierUris` dans le manifeste de l’application) par exemple sur `https://contoso.com/my-api` ou (simplement en utilisant le nom du locataire par défaut) `https://contoso.onmicrosoft.com/my-api`.

Si vous n’utilisez pas un domaine vérifié, Azure AD retourne un code d’erreur `AADSTS501461` avec le message *« AcceptMappedClaims est pris en charge uniquement pour une audience de jetons correspondant au GUID de l’application ou une audience dans les domaines vérifiés du locataire. Vous pouvez modifier l’identificateur de ressource ou utiliser une clé de signature propre à l’application. »*

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations, consultez l’article de référence relatif au [type de stratégie de mappage des revendications](reference-claims-mapping-policy-type.md).
- Pour savoir comment personnaliser les revendications émises dans le jeton SAML via le portail Azure, consultez [How to: Customize claims issued in the SAML token for enterprise applications](active-directory-saml-claims-customization.md) (Comment : Personnaliser des revendications émises dans le jeton SAML pour les applications d’entreprise)
- Pour plus d’informations sur les attributs d’extension, consultez [Utiliser des attributs de l’extension de schéma d’annuaire dans les revendications](active-directory-schema-extensions.md).
