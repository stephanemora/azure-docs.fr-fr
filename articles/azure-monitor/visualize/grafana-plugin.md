---
title: Assurer le monitoring des applications et services Azure à l’aide de Grafana
description: Acheminez les données Azure Monitor et Application Insights en vue de les afficher dans Grafana.
ms.topic: conceptual
ms.date: 11/06/2017
ms.openlocfilehash: 65bd221bd2f5574db33e6c164f75bb0760dabd3b
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129812016"
---
# <a name="monitor-your-azure-services-in-grafana"></a>Surveiller vos services Azure dans Grafana
Vous pouvez surveiller les services et applications Azure à l’aide de [Grafana](https://grafana.com/) et de son [plug-in Azure Monitor de source de données](https://grafana.com/docs/grafana/latest/datasources/azuremonitor/) inclus. Le plug-in récupère les données de trois services Azure :
- Métriques Azure Monitor pour les données numériques de série chronologique à partir des données des ressources Azure. 
- Journaux d’activité Azure Monitor pour les données de journal et de performances à partir des ressources Azure qui vous permettent de formuler des requêtes à l’aide du langage de requête Kusto (KQL).
- Azure Resource Graph pour interroger et identifier rapidement les ressources Azure parmi les abonnements.

Vous pouvez ensuite afficher ces données de performances et de disponibilité sur vos tableaux de bord Grafana.

Suivez les étapes suivantes pour configurer un serveur Grafana, utiliser les tableaux de bord Azure Monitor prêts à l’emploi et créer des tableaux de bord personnalisés avec les métriques et les journaux d’activité d’Azure Monitor.

## <a name="set-up-a-grafana-server"></a>Configurer un serveur Grafana

### <a name="set-up-grafana-locally"></a>Configurer Grafana localement
Pour configurer un serveur Grafana local, [téléchargez et installez Grafana dans votre environnement local](https://grafana.com/grafana/download).

### <a name="set-up-grafana-on-azure-through-the-azure-marketplace"></a>Configurer Grafana sur Azure par l’intermédiaire de la Place de marché Azure
1. Accédez à la Place de marché Azure et choisissez Grafana, de Grafana Labs.

2. Renseignez les noms et les détails. Créez un groupe de ressources. Effectuez le suivi des valeurs que vous choisissez pour le nom d’utilisateur de la machine virtuelle, le mot de passe de la machine virtuelle et le mot de passe d’administrateur du serveur Grafana.  

3. Choisissez une taille de machine virtuelle et un compte de stockage.

4. Configurez les paramètres de configuration du réseau.

5. Affichez le résumé et sélectionnez **Créer** après avoir accepté les conditions d’utilisation.

6. Lorsque le déploiement est terminé, sélectionnez **Accéder au groupe de ressources**. Une liste de ressources nouvellement créées s’affiche.

    ![Objets du groupe de ressources Grafana](media/grafana-plugin/grafana-resource-group.png)

    Si vous sélectionnez le groupe de sécurité réseau (*grafana-nsg* dans ce cas), vous vous apercevez que le port 3000 est utilisé pour accéder au serveur Grafana.

7. Récupérez l’adresse IP publique de votre serveur Grafana, puis revenez à la liste des ressources et sélectionnez **Adresse IP publique**.

## <a name="sign-in-to-grafana"></a>Se connecter à Grafana

> [!IMPORTANT]
> Le navigateur Internet Explorer et les anciens navigateurs de Microsoft Edge ne sont pas compatibles avec Grafana. Vous devez utiliser un navigateur basé sur Chromium, y compris la version actuelle de Microsoft Edge. Voir [Navigateurs pris en charge pour Grafana](https://grafana.com/docs/grafana/latest/installation/requirements/#supported-web-browsers).

1. À l’aide de l’adresse IP de votre serveur, ouvrez la page de connexion *http://\<IP address\>:3000* ou le *\<DNSName>\:3000* dans votre navigateur. 3000 étant le port par défaut, notez que vous avez peut-être sélectionné un port différent pendant la configuration. Une page de connexion doit s’afficher pour le serveur Grafana que vous avez créé.

    ![Écran de connexion Grafana](./media/grafana-plugin/login-screen.png)

2. Connectez-vous avec le nom d’utilisateur *admin* et le mot de passe d’administrateur du serveur Grafana que vous avez créé précédemment. Si vous utilisez une configuration locale, le mot de passe par défaut doit être *administrateur* ; il vous est demandé de le changer lors de votre première connexion.

## <a name="configure-data-source-plugin"></a>Configurer le plug-in de source de données

Une fois la connexion établie, vous devriez voir l’option permettant d’ajouter votre première source de données.

![Ajouter une source de données](./media/grafana-plugin/add-data-source.png)

1. Sélectionnez **Add data source** (Ajouter une source de données), filtrez par nom *Azure* et sélectionnez la source de données **Azure Monitor**.

![Source de données Azure Monitor](./media/grafana-plugin/azure-monitor-data-source-list.png)

2. Choisissez un nom pour la source de données et choisissez entre Identité managée et Inscription d’application pour l’authentification.

Si votre instance Grafana est hébergée sur une machine virtuelle Azure dont l’identité managée est activée, vous pouvez utiliser cette approche pour l’authentification. Toutefois, si votre instance Grafana n’est pas hébergée sur Azure ou si l’identité managée n’est pas activée, vous devrez utiliser Inscription d’application avec un principal de service Azure pour configurer l’authentification.

### <a name="use-managed-identity"></a>Utiliser l’identité managée

1. Activez l’identité managée sur votre machine virtuelle et modifiez le paramètre de prise en charge de l’identité managée du serveur Grafana sur true.
    * Pour l’abonnement, le groupe de ressources ou les ressources que vous souhaitez visualiser dans Grafana, le [rôle Lecteur d’analyse](/azure/azure-monitor/roles-permissions-security) doit être attribué à l’identité managée de votre machine virtuelle d’hébergement.
    * En outre, vous devrez mettre à jour le paramètre « managed_identity_enabled = true » dans la configuration du serveur Grafana. Pour plus d’informations, consultez la page [Configuration de Grafana](https://grafana.com/docs/grafana/latest/administration/configuration/). Une fois les deux étapes terminées, vous pouvez ensuite enregistrer et tester l’accès.

2. Sélectionnez **Save & test** (Enregistrer et tester) pour que Grafana teste les informations d’identification. Un message similaire à celui qui suit doit s’afficher.  
    
   ![Configuration de l’identité managée de la source de données Grafana approuvée](./media/grafana-plugin/managed-identity.png)

### <a name="or-use-app-registration"></a>Ou utiliser l’inscription d’application

1. Créer un principal de service - Grafana utilise un principal de service Azure Active Directory pour se connecter à des API Azure Monitor et collecter des données. Vous devez créer un principal de service ou en utiliser un existant pour gérer l’accès à vos ressources Azure.
    * Consultez [ces instructions](/azure/active-directory/develop/howto-create-service-principal-portal) pour créer un principal de service. Copiez et enregistrez votre ID de locataire (ID d’annuaire), ID client (ID d’application) et le secret client (valeur de la clé d’application).
    * Consultez [Affecter l’application à un rôle](/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) pour attribuer le [rôle Lecteur d’analyse](/azure/azure-monitor/roles-permissions-security) à l’application Azure Active Directory dans l’abonnement, le groupe de ressources ou la ressource que vous souhaitez superviser.
  
2. Fournissez les informations de connexion que vous souhaitez utiliser.
    * Lors de la configuration du plug-in, vous pouvez indiquer quel cloud Azure vous souhaitez que le plug-in supervise (Public, Azure - US Government, Azure - Allemagne ou Azure - Chine).
        > [!NOTE]
        > Certains champs de source de données sont nommés différemment de leurs paramètres Azure corrélés :
        > * L’ID de locataire est l’ID d’annuaire Azure
        > * L’ID client est l’ID d’application Azure Active Directory
        > * Le secret client est la valeur de la clé de l’application Azure Active Directory

3. Sélectionnez **Save & test** (Enregistrer et tester) pour que Grafana teste les informations d’identification. Un message similaire à celui qui suit doit s’afficher.  
    
   ![Configuration de l’inscription d’application de la source de données Grafana approuvée](./media/grafana-plugin/app-registration.png)

## <a name="use-azure-monitor-data-source-dashboards"></a>Utiliser les tableaux de bord des sources de données Azure Monitor

Le plug-in Azure Monitor comprend plusieurs tableaux de bord prêts à l’emploi que vous pouvez importer pour commencer.

1. Cliquez sur l’onglet **Dashboards** (Tableaux de bord) du plug-in Azure Monitor pour afficher la liste des tableaux de bord disponibles.

   ![Tableaux de bord des sources de données Azure Monitor](./media/grafana-plugin/azure-data-source-dashboards.png)

2. Cliquez sur **Import** (Importer) pour télécharger un tableau de bord.

3. Cliquez sur le nom du tableau de bord importé pour l’ouvrir.

4. Utilisez les sélecteurs déroulants en haut du tableau de bord pour choisir l’abonnement, le groupe de ressources et la ressource qui vous intéresse.

   ![Tableaux de bord d’informations sur le stockage](./media/grafana-plugin/storage-insights-dashboard.png)

## <a name="build-grafana-dashboards"></a>Créer des tableaux de bord Grafana

1. Accédez à la page d’accueil de Grafana, puis sélectionnez **Create your first dashboard** (Créer votre premier tableau de bord).

2. Dans le nouveau tableau de bord, sélectionnez **Add an empty panel** (Ajouter un panneau vide).

3. Un panneau vide *Time series* (Série chronologique) apparaît sur votre tableau de bord avec un éditeur de requête illustré ci-dessous. Sélectionnez la source de données Azure Monitor que vous avez configurée.
   * Collecte des métriques Azure Monitor : Sélectionnez **Metrics** (Métriques) dans la liste déroulante du service. Une liste de sélecteurs s’affiche, dans laquelle vous pouvez sélectionner les ressources et le métrique à superviser dans ce graphe. Pour collecter les métriques à partir d’une machine virtuelle, utilisez l’espace de noms **Microsoft.Compute/VirtualMachines**. Après avoir sélectionné les machines virtuelles et les métriques, vous pouvez commencer à voir leurs données s’afficher dans le tableau de bord.
     ![Grafana : Configuration du graphe pour les métriques Azure Monitor](./media/grafana-plugin/metrics-config.png)
   * Collecte des données des journaux d’activité Azure Monitor : Sélectionnez **Logs** (Journaux d’activité) dans la liste déroulante du service. Sélectionnez la ressource ou l’espace de travail Log Analytics que vous souhaitez interroger et définissez le texte de la requête. Notez que le plug-in Azure Monitor vous permet d’interroger les journaux d’activité pour des ressources spécifiques ou à partir d’un espace de travail Log Analytics. Dans l’éditeur de requête ci-dessous, vous pouvez copier toute requête de journal existante ou en créer une nouvelle. Au fur et à mesure que vous tapez votre requête, IntelliSense apparaît en suggérant des options de saisie semi-automatique. Enfin, sélectionnez le type de visualisation, **Time series** (Série chronologique), et exécutez la requête.
    
     > [!NOTE]
     >
     > La requête par défaut fournie avec le plug-in utilise deux macros : "$__timeFilter() et $__interval. 
     > Lorsque vous effectuez un zoom sur une partie d’un graphe, ces macros permettent à Grafana de calculer dynamiquement la plage de temps et le fragment de temps. Vous pouvez supprimer ces macros et utiliser un filtre d’heure standard, tel que *TimeGenerated > ago(1h)* , mais cela signifie que le graphe ne prend plus en charge la fonctionnalité de zoom.
    
     L’exemple suivant montre une requête exécutée sur une ressource Application Insights pour obtenir le temps de réponse moyen de toutes les demandes.

     ![Grafana - Configuration du graphe pour Azure Log Analytics](./media/grafana-plugin/logs-config.png)

    * Outre les requêtes de métriques et de journaux présentées ci-dessus, le plug-in Azure Monitor prend en charge les requêtes d’[Azure Resource Graph](/azure/governance/resource-graph/concepts/explore-resources).

## <a name="advanced-grafana-features"></a>Fonctionnalités avancées de Grafana

### <a name="variables"></a>Variables
Certaines valeurs de ressources et de requêtes peuvent être sélectionnées par l’utilisateur du tableau de bord via des listes déroulantes de l’interface utilisateur et être mises à jour dans la ressource ou la requête.
Prenons l’exemple de la requête suivante qui montre l’utilisation d’un espace de travail Log Analytics :
```
Usage 
| where $__timeFilter(TimeGenerated) 
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```

Vous pouvez configurer une variable qui répertorie tous les **espaces de travail** disponibles, puis met à jour la ressource qui est interrogée en fonction de la sélection de l’utilisateur.
Pour créer une variable, cliquez sur le bouton Settings (Paramètres) du tableau de bord situé dans la zone supérieure droite, sélectionnez **Variables**, puis **New** (Nouveau).
Dans la page de la variable, définissez la source de données et la requête à exécuter afin d’obtenir la liste des valeurs.

![Grafana : définir une variable](./media/grafana-plugin/define-variable.png)

Une fois créée, modifiez la ressource de votre requête pour utiliser les valeurs sélectionnées et vos graphiques réagiront en conséquence :

![Requête avec variable](./media/grafana-plugin/query-with-variable.png)

Consultez la liste complète des [variables de modèle](https://grafana.com/docs/grafana/latest/datasources/azuremonitor/template-variables/) disponibles dans le plug-in Azure Monitor.

### <a name="create-dashboard-playlists"></a>Créer des sélections de tableau de bord

Une des nombreuses fonctionnalités intéressantes de Grafana repose sur la sélection de tableaux de bord. Vous pouvez créer plusieurs tableaux de bord et les ajouter à une liste en configurant un intervalle pour que chaque tableau de bord s’affiche. Accédez à l’élément de menu Dashboards (Tableaux de bord) et sélectionnez **Playlists** (Listes de lecture) pour créer une liste de lecture de tableaux de bord existants à parcourir. Vous pouvez les afficher sur un grand écran mural pour fournir un tableau complet à votre groupe.

![Exemple de sélection Grafana](./media/grafana-plugin/playlist.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous avez configuré un environnement Grafana sur Azure, vous êtes facturé lorsque les machines virtuelles sont en cours d’exécution, que vous les utilisiez ou non. Pour éviter des frais supplémentaires, nettoyez le groupe de ressources créé dans cet article.

1. Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources** et sur **Grafana**.
2. Dans la page de votre groupe de ressources, cliquez sur **Supprimer**, tapez **Grafana** dans la zone de texte, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes
* [Comparer les métriques et les journaux d’activité Azure Monitor](../data-platform.md)
