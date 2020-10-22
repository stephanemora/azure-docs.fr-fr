---
title: Se connecter au portail LUIS
description: Si vous êtes un nouvel utilisateur qui se connecte au portail LUIS, l’expérience de connexion varie légèrement en fonction de votre compte d’utilisateur actuel.
services: cognitive-services
ms.custom: ''
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/08/2020
ms.topic: how-to
ms.author: a-sakand
author: skandil
ms.openlocfilehash: d801971ca62c416c66608b40aab3e8052fe941a1
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91931408"
---
# <a name="sign-in-to-luis-portal"></a>Se connecter au portail LUIS

Si vous êtes un nouvel utilisateur qui se connecte au portail LUIS, l’expérience de connexion varie légèrement en fonction de votre compte d’utilisateur actuel :
  * Associé à un abonnement Azure
  * Non associé à un abonnement Azure

## <a name="determine-account-type"></a>Déterminer le type de compte

Lorsque vous vous connectez pour la première fois au portail LUIS, utilisez les indicateurs visuels suivants pour déterminer le type de compte.

### <a name="account-without-azure-subscription"></a>Compte sans abonnement Azure

Un compte qui n’est pas associé à un abonnement Azure présente l’icône Azure dans la barre de navigation en haut à droite. Une fois que vous migrez vers le type de compte associé, l’icône ne s’affiche plus.

:::image type="content" source="media/sign-in/sign-in-with-account-without-azure-subscription.png" alt-text="Capture d’écran partielle de la barre de navigation LUIS avec l’icône Azure.":::

### <a name="account-with-azure-subscription"></a>Un compte avec un abonnement Azure

Un compte associé à un abonnement Azure vous permet de sélectionner votre abonnement et votre ressource à utiliser.

:::image type="content" source="media/sign-in/resource-selection.png" alt-text="Capture d’écran partielle de la barre de navigation LUIS avec l’icône Azure.":::

## <a name="sign-in-with-account-associated-with-an-azure-subscription"></a>Se connecter avec un compte associé à un abonnement Azure

