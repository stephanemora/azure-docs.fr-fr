---
title: Déploiement continu pour Azure Functions | Microsoft Docs
description: Utilisez les fonctionnalités de déploiement continu d’Azure App Service pour publier vos fonctions.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/25/2016
ms.author: glenga
ms.openlocfilehash: cb3f3ad3bb7b42429654ea4bf9b49f7e230db1da
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943883"
---
# <a name="continuous-deployment-for-azure-functions"></a>Déploiement continu pour Azure Functions
Azure Functions rend plus facile à déployer votre application de fonction à l’aide de l’intégration continue. Functions s’intègre avec les référentiels de code principal et les sources de déploiement. Cette intégration permet un flux de travail où le code de la fonction mises à jour effectuée via un de ces services qui déclenchent le déploiement vers Azure. Si vous débutez avec Azure Functions, commencez par le [vue d’ensemble d’Azure Functions](functions-overview.md).

Déploiement continu est une option intéressante pour les projets dans laquelle vous intégrez plusieurs et les contributions de fréquentes. Il vous permet également de conserver le contrôle de source sur votre code de fonction. Azure Functions prend en charge les sources de déploiement suivantes :

* [Bitbucket](https://bitbucket.org/)
* [Dropbox](https://www.dropbox.com/)
* Référentiel externe (Git ou Mercurial)
* [Référentiel Git local](../app-service/deploy-local-git.md)
* [GitHub](https://github.com)
* [OneDrive](https://onedrive.live.com/)
* [Azure DevOps](https://azure.microsoft.com/services/devops/)

Les déploiements sont configurés au cas par cas, selon les Function Apps. Une fois le déploiement continu activé, l’accès au code de fonction dans le portail est défini sur *lecture seule*.

## <a name="requirements-for-continuous-deployment"></a>Configuration requise pour le déploiement continu

Avant de configurer un déploiement continu, vous devez disposer de votre source de déploiement configuré et votre code de fonction dans la source du déploiement. Dans un déploiement d’application de fonction, chaque fonction est dans un sous-répertoire nommé, où le nom du répertoire est le nom de la fonction.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Pour pouvoir déployer à partir d’Azure DevOps, vous devez commencer par lier votre organisation Azure DevOps avec votre abonnement Azure. Pour plus d’informations, voir [Configurer la facturation pour votre organisation Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/billing/set-up-billing-for-your-organization-vs?view=vsts#set-up-billing-via-the-azure-portal).

## <a name="set-up-continuous-deployment"></a>Configurer un déploiement continu
Utilisez cette procédure pour configurer le déploiement continu d’une Function App existante. Les étapes suivantes présentent l’intégration avec un référentiel GitHub, mais des étapes similaires s’appliquent à Azure DevOps ou à d’autres services de déploiement.

1. Dans votre function app dans le [Azure portal](https://portal.azure.com), sélectionnez **fonctionnalités de la plateforme** > **options de déploiement**. 
   
    ![Sélections pour l’ouverture des options de déploiement](./media/functions-continuous-deployment/setup-deployment.png)
 
1. Sur le **déploiements** panneau, sélectionnez **le programme d’installation**.
 
    ![Panneau déploiements](./media/functions-continuous-deployment/setup-deployment-1.png)
   
1. Sur le **source de déploiement** panneau, sélectionnez **choisir une source**. Renseignez les informations de votre source de déploiement choisi, puis sélectionnez **OK**.
   
    ![Choix d’une source de déploiement](./media/functions-continuous-deployment/choose-deployment-source.png)

Après avoir défini un déploiement continu, toutes les modifications de fichier dans votre source de déploiement sont copiées vers l’application de fonction et un déploiement complet de site est déclenché. Le site est redéployé lorsque les fichiers de la source sont mis à jour.

## <a name="deployment-scenarios"></a>Scénarios de déploiement

Scénarios de déploiement classiques incluent la création d’un déploiement intermédiaire et le déplacement des fonctions existantes pour le déploiement continu.

<a name="staging"></a>
### <a name="create-a-staging-deployment"></a>Créer un déploiement intermédiaire

Applications de fonction ne prend encore en charge les emplacements de déploiement. Mais vous pouvez toujours gérer les déploiements intermédiaire et de production distincts à l’aide de l’intégration continue.

Voici à quoi ressemble généralement le processus pour configurer et travailler avec un déploiement intermédiaire :

1. Créer deux applications de fonction dans votre abonnement : un pour le code de production et un pour l’environnement intermédiaire. 

1. Créez une source de déploiement, si vous n’en avez pas déjà. Cet exemple utilise [GitHub].

1. Pour votre Function App de production, effectuez les étapes décrites ci-dessus dans la section [Configurer un déploiement continu](#set-up-continuous-deployment), puis définissez la branche de déploiement sur la branche principale de votre référentiel GitHub.
   
    ![Sélections pour choisir une branche de déploiement](./media/functions-continuous-deployment/choose-deployment-branch.png)

1. Répétez l’étape 3 pour l’application de fonction intermédiaire, mais sélectionnez la branche intermédiaire au lieu de cela dans votre référentiel GitHub. Si votre source de déploiement ne prend pas en charge la création de branches, utilisez un autre dossier.
    
1. Mettre à jour votre code dans la branche ou un dossier intermédiaire et vérifiez que le déploiement intermédiaire reflète ces modifications.

1. Après vérification, fusionnez les modifications de la branche intermédiaire dans la branche principale. Cette opération déclenche le déploiement de la Function App de production. Si votre source de déploiement ne prend pas en charge les branches, remplacez les fichiers dans le dossier de production par les fichiers du dossier intermédiaire.

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>Déplacer des fonctions existantes pour un déploiement continu
Lorsque vous disposez de fonctions existantes que vous avez créées et conservées dans le portail, vous devez télécharger vos fichiers de code de fonction à l’aide de FTP ou le référentiel Git local avant que vous pouvez définir un déploiement continu comme décrit précédemment. Faire cela dans les paramètres d’Azure App Service pour votre function app. Après avoir téléchargé les fichiers, vous pouvez les télécharger à votre source de déploiement continu choisi.

> [!NOTE]
> Après avoir configuré l’intégration continue, vous ne pouvez plus modifier vos fichiers sources dans le portail Functions.

<a name="credentials"></a>
#### <a name="configure-deployment-credentials"></a>configurer les informations d’identification de déploiement
Avant de pouvoir télécharger des fichiers à partir de votre application de fonction à l’aide de FTP ou un référentiel Git local, vous devez configurer vos informations d’identification pour accéder au site. Informations d’identification sont définies au niveau de l’application de fonction. Utilisez la procédure suivante pour définir les informations d’identification de déploiement dans le portail Azure :

1. Dans votre function app dans le [Azure portal](https://portal.azure.com), sélectionnez **fonctionnalités de la plateforme** > **informations d’identification de déploiement**.
   
1. Entrez un nom d’utilisateur et un mot de passe, puis sélectionnez **enregistrer**. 

   ![Sélections pour définir les informations d’identification de déploiement local](./media/functions-continuous-deployment/setup-deployment-credentials.png)

Vous pouvez désormais utiliser ces informations d’identification pour accéder à votre application de fonction à partir du FTP ou du référentiel Git intégré.

<a name="downftp"></a>
#### <a name="download-files-by-using-ftp"></a>Télécharger des fichiers à l’aide de FTP

1. Dans votre function app dans le [Azure portal](https://portal.azure.com), sélectionnez **fonctionnalités de la plateforme** > **propriétés**. Ensuite, copiez les valeurs de **utilisateur FTP/déploiement**, **nom d’hôte FTP**, et **nom d’hôte FTPS**.  

   La valeur **Utilisateur FTP/déploiement** doit être entrée telle qu’elle est affichée dans le portail et inclure le nom de l’application afin de fournir le contexte approprié pour le serveur FTP.
   
   ![Sélections pour obtenir des informations de votre déploiement](./media/functions-continuous-deployment/get-deployment-credentials.png)

1. À partir de votre client FTP, utilisez les informations de connexion que vous avez recueillies pour vous connecter à votre application et de télécharger les fichiers sources pour vos fonctions.

<a name="downgit"></a>
#### <a name="download-files-by-using-a-local-git-repository"></a>Télécharger des fichiers à l’aide d’un référentiel Git local

1. Dans votre function app dans le [Azure portal](https://portal.azure.com), sélectionnez **fonctionnalités de la plateforme** > **options de déploiement**. 
   
    ![Sélections pour l’ouverture des options de déploiement](./media/functions-continuous-deployment/setup-deployment.png)
 
1. Ensuite, sous la **déploiements** panneau, sélectionnez **le programme d’installation**.
 
    ![Panneau déploiements](./media/functions-continuous-deployment/setup-deployment-1.png)
   
1. Sur le **source de déploiement** panneau, sélectionnez **référentiel Git Local** > **OK**.

1. Dans **fonctionnalités de la plateforme**, sélectionnez **propriétés** et notez la valeur de l’URL Git. 
   
    ![Sélections pour l’obtention de l’URL Git](./media/functions-continuous-deployment/get-local-git-deployment-url.png)

1. Clonez le référentiel sur votre ordinateur local à l’aide d’une invite de commandes prenant en charge Git ou votre outil Git favori. La commande de clone Git se présente comme suit :
   
        git clone https://username@my-function-app.scm.azurewebsites.net:443/my-function-app.git

1. Récupérez les fichiers à partir de votre application de fonction sur le clone de votre ordinateur local, comme dans l’exemple suivant :
   
        git pull origin master
   
    Si nécessaire, fournissez vos [informations d’identification de déploiement configurées](#credentials).  

[GitHub]: https://github.com/

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Meilleures pratiques pour Azure Functions](functions-best-practices.md)
