---
title: Configuration d’Azure Key Vault avec une rotation des clés et un audit de bout en bout | Microsoft Docs
description: Utilisez cette procédure pour configurer la rotation des clés et la surveillance des journaux de coffre de clés.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: ''
ms.service: key-vault
ms.subservice: secrets
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: d2981495a256ce5fb8f8f3584e68ac91541f9d62
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427145"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>Configurer Azure Key Vault avec une rotation des clés et un audit

## <a name="introduction"></a>Introduction

Une fois que vous avez un coffre de clés, vous pouvez commencer à l’utiliser pour stocker les clés et les secrets. Vos applications ne doivent plus nécessairement conserver vos clés et secrets, mais peuvent les demander au coffre en cas de besoin. Un coffre de clés vous permet de mettre à jour les clés et les secrets sans affecter le comportement de votre application, et de disposer ainsi de toute une multitude de possibilités de gestion des clés et secrets.

Cet article explique comment implémenter une rotation planifiée des clés de compte de stockage, superviser les journaux d’audit de coffre de clés et déclencher des alertes quand des demandes inattendues sont effectuées. 

Vous devez d’abord créer un coffre de clés à l’aide de la méthode de votre choix :

- [Définir et récupérer un secret depuis Azure Key Vault à l’aide d’Azure CLI](quick-create-cli.md)
- [Définir et récupérer un secret depuis Azure Key Vault à l’aide d’Azure PowerShell](quick-create-powershell.md)
- [Définir et récupérer un secret depuis Azure Key Vault à l’aide du portail Azure](quick-create-portal.md)


## <a name="store-a-secret"></a>Stocker un secret

Pour qu’une application puisse récupérer un secret dans Key Vault, vous devez tout d’abord créer le secret et le télécharger dans votre coffre.

Démarrez une session Azure PowerShell et connectez-vous à votre compte Azure avec la commande suivante :

```powershell
Connect-AzAccount
```

Dans la fenêtre contextuelle de votre navigateur, entrez le nom d’utilisateur et le mot de passe associés à votre compte Azure. PowerShell obtient alors tous les abonnements associés à ce compte. PowerShell utilise le premier par défaut.

Si vous disposez de plusieurs abonnements, vous devrez peut-être spécifier celui qui a été utilisé pour créer votre coffre de clés. Entrez la commande suivante pour afficher les abonnements de votre compte :

```powershell
Get-AzSubscription
```

Pour spécifier l’abonnement associé au coffre de clés que vous allez journaliser, entrez la commande suivante :

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

Cet article présentant le stockage d’une clé de compte de stockage sous la forme d’un secret, vous devez obtenir cette clé de compte de stockage.

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Une fois votre secret récupéré (votre clé de compte de stockage dans notre exemple), vous devez convertir cette clé en une chaîne sécurisée, puis créer un secret avec cette valeur dans votre coffre de clés.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

Ensuite, obtenez l’URI pour le secret que vous avez créé. Vous aurez besoin de cet URI à une étape ultérieure pour appeler le coffre de clés et récupérer votre secret. Exécutez la commande PowerShell suivante et notez la valeur de l’ID, qui correspond à l’URI du secret :

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Configurer d’application

Maintenant que vous disposez d’un secret stocké, vous pouvez utiliser le code pour le récupérer et l’utiliser après avoir effectué quelques étapes.

Tout d’abord, vous devez inscrire votre application auprès d’Azure Active Directory. Ensuite, indiquez à Key Vault les informations de votre application afin qu’il autorise les requêtes émises par votre application.

> [!NOTE]
> Votre application doit être créée sur le même client Azure Active Directory que votre coffre de clés.

1. Ouvrez **Azure Active Directory**.
2. Sélectionnez **Inscriptions d’applications**. 
3. Sélectionnez **Nouvelle inscription d’application** pour ajouter une application à Azure Active Directory.

    ![Ouvrir des applications dans Azure Active Directory](../media/keyvault-keyrotation/azure-ad-application.png)

4. Sous **Créer**, conservez le type d’application **Web app/API** et donnez un nom à votre application. Attribuez à votre application une **URL de connexion**. Vous pouvez utiliser n’importe quelle URL dans le cadre de cette démonstration.

    ![Créer une inscription d’application](../media/keyvault-keyrotation/create-app.png)

5. Une fois l’application ajoutée à Azure Active Directory, la page de l’application s’ouvre. Sélectionnez **Paramètres**, puis **Propriétés**. Copiez la valeur figurant dans **ID de l’application**. Vous en aurez besoin plus tard.

