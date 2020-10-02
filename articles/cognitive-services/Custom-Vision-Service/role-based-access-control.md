---
title: Contrôle d’accès en fonction du rôle – Custom Vision
titleSuffix: Azure Cognitive Services
description: Cet article vous explique comment configurer le contrôle d’accès en fonction du rôle pour vos projets Custom Vision.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: fc536b3b0c7af113919f274fc4bdd5fad63cbd06
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2020
ms.locfileid: "90057110"
---
# <a name="role-based-access-control"></a>Contrôle d'accès en fonction d'un rôle

Custom Vision prend en charge le contrôle d’accès en fonction du rôle Azure (Azure RBAC), un système d’autorisation pour la gestion de l’accès individuel aux ressources Azure. À l’aide de RBAC, vous attribuez à différents membres d’équipe différents niveaux d’autorisations pour vos projets Custom Vision. Pour plus d’informations sur RBAC, consultez la [documentation Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/).

## <a name="add-role-assignment-to-custom-vision-resource"></a>Ajouter une attribution de rôle à une ressource Custom Vision

Azure RBAC peut être assigné à une ressource Custom Vision. Pour accorder l’accès à une ressource Azure, vous ajoutez une attribution de rôle.
1. Dans le [portail Azure](https://ms.portal.azure.com/), sélectionnez **Tous les services**. 
1. Ensuite, sélectionnez **Cognitive Services** et accédez à votre ressource d’entraînement Custom Vision.
   > [!NOTE]
   > Vous pouvez également configurer RBAC pour des groupes de ressources, des abonnements ou des groupes d’administration complets. Pour ce faire, sélectionnez le niveau d’étendue souhaité, puis accédez à l’élément de votre choix (par exemple, en sélectionnant **Groupes de ressources** puis en cliquant sur le groupe de ressources souhaité).
1. Sélectionnez **Contrôle d’accès (IAM)** dans le menu de navigation de gauche.
1. Sélectionnez l’onglet **Attributions de rôles** afin d’afficher les attributions de rôles pour cette étendue.
1. Sélectionnez **Ajouter** -> **Ajouter une attribution de rôle**.
1. Dans la liste déroulante **Rôle**, sélectionnez un rôle à ajouter.
1. Dans la liste **Sélectionner**, sélectionnez un utilisateur, un groupe, un principal de service ou une identité gérée. Si vous ne voyez pas le principal de sécurité dans la liste, vous pouvez saisir du texte dans la zone Sélectionner pour rechercher des noms complets, des adresses e-mail et des identificateurs d’objet dans l’annuaire.
1. Sélectionnez **Enregistrer** pour attribuer le rôle.

Au bout de quelques minutes, le rôle sélectionné est attribué à la cible dans l’étendue sélectionnée.

## <a name="custom-vision-role-types"></a>Types de rôles Custom Vision

Utilisez le tableau suivant pour déterminer les besoins d’accès à vos ressources Custom Vision.

|Role  |Autorisations  |
|---------|---------|
|`Cognitive Service Custom Vision Contributor`     | Accès complet aux projets, y compris la possibilité de créer, de modifier et de supprimer un projet.        |
|`Cognitive Service Custom Vision Trainer`     | Accès complet, à l’exception de la possibilité de créer et de supprimer un projet. Les entraîneurs peuvent afficher et modifier des projets, ainsi qu’entraîner, publier, dépublier ou exporter les modèles.        |
|`Cognitive Service Custom Vision Labeler`     | Possibilité de télécharger, de modifier ou de supprimer des images d’entraînement, ainsi que de créer, ajouter, supprimer ou effacer des étiquettes. Les étiqueteurs peuvent consulter les projets, mais ne peuvent pas mettre à jour autre chose que des images d’entraînement et des étiquettes.         |
|`Cognitive Service Custom Vision Deployment`     | Possibilité de publier, d’annuler la publication ou d’exporter les modèles. Les responsables de déploiement peuvent consulter les projets, mais ne peuvent pas mettre à jour un projet, des images d’entraînement ni des étiquettes.        |
|`Cognitive Service Custom Vision Reader`     | Possibilité de consulter les projets. Les lecteurs ne peuvent apporter aucune modification.        |
