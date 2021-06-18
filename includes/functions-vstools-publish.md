---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/30/2020
ms.author: glenga
ms.openlocfilehash: 9cbab70d37c689967c7129b3205a9194e9de9ade
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111351032"
---
1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet et sélectionnez **Publier** puis, dans **Cible**, sélectionnez **Azure**, puis **Suivant**.

1. Pour **Cible spécifique**, choisissez **Application de fonction Azure (Windows)** , qui crée une application de fonction s’exécutant sur Windows.

1. Dans **Instance de la fonction**, choisissez **Créer une application Azure Function...** 

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-new-resource.png" alt-text="Créer une instance d’application de fonction":::

1. Créez une instance en utilisant les valeurs spécifiées dans le tableau suivant :

    | Paramètre      | Value  | Description                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nom** | Nom globalement unique | Nom qui identifie uniquement votre nouvelle application de fonction. Acceptez ce nom ou entrez un nouveau nom. Les caractères valides sont `a-z`, `0-9` et `-`. |
    | **Abonnement** | Votre abonnement | Sélectionnez l’abonnement Azure à utiliser. Acceptez cet abonnement ou sélectionnez-en un nouveau dans la liste déroulante. |
    | **[Groupe de ressources](../articles/azure-resource-manager/management/overview.md)** | Nom de votre groupe de ressources |  Groupe de ressources dans lequel créer votre application de fonction. Sélectionnez un groupe de ressources existant dans la liste déroulante, ou choisissez **Créer** pour créer un groupe de ressources.|
    | **[Type de plan](../articles/azure-functions/functions-scale.md)** | Consommation | Quand vous publiez votre projet dans une application de fonction qui s’exécute dans un [Plan Consommation](../articles/azure-functions/consumption-plan.md), vous payez uniquement pour les exécutions de votre application de fonction. D’autres plans d’hébergement occasionnent des coûts plus élevés. |
    | **Lieu** | Emplacement du service d’application | Choisissez un **Emplacement** dans une [région](https://azure.microsoft.com/regions/) proche de chez vous, ou proche d’autres services auxquels vos fonctions ont accès. |
    | **[Stockage Azure](../articles/azure-functions/storage-considerations.md)** | Compte de stockage à usage général | Le runtime Functions exige un compte Stockage Azure. Sélectionnez **Nouveau** pour configurer un compte de stockage universel. Vous pouvez également choisir un compte existant qui répond aux [exigences relatives aux comptes de stockage](../articles/azure-functions/storage-considerations.md#storage-account-requirements).  |

    ![Boîte de dialogue Créer App Service](./media/functions-vstools-publish/functions-visual-studio-publish.png)

1. Sélectionnez **Créer** pour créer une application de fonction et les ressources associées dans Azure. L’état de la création des ressources est affiché en bas à gauche de la fenêtre. 

1. De retour dans **Instance de fonction**, vérifiez que **Exécuter à partir du fichier de package** est coché. Votre application de fonction est déployée en utilisant [Zip Deploy](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) avec le mode [Exécuter à partir du fichier de package](../articles/azure-functions/run-functions-from-deployment-package.md) activé. Il s’agit de la méthode de déploiement recommandée pour votre projet fonctions, car elle offre de meilleures performances. 

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile-step-4.png" alt-text="Terminer la création du profil":::

1. Sélectionnez **Terminer**, puis dans la page Publier, sélectionnez **Publier** pour déployer le package contenant vos fichiers projet dans votre nouvelle application de fonction dans Azure. 

    Une fois le déploiement terminé, l’URL racine de l’application de fonction dans Azure est affichée sous l’onglet **Publier**. 
    
1.  Sous l’onglet Publier, choisissez **Gérer dans Cloud Explorer**. La nouvelle ressource Azure d’application de fonction est alors ouverte dans Cloud Explorer. 
    
    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-complete.png" alt-text="Publier un message de réussite":::
    
    Cloud Explorer vous permet d’utiliser Visual Studio pour afficher le contenu du site, démarrer et arrêter l’application de fonction, et accéder directement aux ressources de l’application de fonction sur Azure et dans le portail Azure. 
