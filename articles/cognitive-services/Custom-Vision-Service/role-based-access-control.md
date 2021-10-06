---
title: Contrôle d’accès en fonction du rôle Azure – Custom Vision
titleSuffix: Azure Cognitive Services
description: Cet article vous explique comment configurer le contrôle d’accès en fonction du rôle Azure pour vos projets Custom Vision.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: e2f59dc309181308f409b3533d58d3b6b4b1fdd8
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361879"
---
# <a name="azure-role-based-access-control"></a>Contrôle d'accès en fonction du rôle Azure

Custom Vision prend en charge le contrôle d’accès en fonction du rôle Azure (Azure RBAC), un système d’autorisation pour la gestion de l’accès individuel aux ressources Azure. À l’aide d’Azure RBAC, vous attribuez à différents membres d’équipe différents niveaux d’autorisations pour vos projets Custom Vision. Pour plus d’informations sur Azure RBAC, consultez la [documentation Azure RBAC](../../role-based-access-control/index.yml).

## <a name="add-role-assignment-to-custom-vision-resource"></a>Ajouter une attribution de rôle à une ressource Custom Vision

Azure RBAC peut être assigné à une ressource Custom Vision. Pour accorder l’accès à une ressource Azure, vous ajoutez une attribution de rôle.
1. Dans le [portail Azure](https://ms.portal.azure.com/), sélectionnez **Tous les services**. 
1. Ensuite, sélectionnez **Cognitive Services** et accédez à votre ressource d’entraînement Custom Vision.
   > [!NOTE]
   > Vous pouvez également configurer Azure RBAC pour des groupes de ressources, des abonnements ou des groupes d’administration complets. Pour ce faire, sélectionnez le niveau d’étendue souhaité, puis accédez à l’élément de votre choix (par exemple, en sélectionnant **Groupes de ressources** puis en cliquant sur le groupe de ressources souhaité).
1. Sélectionnez **Contrôle d’accès (IAM)** dans le menu de navigation de gauche.
1. Sélectionnez **Ajouter** -> **Ajouter une attribution de rôle**.
1. Dans l’onglet **Rôle** de l’écran suivant, sélectionnez le rôle que vous souhaitez ajouter.
1. Dans l’onglet **Membres**, sélectionnez un utilisateur, un groupe, un principal de service ou une identité managée.
1. Dans l’onglet **Passer en revue + affecter**, sélectionnez **Passer en revue + affecter** pour affecter le rôle.

Au bout de quelques minutes, le rôle sélectionné est attribué à la cible dans l’étendue sélectionnée. Pour recevoir del’aide relative à ces étapes, consultez [Attribuer des rôles Azure à l’aide du Portail Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="custom-vision-role-types"></a>Types de rôles Custom Vision

Utilisez le tableau suivant pour déterminer les besoins d’accès à vos ressources Custom Vision.

|Role  |Autorisations  |
|---------|---------|
|`Cognitive Services Custom Vision Contributor`     | Accès complet aux projets, y compris la possibilité de créer, de modifier et de supprimer un projet.        |
|`Cognitive Services Custom Vision Trainer`     | Accès complet, à l’exception de la possibilité de créer et de supprimer un projet. Les entraîneurs peuvent afficher et modifier des projets, ainsi qu’entraîner, publier, dépublier ou exporter les modèles.        |
|`Cognitive Services Custom Vision Labeler`     | Possibilité de télécharger, de modifier ou de supprimer des images d’entraînement, ainsi que de créer, ajouter, supprimer ou effacer des étiquettes. Les étiqueteurs peuvent consulter les projets, mais ne peuvent pas mettre à jour autre chose que des images d’entraînement et des étiquettes.         |
|`Cognitive Services Custom Vision Deployment`     | Possibilité de publier, d’annuler la publication ou d’exporter les modèles. Les responsables de déploiement peuvent consulter les projets, mais ne peuvent pas mettre à jour un projet, des images d’entraînement ni des étiquettes.        |
|`Cognitive Services Custom Vision Reader`     | Possibilité de consulter les projets. Les lecteurs ne peuvent apporter aucune modification.        |