---
title: Kit de développement logiciel (SDK) Microsoft Graph PowerShell et Azure Active Directory Identity Protection
description: Découvrez comment interroger Microsoft Graph pour obtenir les détections de risques et les informations associées à partir d’Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 01/25/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21274636a38cb37ac62f2b73112d9346482fe573
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532215"
---
# <a name="azure-active-directory-identity-protection-and-the-microsoft-graph-powershell-sdk"></a>Azure Active Directory Identity Protection et Kit de développement logiciel (SDK) Microsoft Graph PowerShell

Microsoft Graph est le point de terminaison d’API unifiée de Microsoft et accueille également les API [d’Azure Active Directory Identity Protection](./overview-identity-protection.md). Cet article vous montre comment utiliser le [Kit de développement logiciel (SDK) Microsoft Graph PowerShell](/graph/powershell/get-started) pour accéder à des informations relatives aux utilisateurs à risque à l’aide de PowerShell. Les organisations qui souhaitent interroger directement les API Microsoft Graph peuvent utiliser l’article [Tutoriel : Identifier et corriger les risques à l’aide des API Microsoft Graph](/graph/tutorial-riskdetection-api) pour commencer ce parcours.


## <a name="connect-to-microsoft-graph"></a>Se connecter à Microsoft Graph

L’accès aux données d’Identity Protection par le biais de Microsoft Graph se fait en quatre étapes :

- [Créer un certificat](#create-a-certificate)
- [Créez une inscription d’application](#create-a-new-app-registration)
- [Accédez aux autorisations d’API](#configure-api-permissions)
- [Configurez un nom d'informations d'identification valide](#configure-a-valid-credential)

### <a name="create-a-certificate"></a>Créer un certificat

Dans un environnement de production, vous utiliseriez un certificat de votre autorité de certification de production, mais, dans cet exemple, nous utiliserons un certificat auto-signé. Créez et exportez le certificat à l’aide des commandes PowerShell suivantes.

```powershell
$cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"
```

### <a name="create-a-new-app-registration"></a>Créer une nouvelle inscription d’application

1. Dans le Portail Azure, cliquez sur **Azure Active Directory** > **Inscriptions d’applications**.
1. Sélectionnez **Nouvelle inscription**.
1. Dans la page **Créer**, effectuez les étapes suivantes :
   1. Dans la zone de texte **Nom**, entrez le nom de votre application (par exemple : API de détections des risques Azure AD).
   1. Sous **Types de comptes pris en charge**, sélectionnez le type de compte qui utilisera les API.
   1. Sélectionnez **Inscription**.
1. Notez l’**ID d’application (client)** et l’**ID de répertoire (locataire)** , car vous en aurez besoin ultérieurement.

### <a name="configure-api-permissions"></a>Configurez les autorisations d’API

Dans cet exemple, nous configurons les permissions d’application, ce qui permet d’utiliser l’exemple sans aide. Si vous accordez des autorisations à un utilisateur qui sera connecté, choisissez à la place des permissions déléguées. Pour plus d’informations sur les différents types d’autorisations, consultez l’article [Autorisations et consentement dans la plateforme d’identités Microsoft](../develop/v2-permissions-and-consent.md#permission-types).

1. Depuis **l’application** que vous avez créée, puis sélectionnez **Autorisations de l’API**.
1. Dans la page **Autorisations configurées**, dans la barre d’outils en haut, cliquez sur **Ajouter une autorisation**.
1. Dans la page **Ajouter un accès d’API**, cliquez sur **Sélectionner une API**.
1. Dans la page **Sélectionner une API**, sélectionnez **Microsoft Graph**, puis cliquez sur **Sélectionner**.
1. Page Demander des **autorisations d’API** 
   1. Sélectionnez **Autorisations de l’application**.
   1. Cochez les cases en regard de `IdentityRiskEvent.Read.All` et `IdentityRiskyUser.Read.All`.
   1. Sélectionnez **Ajouter des autorisations**.
1. Sélectionner **Accorder le consentement de l’administrateur pour le domaine** 

### <a name="configure-a-valid-credential"></a>Configurez un nom d'informations d'identification valide

1. Depuis **l’application** que vous avez créée, sélectionnez **Certificats et secrets**.
1. Sous **Certificats**, sélectionnez **Charger le certificat**.
   1. Sélectionnez le certificat précédemment exporté dans la fenêtre qui s’ouvre.
   1. Sélectionnez **Ajouter**.
1. Notez l’**empreinte** du certificat, car vous aurez besoin de ces informations à l’étape suivante.

## <a name="list-risky-users-using-powershell"></a>Répertorier les utilisateurs à risque à l’aide de PowerShell

Pour pouvoir interroger Microsoft Graph, nous devons installer le module `Microsoft.Graph` dans notre fenêtre PowerShell à l’aide de la commande `Install-Module Microsoft.Graph`.

Modifiez les variables suivantes pour inclure les informations générées lors des étapes précédentes, puis exécutez-les dans leur ensemble pour obtenir des détails relatifs aux utilisateurs à risque à l’aide de PowerShell.

```powershell
$ClientID       = "<your client ID here>"        # Application (client) ID gathered when creating the app registration
$tenantdomain   = "<your tenant domain here>"    # Directory (tenant) ID gathered when creating the app registration
$Thumbprint     = "<your client secret here>"    # Certificate thumbprint gathered when configuring your credential

Select-MgProfile -Name "beta"
  
Connect-MgGraph -ClientId $ClientID -TenantId $tenantdomain -CertificateThumbprint $Thumbprint

Get-MgRiskyUser -All
```

## <a name="next-steps"></a>Étapes suivantes

- [Prise en main du Kit de développement logiciel (SDK) Microsoft Graph PowerShell](/graph/powershell/get-started)
- [Tutoriel : Identifier et corriger les risques à l’aide des API Microsoft Graph](/graph/tutorial-riskdetection-api)
- [Overview of Microsoft Graph (Vue d’ensemble de Microsoft Graph)](https://developer.microsoft.com/graph/docs)
- [Obtenir l’accès sans utilisateur](/graph/auth-v2-service)
- [Azure AD Identity Protection Service Root (Racine de service d’Azure AD Identity Protection)](/graph/api/resources/identityprotectionroot)
- [Azure Active Directory Identity Protection](./overview-identity-protection.md)
