---
title: Authentification de service à service auprès d’Azure Key Vault à l’aide de .NET
description: Utilisez la bibliothèque de Microsoft.Azure.Services.AppAuthentication pour effectuer l’authentification auprès d’Azure Key Vault à l’aide de .NET.
keywords: informations d’identification locales pour l’authentification Auzre Key Vault
author: msmbaldwin
services: key-vault
ms.author: mbaldwin
ms.date: 06/30/2020
ms.topic: conceptual
ms.service: key-vault
ms.subservice: general
ms.openlocfilehash: 83c4a48f8c177cf84078966bae32126102b45c3b
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86521018"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Authentification de service à service auprès d’Azure Key Vault à l’aide de .NET

> [!NOTE]
> Les méthodes d’authentification documentées dans cet article ne sont plus considérées comme de bonnes pratiques. Nous vous encourageons à adopter les méthodes d’authentification mises à jour dans le [Guide pratique pour s’authentifier auprès d’Azure Key Vault](authentication.md).

Pour vous authentifier auprès d’Azure Key Vault, vous avez besoin d’informations d’identification Azure Active Directory (Azure AD), soit un secret partagé ou certificat.

La gestion de ces informations d’identification peut être difficile. Il peut être tentant de regrouper les informations d’identification au sein d’une application, en les incluant dans des fichiers source ou de configuration. L’élément `Microsoft.Azure.Services.AppAuthentication` de la bibliothèque .NET simplifie ce problème. Il utilise les informations d’identification du développeur pour l’authentification pendant le développement local. Lorsque la solution est déployée ultérieurement vers Azure, la bibliothèque bascule automatiquement vers les informations d’identification de l’application. Il est plus sûr d’utiliser les informations d’identification du développeur pendant le développement local, car vous n’avez pas besoin de créer des informations d’identification Azure AD ou de partager des informations d’identification entre les développeurs.

La bibliothèque `Microsoft.Azure.Services.AppAuthentication` gère l’authentification automatiquement, ce qui vous permet de vous concentrer sur votre solution, plutôt que sur vos informations d’identification. Elle prend en charge le développement local avec Microsoft Visual Studio, Azure CLI ou l’authentification intégrée Azure AD. Quand elle est déployée sur une ressource Azure qui prend en charge une identité managée, la bibliothèque utilise automatiquement des [identités managées pour les ressources Azure](../../active-directory/msi-overview.md). Aucune modification du code ou de la configuration n’est requise. La bibliothèque prend également en charge les [informations d’identification client](../../azure-resource-manager/resource-group-authenticate-service-principal.md) Azure AD lorsqu’une identité managée n’est pas disponible, ou lorsque le contexte de sécurité du développeur ne peut être déterminé pendant le développement local.

## <a name="prerequisites"></a>Prérequis

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) ou [Visual Studio 2017 v15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/).

- L’extension d’authentification d’application pour Visual Studio, disponible en tant qu’extension distincte de Visual Studio 2017 Update 5, et regroupée avec le produit dans Update 6 et versions ultérieures. Avec Update 6 ou ultérieure, vous pouvez vérifier l’installation de l’extension d’authentification d’application en sélectionnant les outils de développement Azure dans le programme d’installation de Visual Studio.

## <a name="using-the-library"></a>Utilisation de la bibliothèque

Pour les applications .NET, la façon la plus simple d’utiliser une identité managée consiste à tirer parti du package `Microsoft.Azure.Services.AppAuthentication`. Voici comment démarrer :

1. Sélectionnez **Outils** > **Gestionnaire de package NuGet** > **Gérer les packages NuGet pour la solution** pour ajouter des références aux packages NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) et [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) pour votre projet.

