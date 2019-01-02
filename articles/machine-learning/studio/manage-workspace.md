---
title: Gérer un espace de travail Machine Learning Studio | Microsoft Docs
description: Gestion de l'accès aux espaces de travail Azure Machine Learning, et déploiement et gestion des services web d'API ML
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: daf3d413-7a77-4beb-9a7a-6b4bdf717719
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.openlocfilehash: 8c5dfd82a7bf0d1985869c8de4e3b313ef885947
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53270771"
---
# <a name="manage-an-azure-machine-learning-studio-workspace"></a>Gérer un espace de travail Azure Machine Learning Studio

> [!NOTE]
> Pour plus d’informations sur la gestion des services web dans le portail des services web Azure Machine Learning, voir [Gérer un service web à l’aide du portail des services web Azure Machine Learning](manage-new-webservice.md).
> 
> 

Vous pouvez gérer les espaces de travail Machine Learning sur le Portail Azure.



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