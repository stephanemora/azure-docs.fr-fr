---
title: Fichier include
description: Fichier include pour les pages d’accueil du scénario de client confidentiel (démon, application web, API web)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: a3acdbb93dd20f0b89e4f99d64f5f7a30ce40623
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87102638"
---
## <a name="register-secrets-or-certificates"></a>Inscrire des secrets ou des certificats

Comme pour toute application de cliente confidentiel, vous devez inscrire un certificat ou un secret. Vous pouvez inscrire les secrets de votre application via une expérience interactive dans le [portail Microsoft Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview), ou à l’aide d’outils en ligne de commande (par exemple PowerShell).

### <a name="register-client-secrets-by-using-the-application-registration-portal"></a>Inscrire des secrets clients avec le portail d’inscription d’applications

La gestion des informations d’identification du client s’effectue dans la page **Certificats et secrets** d’une application :

![Page Certificats et secrets](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets.png)

- Vous créez un *clé secrète client* en sélectionnant **Nouvelle clé secrète client** dans l’inscription de l’application sur le Portail Microsoft Azure. Quand vous créez une clé secrète client, vous _devez_ enregistrer la chaîne de la clé secrète avant de quitter le volet **Certificats et clés secrètes**. La chaîne de la clé secrète ne s’affiche plus.
- Pendant l’inscription de l’application, vous utilisez le bouton **Charger le certificat** pour charger le certificat. Azure AD prend uniquement en charge les certificats qui sont directement inscrits sur l’application et ne suivent pas de chaînes de certificats.

Pour en savoir plus, voir [Démarrage rapide : Configurer une application cliente pour accéder aux API web | Ajouter des informations d’identification à votre application.](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application)

### <a name="register-client-secrets-by-using-powershell"></a>Inscrire des secrets clients avec PowerShell

Vous pouvez également inscrire votre application auprès d’Azure AD à l’aide d’outils en ligne de commande. L’exemple de commande [active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) montre comment inscrire un certificat ou un secret d’application avec une application Azure AD :

- Pour découvrir comment inscrire un secret d’application, consultez [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190).
- Pour découvrir comment inscrire un certificat avec une application, consultez [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178).
