---
title: Gérer les versions de l’API AuthN/AuthZ
description: Mettez à niveau votre API d’authentification App Service vers V2 ou épinglez-la à une version spécifique, si nécessaire.
ms.topic: article
ms.date: 03/29/2021
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: e526f4a56806fa41d8955337d7e8463afac80b21
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113094928"
---
# <a name="manage-the-api-and-runtime-versions-of-app-service-authentication"></a>Gérer les versions d’API et d’exécution de l’authentification App Service

Cet article vous explique comment personnaliser les versions d’API et d’exécution [d’authentification et d’autorisation intégrées dans App Service](overview-authentication-authorization.md).

Il existe deux versions de l’API de gestion pour l’authentification App Service. La version v2 est requise pour l’expérience « Authentification » dans le portail Azure. Une application qui utilise déjà l’API v1 peut effectuer une mise à niveau vers la version v2 une fois que quelques modifications ont été apportées. Plus précisément, la configuration du secret doit être déplacée vers les paramètres d’application à emplacement fixe. Vous pouvez effectuer cette opération automatiquement à partir de la section « Authentification » du portail de votre application.

## <a name="update-the-configuration-version"></a>Mettre à jour la version de configuration

> [!WARNING]
> La migration vers la version 2 désactivera la gestion de la fonctionnalité d’authentification/autorisation d’App Service de l’application par le biais de certains clients, comme son expérience actuelle dans le portail Azure, Azure CLI et Azure PowerShell. Cette opération ne peut pas être inversée.

