---
title: Déployer une application web avec un modèle – Azure Cosmos DB
description: Découvrez comment déployer un compte Azure Cosmos DB, Azure App Service Web Apps et un exemple d’application web avec un modèle Azure Resource Manager.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: sngun
ms.openlocfilehash: 2306dbe234e171ac613c33458df1990b767637df
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128373"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-web-apps-using-an-azure-resource-manager-template"></a>Déployer Azure Cosmos DB et Azure App Service Web Apps avec un modèle Azure Resource Manager
Ce tutoriel vous montre comment utiliser un modèle Azure Resource Manager pour déployer et intégrer [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), une application web [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) et un exemple d’application web.

À l’aide de modèles Azure Resource Manager, vous pouvez facilement automatiser le déploiement et la configuration de vos ressources Azure.  Ce didacticiel montre comment déployer une application web et configurer automatiquement les informations de connexion du compte Azure Cosmos DB.

Après avoir terminé ce didacticiel, vous serez en mesure de répondre aux questions suivantes :  

* Comment puis-je utiliser un modèle Azure Resource Manager pour déployer et intégrer un compte Azure Cosmos DB et une application web dans Azure App Service ?
* Comment puis-je utiliser un modèle Azure Resource Manager pour déployer et intégrer un compte Azure Cosmos DB, une application web dans App Service Web Apps et une application Webdeploy ?

<a id="Prerequisites"></a>

## <a name="prerequisites"></a>Prérequis
> [!TIP]
> Ce didacticiel ne suppose pas d’être familiarisé avec les modèles Azure Resource Manager ou JSON, sauf si vous envisagez de modifier les modèles référencés ou les options de déploiement.
> 
> 

Avant de suivre les instructions de ce didacticiel, assurez-vous de disposer d’un abonnement Azure. Azure est une plateforme disponible par abonnement.  Pour plus d'informations sur l'obtention d'un abonnement, consultez les pages [Modes d’achat d’Azure](https://azure.microsoft.com/pricing/purchase-options/), [Offres spéciales membres](https://azure.microsoft.com/pricing/member-offers/) ou [Version d'évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

## <a id="CreateDB"></a>Étape 1 : Télécharger les fichiers de modèle
Commençons par télécharger les fichiers de modèle nécessaires pour ce didacticiel.

1. Téléchargez le modèle [d’exemple de création d’un compte Azure Cosmos DB, de Web Apps et de déploiement d’une application de démonstration](https://portalcontent.blob.core.windows.net/samples/DocDBWebsiteTodo.json) dans un dossier local (par exemple, C:\Azure Cosmos DBTemplates). Ce modèle déploie un compte Azure Cosmos DB, une application web App Service et une application web.  Il configure également automatiquement l’application web pour se connecter au compte Azure Cosmos DB.
2. Téléchargez le modèle [d’exemple de création d’un compte Azure Cosmos DB et de Web Apps](https://portalcontent.blob.core.windows.net/samples/DocDBWebSite.json) dans un dossier local (par exemple, C:\Azure Cosmos DBTemplates). Ce modèle déploie un compte Azure Cosmos DB et une application web App Service, et modifie les paramètres de l’application du site pour faire facilement apparaître les informations de connexion Azure Cosmos DB. Toutefois, il n’inclut pas d’application web.  

<a id="Build"></a>

## <a name="step-2-deploy-the-azure-cosmos-db-account-app-service-web-app-and-demo-application-sample"></a>Étape 2 : Déployer un compte Azure Cosmos DB, une application web App Service et une application de démonstration
Nous allons maintenant déployer notre premier modèle.

> [!TIP]
> Ce modèle ne valide pas le fait que le nom de l’application web et le nom du compte Azure Cosmos DB entrés dans le modèle suivant soient a) corrects et b) disponibles.  Il est vivement recommandé de vérifier la disponibilité des noms que vous souhaitez fournir avant de soumettre le déploiement.
> 
> 