1. Connectez-vous au [portail LUIS](https://www.luis.ai) et acceptez les conditions d’utilisation.

1. Vous avez deux options pour vous inscrire :

    * Continuer à utiliser une ressource Azure, qui est la méthode recommandée et sera bientôt la seule disponible. Cette méthode vous permet de lier votre compte LUIS à une ressource de création Azure lors de l’inscription en choisissant une ressource existante dans votre abonnement ou en créant une nouvelle ressource. Cela équivaut à vous inscrire à la migration sans avoir à passer par le [processus de migration](luis-migration-authoring.md#what-is-migration) plus tard. Tous les utilisateurs seront amenés à migrer d’ici le 2 novembre 2020.

    * Continuer à utiliser la clé de démarrage ou d’essai. Cette méthode vous permet de vous connecter à LUIS avec la ressource de démarrage ou d’essai fournie sans avoir à créer de ressources. Si vous choisissez cette méthode, vous devrez plus tard [migrer votre compte](luis-migration-authoring.md#migration-steps) et lier vos applications à une ressource de création. C’est pourquoi il est recommandé de choisir la méthode où vous poursuivez avec votre ressource Azure.

    [En savoir plus sur les clés de création et de démarrage](luis-how-to-azure-subscription.md#luis-resources). Les deux ressources vous donnent 1 million transactions de création gratuites et 1000 transactions de point de terminaison de prédiction.

    :::image type="content" source="media/sign-in/signup-landing-page.png" alt-text="Capture d’écran partielle de la barre de navigation LUIS avec l’icône Azure.":::

1. Utiliser une ressource de création existante

    :::image type="content" source="media/sign-in/signup-choose-resource.png" alt-text="Capture d’écran partielle de la barre de navigation LUIS avec l’icône Azure.":::

    Si vous avez déjà utilisé LUIS pour créer des ressources dans votre abonnement et que vous en associez une à votre compte LUIS pendant la connexion, choisissez l’option **Utiliser une ressource de création existante** et fournissez les informations suivantes :

    * **Tenant** (Locataire) : locataire auquel votre abonnement Azure est associé. Vous ne serez pas en mesure de basculer les locataires à partir de la fenêtre existante. Vous pouvez modifier des locataires en sélectionnant l’avatar à droite qui contient vos initiales dans la barre supérieure.
    * **Subscription name** (Nom de l’abonnement) : abonnement qui sera associé à la ressource. Si votre locataire possède plusieurs abonnements, sélectionnez celui que vous souhaitez dans la liste déroulante.
    * **Nom de la ressource** : la ressource de création à laquelle vous souhaitez associer le compte.

    > [!Note]
    > Si la ressource de création que vous recherchez est grisée dans la liste déroulante, cela signifie que vous vous êtes connecté à un autre portail régional. [Comprendre le concept des portails régionaux](luis-reference-regions.md#luis-authoring-regions).

1. Créer une ressource de création

    :::image type="content" source="media/sign-in/signup-create-resource.png" alt-text="Capture d’écran partielle de la barre de navigation LUIS avec l’icône Azure.":::

    Quand vous **créez une ressource de création**, fournissez les informations suivantes :

    * **Tenant** (Locataire) : locataire auquel votre abonnement Azure est associé. Vous ne serez pas en mesure de basculer les locataires à partir de la fenêtre existante. Vous pouvez modifier des locataires en sélectionnant l’avatar à droite qui contient vos initiales dans la barre supérieure.
    * **Resource name** (Nom de la ressource) : nom personnalisé que vous choisissez, utilisé comme élément de l’URL pour vos transactions de création. Le nom de votre ressource doit contenir uniquement des caractères alphanumériques et « - », et ne peut pas se commencer ou se terminer par « - ». Si d’autres symboles sont inclus dans le nom, la création d’une ressource échoue.
    * **Subscription name** (Nom de l’abonnement) : abonnement qui sera associé à la ressource. Si votre locataire possède plusieurs abonnements, sélectionnez celui que vous souhaitez dans la liste déroulante.
    * **Resource group** (Groupe de ressources) : nom de groupe de ressources personnalisé que vous choisissez dans votre abonnement. Les groupes de ressources vous permettent de regrouper des ressources Azure pour l’accès et la gestion. Si vous n’avez pas de groupe de ressources dans votre abonnement, vous ne serez pas autorisé à en créer un dans le portail LUIS. Accédez au [Portail Azure](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup) pour en créer un, puis cliquez sur LUIS pour continuer le processus de connexion.

1. Une fois que vous avez choisi votre méthode, le message « Votre compte a été migré avec succès » peut prendre quelques secondes avant de s’afficher. Terminez en sélectionnant **Continuer**.

    :::image type="content" source="media/sign-in/signup-confirm-2.png" alt-text="Capture d’écran partielle de la barre de navigation LUIS avec l’icône Azure.":::

    > [!Note]
    > Si vous avez un abonnement et au moins une ressource de création dans la région dans laquelle vous vous inscrivez sur le portail, vous pouvez vous connecter automatiquement à LUIS, migré et associé à une ressource, sans avoir à choisir une méthode d’accès.


## <a name="sign-in-with-user-account-not-associated-with-an-azure-subscription"></a>Se connecter avec un compte utilisateur non associé à un abonnement Azure

1. Connectez-vous au [portail LUIS](https://www.luis.ai) et veillez à accepter les conditions d’utilisation.

1. Terminez en sélectionnant **Continuer**. Vous vous connecterez automatiquement avec une clé d’évaluation/de démarrage. Cela signifie que vous devrez plus tard [migrer votre compte](luis-migration-authoring.md#migration-steps) et lier vos applications à une ressource de création. Pour passer au processus de migration, vous devez vous inscrire à une [Évaluation gratuite d’Azure](https://azure.microsoft.com/free/).

    :::image type="content" source="media/sign-in/signup-no-subscription.png" alt-text="Capture d’écran partielle de la barre de navigation LUIS avec l’icône Azure.":::

## <a name="troubleshooting"></a>Dépannage

* Si vous créez une ressource de création à partir du Portail Azure dans une autre région que le portail auquel vous vous connectez, la ressource de création est grisée.
* Lors de la création d’une ressource, assurez-vous que le nom de la ressource ne contient que des caractères alphanumériques, '-', et ne commence ou ne se termine pas par '-'. Dans le cas contraire, elle échoue.
* Assurez-vous que vous disposez des [autorisations appropriées sur votre abonnement pour créer une ressource Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles). Si vous ne disposez pas des autorisations appropriées, contactez l’administrateur de votre abonnement pour obtenir des autorisations suffisantes.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [démarrer une nouvelle application](luis-how-to-start-new-app.md)
