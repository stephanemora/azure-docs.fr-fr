---
title: Importer et publier votre première API dans Gestion des API Azure
description: Découvrez comment importer une API de la spécification OpenAPI dans Gestion des API Azure, et comment tester votre API dans le portail Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: apimpm
ms.openlocfilehash: 886063dcf886d79ac960814f20b3789e8e3b6839
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2020
ms.locfileid: "78163484"
---
# <a name="import-and-publish-your-first-api"></a>Importer et publier votre première API 

Ce tutoriel montre comment importer une API back-end de la spécification OpenAPI au format JSON dans Gestion des API Azure. Microsoft fournit l’API back-end et l’héberge sur Azure à l’adresse suivante : [https://conferenceapi.azurewebsites.net?format=json](https://conferenceapi.azurewebsites.net?format=json).

Une fois que vous avez importé l’API back-end dans Gestion des API, votre API du service Gestion des API devient une façade pour l’API back-end. Vous pouvez personnaliser la façade selon vos besoins dans Gestion des API sans toucher à l’API back-end. Pour plus d’informations, consultez [Transformer et protéger votre API](transform-api.md). 

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Importer une API dans Gestion des API
> * Tester l’API dans le portail Azure

![Nouvelle API](./media/api-management-import-and-publish/created-api.png)

## <a name="prerequisites"></a>Prérequis

- Comprendre la [terminologie relative au service Gestion des API Azure](api-management-terminology.md)
- [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importer et publier une API back-end

Cette section montre comment importer et publier une API back-end de la spécification OpenAPI.
 
1. Dans le volet de navigation gauche de votre instance du service Gestion des API, sélectionnez **API** dans la section **Gestion des API**.
1. Sélectionnez la vignette **OpenAPI**, puis sélectionnez **Complet** dans l’écran contextuel.
1. Dans l’écran **Create from OpenAPI specification** (Créer à partir de la spécification OpenAPI), utilisez les valeurs du tableau suivant pour créer votre API.
   
   Une étoile rouge à côté d’un champ du formulaire indique que le champ est obligatoire. Vous pouvez définir les valeurs de l’API au moment de la création, ou un peu plus tard en accédant à l’onglet **Paramètres**. 
   
   ![Création d'une API](./media/api-management-import-and-publish/create-api.png)
   
   |Paramètre|Valeur|Description|
   |-------|-----|-----------|
   |**Spécification OpenAPI**|*https:\//conferenceapi.azurewebsites.net?format=json*|Service qui implémente l’API. La gestion des API transmet les demandes à cette adresse.|
   |**Nom complet**|Une fois que vous avez entré l’URL de service précédente, le service Gestion des API remplit ce champ en fonction du contenu JSON.|Nom affiché dans le portail des développeurs.|
   |**Nom**|Une fois que vous avez entré l’URL de service précédente, le service Gestion des API remplit ce champ en fonction du contenu JSON.|Nom unique de l’API.|
   |**Description**|Une fois que vous avez entré l’URL de service précédente, le service Gestion des API remplit ce champ en fonction du contenu JSON.|Description facultative de l’API.|
   |**Modèle d’URL**|**HTTPS**|Protocoles utilisables pour accéder à l’API.|
   |**Suffixe de l’URL de l’API**|*conference*|Suffixe ajouté à l’URL de base du service Gestion des API. Le service Gestion des API distingue les API par leur suffixe. Ainsi, ce dernier doit être unique pour chaque API d’un éditeur donné.|
   |**Produits**|**Illimité**|Association d’une ou de plusieurs API. Chaque instance du service Gestion des API est fournie avec deux exemples de produits : **Starter** et **Unlimited**. Vous publiez une API en l’associant à un produit, **Unlimited** (Illimité) dans cet exemple.<br/>Vous pouvez inclure plusieurs API dans un produit et les proposer aux développeurs via le portail des développeurs. Pour ajouter cette API à un autre produit, tapez ou sélectionnez le nom du produit. Répétez cette étape pour ajouter l’API à plusieurs produits. Vous pouvez également ajouter des API aux produits plus tard à partir de la page **Paramètres**.<br/>Pour obtenir l’accès à l’API, les développeurs doivent s’abonner à un produit. Quand ils s’abonnent, ils obtiennent une clé d’abonnement valable pour toutes les API de ce produit. <br/>Si vous avez créé l’instance du service Gestion des API, vous êtes déjà administrateur. Vous êtes donc abonné à tous les produits de l’instance.|
   |**Balises**| |Étiquettes permettant d’organiser les API pour la recherche, le regroupement ou le filtrage.|
   |**Créer une version pour cette API ?**|Sélectionner ou désélectionner|Pour plus d’informations sur la gestion de versions, consultez [Publier plusieurs versions de votre API](api-management-get-started-publish-versions.md).|
   
   > [!NOTE]
   > Pour publier l’API, vous devez l’associer à un produit. Vous pouvez le faire à partir de la page **Paramètres**.
   
1. Sélectionnez **Create** (Créer).

Si vous rencontrez des problèmes au moment de l’importation d’une définition d’API, consultez la [liste des problèmes et restrictions connus](api-management-api-import-restrictions.md).

## <a name="test-the-new-api-in-the-azure-portal"></a>Tester la nouvelle API dans le Portail Azure

Vous pouvez appeler les opérations d’API directement à partir du portail Azure, qui offre un moyen pratique de voir et de tester les opérations.

1. Dans le volet de navigation gauche de votre instance du service Gestion des API, sélectionnez **API** dans la section **Gestion des API**, puis sélectionnez **Demo Conference API** (API de conférence de démonstration).
1. Sélectionnez l’onglet **Test**, puis sélectionnez **GetSpeakers**. La page affiche les **paramètres de requête** et les **en-têtes**, le cas échéant. La valeur de **Ocp-Apim-Subscription-Key** est indiquée automatiquement pour la clé d’abonnement associée à cette API.
1. Sélectionnez **Envoyer**.
   
   ![Tester le mappage d’API](./media/api-management-import-and-publish/01-import-first-api-01.png)
   
   Le serveur principal répond avec **200 OK** et certaines données.

## <a name="next-steps"> </a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Importer votre première API
> * Tester l’API dans le portail Azure

Passez au tutoriel suivant pour découvrir comment créer et publier un produit :

> [!div class="nextstepaction"]
> [Créer et publier un produit](api-management-howto-add-products.md)
