---
title: Résolution de problèmes
description: Comment détecter un problème sur Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 22c304b26eddaee4444f6eb12957e2a6fedf7810
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793288"
---
# <a name="troubleshooting"></a>Résolution de problèmes

Les sections suivantes décrivent les problèmes courants liés à Azure FarmBeats et la façon de les corriger.

Pour obtenir de l’aide supplémentaire, écrivez-nous à l’adresse farmbeatssupport@microsoft.com en incluant le fichier deployer.log dans l’e-mail.

 Procédez comme suit pour télécharger le fichier deployer.log :

1. L’icône en surbrillance et sélectionnez l’option **Télécharger** dans la liste déroulante.

    ![Projet FarmBeats](./media/troubleshooting-farmbeats/download-deployer-log-1.png)

2. Sur l’écran suivant, entrez le chemin d’accès à votre fichier deployer.log. Par exemple, farmbeats-deployer.log.

## <a name="sensor-telemetry"></a>Données de télémétrie des capteurs

### <a name="telemetry-not-seen"></a>Télémétrie non détectée

**Symptôme** : Des appareils/capteurs sont déployés, et vous avez lié FarmBeats à votre partenaire d’appareil. Toutefois, vous ne pouvez pas obtenir/afficher les données de télémétrie sur FarmBeats.

**Action corrective** : Rendez-vous sur le Portail Azure et suivez les étapes ci-dessous :

1. Accédez à votre groupe de ressources Hub de données FarmBeats.   
2. Sélectionnez le **hub d’événements** (DatafeedEventHubNamespace....) et vérifiez le nombre de messages entrants.   
3. S’il n’y a **AUCUN message entrant**, contactez votre partenaire d’appareil.  
4. S’il y a des **messages entrants**, contactez farmbeatssupport@microsoft.com, en fournissant les journaux du hub de données et de l’accélérateur ainsi que les données de télémétrie capturées.

