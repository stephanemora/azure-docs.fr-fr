---
title: Paramètres d’entrée de Runbook
description: Les paramètres d’entrée de Runbook augmentent la flexibilité des Runbooks en vous permettant de transmettre des données à un Runbook lors de son démarrage. Cet article décrit différents cas où des paramètres d’entrée sont utilisés dans des Runbooks.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 02/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: be7d244f5aa422b2083d35fc56a52318a4379b79
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850225"
---
# <a name="runbook-input-parameters"></a>Paramètres d’entrée de Runbook

Les paramètres d'entrée de Runbook augmentent la flexibilité d'un Runbook en vous permettant de lui transmettre des données lors de son démarrage. Les paramètres permettent que les actions du Runbook soient ciblées pour des scénarios et environnements spécifiques. Cet article explore différents scénarios dans lesquels des paramètres d’entrée sont utilisés dans des runbooks.

## <a name="configure-input-parameters"></a>Configurer les paramètres d’entrée

Des paramètres d’entrée peuvent être configurés dans des Runbooks PowerShell, PowerShell Workflow, Python et graphiques. Un Runbook peut avoir plusieurs paramètres avec différents types de données, ou aucun paramètre. Des paramètres d'entrée peuvent être obligatoires ou facultatifs, et vous pouvez disposer d'une valeur par défaut pour les paramètres facultatifs. Vous attribuez des valeurs aux paramètres d'entrée d'un Runbook lorsque vous le démarrez à l'aide d'une des méthodes disponibles. Ces méthodes incluent le démarrage d'un Runbook à partir du portail Azure, d'un service web ou de PowerShell. Vous pouvez également démarrer un Runbook en tant que Runbook enfant appelé en ligne dans un autre Runbook.

## <a name="configure-input-parameters-in-powershell-runbooks"></a>Configurer des paramètres d'entrée dans des Runbooks PowerShell

Les Runbooks PowerShell et PowerShell Workflow d'Azure Automation prennent en charge les paramètres d'entrée définis à l'aide des attributs suivants :  

| **Propriété** | **Description** |
|:--- |:--- |
| `Type` |Requis. Type de données attendu pour la valeur du paramètre. Tout type .NET est valide. |
| `Name` |Requis. Le nom du paramètre. Il doit être unique dans un Runbook, ne peut contenir que des lettres, des chiffres ou des caractères de soulignement, et doit commencer par une lettre. |
| `Mandatory` |facultatif. Spécifie si une valeur doit être fournie pour le paramètre. Si vous définissez ce paramètre sur **\$true**, une valeur doit être fournie au démarrage du Runbook. Si vous définissez ce paramètre sur **\$false**, la valeur est facultative. |
| `Default value` |facultatif. Spécifie une valeur à utiliser pour le paramètre si aucune valeur n’est transmise lors du démarrage du runbook. Une valeur par défaut peut être définie pour tout paramètre, qui rend automatiquement le paramètre facultatif, indépendamment du paramètre Mandatory. |

Windows PowerShell prend en charge d’autres attributs de paramètres d’entrée que ceux répertoriés ici, tels que la validation, les alias et les jeux de paramètres. Toutefois, Azure Automation ne prend actuellement en charge que les paramètres d’entrée ci-dessus.

Une définition de paramètre dans des Runbooks PowerShell Workflow a la forme générale suivante, où plusieurs paramètres sont séparés par des virgules.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

> [!NOTE]
> Lorsque vous définissez des paramètres, si vous ne spécifiez pas l’attribut **Mandatory** , par défaut, le paramètre est considéré comme facultatif. De même, si l’un des paramètres des runbooks PowerShell Workflow possède la valeur par défaut, il est traité par PowerShell comme un paramètre facultatif, indépendamment de la valeur de l’attribut **Mandatory**.

En guise d’exemple, nous allons configurer les paramètres d’entrée pour un Runbook PowerShell Workflow qui renvoie des détails sur des machines virtuelles, soit une machine virtuelle unique, soit toutes les machines virtuelles au sein d’un groupe de ressources. Ce Runbook comporte deux paramètres, comme illustré dans la capture d’écran ci-dessous : le nom de la machine virtuelle et le nom du groupe de ressources.