Ensuite, générez une clé pour votre application afin qu’elle puisse interagir avec Azure Active Directory. Pour créer une clé, sélectionnez **Clés** sous **Paramètres**. Notez la clé générée pour votre application Azure Active Directory. Vous en aurez besoin dans une étape ultérieure. La clé ne sera plus disponible lorsque vous aurez quitté cette section. 

![Clés d’application Azure Active Directory](../media/keyvault-keyrotation/create-key.png)

Avant d’appeler votre coffre de clés depuis votre application, vous devez fournir au coffre de clés des informations sur votre application et ses autorisations. La commande suivante utilise le nom du coffre et l’ID de votre application Azure Active Directory pour accorder à l’application un accès **Get** à votre coffre de clés.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Vous êtes maintenant prêt à créer vos appels d’application. Dans votre application, vous devez installer les packages NuGet nécessaires pour interagir avec Azure Key Vault et Azure Active Directory. Entrez les commandes suivantes dans la console du gestionnaire de package Visual Studio. Au moment de la rédaction de cet article, la version du package Azure Active Directory est la version 3.10.305231913. Vérifiez donc que vous disposez de la dernière version et mettez-la à jour si nécessaire.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

Dans votre code d’application, créez une classe qui contiendra la méthode d’authentification de votre Azure Active Directory. Dans cet exemple, la classe est appelée **Utils**. Ajoutez l'instruction `using` suivante :

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Ajoutez ensuite la méthode suivante pour récupérer le jeton JWT d’Azure Active Directory. Pour faciliter la maintenance, vous souhaiterez peut-être déplacer les valeurs de chaîne codées en dur dans votre configuration web ou d’application.

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

Ajoutez le code nécessaire pour appeler Key Vault et récupérer votre valeur secrète. Vous devez tout d’abord ajouter l’instruction `using` suivante :

```csharp
using Microsoft.Azure.KeyVault;
```

Ajoutez les appels de méthode pour appeler Key Vault et récupérer votre secret. Dans cette méthode, vous fournissez l’URI de secret que vous avez enregistré dans une étape précédente. Notez l’utilisation de la méthode **GetToken** à partir de la classe **Utils** créée précédemment.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Lorsque vous exécutez votre application, vous devez désormais vous authentifier auprès d’Azure Active Directory et récupérer votre valeur secrète dans Azure Key Vault.

## <a name="key-rotation-using-azure-automation"></a>Rotation des clés à l’aide d’Azure Automation

> [!IMPORTANT]
> Les runbooks Azure Automation nécessitent encore l’utilisation du module `AzureRM`.

Vous êtes maintenant prêt à configurer une stratégie de rotation pour les valeurs stockées en tant que secrets Key Vault. La rotation des secrets peut s’effectuer de plusieurs façons :

- Par un processus manuel
- Par programmation en utilisant des appels d’API
- Via un script Azure Automation

Dans cet article, vous utiliserez Azure PowerShell et Azure Automation pour modifier la clé d’accès d’un compte de stockage Azure. Ensuite, vous mettrez à jour un secret de coffre de clés avec cette nouvelle clé.

Pour qu’Azure Automation puisse définir des valeurs secrètes dans votre coffre de clés, vous devez obtenir l’ID client pour la connexion nommée **AzureRunAsConnection**. Cette connexion a été créée pendant la définition de votre instance Azure Automation. Vous pouvez obtenir cet ID en sélectionnant **Ressources** dans votre instance Azure Automation. Sélectionnez ensuite **Connexions**, puis le principal du service **AzureRunAsConnection**. Notez l’**ID d’application**.

![ID client Azure Automation](../media/keyvault-keyrotation/Azure_Automation_ClientID.png)

Dans **Ressources**, sélectionnez **Modules**. Sélectionnez **Galerie**, puis recherchez et importez les versions à jour de chacun des modules suivants :

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> Au moment de la rédaction de cet article, seuls les modules mentionnés précédemment devaient être mis à jour pour le script suivant. Si le travail d’automatisation échoue, vérifiez que tous les modules nécessaires et leurs dépendances ont été importés.

Une fois que vous avez récupéré l’ID d’application pour votre connexion Azure Automation, vous devez indiquer à votre coffre de clés que cette application est autorisée à mettre à jour les secrets de votre coffre. Utilisez la commande PowerShell suivante :

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Sélectionnez ensuite **Runbooks** sous votre instance Azure Automation, puis **Ajouter un runbook**. Sélectionnez **Création rapide**. Donnez un nom à votre runbook et sélectionnez **PowerShell** comme type de runbook. Vous pouvez ajouter une description. Pour finir, sélectionnez **Créer**.

