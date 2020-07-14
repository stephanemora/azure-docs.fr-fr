---
title: Tutoriel sur la rotation des ressources avec un seul jeu d’informations d’authentification
description: Utilisez ce tutoriel pour découvrir comment automatiser la rotation d’un secret pour des ressources qui utilisent un seul jeu d’informations d’authentification.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.openlocfilehash: 9bff8c040f4cfed612278dd83ebb354b31a3a1f3
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85801442"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-use-one-set-of-authentication-credentials"></a>Automatiser la rotation d’un secret pour des ressources qui utilisent un seul jeu d’informations d’authentification

La meilleure façon de s’authentifier auprès des services Azure consiste à utiliser une [identité managée](../general/managed-identity.md). Il existe toutefois des scénarios dans lesquels cette approche est inappropriée. Dans ces cas-là, des clés d’accès ou des secrets sont utilisés. Vous devez permuter régulièrement les clés d’accès ou les secrets.

Ce tutoriel montre comment automatiser la rotation régulière des secrets pour les bases de données et les services qui utilisent un seul jeu d’informations d’authentification. Plus précisément, ce tutoriel permute les mots de passe SQL Server stockés dans Azure Key Vault à l’aide d’une fonction déclenchée par une notification Azure Event Grid :

![Diagramme de la solution de permutation](../media/rotate1.png)

1. Trente jours avant la date d’expiration d’un secret, Key Vault publie l’événement « Expiration proche » sur Event Grid.
1. Event Grid vérifie les abonnements aux événements et utilise HTTP POST pour appeler le point de terminaison de l’application de fonction abonné à l’événement.
1. L’application de fonction reçoit les informations du secret, génère un nouveau mot de passe aléatoire et crée une version pour le secret avec le nouveau mot de passe dans Key Vault.
1. L’application de fonction met à jour SQL Server avec le nouveau mot de passe.

> [!NOTE]
> Il peut y avoir un décalage entre les étapes 3 et 4. Pendant ce temps, le secret dans Key Vault ne peut pas s’authentifier auprès de SQL Server. En cas de d’échec de l’une des étapes, Event Grid procède à de nouvelles tentatives pendant deux heures.

## <a name="create-a-key-vault-and-sql-server-instance"></a>Créer un coffre de clés et une instance SQL Server

La première étape consiste à créer un coffre de clés ainsi qu’une instance et une base de données SQL Server, puis à stocker le mot de passe administrateur SQL Server dans Key Vault.

Pour créer des composants, ce tutoriel utilise un modèle Azure Resource Manager existant. Le code est disponible ici : [exemple de modèle de rotation de secret de base](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates).

1. Sélectionnez le lien de déploiement d’un modèle Azure :
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Sous **Groupe de ressources**, sélectionnez **Créer**. Nommez le groupe **simplerotation**.
1. Sélectionnez **Achat**.

    ![Créer un groupe de ressources](../media/rotate2.png)

Vous disposez maintenant d’un coffre de clés, d’une instance SQL Server et d’une base de données SQL. Vous pouvez vérifier cette configuration dans l’interface de ligne de commande Azure CLI en exécutant la commande suivante :

```azurecli
az resource list -o table
```

Le résultat doit ressembler à la sortie suivante :

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv          simplerotation      eastus      Microsoft.KeyVault/vaults
simplerotation-sql         simplerotation      eastus      Microsoft.Sql/servers
simplerotation-sql/master  simplerotation      eastus      Microsoft.Sql/servers/databases
```

## <a name="create-a-function-app"></a>Créer une application de fonction

Ensuite, créez une application de fonction avec une identité managée par le système en plus des autres composants nécessaires.

L’application de fonction nécessite les composants suivants :
- Un plan Azure App Service
- Un compte de stockage
- Une stratégie d’accès pour accéder aux secrets dans Key Vault par le biais d’une identité managée d’application de fonction

1. Sélectionnez le lien de déploiement d’un modèle Azure :
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Dans la liste **Groupe de ressources**, sélectionnez **simplerotation**.
1. Sélectionnez **Achat**.

   ![Sélectionner Achat](../media/rotate3.png)

Après avoir effectué les étapes précédentes, vous disposez d’un compte de stockage, d’une batterie de serveurs et d’une application de fonction. Vous pouvez vérifier cette configuration dans l’interface de ligne de commande (CLI) Azure en exécutant la commande suivante :

```azurecli
az resource list -o table
```

Le résultat doit ressembler à la sortie suivante :

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv          simplerotation       eastus      Microsoft.KeyVault/vaults
simplerotation-sql         simplerotation       eastus      Microsoft.Sql/servers
simplerotation-sql/master  simplerotation       eastus      Microsoft.Sql/servers/databases
simplerotationstrg         simplerotation       eastus      Microsoft.Storage/storageAccounts
simplerotation-plan        simplerotation       eastus      Microsoft.Web/serverFarms
simplerotation-fn          simplerotation       eastus      Microsoft.Web/sites
```

Pour plus d’informations sur la création d’une application de fonction et sur l’utilisation d’une identité managée pour accéder à Key Vault, consultez [Créer une application de fonction à partir du portail Azure](../../azure-functions/functions-create-function-app-portal.md) et [Fournir une authentification Key Vault avec une identité managée](../general/managed-identity.md).

### <a name="rotation-function"></a>Fonction de permutation
La fonction utilise un événement pour déclencher la permutation d’un secret en mettant à jour Key Vault et la base de données SQL.

#### <a name="function-trigger-event"></a>Événement de déclencheur de fonction

Cette fonction lit les données d’événement et exécute la logique de permutation :

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

#### <a name="secret-rotation-logic"></a>Logique de permutation du secret
Cette méthode de permutation lit les informations de la base de données à partir du secret, crée une version du secret et met à jour la base de données avec le nouveau secret :