![PowerShell Workflow d’Automation](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

Dans cette définition de paramètre, les paramètres **\$VMName** et **\$resourceGroupName** sont des paramètres simples de type chaîne. Toutefois, les Runbooks PowerShell et PowerShell Workflow prennent en charge tous les types, simples et complexes, tels que **object** ou **PSCredential** pour les paramètres d’entrée.

Si votre runbook comporte un paramètre d’entrée de type d’objet, utilisez une table de hachage PowerShell contenant des paires (nom, valeur) pour transmettre une valeur. Par exemple, si vous avez le paramètre suivant dans un Runbook :

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

Vous pouvez transmettre la valeur suivante au paramètre :

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Quand vous ne passez aucune valeur à un paramètre de type `[String]` facultatif qui a une _valeur par défaut_ de `\$null`, la valeur du paramètre est une _chaîne vide_, **et non** `\$null`.

## <a name="configure-input-parameters-in-graphical-runbooks"></a>Configurer des paramètres d’entrée dans des Runbooks graphiques

Pour configurer un Runbook graphique avec des paramètres d’entrée, nous allons créer un [Runbook graphique](automation-first-runbook-graphical.md) qui renvoie des détails sur des machines virtuelles, soit une machine virtuelle unique, soit toutes les machines virtuelles au sein d’un groupe de ressources. La configuration d'un Runbook implique deux activités principales, comme décrit ci-dessous.

[**Authentifier des Runbooks avec un compte d’identification Azure**](automation-sec-configure-azure-runas-account.md) pour s’authentifier avec Azure.

[**Get-AzureRmVm**](/powershell/module/azurerm.compute/get-azurermvm) pour récupérer les propriétés d’une machine virtuelle.

Vous pouvez utiliser l’activité [**Write-Output**](/powershell/module/microsoft.powershell.utility/write-output) pour renvoyer les noms de machines virtuelles. L’activité **Get-AzureRmVm** accepte deux paramètres : le **nom de machine virtuelle** et le **nom du groupe de ressources**. Étant donné que ces paramètres peuvent nécessiter des valeurs différentes chaque fois que vous démarrez le Runbook, vous pouvez ajouter des paramètres d’entrée à votre Runbook. Voici les étapes pour ajouter des paramètres d’entrée :

1. Sélectionnez le Runbook graphique dans le panneau **Runbooks** , puis cliquez sur [**Modifier**](automation-graphical-authoring-intro.md).
2. Dans l’éditeur de Runbooks, cliquez sur **Entrée et sortie** pour ouvrir le panneau **Entrée et sortie**.

   ![Runbook graphique d’Automation](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. Le panneau **Entrée et sortie** affiche la liste des paramètres d’entrée qui sont définis pour le Runbook. Dans ce panneau, vous pouvez ajouter un nouveau paramètre d’entrée ou modifier la configuration d’un paramètre d’entrée existant. Pour ajouter un nouveau paramètre pour le Runbook, cliquez sur **Ajouter une entrée** pour ouvrir le panneau **Paramètre d’entrée de Runbook**. Dans ce panneau, vous pouvez configurer les paramètres suivants :

   | **Propriété** | **Description** |
   |:--- |:--- |
   | `Name` |Requis. Le nom du paramètre. Il doit être unique dans un Runbook, ne peut contenir que des lettres, des chiffres ou des caractères de soulignement, et doit commencer par une lettre. |
   | `Description` |facultatif. Description de l’objectif du paramètre d’entrée. |
   | `Type` |facultatif. Type de données attendu pour la valeur du paramètre. Les types de paramètres pris en charge sont **String**, **Int32**, **Int64**, **Decimal**, **Boolean**, **DateTime** et **Object**. Si un type de données n’est pas sélectionné, le type par défaut est **String**. |
   | `Mandatory` |facultatif. Spécifie si une valeur doit être fournie pour le paramètre. Si vous choisissez **yes**, une valeur doit être fournie lors du démarrage du Runbook. Si vous choisissez **no**, aucune valeur n’est requise lors du démarrage du Runbook, et une valeur par défaut peut être définie. |
   | `Default Value` |facultatif. Spécifie une valeur à utiliser pour le paramètre si aucune valeur n’est transmise lors du démarrage du runbook. Une valeur par défaut peut être définie pour un paramètre qui n’est pas obligatoire. Pour définir une valeur par défaut, choisissez **Custom**. Cette valeur est utilisée, sauf si une autre valeur est fournie lorsque du démarrage du Runbook. Choisissez **None** si vous ne souhaitez pas fournir de valeur par défaut. |

    ![Ajouter une nouvelle entrée](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Créez deux paramètres avec les propriétés suivantes ; celles-ci seront utilisées par l'activité **Get-AzureRmVm** :

   * **Paramètre 1 :**
     * Nom - VMName
     * Type : Chaîne
     * Obligatoire : Non
   * **Paramètre 2 :**
     * Nom : resourceGroupName
     * Type : Chaîne
     * Obligatoire : Non
     * Valeur par défaut : Personnalisé
     * Valeur par défaut personnalisée : \<Nom du groupe de ressources qui contient les machines virtuelles\>

5. Une fois les paramètres ajoutés, cliquez sur **OK**. Vous pouvez maintenant les voir sur la **page Entrée et sortie**. Cliquez de nouveau sur **OK**, puis sur **Enregistrer** et **Publier** pour publier votre Runbook.

## <a name="configure-input-parameters-in-python-runbooks"></a>Configurer des paramètres d’entrée dans des Runbooks Python

Contrairement aux runbooks PowerShell, PowerShell Workflow et graphiques, les runbooks Python ne prennent pas de paramètres nommés.
Tous les paramètres d’entrée sont analysés en tant que tableau de valeurs d’argument.
Vous accédez au tableau en important le module `sys` dans votre script Python, puis en utilisant le tableau `sys.argv`.
Il est important de noter que le premier élément du tableau, `sys.argv[0]`, est le nom du script, par conséquent, le premier paramètre d’entrée réel est `sys.argv[1]`.

Pour obtenir un exemple montrant comment utiliser les paramètres d’entrée dans un runbook Python, consultez [My first Python runbook in Azure Automation](automation-first-runbook-textual-python2.md) (Mon premier runbook Python dans Azure Automation).

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Affecter des valeurs aux paramètres d’entrée dans des Runbooks

Vous pouvez transmettre des valeurs aux paramètres d’entrée de runbooks dans les cas suivants :

### <a name="start-a-runbook-and-assign-parameters"></a>Démarrer un Runbook et affecter des paramètres

Un Runbook peut être démarré de plusieurs façons : via le portail Azure, avec un WebHook, avec des applets de commande PowerShell, avec l’API REST ou avec le Kit de développement logiciel (SDK). Nous abordons ci-dessous différentes méthodes pour démarrer un Runbook et affecter des paramètres.

#### <a name="start-a-published-runbook-by-using-the-azure-portal-and-assign-parameters"></a>Démarrer un Runbook publié à l’aide du portail Azure et affecter des paramètres

Au [démarrage du runbook](start-runbooks.md#start-a-runbook-with-the-azure-portal), le panneau **Démarrer le runbook** s’ouvre. Vous pouvez y entrer les valeurs des paramètres que avez créés.

![Démarrer à l’aide du portail](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

Dans l'étiquette située sous la zone d'entrée, vous pouvez voir les attributs qui ont été définis pour le paramètre. Les attributs incluent Obligatoire ou Facultatif, Type et Valeur par défaut. Dans la bulle d’aide en regard du nom de paramètre, vous pouvez voir toutes les informations clés dont vous avez besoin pour prendre des décisions sur les valeurs d’entrée du paramètre. Ces informations indiquent si un paramètre est obligatoire ou facultatif. Elles précisent également le type et la valeur par défaut (le cas échéant) et d’autres notes utiles.

> [!NOTE]
> Les paramètres de type String prennent en charge les valeurs de chaîne **vides** .  La valeur **[EmptyString]** dans la zone des paramètres d’entrée a pour effet de transmettre une chaîne vide au paramètre. De même, les paramètres de type String ne prennent pas en charge la transmission de valeurs **Null** . Si vous ne transmettez pas de valeur au paramètre de type String, PowerShell interprète sa valeur comme Null.

#### <a name="start-a-published-runbook-by-using-powershell-cmdlets-and-assign-parameters"></a>Démarrer un Runbook publié à l’aide d’applets de commande PowerShell et affecter des paramètres

* **Applets de commande Azure Resource Manager :** vous pouvez démarrer un runbook Automation créé dans un groupe de ressources en utilisant [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook).
  
  **Exemple :**

  ```powershell
  $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
  Start-AzureRmAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
  ```

* **Applets de commande du modèle de déploiement Azure Classic :** vous pouvez démarrer un runbook Automation créé dans un groupe de ressources par défaut en utilisant [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure/start-azureautomationrunbook).
  
  **Exemple :**

  ```powershell
  $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
  Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
  ```

> [!NOTE]
> Lorsque vous démarrez un Runbook à l’aide d’applets de commande PowerShell, un paramètre par défaut, **MicrosoftApplicationManagementStartedBy**, est créé avec la valeur **PowerShell**. Vous pouvez voir ce paramètre sur la page **Détails du travail** .  

#### <a name="start-a-runbook-by-using-an-sdk-and-assign-parameters"></a>Démarrer un Runbook à l’aide d’un Kit de développement logiciel (SDK) et affecter des paramètres

* **Méthode d’Azure Resource Manager :** vous pouvez démarrer un runbook en utilisant le kit SDK d’un langage de programmation. Voici un extrait de code C# permettant de démarrer un Runbook dans votre compte Automation. Vous pouvez voir le code complet dans notre [dépôt GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).  

  ```csharp
   public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
      {
        var response = AutomationClient.Jobs.Create(resourceGroupName, automationAccount, new JobCreateParameters
         {
            Properties = new JobCreateProperties
             {
                Runbook = new RunbookAssociationProperty
                 {
                   Name = runbookName
                 },
                   Parameters = parameters
             }
         });
      return response.Job;
      }
  ```

* **Méthode du modèle de déploiement Azure Classic :** vous pouvez démarrer un runbook en utilisant le kit SDK d’un langage de programmation. Voici un extrait de code C# permettant de démarrer un Runbook dans votre compte Automation. Vous pouvez voir le code complet dans notre [dépôt GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).

  ```csharp
  public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
    {
      var response = AutomationClient.Jobs.Create(automationAccount, new JobCreateParameters
    {
      Properties = new JobCreateProperties
         {
           Runbook = new RunbookAssociationProperty
         {
           Name = runbookName
              },
                Parameters = parameters
              }
       });
      return response.Job;
    }
  ```

  Pour lancer cette méthode, créez un dictionnaire pour stocker les paramètres de Runbook **VMName** et **resourceGroupName** ainsi que leurs valeurs. Démarrez ensuite le Runbook. Voici l’extrait de code C# permettant d’appeler la méthode définie ci-dessus.

  ```csharp
  IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
  // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
  RunbookParameters.Add("resourceGroupName", "WSSC1");
  
  //Call the StartRunbook method with parameters
  StartRunbook("Get-AzureVMGraphical", RunbookParameters);
  ```

#### <a name="start-a-runbook-by-using-the-rest-api-and-assign-parameters"></a>Démarrer un Runbook à l’aide de l’API REST et affecter des paramètres

Un travail de runbook peut être créé et démarré à l'aide de l'API REST d'Azure Automation en utilisant la méthode **PUT** avec l'URI de requête suivant : `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`


Dans l’URI de demande, remplacez les paramètres suivants :

* **subscriptionId :** ID de votre abonnement Azure.  
* **resourceGroupName :** nom du groupe de ressources du compte Automation.
* **automationAccountName :** nom de votre compte Automation hébergé dans le service cloud spécifié.  
* **jobName :** GUID du travail. Vous pouvez créer des GUID dans PowerShell à l’aide de la commande **[GUID]::NewGuid(). ToString()** .

Pour transmettre des paramètres à la tâche du Runbook, utilisez le corps de la demande. Il accepte les deux propriétés suivantes, fournies au format JSON :

* **Runbook name** : Requis. Nom du Runbook pour le démarrage de la tâche.  
* **Runbook parameters** : facultatif. Dictionnaire de la liste de paramètres au format (name, value) où name doit être un nom de type String, et valeur peut être toute valeur JSON valide.

Si vous souhaitez démarrer le Runbook **Get-AzureVMTextual** créé précédemment avec **VMName** et **resourceGroupName** en tant que paramètres, utilisez le format JSON suivant pour le corps de la demande.

   ```json
    {
      "properties":{
        "runbook":{
        "name":"Get-AzureVMTextual"},
      "parameters":{
         "VMName":"WindowsVM",
         "resourceGroupName":"ContosoSales"}
        }
    }
   ```

Un code d’état HTTP 201 est retourné si la tâche est correctement créée. Pour plus d’informations sur les en-têtes et les corps de réponse, consultez l'article [Guide pratique pour créer une tâche de runbook à l'aide de l'API REST](/rest/api/automation/job/create).

### <a name="test-a-runbook-and-assign-parameters"></a>Tester un Runbook et affecter des paramètres

Lorsque vous [testez le brouillon de votre runbook](automation-testing-runbook.md) à l’aide de l’option de test, la page **Test** s’ouvre, et vous pouvez y configurer les valeurs des paramètres que vous avez créés.

![Tester et affecter des paramètres](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Lier une planification à un Runbook et affecter des paramètres

Vous pouvez [lier une planification](automation-schedules.md) à votre Runbook afin de démarrer celui-ci à un moment spécifique. Lors de la création de la planification, vous affectez des paramètres d’entrée, que le runbook utilise quand la planification le démarre. Vous ne pouvez pas enregistrer la planification tant que toutes les valeurs de paramètres obligatoires n’ont pas été fournies.

![Planifier et affecter des paramètres](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Créer un WebHook pour un Runbook et affecter des paramètres

Vous pouvez créer un [WebHook](automation-webhooks.md) pour votre Runbook, puis configurer les paramètres d’entrée de celui-ci. Vous ne pouvez pas enregistrer le WebHook tant que toutes les valeurs de paramètres obligatoires n’ont pas été fournies.

![Créer un WebHook et affecter des paramètres](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Lorsque vous exécutez un Runbook à l’aide d’un WebHook, un paramètre d’entrée prédéfini, **[Webhookdata](automation-webhooks.md#details-of-a-webhook)** , est envoyé avec les paramètres d’entrée que vous avez définis. Pour plus de détails, vous pouvez cliquer sur le paramètre **WebhookData** pour le développer.

![Paramètre WebhookData](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="pass-a-json-object-to-a-runbook"></a>Passer un objet JSON à un runbook

Il peut être utile stocker les données à passer dans un runbook dans un fichier JSON.
Par exemple, vous pouvez créer un fichier JSON qui contient tous les paramètres que vous souhaitez passer dans un runbook. Pour ce faire, vous devez convertir le fichier JSON en chaîne, puis convertir la chaîne en objet PowerShell avant de passer celui-ci au runbook.

Dans cet exemple, vous disposez d'un script PowerShell qui appelle [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) pour démarrer un runbook PowerShell, en passant le contenu du fichier JSON au runbook.
Le runbook PowerShell démarre une machine virtuelle Azure, en obtenant les paramètres pour la machine virtuelle à partir de l’objet JSON qui a été passé.

### <a name="create-the-json-file"></a>Créer le fichier JSON

Tapez le test suivant dans un fichier texte et enregistrez-le sous `test.json` quelque part sur votre ordinateur local.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Créer le runbook

Créer un nouveau runbook PowerShell nommé « Test-Json » dans Azure Automation.
Pour savoir comment créer un nouveau runbook PowerShell, consultez [Mon premier runbook PowerShell](automation-first-runbook-textual-powershell.md).

Pour accepter les données JSON, le runbook doit prendre un objet comme paramètre d’entrée.

Le runbook peut utiliser ensuite les propriétés définies dans le JSON.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzureRmVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
```

Enregistrez et publiez ce runbook dans votre compte Automation.

### <a name="call-the-runbook-from-powershell"></a>Appeler le runbook à partir de PowerShell

Vous pouvez maintenant appeler le runbook à partir de votre ordinateur local à l’aide d’Azure PowerShell.
Lancer les commandes PowerShell suivantes :

1. Connectez-vous à Azure :

   ```powershell
   Connect-AzureRmAccount
   ```

   Vous êtes invité à entrer vos informations d'identification Azure.

   > [!IMPORTANT]
   > **Add-AzureRmAccount** est désormais un alias de **Connect-AzureRMAccount**. Quand vous effectuez une recherche dans vos éléments de bibliothèque, si vous ne voyez pas **Connect-AzureRMAccount**, vous pouvez utiliser **Add-AzureRmAccount** ou mettre à jour vos modules dans votre compte Automation.

1. Obtenir le contenu du fichier JSON et les convertir en une chaîne :

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

   `JsonPath` est le chemin d’accès où vous avez enregistré le fichier JSON.

1. Convertir le contenu de la chaîne de `$json` dans un objet PowerShell :

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Créer une table de hachage pour les paramètres pour `Start-AzureRmAutomationRunbook` :

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   Notez que vous définissez la valeur `Parameters` sur l'objet PowerShell contenant les valeurs du fichier JSON.
1. Démarrer le runbook

   ```powershell
   $job = Start-AzureRmAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les différentes façons de démarrer un Runbook, voir [Démarrage d’un Runbook](automation-starting-a-runbook.md).
* Pour modifier un Runbook textuel, voir [Modification des Runbooks textuels](automation-edit-textual-runbook.md).
* Pour modifier un Runbook graphique, voir [Création graphique dans Azure Automation](automation-graphical-authoring-intro.md).