1. Ajoutez le code suivant :

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // Instantiate a new KeyVaultClient object, with an access token to Key Vault
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider1.KeyVaultTokenCallback));

    // Optional: Request an access token to other Azure services
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider2.GetAccessTokenAsync("https://management.azure.com/").ConfigureAwait(false);
    ```

La classe `AzureServiceTokenProvider` met en cache le jeton en mémoire et le récupère à partir d’Azure AD, juste avant l’expiration. Ainsi, vous n’avez plus besoin de vérifier la date d’expiration avant d’appeler la méthode `GetAccessTokenAsync`. Il vous suffit d’appeler la méthode lorsque vous souhaitez utiliser le jeton.

La méthode `GetAccessTokenAsync` requiert un identificateur de ressource. Pour en savoir plus sur services Microsoft Azure, voir[Que sont les identités managées pour les ressources Azure ?](../../active-directory/msi-overview.md).

## <a name="local-development-authentication"></a>Authentification du développement local

Pour un développement local, il existe deux scénarios d’authentification principaux : [authentification auprès des services Azure](#authenticating-to-azure-services) et [authentification auprès des services personnalisés](#authenticating-to-custom-services).

### <a name="authenticating-to-azure-services"></a>Authentification auprès des services Azure

Les ordinateurs locaux ne prennent pas en charge les identités managées pour les ressources Azure. Par conséquent, la bibliothèque `Microsoft.Azure.Services.AppAuthentication` utilise vos informations d’identification de développeur pour s’exécuter dans votre environnement de développement local. Lorsque la solution est déployée sur Azure, la bibliothèque utilise une identité managée pour basculer vers un flux d’octroi d’informations d’identification de client OAuth 2.0. Cette approche signifie que vous pouvez sans problème tester le même code en local et à distance.

Pour le développement local, `AzureServiceTokenProvider` extrait des jetons à l’aide de **Visual Studio**, de **l’interface de ligne de commande Azure** (CLI) ou de **l’authentification intégrée Azure AD**. Le système essaie chaque option de manière séquentielle et utilise la première option qui aboutit. Si aucune option ne fonctionne, une exception `AzureServiceTokenProviderException` est levée, avec des informations détaillées.

#### <a name="authenticating-with-visual-studio"></a>Authentification avec Visual Studio

Pour authentifier à l’aide de Visual Studio :

1. Connectez-vous à Visual Studio puis sélectionnez **Outils**&nbsp;>&nbsp;**Options** pour ouvrir **Options**.

1. Sélectionnez **Authentification par le service Azure**, choisissez un compte pour le développement local, puis cliquez sur **OK**.

Si vous rencontrez des problèmes lorsque vous utilisez Visual Studio, telles que les erreurs impliquant le fichier du fournisseur de jetons, lisez attentivement les étapes précédentes.

Il se peut que vous deviez authentifier à nouveau votre jeton de développeur. Pour ce faire, sélectionnez **Outils**&nbsp;>&nbsp;**Options**, puis **Azure&nbsp;Service&nbsp;Authentification**. Recherchez un lien **Réauthentifier** sous le compte sélectionné. Cliquez sur ce lien pour effectuer l’authentification.

#### <a name="authenticating-with-azure-cli"></a>Authentification avec Azure CLI

Pour utiliser Azure CLI pour le développement local, vérifiez que vous disposez d’[Azure CLI v 2.0.12](/cli/azure/install-azure-cli) ou version ultérieure.

Pour utiliser Azure CLI :

1. Recherchez Azure CLI dans la barre des tâches Windows pour ouvrir l’**invite de commandes de Microsoft Azure**.

1. Connectez-vous au portail Azure : *az login* pour se connecter à Azure.

1. Vérifiez l’accès en entrant *az account get-access-token --resource https:\//vault.azure.net*. Si vous recevez une erreur, vérifiez que la version appropriée d’Azure CLI est correctement installée.

   Si Azure CLI n’est pas installé dans le répertoire par défaut, vous pouvez recevoir une erreur signalant que `AzureServiceTokenProvider` ne peut pas trouver le chemin d’accès à Azure CLI. Utilisez la variable d’environnement **AzureCLIPath** pour définir le dossier d’installation d’Azure CLI. Le paramètre `AzureServiceTokenProvider` ajoute le répertoire spécifié dans la variable d’environnement **AzureCLIPath** à la variable d’environnement **Path**, le cas échéant.

1. Si vous êtes connecté à Azure CLI à l’aide de plusieurs comptes, ou si votre compte peut accéder à plusieurs abonnements, vous devez spécifier l’abonnement à utiliser. Entrez la commande *az account set --subscription <subscription-id>* .

Cette commande génère une sortie uniquement en cas d’échec. Pour vérifier les paramètres de compte en cours, entrez la commande `az account list`.

#### <a name="authenticating-with-azure-ad-authentication"></a>Authentification via la fonction d’authentification Azure AD

Pour utiliser l’authentification Azure Active Directory, vérifiez les éléments suivants :

- Votre Active Directory local est synchronisé avec Azure AD. Pour plus d’informations, voir [Présentation de l’identité hybride avec Azure Active Directory](../../active-directory/connect/active-directory-aadconnect.md).

- Votre code s’exécute sur un ordinateur joint au domaine.

### <a name="authenticating-to-custom-services"></a>Authentification auprès de services personnalisés

Lorsqu’un service appelle les services Azure, les étapes précédentes sont applicables, car ces services permettent d’accéder aux utilisateurs et aux applications.

Lorsque vous créez un service qui appelle un service personnalisé, utilisez les informations d’identification du client Azure AD pour l’authentification de développement local. Nous avons deux options :

- Utilisez un principal de service pour la connexion à Azure :

    1. Créer un principal de service. Pour plus d’informations, consultez [Créer un principal de service Azure avec Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

    1. Utilisez Azure CLI pour vous connecter avec la commande suivante :

        ```azurecli
        az login --service-principal -u <principal-id> --password <password> --tenant <tenant-id> --allow-no-subscriptions
        ```

        Comme le principal du service ne dispose pas nécessairement d’un accès à un abonnement, utilisez l’argument `--allow-no-subscriptions`.

- Utilisez des variables d’environnement pour spécifier les détails du principal de service. Pour en savoir plus, voir [Exécution de l’application à l’aide d’un principal de service](#running-the-application-using-a-service-principal).

Une fois que vous êtes connecté à Azure, `AzureServiceTokenProvider` récupère un jeton pour le développement local à l’aide du principal de service.

Cette approche s’applique uniquement au développement local. Lorsque votre solution est déployée sur Azure, la bibliothèque bascule sur une identité managée pour l’authentification.

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>Exécution de l’application en utilisant une identité managée ou une identité affectée par l’utilisateur

Lorsque vous exécutez votre code dans Azure App Service ou une machine virtuelle Azure pour laquelle une identité managée est activée, la bibliothèque utilise automatiquement l’identité managée. Aucun changement dans le code n’est nécessaire. Toutefois, l’identité managée doit disposer des autorisations *GET* pour accéder au coffre de clés. Vous pouvez accorder à l’identité managée les autorisations *GET* via les *stratégies d’accès* du coffre de clés.

Vous pouvez aussi authentifier avec une identité affectée par l’utilisateur. Pour en savoir plus sur les identités affectées par l’utilisateur, consultez [À propos des identités managées pour les ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types). Pour s’authentifier avec une identité affectée à l’utilisateur, vous devez spécifier l’ID client dans la chaîne de connexion. La chaîne de connexion est spécifiée dans [Prise en charge de chaînes de connexion](#connection-string-support).

## <a name="running-the-application-using-a-service-principal"></a>Exécution de l’application à l’aide d’un principal de service

Il peut être nécessaire de créer une information d’identification de client Azure Active Directory pour s’authentifier. Cette situation peut se produire dans les exemples suivants :

- Votre code s’exécute dans un environnement de développement local, mais non sous l’identité du développeur. Service Fabric, par exemple, utilise le [compte NetworkService](../../service-fabric/service-fabric-application-secret-management.md) pour le développement local.

- Votre code s’exécute dans un environnement de développement local, et vous êtes authentifié auprès d’un service personnalisé. Vous n’utilisez donc pas votre identité de développeur.

- Votre code s’exécute sur une ressource de calcul Azure qui ne prend pas encore en charge les identités managées pour les ressources Azure, comme Azure Batch.

Il existe trois méthodes principales d’utilisation d’un principal de service pour exécuter votre application. Pour utiliser un d’eux, vous devez d’abord créer un principal de service. Pour plus d’informations, consultez [Créer un principal de service Azure avec Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

### <a name="use-a-certificate-in-local-keystore-to-sign-into-azure-ad"></a>Utilisez un certificat dans le magasin de clés local pour vous connecter à Azure AD

1. Créer un certificat principal de service à l’aide de la commande Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac).

    ```azurecli
    az ad sp create-for-rbac --create-cert
    ```

    Cette commande crée un fichier .pem (clé privée) qui est stocké dans votre répertoire de base. Déployez le certificat dans le magasin *LocalMachine* ou *CurrentUser*.

    > [!Important]
    > La commande CLI génère un fichier .pem, mais Windows fournit uniquement un support natif pour les certificats PFX. Pour générer un certificat PFX à la place, utilisez les commandes PowerShell indiqués ici : [Créer un principal de service avec un certificat auto-signé](../../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate). Ces commandes déploient également automatiquement le certificat.

1. Définissez la variable d’environnement **AzureServicesAuthConnectionString** sur la valeur suivante :

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```

    Remplacez *{AppId}* , *{TenantId}* et *{Thumbprint}* par les valeurs générées à l’étape 1. Remplacez *{CertificateStore}* par *LocalMachine* ou *CurrentUser*, en fonction de votre plan de déploiement.

