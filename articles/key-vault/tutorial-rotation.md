---
title: Tutoriel sur la rotation d’un mot de passe/utilisateur unique
description: Utilisez ce tutoriel pour automatiser la rotation d’un mot de passe/utilisateur unique
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.openlocfilehash: 890932f7c0e46a2c9c0b6e1cf1461e4d7d25b409
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "79223356"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-with-single-userpassword-authentication"></a>Automatiser la rotation d’un secret pour les ressources avec authentification par mot de passe/utilisateur unique

Bien que la meilleure façon de s’authentifier auprès des services Azure consiste à utiliser une [identité managée](managed-identity.md), il existe des scénarios dans lesquels cette approche est inappropriée. Dans ces cas, des clés d’accès ou secrets sont utilisés. Il est nécessaire de faire régulièrement pivoter les clés d’accès ou secrets.

Ce tutoriel montre comment automatiser la rotation périodique des secrets pour les bases de données et les services avec authentification par mot de passe/utilisateur unique. Plus précisément, ce scénario fait pivoter les mots de passe SQL Server stockés dans le coffre de clés à l’aide d’une fonction déclenchée par une notification Event Grid :

![Diagramme de la rotation](./media/rotate1.png)

1. Trente jours avant la date d’expiration d’un secret, Key Vault publie l’événement « Expiration proche » sur Event Grid.
1. Event Grid vérifie les abonnements aux événements et, à l’aide du protocole http, appelle le point de terminaison Function App abonné à cet événement.
1. L’application de fonction reçoit les informations du secret, génère un nouveau mot de passe aléatoire et crée une version pour le secret avec un nouveau mot de passe dans Key Vault.
1. L’application de fonction met à jour SQL avec le nouveau mot de passe.

> [!NOTE]
> Il peut y avoir un délai entre les étapes 3 et 4, au cours duquel le secret dans Key Vault n’est pas valide pour l’authentification auprès de SQL. En cas de défaillance dans l’une des étapes, Event Grid procède à de nouvelles tentatives pendant 2 heures.

## <a name="setup"></a>Programme d’installation

## <a name="create-a-key-vault-and-sql-server"></a>Créer un coffre de clés et un serveur SQL

Avant de commencer, nous devons créer un coffre de clés, un serveur SQL et une base de données SQL, puis stocker le mot de passe d’administrateur SQL Server dans Key Vault.

Pour créer les composants, ce tutoriel utilise un modèle Azure Resource Manager existant. Vous trouverez le code entier ici : [exemple de modèle de rotation de secret de base](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates).

1. Cliquez sur le lien de déploiement d’un modèle Azure :
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Pour « Groupe de ressources », sélectionnez « Créer » et donnez-lui le nom « simplerotation ».
1. Sélectionnez « Achat ».

    ![Créer un groupe de ressources](./media/rotate2.png)

À l’issue de ces étapes, vous disposez d’un coffre de clés, d’un serveur SQL et d’une base de données SQL. Vous pouvez le vérifier dans un terminal Azure CLI en exécutant la commande suivante :

```azurecli
az resource list -o table
```

Les résultats ressembleront à ce qui suit :

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv             simplerotation             eastus      Microsoft.KeyVault/vaults
simplerotation-sql            simplerotation             eastus      Microsoft.Sql/servers
simplerotation-sql/master     simplerotation             eastus      Microsoft.Sql/servers/databases
```

## <a name="create-function-app"></a>Créer une application de fonction

Créez une application de fonction avec une identité managée par le système ainsi que les composants supplémentaires requis : 

L’application de fonction nécessite les composants et la configuration ci-dessous :
- Plan App Service
- Compte de stockage
- Stratégie d’accès pour accéder aux secrets dans Key Vault à l’aide d’une identité managée d’application de fonction

1. Cliquez sur le lien de déploiement d’un modèle Azure :
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Pour « Groupe de ressources », sélectionnez « simplerotation ».
1. Sélectionnez « Achat ».

   ![Écran d’achat](./media/rotate3.png)

À l’issue des étapes ci-dessus, vous disposez d’un compte de stockage, d’une batterie de serveurs et d’une application de fonction.  Vous pouvez le vérifier dans un terminal Azure CLI en exécutant la commande suivante :

```azurecli
az resource list -o table
```

Les résultats ressembleront à ce qui suit :

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv             simplerotation             eastus      Microsoft.KeyVault/vaults
simplerotation-sql            simplerotation             eastus      Microsoft.Sql/servers
simplerotation-sql/master     simplerotation             eastus      Microsoft.Sql/servers/databases
simplerotationstrg            simplerotation             eastus      Microsoft.Storage/storageAccounts
simplerotation-plan           simplerotation             eastus      Microsoft.Web/serverFarms
simplerotation-fn             simplerotation             eastus      Microsoft.Web/sites
```

