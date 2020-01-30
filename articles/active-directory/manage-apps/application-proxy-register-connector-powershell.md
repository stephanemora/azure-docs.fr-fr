---
title: Installation sans assistance du connecteur Azure AD App Proxy | Documents Microsoft
description: Explique comment effectuer une installation silencieuse du connecteur du Proxy d’application Azure AD pour offrir un accès à distance sécurisé à vos applications locales.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/24/2020
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b43d2de0a366d7e69a025b2e4e2998dccda2038e
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2020
ms.locfileid: "76756209"
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>Créer un script d’installation sans assistance pour le connecteur de proxy d’application Azure AD

Cette rubrique vous aide à créer un script Windows PowerShell qui active l’installation sans assistance et l’inscription de votre connecteur de proxy d’application Azure AD.

Cette fonctionnalité est utile lorsque vous souhaitez :

* Installer le connecteur sur des serveurs Windows sur lesquels aucune interface utilisateur n’est activée ou auxquels vous ne pouvez pas accéder avec le Bureau à distance
* Installer et inscrire beaucoup de connecteurs à la fois.
* Intégrer l’installation et l’inscription du connecteur dans le cadre d’une autre procédure.
* Créer une image de serveur standard qui contient les exécutables du connecteur, mais qui n’est pas inscrite.

Pour que le [connecteur de proxy d’application](application-proxy-connectors.md) puisse fonctionner, vous devez l’inscrire auprès de votre répertoire Azure AD à l’aide d’un identifiant d’administrateur d’application et d’un mot de passe. En général, ces informations sont saisies lors de l’installation du connecteur dans une boîte de dialogue contextuelle, mais vous pouvez utiliser PowerShell pour automatiser ce processus.

Une installation sans assistance comporte deux étapes. Tout d’abord, vous devez installer le connecteur. Ensuite, vous devez inscrire le connecteur auprès d’Azure Active Directory. 

## <a name="install-the-connector"></a>Installation du connecteur
Effectuez les étapes suivantes pour installer le connecteur sans l’inscrire :

1. Ouvrez une invite de commandes.
2. Exécutez la commande suivante, dans laquelle /q signifie « installation silencieuse ». Une installation silencieuse ne vous invite pas à accepter le Contrat de Licence Utilisateur Final.
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="register-the-connector-with-azure-ad"></a>Inscription du connecteur sur Azure Active Directory
Deux méthodes permettent d’inscrire le connecteur :

* Inscription du connecteur à l’aide d’un objet d’informations d’identification Windows PowerShell
* Inscription du connecteur à l’aide d’un jeton créé hors connexion

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Inscription du connecteur à l’aide d’un objet d’informations d’identification Windows PowerShell
1. Créez un objet d’informations d’identification Windows PowerShell `$cred` qui contient un nom d’utilisateur et un mot de passe d’administration pour votre annuaire. Exécutez la commande suivante, en remplaçant *\<username\>* et *\<password\>*  :
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. Accédez à **C:\Program Files\Microsoft AAD App Proxy Connector** et exécutez le script suivant en utilisant l’objet `$cred` que vous avez créé :
   
        .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy

### <a name="register-the-connector-using-a-token-created-offline"></a>Inscription du connecteur à l’aide d’un jeton créé hors connexion
1. Créez un jeton hors connexion à l’aide de la classe AuthenticationContext en utilisant les valeurs de cet extrait de code ou les applets de commande PowerShell ci-dessous :

    **Utilisation de C# :**

        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.microsoftonline.com/common/oauth2/token?api-version=1.0");

        /// <summary>
        /// The application ID of the connector in AAD
        /// </summary>
        static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

        /// <summary>
        /// The reply address of the connector application in AAD
        /// </summary>
        static readonly Uri ConnectorRedirectAddress = new Uri("urn:ietf:wg:oauth:2.0:oob");

        /// <summary>
        /// The AppIdUri of the registration service in AAD
        /// </summary>
        static readonly Uri RegistrationServiceAppIdUri = new Uri("https://proxy.cloudwebappproxy.net/registerapp");

        #endregion

        #region private members
        private string token;
        private string tenantID;
        #endregion

        public void GetAuthenticationToken()
        {
            AuthenticationContext authContext = new AuthenticationContext(AadAuthenticationEndpoint.AbsoluteUri);

            AuthenticationResult authResult = authContext.AcquireToken(RegistrationServiceAppIdUri.AbsoluteUri,
                ConnectorAppId,
                ConnectorRedirectAddress,
                PromptBehavior.Always);

            if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
            {
                Trace.TraceError("Authentication result, token or tenant id returned are null");
                throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
            }

            token = authResult.AccessToken;
            tenantID = authResult.TenantId;
        }

    **Utilisation de PowerShell :**

        # Locate AzureAD PowerShell Module
        # Change Name of Module to AzureAD after what you have installed
        $AADPoshPath = (Get-InstalledModule -Name AzureAD).InstalledLocation
        # Set Location for ADAL Helper Library
        $ADALPath = $(Get-ChildItem -Path $($AADPoshPath) -Filter Microsoft.IdentityModel.Clients.ActiveDirectory.dll -Recurse ).FullName | Select-Object -Last 1
        
        # Add ADAL Helper Library
        Add-Type -Path $ADALPath
        
        #region constants
        
        # The AAD authentication endpoint uri
        [uri]$AadAuthenticationEndpoint = "https://login.microsoftonline.com/common/oauth2/token?api-version=1.0/" 
        
        # The application ID of the connector in AAD
        [string]$ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489"
        
        # The reply address of the connector application in AAD
        [uri]$ConnectorRedirectAddress = "urn:ietf:wg:oauth:2.0:oob" 
        
        # The AppIdUri of the registration service in AAD
        [uri]$RegistrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp"
        
        #endregion
        
        #region GetAuthenticationToken
        
        # Set AuthN context
        $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $AadAuthenticationEndpoint
        
        # Build platform parameters
        $promptBehavior = [Microsoft.IdentityModel.Clients.ActiveDirectory.PromptBehavior]::Always
        $platformParam = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList $promptBehavior
        
        # Do AuthN and get token
        $authResult = $authContext.AcquireTokenAsync($RegistrationServiceAppIdUri.AbsoluteUri, $ConnectorAppId, $ConnectorRedirectAddress, $platformParam).Result
        
        # Check AuthN result
        If (($authResult) -and ($authResult.AccessToken) -and ($authResult.TenantId) ) {
        $token = $authResult.AccessToken
        $tenantId = $authResult.TenantId
        }
        Else {
        Write-Output "Authentication result, token or tenant id returned are null"
        }
        
        #endregion

2. Une fois que vous disposez du jeton, créez une chaîne SecureString en utilisant le jeton :

   `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`

3. Exécuter la commande Windows PowerShell suivante, en remplaçant \<locataire GUID\> avec votre ID de répertoire :

   `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy`

## <a name="next-steps"></a>Étapes suivantes 
* [Publier des applications avec votre propre nom de domaine](application-proxy-configure-custom-domain.md)
* [Activer l’authentification unique](application-proxy-configure-single-sign-on-with-kcd.md)
* [Résoudre les problèmes rencontrés avec le proxy d’application](application-proxy-troubleshoot.md)