![Créer un runbook](../media/keyvault-keyrotation/Create_Runbook.png)

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

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

Dans le volet de l’éditeur, sélectionnez le volet **Test** pour tester votre script. Une fois le script exécuté sans erreur, vous pouvez sélectionner **Publier**, puis appliquer une planification au runbook dans le volet de configuration du runbook.

## <a name="key-vault-auditing-pipeline"></a>Pipeline d’audit de Key Vault

Lorsque vous configurez un coffre de clés, vous pouvez activer la fonction d’audit afin de collecter des journaux d’activité de demandes d’accès au coffre de clés. Ces journaux sont stockés dans un compte de stockage Azure spécifié et peuvent être récupérés, contrôlés et analysés. Le scénario suivant utilise des fonctions Azure, des applications logiques Azure et des journaux d’audit du coffre de clés afin de créer un pipeline pour envoyer un e-mail lorsqu’une application qui ne correspond pas à l’ID de l’application web récupère des secrets dans le coffre.

Vous devez tout d’abord activer la journalisation sur votre coffre de clés. Utilisez les commandes PowerShell suivantes. (Vous pouvez afficher les détails complets dans [cet article sur la journalisation de coffre de clé](../general/logging.md).)

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

Une fois la journalisation activée, les journaux d’audit sont placés dans le compte de stockage spécifié. Ces journaux d’activité contiennent des événements indiquant la méthode et la date/l’heure d’accès à vos coffres de clés, et qui y a accédé.

> [!NOTE]
> Vous pouvez accéder aux informations de journalisation 10 minutes après l’opération sur le coffre de clés. Elles sont souvent disponibles en moins de temps.

L’étape suivante consiste à [créer une file d’attente Azure Service Bus](../../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). C’est dans cette file d’attente que les journaux d’audit de coffre de clés sont envoyés. Lorsque les messages du journal d’audit se trouvent dans la file d’attente, l’application logique les récupère et agit en conséquence. Procédez comme suit pour créer une instance Service Bus :

1. Créez un espace de noms Service Bus (si vous en avez déjà un et souhaitez l’utiliser, passez à l’étape 2).
2. Recherchez l’instance Service Bus dans le portail Azure, puis sélectionnez l’espace de noms dans lequel vous souhaitez créer la file d’attente.
3. Sélectionnez **Créer une ressource** > **Intégration Entreprise** > **Service Bus**, puis entrez les informations requises.
4. Trouvez les informations de connexion Service Bus en sélectionnant l’espace de noms et en cliquant sur **Informations de connexion**. Vous aurez besoin de ces informations dans la section suivante.

Ensuite, [créez une fonction Azure](../../azure-functions/functions-create-first-azure-function.md) pour interroger les journaux de coffre de clés dans le compte de stockage et récupérer les nouveaux événements. Cette fonction sera déclenchée selon une planification.

Pour créer une application de fonction Azure, sélectionnez **Créer une ressource**, recherchez **Function App** sur la Place de Marché, puis cliquez sur **Créer**. Lors de la création, vous pouvez utiliser un plan d’hébergement existant ou en créer un nouveau. Vous pouvez également opter pour un hébergement dynamique. Pour plus d’informations sur les options d’hébergement pour Azure Functions, voir [Mise à l’échelle d’Azure Functions](../../azure-functions/functions-scale.md).

Une fois l’application de fonction Azure est créée, accédez-y, puis sélectionnez le scénario **Timer** et **C\#** pour le langage. Sélectionnez ensuite **Créer cette fonction**.

![Panneau d’accueil d’Azure Functions](../media/keyvault-keyrotation/Azure_Functions_Start.png)

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
            log.Verbose($"Sync point file didn't have a date. Setting to now.");
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
> Modifiez les variables du code précédent de façon à ce qu’elles pointent vers votre compte de stockage où les journaux de coffre de clés sont écrits, vers l’instance Service Bus que vous avez créée précédemment et vers le chemin d’accès spécifique des journaux de stockage du coffre de clés.

La fonction récupère le dernier fichier journal du compte de stockage dans lequel les journaux d’activité de coffre de clés sont écrits, extrait les événements les plus récents de ce fichier et les place dans une file d’attente Service Bus. 

Étant donné qu’un même fichier peut comporter plusieurs événements, vous devez créer un fichier sync.txt que la fonction examine également pour déterminer l’horodatage du dernier événement récupéré. Vous vous assurez ainsi que le même événement n’est pas envoyé plusieurs fois. 

