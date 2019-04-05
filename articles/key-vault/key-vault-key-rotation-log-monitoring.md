---
title: Configuration d’Azure Key Vault avec une rotation des clés et un audit de bout en bout | Microsoft Docs
description: Utilisez ce guide pratique pour vous aider à configurer la rotation des clés et surveillez les journaux de coffre de clés.
services: key-vault
documentationcenter: ''
author: barclayn
manager: barbkess
tags: ''
ms.assetid: 9cd7e15e-23b8-41c0-a10a-06e6207ed157
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: fb3300a45f905eb57fcc4880269e4a9bed9dac0c
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2019
ms.locfileid: "59045983"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>Configurer Azure Key Vault avec une rotation des clés et un audit

## <a name="introduction"></a>Présentation

Une fois que vous avez un coffre de clés, vous pouvez commencer à l’utiliser pour stocker les clés et les secrets. Vos applications ne doivent plus nécessairement conserver vos clés et secrets, mais peuvent les demander au coffre en cas de besoin. Un coffre de clés vous permet de mettre à jour des clés et secrets sans affecter le comportement de votre application, toute une multitude de possibilités pour votre clé et la gestion des secrets.

>[!IMPORTANT]
> Les exemples de cet article sont fournis à titre d’illustration uniquement. Ils ne sont pas conçus pour la production. 

Cet article vous guide tout au long des procédures suivantes :

- Un exemple d’utilisation d’Azure Key Vault pour stocker un secret. Dans cet article, le secret stocké est la clé de compte de stockage Azure accédée par une application. 
- Comment implémenter une rotation planifiée de cette clé de compte de stockage.
- Guide pratique pour surveiller la clé de journaux d’audit du coffre et déclencher des alertes lors de requêtes inattendues.