1. Exécutez l'application.

### <a name="use-a-shared-secret-credential-to-sign-into-azure-ad"></a>Utilisez une information d’identification de secret partagé pour vous connecter à Azure AD

1. Créez un certificat principal de service avec un mot de passe à l’aide de la commande Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) et du paramètre --sdk-auth.

    ```azurecli
    az ad sp create-for-rbac --sdk-auth
    ```

1. Définissez la variable d’environnement **AzureServicesAuthConnectionString** sur la valeur suivante :

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}
    ```

    Remplacez _{AppId}_ , _{TenantId}_ et _{ClientSecret}_ par les valeurs générées à l’étape 1.

1. Exécutez l'application.

Une fois les systèmes correctement configurés, le code n’a pas besoin d’être modifié plus avant. `AzureServiceTokenProvider` utilise la variable d’environnement et le certificat pour l’authentification auprès d’Azure AD.

### <a name="use-a-certificate-in-key-vault-to-sign-into-azure-ad"></a>Utilisez un certificat dans Key Vault pour vous connecter à Azure AD

Cette option vous permet de stocker le certificat de client d’un principal de service dans Key Vault et l’utiliser pour l’authentification de principal de service. Vous pouvez utiliser cette option pour les scénarios suivants :

- Authentification locale, dans laquelle vous souhaitez vous authentifier à l’aide d’un principal de service explicite et que vous souhaitez conserver les informations d’identification du principal de service en toute sécurité dans un coffre de clés. Compte de développeur doit avoir accès au coffre de clés.

- Authentification à partir d’Azure où vous souhaitez utiliser les informations d’identification explicites et pour conserver les informations d’identification du principal de service en toute sécurité dans un coffre de clés. Vous pouvez utiliser cette option pour un scénario interlocataire. Identité managée doit avoir accès au coffre de clés.

L’identité managée ou votre identité de développeur doit avoir l’autorisation de récupérer le certificat de client à partir du coffre de clés. La bibliothèque AppAuthentication utilise le certificat récupéré comme informations d’identification du client du principal de service.

Pour utiliser un certificat client pour l’authentification de principal de service :

1. Créez un certificat de principal du service et stockez-le automatiquement dans votre Key Vault. Utilisez la commande Azure CLI [az ad sp create-for-rbac --keyvault \<keyvaultname> --cert \<certificatename> --create-cert --skip-assignment](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

    ```azurecli
    az ad sp create-for-rbac --keyvault <keyvaultname> --cert <certificatename> --create-cert --skip-assignment
    ```

    L’identificateur de certificat sera une URL au format `https://<keyvaultname>.vault.azure.net/secrets/<certificatename>`

