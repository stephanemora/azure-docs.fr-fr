---
title: Conditions préalables à une offre Azure Application | Microsoft Docs
description: Conditions préalables à la publication d’une offre Azure Application sur la Place de marché Microsoft Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: b3f978635127ef6aabb123d1c95b76ed06fccbbf
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56097788"
---
# <a name="azure-application-prerequisites"></a>Conditions préalables à Azure Application

Cet article décrit les conditions techniques et métier préalables à la publication d’une offre d’application managée sur la Place de marché Azure.  Si ce n’est déjà fait, visionnez la vidéo [Création de modèles de solution et d’applications managées pour la Place de marché Azure](https://channel9.msdn.com/Events/Build/2018/BRK3603).


## <a name="technical-requirements"></a>Exigences techniques

La configuration technique requise inclut les éléments suivants :

*   Modèles Azure Resource Manager Pour plus d’informations, consultez [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Cet article décrit la structure d’un modèle Azure Resource Manager. Elle présente les différentes sections d’un modèle et les propriétés disponibles dans ces sections. Le modèle se compose d’un JSON et d’expressions que vous pouvez utiliser pour construire des valeurs pour votre déploiement. 
* Modèles de démarrage rapide Azure.<br> Pour plus d'informations, consultez les pages suivantes :

  * [Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/). Déployez des ressources Azure par le biais d’Azure Resource Manager avec des modèles distribués par la communauté pour améliorer votre productivité. Azure Resource Manager vous permet d’approvisionner vos applications à l'aide d'un modèle déclaratif. Dans un modèle unique, vous pouvez déployer plusieurs services ainsi que leurs dépendances. Le même modèle vous permet de déployer plusieurs fois votre application à chaque phase du cycle de vie de l’application.
  * [GitHub : Modèles de démarrage rapide Azure Resource Manager](https://github.com/azure/azure-quickstart-templates). Ce référentiel contient tous les modèles Azure Resource Manager disponibles actuellement et distribués par la communauté. Un index de modèles pouvant faire l’objet de recherches est conservé sur https://azure.microsoft.com/en-us/documentation/templates/.
* Créer des définitions d’interface utilisateur<br>
Pour plus d’informations, consultez [Créer une interface utilisateur de portail Azure pour votre application managée](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview). Cet article présente les concepts de base du fichier createUiDefinition.json. Le portail Azure utilise ce fichier pour générer l’interface utilisateur permettant de créer une application managée.


## <a name="business-requirements"></a>Exigences commerciales

Les exigences de l’entreprise incluent des obligations procédurales, contractuelles et légales suivantes :

* Vous devez être inscrit en tant qu’éditeur de la place de marché Cloud. Si vous n’êtes pas inscrit, suivez les étapes décrites dans l’article Devenir un éditeur sur la Place de marché dans le cloud.

>[!NOTE]
>Vous devez utiliser le même compte d’inscription au Centre de développement Microsoft pour vous connecter au Portail Cloud Partner. Vous ne devez utiliser qu'un seul compte Microsoft pour vos offres Azure Marketplace. Ce compte ne doit pas être propre à des services ou des offres spécifiques.

* Votre entreprise (ou sa filiale) doit être située dans un pays expéditeur pris en charge par la Place de marché Microsoft Azure. Pour obtenir la liste actuelle de ces pays, consultez les [politiques concernant la participation à la Place de marché Microsoft Azure](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
* Votre produit doit être disponible sous licence d’une manière compatible avec les modèles de facturation pris en charge par la Place de marché Microsoft Azure. Pour plus d’informations, consultez [Options de facturation sur la Place de marché Azure](https://docs.microsoft.com/azure/marketplace/marketplace-commercial-transaction-capabilities-and-considerations).
* Il vous incombe de consentir tous les efforts raisonnables pour mettre à la disposition de vos clients un support technique. Ce support peut être gratuit, payant ou accessible via une communauté.
* Il vous incombe de gérer les licences de vos logiciels et de toutes les dépendances de logiciels tiers.
* Vous devez fournir un contenu conforme aux critères définis pour que votre offre soit répertoriée sur la Place de marché Microsoft Azure et sur le portail Azure.
* Vous devez accepter les termes de la Stratégie de participation de la Place de marché Microsoft Azure et du Contrat d’éditeur.
* Vous devez vous engager à respecter les Conditions d’utilisation du site web Microsoft Azure, la Déclaration de confidentialité Microsoft et le Contrat relatif au programme Microsoft Azure Certified.


## <a name="publishing-requirements"></a>Exigences de publication

Pour publier une offre Azure Application, vous devez respecter les conditions préalables suivantes :

* Avoir des métadonnées prêtes à être utilisées. La liste suivante (non exhaustive) présente un exemple de métadonnées :
  * Un titre
  * Une description (au format HTML)
  * Une image de logo (au format PNG), dans les tailles d’image fixes suivantes : 40 x 40 pixels, 90 x 90 pixels, 115 x 115 pixels et 255 x 115 pixels.
* Des *conditions d’utilisation* et une *politique de confidentialité*
* Documentation
* Contacts du support


## <a name="next-steps"></a>Étapes suivantes

Une fois toutes les conditions requises remplies, vous êtes prêt à [créer une offre d’application Azure](./cpp-create-offer.md). 
 
