---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 30b5ae499d29b8b78b5852074362841ac1ceb49f
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134128"
---
1. Connectez-vous au [portail Azure].
2. Sélectionnez **+NOUVEAU** > **Web + Mobile** > **Application mobile**, puis indiquez le nom de votre serveur principal Mobile Apps.
3. Pour **Groupe de ressources**, sélectionnez un groupe de ressources existant ou créez-en un (en utilisant le même nom que votre application). 
4. Pour **Plan App Service**, le plan par défaut (dans [Niveau Standard](https://azure.microsoft.com/pricing/details/app-service/)) est sélectionné. Vous pouvez sélectionner un autre plan ou en [créer un](../articles/app-service/app-service-plan-manage.md#create-an-app-service-plan). 

   Les paramètres du plan App Service déterminent [l’emplacement, les fonctionnalités, les coûts et les ressources de calcul](https://azure.microsoft.com/pricing/details/app-service/) associés à votre application. Pour plus d’informations sur les plans App Service et sur la création d’un plan à un autre niveau tarifaire et à l’emplacement souhaité, consultez l’article [Présentation détaillée des plans Azure App Service](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
   
5. Sélectionnez **Créer**. Cette opération crée le serveur principal Mobile Apps. 
6. Dans le volet **Paramètres** du nouveau serveur principal Mobile Apps, sélectionnez **Démarrage rapide** > votre plateforme d’application cliente > **Connecter une base de données**. 
   
   ![Sélections pour la connexion d’une base de données](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)
7. Dans le volet **Ajouter une connexion de données**, sélectionnez **SQL Database** > **Créer une base de données**. Entrez le nom de la base de données, choisissez un niveau tarifaire, puis sélectionnez **Serveur**. Vous pouvez réutiliser cette nouvelle base de données. Si vous disposez déjà d’une base de données au même emplacement, vous pouvez choisir à la place **Utiliser une base de données existante**. Nous déconseillons l’utilisation d’une base de données à un autre emplacement, en raison de la latence plus importante et des frais de bande passante supplémentaires.
   
   ![Sélection d’une base de données](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)
8. Dans le volet **Nouveau serveur**, entrez un nom unique dans la zone **Nom du serveur**, indiquez l’identifiant de connexion et le mot de passe, sélectionnez **Autoriser les services Azure à accéder au serveur**, puis sélectionnez **OK**. Cette opération crée la base de données.
9. Dans le volet **Ajouter une connexion de données**, sélectionnez **Chaîne de connexion**, entrez l’identifiant de connexion et le mot de passe pour votre base de données, puis sélectionnez **OK**. 

   Patientez quelques minutes jusqu’au déploiement de la base de données, puis continuez.

<!-- URLs. -->
[Portail Azure]: https://portal.azure.com/