1. Connectez-vous au [portail Azure](https://portal.azure.com), cliquez sur Nouveau et recherchez « Déploiement de modèle ».
    ![Capture d’écran de l’interface utilisateur du déploiement de modèle](./media/create-website/TemplateDeployment1.png)
2. Sélectionnez l’élément Template deployment et cliquez sur **Créer** ![Capture d’écran de l’interface utilisateur du Template deployment](./media/create-website/TemplateDeployment2.png)
3. Cliquez sur **Modifier le modèle**, collez le contenu du fichier de modèle DocDBWebsiteTodo.json, puis cliquez sur **Enregistrer**.
   ![Capture d’écran de l’interface utilisateur du déploiement de modèle](./media/create-website/TemplateDeployment3.png)
4. Cliquez sur **Modifier les paramètres**, entrez des valeurs pour chacun des paramètres obligatoires, puis cliquez sur **OK**.  Les paramètres sont les suivants :
   
   1. SITENAME : Indique le nom de l’application web App Service. Ce paramètre est utilisé pour former l’URL d’accès à l’application web (par exemple, si vous indiquez « mydemodocdbwebapp », l’URL par laquelle vous accéderez à l’application web sera `mydemodocdbwebapp.azurewebsites.net`).
   2. HOSTINGPLANNAME : Spécifie le nom du plan d’hébergement App Service à créer.
   3. LOCATION : Spécifie l’emplacement Azure dans lequel créer les ressources Azure Cosmos DB et d’application web.
   4. DATABASEACCOUNTNAME : Spécifie le nom du compte Azure Cosmos DB à créer.   
      
      ![Capture d’écran de l’interface utilisateur du déploiement de modèle](./media/create-website/TemplateDeployment4.png)
5. Choisissez un groupe de ressources existant ou fournissez un nom afin de créer un nouveau groupe de ressources et choisissez un emplacement pour le groupe de ressources.

    ![Capture d’écran de l’interface utilisateur du déploiement de modèle](./media/create-website/TemplateDeployment5.png)
6. Cliquez sur **Consulter les termes et conditions**, **Acheter**, puis cliquez sur **Créer** pour commencer le déploiement.  Sélectionnez **Épingler au tableau de bord** pour que le déploiement obtenu soit facilement visible sur votre page d’accueil du portail Azure.
   ![Capture d’écran de l’interface utilisateur du déploiement de modèle](./media/create-website/TemplateDeployment6.png)
7. Une fois le déploiement terminé, le volet Groupe de ressources s’ouvre.
   ![Capture d’écran du volet Groupe de ressources](./media/create-website/TemplateDeployment7.png)  
8. Pour utiliser l’application, accédez à l’URL de l’application web (dans l’exemple ci-dessus, l’URL doit être `http://mydemodocdbwebapp.azurewebsites.net`).  Vous verrez l'application web suivante :
   
   ![Exemple d’application Todo](./media/create-website/image2.png)
9. Continuez et créez deux tâches dans l’application web, puis revenez dans le volet Groupe de ressources du portail Azure. Cliquez sur la ressource de compte Azure Cosmos DB dans la liste des ressources, puis cliquez sur **Explorateur de données**.
10. Exécutez la requête par défaut, « SELECT * FROM c », et examinez les résultats.  Notez que la requête a récupéré la représentation JSON des éléments de tâche que vous avez créés à l'étape 7 ci-dessus.  N’hésitez pas à faire des essais avec des requêtes ; par exemple, essayez d’exécuter SELECT * FROM c WHERE c.isComplete = true pour retourner tous les éléments de tâche qui ont été marqués comme terminés.
11. N’hésitez pas à explorer l’utilisation du portail Azure Cosmos DB ou modifier l’exemple d’application Todo.  Lorsque vous êtes prêt, nous allons déployer un autre modèle.

<a id="Build"></a> 

## <a name="step-3-deploy-the-document-account-and-web-app-sample"></a>Étape 3 : Déployer l’exemple de compte DocumentDB et d’application web
Nous allons maintenant déployer notre deuxième modèle.  Ce modèle vous montre comment injecter des informations de connexion Azure Cosmos DB, telles que le point de terminaison de compte et la clé principale, dans une application web en tant que paramètres d’application ou en tant que chaîne de connexion personnalisée. Par exemple, vous disposez peut-être de votre propre application web que vous souhaitez déployer avec un compte Azure Cosmos DB, et les informations de connexion sont renseignées automatiquement pendant le déploiement.

> [!TIP]
> Ce modèle ne valide pas le fait que le nom de l’application web et le nom du compte Azure Cosmos DB entrés ci-dessous soient a) corrects et b) disponibles.  Il est vivement recommandé de vérifier la disponibilité des noms que vous souhaitez fournir avant de soumettre le déploiement.
> 
> 