> [!NOTE]
> Cet article n’explique en détail la configuration initiale de votre coffre de clés. Pour en savoir plus, consultez [Présentation d'Azure Key Vault](key-vault-overview.md). Pour obtenir des instructions de l’interface de ligne de commande multiplateforme, consultez [gestion de Key Vault à l’aide de l’interface CLI](key-vault-manage-with-cli2.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-up-key-vault"></a>Configuration d’Azure Key Vault

Pour qu’une application puisse récupérer un secret dans Key Vault, vous devez tout d’abord créer le secret et le télécharger dans votre coffre.

Démarrez une session Azure PowerShell et connectez-vous à votre compte Azure avec la commande suivante :

```powershell
Connect-AzAccount
```

Dans la fenêtre du navigateur contextuelle, entrez le nom d’utilisateur et le mot de passe pour votre compte Azure. PowerShell obtient alors tous les abonnements associés à ce compte. PowerShell utilise le premier par défaut.

Si vous disposez de plusieurs abonnements, vous devrez peut-être spécifier celui qui a été utilisé pour créer votre coffre de clés. Entrez la commande suivante pour afficher les abonnements de votre compte :

```powershell
Get-AzSubscription
```

Pour spécifier l’abonnement associé au coffre de clés que vous allez vous connecter, entrez :

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

Cet article présentant le stockage d’une clé de compte de stockage sous la forme d’un secret, vous devez obtenir cette clé de compte de stockage.

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Après avoir récupéré votre secret (dans ce cas, il s’agit de votre clé de compte de stockage), vous devez convertir cette clé en une chaîne sécurisée et ensuite créer un secret avec cette valeur dans votre coffre de clés.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

Ensuite, obtenez l’URI pour le secret que vous avez créé. Vous aurez besoin de cet URI dans une étape ultérieure pour appeler le coffre de clés et récupérer votre secret. Exécutez la commande PowerShell suivante et notez la valeur d’ID, les URI du secret :

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Configurer d’application

Maintenant que vous avez une clé secrète stockée, vous pouvez utiliser le code pour récupérer et l’utiliser après avoir effectué quelques étapes supplémentaires.

Tout d’abord, vous devez inscrire votre application avec Azure Active Directory. Puis indiquer à Key Vault informations de votre application afin qu’il autorise les demandes à partir de votre application.

> [!NOTE]
> Votre application doit être créée sur le même client Azure Active Directory que votre coffre de clés.

1. Ouvrez **Azure Active Directory**.
2. Sélectionnez **Inscriptions d’applications**. 
3. Sélectionnez **nouvelle inscription d’application** pour ajouter une application à Azure Active Directory.

    ![Ouvrir des applications dans Azure Active Directory](./media/keyvault-keyrotation/azure-ad-application.png)

4. Sous **créer**, laissez le type d’application **application Web / API** et donnez un nom à votre application. Donnez à votre application un **Sign-on URL**. Cette URL peut être tout ce que vous souhaitez pour cette démonstration.

    ![Créer une inscription d’application](./media/keyvault-keyrotation/create-app.png)

5. Une fois que l’application est ajoutée à Azure Active Directory, la page d’application s’ouvre. Sélectionnez **paramètres**, puis sélectionnez **propriétés**. Copiez la valeur figurant dans **ID de l’application**. Vous en aurez besoin dans les étapes ultérieures.

Ensuite, générez une clé pour votre application afin qu’il peut interagir avec Azure Active Directory. Pour créer une clé, sélectionnez **clés** sous **paramètres**. Prenez note de la clé qui vient d’être générée pour votre application Azure Active Directory. Vous en aurez besoin dans une étape ultérieure. La clé n’est pas disponible après avoir quitté cette section. 

![Clés d’application Azure Active Directory](./media/keyvault-keyrotation/create-key.png)

Avant d’établir des appels à partir de votre application dans le coffre de clés, vous devez indiquer le coffre de clés sur votre application et ses autorisations. La commande suivante utilise le nom du coffre et l’ID d’application à partir de votre application Azure Active Directory pour accorder à l’application **obtenir** accès à votre coffre de clés.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Vous êtes maintenant prêt à commencer à créer votre application appelle. Dans votre application, vous devez installer les packages NuGet qui sont requises pour interagir avec Azure Key Vault et Azure Active Directory. Entrez les commandes suivantes dans la console du gestionnaire de package Visual Studio. Au moment de la rédaction de cet article, la version actuelle du package Azure Active Directory est la version 3.10.305231913, par conséquent, vérifiez la version la plus récente et mettre à jour en fonction des besoins.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

Dans votre code d’application, créez une classe qui contiendra la méthode d’authentification de votre Azure Active Directory. Dans cet exemple, la classe est appelée **Utils**. Ajoutez l'instruction `using` suivante :

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Ajoutez ensuite la méthode suivante pour récupérer le jeton JWT d’Azure Active Directory. Pour faciliter la maintenance, vous souhaiterez déplacer les valeurs de chaîne codées en dur dans votre configuration web ou application.

```csharp
public async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);

    ClientCredential clientCred = new ClientCredential("<AzureADApplicationClientID>","<AzureADApplicationClientKey>");

    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)

    throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

Ajoutez le code nécessaire pour appeler Key Vault et récupérer votre valeur secrète. Vous devez tout d’abord, ajoutez le code suivant `using` instruction :

```csharp
using Microsoft.Azure.KeyVault;
```

Ajoutez les appels de méthode pour appeler Key Vault et récupérer votre secret. Dans cette méthode, vous fournissez l’URI de secret que vous avez enregistré dans une étape précédente. Notez l’utilisation de la **GetToken** méthode à partir de la **Utils** classe que vous avez créé précédemment.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Lorsque vous exécutez votre application, vous devez désormais vous authentifier auprès d’Azure Active Directory et récupérer votre valeur secrète dans Azure Key Vault.

## <a name="key-rotation-using-azure-automation"></a>Rotation des clés à l’aide d’Azure Automation

> [!IMPORTANT]
> Runbooks Azure Automation nécessitent encore l’utilisation de la `AzureRM` module.

Vous êtes maintenant prêt à configurer une stratégie de rotation pour les valeurs que vous stockez en tant que secrets de coffre de clés. La rotation des secrets peut être de plusieurs façons :

- Dans le cadre d’un processus manuel
- Par programmation en utilisant des appels d’API
- Via un script d’automatisation d’Azure

Dans le cadre de cet article, vous utiliserez PowerShell et Azure Automation pour modifier la clé d’accès d’un compte de stockage Azure. Vous allez ensuite mettre à jour un secret de coffre de clés avec cette nouvelle clé.

Pour permettre à Azure Automation pour définir des valeurs secrètes dans votre coffre de clés, vous devez obtenir l’ID client pour la connexion nommée **AzureRunAsConnection**. Cette connexion a été créée lorsque vous avez établi votre instance Azure Automation. Pour rechercher cet ID, sélectionnez **ressources** à partir de votre instance Azure Automation. À partir de là, sélectionnez **connexions**, puis sélectionnez le **AzureRunAsConnection** principal du service. Prenez note de la **ApplicationId** valeur.

![ID client Azure Automation](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

Dans **actifs**, sélectionnez **Modules**. Sélectionnez **galerie**, puis recherchez et importez les versions mises à jour de chacun des modules suivants :

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> Au moment de la rédaction de cet article, seuls les modules mentionnés précédemment devaient être mis à jour pour le script suivant. Si votre tâche d’automatisation échoue, vérifiez que vous avez importé tous les modules nécessaires et leurs dépendances.

Une fois que vous avez récupéré l’ID d’application pour votre connexion Azure Automation, vous devez indiquer votre coffre de clés que cette application est autorisé à mettre à jour les clés secrètes dans votre coffre. Utilisez la commande PowerShell suivante :

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Ensuite, sélectionnez **Runbooks** sous votre instance Azure Automation, puis sélectionnez **ajouter des runbooks**. Sélectionnez **Création rapide**. Nommez votre runbook, puis sélectionnez **PowerShell** comme type de runbook. Vous pouvez ajouter une description. Pour finir, sélectionnez **Créer**.

![Créer un runbook](./media/keyvault-keyrotation/Create_Runbook.png)

Collez le script PowerShell suivant dans le volet de l’éditeur de votre nouveau runbook :

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection"
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Connect-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
    "Login complete."
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

# Optionally you can set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureRmKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

Dans le volet de l’éditeur, sélectionnez **volet de Test** pour tester votre script. Une fois que le script s’exécute sans erreur, vous pouvez sélectionner **publier**, et vous pouvez ensuite appliquer une planification pour le runbook dans le panneau de configuration de runbook.

## <a name="key-vault-auditing-pipeline"></a>Pipeline d’audit de Key Vault

Lorsque vous configurez un coffre de clés, vous pouvez activer la fonction d’audit afin de collecter des journaux de demandes d’accès au coffre de clés. Ces journaux sont stockés dans un compte de stockage Azure désigné et peut être récupérés, contrôlés et analysés. Le scénario suivant utilise Azure functions, Azure logic apps et les journaux d’audit de coffre de clés pour créer un pipeline qui envoie un e-mail quand une application qui ne correspond pas à l’ID d’application de l’application web récupère les secrets du coffre.

Vous devez tout d’abord activer la journalisation sur votre coffre de clés. Utilisez les commandes PowerShell suivantes. (Vous pouvez afficher les détails complets dans [cet article sur la journalisation de coffre de clé](key-vault-logging.md).)

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

Une fois que la journalisation est activée, les journaux d’audit démarrer en cours stockées dans le compte de stockage désigné. Ces journaux contiennent des événements indiquant la méthode et la date/l’heure d’accès à vos coffres de clés, et qui y a accédé.

> [!NOTE]
> Vous pouvez accéder aux informations de journalisation 10 minutes après l’opération sur le coffre de clés. Il souvent sera disponible plus tôt que.

L’étape suivante consiste à [créer une file d’attente Azure Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Cette file d’attente est où les journaux d’audit de coffre de clés sont envoyés. Lorsque les messages de journal d’audit se trouvent dans la file d’attente, l’application logique les récupère et agit en conséquence. Créer une instance de Service Bus avec les étapes suivantes :

1. Créer un espace de noms Service Bus (si vous avez déjà une que vous souhaitez utiliser, passez à l’étape 2).
2. Accédez à l’instance de Service Bus dans le portail Azure et sélectionnez l’espace de noms que vous souhaitez créer la file d’attente.
3. Sélectionnez **créer une ressource** > **Enterprise Integration** > **Service Bus**, puis entrez les informations requises.
4. Rechercher les informations de connexion de Service Bus en sélectionnant l’espace de noms, puis **informations de connexion**. Vous aurez besoin de ces informations pour la section suivante.

Ensuite, [créer une fonction Azure](../azure-functions/functions-create-first-azure-function.md) pour interroger les journaux de coffre de clés au sein du compte de stockage et récupérer les nouveaux événements. Cette fonction est déclenchée selon une planification.

Pour créer une application de fonction Azure, sélectionnez **créer une ressource**, rechercher la place de marché **Function App**, puis sélectionnez **créer**. Lors de la création, vous pouvez utiliser un plan d’hébergement existant ou en créer un nouveau. Vous pouvez également opter pour un hébergement dynamique. Pour plus d’informations sur les options d’hébergement pour Azure Functions, consultez [mise à l’échelle Azure Functions](../azure-functions/functions-scale.md).

Une fois l’application de fonction Azure est créée, accédez à ce dernier, puis sélectionnez le **minuteur** scénario et **C\#**  pour la langue. Puis sélectionnez **créer cette fonction**.

![Panneau d’accueil d’Azure Functions](./media/keyvault-keyrotation/Azure_Functions_Start.png)

Dans l’onglet **Développement** , remplacez le code run.csx par le code suivant :

```csharp
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.ServiceBus.Messaging;
using System.Text;

public static void Run(TimerInfo myTimer, TextReader inputBlob, TextWriter outputBlob, TraceWriter log)
{
    log.Info("Starting");

    CloudStorageAccount sourceStorageAccount = new CloudStorageAccount(new StorageCredentials("<STORAGE_ACCOUNT_NAME>", "<STORAGE_ACCOUNT_KEY>"), true);

    CloudBlobClient sourceCloudBlobClient = sourceStorageAccount.CreateCloudBlobClient();

    var connectionString = "<SERVICE_BUS_CONNECTION_STRING>";
    var queueName = "<SERVICE_BUS_QUEUE_NAME>";

    var sbClient = QueueClient.CreateFromConnectionString(connectionString, queueName);

    DateTime dtPrev = DateTime.UtcNow;
    if(inputBlob != null)
    {
        var txt = inputBlob.ReadToEnd();

        if(!string.IsNullOrEmpty(txt))
        {
            dtPrev = DateTime.Parse(txt);
            log.Verbose($"SyncPoint: {dtPrev.ToString("O")}");
        }
        else
        {
            dtPrev = DateTime.UtcNow;
            log.Verbose($"Sync point file didnt have a date. Setting to now.");
        }
    }

    var now = DateTime.UtcNow;

    string blobPrefix = "insights-logs-auditevent/resourceId=/SUBSCRIPTIONS/<SUBSCRIPTION_ID>/RESOURCEGROUPS/<RESOURCE_GROUP_NAME>/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/<KEY_VAULT_NAME>/y=" + now.Year +"/m="+now.Month.ToString("D2")+"/d="+ (now.Day).ToString("D2")+"/h="+(now.Hour).ToString("D2")+"/m=00/";

    log.Info($"Scanning:  {blobPrefix}");

    IEnumerable<IListBlobItem> blobs = sourceCloudBlobClient.ListBlobs(blobPrefix, true);

    log.Info($"found {blobs.Count()} blobs");

    foreach(var item in blobs)
    {
        if (item is CloudBlockBlob)
        {
            CloudBlockBlob blockBlob = (CloudBlockBlob)item;

            log.Info($"Syncing: {item.Uri}");

            string sharedAccessUri = GetContainerSasUri(blockBlob);

            CloudBlockBlob sourceBlob = new CloudBlockBlob(new Uri(sharedAccessUri));

            string text;
            using (var memoryStream = new MemoryStream())
            {
                sourceBlob.DownloadToStream(memoryStream);
                text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }

            dynamic dynJson = JsonConvert.DeserializeObject(text);

            //Required to order by time as they might not be in the file
            var results = ((IEnumerable<dynamic>) dynJson.records).OrderBy(p => p.time);

            foreach (var jsonItem in results)
            {
                DateTime dt = Convert.ToDateTime(jsonItem.time);

                if(dt>dtPrev){
                    log.Info($"{jsonItem.ToString()}");

                    var payloadStream = new MemoryStream(Encoding.UTF8.GetBytes(jsonItem.ToString()));
                    //When sending to ServiceBus, use the payloadStream and set keeporiginal to true
                    var message = new BrokeredMessage(payloadStream, true);
                    sbClient.Send(message);
                    dtPrev = dt;
                }
            }
        }
    }
    outputBlob.Write(dtPrev.ToString("o"));
}

static string GetContainerSasUri(CloudBlockBlob blob)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

> [!NOTE]
> Modifier les variables dans le code précédent pour pointer vers votre compte de stockage où sont écrits les journaux de coffre de clés, à l’instance de Service Bus que vous avez créé précédemment et le chemin d’accès spécifiques dans les journaux de stockage de coffre de clés.

La fonction récupère le dernier fichier journal du compte de stockage dans lequel les journaux de coffre de clés sont écrits, extrait les événements les plus récents de ce fichier et les place dans une file d’attente Service Bus. 

Comme un seul fichier peut avoir plusieurs événements, vous devez créer un fichier sync.txt que la fonction examine également pour déterminer l’horodatage du dernier événement récupéré. À l’aide de ce fichier permet de s’assurer que vous n’envoyez le même événement plusieurs fois. 

Le fichier sync.txt contient un horodatage pour l’événement rencontré en dernier. Lorsque les journaux sont chargées, elles doivent être triées en fonction de leur traitement par horodatages pour vous assurer qu’ils sont correctement ordonnés.

Pour cette fonction, nous référencer deux bibliothèques supplémentaires qui ne sont pas disponibles dès le départ dans Azure Functions. Pour inclure ces bibliothèques, nous avons besoin d’Azure Functions à les extraire à l’aide de NuGet. Sous le **Code** boîte, sélectionnez **afficher les fichiers**.

![Option « Afficher les fichiers »](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

Ajoutez un fichier appelé project.json avec le contenu suivant :

```json
    {
      "frameworks": {
        "net46":{
          "dependencies": {
                "WindowsAzure.Storage": "7.0.0",
                "WindowsAzure.ServiceBus":"3.2.2"
          }
        }
       }
    }
```

Une fois que vous sélectionnez **enregistrer**, Azure Functions télécharge les fichiers binaires nécessaires.

Basculez vers l’onglet **Intégration** et donnez un nom explicite au paramètre du minuteur à utiliser dans la fonction. Dans le code précédent, la fonction attend le minuteur est appelé *myTimer*. Spécifiez un [expression CRON](../app-service/webjobs-create.md#CreateScheduledCRON) de la minuterie comme suit : `0 * * * * *`. Cette expression provoque la fonction à exécuter une fois par minute.

Sur le même **intégrer** onglet, ajoutez une entrée du type **stockage Blob Azure**. Cette entrée pointe vers le fichier sync.txt contenant l’horodatage du dernier événement examiné par la fonction. Cette entrée est accessible au sein de la fonction en utilisant le nom de paramètre. Dans le code précédent, l’entrée de stockage d’objets Blob Azure nécessite que le nom de paramètre *inputBlob*. Sélectionnez le compte de stockage où se trouvera le fichier sync.txt (il peut être le même ou un autre compte de stockage). Dans le champ de chemin d’accès, indiquez le chemin d’accès au fichier dans le format `{container-name}/path/to/sync.txt`.

Ajouter une sortie du type **stockage Blob Azure**. Cette sortie pointera vers le fichier sync.txt que vous avez défini dans l’entrée. Cette sortie est utilisée par la fonction pour écrire l’horodatage du dernier événement examiné. Dans le code précédent, le paramètre doit être appelé *outputBlob*.

La fonction est maintenant prête. N’oubliez pas de revenir à l’onglet **Développement** et d’enregistrer le code. Consultez la fenêtre Sortie pour les erreurs de compilation et corrigez-les en fonction des besoins. Si le code se compile, puis le code doit maintenant être consultant les journaux de coffre de clés chaque minute et placer tout nouvel événement dans la file d’attente Service Bus définie. Vous devez voir des informations de journalisation dans la fenêtre du journal à chaque déclenchement de la fonction.

### <a name="azure-logic-app"></a>Application logique Azure

Ensuite, vous devez créer une application logique Azure qui sélectionne les événements que la fonction place dans la file d’attente Service Bus, analyse le contenu et envoie un e-mail selon une condition est remplie.

[Créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md) en sélectionnant **créer une ressource** > **intégration** > **application logique**.

Une fois l’application logique est créée, accédez à ce dernier, puis sélectionnez **modifier**. Dans l’éditeur d’application logique, sélectionnez **file d’attente de Bus de Service** et entrez vos informations d’identification de Service Bus pour vous connecter à la file d’attente.

![Service Bus d’application logique Azure](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Sélectionnez **Ajouter une condition**. Dans la condition, basculez vers l’éditeur avancé et entrez le code suivant. Remplacez *APP_ID* avec l’ID d’application réel de votre application web :

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Cette expression retourne essentiellement **false** si le *appid* à partir de l’événement entrant (qui est le corps du message Service Bus) n’est pas le *appid* de l’application.

Maintenant, créez une action sous **si non, ne rien faire**.

![Azure Logic Apps choisissez action](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Pour l’action, sélectionnez **Office 365 - envoyer un e-mail**. Renseignez les champs pour créer un e-mail à envoyer lorsque la condition définie retourne **false**. Si vous n’avez pas Office 365, recherchez des alternatives obtenir les mêmes résultats.

Vous disposez maintenant d’un pipeline de bout en bout qui recherche les nouveaux journaux d’audit de coffre de clés une fois par minute. Il place les nouveaux journaux qu’il trouve une file d’attente Service Bus. L’application logique est déclenchée lorsqu’un nouveau message arrive dans la file d’attente. Si le *appid* au sein de l’événement ne correspond pas à l’ID d’application de l’application appelante, il envoie un message électronique.