L’API v2 ne prend pas en charge la création ni la modification d’un compte Microsoft en tant que fournisseur distinct, comme c’était le cas dans la version 1. Elle s’appuie plutôt sur la [plateforme d’identités Microsoft](../active-directory/develop/v2-overview.md) convergente pour permettre aux utilisateurs de se connecter avec Azure AD et leurs comptes Microsoft personnels. Lors du basculement vers l’API v2, la configuration v1 d’Azure Active Directory est utilisée pour configurer le fournisseur Plateforme d’identités Microsoft. Le fournisseur de comptes Microsoft v1 sera reporté dans le processus de migration et continuera à fonctionner normalement, mais il est recommandé de passer au modèle plus récent de la plateforme d’identités Microsoft. Pour en savoir plus, consultez [Prise en charge des inscriptions de fournisseur de comptes Microsoft](#support-for-microsoft-account-provider-registrations).

Le processus de migration automatique déplacera les secrets du fournisseur dans les paramètres de l’application, puis convertira le reste de la configuration au nouveau format. Pour utiliser la migration automatique :

1. Accédez à votre application dans le portail et sélectionnez l’option de menu **Authentification**.
1. Si l’application est configurée à l’aide du modèle v1, un bouton **Mettre à niveau** s’affiche.
1. Lisez la description de la demande de confirmation. Si vous êtes prêt à effectuer la migration, cliquez sur **Mettre à niveau** dans l’invite.

### <a name="manually-managing-the-migration"></a>Gestion manuelle de la migration

Les étapes suivantes vous permettront de migrer manuellement l’application vers l’API v2 si vous ne souhaitez pas utiliser la version automatique mentionnée ci-dessus.

#### <a name="moving-secrets-to-application-settings"></a>Déplacement de secrets vers des paramètres d’application

1. Récupérez votre configuration actuelle à l’aide de l’API v1 :

   ```azurecli
   az webapp auth show -g <group_name> -n <site_name>
   ```

   Dans la charge utile JSON résultante, prenez note de la valeur de secret utilisée pour chaque fournisseur que vous avez configuré :

   * AAD : `clientSecret`
   * Google : `googleClientSecret`
   * Facebook : `facebookAppSecret`
   * Twitter : `twitterConsumerSecret`
   * Compte Microsoft : `microsoftAccountClientSecret`

   > [!IMPORTANT]
   > Les valeurs de secret sont des informations d’identification de sécurité importantes et doivent être manipulées avec précaution. Ne partagez pas ces valeurs et ne les conservez pas sur un ordinateur local.

1. Créez des paramètres d’application à emplacement fixe pour chaque valeur de secret. Vous pouvez choisir le nom de chaque paramètre d’application. Sa valeur doit correspondre à ce que vous avez obtenu à l’étape précédente ou [référencer un secret Key Vault](./app-service-key-vault-references.md?toc=/azure/azure-functions/toc.json) que vous avez créé avec cette valeur.

   Pour créer le paramètre, vous pouvez utiliser le portail Azure ou exécuter une variation du code suivant pour chaque fournisseur :

   ```azurecli
   # For Web Apps, Google example    
   az webapp config appsettings set -g <group_name> -n <site_name> --slot-settings GOOGLE_PROVIDER_AUTHENTICATION_SECRET=<value_from_previous_step>

   # For Azure Functions, Twitter example
   az functionapp config appsettings set -g <group_name> -n <site_name> --slot-settings TWITTER_PROVIDER_AUTHENTICATION_SECRET=<value_from_previous_step>
   ```

   > [!NOTE]
   > Les paramètres d’application pour cette configuration doivent être marqués comme étant à emplacement fixe, ce qui signifie qu’ils ne seront pas déplacés d’un environnement à un autre lors d’une [opération d’échange d’emplacements](./deploy-staging-slots.md). Cela est dû au fait que votre configuration d’authentification est elle-même liée à l’environnement. 

1. Créez un fichier JSON nommé `authsettings.json`. Prenez la sortie que vous avez reçue précédemment et supprimez chaque valeur de secret de celle-ci. Écrivez le reste de la sortie dans le fichier, en vous assurant qu’aucun secret n’y est inclus. Dans certains cas, la configuration peut comporter des tableaux contenant des chaînes vides. Assurez-vous que `microsoftAccountOAuthScopes` n’en contient pas, et si c’est le cas, remplacez cette valeur par `null`.

1. Ajoutez une propriété à `authsettings.json` qui pointe vers le nom du paramètre d’application que vous avez créé précédemment pour chaque fournisseur :
 
   * AAD : `clientSecretSettingName`
   * Google : `googleClientSecretSettingName`
   * Facebook : `facebookAppSecretSettingName`
   * Twitter : `twitterConsumerSecretSettingName`
   * Compte Microsoft : `microsoftAccountClientSecretSettingName`

   Un exemple de fichier après cette opération peut ressembler à ce qui suit, ici configuré uniquement pour AAD :

   ```json
   {
       "id": "/subscriptions/00d563f8-5b89-4c6a-bcec-c1b9f6d607e0/resourceGroups/myresourcegroup/providers/Microsoft.Web/sites/mywebapp/config/authsettings",
       "name": "authsettings",
       "type": "Microsoft.Web/sites/config",
       "location": "Central US",
       "properties": {
           "enabled": true,
           "runtimeVersion": "~1",
           "unauthenticatedClientAction": "AllowAnonymous",
           "tokenStoreEnabled": true,
           "allowedExternalRedirectUrls": null,
           "defaultProvider": "AzureActiveDirectory",
           "clientId": "3197c8ed-2470-480a-8fae-58c25558ac9b",
           "clientSecret": "",
           "clientSecretSettingName": "MICROSOFT_IDENTITY_AUTHENTICATION_SECRET",
           "clientSecretCertificateThumbprint": null,
           "issuer": "https://sts.windows.net/0b2ef922-672a-4707-9643-9a5726eec524/",
           "allowedAudiences": [
               "https://mywebapp.azurewebsites.net"
           ],
           "additionalLoginParams": null,
           "isAadAutoProvisioned": true,
           "aadClaimsAuthorization": null,
           "googleClientId": null,
           "googleClientSecret": null,
           "googleClientSecretSettingName": null,
           "googleOAuthScopes": null,
           "facebookAppId": null,
           "facebookAppSecret": null,
           "facebookAppSecretSettingName": null,
           "facebookOAuthScopes": null,
           "gitHubClientId": null,
           "gitHubClientSecret": null,
           "gitHubClientSecretSettingName": null,
           "gitHubOAuthScopes": null,
           "twitterConsumerKey": null,
           "twitterConsumerSecret": null,
           "twitterConsumerSecretSettingName": null,
           "microsoftAccountClientId": null,
           "microsoftAccountClientSecret": null,
           "microsoftAccountClientSecretSettingName": null,
           "microsoftAccountOAuthScopes": null,
           "isAuthFromFile": "false"
       }   
   }
   ```

1. Envoyez ce fichier comme nouvelle configuration d’authentification/d’autorisation pour votre application :

   ```azurecli
   az rest --method PUT --url "/subscriptions/<subscription_id>/resourceGroups/<group_name>/providers/Microsoft.Web/sites/<site_name>/config/authsettings?api-version=2020-06-01" --body @./authsettings.json
   ```

1. Vérifiez que votre application fonctionne toujours comme prévu après ce geste.

1. Supprimez le fichier utilisé dans les étapes précédentes.

Vous avez maintenant migré l’application pour stocker les secrets du fournisseur d’identité comme paramètres d’application.

#### <a name="support-for-microsoft-account-provider-registrations"></a>Prise en charge des inscriptions de fournisseur de comptes Microsoft

Si votre configuration actuelle contient un fournisseur Compte Microsoft, mais pas de fournisseur Azure Active Directory, vous pouvez basculer la configuration vers le fournisseur Azure Active Directory, puis effectuer la migration. Pour ce faire :

1. Accédez à [**Inscriptions d’applications**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) dans le portail Azure et recherchez l’inscription associée à votre fournisseur Compte Microsoft. Il peut se trouver sous le titre « Applications du compte personnel ».
1. Accédez à la page « Authentification » pour l’inscription. Sous « URI de redirection », vous devez voir une entrée se terminant par `/.auth/login/microsoftaccount/callback`. Copiez cet URI.
1. Ajoutez un nouvel URI qui correspond à celui que vous venez de copier, sauf qu’il se termine par `/.auth/login/aad/callback`. Cela permettra à l’inscription d’être utilisée par la configuration d’authentification/autorisation d’App Service.
1. Accédez à la configuration d’authentification/autorisation d’App Service pour votre application.
1. Collectez la configuration du fournisseur Compte Microsoft.
1. Configurez le fournisseur Azure Active Directory à l’aide du mode d’administration « Avancé », en fournissant l’ID client et les valeurs de clé secrète client que vous avez collectés à l’étape précédente. Pour l’URL de l’émetteur, utilisez `<authentication-endpoint>/<tenant-id>/v2.0`, puis remplacez *\<authentication-endpoint>* par le [point de terminaison d’authentification de votre environnement cloud](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints) (par exemple, « https://login.microsoftonline.com  » pour Azure international), puis *\<tenant-id>* par votre **ID de répertoire (locataire)** .
1. Une fois que vous avez enregistré la configuration, testez le flux de connexion en accédant dans votre navigateur au point de terminaison `/.auth/login/aad` de votre site et en allant jusqu’au bout du flux de connexion.
1. À ce stade, vous avez copié la configuration, mais la configuration actuelle du fournisseur Compte Microsoft est conservée. Avant de la supprimer, assurez-vous que toutes les parties de votre application référencent le fournisseur Azure Active Directory via des liens de connexion, etc. Vérifiez que toutes les parties de votre application fonctionnent comme prévu.
1. Une fois que vous avez validé que les choses fonctionnent avec le fournisseur Azure Active Directory (AAD), vous pouvez supprimer la configuration du fournisseur Compte Microsoft.

> [!WARNING]
> Il est possible de converger les deux inscriptions en modifiant les [types de comptes pris en charge](../active-directory/develop/supported-accounts-validation.md) pour l’inscription de l’application AAD. Toutefois, cela obligerait les utilisateurs Compte Microsoft à donner leur consentement à une nouvelle invite, et les revendications d’identité de ces utilisateurs pourraient être différentes dans leur structure, `sub` changeant notamment de valeurs, puisqu’un nouveau ID d’application est utilisé. Cette approche n’est pas recommandée, sauf si elle est parfaitement comprise. Vous devriez plutôt attendre la prise en charge des deux inscriptions dans la surface de l’API v2.

#### <a name="switching-to-v2"></a>Passage à v2

Une fois les étapes ci-dessus effectuées, accédez à l’application dans le portail Azure. Sélectionnez la section « Authentification (préversion) ». 

Vous pouvez également effectuer une requête PUT sur la ressource `config/authsettingsv2` sous la ressource de site. Le schéma de la charge utile est le même que celui capturé dans [Configuration basée sur des fichiers](configure-authentication-file-based.md).

## <a name="pin-your-app-to-a-specific-authentication-runtime-version"></a>Épingler votre application à une version spécifique du runtime d’authentification

Lorsque vous activez l’authentification/l’autorisation, l’intergiciel (middleware) de la plateforme est injecté dans votre pipeline de requêtes HTTP, comme décrit dans la [vue d’ensemble des fonctionnalités](overview-authentication-authorization.md#how-it-works). Cet intergiciel (middleware) de plateforme est régulièrement mis à jour avec de nouvelles fonctionnalités et des améliorations dans le cadre des mises à jour de routine de la plateforme. Par défaut, votre application web ou de fonction s’exécutera sur la dernière version de cet intergiciel (middleware) de plateforme. Ces mises à jour automatiques sont toujours à compatibilité descendante. Toutefois, dans les rares cas où cette mise à jour automatique introduit un problème de runtime pour votre application web ou de fonction, vous pouvez revenir temporairement à la version précédente de l’intergiciel (middleware). Cet article explique comment épingler temporairement une application à une version spécifique de l’intergiciel (middleware) d’authentification.

### <a name="automatic-and-manual-version-updates"></a>Mises à jour de versions automatiques et manuelles 

Vous pouvez épingler votre application à une version spécifique de l’intergiciel (middleware) de plateforme en définissant un paramètre `runtimeVersion` pour l’application. Votre application s’exécute toujours sur la version la plus récente, sauf si vous choisissez de la réépingler explicitement à une version spécifique. Plusieurs versions sont prises en charge à la fois. Si vous épinglez votre application à une version non valide qui n’est plus prise en charge, votre application utilisera la version la plus récente à la place. Pour toujours exécuter la version la plus récente, définissez `runtimeVersion` sur ~1. 

### <a name="view-and-update-the-current-runtime-version"></a>Afficher et mettre à jour la version actuelle du runtime

Vous pouvez modifier la version du runtime utilisée par votre application. La nouvelle version du runtime doit prendre effet après le redémarrage de l’application. 

#### <a name="view-the-current-runtime-version"></a>Afficher la version actuelle du runtime

Vous pouvez afficher la version actuelle de l’intergiciel (middleware) d’authentification de la plateforme à l’aide d’Azure CLI ou via l’un des points de terminaison HTTP de la version intégrés dans votre application.

##### <a name="from-the-azure-cli"></a>Dans Azure CLI

À l’aide d’Azure CLI, affichez la version actuelle de l’intergiciel (middleware) avec la commande [az webapp auth show](/cli/azure/webapp/auth#az_webapp_auth_show).

```azurecli-interactive
az webapp auth show --name <my_app_name> \
--resource-group <my_resource_group>
```

Dans ce code, remplacez `<my_app_name>` par le nom de votre application. Remplacez également `<my_resource_group>` par le nom du groupe de ressources de votre application.

Le champ `runtimeVersion` est affiché dans la sortie de l’interface CLI. Il ressemble à l’exemple de sortie suivant, qui a été tronqué pour une meilleure lisibilité : 
```output
{
  "additionalLoginParams": null,
  "allowedAudiences": null,
    ...
  "runtimeVersion": "1.3.2",
    ...
}
```

##### <a name="from-the-version-endpoint"></a>À partir du point de terminaison de version

Vous pouvez également atteindre le point de terminaison /.auth/version sur une application pour afficher la version actuelle de l’intergiciel (middleware) sur laquelle l’application s’exécute. Cela ressemble à l’exemple de sortie suivant :
```output
{
"version": "1.3.2"
}
```

#### <a name="update-the-current-runtime-version"></a>Mettre à jour la version actuelle du runtime

À l’aide d’Azure CLI, vous pouvez mettre à jour le paramètre `runtimeVersion` dans l’application avec la commande [az webapp auth update](/cli/azure/webapp/auth#az_webapp_auth_update).

```azurecli-interactive
az webapp auth update --name <my_app_name> \
--resource-group <my_resource_group> \
--runtime-version <version>
```

Remplacez `<my_app_name>` par le nom de votre application. Remplacez également `<my_resource_group>` par le nom du groupe de ressources de votre application. Par ailleurs, remplacez `<version>` par une version valide du runtime 1.x ou `~1` pour la version la plus récente. Vous trouverez les [notes de publication sur les différentes versions du runtime](https://github.com/Azure/app-service-announcements) pour déterminer la version sur laquelle épingler.

Vous pouvez exécuter cette commande à partir de [Azure Cloud Shell](../cloud-shell/overview.md) en choisissant **Essayer** dans l’exemple de code qui précède. Vous pouvez également utiliser [Azure CLI en local](/cli/azure/install-azure-cli) pour exécuter cette commande après avoir lancé la commande [az login](/cli/azure/reference-index#az_login) pour vous connecter.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Authentifier et autoriser des utilisateurs de bout en bout](tutorial-auth-aad.md)
