---
title: Se connecter à Azure Allemagne avec Visual Studio | Microsoft Docs
description: Informations sur la gestion de votre abonnement dans Azure Allemagne avec Visual Studio
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: f5749ed9d2080a92a33993d8131595359110b75e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2020
ms.locfileid: "122531347"
---
# <a name="connect-to-azure-germany-by-using-visual-studio"></a>Se connecter à Azure Allemagne avec Visual Studio

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

Les développeurs utilisent Visual Studio pour gérer facilement leurs abonnements Azure lors de la création de solutions. Actuellement, vous ne pouvez pas configurer une connexion à Azure Allemagne dans l’interface utilisateur de Visual Studio.

## <a name="visual-studio-2017-and-visual-studio-2019"></a>Visual Studio 2017 et Visual Studio 2019

Visual Studio requiert un fichier de configuration pour se connecter à Azure Allemagne. Avec ce fichier en place, Visual Studio se connecte à Azure Allemagne, au lieu d’Azure global.

### <a name="create-a-configuration-file-for-azure-germany"></a>Créer un fichier de configuration pour Azure Allemagne

Créez un fichier nommé *AadProvider.Configuration.json* avec le contenu suivant :

```json
{
  "AuthenticationQueryParameters":null,
  "AsmEndPoint":"https://management.microsoftazure.de/",
  "Authority":"https://login.microsoftonline.de/",
  "AzureResourceManagementEndpoint":"https://management.microsoftazure.de/",
  "AzureResourceManagementAudienceEndpoints":["https://management.core.cloudapi.de/"],
  "ClientIdentifier":"872cd9fa-d31f-45e0-9eab-6e460a02d1f1",
  "EnvironmentName":"BlackForest",
  "GraphEndpoint":"https://graph.cloudapi.de",
  "MsaHomeTenantId":"f577cd82-810c-43f9-a1f6-0cc532871050",
  "NativeClientRedirect":"urn:ietf:wg:oauth:2.0:oob",
  "PortalEndpoint":"https://portal.core.cloudapi.de/",
  "ResourceEndpoint":"https://management.microsoftazure.de/",
  "ValidateAuthority":true,
  "VisualStudioOnlineEndpoint":"https://app.vssps.visualstudio.com/",
  "VisualStudioOnlineAudience":"499b84ac-1321-427f-aa17-267ca6975798"
}
```

### <a name="update-visual-studio-for-azure-germany"></a>Mettre à jour Visual Studio pour Azure Allemagne

1. Fermez Visual Studio.
1. Placez *AadProvider.Configuration.json* dans le dossier *%localappdata%\\.IdentityService\AadConfigurations*. Créez le dossier s’il ne s’y trouve pas.
1. Démarrez Visual Studio et commencez à utiliser votre compte Azure Allemagne.

> [!NOTE]
> Avec le fichier de configuration, seuls les abonnements Azure Allemagne sont accessibles. Les abonnements que vous avez configurés précédemment sont toujours visibles mais ils ne fonctionnent pas, car Visual Studio est maintenant connecté à Azure Allemagne au lieu d’Azure global. Pour vous connecter à Azure global, supprimez le fichier.
>

### <a name="revert-a-visual-studio-connection-to-azure-germany"></a>Rétablir la connexion Visual Studio à Azure Allemagne

Pour permettre à Visual Studio de se connecter à Azure global, vous devez supprimer le fichier de configuration qui permet la connexion à Azure Allemagne.

1. Fermez Visual Studio.
1. Supprimez ou renommez le dossier *%localappdata%\.IdentityService\AadConfigurations*.
1. Redémarrez Visual Studio et commencez à utiliser votre compte Azure global.

> [!NOTE]
> Une fois cette configuration rétablie, vos abonnements Azure Allemagne ne sont plus accessibles.
>

## <a name="visual-studio-2015"></a>Visual Studio 2015

Visual Studio 2015 requiert une modification de registre pour se connecter à Azure Allemagne. Une fois cette clé de registre définie, Visual Studio se connecte à Azure Allemagne au lieu d’Azure global.

### <a name="update-visual-studio-2015-for-azure-germany"></a>Mettre à jour Visual Studio 2015 pour Azure Allemagne

Pour permettre à Visual Studio de se connecter à Azure Allemagne, vous devez mettre à jour le registre.

1. Fermez Visual Studio.
1. Créez un fichier texte nommé *VisualStudioForAzureGermany.reg*.
1. Copiez et collez le texte suivant dans *VisualStudioForAzureGermany.reg* :

```
Windows Registry Editor Version 5.00

[HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
"AadInstance"="https://login.microsoftonline.de/"
"adaluri"="https://management.microsoftazure.de"
"AzureRMEndpoint"="https://management.microsoftazure.de"
"AzureRMAudienceEndpoint"="https://management.core.cloudapi.de"
"EnableAzureRMIdentity"="true"
"GraphUrl"="graph.cloudapi.de"
"AadApplicationTenant"="f577cd82-810c-43f9-a1f6-0cc532871050"
```

1. Enregistrez le fichier, puis exécutez-le en double-cliquant dessus. Vous êtes invité à fusionner le fichier dans votre registre.
1. Démarrez Visual Studio et commencez à utiliser [Cloud Explorer](/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer) avec votre compte Azure Allemagne.

> [!NOTE]
> Une fois cette clé de registre définie, seuls les abonnements Azure Allemagne sont accessibles. Les abonnements que vous avez configurés précédemment sont toujours visibles mais ils ne fonctionnent pas, car Visual Studio est maintenant connecté à Azure Allemagne au lieu d’Azure global. Pour vous connecter à Azure global, annulez les modifications.
>

### <a name="revert-a-visual-studio-2015-connection-to-azure-germany"></a>Rétablir une connexion Visual Studio 2015 à Azure Allemagne

Pour permettre à Visual Studio de se connecter à Azure global, vous devez supprimer les paramètres du registre qui permettent la connexion à Azure Allemagne.

1. Fermez Visual Studio.
1. Créez un fichier texte nommé *VisualStudioForAzureGermany_Remove.reg*.
1. Copiez et collez le texte suivant dans *VisualStudioForAzureGermany_Remove.reg* :

```
Windows Registry Editor Version 5.00

[HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
"AadInstance"=-
"adaluri"=-
"AzureRMEndpoint"=-
"AzureRMAudienceEndpoint"=-
"EnableAzureRMIdentity"=-
"GraphUrl"=-
```

1. Enregistrez le fichier, puis exécutez-le en double-cliquant dessus. Vous êtes invité à fusionner le fichier dans votre registre.
1. Démarrez Visual Studio.

> [!NOTE]
> Une fois cette clé de registre rétablie, vos abonnements Azure Allemagne s’affichent mais ne sont pas accessibles. Vous pouvez les retirer en toute sécurité.
>

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la connexion à Azure Allemagne, consultez les ressources suivantes :

* [Se connecter à Azure Allemagne avec PowerShell](./germany-get-started-connect-with-ps.md)
* [Se connecter à Azure Allemagne avec Azure CLI](./germany-get-started-connect-with-cli.md)
* [Se connecter à Azure Allemagne avec le portail Azure](./germany-get-started-connect-with-portal.md)