Voir la [section Journaux](#collect-logs-manually) du document pour comprendre comment télécharger les journaux.  

### <a name="dont-have-the-eventhub-connection-string"></a>Chaîne de connexion du hub d’événements manquante

**Action corrective** : Accédez à Swagger DataHub et effectuez ces étapes :
1. Accédez à l’API partenaire
2. Cliquez sur GET -> Try it Out -> Execute (GET -> Essayer -> Exécuter)
3. Notez l’ID partenaire du partenaire de capteur qui vous intéresse
4. Revenez dans l’API partenaire et cliquez sur GET/{id}
5. Spécifiez l’ID noté à l’étape 3, puis cliquez sur Execute (Exécuter)
6. La réponse de l’API contient normalement la chaîne de connexion du hub d’événements

### <a name="device-appears-offline"></a>L’appareil apparaît hors connexion

**Symptômes** : Les appareils sont installés et vous avez lié FarmBeats à votre partenaire d’appareil. Les appareils sont en ligne et envoient des données de télémétrie, mais ils apparaissent Hors connexion.

**Action corrective** : L’intervalle de création de rapports n’est pas configurée pour cet appareil. Contactez le fabricant de votre appareil pour définir l’intervalle de création de rapports. 

### <a name="error-deleting-a-resource"></a>Erreur de suppression d’une ressource

Voici les scénarios d’erreur courants lors de la suppression d’un appareil :  

**Message** : L’appareil est référencé dans les capteurs : Un ou plusieurs capteurs sont associés à l’appareil. Supprimez les capteurs, puis supprimez l’appareil.  

**Signification** : L’appareil est associé à plusieurs capteurs déployés dans l’exploitation agricole.   

**Action corrective** :  

1. Supprimez les capteurs associés à l’appareil par l’intermédiaire de l’accélérateur.  
2. Au cas où vous souhaiteriez associer les capteurs à un autre appareil, demandez à votre partenaire d’appareil d’en faire de même.  
3. Supprimez l’appareil à l’aide d’un appel d’API DELETE en définissant le paramètre force sur « true ».  

**Message** : L’appareil est référencé dans les appareils en tant que ParentDeviceId : Un ou plusieurs appareils associés à cet appareil sont des appareils enfants. Supprimez-les, puis supprimez cet appareil.  

**Signification** : D’autres appareils sont associés à votre appareil.  

**Action corrective** :

1. Supprimez les appareils associés à l’appareil spécifique.  
2. Supprimer l’appareil spécifique  

    > [!NOTE]
    > Vous ne pouvez pas supprimer un appareil si des capteurs y sont associés. Pour plus d’informations sur la suppression de capteurs associés, consultez [Supprimer des capteurs](get-sensor-data-from-sensor-partner.md) dans le chapitre Obtenir des données de capteur.


## <a name="issues-with-jobs"></a>Problèmes liés aux travaux

### <a name="farmbeats-internal-error"></a>Erreur interne FarmBeats

**Message** : Erreur interne FarmBeats, consultez le guide de résolution des problèmes pour plus d’informations.

**Action corrective** : Cela peut être dû à une défaillance temporaire dans le pipeline de données. Recréez le travail. Si l’erreur persiste, ajoutez l’erreur dans un billet sur le forum de FarmBeats ou contactez FarmBeatsSupport@microsoft.com.

## <a name="accelerator-troubleshooting"></a>Résolution des problèmes des accélérateurs

### <a name="access-control"></a>Contrôle d’accès

**Erreur lors de l’ajout de l’attribution de rôle**

**Message** :Aucun utilisateur correspondant trouvé.

**Action corrective** : Vérifiez l’ID d’e-mail pour lequel vous essayez d’effectuer une attribution de rôle. L’ID d’e-mail doit correspondre exactement à celui inscrit pour cet utilisateur dans Active Directory. Si l’erreur persiste, ajoutez l’erreur dans un billet sur le forum de FarmBeats ou contactez FarmBeatsSupport@microsoft.com.

### <a name="unable-to-log-in-to-accelerator"></a>Impossible de se connecter à l’accélérateur

**Message** : Error: Vous n’êtes pas autorisé à appeler le service. Contactez l’administrateur pour obtenir une autorisation.

**Action corrective** : Demandez à l’administrateur de vous autoriser à accéder au déploiement FarmBeats. Pour ce faire, vous pouvez publier un BILLET des API RoleAssignment ou agir par le biais d’Access Control dans le volet Paramètres de l’accélérateur.  

Si vous avez déjà été ajouté et que vous êtes confronté à cette erreur, réessayez en actualisant la page.  Si l’erreur persiste, ajoutez l’erreur dans un billet sur le forum de FarmBeats ou contactez FarmBeatsSupport@microsoft.com.

![Projet FarmBeats](./media/troubleshooting-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator---an-unknown-error-occurred"></a>Accélérateur – Une erreur inconnue s’est produite  

**Message** : Error: Une erreur inconnue s’est produite.

**Action corrective** : Cela se produit si la page reste inactive trop longtemps. Actualisez la page.  

Si l’erreur persiste, ajoutez l’erreur dans un billet sur le forum de FarmBeats ou contactez FarmBeatsSupport@microsoft.com.

**L’accélérateur FarmBeats n’indique pas la dernière version, même après la mise à niveau de FarmBeatsDeployment**

**Action corrective** : Cela se produit en raison de la persistance du travail de service dans le navigateur.
Fermez tous les onglets de navigateur pour lesquels l’accélérateur est ouvert, puis fermez la fenêtre du navigateur. Démarrez une nouvelle instance du navigateur et rechargez l’URI de l’accélérateur. Cela chargera la nouvelle version de l’accélérateur.

## <a name="sentinel-imagery-related-issues"></a>Problèmes liés à l’imagerie d’Azure Sentinel

### <a name="sentinel-wrong-username-or-password"></a>Nom d’utilisateur ou mot de passe Azure Sentinel incorrect

**Message d’échec du travail** :Une authentification complète est nécessaire pour accéder à cette ressource.

**Action corrective** :Exécutez à nouveau le programme d’installation pour mettre à niveau le hub de données avec le nom d’utilisateur et le mot de passe corrects.

**Action corrective** :Exécutez à nouveau le travail défaillant ou exécutez un travail d’indices satellite pour une plage de dates de 5 à 7 jours et vérifiez si le travail est réussi.

### <a name="sentinel-hub-wrong-url-or-not-accessible"></a>URL du hub Azure Sentinel incorrecte ou inaccessible 

**Message d’échec du travail** :Un problème s’est produit. La page à laquelle vous tentez d’accéder est (temporairement) indisponible. 

**Action corrective** :
1.  Ouvrez l’URL Azure Sentinel (https://scihub.copernicus.eu/dhus/) dans le navigateur et vérifiez si le site web est accessible. 
2.  Si le site web n’est pas accessible, vérifiez si un pare-feu/réseau d’entreprise, etc. le bloque et prenez les mesures nécessaires pour autoriser l’URL ci-dessus. 
3.  Exécutez à nouveau le travail défaillant ou exécutez un travail d’indices satellite pour une plage de dates de 5 à 7 jours et vérifiez si le travail est réussi.  

### <a name="sentinel-server-down-for-maintenance"></a>Serveur Azure Sentinel hors service pour cause de maintenance

**Message d’échec du travail** : Le Copernicus Open Access Hub est momentanément indisponible. Nous sommes désolés pour ce désagrément, nous effectuons actuellement une opération de maintenance. Le portail sera bientôt de retour en ligne. 

**Action corrective** :

1.  Ce problème peut se produire si des activités de maintenance sont effectuées sur le serveur Azure Sentinel. 
2.  Si un travail/pipeline échoue à cause de la raison ci-dessus, renvoyez le travail après un certain temps. 
3.  L’utilisateur peut accéder à l’adresse https://scihub.copernicus.eu/news/ pour consulter les informations sur les activités de maintenance de Sentinel planifiées et non planifiées.  
4.  Exécutez à nouveau le travail défaillant ou exécutez un travail d’indices satellite pour une plage de dates de 5 à 7 jours et vérifiez si le travail est réussi.

### <a name="sentinel-maximum-number-of-connections-reached"></a>Nombre maximal de connexions Azure Sentinel atteint

**Message d’échec du travail** : Nombre maximal de deux flux simultanés atteints par l’utilisateur « <username> » 

**Action corrective** :
1.  Si un travail échoue à cause de la raison ci-dessus, le même compte Sentinel est utilisé dans un autre déploiement ou logiciel. 
2.  L’utilisateur peut créer un autre compte Sentinel et réexécuter le programme d’installation pour mettre à jour le hub de données avec le nouveau nom d’utilisateur/mot de passe Sentinel.  
3.  Exécutez à nouveau le travail défaillant ou exécutez un travail d’indices satellite pour une plage de dates de 5 à 7 jours et vérifiez si le travail est réussi.

### <a name="sentinel-server-refused-connection"></a>Connexion refusée par le serveur Azure Sentinel 

**Message d’échec du travail** :

Le serveur a refusé la connexion à : http://172.30.175.69:8983/solr/dhus 

**Action corrective** : Ce problème peut se produire si des activités de maintenance sont effectuées sur le serveur Azure Sentinel. 
1.  Si un travail/pipeline échoue à cause de la raison ci-dessus, renvoyez le travail après un certain temps. 
2.  L’utilisateur peut accéder à l’adresse https://scihub.copernicus.eu/news/ pour consulter les informations sur les activités de maintenance de Sentinel planifiées et non planifiées.  
3.  Exécutez à nouveau le travail défaillant ou exécutez un travail d’indices satellite pour une plage de dates de 5 à 7 jours et vérifiez si le travail est réussi.


## <a name="collect-logs-manually"></a>Collecter les journaux manuellement

[Installez et déployez]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) l’Explorateur Stockage Azure.

### <a name="how-to-collect-data-hub-adf-job-logs"></a>Comment collecter les journaux de travaux ADF du hub de données
1. Connectez-vous à https://portal.azure.com
2. Dans la zone de texte **Rechercher**, recherchez le groupe de ressources Hub de données FarmBeats.

    > [!NOTE]
    > Sélectionnez le groupe de ressources du hub de données qui a été spécifié au moment du déploiement de FarmBeats.

Dans le tableau de bord du groupe de ressources, recherchez le compte de stockage (datahublogs…). Par exemple, datahublogsmvxmq  

1.  Sélectionnez le compte de stockage dans la colonne **Nom** pour afficher le tableau de bord **Compte de stockage**.
2.  Dans la page (datahubblogs…), sélectionnez **Ouvrir dans Explorer** pour afficher l’application **Ouvrir Explorateur Stockage Azure**.
3.  Dans le volet gauche, (datahubblogs…), **Conteneurs d’objets blob**, sélectionnez **job-logs**.
4.  Dans l’onglet **job-logs**, sélectionnez **Télécharger**.
5.  Sélectionnez l’emplacement pour télécharger les journaux dans un dossier local sur votre ordinateur.
6.  Envoyez le fichier zip téléchargé à l’adresse farmbeatssupport@microsoft.com

    ![Projet FarmBeats](./media/troubleshooting-farmbeats/collecting-logs-manually-1.png)

### <a name="how-to-collect-accelerator-adf-job-logs"></a>Comment collecter les journaux de travaux de l’ADF de l’accélérateur

1.  Connectez-vous à https://portal.azure.com
2.  Dans la zone de texte **Rechercher**, recherchez le groupe de ressources Accélérateur FarmBeats.

    > [!NOTE]
    > Sélectionnez le groupe de ressources Accélérateur qui a été spécifié au moment du déploiement de FarmBeats.

3.  Dans le tableau de bord du groupe de ressources, recherchez le stockage… compte de stockage. Par exemple, storagedop4k
4.  Sélectionnez le compte de stockage dans la colonne **Nom** pour afficher le tableau de bord Compte de stockage.
5.  Dans la page (storage…), sélectionnez **Ouvrir dans Explorer** pour afficher l’application Ouvrir Explorateur Stockage Azure.
6.  Dans le volet gauche, (storage…), **Conteneurs d’objets blob**, sélectionnez **job-logs**.
7.  Dans l’onglet **job-logs**, sélectionnez **Télécharger**.
8.  Sélectionnez l’emplacement pour télécharger les journaux dans un dossier local sur votre ordinateur.
9.  Envoyez le fichier zip téléchargé à l’adresse farmbeatssupport@microsoft.com


### <a name="how-to-collect-data-hub-app-service-logs"></a>Comment collecter les journaux de service d’application du hub de données

1.  Connectez-vous à https://portal.azure.com
2.  Dans la zone de texte **Rechercher**, recherchez le groupe de ressources Hub de données FarmBeats.

    > [!NOTE]
    > Sélectionnez le groupe de ressources du hub de données qui a été spécifié au moment du déploiement de FarmBeats.

3.  Dans le groupe de ressources, recherchez le compte de stockage (datahublogs…). Par exemple, datahublogsmvxmq
4.  Sélectionnez le compte de stockage dans la colonne **Nom** pour afficher le tableau de bord **Compte de stockage**.
5.  Dans la page (datahubblogs…), sélectionnez **Ouvrir dans Explorer** pour afficher l’application **Ouvrir Explorateur Stockage Azure**.
6.  Dans le volet gauche, (datahubblogs…), **Conteneurs d’objets blob**, sélectionnez appinsights-logs.
7.  Dans l’onglet appinsights-logs, sélectionnez **Télécharger**.
8.  Sélectionnez le chemin d’accès pour télécharger les journaux dans un dossier local sur votre ordinateur.
9.  Envoyez le dossier téléchargé à l’adresse farmbeatssupport@microsoft.com

### <a name="how-to-collect-accelerator-app-service-logs"></a>Comment collecter les journaux de service d’application de l’accélérateur

1.  Connectez-vous à https://portal.azure.com
2.  Dans la zone de texte **Rechercher**, recherchez le groupe de ressources Accélérateur FarmBeats.

    > [!NOTE]
    > Sélectionnez le groupe de ressources Accélérateur FarmBeats fourni au moment du déploiement de FarmBeats.

3.  Dans le groupe de ressources, recherchez le compte de stockage (storage…). Par exemple, storagedop4k
4.  Sélectionnez le compte de stockage dans la colonne **Nom** pour afficher le tableau de bord **Compte de stockage**.
5.  Dans la page (storage…), sélectionnez **Ouvrir dans Explorer** pour afficher l’application **Ouvrir Explorateur Stockage Azure**.
6.  Dans le volet gauche, (storage…), **Conteneurs d’objets blob**, sélectionnez appinsights-logs.
7.  Dans l’onglet appinsights-logs, sélectionnez **Télécharger**.
8.  Sélectionnez l’emplacement pour télécharger les journaux dans un dossier local sur votre ordinateur.
9.  Envoyez le dossier téléchargé à l’adresse farmbeatssupport@microsoft.com

## <a name="known-issues"></a>Problèmes connus

## <a name="batch-related-issues"></a>Problèmes liés à Azure Batch

**Erreur** : Le quota régional de comptes Azure Batch pour l’abonnement spécifié a été atteint.

**Action corrective** : Augmentez le quota ou supprimez les comptes Batch inutilisés, puis réexécutez le déploiement.

### <a name="azure-active-directory-related-issues"></a>Problèmes liés à Azure Active Directory

**Erreur** : Impossible de mettre à jour les paramètres requis pour Azure AD App d41axx40-xx21-4fbd-8xxf-97xxx9e2xxc0 : Privilèges insuffisants pour effectuer l’opération. Assurez-vous que les paramètres ci-dessus sont configurés correctement pour Azure AD App.

**Signification** : La configuration de l’inscription de l’application Azure AD n’a pas été effectuée correctement.  

**Action corrective** : Demandez à l’administrateur informatique (avec accès en lecture du locataire) d’utiliser notre [script](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect/tree/master/AppCreationScripts) pour créer l’inscription de l’application Azure AD. Ce script s’occupera également des étapes de configuration.

**Erreur** : Impossible de créer un « dummyname » Active Directory App dans ce locataire : Un autre objet avec la même valeur pour les URI d’identificateur de propriété existe déjà

**Signification** : Une inscription de l’application Azure AD avec le même nom existe déjà.

**Action corrective** : Supprimez l’inscription existante de l’application Azure AD ou réutilisez-la pour l’installation. Si vous réutilisez l’inscription Azure AD existante, transmettez l’ID d’application et la clé secrète client au programme d’installation et redéployez.

## <a name="inputjson-related-issues"></a>Problèmes liés à input.json

**Erreur** de lecture de l’entrée à partir du fichier input.json

**Action corrective** : Ce problème est généralement dû au fait que le chemin d’accès ou le nom du fichier JSON d’entrée n’est pas spécifié correctement dans le programme d’installation. Effectuez les corrections appropriées et faites une nouvelle tentative de redéploiement.

**Erreur d’analyse de l’entrée json**

**Action corrective** : Ce problème se produit principalement en raison de valeurs incorrectes dans le fichier JSON d’entrée. Effectuez les corrections appropriées et faites une nouvelle tentative de déploiement.

## <a name="high-cpu-usage"></a>Utilisation élevée du processeur

**Erreur** : Vous recevez une alerte par e-mail qui fait référence à une alerte d’utilisation élevée de l’UC. 

**Action corrective** : 
1.  Accédez à votre groupe de ressources Hub de données FarmBeats.
2.  Sélectionnez le service d’application.  
3.  Accédez à Monter en puissance (plan App Service) et sélectionnez un [niveau tarifaire](https://azure.microsoft.com/pricing/details/app-service/windows/) approprié

## <a name="next-steps"></a>Étapes suivantes

Si les problèmes persistent, contactez-nous sur notre [forum de support](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats).