1. Remplacez `{KeyVaultCertificateSecretIdentifier}` dans cette chaîne de connexion avec l’identificateur de certificat :

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}
    ```

    Par exemple, si votre coffre de clés a été appelé *myKeyVault* et que vous avez créé un certificat nommé *myCert*, l’identificateur de certificat serait :

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier=https://myKeyVault.vault.azure.net/secrets/myCert
    ```

## <a name="connection-string-support"></a>Prise en charge de chaînes de connexion

Par défaut, `AzureServiceTokenProvider` essaie les méthodes d’authentification suivantes, dans l’ordre, pour récupérer un jeton :

- [Identité managée pour les ressources Azure](../..//active-directory/managed-identities-azure-resources/overview.md)
- Authentification Visual Studio
- [Authentification Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)
- [Authentification Windows intégrée](/aspnet/web-api/overview/security/integrated-windows-authentication)

Pour contrôler le processus, utilisez une chaîne de connexion passée au constructeur `AzureServiceTokenProvider` ou spécifiée dans la variable d’environnement *AzureServicesAuthConnectionString*.  Les options suivantes sont prises en charge :

| Option de chaîne de connexion | Scénario | Commentaires|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Développement local | `AzureServiceTokenProvider` utilise Azure CLI pour obtenir un jeton. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Développement local | `AzureServiceTokenProvider` utilise Visual Studio pour obtenir un jeton. |
| `RunAs=CurrentUser` | Développement local | Non pris en charge dans .NET Core. `AzureServiceTokenProvider` utilise l’authentification intégrée Azure AD pour obtenir un jeton. |
| `RunAs=App` | [Identités managées pour les ressources Azure](../../active-directory/managed-identities-azure-resources/index.yml) | `AzureServiceTokenProvider` utilise une identité managée pour obtenir in jeton. |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [Identité affectée par l’utilisateur pour les ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) | `AzureServiceTokenProvider` utilise une identité affectée par l’utilisateur pour obtenir un jeton. |
| `RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}` | Authentification des services personnalisés | `KeyVaultCertificateSecretIdentifier` est l’identificateur du secret du certificat. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`| Principal du service | `AzureServiceTokenProvider` utilise un certificat pour obtenir un jeton de la part d’Azure AD. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | Principal du service | `AzureServiceTokenProvider` utilise un certificat pour obtenir un jeton de la part d’Azure AD.|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Principal du service |`AzureServiceTokenProvider` utilise un secret pour obtenir un jeton de la part d’Azure AD. |

## <a name="samples"></a>Exemples

Pour voir la bibliothèque `Microsoft.Azure.Services.AppAuthentication` en action, voir les exemples de code suivant.

- [Utilisation d’une identité managée pour récupérer un secret à partir d’Azure Key Vault lors de l’exécution](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

- [Déploiement par programme d’un modèle Azure Resource Manager à partir d’une machine virtuelle Azure avec une identité managée](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet).

- [Utilisation d’un exemple .NET Core et d’une identité managée pour appeler des services Azure à partir d’une machine virtuelle Linux Azure](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/).

## <a name="appauthentication-troubleshooting"></a>Résolution des problèmes AppAuthentication

### <a name="common-issues-during-local-development"></a>Problèmes courants lors du développement local

#### <a name="azure-cli-is-not-installed-youre-not-logged-in-or-you-dont-have-the-latest-version"></a>Azure CLI n’est pas installé, vous n’êtes pas connecté ou vous ne disposez pas de la dernière version

Exécutez *az account get-access-token* pour déterminer si Azure CLI affiche un jeton pour vous. Si aucun **programme de ce type n’est trouvé**, installez la [dernière version d’Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). Vous serez peut-être invité à vous connecter.

#### <a name="azureservicetokenprovider-cant-find-the-path-for-azure-cli"></a>AzureServiceTokenProvider ne peut pas trouver le chemin d’accès pour Azure CLI

AzureServiceTokenProvider recherche Azure CLI à ses emplacements d’installation par défaut. S’il ne trouve pas Azure CLI, définissez la variable d’environnement **AzureCLIPath** sur le dossier d’installation Azure CLI. AzureServiceTokenProvider ajoute la variable d’environnement à la variable d’environnement de chemin.

#### <a name="youre-logged-into-azure-cli-using-multiple-accounts-the-same-account-has-access-to-subscriptions-in-multiple-tenants-or-you-get-an-access-denied-error-when-trying-to-make-calls-during-local-development"></a>Vous êtes connecté à Azure CLI à l’aide de plusieurs comptes, le même compte a accès aux abonnements dans plusieurs locataires, ou vous recevez une erreur d’accès refusé lorsque vous essayez d’effectuer des appels pendant le développement local

À l’aide d’Azure CLI, définissez l’abonnement par défaut sur celui qui contient le compte que vous souhaitez utiliser. L’abonnement doit être dans le même locataire que la ressource à laquelle vous souhaitez accéder : **az account set --subscription [subscription-id]** . Si aucune sortie n’est visible, cela signifie que la procédure a réussi. Vérifiez que le compte approprié est désormais le compte par défaut à l’aide de la commande **az account list**.

### <a name="common-issues-across-environments"></a>Problèmes courants dans les environnements

#### <a name="unauthorized-access-access-denied-forbidden-or-similar-error"></a>Accès non autorisé, accès refusé, interdit, ou erreur similaire

Le principal utilisé n’a pas accès à la ressource à laquelle il tente d’accéder. Accordez à votre compte d’utilisateur ou au « contributeur » MSI d’App Service l’accès à une ressource. Celle-ci varie selon que vous exécutez l’exemple sur votre ordinateur local ou déployé dans Azure sur votre App Service. Certaines ressources, telles que les coffres de clés, disposent également de leurs propres [stratégies d’accès](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault#data-plane-and-access-policies) que vous pouvez utiliser pour accorder l’accès aux principaux (utilisateurs, applications et groupes).

### <a name="common-issues-when-deployed-to-azure-app-service"></a>Problèmes courants lors du déploiement sur Azure App Service

#### <a name="managed-identity-isnt-set-up-on-the-app-service"></a>L’identité gérée n’est pas configurée sur l’App Service

Vérifiez que les variables d’environnement MSI_ENDPOINT et MSI_SECRET existent à l’aide de la [console de débogage Kudu](https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/). Si ces variables d’environnement n’existent pas, l’identité managée n’est pas activée sur le App Service.

### <a name="common-issues-when-deployed-locally-with-iis"></a>Problèmes courants lors du déploiement local avec IIS

#### <a name="cant-retrieve-tokens-when-debugging-app-in-iis"></a>Impossible de récupérer les jetons lors du débogage de l’application dans IIS

Par défaut, AppAuth s’exécute dans un contexte utilisateur différent dans IIS. C’est pourquoi il n’a pas accès à l’utilisation de votre identité de développeur pour récupérer des jetons d’accès. Vous pouvez configurer IIS pour qu’il s’exécute avec votre contexte utilisateur en suivant les deux étapes suivantes :
- Configurez le pool d’applications pour que l’application Web s’exécute en tant que compte d’utilisateur actuel. Plus d’informations, cliquez [ici](https://docs.microsoft.com/iis/manage/configuring-security/application-pool-identities#configuring-iis-application-pool-identities)
- Configurez « setProfileEnvironment » sur « true ». Plus d’informations, cliquez [ici](https://docs.microsoft.com/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration). 

    - Accédez à %windir%\System32\inetsrv\config\applicationHost.config
    - Recherchez « setProfileEnvironment ». Si la valeur est définie sur « false », modifiez-la en « true ». Si elles est absente, ajoutez-la en tant qu’attribut à l’élément processModel (/configuration/system.applicationHost/applicationPools/applicationPoolDefaults/processModel/@setProfileEnvironment) et définissez-la sur « true ».

- En savoir plus sur les [identités managées pour les ressources Azure](../../active-directory/managed-identities-azure-resources/index.yml).
- Apprenez-en davantage sur les [scénarios d’authentification Azure AD](../../active-directory/develop/active-directory-authentication-scenarios.md).
