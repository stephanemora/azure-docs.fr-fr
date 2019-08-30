---
title: 'Démarrage rapide : Créer un index Recherche Azure en Java'
description: Explique comment créer un index, charger des données et exécuter des requêtes à l’aide de Java et des API REST de Recherche Azure.
services: search
author: jj09
manager: jlembicz
ms.service: search
ms.topic: conceptual
ms.date: 08/26/2018
ms.author: jjed
ms.custom: seodec2018, seo-java-july2019
ms.openlocfilehash: 7deb9d2cf16aa82de7ce4ea163652c2936819063
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533242"
---
# <a name="quickstart-create-an-azure-search-index-in-java"></a>Démarrage rapide : Créer un index Recherche Azure en Java
> [!div class="op_single_selector"]
> * [Portal](search-get-started-portal.md)
> * [.NET](search-howto-dotnet-sdk.md)
> 
> 

Apprenez à créer une application Java personnalisée, qui utilise Azure Search pour ses fonctionnalités de recherche. Ce didacticiel utilise l’ [API REST du service Azure Search](https://msdn.microsoft.com/library/dn798935.aspx) pour créer les objets et opérations utilisés dans cet exercice.

Pour exécuter cet exemple, vous devez disposer d’un service Azure Search auquel vous pouvez vous connecter dans le [portail Azure](https://portal.azure.com). Consultez [Création d’un service Azure Search dans le portail](search-create-service-portal.md) pour obtenir des instructions pas-à-pas.

Nous avons utilisé les logiciels suivants pour générer et tester cet exemple :

* [Environnement de développement intégré (IDE) Eclipse pour développeurs Java EE](https://www.eclipse.org/downloads/packages/release/photon/r/eclipse-ide-java-ee-developers). Veillez à télécharger la version EE. Une des étapes de vérification nécessite une fonctionnalité présente uniquement dans cette édition.
* [JDK 8u181](https://aka.ms/azure-jdks)
* [Apache Tomcat 8.5.33](https://tomcat.apache.org/download-80.cgi#8.5.33)

## <a name="about-the-data"></a>À propos des données
Cet exemple d'application utilise des données de l’ [USGS (United States Geological Services)](https://geonames.usgs.gov/domestic/download_data.htm), concernant l'État de Rhode Island pour réduire la taille du jeu de données. Nous allons utiliser ces données pour créer une application de recherche qui renvoie des bâtiments importants, tels que les hôpitaux et les écoles, ainsi que des caractéristiques géologiques, telles que les ruisseaux, les lacs et les sommets.

Dans cette application, le programme **SearchServlet.java** crée et charge l’index à l’aide d’une construction [Indexer](https://msdn.microsoft.com/library/azure/dn798918.aspx), en récupérant le jeu de données USGS filtré à partir d’une base de données Azure SQL Database. Les informations d’identification et de connexion à la source de données en ligne sont fournies dans le code du programme. Pour accéder aux données, aucune configuration supplémentaire n'est nécessaire.

> [!NOTE]
> Nous avons filtré ce jeu de données pour ne pas dépasser la limite de 10 000 documents du niveau de tarification gratuit. Si vous utilisez le niveau standard, cette limite ne s'applique pas et vous pouvez modifier ce code pour utiliser un jeu de données plus important. Pour plus d'informations sur la capacité de chaque niveau de tarification, consultez la section [Limites et contraintes](search-limits-quotas-capacity.md).
> 
> 

## <a name="about-the-program-files"></a>À propos des fichiers de programme
La liste suivante décrit les fichiers qui sont pertinents pour cet exemple.

* Search.jsp : fournit l’interface utilisateur
* SearchServlet.java : fournit des méthodes (semblables à un contrôleur dans MVC)
* SearchServiceClient.java : gère les requêtes HTTP
* SearchServiceHelper.java : classe d’assistance qui fournit des méthodes statiques
* Document.java : fournit le modèle de données
* config.properties : définit l’URL et la valeur `api-key` du service Recherche Azure
* pom.xml : dépendance Maven

<a id="sub-2"></a>

## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Rechercher le nom et la valeur `api-key` de votre service Recherche Azure
Tous les appels d’API REST dans le service Recherche Azure exigent que vous fournissiez l’URL du service et un paramètre `api-key`. 

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans la barre d’index, sélectionnez **Service de recherche** pour obtenir la liste des services Azure Search provisionnés pour votre abonnement.
3. Sélectionnez le service que vous souhaitez utiliser.
4. Le tableau de bord des services affiche des vignettes contenant des informations essentielles, ainsi que l'icône de clé permettant d'accéder aux clés administrateur.
   
      ![Capture d’écran montrant comment accéder aux clés d’administration à partir du tableau de bord du service][3]
5. Copiez l'URL du service et une clé d’administration. Vous en aurez besoin plus tard, pour les ajouter au fichier **config.properties** .

## <a name="download-the-sample-files"></a>Télécharger les fichiers exemples
1. Accédez à [search-java-indexer-demo](https://github.com/Azure-Samples/search-java-indexer-demo) sur GitHub.
2. Sélectionnez **Download ZIP**, enregistrez le fichier zip sur le disque, puis extrayez tous les fichiers qu’il contient. Si vous le souhaitez, vous pouvez extraire ces fichiers dans votre espace de travail Java pour faciliter la recherche du projet ultérieurement.
3. Les fichiers exemples sont en lecture seule. Avec le bouton droit de la souris, cliquez sur le dossier et désactivez l'attribut de lecture seule.

Toutes les modifications et instructions d'exécution ultérieures seront effectuées sur les fichiers de ce dossier.  

## <a name="import-project"></a>Importer le projet
1. Dans Eclipse, sélectionnez **File** > **Import** > **General** > **Existing Projects into Workspace**.
   
    ![Capture d’écran montrant comment importer un projet existant][4]
2. Dans **Select root directory**, accédez au dossier contenant les fichiers exemples. Sélectionnez le dossier qui contient le dossier .project. Le projet doit s'afficher sélectionné dans la liste **Projects** .
   
    ![Capture d’écran montrant la liste des projets dans la fenêtre Importer des projets][12]
3. Sélectionnez **Terminer**.
4. Utilisez **Project Explorer** pour afficher et modifier les fichiers. Si l’explorateur n’est pas ouvert, sélectionnez**Window** > **Show View** > **Project Explorer** ou utilisez le raccourci pour l’ouvrir.

## <a name="configure-the-service-url-and-api-key"></a>Configurer l’URL du service et le paramètre `api-key`
1. Dans **Project Explorer**, double-cliquez sur **config.properties** pour modifier les paramètres de configuration contenant le nom du serveur et le paramètre `api-key`.
2. Reportez-vous aux étapes précédentes de cet article. Vous y trouverez l’URL de service et le paramètre `api-key` du service dans le [Portail Microsoft Azure](https://portal.azure.com) afin d’obtenir les valeurs que vous allez saisir dans le fichier **config.properties**.
3. Dans le fichier **config.properties**, remplacez « API Key » par le paramètre `api-key` de votre service. Ensuite, le nom du service (le premier composant de l’URL https://servicename.search.windows.net) remplace « ServiceName » dans le même fichier.
   
    ![Capture d’écran montrant comment remplacer la clé API][5]

## <a name="configure-the-project-build-and-runtime-environments"></a>Configuration des environnements de projet, de génération et d'exécution
1. Dans Project Explorer dans Eclipse, cliquez avec le bouton droit sur le projet, puis cliquez sur **Properties** > **Project Facets**.
2. Sélectionnez **Dynamic Web Module**, **Java** et **JavaScript**.
   
    ![Capture d’écran montrant comment sélectionner les facettes de projet pour votre projet][6]
3. Sélectionnez **Appliquer**.
4. Sélectionnez **Window** > **Preferences** > **Server** > **Runtime Environments** > **Add..** .
5. Développez Apache et sélectionnez la version du serveur Apache Tomcat que vous avez précédemment installée. Sur notre système, nous avons installé la version 8.
   
    ![Capture d’écran montrant où, dans la fenêtre de l’environnement d’exécution, vous pouvez sélectionner votre version d’Apache Tomcat][7]
6. Dans la page suivante, spécifiez le répertoire d'installation de Tomcat. Sur un ordinateur Windows, il s’agit très probablement du répertoire C:\Program Files\Apache Software Foundation\Tomcat *version*.
7. Sélectionnez **Terminer**.
8. Sélectionnez **Window** > **Preferences** > **Java** > **Installed JREs** > **Add**.
9. Dans **Add JRE**, sélectionnez **Standard VM**.
10. Sélectionnez **Suivant**.
11. Dans JRE Definition, dans la page d’accueil de JRE, sélectionnez **Directory**.
12. Accédez à **Program Files** > **Java** et sélectionnez le JDK que vous avez installé précédemment. Il est important de sélectionner le JDK comme JRE.
13. Dans Installed JREs, sélectionnez le **JDK**. Vos paramètres doivent être similaires au contenu de la capture d'écran suivante.
    
    ![Capture d’écran montrant comment sélectionner JDK comme JRE installé][9]
14. Si vous le souhaitez, sélectionnez **Window** > **Web Browser** > **Internet Explorer** pour ouvrir l’application dans un navigateur externe. Un navigateur externe vous donne une meilleure expérience de l’application Web.
    
    ![Capture d’écran montrant comment sélectionner Internet Explorer comme fenêtre de navigation externe][8]

La phase de configuration est maintenant terminée. Vous allez maintenant créer et exécuter le projet.

## <a name="build-the-project"></a>Créer le projet
1. Dans l’Explorateur de projets, cliquez avec le bouton droit sur le nom du projet et sélectionnez **Run As** > **Maven build** pour configurer le projet.
   
    ![Capture d’écran montrant comment sélectionner Maven build dans la fenêtre de l’Explorateur de projets][10]
2. Dans Edit Configuration, dans Goals, entrez « clean install », puis sélectionnez **Run**.

Des messages d'état s’affichent dans la fenêtre de la console. Le message BUILD SUCCESS indique que le projet a été généré sans erreur.

## <a name="run-the-app"></a>Exécution de l'application
Dans cette dernière étape, vous allez exécuter l'application dans un environnement d'exécution de serveur local.

Si ce n’est déjà fait, vous devez spécifier un environnement d'exécution de serveur dans Eclipse.

1. Dans Project Explorer, développez **WebContent**.
2. Cliquez avec le bouton droit sur **Search.jsp** > **Run As** > **Run on Server**. Sélectionnez le serveur Apache Tomcat, puis sélectionnez **Run**.

> [!TIP]
> Si vous avez stocké votre projet dans un espace de travail personnalisé, vous devrez désigner cet emplacement dans **Run Configuration** pour éviter une erreur lors du démarrage du serveur. Dans Project Explorer, cliquez avec le bouton droit sur **Search.jsp** > **Run As** > **Run Configurations**. Sélectionnez le serveur Apache Tomcat. Sélectionnez **Arguments**. Sélectionnez **Workspace** ou **File System** pour définir le dossier contenant le projet.
> 
> 

Lorsque vous exécutez l'application, une fenêtre de navigateur affiche un champ de recherche  permettant d'entrer des termes.

Attendez environ une minute avant de sélectionner **Search**, le temps que le service crée et charge l’index. Si vous obtenez une erreur HTTP 404, Patientez encore un peu avant de réessayer.

## <a name="search-on-usgs-data"></a>Exécution d'une recherche sur les données USGS
Le jeu de données USGS comprend les enregistrements qui correspondent à l'État de Rhode Island. Si vous sélectionnez **Search** et que le champ de recherche est vide, vous obtiendrez les 50 premières entrées, ce qui correspond au paramétrage par défaut.

Saisissez un terme pour que le moteur de recherche puisse travailler. Essayez d'entrer le nom d’une figure locale. « Roger Williams » fut le premier gouverneur de Rhode Island. De nombreux parcs, bâtiments et écoles portent son nom.

![Capture d’écran montrant comment effectuer une recherche sur des données de groupes de sécurité universels][11]

Vous pouvez également essayer les termes suivants :

* Pawtucket
* Pembroke
* goose +cape

## <a name="next-steps"></a>Étapes suivantes
Ceci est le premier didacticiel Azure Search basé sur Java et le jeu de données USGS. Au fil du temps, nous le compléterons avec des fonctionnalités de recherche supplémentaires que vous souhaiterez peut-être utiliser dans vos solutions personnalisées.

Si vous avez déjà une certaine maîtrise d’Azure Search, vous pouvez utiliser cet exemple comme un tremplin pour d’autres expérimentations, notamment pour améliorer la [page de recherche](search-pagination-page-layout.md) ou mettre en place la [navigation par facettes](search-faceted-navigation.md). Vous pouvez également améliorer la page des résultats de la recherche en ajoutant des décomptes et en traitant les documents par lots afin que les utilisateurs puissent parcourir les résultats.

Vous découvrez Azure Search ? Nous vous recommandons de suivre les autres didacticiels pour comprendre ce que vous pouvez créer. Consultez les autres ressources disponibles dans notre [page de documentation](https://azure.microsoft.com/documentation/services/search/) . 

<!--Image references-->
[1]: ./media/search-get-started-java/create-search-portal-1.PNG
[2]: ./media/search-get-started-java/create-search-portal-21.PNG
[3]: ./media/search-get-started-java/create-search-portal-31.PNG
[4]: ./media/search-get-started-java/AzSearch-Java-Import1.PNG
[5]: ./media/search-get-started-java/AzSearch-Java-config1.PNG
[6]: ./media/search-get-started-java/AzSearch-Java-ProjectFacets1.PNG
[7]: ./media/search-get-started-java/AzSearch-Java-runtime1.PNG
[8]: ./media/search-get-started-java/AzSearch-Java-Browser1.PNG
[9]: ./media/search-get-started-java/AzSearch-Java-JREPref1.PNG
[10]: ./media/search-get-started-java/AzSearch-Java-BuildProject1.PNG
[11]: ./media/search-get-started-java/rogerwilliamsschool1.PNG
[12]: ./media/search-get-started-java/AzSearch-Java-SelectProject.png
