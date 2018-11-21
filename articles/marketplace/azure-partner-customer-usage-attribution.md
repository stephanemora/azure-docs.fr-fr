---
title: Attribution d’utilisation client et partenaire Azure
description: Vue d’ensemble montrant comment effectuer le suivi de l’utilisation du client pour les solutions de la place de marché Azure
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: yijenj
manager: nunoc
editor: ''
ms.assetid: e8d228c8-f9e8-4a80-9319-7b94d41c43a6
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/15/2018
ms.author: yijenj
ms.openlocfilehash: 3a1c5341e391c8be1af42eea940fbf147b88e7c8
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51685699"
---
# <a name="azure-partner-customer-usage-attribution"></a>Attribution de l’utilisation de client partenaire Azure

En tant que partenaire en logiciels pour Azure, vos solutions requièrent les composants Azure ou un déploiement direct sur l’infrastructure Azure. Les clients qui déploient une solution de partenaire et configurent leurs propres ressources Azure peuvent avoir du mal à s’informer sur l’état du déploiement et à percevoir l’impact sur la croissance Azure. Un niveau supérieur de visibilité vous permet de vous aligner avec les équipes de vente Microsoft et de bénéficier d’un crédit pour les programmes de partenaires Microsoft.   

Microsoft propose désormais une méthode aidant les partenaires à mieux suivre l’utilisation d’Azure dans les déploiements clients de leurs logiciels sur Azure. Cette nouvelle méthode a recours à Azure Resource Manager pour orchestrer le déploiement des services Azure.

En tant que partenaire Microsoft, vous pouvez associer l’utilisation d’Azure à des ressources Azure quelconques que vous configurez pour le compte d’un client. Vous pouvez former cette association sur la Place de marché Azure, dans le référentiel de démarrage rapide, dans les référentiels GitHub privés et lors d’engagements clients individualisés. Pour activer le suivi, deux approches sont possibles :

- Modèles Azure Resource Manager : modèles Resource Manager ou modèles de solution permettant de déployer les services Azure afin d’exécuter les logiciels du partenaire. Les partenaires peuvent créer un modèle Resource Manager qui définit l’infrastructure et la configuration de leur solution Azure. Un modèle Resource Manager vous permet, ainsi qu’à vos clients, de déployer votre solution tout au long de son cycle de vie. Vous pouvez être certain que vos ressources sont déployées dans un état cohérent. 

- API Azure Resource Manager : les partenaires peuvent appeler directement les API Resource Manager pour déployer un modèle Resource Manager ou pour générer les appels d’API, afin de configurer directement les services Azure. 

## <a name="use-resource-manager-templates"></a>Utiliser des modèles Resource Manager

De nombreuses solutions de partenaires sont déployées pour l’abonnement d’un client grâce aux modèles Resource Manager. Si vous disposez d’un modèle Resource Manager sur la Place de marché Azure, sur GitHub ou dans le cadre d’un démarrage rapide, le processus de modification de votre modèle pour activer cette nouvelle méthode de suivi doit être relativement simple. Si vous n’utilisez pas de modèle Azure Resource Manager, voici quelques liens pour vous aider à mieux comprendre les modèles Resource Manager et la manière d’en créer un : 

*   [Créer et déployer des modèles Azure Resource Manager à l’aide du portail Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)
*   [Guide de création d’un modèle de solution pour Azure Marketplace](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-solution-template-creation)

## <a name="add-a-guid-to-your-template"></a>Ajouter un GUID à votre modèle

Pour ajouter un identificateur global unique (GUID), vous devez apporter une modification unique au fichier de modèle principal :