Le fichier sync.txt contient l’horodatage du dernier événement rencontré. Une fois les journaux chargés, ils doivent être triés en fonction de leur horodatage pour que l’ordre soit correct.

Pour cette fonction, nous faisons référence à deux bibliothèques supplémentaires qui ne sont pas disponibles par défaut dans Azure Functions. Pour inclure ces bibliothèques, Azure Functions doit les récupérer à l’aide de NuGet. Dans la zone **Code**, sélectionnez **Afficher les fichiers**.

![Option « Afficher les fichiers »](../media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

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

Lorsque vous cliquez sur **Enregistrer**, Azure Functions télécharge les fichiers binaires nécessaires.

Basculez vers l’onglet **Intégration** et donnez un nom explicite au paramètre du minuteur à utiliser dans la fonction. Dans le code précédent, la fonction attend le minuteur appelé *myTimer*. Spécifiez une [expression CRON](../../app-service/webjobs-create.md#CreateScheduledCRON) pour le minuteur, comme suit : `0 * * * * *`. Cette expression activera l’exécution de la fonction une fois par minute.

Dans ce même onglet **Intégration**, ajoutez une entrée de type **Stockage Blob Azure**. Cette entrée pointera vers le fichier sync.txt contenant l’horodatage du dernier événement examiné par la fonction. Cette entrée est accessible au sein de la fonction en utilisant le nom de paramètre. Dans le code précédent, l’entrée de Stockage Blob Azure attend le nom de paramètre *inputBlob*. Sélectionnez le compte de stockage dans lequel se trouvera le fichier sync.txt (il peut s’agir du même compte de stockage ou d’un autre). Dans le champ de chemin d’accès, indiquez le chemin du fichier au format `{container-name}/path/to/sync.txt`.

Ajoutez une sortie de type **Stockage Blob Azure**. Cette sortie pointe vers le fichier sync.txt que vous avez défini dans l’entrée. Cette sortie est utilisée par la fonction pour écrire l’horodatage du dernier événement examiné. Dans le code précédent, le paramètre doit être appelé *outputBlob*.

La fonction est prête. N’oubliez pas de revenir à l’onglet **Développement** et d’enregistrer le code. Dans la fenêtre de sortie, recherchez d’éventuelles erreurs de compilation et corrigez-les si nécessaire. S’il est compilé, le code doit maintenant vérifier les journaux du coffre de clés chaque minute et placer tout nouvel événement dans la file d’attente Service Bus définie. Vous devez voir des informations de journalisation dans la fenêtre du journal à chaque déclenchement de la fonction.

### <a name="azure-logic-app"></a>Application logique Azure

Vous devez ensuite créer une application logique Azure qui récupère les événements que la fonction place dans la file d’attente Service Bus, analyse le contenu et envoie un e-mail lorsqu’une condition est remplie.

[Créez une application logique](../../logic-apps/quickstart-create-first-logic-app-workflow.md) en sélectionnant **Créer une ressource** > **Intégration** > **Application logique**.

Une fois l’application logique créée, accédez-y, puis sélectionnez **Modifier**. Dans l’éditeur d’application logique, sélectionnez la **file d’attente Service Bus** et entrez vos informations d’identification Service Bus pour la connecter à la file d’attente.

![Service Bus d’application logique Azure](../media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Sélectionnez **Ajouter une condition**. Dans la condition, basculez vers l’éditeur avancé et entrez le code suivant. Remplacez *APP_ID* par l’ID d’application réel de votre application web :

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Cette expression renvoie essentiellement la valeur **false** si le paramètre *appid* de l’événement entrant (à savoir le corps du message Service Bus) ne correspond pas au paramètre *appid* de l’application.

Créez maintenant une action sous **SI NON, NE RIEN FAIRE**.

![Choisir une action Azure Logic Apps](../media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Pour l’action, sélectionnez **Office 365 - Envoyer un message électronique**. Renseignez les champs pour créer un e-mail à envoyer lorsque la condition définie retourne **false**. Si vous n’avez pas Office 365, recherchez des alternatives pour parvenir aux mêmes résultats.

Vous disposez maintenant d’un pipeline de bout en bout qui recherche les nouveaux journaux d’audit de coffre de clés une fois par minute. Il place les nouveaux journaux qu’il trouve dans une file d’attente Service Bus. L’application logique est déclenchée lorsqu’un nouveau message arrive dans la file d’attente. Si le paramètre *appid* de l’événement ne correspond pas à l’ID de l’application appelante, il envoie un e-mail.
