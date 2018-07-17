---
title: Créer un pipeline CI/CD pour Java avec Azure DevOps Project | Guide de démarrage rapide
description: DevOps Project facilite la prise en main d’Azure. Ce composant vous aide à lancer une application sur le service Azure de votre choix en quelques étapes rapides.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: douge
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 7bff77e5695cf0a9b33f512a0b49b3715b4ba047
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37966929"
---
# <a name="create-a-cicd-pipeline-for-java-with-the-azure-devops-project"></a>Créer un pipeline CI/CD pour Java avec Azure DevOps Project

Azure DevOps Project présente une expérience simplifiée qui crée des ressources Azure et configure un pipeline d’intégration continue (CI) et de livraison continue (CD) pour votre application Java dans Visual Studio Team Services (VSTS), la solution DevOps de Microsoft pour Azure.  

Si vous ne disposez pas d’un abonnement Azure, vous pouvez en obtenir un gratuitement via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Azure DevOps Project crée un pipeline CI/CD dans VSTS.  Vous pouvez créer un **compte VSTS** gratuit ou utiliser un **compte existant**.  DevOps Project crée également des **ressources Azure** dans **l’abonnement Azure** de votre choix.

1. Connectez-vous au [portail Microsoft Azure](https://portal.azure.com).

1. Sélectionnez l’icône **Créer une ressource** dans la barre de navigation gauche, puis recherchez **Projet DevOps**.  Cliquez sur **Créer**.

    ![Démarrage de la configuration de la livraison continue](_img/azure-devops-project-java/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Sélectionner un exemple d’application et le service Azure

1. Sélectionnez l’exemple d’application **Java**.  Les exemples Java offrent le choix entre plusieurs infrastructures d'application.

1. **Spring** est l’exemple d’infrastructure par défaut. Laissez le paramètre par défaut, puis choisissez **Suivant**.  

1. La cible de déploiement par défaut est **Web App pour conteneurs**.  L’infrastructure d’application, que vous avez choisie dans les étapes précédentes, détermine le type de cible de déploiement de service Azure disponible ici.  Laissez le paramètre par défaut, puis choisissez **Suivant**.
 
## <a name="configure-vsts-and-an-azure-subscription"></a>Configurer VSTS et un abonnement Azure 

1. Créez un **nouveau** compte VSTS ou choisissez un compte **existant**.  Choisissez un **nom** pour votre projet VSTS.  Sélectionnez votre **abonnement Azure**, **l’emplacement**, puis le **nom** de votre application.  Une fois que vous avez terminé, sélectionnez **Terminé**.

    ![Entrer les informations VSTS](_img/azure-devops-project-java/vstsazureinfo.png)

1. En quelques minutes, le **tableau de bord du projet** est chargé dans le portail Azure.  Un exemple d’application est configuré dans un référentiel dans votre compte VSTS, une build est exécutée, et votre application est déployée dans Azure.  Ce tableau de bord permet de visualiser votre **référentiel de code**, votre **pipeline VSTS CI/CD** et votre **application dans Azure**.  Sur le côté droit du tableau de bord, sélectionnez **Parcourir** pour afficher votre application en cours d’exécution.

    ![Vue du tableau de bord](_img/azure-devops-project-java/dashboardnopreview.png) 
    
Azure DevOps Project configure automatiquement une build CI ainsi qu’un déclencheur de mise en production.  Vous êtes maintenant prêt à collaborer avec une équipe sur une application Java avec un processus CI/CD qui déploie automatiquement votre dernier travail sur votre site web.

## <a name="commit-code-changes-and-execute-cicd"></a>Valider les modifications de code et exécuter CI/CD

Azure DevOps Project a créé un référentiel Git dans votre compte VSTS ou GitHub.  Suivez les étapes ci-dessous pour afficher le référentiel et modifier le code dans votre application.

1. Sur la gauche du tableau de bord DevOps Project, sélectionnez le lien correspondant à votre branche **maîtresse**.  Ce lien ouvre une vue sur le référentiel Git récemment créé.

1. Pour afficher l’URL du clone du référentiel, sélectionnez **Clone** en haut à droite du navigateur. Vous pouvez cloner votre référentiel Git dans votre environnement de développement intégré favori.  Dans les prochaines étapes, vous allez utiliser le navigateur web pour effectuer des modifications de code directement dans la branche maîtresse et les valider.

1. Sur la gauche du navigateur, accédez au fichier **src/main/webapp/index.html**.

1. Sélectionnez **Modifier** et modifiez le texte.  Par exemple, modifiez l’une des balises div.

1. Choisissez **Valider** et enregistrez vos modifications.

1. Dans le navigateur, accédez au **tableau de bord Azure DevOps Project**.  Vous devez maintenant voir qu’une build est en cours.  Les modifications que vous venez d’effectuer sont automatiquement générées et déployées via un pipeline VSTS CI/CD.

## <a name="examine-the-vsts-cicd-pipeline"></a>Examiner le pipeline VSTS CI/CD

Azure DevOps Project a configuré automatiquement un pipeline VSTS CI/CD complet dans votre compte VSTS.  Explorez et personnalisez le pipeline selon vos besoins.  Suivez les étapes ci-dessous pour vous familiariser avec les définitions de mise en production et de build VSTS.

1. Sélectionnez **Pipelines de build** en **haut** du tableau de bord Azure DevOps Project.  Ce lien ouvre un onglet de navigateur ainsi que la définition de build VSTS pour votre nouveau projet.

1. Déplacez le curseur de souris vers la droite de la définition de build en regard du champ **État**. Sélectionnez les **points de suspension** affichés.  Cette action ouvre un menu où vous pouvez démarrer plusieurs activités, telles que mettre une nouvelle build en file d’attente, suspendre une build, et modifier une définition de build.

1. Sélectionnez **Modifier**.

1. Dans cette vue, **examinez les différentes tâches** de votre définition de build.  La build exécute différentes tâches, telles que la récupération (fetch) de sources à partir du référentiel Git, la restauration de dépendances et la publication de sorties utilisées pour les déploiements.

1. En haut de la définition de build, sélectionnez le **nom de la définition de build**.

1. Modifiez le **nom** de votre définition de build pour le remplacer par un nom plus descriptif.  Sélectionnez **Enregistrer et mettre en file d’attente**, puis **Enregistrer**.

1. Sous le nom de votre définition de build, sélectionnez **Historique**.  Vous pouvez observer une piste d’audit des modifications que vous avez apportées récemment à la build.  VSTS suit les modifications apportées à la définition de build et vous permet de comparer les versions.

1. Sélectionnez **Déclencheurs**.  Azure DevOps Project a créé automatiquement un déclencheur d’intégration continue, et chaque validation dans le référentiel démarre une nouvelle build.  Vous pouvez éventuellement choisir d’inclure ou d’exclure des branches dans le processus d’intégration continue.

1. Sélectionnez **Rétention**.  Selon votre scénario, vous pouvez spécifier des stratégies pour conserver ou supprimer un certain nombre de builds.

1. Sélectionnez **Build et mise en production**, puis **Versions**.  Azure DevOps Project a créé une définition de mise en production VSTS pour gérer les déploiements dans Azure.

1. Sur le côté gauche du navigateur, sélectionnez les **points de suspension** en regard de votre définition de mise en production, puis **Modifier**.

1. La définition de mise en production contient un **pipeline**, qui définit le processus de mise en production.  Sous **Artefacts**, sélectionnez **Déposer**.  La définition de build que vous avez examinée dans les étapes précédentes génère la sortie utilisée pour l’artefact. 

1. À droite de l’icône **Déposer**, sélectionnez le **déclencheur de déploiement continu**.  Cette définition de mise en production possède un déclencheur CD actif, qui exécute un déploiement à chaque fois qu’un nouvel artefact de build est disponible.  Si vous le souhaitez, vous pouvez désactiver le déclencheur afin que vos déploiements nécessitent une exécution manuelle. 

1. À gauche du navigateur, sélectionnez **Tâches**.  Il s’agit des activités effectuées par votre processus de déploiement.  Dans cet exemple, une tâche a été créée pour déployer un **service Azure App**.

1. À droite du navigateur, sélectionnez **Afficher les mises en production**.  Cette vue affiche un historique des mises en production.

1. Sélectionnez les **points de suspension** situés en regard de vos mises en production, puis **Ouvrir**.  Il y a plusieurs menus à explorer dans cette vue, comme un résumé des mises en production, les éléments de travail associés et les tests.

1. Sélectionnez **Validations**.  Cette vue présente les validations de code associées au déploiement spécifique. 

1. Sélectionnez **Journaux**.  Les journaux contiennent des informations utiles sur le processus de déploiement.  Ils peuvent être affichés pendant et après les déploiements.

## <a name="clean-up-resources"></a>Supprimer les ressources

Lorsque vous n’en avez plus besoin, vous pouvez supprimer le service Azure App et les ressources associées créées dans ce guide de démarrage rapide à l’aide de la fonctionnalité **Supprimer** du tableau de bord Azure DevOps Project.

## <a name="next-steps"></a>Étapes suivantes

Lorsque vous avez configuré votre processus CI/CD dans ce guide de démarrage rapide, des définitions de mise en production et de build ont été automatiquement créées dans votre projet VSTS. Vous pouvez modifier ces définitions afin qu’elles répondent aux besoins de votre équipe. Pour en savoir plus, consultez ce didacticiel :

> [!div class="nextstepaction"]
> [Personnaliser le processus CD](https://docs.microsoft.com/vsts/pipelines/release/define-multistage-release-process?view=vsts)