Pour plus d’informations sur la création d’une application de fonction et sur l’utilisation d’une identité managée pour accéder à Key Vault, consultez [Créer une application de fonction à partir du portail Azure](../azure-functions/functions-create-function-app-portal.md) et [Fournir une authentification Key Vault avec une identité managée](managed-identity.md).

### <a name="rotation-function-and-deployment"></a>Fonction de rotation et déploiement
La fonction utilise l’événement comme déclencheur et effectue la rotation d’un secret en mettant à jour Key Vault et la base de données SQL.

#### <a name="function-event-trigger-handler"></a>Gestionnaire du déclencheur d’événements de fonction

La fonction ci-dessous lit les données d’événement et exécute la logique de rotation.

```csharp
public static class SimpleRotationEventHandler
{
    [FunctionName("SimpleRotation")]
       public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
       {
            log.LogInformation("C# Event trigger function processed a request.");
            var secretName = eventGridEvent.Subject;
            var secretVersion = Regex.Match(eventGridEvent.Data.ToString(), "Version\":\"([a-z0-9]*)").Groups[1].ToString();
            var keyVaultName = Regex.Match(eventGridEvent.Topic, ".vaults.(.*)").Groups[1].ToString();
            log.LogInformation($"Key Vault Name: {keyVaultName}");
            log.LogInformation($"Secret Name: {secretName}");
            log.LogInformation($"Secret Version: {secretVersion}");

            SeretRotator.RotateSecret(log, secretName, secretVersion, keyVaultName);
        }
}
```

#### <a name="secret-rotation-logic"></a>Logique de rotation du secret
Cette méthode de rotation lit les informations de la base de données à partir du secret, crée une version du secret et met à jour la base de données avec un nouveau secret.

```csharp
public class SecretRotator
    {
       private const string UserIdTagName = "UserID";
       private const string DataSourceTagName = "DataSource";
       private const int SecretExpirationDays = 31;

    public static void RotateSecret(ILogger log, string secretName, string secretVersion, string keyVaultName)
    {
           //Retrieve Current Secret
           var kvUri = "https://" + keyVaultName + ".vault.azure.net";
           var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
           KeyVaultSecret secret = client.GetSecret(secretName, secretVersion);
           log.LogInformation("Secret Info Retrieved");
        
           //Retrieve Secret Info
           var userId = secret.Properties.Tags.ContainsKey(UserIdTagName) ?  
                        secret.Properties.Tags[UserIdTagName] : "";
           var datasource = secret.Properties.Tags.ContainsKey(DataSourceTagName) ? 
                            secret.Properties.Tags[DataSourceTagName] : "";
           log.LogInformation($"Data Source Name: {datasource}");
           log.LogInformation($"User Id Name: {userId}");
        
           //create new password
           var randomPassword = CreateRandomPassword();
           log.LogInformation("New Password Generated");
        
           //Check db connection using existing secret
           CheckServiceConnection(secret);
           log.LogInformation("Service Connection Validated");
                    
           //Create new secret with generated password
           CreateNewSecretVersion(client, secret, randomPassword);
           log.LogInformation("New Secret Version Generated");
        
           //Update db password
           UpdateServicePassword(secret, randomPassword);
           log.LogInformation("Password Changed");
           log.LogInformation($"Secret Rotated Succesffuly");
    }
}
```
Vous trouverez le code entier ici : https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/rotation-function

