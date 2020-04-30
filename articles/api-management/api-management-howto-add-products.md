---
title: Création et publication d'un produit dans Gestion des API Azure
description: Apprenez à créer et à publier des produits dans Gestion des API Azure.
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
ms.date: 08/10/2018
ms.author: apimpm
ms.openlocfilehash: 686d6e2ff01fd4d8283350ab8097e1f73c914af0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81260994"
---
# <a name="create-and-publish-a-product"></a>Créer et publier un produit  

Dans Gestion des API Azure, un produit contient une ou plusieurs API, ainsi qu’un quota et des conditions d’utilisation. Une fois le produit publié, les développeurs peuvent s'y abonner et commencer à utiliser ses API.  

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer et publier un produit
> * Ajouter une API au produit

![Ajouter un didacticiel du produit](media/api-management-howto-add-products/added-product.png)

## <a name="prerequisites"></a>Prérequis

+ Apprenez la [terminologie relative à Gestion des API Azure](api-management-terminology.md).
+ Suivez ce guide de démarrage rapide : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).
+ Effectuez également toutes les étapes du tutoriel suivant : [Importer et publier votre première API](import-and-publish.md).

## <a name="create-and-publish-a-product"></a>Créer et publier un produit

![Add product](media/api-management-howto-add-products/02-create-publish-product-01.png)

1. Cliquez sur **Produits** dans le menu à gauche pour afficher la page **Produits**.
2. Cliquez sur **+ Ajouter**.

    Quand vous ajoutez un produit, vous devez fournir les informations suivantes : 

    | Nom                     | Description                                                                                                                                                                                                                                                                                                             |
    |--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Nom complet             | Le nom qui doit apparaître dans le **portail des développeurs**.                                                                                                                                                                                                                                                        |
    | Nom                     | Nom descriptif du produit.                                                                                                                                                                                                                                                                                      |
    | Description              | Le champ **Description** vous permet d’ajouter des informations détaillées sur le produit, comme son objectif, les API auxquelles il permet l’accès, ainsi que d’autres informations utiles.                                                                                                                                               |
    | State                    | Appuyez sur **Publié** si vous souhaitez publier le produit. Avant de pouvoir appeler les API dans un produit, ce produit doit être publié. Par défaut, les nouveaux produits ne sont pas publiés et ne sont visibles que pour les utilisateurs du groupe **Administrateurs** .                                                                                      |
    | Nécessite un abonnement    | Cochez **Require subscription** (Demander un abonnement) si un utilisateur doit s’abonner pour pouvoir utiliser le produit.                                                                                                                                                                                                                                   |
    | Nécessite une approbation        | Cochez **Demander une approbation** si vous souhaitez qu’un administrateur révise et accepte ou refuse les tentatives d’abonnement à ce produit. Si la case n’est pas cochée, les tentatives d’abonnement sont automatiquement approuvées.                                                                                                                         |
    | Limite du nombre d’abonnements | Pour limiter le nombre de plusieurs abonnements simultanés, entrez la limite d’abonnements.                                                                                                                                                                                                                                |
    | Conditions légales              | Vous pouvez inclure les conditions d’utilisation que les abonnés doivent accepter pour pouvoir utiliser le produit.                                                                                                                                                                                                             |
    | API                     | Les produits sont des associations d’une ou de plusieurs API. Vous pouvez inclure un certain nombre d’API et les proposer aux développeurs dans le portail des développeurs. <br/> Vous pouvez ajouter une API existante pendant la création du produit. Vous pouvez ajouter une API au produit ultérieurement, soit à partir de la page **Paramètres** du produit, soit pendant la création d’une API. |

3. Cliquez sur **Créer** pour créer le produit.

### <a name="add-more-configurations"></a>Ajouter des configurations

Vous pouvez poursuivre la configuration du produit après l’avoir enregistré en choisissant l’onglet **Paramètres**. 

Afficher/ajouter des abonnés au produit à partir de l’onglet **Abonnements**.

Définissez la visibilité d’un produit pour les développeurs ou invités à partir de l’onglet **Contrôle d’accès**.

## <a name="add-apis-to-a-product"></a><a name="add-apis"> </a>Ajouter des API à un produit

Les produits sont des associations d’une ou de plusieurs API. Vous pouvez inclure un certain nombre d’API et les proposer aux développeurs dans le portail des développeurs. Vous pouvez ajouter une API existante pendant la création du produit. Vous pouvez ajouter une API au produit ultérieurement, soit à partir de la page **Paramètres** des produits, soit pendant la création d’une API.

Les développeurs doivent s’abonner à un produit pour obtenir l’accès à l’API. Quand ils s’abonnent à un produit, ils obtiennent une clé d’abonnement qui est valable pour toutes les API de ce produit. Si vous avez créé l’instance APIM, vous êtes abonné à chaque produit par défaut, car vous êtes déjà administrateur.

### <a name="add-an-api-to-an-existing-product"></a>Ajouter une API à un produit existant

![Ajouter une API de produit](media/api-management-howto-add-products/02-create-publish-product-02.png)

1. Dans l’onglet **Produits**, sélectionnez un produit.
2. Accédez à l’onglet **API**.
3. Cliquez sur **+ Ajouter**.
4. Choisissez une API et cliquez sur **Sélectionner**.

> [!TIP]
> Vous pouvez créer ou mettre à jour l’abonnement de l’utilisateur à un *produit* avec les clés d’abonnement personnalisées [via l’API REST](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/subscription/createorupdate) ou une commande PowerShell.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer et publier un produit
> * Ajouter une API au produit

Passez au tutoriel suivant :

> [!div class="nextstepaction"]
> [Créer une API vide et simuler des réponses de l’API](mock-api-responses.md)
