---
title: Attribution d’utilisation client et partenaire Azure
description: Vue d’ensemble montrant comment effectuer le suivi de l’utilisation du client pour les solutions de la place de marché Azure
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: e8d228c8-f9e8-4a80-9319-7b94d41c43a6
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/26/2018
ms.author: ellacroi
ms.openlocfilehash: 46cd5dbc044cbd0b7e38e5f0d0c8aa1916387a2d
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038189"
---
# <a name="azure-partner-customer-usage-attribution"></a>Attribution de l’utilisation de client partenaire Azure

En tant que partenaire en logiciels pour Azure, vos solutions requièrent les composants Azure ou sont déployées directement sur l’infrastructure Azure.  Aujourd’hui, lorsqu’un client déploie une solution partenaire et configure ses propres ressources Azure, il est difficile pour le partenaire d’obtenir une visibilité sur l’état de ces déploiements et d’observer l’impact sur la croissance Azure. L’ajout d’un niveau plus élevé de visibilité aide les partenaires à s’aligner avec les équipes de vente Microsoft et bénéficier d’un crédit pour les programmes de partenaires Microsoft.   

Microsoft crée une nouvelle méthode pour aider les partenaires à mieux suivre l’utilisation d’Azure qui résulte du déploiement par un client de vos logiciels sur Azure. Cette nouvelle méthode repose sur l’utilisation d’Azure Resource Manager pour orchestrer le déploiement des services Azure.

En tant que partenaire Microsoft, vous pouvez associer l’utilisation d’Azure à des ressources Azure quelconques que vous configurez pour le compte d’un client.  Cela est possible via la Place de marché Azure, le dépôt de démarrage rapide, les dépôts GitHub privés et même les engagements de client à client.  Pour activer le suivi, deux approches sont disponibles :

- Modèles Azure Resource Manager : modèles Azure Resource Manager ou modèles de solution permettant de déployer les services Azure afin d’exécuter les logiciels du partenaire. Les partenaires peuvent créer un modèle Azure Resource Manager qui définit l’infrastructure et la configuration de votre solution Azure. La création d’un modèle Azure Resource Manager permet à vous et à vos clients de déployer votre solution à plusieurs reprises tout au long de son cycle de vie pour avoir la garantie que vos ressources présentent un état cohérent lors de leur déploiement. 

- API Azure Resource Manager : les partenaires peuvent appeler les API Azure Resource Manager directement pour déployer un modèle Azure Resource Manager ou pour générer les appels d’API, afin de configurer directement les services Azure. 

## <a name="method-1-azure-resource-manager-templates"></a>Méthode 1 : Modèles Azure Resource Manager 

Aujourd'hui, de nombreuses solutions de partenaires sont déployées lors de l’abonnement d’un client à l’aide des modèles Azure Resource Manager.  Si vous disposez déjà d’un modèle Azure Resource Manager sur la Place de marché Azure, sur GitHub ou dans le cadre d’un guide de démarrage rapide, le processus de modification de votre modèle pour activer cette nouvelle méthode de suivi doit être relativement simple.  Si vous n’utilisez actuellement pas de modèle Azure Resource Manager, voici quelques liens pour vous aider à mieux comprendre les modèles Azure Resource Manager et la manière d’en créer un : 

*   [Créer et déployer votre premier modèle Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)
*   [Guide de création d’un modèle de solution pour la Place de marché Azure](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-solution-template-creation)

## <a name="instructions-add-a-guid-to-your-existing-azure-resource-manager-template"></a>Instructions : ajouter un GUID à votre modèle Azure Resource Manager existant

L’ajout d’un GUID est une modification unique du fichier de modèle principal :
 1. Créez un GUID et supposez que la valeur générée est eb7927c8-dd66-43e1-b0cf-c346a422063
 2. Ouvrez le modèle Azure Resource Manager.
 3. Ajoutez une nouvelle ressource dans le fichier de modèle principal. Cette ressource doit simplement être dans le fichier mainTemplate.json ou azuredeploy.json, pas dans l’un des modèles imbriqués ou liés.
 4. Entrez le GUID après « pid- », comme indiqué ci-dessus.

   Le résultat doit ressembler à l’exemple suivant : `pid-eb7927c8-dd66-43e1-b0cf-c346a422063`

 5. Recherchez d’éventuelles erreurs dans le modèle.
 6. Republiez le modèle dans les dépôts appropriés.