#### <a name="function-deployment"></a>Déploiement de la fonction

1. Téléchargez le fichier zip de l’application de fonction : https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip

1. Chargez le fichier simplerotationsample-fn.zip sur Azure Cloud Shell.
 
1. Utilisez la commande CLI ci-dessous pour déployer le fichier zip sur l’application de fonction :

```azurecli
az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
```
![Écran d’achat](./media/rotate4.png)

Après le déploiement, vous devez remarquer deux fonctions sous simplerotation-fn :

![Azure Cloud Shell](./media/rotate5.png)

### <a name="add-event-subscription-for-secretnearexpiry-event"></a>Ajouter un abonnement aux événements pour l’événement « SecretNearExpiry »

Copiez la clé eventgrid_extension de l’application de fonction.

![Azure Cloud Shell](./media/rotate6.png)

![Tester et vérifier](./media/rotate7.png)

Utilisez la clé d’extension eventgrid copiée et votre ID d’abonnement dans la commande ci-dessous afin de créer un abonnement Event Grid pour les événements SecretNearExpiry.

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

### <a name="add-secret-to-key-vault"></a>Ajouter un secret à Key Vault
Définissez votre stratégie d’accès pour accorder l’autorisation « Gérer les secrets » aux utilisateurs.

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

Créez maintenant un secret avec des étiquettes contenant la source de données de base de données SQL et l’ID utilisateur, avec la date d’expiration définie pour demain.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

La création d’un secret avec une date d’expiration proche entraîne la publication immédiate d’un événement SecretNearExpiry, qui déclenche à son tour la fonction de rotation du secret.

### <a name="test-and-verify"></a>Tester et vérifier
Après quelques minutes, le secret sqluser doit subir une rotation automatique.

Pour vérifier la rotation du secret, accédez à Key Vault > Secrets.

![Tester et vérifier](./media/rotate8.png)

Ouvrez le secret « sqluser », puis affichez la version d’origine et la version pivotée.

![Tester et vérifier](./media/rotate9.png)

## <a name="create-web-app"></a>Créer une application web

Pour vérifier les informations d’identification SQL, créez une application web. Cette application web obtient le secret à partir du coffre de clés, extrait les informations de la base de données SQL et les informations d’identification du secret, puis teste la connexion à SQL.

L’application web nécessite les composants et la configuration ci-dessous :
- Application web avec identité managée par le système
- Stratégie d’accès pour accéder aux secrets dans Key Vault à l’aide d’une identité managée d’application web

1. Cliquez sur le lien de déploiement d’un modèle Azure :
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Sélectionnez le groupe de ressources **simplerotation**.
1. Cliquez sur Achat.

### <a name="deploy-web-app"></a>Déployer l’application web

Vous trouverez le code source de l’application web à la page https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp. Pour le déploiement de l’application web, procédez comme suit :

1. Téléchargez le fichier zip de l’application de fonction à partir de https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip.
1. Chargez le fichier `simplerotationsample-app.zip` sur Azure Cloud Shell.
1. Utilisez cette commande Azure CLI pour déployer le fichier zip sur l’application de fonction :

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

#### <a name="open-web-application"></a>Ouvrir l’application web

Accédez à l’application déployée, puis cliquez sur « URL » :
 
![Tester et vérifier](./media/rotate10.png)

La valeur du secret généré doit apparaître avec Base de données connectée défini sur true.

## <a name="learn-more"></a>En savoir plus :

- Présentation : [Monitoring de Key Vault avec Azure Event Grid (préversion)](event-grid-overview.md)
- Procédure : [Recevoir un e-mail en cas de changement d’un secret de coffre de clés](event-grid-logicapps.md)
- [Schéma des événements Azure Event Grid pour Azure Key Vault (préversion)](../event-grid/event-schema-key-vault.md)
