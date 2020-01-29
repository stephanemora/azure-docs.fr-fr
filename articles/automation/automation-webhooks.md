---
title: Démarrage d’un runbook Azure Automation avec un webhook
description: Webhook qui permet à un client de démarrer un runbook dans Azure Automation à partir d’un appel HTTP.  Cet article décrit comment créer un webhook et l’appeler pour démarrer un runbook.
services: automation
ms.subservice: process-automation
ms.date: 01/16/2020
ms.topic: conceptual
ms.openlocfilehash: f86193e818a91132f9bbca447acadd7e81747522
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76155823"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Démarrage d’un runbook Azure Automation avec un webhook

Un *webhook* vous permet de démarrer un Runbook dans Azure Automation via une simple requête HTTP. Les services externes, tels que Azure DevOps Services, GitHub, les journaux Azure Monitor ou les applications personnalisées, peuvent ainsi démarrer les runbooks sans avoir à implémenter une solution complète à l’aide de l’API Azure Automation.
![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

Vous pouvez comparer les webhooks à d'autres méthodes de démarrage d'un Runbook dans [Démarrage d'un Runbook dans Azure Automation](automation-starting-a-runbook.md)

> [!NOTE]
> L’utilisation d’un webhook pour démarrer un runbook Python n’est pas prise en charge.

## <a name="details-of-a-webhook"></a>Détails d'un webhook

Le tableau suivant décrit les propriétés que vous devez configurer pour un webhook.

| Propriété | Description |
|:--- |:--- |
| Name |Vous pouvez attribuer le nom de votre choix à un webhook, car il n’apparaît pas au client. Il sert seulement à identifier le runbook dans Azure Automation. <br> À titre de meilleure pratique, nommez le webhook d’après le client qui l’utilise. |
| URL |L'URL du webhook est l'adresse unique qu'un client appelle avec une méthode HTTP POST pour démarrer le Runbook lié au webhook. Elle est générée automatiquement lorsque vous créez le webhook. Vous ne pouvez pas spécifier d’URL personnalisée. <br> <br> L'URL contient un jeton de sécurité qui permet que le Runbook soit appelé par un système tiers sans authentification supplémentaire. Pour cette raison, elle doit être traitée comme un mot de passe. Pour des raisons de sécurité, vous pouvez uniquement afficher l’URL dans le portail Azure au moment de la création du webhook. Notez l'URL dans un emplacement sécurisé en vue d'une utilisation ultérieure. |
| Date d'expiration |Comme un certificat, chaque webhook possède une date d'expiration à partir de laquelle il ne peut plus être utilisé. Cette date d’expiration peut être modifiée après la création du webhook tant que celui-ci n’a pas expiré. |
| activé |Un webhook est activé par défaut lorsqu’il est créé. Si vous le définissez sur Disabled, aucun client n’est en mesure de l’utiliser. Vous pouvez définir la propriété **Enabled** lorsque vous créez le webhook ou à tout moment après qu’il a été créé. |

### <a name="parameters"></a>Paramètres

Un webhook peut définir les valeurs des paramètres du Runbook qui sont utilisées lorsque le Runbook est démarré par ce webhook. Le webhook doit inclure les valeurs de tous les paramètres obligatoires du Runbook et peut inclure les valeurs des paramètres optionnels. Une valeur de paramètre configurée pour un Webhook peut être modifiée même après la création du Webhook. Plusieurs webhooks liés à un même Runbook peuvent utiliser différentes valeurs de paramètres.

Lorsqu’un client démarre un runbook à l’aide d’un webhook, il ne peut pas remplacer les valeurs de paramètres définies dans le webhook. Pour recevoir des données du client, le runbook peut accepter un paramètre unique appelé **$WebhookData**. Ce paramètre est de type [object] qui contient les données que le client inclut dans la requête POST.

![Propriétés Webhookdata](media/automation-webhooks/webhook-data-properties.png)

L’objet **$WebhookData** comporte les propriétés suivantes :

| Propriété | Description |
|:--- |:--- |
| WebhookName |Nom du webhook. |
| RequestHeader |Table de hachage contenant les en-têtes de la requête POST entrante. |
| RequestBody |Corps de la requête POST entrante. Ceci conserve les mises en forme telles que les chaînes, JSON, XML ou les données de formulaire codées. Le Runbook doit être écrit pour fonctionner avec le format de données qui est attendu. |

Il n’existe aucune configuration du webhook requise pour prendre en charge le paramètre **$WebhookData** et le runbook n’est pas obligé de l’accepter. Si le runbook ne définit pas le paramètre, tous les détails de la demande envoyée depuis le client sont ignorés.

> [!NOTE]
> Lors de l’appel d’un webhook, vous devez toujours stocker toutes les valeurs de paramètre en cas d’échec de l’appel. En cas de panne réseau ou de problème de connexion, vous ne pourrez pas récupérer les appels de webhook ayant échoué.

Si vous spécifiez une valeur de $WebhookData lors de la création du Webhook, elle est remplacée lorsque le Webhook démarre le runbook avec les données de la requête POST du client, même si celui-ci ne fournit aucune donnée dans le corps de la requête. Si vous démarrez un Runbook ayant $WebhookData à l'aide d'une méthode autre qu'un webhook, vous pouvez fournir une valeur pour $Webhookdata qui est reconnue par le Runbook. Cette valeur doit être un objet avec les mêmes [propriétés](#details-of-a-webhook) que $Webhookdata afin que le Runbook puisse l'utiliser correctement comme s’il s’agissait d’une valeur WebhookData réelle transmise par un webhook.

Par exemple, si vous démarrez le runbook suivant à partir du portail Azure et que vous souhaitez passer certains exemples WebhookData à des fins de test, étant donné que WebhookData est un objet, ils doivent être transmis en tant que valeur JSON dans l'interface utilisateur.

![Paramètre WebhookData à partir de l'interface utilisateur](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Pour le runbook suivant, si vous avez les propriétés suivantes pour le paramètre WebhookData :

* WebhookName: *MyWebhook*
* RequestBody: *[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*

Vous pouvez alors transmettre la valeur JSON suivante dans l'interface utilisateur pour le paramètre WebhookData. L’exemple suivant, avec les retours chariot et les caractères de saut de ligne, correspond au format qui est transmis à partir d’un webhook.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Démarrage du paramètre WebhookData à partir de l'interface utilisateur](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Les valeurs de tous les paramètres d'entrée sont enregistrés avec la tâche du Runbook. Cela signifie qu'une entrée fournie par le client dans la requête webhook sera enregistrée et accessible à toute personne ayant accès à la tâche Automation.  Pour cette raison, soyez prudent lorsque vous incluez des informations sensibles dans les appels du webhook.

## <a name="security"></a>Sécurité

La sécurité d'un webhook s'appuie sur la confidentialité de son URL, laquelle contient un jeton de sécurité lui permettant d'être appelée. Azure Automation n'effectue pas d'authentification de la demande tant qu'elle est adressée à la bonne URL. Pour cette raison, les webhooks ne doivent pas être utilisés pour les Runbooks qui exécutent des fonctions très sensibles sans recourir à un autre moyen de validation de la demande.

Vous pouvez inclure une logique dans le Runbook pour déterminer qu'il a été appelé par un webhook en vérifiant la propriété **WebhookName** du paramètre $WebhookData. Le Runbook peut effectuer une validation supplémentaire en recherchant des informations spécifiques dans les propriétés **RequestHeader** ou **RequestBody**.

Une autre stratégie consiste à ce que le Runbook effectue la validation d'une condition externe lorsqu'il reçoit une demande du webhook. Par exemple, imaginez un runbook appelé par GitHub chaque fois qu’il est procédé à une nouvelle validation d’un référentiel GitHub. Le Runbook peut se connecter à GitHub pour s'assurer qu'une nouvelle validation s’est produite avant de continuer.

## <a name="creating-a-webhook"></a>Création d'un webhook

Utilisez la procédure suivante pour créer un webhook lié à un Runbook dans le portail Azure.

1. Sur la page **Runbooks** du Portail Azure, cliquez sur le runbook que le Webhook démarre pour afficher sa page Détails. Vérifiez que l’**état** du runbook est **Publié**.
2. Cliquez sur **Webhook** en haut de la page pour ouvrir la page **Ajouter un Webhook**.
3. Cliquez sur **Créer un Webhook** pour ouvrir la page **Créer un Webhook**.
4. Spécifiez un **Nom** et une **Date d’expiration** pour le webhook, ainsi que s’il doit être activé. Pour plus d'informations sur ces propriétés, consultez [Détails d'un webhook](#details-of-a-webhook) .
5. Cliquez sur l'icône de copie et appuyez sur Ctrl + C pour copier l'URL du webhook. Puis enregistrez-la dans un endroit sûr. **Une fois que vous avez créé le webhook,vous ne pouvez pas récupérer l’URL à nouveau.**

   ![URL du webhook](media/automation-webhooks/copy-webhook-url.png)

1. Cliquez sur **Paramètres** pour fournir les valeurs des paramètres du Runbook. Si le Runbook possède des paramètres obligatoires, vous ne pouvez pas créer le webhook, sauf si des valeurs sont fournies.
1. Cliquez sur **Créer** pour créer le webhook.

## <a name="using-a-webhook"></a>Utilisation d'un webhook

Pour utiliser un webhook après sa création, votre application cliente doit émettre une requête HTTP POST avec l'URL du webhook. La syntaxe du webhook est au format suivant :

```http
http://<Webhook Server>/token?=<Token Value>
```

Le client reçoit l'un des codes de réponse suivants à la requête POST.

| Code | Texte | Description |
|:--- |:--- |:--- |
| 202 |Acceptée |La requête a été acceptée et le Runbook a été mis en file d'attente avec succès. |
| 400 |Demande incorrecte |La requête a été refusée pour l'une des raisons suivantes : <ul> <li>Le webhook a expiré.</li> <li>Le webhook est désactivé.</li> <li>Le jeton de l’URL n’est pas valide.</li>  </ul> |
| 404 |Introuvable |La requête a été refusée pour l'une des raisons suivantes : <ul> <li>Le webhook est introuvable.</li> <li>Le Runbook est introuvable.</li> <li>Le compte est introuvable.</li>  </ul> |
| 500 |Erreur interne du serveur |L'URL est valide, mais une erreur s'est produite. Soumettez à nouveau la demande. |

En supposant que la requête aboutisse, la réponse webhook contient l’ID de travail au format JSON comme suit. Elle contient un ID de travail unique, mais le format JSON permet des améliorations ultérieures potentielles.

```json
{"JobIds":["<JobId>"]}
```

Le client ne peut pas déterminer l’issue du travail du runbook ou de son état d’achèvement à partir du webhook. Il peut déterminer ces informations à l’aide de l’ID de travail avec une autre méthode telle que [Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjob) ou l’[API Azure Automation](/rest/api/automation/job).

## <a name="renew-webhook"></a>Renouveler un webhook

Quand un webhook est créé, il a une durée de validité de 10 ans. Au terme de cette période, le webhook expire automatiquement. Une fois qu’un webhook est arrivé à expiration, il ne peut pas être réactivé. Il doit être supprimé et recréé. Si un webhook n’a pas atteint sa date d’expiration, il peut faire l’objet d’une extension.

Pour étendre un webhook, accédez au runbook qui le contient. Sélectionnez **Webhooks** sous **Ressources**. Cliquez sur le webhook que vous voulez étendre. La page **Webhook** s’ouvre alors.  Choisissez une nouvelle date et heure d’expiration, puis cliquez sur **Enregistrer**.

## <a name="sample-runbook"></a>Exemple de runbook

L’exemple de runbook suivant accepte les données du webhook et démarre les machines virtuelles spécifiées dans le corps de la demande. Pour tester ce runbook, dans votre compte Automation sous **Runbooks**, cliquez sur **+ Ajouter un runbook**. Si vous ne savez pas comment créer un runbook, consultez [Création d’un runbook](automation-quickstart-create-runbook.md).

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData
)



# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

    # Check header for message to validate request
    if ($WebhookData.RequestHeader.message -eq 'StartedbyContoso')
    {
        Write-Output "Header has required information"}
    else
    {
        Write-Output "Header missing required information";
        exit;
    }

    # Retrieve VM's from Webhook request body
    $vms = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.

    Write-Output "Authenticating to Azure with service principal and certificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    Write-Output "Get connection asset: $ConnectionAssetName"

    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null)
            {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Write-Output "Authenticating to Azure with service principal."
            Add-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Output

        # Start each virtual machine
        foreach ($vm in $vms)
        {
            $vmName = $vm.Name
            Write-Output "Starting $vmName"
            Start-AzureRMVM -Name $vm.Name -ResourceGroup $vm.ResourceGroup
        }
}
else {
    # Error
    write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="test-the-example"></a>Tester l’exemple

L'exemple suivant utilise Windows PowerShell pour démarrer un Runbook avec un webhook. N'importe quel langage qui peut effectuer une requête HTTP peut utiliser un webhook ; Windows PowerShell est utilisé ici à titre d'exemple.

Le Runbook s'attend à une liste de machines virtuelles au format JSON dans le corps de la requête. Le runbook vérifie également que les en-têtes contiennent un message défini pour valider que l’appelant webhook est valide.

```azurepowershell-interactive
$uri = "<webHook Uri>"

$vms  = @(
            @{ Name="vm01";ResourceGroup="vm01"},
            @{ Name="vm02";ResourceGroup="vm02"}
        )
$body = ConvertTo-Json -InputObject $vms
$header = @{ message="StartedbyContoso"}
$response = Invoke-WebRequest -Method Post -Uri $uri -Body $body -Headers $header
$jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
```

L’exemple suivant montre le corps de la requête qui est disponible pour le Runbook dans la propriété **RequestBody** de **WebhookData**. Cette valeur est au format JSON car il s’agit du format qui a été inclus dans le corps de la requête.

```json
[
    {
        "Name":  "vm01",
        "ResourceGroup":  "myResourceGroup"
    },
    {
        "Name":  "vm02",
        "ResourceGroup":  "myResourceGroup"
    }
]
```

L'illustration suivante montre la requête envoyée à partir de Windows PowerShell et sa réponse. L’ID de travail est extrait de la réponse et converti en une chaîne.

![Bouton Webhooks](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>Étapes suivantes

* Pour découvrir comment utiliser Azure Automation pour gérer les alertes Azure, consultez l’article [Utiliser une alerte pour déclencher un runbook Azure Automation](automation-create-alert-triggered-runbook.md).