1. [Créer un GUID](#create-guids) à l’aide de la méthode conseillée et [enregistrer le GUID](#register-guids-and-offers).

1. Ouvrez le modèle Resource Manager.

1. Ajoutez une nouvelle ressource dans le fichier de modèle principal. Cette ressource doit être uniquement dans le fichier **mainTemplate.json** ou **azuredeploy.json**, et pas dans l’un des modèles imbriqués ou liés.

1. Entrez la valeur du GUID après le préfixe **pid-** (par exemple, pid-eb7927c8-dd66-43e1-b0cf-c346a422063).

1. Recherchez d’éventuelles erreurs dans le modèle.

1. Republiez le modèle dans les référentiels appropriés.

1. [Vérifiez que le GUID a fonctionné dans le déploiement du modèle](#verify-the-guid-deployment).

### <a name="sample-resource-manager-template-code"></a>Exemple de code de modèle Resource Manager

Pour activer les ressources de suivi pour votre modèle, vous devez ajouter la ressource supplémentaire suivante dans la section des ressources. Veillez à modifier l’exemple de code ci-dessous avec vos propres entrées lorsque vous l’ajoutez au fichier modèle principal.
Cette ressource doit être ajoutée uniquement dans le fichier **mainTemplate.json** ou **azuredeploy.json**, et pas dans l’un des modèles imbriqués ou liés.
```
// Make sure to modify this sample code with your own inputs where applicable

{ // add this resource to the resources section in the mainTemplate.json (do not add the entire file)
    "apiVersion": "2018-02-01",
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

## <a name="use-the-resource-manager-apis"></a>Utiliser les API Resource Manager

Dans certains cas, les partenaires préfèrent effectuer directement des appels d’API REST Resource Manager afin de déployer des services Azure. [Azure prend en charge plusieurs kits SDK](https://docs.microsoft.com/azure/#pivot=sdkstools) pour permettre ces appels. Vous pouvez utiliser l’un des kits SDK ou appeler les API REST directement pour déployer des ressources.

Si vous utilisez un modèle Resource Manager, vous devez baliser votre solution en vous conformant aux instructions précédentes. Si vous n’utilisez pas un modèle Resource Manager et n’effectuez pas d’appels directs d’API, vous pouvez toujours baliser votre déploiement pour associer l’utilisation des ressources Azure. 

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>Baliser un déploiement avec les API Resource Manager

Pour cette approche de suivi, lorsque vous concevez vos appels d’API, incluez un GUID dans l’en-tête d’agent utilisateur au sein de la requête. Ajoutez le GUID pour chaque offre ou référence SKU. Mettez la chaîne en forme avec le préfixe **pid-** et incluez le GUID généré par le partenaire. Voici un exemple de format GUID à insérer dans l’agent utilisateur : 

![Exemple de format GUID](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!Note]
> Le format de la chaîne est important. Si le préfixe **pid-** n’est pas inclus, il est impossible d’interroger les données. D’autres kits SDK procèdent au suivi différemment. Pour implémenter cette méthode, passez en revue la prise en charge et le suivi de votre kit SDK Azure préféré. 

#### <a name="example-the-python-sdk"></a>Exemple : Kit de développement logiciel (SDK) Python

Pour Python, utilisez l’attribut **config**. Vous pouvez uniquement ajouter l’attribut à un UserAgent. Voici un exemple :

![Ajouter l’attribut à un agent utilisateur](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!Note]
> Ajoutez l’attribut à chaque client. Il n’existe aucune configuration statique globale. Vous pouvez baliser une fabrique de clients pour vous assurer que chaque client effectue un suivi. Pour plus d’informations, consultez cet [exemple de fabrique de clients sur GitHub](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>Baliser un déploiement à l’aide d’Azure PowerShell

Si vous déployez des ressources par le biais d’Azure PowerShell, ajoutez votre GUID à l’aide de la méthode suivante :

```
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>Baliser un déploiement à l’aide d’Azure CLI

Lorsque vous utilisez Azure CLI pour ajouter votre GUID, définissez la variable d’environnement **AZURE_HTTP_USER_AGENT**. Vous pouvez définir cette variable dans la portée d’un script. Vous pouvez également définir la variable de manière globale dans la portée de l’interpréteur de commandes :

```
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```

## <a name="create-guids"></a>Créer des GUID

Un GUID désigne un numéro de référence unique à 32 chiffres hexadécimaux. Pour créer un GUID et effectuer son suivi, vous devez utiliser un générateur GUID. L’équipe de stockage Azure a créé un [formulaire générateur de GUID](https://aka.ms/StoragePartners) qui vous enverra par e-mail un GUID au format approprié et pourra être réutilisé dans les différents systèmes de suivi. 

> [!Note]
> Nous vous recommandons vivement d’utiliser le [formulaire Générateur de GUID de Stockage Azure](https://aka.ms/StoragePartners) pour créer votre GUID. Pour plus d’informations, consultez notre [Forum Aux Questions (FAQ)](#faq).

Nous vous recommandons de créer un GUID unique pour chaque offre et canal de distribution de chaque produit. Si vous ne souhaitez pas que les rapports soient scindés, vous pouvez choisir d'utiliser un GUID unique pour les différents canaux de distribution du produit. 

Si vous déployez un produit à l'aide d'un modèle et qu'il est disponible à la fois sur la Place de marché Azure et sur GitHub, vous pouvez créer et enregistrer 2 GUID distincts :

*   Produit A sur la Place de marché Azure 
*   Produit A sur GitHub

Les rapports sont établis à l'aide de la valeur de partenaire (ID partenaire Microsoft) et des GUID. 

Vous pouvez également effectuer le suivi des GUID à un niveau plus granulaire, par exemple au niveau des références SKU, où les références SKU constituent des variantes d’une offre.

## <a name="register-guids-and-offers"></a>Inscrire des GUID et des offres

Pour inclure un GUID dans notre suivi, le GUID doit être inscrit.  

Toutes les inscriptions pour les GUID de modèle doivent être effectuées sur le portail Cloud Partner de la Place de marché Azure. 

Une fois que vous aurez ajouté le GUID à votre modèle ou dans l’agent utilisateur et enregistré le GUID dans le portail Cloud Partner, tous les déploiements feront l’objet d’un suivi. 

1. Optez pour la [Place de marché Azure](https://aka.ms/listonazuremarketplace) afin d’accéder au portail Cloud Partner.

   * Les partenaires doivent [avoir un profil sur le portail Cloud Partner](https://docs.microsoft.com/azure/marketplace/become-publisher). Vous êtes invité à répertorier l’offre dans la Place de marché Azure ou AppSource.
   * Les partenaires peuvent inscrire plusieurs GUID.
   * Les partenaires peuvent également enregistrer un GUID pour des offres et des modèles de solution extérieurs à la Place de marché.
 
1. Connectez-vous au [portail Cloud Partner](https://cloudpartner.azure.com/).

1. Dans l’angle supérieur droit, sélectionnez l’icône de votre compte, puis **Publisher profile** (Profil de l’éditeur).

   ![Sélectionner le profil de l’éditeur](media/marketplace-publishers-guide/guid-image-for-lu.png)

1. Sur la page **Profil**, cliquez sur **Add Tracking GUID (Ajouter un GUID de suivi).**

   ![Sélectionner Add Tracking GUID (Ajouter un GUID de suivi)](media/marketplace-publishers-guide/guid-how-to-add-tracking.png)

1. Dans le champ **GUID de suivi**, entrez votre GUID de suivi. Entrez simplement le GUID sans le préfixe **pid-**. Dans le champ **Description personnalisée**, entrez le nom ou la description de l’offre.

   ![Page Profil](media/marketplace-publishers-guide/guid-dev-center-login.png)
   
   ![Entrer le GUID et la description de l’offre](media/marketplace-publishers-guide/guid-dev-center-example.png)

1. Pour inscrire plusieurs GUID, sélectionnez de nouveau **Add Tracking GUID** (Ajouter un GUID de suivi). Des champs supplémentaires apparaissent sur la page.

   ![Sélectionner de nouveau Add Tracking GUID (Ajouter un GUID de suivi)](media/marketplace-publishers-guide/guid-dev-center-example-add.png)
   
   ![Entrer un autre GUID et une description de l’offre](media/marketplace-publishers-guide/guid-dev-center-example-description.png)

1. Sélectionnez **Enregistrer**.

   ![Sélectionner Enregistrer](media/marketplace-publishers-guide/guid-dev-center-save.png)

Une fois que vous aurez ajouté le GUID à votre modèle ou dans l’agent utilisateur et enregistré le GUID dans le portail Cloud Partner, tous les déploiements feront l’objet d’un suivi. 

## <a name="verify-the-guid-deployment"></a>Vérifier le déploiement du GUID 

Une fois que vous avez modifié votre modèle et effectué un déploiement de tests, utilisez le script PowerShell suivant pour récupérer les ressources déployées et balisées. 

Vous pouvez utiliser ce script pour vérifier que le GUID a été ajouté avec succès à votre modèle Resource Manager. Le script ne s’applique pas au déploiement de l’API Resource Manager.

Connectez-vous à Azure. Sélectionnez l’abonnement avec le déploiement que vous souhaitez vérifier avant d’exécuter le script. Exécutez le script dans le contexte d’abonnement du déploiement.

Le **GUID** et le nom **resourceGroup** du déploiement sont des paramètres requis.

Vous pouvez obtenir [le script d’origine](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1) sur GitHub.

```
Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the pid deployment

$correlationId = (Get-AzureRmResourceGroupDeployment -ResourceGroupName 
$resourceGroupName -Name "pid-$guid").correlationId

# Find all deployments with that correlationId

$deployments = Get-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in a deployment by name
# PowerShell doesn't surface outputResources on the deployment
# or correlationId on the deploymentOperation

foreach ($deployment in $deployments){

# Get deploymentOperations by deploymentName
# then the resourceId for any create operation

($deployment | Get-AzureRmResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}
```

## <a name="notify-your-customers"></a>Informer vos clients

Les partenaires doivent informer leurs clients des déploiements utilisant le suivi de GUID Resource Manager. Microsoft signale au partenaire l’utilisation d’Azure associée à ces déploiements. Les exemples suivants incluent le contenu que vous pouvez utiliser pour informer vos clients de ces déploiements. Dans les exemples, remplacez \<PARTNER> par le nom de votre entreprise. Les partenaires doivent s’assurer que la notification s’aligne sur leurs stratégies de confidentialité et de collecte de données, y compris sur les options relatives aux clients à exclure du suivi. 

### <a name="notification-for-resource-manager-template-deployments"></a>Notification des déploiements de modèle Resource Manager

Lorsque vous déployez ce modèle, Microsoft peut identifier l’installation du logiciel \<PARTNER> avec les ressources Azure déployées. Microsoft peut mettre en corrélation les ressources Azure utilisées pour prendre en charge le logiciel. Microsoft collecte ces informations pour fournir les meilleures expériences possibles avec leurs produits et pour gérer leur activité. Les données sont collectées et régies par les stratégies de confidentialité de Microsoft, qui se trouvent à l’adresse https://www.microsoft.com/trustcenter. 

### <a name="notification-for-sdk-or-api-deployments"></a>Notification de déploiement des kits SDK ou d’API

Lorsque vous déployez le logiciel \<PARTNER>, Microsoft peut identifier l’installation du logiciel \<PARTNER> avec les ressources Azure déployées. Microsoft peut mettre en corrélation les ressources Azure utilisées pour prendre en charge le logiciel. Microsoft collecte ces informations pour fournir les meilleures expériences possibles avec leurs produits et pour gérer leur activité. Les données sont collectées et régies par les stratégies de confidentialité de Microsoft, qui se trouvent à l’adresse https://www.microsoft.com/trustcenter.

## <a name="get-support"></a>Obtenir de l’aide

Si vous avez besoin d’aide, procédez comme suit.

1. Accédez à la [page de support](https://go.microsoft.com/fwlink/?linkid=844975). 

1. Sous **Type de problème**, sélectionnez **Intégration sur la place de marché**.

1. Choisissez la **catégorie** de votre problème :

   - Pour les problèmes d’association d’utilisation, sélectionnez **Autres**.
   - Pour tout problème d’accès avec le portail Cloud Partner de la Place de marché Azure, sélectionnez **Problème d’accès**.
   
    ![Choisir la catégorie de problème](media/marketplace-publishers-guide/lu-article-incident.png)

1. Sélectionnez **Envoyer une demande**.

1. Sur la page suivante, entrez les valeurs requises. Sélectionnez **Continuer**.

1. Sur la page suivante, entrez les valeurs requises.

   > [!Important] 
   > Dans le champ **Titre de l’incident**, entrez **Suivi de l’utilisation ISV**. Détaillez votre problème.
   
   ![Entrez Suivi de l’utilisation ISV comme titre de l’incident](media/marketplace-publishers-guide/guid-dev-center-help-hd%201.png)

1. Remplissez le formulaire, puis sélectionnez **Envoyer**.

## <a name="faq"></a>Forum Aux Questions

**Quel avantage l’ajout du GUID apporte-t-il au modèle ?**

Microsoft permet aux partenaires de visualiser les déploiements de leurs modèles par les clients, ainsi que les informations détaillées sur leur utilisation influencée. Microsoft et le partenaire peuvent utiliser ces informations pour favoriser un engagement plus proche entre les équipes de vente. Microsoft et le partenaire peuvent utiliser ces données pour obtenir une vue plus cohérente de l’impact d’un partenaire individuel sur la croissance Azure. 

**Qui peut ajouter un GUID à un modèle ?**

La ressource de suivi vise à connecter la solution du partenaire à l’utilisation d’Azure par les clients. Les données d’utilisation sont liées à l’identité Microsoft Partner Network d’un partenaire (ID MPN). La création de rapports est mise à la disposition des partenaires sur le portail Cloud Partner.

**Une fois ajouté, un GUID peut-il être modifié ?**
 
Oui, un client ou un partenaire d’implémentation peut personnaliser le modèle et modifier ou supprimer le GUID. Nous suggérons aux partenaires de décrire de façon proactive le rôle de la ressource et le GUID à leurs clients et partenaires pour empêcher la suppression ou la modification du GUID de suivi. La modification du GUID affecte uniquement les ressources et les déploiements nouveaux, qui n’existent pas.

**Quand la création de rapports sera-t-elle disponible ?**

Une version bêta de la création de rapports doit être disponible bientôt. La création de rapports sera intégrée sur le portail Cloud Partner.

**Puis-je effectuer le suivi de modèles déployés à partir d’un dépôt autre que Microsoft tel que GitHub ?**

Oui, tant que le GUID est présent lorsque le modèle est déployé, l’utilisation fait l’objet d’un suivi. Les partenaires sont tenus d’avoir un profil sur le portail Cloud Partner pour inscrire les modèles associés qui sont publiés en dehors de la Place de marché Azure. 

**Y a-t-il une différence si le modèle est déployé à partir de la Place de marché Azure au lieu d’autres dépôts tels que GitHub ?**

Oui, les partenaires qui publient des offres sur la Place de marché Azure peuvent recevoir des données plus détaillées sur les déploiements à partir de la Place de marché Azure. Les partenaires bénéficient de l’exposition de leur offre aux clients sur le portail de la Place de marché Azure et le Portail Azure. Les offres de la Place de marché Azure génèrent également des prospects pour le partenaire.

**Que se passe-t-il si je crée un modèle personnalisé pour l’engagement d’un client individuel ?**

Vous pouvez toujours ajouter le GUID au modèle. Si vous utilisez un GUID inscrit existant, il est inclus dans la création de rapports. Si vous créez un GUID, vous devez l’inscrire afin de l’inclure dans le suivi.

**Le client reçoit-il également la création de rapports ?**

Les clients peuvent effectuer le suivi de leur utilisation des ressources individuelles ou des groupes de ressources définis par le client sur le Portail Azure.   

**Est-ce que cette méthode de suivi est similaire au partenaire de référence numérique (DPOR) ?**

Cette nouvelle méthode de connexion du déploiement et de l’utilisation à la solution d’un partenaire fournit un mécanisme permettant de lier une solution de partenaire à l’utilisation d’Azure. Le partenaire de référence numérique (DPOR) est destiné à associer un partenaire de conseil (intégrateur de systèmes) ou de gestion (fournisseur de services managés) à l’abonnement Azure d’un client.   

**Quel avantage y a-t-il à utiliser le formulaire Générateur de GUID de Stockage Azure ?**

Le formulaire Générateur de GUID de Stockage Azure est l’assurance de générer un GUID au format adéquat. De plus, si vous utilisez l’une des méthodes de suivi de plan de données de Stockage Azure, vous pouvez utiliser le même GUID pour le suivi de plan de contrôle de la Place de marché. Cela vous permet d’exploiter un même GUID unifié pour l’attribution Partenaire sans avoir à gérer plusieurs GUID.