## <a name="sample-template-code"></a>Exemple de code de modèle

![](https://raw.githubusercontent.com/ellacroi/azure-docs-pr/lu-images-again-dangit-all/articles/marketplace/media/marketplace-publishers-guide/tracking-sample-code-for-lu-1.PNG?token=Ak8ZDB0JzsBdUGlKEIeHNJRS7b0BWn4Gks5bbMwwwA%3D%3D)


## <a name="method-2-azure-resource-manager-apis"></a>Méthode 2 : Interfaces API Azure Resource Manager

Dans certains cas, les partenaires préfèrent effectuer directement des appels d’API REST Azure Resource Manager afin de déployer des services Azure. [Azure prend en charge plusieurs kits SDK](https://docs.microsoft.com/azure/#pivot=sdkstools) pour permettre cela.  Vous pouvez utiliser l’un des kits SDK ou appeler les API REST directement pour déployer des ressources.

Si vous utilisez un modèle Azure Resource Manager, vous devez baliser votre solution en suivant les instructions ci-dessus.  Si vous n’utilisez pas un modèle Azure Resource Manager et n’effectuez pas d’appels directs d’API, vous pouvez toujours baliser votre déploiement pour associer l’utilisation des ressources Azure. 

**Comment baliser un déploiement à l’aide des API Azure Resource Manager :** Pour cette approche, lorsque vous concevez vos appels d’API, vous devez inclure un GUID dans l’en-tête d’agent utilisateur de la demande. Ce GUID doit être ajouté pour chaque offre ou référence SKU.  La chaîne doit être formatée avec le préfixe pid- puis inclure le GUID généré par le partenaire.   

![](https://raw.githubusercontent.com/ellacroi/azure-docs-pr/lu-images-again-dangit-all/articles/marketplace/media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG?token=Ak8ZDDiokRcj4PJj0aMkZmfF8BdOuOTzks5bbM35wA%3D%3D)

>[!Note] 
>Format du GUID à insérer dans l’agent utilisateur : pid-eb7927c8-dd66-43e1-b0cf-c346a422063     // entrez votre GUID après « pid- »

Le format de la chaîne est important. Si le préfixe « pid- » n’est pas inclus, il est impossible d’exécuter la requête sur les données. D’autres kits SDK procèdent différemment.  Pour implémenter cette méthode, vous devez passer en revue la prise en charge et l’approche de votre kit SDK Azure préféré. 

**Exemple utilisant le kit SDK Python :** Pour Python, vous devez utiliser l’attribut « config ». Vous pouvez uniquement ajouter à un UserAgent. Voici un exemple : 

![](https://raw.githubusercontent.com/ellacroi/azure-docs-pr/lu-images-again-dangit-all/articles/marketplace/media/marketplace-publishers-guide/python-for-lu.PNG?token=Ak8ZDK5Um4J6oY-7x25tuBpa168BEiYMks5bbMuUwA%3D%3D)

>Cela doit être fait pour chaque client, il n’existe aucune configuration statique globale (vous pouvez avoir recours à une fabrique de clients pour vous assurer que chaque client procède ainsi). 
>[Informations de référence supplémentaires](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79)

Comment baliser un déploiement en utilisant Azure PowerShell ou l’interface de ligne de commande Azure : si vous déployez des ressources via Azure PowerShell, vous pouvez ajouter votre GUID à l’aide de la méthode suivante :

```

[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")


```

Pour ajouter votre GUID lors de l’utilisation de l’interface de ligne de commande Azure, définissez la variable d’environnement AZURE_HTTP_USER_AGENT.  Vous pouvez la définir dans la portée d’un script ou, pour la définir globalement, dans la portée de l’interpréteur de commandes, utilisez :

```

export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'


```

## <a name="registering-guidsoffers"></a>Enregistrement de GUID/d’offres

Pour que le GUID soit inclus dans notre suivi, il doit être enregistré.  

Tous les enregistrements pour les GUID de modèle doivent être effectués via le portail Cloud Partner (CPP) de la Place de marché Azure. 

1. Optez dès maintenant pour la [Place de marché Azure](http://aka.ms/listonazuremarketplace) pour obtenir l’accès au portail Cloud Partner.

 *  Les partenaires devront [avoir un profil dans CPP](https://docs.microsoft.com/azure/marketplace/become-publisher) et seront encouragés à répertorier l’offre sur la Place de marché Azure ou AppSource 
 *  Les partenaires pourront enregistrer plusieurs GUID 
 *  Les partenaires pourront également enregistrer un GUID pour les offres / modèles de solution extérieurs à la place de marché
 
2. Se connecter au [Portail Cloud Partner](https://cloudpartner.azure.com/)
3. Dans l’angle supérieur droit du portail, cliquez sur l’icône de votre compte, puis sur le **profil d’éditeur**

![](https://github.com/ellacroi/azure-docs-pr/blob/more-lu-images/articles/marketplace/media/marketplace-publishers-guide/guid-image-for-lu.png)

4. Sur la page du profil, cliquez sur **Add Tracking GUID** (Ajouter un GUID de suivi).

![](https://github.com/ellacroi/azure-docs-pr/blob/last-lu-images-i-hope/articles/marketplace/media/marketplace-publishers-guide/guid-how-to-add-tracking.png)

5. Dans le champ développé, entrez votre GUID de suivi (simplement le GUID, sans le préfixe « pid- ») dans le champ **Tracking GUID** (GUID de suivi), puis entrez le nom ou la description de votre offre dans le champ **Description personnalisée**.

![](https://github.com/ellacroi/azure-docs-pr/blob/more-lu-images/articles/marketplace/media/marketplace-publishers-guide/guid-dev-center-login.png)

![](https://github.com/ellacroi/azure-docs-pr/blob/more-lu-images/articles/marketplace/media/marketplace-publishers-guide/guid-dev-center-example.png)

6. Pour inscrire plusieurs GUID, cliquez à nouveau sur **Add Tracking GUID** (Ajouter un GUID de suivi). Un autre champ développé s’ouvre. 

![](https://github.com/ellacroi/azure-docs-pr/blob/more-lu-images/articles/marketplace/media/marketplace-publishers-guide/guid-dev-center-example-add.png)

![](https://github.com/ellacroi/azure-docs-pr/blob/more-lu-images/articles/marketplace/media/marketplace-publishers-guide/guid-dev-center-example-description.png)

7. Lorsque vous avez terminé la modification, cliquez sur **Enregistrer** pour enregistrer vos modifications. 

![](https://github.com/ellacroi/azure-docs-pr/blob/more-lu-images/articles/marketplace/media/marketplace-publishers-guide/guid-dev-center-save.png)



## <a name="verification-of-guid-deployment"></a>Vérification du déploiement de GUID 

Une fois que vous avez modifié votre modèle et effectué un déploiement de test, vous pouvez utiliser le script PowerShell suivant pour récupérer les ressources qui ont été déployées et balisées. 

Vous pouvez l’utiliser pour vérifier si le GUID a été ajouté avec succès à votre modèle Azure Resource Manager. Il ne s’applique pas au déploiement des API Azure Resource Manager.

Connectez-vous à Azure et sélectionnez l’abonnement qui contient le déploiement que vous souhaitez vérifier avant d’exécuter le script. Il doit être exécuté dans le contexte d’abonnement du déploiement.

Le GUID et le nom du groupe de ressources du déploiement sont des paramètres requis.

Vous trouverez le script d’origine [ici](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1).

```

Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName'
)

#get the correlationId of the pid deployment

$correlationId = (Get-AzureRmResourceGroupDeployment -ResourceGroupName 
$resourceGroupName -Name "pid-$guid").correlationId

#find all deployments with that correlationId

$deployments = Get-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

#find all deploymentOperations in a deployment by name (since PowerShell does not surface outputResources on the deployment or correlationId on the deploymentOperation)

foreach ($deployment in $deployments){

#get deploymentOperations by deploymentName and then the resourceId for any create operation

($deployment | Get-AzureRmResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}


```

## <a name="guidance-on-creating-guids"></a>Conseils en matière de création d’identificateurs GUID

Un GUID (identificateur global unique) est un numéro de référence unique composé de 32 chiffres hexadécimaux. Pour créer un GUID, vous devez utiliser un générateur GUID pour créer ses GUID et en assurer le suivi.  Il existe plusieurs [générateurs de GUID en ligne](https://www.bing.com/search?q=guid%20generator&qs=n&form=QBRE&sp=-1&ghc=2&pq=guid%20g&sc=8-6&sk=&cvid=0BAFAFCD70B34E4296BB97FBFA3E1B4E) que vous pouvez utiliser.

Nous vous recommandons de créer un GUID unique pour chaque offre et canal de distribution.  Par exemple, si vous disposez de deux solutions, toutes les deux déployées via un modèle et disponibles à la fois sur la Place de marché Azure et sur GitHub.  Créez quatre GUID :

*   Offre A sur la Place de marché Azure 
*   Offre A sur GitHub
*   Offre B sur la Place de marché Azure 
*   Offre B sur GitHub

La création de rapports est réalisée par partenaire (ID de partenaire Microsoft) et GUID. 

Vous pouvez également choisir d’effectuer le suivi des GUID à un niveau plus granulaire, tel que le niveau des références SKU (où les références SKU sont des variantes d’une offre).

## <a name="guidance-on-privacy-and-data-collection"></a>Conseils en matière de confidentialité et de collecte des données

Les partenaires doivent fournir des messages pour informer leurs clients que les déploiements qui incluent le suivi de GUID d’Azure Resource Manager permettront à Microsoft de signaler l’utilisation d’Azure associée à ces déploiements au partenaire.  Un exemple de langage est fourni ci-dessous. À l’endroit où est indiqué « Partenaire », vous devez renseigner le nom de votre propre entreprise. En outre, les partenaires doivent s’assurer que le langage s’aligne sur leurs propres stratégies de confidentialité et de collection de données, y compris sur les options pour les clients à exclure du suivi : 

**Pour les déploiements de modèle Azure Resource Manager**

Lorsque vous déployez ce modèle, Microsoft peut identifier l’installation de logiciel partenaire avec les ressources Azure déployées.  Microsoft sera en mesure de mettre en corrélation les ressources Azure utilisées pour prendre en charge le logiciel.  Microsoft collecte ces informations pour fournir les meilleures expériences possibles avec leurs produits et pour gérer leur activité. Ces données seront collectées et régies par les stratégies de confidentialité de Microsoft, qui se trouvent à l’adresse https://www.microsoft.com/trustcenter. 

**Pour les déploiements de kits SDK ou d’API**

Lorsque vous déployez ce modèle, Microsoft peut identifier l’installation de partenaire Software avec les ressources Azure déployées.  Microsoft sera en mesure de mettre en corrélation les ressources Azure utilisées pour prendre en charge le logiciel.  Microsoft collecte ces informations pour fournir les meilleures expériences possibles avec leurs produits et pour gérer leur activité. Ces données seront collectées et régies par les stratégies de confidentialité de Microsoft, qui se trouvent à l’adresse https://www.microsoft.com/trustcenter.

## <a name="support"></a>Support

Pour obtenir une assistance, veuillez suivre les étapes ci-dessous :
 1. Consultez la page de support à l’adresse [go.microsoft.com/fwlink/?linkid=844975](https://go.microsoft.com/fwlink/?linkid=844975)
 2. Pour des problèmes d’association d’utilisation – sélectionnez le type de problème : **Intégration sur la place de marché** et la catégorie : **Autre**, puis cliquez sur **Envoyer une demande**. 

Pour des problèmes liés à l’accès au portail Cloud Partner de la Place de marché Azure – sélectionnez le type de problème : **Intégration sur la place de marché** et la catégorie : **Problème d’accès**, puis cliquez sur **Envoyer une demande**.

 ![](https://github.com/ellacroi/azure-docs-pr/blob/last-lu-images-i-hope/articles/marketplace/media/marketplace-publishers-guide/lu-article-incident.png)
 

 3. Renseignez les champs requis à la page suivante, puis cliquez sur **Continuer**.
 4. Renseignez les champs de texte libres à la page suivante. **Important** : renseignez le Titre de l’incident en spécifiant **« Suivi de l’utilisation ISV »** et décrivez votre problème en détail dans le champ de texte libre volumineux qui suit.  Complétez le reste du formulaire et cliquez sur **Envoyer**. 
 
  ![](https://github.com/qianw211/azure-docs-pr/blob/MyImgAdded-2/articles/marketplace/media/marketplace-publishers-guide/guid-dev-center-help.png)

 

## <a name="faqs"></a>FAQ

**Quel avantage apporte l’ajout du GUID au modèle ?**

Microsoft fournira aux partenaires une vue des déploiements de leurs modèles par les clients et des informations détaillées sur leur utilisation influencée.  Microsoft et le partenaire peuvent également utiliser ces informations pour favoriser un engagement plus proche entre les équipes de vente. Microsoft et le partenaire peuvent les utiliser également pour obtenir une vue plus cohérente de l’impact d’un partenaire individuel sur la croissance Azure. 

**Qui peut ajouter un GUID à un modèle ?**

La ressource de suivi est destinée à connecter la solution du partenaire à l’utilisation d’Azure des clients.  Les données d’utilisation sont liées à l’identité Microsoft Partner Network (ID MPN) d’un partenaire et la création de rapports sera disponible pour les partenaires dans le portail Cloud Partner (CPP).  

**Une fois ajouté, un GUID peut-il être modifié ?**
 
Oui, un client ou un partenaire d’implémentation peut personnaliser le modèle et peut modifier ou supprimer le GUID. Nous suggérons aux partenaires de décrire de façon proactive le rôle de la ressource et le GUID à leurs clients et partenaires pour empêcher la suppression ou la modification du GUID de suivi.  La modification du GUID affecte uniquement les ressources et les déploiements nouveaux, qui n’existent pas.

**Quand la création de rapports sera-t-elle disponible ?**

Une version bêta de la création de rapports doit être disponible bientôt.  La création de rapports sera intégrée dans le portail Cloud Partner (CPP).

**Puis-je effectuer le suivi de modèles déployés à partir d’un dépôt autre que Microsoft tel que GitHub ?**

Oui, tant que le GUID est présent lorsque le modèle est déployé, l’utilisation fait l’objet d’un suivi.  
Les partenaires sont tenus d’avoir un profil dans le portail Cloud Partner pour enregistrer les modèles associés publiés hors de la Place de marché Azure. 

**Y a-t-il une différence si le modèle est déployé à partir de la Place de marché Azure au lieu d’autres dépôts tels que GitHub ?**

Oui, les partenaires qui publient des offres sur la Place de marché Azure peuvent recevoir des données plus détaillées sur les déploiements à partir de la Place de marché Azure.  Les partenaires bénéficieront de l’exposition de leur offre aux clients sur le portail de la Place de marché Azure et dans le portail de gestion Azure. Les offres sur la Place de marché Azure génèrent également des prospects pour le partenaire.

**Que se passe-t-il si je crée un modèle personnalisé pour l’engagement d’un client individuel ?**

Vous pouvez toujours ajouter le GUID au modèle.  Si vous utilisez un GUID existant qui a été enregistré, il est inclus dans la création de rapports.  Si vous créez un nouveau GUID, vous devez l’enregistrer afin de l’inclure dans le suivi.

**Le client reçoit-il également la création de rapports ?**

Les clients sont actuellement en mesure d’effectuer le suivi de leur utilisation des ressources individuelles ou des groupes de ressources définis par le client dans le portail de gestion Azure.   

**Est-ce que cette méthode de suivi est similaire au partenaire de référence numérique (DPOR) ?**

Cette nouvelle méthode de connexion du déploiement et de l’utilisation à la solution d’un partenaire vise à fournir un mécanisme pour lier une solution de partenaire à l’utilisation d’Azure.  Le partenaire de référence numérique (DPOR) est destiné à associer un partenaire de conseil (intégrateur de systèmes) ou de gestion (fournisseur de services managés) à l’abonnement Azure d’un client.   
