---
title: Utiliser Cloud Services (support étendu) (préversion)
description: Découvrir comment créer et déployer un service cloud Azure à l’aide d’Azure Resource Manager avec Visual Studio
author: ghogen
ms.service: cloud-services-extended-support
manager: jillfra
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: tutorial
ms.date: 10/5/2020
ms.author: ghogen
ms.openlocfilehash: b12f8beeee054e547a6b58750d8482d32ee14453
ms.sourcegitcommit: 3c8964a946e3b2343eaf8aba54dee41b89acc123
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98747255"
---
# <a name="create-and-deploy-a-azure-cloud-service-extended-support-using-visual-studio"></a>Créer et déployer un service cloud Azure (support étendu) à l’aide de Visual Studio

À compter de [Visual Studio 2019 version 16.9](https://visualstudio.microsoft.com/vs/preview/) (actuellement en préversion), vous pouvez travailler avec des services cloud à l’aide d’Azure Resource Manager (ARM), ce qui simplifie et modernise considérablement la maintenance et la gestion des ressources Azure. Cette option est activée par un nouveau service Azure appelé « Cloud Services (support étendu) ». Vous pouvez publier un service cloud existant dans Cloud Services (support étendu). Pour plus d’informations sur ce service Azure, consultez la [documentation de Cloud Services (support étendu)](overview.md).

> [!IMPORTANT]
> Cloud Services (support étendu) est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-feature-for-your-subscription"></a>Inscrire la fonctionnalité pour votre abonnement
Cloud Services (support étendu) est actuellement en préversion. Inscrivez la fonctionnalité pour votre abonnement comme suit :

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```
Pour plus d’informations, consultez [Prérequis pour le déploiement de Cloud Services (support étendu)](deploy-prerequisite.md)

## <a name="create-a-project"></a>Création d’un projet

Visual Studio fournit un modèle de projet qui vous permet de créer un service cloud Azure avec support étendu, nommé **service cloud Azure (support étendu)** . Un service cloud est un service Azure simple à usage général. Une fois le projet créé, Visual Studio vous permet de configurer, de déboguer et de déployer le service cloud dans Azure.

### <a name="to-create-an-azure-cloud-service-extended-support-project-in-visual-studio"></a>Pour créer un projet de service cloud Azure (support étendu) dans Visual Studio

Cette section vous guide dans le processus de création d’un projet de service cloud Azure dans Visual Studio avec un ou plusieurs rôles web.

1. Dans la fenêtre de démarrage, choisissez **Créer un projet**.

1. Dans la zone de recherche, tapez *Cloud*, puis choisissez **Service cloud Azure (support étendu)** .

   ![Nouveau service cloud Azure avec support étendu](./media/choose-project-template.png)

1. Nommez le projet et choisissez **Créer**.

   ![Nommer le projet](./media/configure-new-project.png)

1. Dans la boîte de dialogue **Nouveau service cloud Microsoft Azure**, sélectionnez les rôles que vous souhaitez ajouter et appuyez sur la touche de direction droite pour les ajouter à votre solution.

    ![Sélectionner les rôles d’un nouveau service cloud Azure](./media/choose-roles.png)

1. Pour renommer un rôle que vous avez ajouté, passez la souris sur le rôle dans la boîte de dialogue **Nouveau service cloud Microsoft Azure**, puis, dans le menu contextuel, sélectionnez **Renommer**. Vous pouvez également renommer un rôle dans votre solution (dans **l’Explorateur de solutions**) une fois qu’il a été ajouté.

    ![Renommer un rôle de service cloud Azure](./media/new-cloud-service-rename.png)

Le projet Azure Visual Studio est associé aux projets de rôle de la solution. Le projet comprend également le *fichier de définition de service* et le *fichier de configuration de service* :

- **Fichier de définition de service** : définit les paramètres d’exécution de votre application, notamment les rôles nécessaires, les points de terminaison et la taille de la machine virtuelle.
- **Fichier de configuration de service** : configure le nombre d’instances d’un rôle exécutées et les valeurs des paramètres définis pour un rôle.

Pour plus d’informations sur ces fichiers, consultez [Configurer des rôles pour un service cloud Azure avec Visual Studio](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service).

## <a name="publish-a-cloud-service"></a>Publier un service cloud

1. Créez ou ouvrez un projet de service cloud Azure dans Visual Studio.

1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis, dans le menu contextuel, sélectionnez **Publier**.

   ![page de connexion](./media/publish-step-1.png)

1. **Compte** : sélectionnez un compte ou **Ajouter un compte** dans la liste déroulante des comptes.

1. **Choisir votre abonnement** : choisissez l’abonnement à utiliser pour votre déploiement. L’abonnement que vous utilisez pour le déploiement de Cloud Services (support étendu) doit disposer des rôles Propriétaire ou Contributeur attribués par le biais du contrôle d’accès en fonction du rôle (RBAC). Si votre abonnement ne dispose d’aucun de ces rôles, consultez [Étapes pour ajouter une attribution de rôle](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-steps) pour en ajouter un avant de continuer.

1. Choisissez **Suivant** pour passer à la page **Paramètres**.

   ![Paramètres courants](./media/publish-settings.png)

1. **Service cloud** : dans la liste déroulante, sélectionnez un service cloud existant ou **Créer**, puis créez un service cloud. Le centre de données s’affiche entre parenthèses pour chaque service cloud. Il est recommandé que l’emplacement du centre de données du service cloud soit identique à celui du compte de stockage.

   Si vous choisissez de créer un service cloud, la boîte de dialogue **Créer un service cloud (support étendu)** s’affiche. Spécifiez l’emplacement et le groupe de ressources que vous voulez utiliser pour le service cloud.

   ![Créer un service cloud avec support étendu](./media/extended-support-dialog.png)

1. **Configuration de build** : sélectionnez **Déboguer** ou **Version finale**.

1. **Configuration de service** : sélectionnez **Cloud** ou **Local**.

1. **Compte de stockage** : sélectionnez le compte de stockage à utiliser pour ce déploiement, ou **Créer** pour créer un compte de stockage. La région s’affiche entre parenthèses pour chaque compte de stockage. Il est recommandé que l’emplacement du centre de données du compte de stockage soit identique à celui du service cloud (Paramètres communs).

   Le compte de stockage Azure stocke le package pour le déploiement de l'application. Une fois l'application déployée, le package est supprimé du compte de stockage.

1. **Coffre de clés** : spécifiez le coffre de clés qui contient les secrets de ce service cloud. Cette option est disponible si le Bureau à distance est activé ou si des certificats sont ajoutés à la configuration.

1. **Activer le Bureau à distance pour tous les rôles** : Sélectionnez cette option si vous souhaitez pouvoir vous connecter à distance au service. Vous êtes invité à spécifier des informations d’identification.

   ![Paramètres du bureau à distance](./media/remote-desktop-configuration.png)

1. Choisissez **Suivant** pour passer à la page **Paramètres de diagnostic**.

   ![Paramètres de diagnostic](./media/diagnostics-settings.png)

   Les diagnostics vous permettent de résoudre les problèmes liés à un service cloud Azure (ou à une machine virtuelle Azure). Pour en savoir plus sur les diagnostics, consultez [Configuration de Diagnostics pour les services cloud et les machines virtuelles Azure](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines). Pour plus d’informations sur Application Insights, consultez [Présentation d’Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview).

1. Cliquez sur **Suivant** pour accéder à la page **Résumé**.

   ![Résumé](./media/publish-summary.png)

1. **Profil cible** : vous pouvez choisir de créer un profil de publication à partir des paramètres que vous avez choisis. Par exemple, vous pouvez créer un profil pour un environnement de test et un autre pour la production. Pour enregistrer ce profil, choisissez l'icône **Enregistrer**. L'Assistant crée le profil et l'enregistre dans le projet Visual Studio. Pour modifier le nom du profil, ouvrez la liste **Profil cible**, puis sélectionnez **Gérer...**.

   > [!Note]
   > Le profil de publication s'affiche dans l'Explorateur de solutions dans Visual Studio, et les paramètres du profil sont écrits dans un fichier portant l'extension .azurePubxml. Les paramètres sont enregistrés en tant qu'attributs de balises XML.

1. Une fois que vous avez configuré tous les paramètres de déploiement de votre projet, sélectionnez **Publier** en bas de la boîte de dialogue. Vous pouvez superviser l’état du processus dans la fenêtre de sortie **Journal d’activité Azure** dans Visual Studio.

Félicitations ! Vous avez publié votre projet de service cloud à support étendu sur Azure. Pour effectuer une nouvelle publication avec les mêmes paramètres, vous pouvez réutiliser le profil de publication ou répéter ces étapes pour en créer un nouveau.

## <a name="clean-up-azure-resources"></a>Nettoyage des ressources Azure

Pour nettoyer les ressources Azure que vous avez créées en suivant ce tutoriel, accédez au [portail Azure](https://portal.azure.com), choisissez **Groupes de ressources**, recherchez et ouvrez le groupe de ressources que vous avez utilisé pour créer le service, puis choisissez **Supprimer le groupe de ressources**.

## <a name="next-steps"></a>Étapes suivantes

Configurez l’intégration continue (CI) à l’aide du bouton **Configurer** sur l’écran **Publier**. Pour plus d’informations, consultez la [documentation Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines).
