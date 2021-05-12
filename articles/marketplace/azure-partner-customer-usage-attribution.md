---
title: Attribution de l’utilisation client Azure
description: Obtenez une vue d’ensemble du suivi de l’utilisation client pour les applications Azure sur la place de marché commerciale et d’autres adresses IP déployables développées par les partenaires.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 04/12/2021
ms.custom: devx-track-terraform
ms.openlocfilehash: aadf1f4c9ab8fa20933256749bd8ce37a1258063
ms.sourcegitcommit: eda26a142f1d3b5a9253176e16b5cbaefe3e31b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109738803"
---
# <a name="azure-customer-usage-attribution"></a>Attribution de l’utilisation client Azure

L’attribution de l’utilisation client associe l’utilisation des ressources Azure dans les abonnements clients créés lors du déploiement de votre adresse IP en tant que partenaire. La formation de ces associations dans les systèmes Microsoft internes offre une meilleure visibilité de l’empreinte Azure exécutant vos logiciels. Pour les [offres Azure Application dans la place de marché commerciale](#commercial-marketplace-azure-apps), cette fonctionnalité de suivi vous aide à vous aligner sur les équipes de vente Microsoft et à gagner du crédit pour les programmes partenaires Microsoft. L’attribution de l’utilisation client n’est pas applicable aux [offres de machines virtuelles Azure sur la Place de marché commerciale](./azure-vm-create.md). L’éditeur de la Place de marché n’a rien à faire pour s’assurer que la consommation Azure des offres de machines virtuelles est suivie dans les abonnements des clients finaux.

L’attribution de l’utilisation de client prend en charge trois options de déploiement :

1. Modèles Azure Resource Manager (composantes essentielles courantes des applications Azure, également appelées « modèles de solution » ou « applications managées » dans la place de marché commerciale) : les partenaires créent des modèles Resource Manager pour définir l’infrastructure et la configuration de leurs solutions Azure. Un modèle Resource Manager permet à vos clients de déployer les ressources de votre solution dans un état cohérent et reproductible.
1. API Azure Resource Manager : les partenaires peuvent appeler les API Resource Manager pour déployer un modèle Resource Manager ou pour provisionner directement les services Azure.
1. Terraform : les partenaires peuvent utiliser Terraform pour déployer un modèle Resource Manager ou pour déployer directement des services Azure.

Il existe des cas d’usage secondaires pour l’attribution de l’utilisation client en dehors de la place de marché commerciale décrits [plus loin dans cet article](#other-use-cases).

>[!IMPORTANT]
>- L’attribution de l’utilisation client n’est pas destinée à suivre le travail des intégrateurs de systèmes, des fournisseurs de services managés ou des outils conçus pour déployer et gérer des ressources Azure.
>- L’attribution de l’utilisation client est destinée aux nouveaux déploiements et ne prend pas en charge le suivi des ressources qui ont déjà été déployées.
>- Tous les services Azure ne sont pas compatibles avec l’attribution de l’utilisation du client. Azure Kubernetes Services (AKS) et Virtual Machine Scale Sets et Azure Batch présentent des problèmes connus qui entraînent la production de rapports d’utilisation insuffisants.

## <a name="commercial-marketplace-azure-apps"></a>Applications Azure de la place de marché commerciale

Le suivi de l’utilisation d’Azure à partir d’applications Azure publiées sur la place de marché commerciale est en grande partie automatique. Lorsque vous chargez un modèle Resource Manager dans le cadre de la [configuration technique du plan de votre application Azure sur la place de marché](./create-new-azure-apps-offer-solution.md#define-the-technical-configuration), l’Espace partenaires ajoute un ID de suivi lisible par Azure Resource Manager.

Si vous utilisez des API Azure Resource Manager, vous devez ajouter votre ID de suivi conformément aux [instructions ci-dessous](#use-resource-manager-apis) pour le transmettre à Azure Resource Manager à mesure que votre code déploie des ressources. Cet ID est visible dans l’Espace partenaires sur la page de configuration technique de votre plan. 

> [!NOTE]
> Pour les applications Azure existantes, une migration ponctuelle a commencé en mars 2021 pour mettre à jour les ID de suivi dans la configuration technique de chaque plan. L’utilisation à partir des déploiements passés de ces offres fait toujours l’objet d’un suivi dans les systèmes Microsoft.
>
>Lorsque vous mettez à jour vos offres, vous n’avez plus besoin d’ajouter le type de ressource **Microsoft.Resources/deployments** dans votre fichier de modèle principal.

## <a name="other-use-cases"></a>Autres cas d’usage 

Vous pouvez utiliser l’attribution de l’utilisation client pour suivre l’utilisation d’Azure des solutions non disponibles dans la place de marché commerciale. Ces solutions se trouvent généralement dans le référentiel Démarrage rapide, les dépôts GitHub privés ou proviennent d’engagements clients individualisés qui créent une adresse IP durable (telle qu’une application scalable et déployable).

Plusieurs étapes manuelles sont requises :

1. Créez un ou plusieurs GUID à utiliser comme ID de suivi.
1. Inscrivez ces GUID dans l’Espace partenaires.
1. Ajoutez vos GUID inscrits à votre application Azure et/ou aux chaînes de l’agent utilisateur.

### <a name="create-guids"></a>Créer des GUID

Contrairement aux ID de suivi créés par l’Espace partenaires en votre nom pour les applications Azure dans la place de marché commerciale, d’autres utilisations de CUA nécessitent la création d’un GUID à utiliser comme ID de suivi. Un GUID désigne un identificateur de référence unique à 32 chiffres hexadécimaux. Pour créer un GUID à des fins de suivi, vous devez utiliser un générateur de GUID, par exemple via PowerShell :

```powershell
[guid]::NewGuid()
```

Vous devez créer un GUID unique pour chaque produit et canal de distribution. Si vous ne souhaitez pas que les rapports soient scindés, vous pouvez utiliser un GUID unique pour les différents canaux de distribution d’un produit. La création de rapports est assurée par le GUID et l’ID Microsoft Partner Network.

### <a name="register-guids"></a>Inscrire les GUID

Les GUID doivent ensuite être inscrits dans l’Espace partenaires afin d’être associés à vous, en tant que partenaire :

1. Connectez-vous à l’[Espace partenaires](https://partner.microsoft.com/dashboard).

1. Inscrivez-vous en tant qu’[éditeur de la place de marché commerciale](https://aka.ms/JoinMarketplace).

1. Sélectionnez **Settings** (Paramètres) (icône d’engrenage) dans le coin supérieur droit, puis **Account settings** (Paramètres du compte).

1. Sélectionnez **Organization profile** > **Identifiers** > **Add Tracking GUID** (Profil d’organisation, Identificateurs, Ajouter un GUID de suivi).

1. Dans la zone **GUID**, entrez votre GUID de suivi. Entrez simplement le GUID sans le préfixe `pid-`. Dans la zone **Description**, entrez le nom ou la description de votre solution.

1. Pour inscrire plusieurs GUID, sélectionnez de nouveau **Add Tracking GUID** (Ajouter un GUID de suivi). Des champs supplémentaires apparaissent sur la page.

1. Sélectionnez **Enregistrer**.

### <a name="add-a-guid-to-a-resource-manager-template"></a>Ajouter un GUID à un modèle Resource Manager

Pour ajouter votre GUID inscrit à un modèle Resource Manager, apportez une seule modification au fichier de modèle principal :

1. Ouvrez le modèle Resource Manager.

1. Ajoutez une nouvelle ressource de type [Microsoft. Resources/Deployments](/azure/templates/microsoft.resources/deployments) dans le fichier de modèle principal. La ressource doit être uniquement dans le fichier **mainTemplate.json** ou **azuredeploy.json**, pas dans l’un des modèles imbriqués ou liés.

1. Entrez la valeur du GUID derrière le préfixe `pid-`, en tant que nom de la ressource. Par exemple, si le GUID est eb7927c8-dd66-43e1-b0cf-c346a422063, le nom de la ressource est **PID-eb7927c8-dd66-43e1-b0cf-c346a422063**. Exemple :
 
```json
{ // add this resource to the resources section in the mainTemplate.json
    "apiVersion": "2020-06-01",
    "name": "pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", // use your generated GUID here
    "type": "Microsoft.Resources/deployments",
    "properties": {
        "mode": "Incremental",
        "template": {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "resources": []
        }
    }
} // remove all comments from the file when complete
```
4. Recherchez d’éventuelles erreurs dans le modèle.

1. Republiez le modèle dans les référentiels appropriés.

1. [Vérifiez que le GUID a fonctionné dans le déploiement du modèle](#verify-deployments-tracked-with-a-guid).

> [!TIP]
> Pour plus d’informations sur la création et la publication de modèles Resource Manager, consultez : [Créer et déployer votre premier modèle Resource Manager](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

### <a name="verify-deployments-tracked-with-a-guid"></a>Vérifier les déploiements suivis à l’aide d’un GUID

Une fois que vous avez modifié votre modèle et effectué un déploiement de test, utilisez le script PowerShell suivant pour récupérer les ressources déployées et balisées.

Vous pouvez utiliser ce script pour vérifier que le GUID a été ajouté avec succès à votre modèle Resource Manager. Le script ne s’applique pas au déploiement de l’API Resource Manager ou Terraform.

Connectez-vous à Azure. Sélectionnez l’abonnement avec le déploiement que vous souhaitez vérifier avant d’exécuter le script. Exécutez le script dans le contexte d’abonnement du déploiement.

Le **GUID** (appelé ci-dessous « deploymentName ») et le nom **resourceGroupName** du déploiement sont des paramètres obligatoires.

Vous pouvez obtenir [le script d’origine](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1) sur GitHub.

```powershell
Param(
    [string][Parameter(Mandatory=$true)]$deploymentName, # the full name of the deployment, e.g. pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the named deployment
$correlationId = (Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name "$deploymentName").correlationId

# Find all deployments with that correlationId
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in all deployments with that correlationId as PowerShell doesn't surface outputResources on the deployment or correlationId on the deploymentOperation

foreach ($deployment in $deployments){
    # Get deploymentOperations by deploymentName
    # then the resourceIds for each resource
    ($deployment | Get-AzResourceGroupDeploymentOperation | Where-Object{$_.targetResource -notlike "*Microsoft.Resources/deployments*"}).TargetResource
}
```

### <a name="notify-your-customers"></a>Informer vos clients

Les partenaires doivent informer leurs clients des déploiements utilisant l’attribution de l’utilisation de client. Microsoft signale au partenaire l’utilisation d’Azure associée à ces déploiements. Les exemples suivants incluent le contenu que vous pouvez utiliser pour informer vos clients de ces déploiements. Dans les exemples, remplacez \<PARTNER> par le nom de votre entreprise. Les partenaires doivent s’assurer que la notification s’aligne sur leurs stratégies de confidentialité et de collecte de données, notamment sur les options relatives aux clients à exclure du suivi.

#### <a name="notification-for-resource-manager-template-deployments"></a>Notification des déploiements de modèle Resource Manager

Quand vous déployez ce modèle, Microsoft peut identifier l’installation du logiciel \<PARTNER> avec les ressources Azure déployées. Microsoft peut mettre en corrélation les ressources utilisées pour prendre en charge le logiciel. Microsoft collecte ces informations pour fournir les meilleures expériences possibles avec leurs produits et pour gérer leur activité. Les données sont collectées et régies par les stratégies de confidentialité de Microsoft, qui se trouvent à l’adresse [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter).

#### <a name="notification-for-sdk-or-api-deployments"></a>Notification de déploiement des kits SDK ou d’API

Quand vous déployez le logiciel \<PARTNER>, Microsoft peut identifier l’installation du logiciel \<PARTNER> avec les ressources Azure déployées. Microsoft peut mettre en corrélation les ressources utilisées pour prendre en charge le logiciel. Microsoft collecte ces informations pour fournir les meilleures expériences possibles avec leurs produits et pour gérer leur activité. Les données sont collectées et régies par les stratégies de confidentialité de Microsoft, qui se trouvent à l’adresse [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter).

## <a name="use-resource-manager-apis"></a>Utiliser des API Resource Manager

Dans certains cas, les partenaires préfèrent effectuer directement des appels d’API REST Resource Manager afin de déployer des services Azure. [Azure prend en charge plusieurs kits SDK](../index.yml?pivot=sdkstools) pour permettre ces appels. Vous pouvez utiliser l’un des kits SDK ou appeler les API REST directement pour déployer des ressources.

Pour autoriser l’attribution de l’utilisation client, lorsque vous concevez vos appels d’API, incluez votre ID de suivi dans l’en-tête de l’agent utilisateur au sein de la demande. Mettez la chaîne en forme avec le préfixe `pid-`. Exemples :

```xml
//Commercial Marketplace Azure app
pid-contoso-myoffer-partnercenter //copy the tracking ID exactly as it appears in Partner Center

//Other use cases
pid-b6addd8f-5ff4-4fc0-a2b5-0ec7861106c4 //enter your GUID after "pid-"
```
> [!IMPORTANT]
> Si vous utilisez des API Resource Manager avec une application Azure dans la place de marché commerciale, utilisez l’ID de suivi fourni dans l’Espace partenaires. N’utilisez PAS un GUID.

Divers kits SDK interagissent différemment avec les API Resource Manager et nécessitent des différences dans votre code. Les exemples ci-dessous présentent l’approche de la place de marché non commerciale utilisant un GUID et couvrent un large éventail de kits SDK Azure les plus populaires.

#### <a name="example-python-sdk"></a>Exemple : SDK Python

Pour Python, utilisez l’attribut **config**. Vous pouvez uniquement ajouter l’attribut à un UserAgent. Exemple :

```python
client = azure.mgmt.servicebus.ServiceBusManagementClient(**parameters)
client.config.add_user_agent("pid-b6addd8f-5ff4-4fc0-a2b5-0ec7861106c4")
```
> [!IMPORTANT]
> Ajoutez l’attribut à chaque client. Il n’existe aucune configuration statique globale. Vous pouvez baliser une fabrique de clients pour vous assurer que chaque client effectue un suivi. Pour plus d’informations, consultez cet [exemple de fabrique de clients sur GitHub](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="example-net-sdk"></a>Exemple : kit SDK .NET

Pour .NET, veillez à définir l’agent utilisateur. Utilisez la bibliothèque [Microsoft.Azure.Management.Fluent](/dotnet/api/microsoft.azure.management.fluent) pour définir l’agent utilisateur avec le code suivant (exemple en C#) :

```csharp
var azure = Microsoft.Azure.Management.Fluent.Azure
    .Configure()
    // Add your pid in the user agent header
    .WithUserAgent("pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", String.Empty) 
    .Authenticate(/* Credentials created via Microsoft.Azure.Management.ResourceManager.Fluent.SdkContext.AzureCredentialsFactory */)
    .WithSubscription("<subscription ID>");
```

#### <a name="example-azure-powershell"></a>Exemple : Azure PowerShell

Si vous déployez des ressources par le biais d’Azure PowerShell, ajoutez votre GUID à l’aide de cette méthode :

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="example-azure-cli"></a>Exemple : Azure CLI

Lorsque vous utilisez Azure CLI pour ajouter votre GUID, définissez la variable d’environnement **AZURE_HTTP_USER_AGENT** dans l’étendue d’un script. Vous pouvez également définir la variable de manière globale dans la portée de l’interpréteur de commandes :

```powershell
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```

Pour plus d’informations, consultez la page [Kit de développement logiciel (SDK) Azure pour Go](/azure/developer/go/).

## <a name="use-terraform"></a>Utiliser Terraform

La prise en charge de Terraform est disponible via la version 1.21.0 du fournisseur Azure : [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019). Cela s’applique à tous les partenaires qui déploient leur solution via Terraform ainsi qu’à toutes les ressources déployées et mesurées par le fournisseur Azure (version 1.21.0 ou ultérieure).

Le fournisseur Azure pour Terraform a ajouté un nouveau champ facultatif intitulé [*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id) permettant de spécifier le GUID de suivi utilisé pour votre solution. La valeur de ce champ peut également être approvisionnée depuis la variable d’environnement *ARM_PARTNER_ID*.

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
Définissez la valeur de *partner_id* sur un GUID inscrit. NE faites PAS précéder le GUID de « pid- ». Définissez-le simplement sur le GUID réel.

> [!IMPORTANT]
> Si vous utilisez Terraform avec une application Azure dans la place de marché commerciale, utilisez l’ID de suivi complet fourni dans l’Espace partenaires. N’utilisez PAS un GUID.

## <a name="get-support"></a>Obtenir de l’aide

Découvrez les options de support disponibles via la place de marché commerciale dans [Support technique pour le programme Place de marché commerciale dans l’Espace partenaires](support.md).

### <a name="how-to-submit-a-technical-consultation-request"></a>Comment envoyer une demande de consultation technique

1. Visitez [Services techniques des partenaires](https://aka.ms/TechnicalJourney).
1. Sélectionnez **Infrastructure cloud et gestion** pour afficher le parcours technique.
1. Sélectionnez **Services de déploiement** > **Envoyer une demande**.
1. Connectez-vous à l’aide de votre MSA (compte MPN) ou de votre AAD (compte Tableau de bord partenaire).
1. Renseignez/vérifiez les coordonnées sur le formulaire qui s’ouvre. Les détails de la consultation peuvent être préremplis ou il peut y avoir des options de liste déroulante.
1. Entrez un titre et une description détaillée du problème.
1. Sélectionnez **Envoyer**.

Consultez des instructions pas à pas avec captures d’écran dans [Utilisation des services techniques de prévente et de déploiement](https://aka.ms/TechConsultInstructions).

Vous serez contacté par un consultant technique partenaire Microsoft afin d’organiser un appel destiné à évaluer l’étendue de vos besoins.

## <a name="report"></a>Rapport
La création de rapports sur le suivi de l’utilisation d’Azure par le biais de l’attribution de l’utilisation client n’est pas disponible aujourd’hui pour les partenaires ISV. L’ajout de rapports au programme du marketplace commercial dans Espace partenaires pour couvrir l’attribution de l’utilisation client est prévu pour le second semestre 2021.

## <a name="faq"></a>Forum aux questions

#### <a name="after-a-tracking-id-is-added-can-it-be-changed"></a>Une fois ajouté, un ID de suivi peut-il être modifié ?

Les ID de suivi pour les applications Azure dans la place de marché commerciale sont managés automatiquement par l’Espace partenaires. Un client peut cependant télécharger un modèle et modifier ou supprimer l’ID de suivi. Les partenaires doivent décrire de manière proactive le rôle de l’ID de suivi à leurs clients pour empêcher la suppression ou les modifications. La modification de l’ID de suivi affecte uniquement les ressources et déploiements nouveaux, pas ceux existants.

#### <a name="can-i-track-templates-deployed-from-a-non-microsoft-repository-like-github"></a>Puis-je effectuer le suivi de modèles déployés à partir d’un dépôt autre que Microsoft tel que GitHub ?

Oui, tant que l’ID de suivi est présent lorsque le modèle est déployé, l’utilisation fait l’objet d’un suivi. Pour conserver l’association entre vous, en tant qu’éditeur, et votre modèle déployé à partir d’un référentiel non-Microsoft, commencez par télécharger une copie de votre modèle publié (qui contient l’ID de suivi) à partir du référencement de votre offre sur la place de marché commerciale dans le portail Azure. Publiez cette version dans GitHub ou un autre référentiel non-Microsoft.

Si votre modèle n’est pas référencé dans la place de marché commerciale et qu’il comprend un GUID inscrit, assurez-vous que le GUID est présent dans la version que vous publiez dans GitHub ou un autre référentiel non-Microsoft.

#### <a name="does-the-customer-receive-reporting-as-well"></a>Le client reçoit-il également la création de rapports ?

Non. Les clients peuvent effectuer le suivi de leur utilisation de l’ensemble des ressources ou groupes de ressources sur le portail Azure. Les clients ne voient pas l’utilisation détaillée par l’ID de suivi CUA.

#### <a name="is-customer-usage-attribution-similar-to-the-digital-partner-of-record-dpor-or-partner-admin-link-pal"></a>L’attribution de l’utilisation client est-elle similaire au partenaire de référence numérique (DPOR) ou au lien d’administrateur partenaire (PAL) ?

L’attribution de l’utilisation client est un mécanisme permettant d’associer l’utilisation d’Azure à l’adresse IP renouvelable et déployable d’un partenaire au moment du déploiement. DPOR et PAL sont conçus pour associer un partenaire de conseil (intégrateur de systèmes) ou de gestion (fournisseur de services managés) avec l’empreinte Azure pertinente d’un client durant la période où le partenaire est engagé auprès du client.