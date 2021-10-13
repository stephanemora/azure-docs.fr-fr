---
title: Fournisseurs d’étiquetage de données
titleSuffix: Azure Machine Learning
description: Utilisez une société d’étiquetage de données pour vous aider à étiqueter les données dans votre projet d’étiquetage des données
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.reviewer: sgilley
author: kvijaykannan
ms.author: vkann
ms.date: 09/30/2021
ms.topic: how-to
ms.openlocfilehash: 2937be805d82abde1125c8c8b23162d7eedec015
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129429544"
---
# <a name="work-with-a-data-labeling-vendor-company"></a>Utiliser une société d’étiquetage de données

Découvrez comment impliquer une société d’étiquetage de données pour vous aider à étiqueter vos données. Vous pouvez en savoir plus sur ces entreprises et sur les services d'étiquetage qu'elles proposent sur leurs pages de la [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/consulting-services?page=1&search=AzureMLVend).


## <a name="workflow-summary"></a>Résumé du workflow

Avant de créer votre projet d’étiquetage de données :

1. Sélectionnez un fournisseur de services d’étiquetage.  Pour trouver un fournisseur sur la Place de marché Microsoft Azure :
    1. Passez en revue les [Détails de la liste de ces sociétés d’étiquetage](https://azuremarketplace.microsoft.com/marketplace/consulting-services?page=1&search=AzureMLVend).
    1. Si la société d’étiquetage répond à vos exigences, choisissez l'option **Me contacter** sur la Place de marché Azure. La Place de marché Azure achemine votre demande vers la société d’étiquetage. Vous pouvez contacter plusieurs sociétés avant de choisir la société finale.

1. Contactez un fournisseur de services d’étiquetage et concluez un accord.

Une fois le contrat avec la société d’étiquetage en place :

1. Créez le projet d’étiquetage dans [Azure Machine Learning Studio](https://ml.azure.com). Pour plus d’informations sur la création d’un projet, consultez les articles sur la création d’un [projet d’étiquetage d’image](how-to-create-image-labeling-projects.md) ou de [texte](how-to-create-text-labeling-projects.md).
1. Vous n’êtes pas limité à l’utilisation d’un fournisseur d’étiquetage des données de la Place de marché Azure.  Toutefois, si vous utilisez un fournisseur de la Place de marché Azure :
    1. Sélectionnez **Utiliser une société d’étiquetage de la Place de marché Azure** à l’étape de sélection de main-d’œuvre.
    1. Sélectionnez la société d’étiquetage des données appropriée dans la liste déroulante.

    > [!NOTE]
    > Le nom de la société d’étiquetage ne peut pas être modifié une fois que vous avez créé le projet d’étiquetage.

1. Pour tout fournisseur, que vous le trouviez via la place de marché Azure ou un autre emplacement, activez l’accès (rôles `labeler` et `techlead`) pour la société d’étiquetage à l’aide du contrôle d’accès en fonction du rôle Azure (Azure RBAC). Ces rôles permettent à la société d’accéder aux ressources pour annoter vos données.

## <a name="select-a-company"></a><a name="review"></a> Sélectionner une société

Microsoft a identifié des fournisseurs de services d’étiquetage avec les connaissances et l’expérience pour répondre à vos besoins. Vous pouvez en apprendre plus sur les fournisseurs de services d’étiquetage et choisir un fournisseur, en tenant compte des besoins et des exigences de vos projets, dans leurs pages d’annonce sur la [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/consulting-services?page=1&search=AzureMLVend).

> [!IMPORTANT]
> Vous pouvez en savoir plus sur ces entreprises et sur les services d'étiquetage qu'elles proposent sur leurs pages de la Place de marché Azure. Vous êtes responsable de toute décision d’utiliser une société d’étiquetage qui offre des services via la Place de marché Azure, et vous devez déterminer indépendamment si une société d’étiquetage et son expérience, ses services, son personnel, ses conditions, etc. répondront aux besoins de votre projet. Vous pouvez contacter une société d’étiquetage qui offre des services via la Place de marché Azure à l’aide de l’option **Me contacter**, et vous pouvez vous attendre à recevoir une réponse de la société contactée dans un délai de trois jours ouvrables. Vous allez conclure un accord et effectuer le paiement directement auprès de la société d’étiquetage.

Microsoft passe régulièrement en revue la liste des fournisseurs de services d’étiquetage potentiels dans la Place de marché Azure et peut ajouter ou supprimer des fournisseurs de la liste à tout moment.  

* Si un fournisseur est supprimé, cela n’affecte pas les projets existants ou l’accès de l’entreprise à ces projets.
* Si vous utilisez un fournisseur qui n’est plus répertorié dans la Place de marché Azure, ne sélectionnez pas l’option **Utiliser une société d’étiquetage de la Place de marché Azure** dans votre nouveau projet.
* Un fournisseur supprimé n’aura plus d’annonce dans la Place de marché Azure.
* Un fournisseur supprimé ne pourra plus être contacté via la Place de marché Azure.

Vous pouvez faire appel à plusieurs sociétés d’étiquetage pour différents besoins en matière de projets d’étiquetage. Chaque projet est lié à une société d’étiquetage.

Vous trouverez ci-dessous les sociétés d’étiquetage qui peuvent vous aider à étiqueter vos données à l’aide des services d’étiquetage des données Azure Machine Learning. Consultez les [annonces des fournisseurs](https://azuremarketplace.microsoft.com/marketplace/consulting-services?page=1&search=AzureMLVend).

* [iSoftStone](https://azuremarketplace.microsoft.com/marketplace/consulting-services/isoftstoneinc1614950352893.20210527) 

* [Quadrant Resource](https://azuremarketplace.microsoft.com/marketplace/consulting-services/quadrantresourcellc1587325810226.quadrant_resource_data_labeling)

## <a name="enter-into-a-contract"></a>Conclure un accord 

Une fois que vous avez sélectionné la société d’étiquetage que vous souhaitez utiliser, vous devez passer un contrat directement avec la société d’étiquetage qui définit les conditions de votre engagement. Microsoft n’est pas partie de ce contrat et ne joue aucun rôle dans la détermination ou la négociation de ses conditions générales. Les montants payables dans le cadre de ce contrat seront payés directement à la société d’étiquetage.

Si vous activez l’étiquetage assisté par ML dans un projet d’étiquetage, Microsoft vous facturera séparément pour les ressources de calcul consommées dans le cadre de ce service. Tous les autres frais associés à l’utilisation d’Azure Machine Learning (par exemple le stockage des données utilisées dans votre espace de travail Azure Machine Learning) sont régis par les termes de votre contrat avec Microsoft.

## <a name="enable-access"></a>Activer l’accès

Pour que la société d’étiquetage ait accès à vos projets, vous pouvez y accéder via leur donner l’accès via le [contrôle d’accès en fonction du rôle Azure (Azure RBAC)](how-to-assign-roles.md#manage-workspace-access) au niveau de l’espace de travail.  Si vous envisagez d’utiliser plusieurs sociétés d’étiquetage pour différents projets d’étiquetage, nous vous recommandons de créer des espaces de travail distincts pour chacune.

> [!IMPORTANT]
> Vous, et non Microsoft, êtes responsable de tous les aspects de votre engagement avec une société d’étiquetage, y compris mais sans s’y limiter, les problèmes liés à l’étendue, la qualité, la planification et la tarification.

## <a name="next-steps"></a>Étapes suivantes

* [Créer un projet d’étiquetage d’image et exporter des étiquettes](how-to-create-image-labeling-projects.md)
* [Créer un projet d’étiquetage de texte et exporter des étiquettes (préversion)](how-to-create-text-labeling-projects.md)