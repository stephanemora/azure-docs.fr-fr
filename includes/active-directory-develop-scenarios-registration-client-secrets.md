---
title: Fichier Include
description: fichier Include pour le scénario de client confidentiel (démon, application Web, API Web) de pages d’accueil
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
ms.date: 04/18/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 9ee7422b372993d60c629524eb036b9678e5776c
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074649"
---
## <a name="registration-of-secrets-or-certificates"></a>Enregistrement des secrets ou certificats

Comme pour toute application de client confidentiel, vous devez inscrire un certificat ou une clé secrète. Vous pouvez inscrire des secrets de votre application via l’expérience interactive dans le [Azure portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview), ou à l’aide des outils de ligne de commande (par exemple, PowerShell)

### <a name="registering-client-secrets-using-the-application-registration-portal"></a>L’inscription des clés secrètes de client à l’aide du portail de l’inscription d’application

La gestion des informations d’identification du client se produit dans le **certificats et clés secrètes** page pour une application :

![image](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png)

- le secret d’application (également le secret client nommé) est généré par Azure AD, lors de l’inscription de l’application de client confidentiel. Cette génération se produit lorsque vous sélectionnez **nouvelle clé secrète client**. À ce stade, vous devez copier la chaîne de secret dans le Presse-papiers pour une utilisation dans votre application, avant de sélectionner **enregistrer**. Cette chaîne n’affiche plus.
- le certificat est chargé dans l’application d’inscription avec le **télécharger un certificat** bouton

Pour plus d’informations, consultez [Guide de démarrage rapide : Configurer une application cliente pour accéder aux API web | Ajouter des informations d’identification à votre application](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application)

### <a name="registering-client-secrets-using-powershell"></a>L’inscription des clés secrètes de client à l’aide de PowerShell

Vous pouvez également enregistrer votre application auprès d’Azure AD à l’aide des outils de ligne de commande. Le [active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) exemple montre comment inscrire un secret d’application ou un certificat avec une application Azure AD :

- Pour plus d’informations sur l’inscription d’un secret d’application, consultez [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190)
- Pour plus d’informations sur la façon d’inscrire un certificat avec l’application, consultez [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178)