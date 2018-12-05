---
title: Gérer un espace de travail Machine Learning - Azure Machine Learning Studio | Microsoft Docs
description: Gestion de l'accès aux espaces de travail Azure Machine Learning, et déploiement et gestion des services web d'API ML
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=hshapiro, author=heatherbshapiro)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: daf3d413-7a77-4beb-9a7a-6b4bdf717719
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.openlocfilehash: c470d10b933b31d4cfe151fb541c2182562a2805
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52314102"
---
# <a name="manage-an-azure-machine-learning-workspace"></a>Gestion d'un espace de travail Azure Machine Learning

> [!NOTE]
> Pour plus d’informations sur la gestion des services web dans le portail des services web Azure Machine Learning, voir [Gérer un service web à l’aide du portail des services web Azure Machine Learning](manage-new-webservice.md).
> 
> 

Vous pouvez gérer les espaces de travail Machine Learning sur le Portail Azure.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="use-the-azure-portal"></a>Utilisation du portail Azure

Pour gérer un espace de travail dans le portail Azure :

1. Connectez-vous au [portail Azure](https://portal.azure.com/) à l’aide d’un compte d’administrateur d’abonnements Azure.
2. Dans la zone de recherche située en haut de la page, entrez « espaces de travail machine learning », puis sélectionnez **Espaces de travail Machine Learning**.
3. Cliquez sur l’espace de travail que vous souhaitez gérer.

Outre les options et les informations de gestion des ressources standard disponibles, vous pouvez :

- Afficher la page **Propriétés** : cette page contient les informations d’espace de travail et de ressource. Vous pouvez modifier l’abonnement et le groupe de ressources auxquels cet espace de travail est connecté.
- **Resynchroniser les clés de stockage** : l’espace de travail gère les clés d’accès au compte de stockage. Si le compte de stockage modifie les clés, vous pouvez cliquer sur **Resynchroniser les clés** pour les synchroniser avec l’espace de travail.

Pour gérer les services web associés à cet espace de travail, utilisez le portail des services web Machine Learning. Pour en savoir plus, consultez l’article [Gérer un service web à l’aide du portail des services web Azure Machine Learning](manage-new-webservice.md).

> [!NOTE]
> Pour déployer ou gérer de nouveaux services web, vous devez avoir un rôle de contributeur ou d’administrateur dans l’abonnement dans lequel le service web est déployé. Si vous invitez un autre utilisateur dans un espace de travail Machine Learning, vous devez l’affecter à un rôle de contributeur ou d’administrateur dans l’abonnement avant qu’il ne puisse déployer ou gérer des services web. 
> 
>Pour plus d’informations sur la définition des autorisations d’accès, consultez [Gérer les accès à l’aide du contrôle d’accès en fonction du rôle et du portail Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [déployer le Machine Learning avec des modèles Azure Resource Manager](deploy-with-resource-manager-template.md). 