1. Dans le [portail Azure](https://portal.azure.com), cliquez sur Nouveau et recherchez « Déploiement de modèle ».
    ![Capture d’écran de l’interface utilisateur du déploiement de modèle](./media/create-website/TemplateDeployment1.png)
2. Sélectionnez l’élément Template deployment et cliquez sur **Créer** ![Capture d’écran de l’interface utilisateur du Template deployment](./media/create-website/TemplateDeployment2.png)
3. Cliquez sur **Modifier le modèle**, collez le contenu du fichier de modèle DocDBWebSite.json, puis cliquez sur **Enregistrer**.
   ![Capture d’écran de l’interface utilisateur du déploiement de modèle](./media/create-website/TemplateDeployment3.png)
4. Cliquez sur **Modifier les paramètres**, entrez des valeurs pour chacun des paramètres obligatoires, puis cliquez sur **OK**.  Les paramètres sont les suivants :
   
   1. SITENAME : Spécifie le nom de l’application web App Service. Ce paramètre est utilisé pour former l’URL d’accès à l’application web (par exemple, si vous spécifiez « mydemodocdbwebapp », l’URL par laquelle vous accéderez à l’application web sera mydemodocdbwebapp.azurewebsites.net).
   2. HOSTINGPLANNAME : Spécifie le nom du plan d’hébergement App Service à créer.
   3. LOCATION : Spécifie l’emplacement Azure dans lequel créer les ressources Azure Cosmos DB et d’application web.
   4. DATABASEACCOUNTNAME : Spécifie le nom du compte Azure Cosmos DB à créer.   
      
      ![Capture d’écran de l’interface utilisateur du déploiement de modèle](./media/create-website/TemplateDeployment4.png)
5. Choisissez un groupe de ressources existant ou fournissez un nom afin de créer un nouveau groupe de ressources et choisissez un emplacement pour le groupe de ressources.

    ![Capture d’écran de l’interface utilisateur du déploiement de modèle](./media/create-website/TemplateDeployment5.png)
6. Cliquez sur **Consulter les termes et conditions**, **Acheter**, puis cliquez sur **Créer** pour commencer le déploiement.  Sélectionnez **Épingler au tableau de bord** pour que le déploiement obtenu soit facilement visible sur votre page d’accueil du portail Azure.
   ![Capture d’écran de l’interface utilisateur du déploiement de modèle](./media/create-website/TemplateDeployment6.png)
7. Une fois le déploiement terminé, le volet Groupe de ressources s’ouvre.
   ![Capture d’écran du volet Groupe de ressources](./media/create-website/TemplateDeployment7.png)  
8. Cliquez sur la ressource de l’application web dans la liste des ressources, puis sur **Paramètres de l’application** ![Capture d’écran du groupe de ressources](./media/create-website/TemplateDeployment9.png)  
9. Remarquez l’existence de paramètres d’application pour le point de terminaison Azure Cosmos DB et chacune des clés principales Azure Cosmos DB.

    ![Capture d’écran des paramètres de l’application](./media/create-website/TemplateDeployment10.png)  
10. N’hésitez pas à continuer à explorer le portail Azure ou à suivre l’un de nos [exemples](https://go.microsoft.com/fwlink/?LinkID=402386) Azure Cosmos DB pour créer votre propre application Azure Cosmos DB.

<a name="NextSteps"></a>

## <a name="next-steps"></a>Étapes suivantes
Félicitations ! Vous avez déployé Azure Cosmos DB, une application web App Service et un exemple d’application web avec les modèles Azure Resource Manager.

* Pour en savoir plus sur Azure Cosmos DB, cliquez [ici](https://azure.microsoft.com/services/cosmos-db/).
* Pour en savoir plus sur les applications web Azure App Service, cliquez [ici](https://go.microsoft.com/fwlink/?LinkId=325362).
* Pour en savoir plus sur les modèles Azure Resource Manager, cliquez [ici](https://msdn.microsoft.com/library/azure/dn790549.aspx).

## <a name="whats-changed"></a>Changements apportés
* Pour connaître tous les changements entre Websites et App Service, consultez l’article : [Azure App Service et les services Azure existants](https://go.microsoft.com/fwlink/?LinkId=529714)

> [!NOTE]
> Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](https://go.microsoft.com/fwlink/?LinkId=523751), où vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.
> 
> 

