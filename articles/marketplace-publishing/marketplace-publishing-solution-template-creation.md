---
title: Guide de création d’un modèle de solution pour Azure Marketplace | Microsoft Docs
description: Instructions détaillées pour créer, certifier et déployer un modèle de solution d’image multimachine virtuelle que d’autres peuvent acheter sur Azure Marketplace.
services: marketplace-publishing
documentationcenter: ''
author: v-miclar
manager: hascipio
editor: ''
ms.assetid: e14e05f2-2385-4ce0-b351-0747cb74ba19
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2016
ms.author: hascipio; v-divte
ROBOTS: NOINDEX
ms.openlocfilehash: 914dece4ba064af00c5b2c34d43dedbb1d62e2e2
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54073738"
---
# <a name="guide-to-create-a-solution-template-for-azure-marketplace"></a>Guide de création d’un modèle de solution pour Azure Marketplace
Une fois l’étape 1 [Création de comptes et inscription][link-acct-creation] exécutée, nous vous avons guidé dans la procédure de création d’un modèle de solution compatible Azure avec la section [Conditions techniques préalables pour créer un modèle de solution](marketplace-publishing-solution-template-creation-prerequisites.md). À présent, nous allons vous guider dans la procédure de création d’un modèle de solution à plusieurs machines virtuelles dans le [Portail de publication][link-pubportal] pour Azure Marketplace.

## <a name="create-your-solution-template-offer-in-the-publishing-portal"></a>Créer votre offre de modèle de solution dans le portail de publication
Accédez à  [https://publish.windowsazure.com](http://publish.windowsazure.com). Pour votre première connexion au [portail de publication](https://publish.windowsazure.com/), utilisez le compte avec lequel le profil de vendeur de votre entreprise a été inscrit. Plus tard, vous pourrez ajouter des employés de votre entreprise comme coadministrateurs dans le portail de publication.

### <a name="1-select-solution-templates"></a>1. Sélectionner « Modèles de solution »
  ![drawing][img-pubportal-menu-sol-templ]

### <a name="2-create-a-new-solution-template"></a>2. Créer un modèle de solution
  ![drawing][img-pubportal-sol-templ-new]

### <a name="3-start-with-topologies"></a>3. Démarrer avec les topologies
Un modèle de solution est « parent » de toutes ses topologies. Vous pouvez définir plusieurs topologies dans une offre/un modèle de solution. Quand une offre est envoyée dans l’environnement intermédiaire, toutes ses topologies l’accompagnent. Suivez les étapes ci-dessous pour définir votre offre :     

* Créer une topologie : L’« identificateur de topologie » est généralement le nom de la topologie du modèle de solution. L’identificateur de topologie est utilisé dans l’URL comme indiqué ci-dessous :

  Place de marché Azure : http://azure.microsoft.com/marketplace/partners/{PublisherNamespace}/{OfferIdentifier}{TopologyIdentifier}

  Portail Azure : https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{TopologyIdentifier}
* Ajouter une nouvelle version.

### <a name="4-get-your-topology-versions-certified"></a>4. Obtenir la certification des versions de votre topologie
Téléchargez un fichier zip contenant tous les fichiers requis pour configurer cette version particulière de la topologie. Ce fichier zip doit contenir les fichiers suivants :

* Fichiers *mainTemplate.json* et *createUiDefinition.json* dans le répertoire racine.
* Tous les modèles liés et tous les scripts nécessaires.

  > [!TIP]
  > Pendant que vos développeurs se chargent de la création des topologies du modèle de solution et obtiennent leur certification, les services commerciaux, marketing et/ou juridique de votre entreprise se chargent du contenu marketing et juridique.
  >
  >

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez créé votre modèle de solution et chargé le fichier zip, suivez les instructions du [Guide de contenu marketing Place de marché](marketplace-publishing-push-to-staging.md) avant de soumettre l’offre. Vous pouvez accéder à l’ensemble des articles concernant la publication sur la Place de marché dans [Bien démarrer : Publier et gérer une offre dans la Place de marché Azure](marketplace-publishing-getting-started.md).

Les rubriques suivantes peuvent également vous intéresser :

* Images de machines virtuelles : [À propos des images de machine virtuelle dans Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)
* Extensions de machines virtuelles : [Fonctionnalités et extensions de machine virtuelle Azure](../virtual-machines/extensions/features-windows.md)
* Azure Resource Manager : [Création de modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) et [Exemples de modèles simples](https://github.com/rjmax/ArmExamples)
* Limitations des comptes de stockage : [How to Monitor for Storage Account Throttling](https://blogs.msdn.com/b/mast/archive/2014/08/02/how-to-monitor-for-storage-account-throttling.aspx) et [Stockage Premium](../virtual-machines/windows/premium-storage.md#scalability-and-performance-targets)

[img-pubportal-menu-sol-templ]:media/marketplace-publishing-solution-template-creation/pubportal-menu-solution-templates.png
[img-pubportal-sol-templ-new]:media/marketplace-publishing-solution-template-creation/pubportal-solution-template-new.png
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-pubportal]:https://publish.windowsazure.com