```csharp
public class SecretRotator
    {
       private const string UserIdTagName = "UserID";
       private const string DataSourceTagName = "DataSource";
       private const int SecretExpirationDays = 31;

    public static void RotateSecret(ILogger log, string secretName, string secretVersion, string keyVaultName)
    {
           //Retrieve current secret
           var kvUri = "https://" + keyVaultName + ".vault.azure.net";
           var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
           KeyVaultSecret secret = client.GetSecret(secretName, secretVersion);
           log.LogInformation("Secret Info Retrieved");
        
           //Retrieve secret info
           var userId = secret.Properties.Tags.ContainsKey(UserIdTagName) ?  
                        secret.Properties.Tags[UserIdTagName] : "";
           var datasource = secret.Properties.Tags.ContainsKey(DataSourceTagName) ? 
                            secret.Properties.Tags[DataSourceTagName] : "";
           log.LogInformation($"Data Source Name: {datasource}");
           log.LogInformation($"User Id Name: {userId}");
        
           //Create new password
           var randomPassword = CreateRandomPassword();
           log.LogInformation("New Password Generated");
        
           //Check DB connection using existing secret
           CheckServiceConnection(secret);
           log.LogInformation("Service Connection Validated");
                    
           //Create new secret with generated password
           CreateNewSecretVersion(client, secret, randomPassword);
           log.LogInformation("New Secret Version Generated");
        
           //Update DB password
           UpdateServicePassword(secret, randomPassword);
           log.LogInformation("Password Changed");
           log.LogInformation($"Secret Rotated Succesffuly");
    }
}
```
Le code complet est disponible sur [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/rotation-function).

#### <a name="function-deployment"></a>Déploiement de la fonction

1. Téléchargez le fichier zip de l’application de fonction à partir de [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip).

1. Chargez le fichier simplerotationsample-fn.zip dans Azure Cloud Shell.

   ![Charger le fichier](../media/rotate4.png)
1. Utilisez cette commande Azure CLI pour déployer le fichier zip sur l’application de fonction :

   ```azurecli
   az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
   ```

Une fois la fonction déployée, vous devez voir deux fonctions sous simplerotation-fn :

![Fonctions SimpleRotation et SimpleRotationHttpTest](../media/rotate5.png)

## <a name="add-an-event-subscription-for-the-secretnearexpiry-event"></a>Ajouter un abonnement aux événements pour l’événement SecretNearExpiry

Copiez la clé `eventgrid_extension` de l’application de fonction :

   ![Sélectionnez Paramètres de l’application de fonction](../media/rotate6.png)

   ![Clé eventgrid_extension](../media/rotate7.png)

Utilisez la clé `eventgrid_extension` copiée et votre ID d’abonnement dans la commande suivante afin de créer un abonnement Event Grid pour les événements `SecretNearExpiry` :

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

## <a name="add-the-secret-to-key-vault"></a>Ajouter le secret à Key Vault
Définissez votre stratégie d’accès pour accorder des autorisations *Gérer les secrets* aux utilisateurs :

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

Créez un secret avec des étiquettes qui contiennent la source de données de base de données SQL et l’ID utilisateur. Incluez une date d’expiration définie pour demain.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

La création d’un secret avec une date d’expiration proche entraîne la publication immédiate d’un événement `SecretNearExpiry`, qui déclenche à son tour la fonction de permutation du secret.

## <a name="test-and-verify"></a>Tester et vérifier
Après quelques minutes, le secret `sqluser` doit subir une permutation automatique.

Pour vérifier que le secret a permuté, accédez à **Key Vault** > **Secrets** :

![Accéder à Secrets](../media/rotate8.png)

Ouvrez le secret **sqluser**, puis affichez la version d’origine et la version permutée :

![Ouvrir le secret sqluser](../media/rotate9.png)

### <a name="create-a-web-app"></a>Créer une application web

Pour vérifier les informations d’identification SQL, créez une application web. Cette application web obtient le secret à partir de Key Vault, extrait les informations de la base de données SQL et les informations d’identification du secret, puis teste la connexion à SQL Server.

L’application web nécessite les composants suivants :
- Une application web avec identité managée par le système
- Une stratégie d’accès pour accéder aux secrets dans Key Vault par le biais d’une identité managée d’application web

1. Sélectionnez le lien de déploiement d’un modèle Azure :
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Sélectionnez le groupe de ressources **simplerotation**.
1. Sélectionnez **Achat**.

### <a name="deploy-the-web-app"></a>Déployer l’application web

Vous trouverez le code source de l’application web sur [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp).

Pour déployer l’application web, effectuez les étapes suivantes :

1. Téléchargez le fichier zip de l’application de fonction à partir de [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip).
1. Chargez le fichier simplerotationsample-app.zip dans Azure Cloud Shell.
1. Utilisez cette commande Azure CLI pour déployer le fichier zip sur l’application de fonction :

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

### <a name="open-the-web-app"></a>Ouvrir l’application web

Accédez à l’application déployée, puis sélectionnez l’URL :
 
![Sélectionner l’URL](../media/rotate10.png)

Lorsque l’application s’ouvre dans le navigateur, vous voyez la **Valeur du secret généré** et une **Base de données connectée** dont la valeur est *true*.

## <a name="learn-more"></a>En savoir plus

- Présentation : [Monitoring de Key Vault avec Azure Event Grid (préversion)](../general/event-grid-overview.md)
- Procédure : [Recevoir un e-mail en cas de changement d’un secret de coffre de clés](../general/event-grid-logicapps.md)
- [Schéma des événements Azure Event Grid pour Azure Key Vault (préversion)](../../event-grid/event-schema-key-vault.md)
