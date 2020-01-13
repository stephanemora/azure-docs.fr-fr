---
title: Résoudre les problèmes liés à Azure FarmBeats
description: Cet article explique comment résoudre les problèmes liés à Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: f017b19ef8bd8e4c44d9e2885da6fbaf172808a1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75476827"
---
# <a name="troubleshoot"></a>Dépanner

Cet article fournit des solutions aux problèmes courants liés à Azure FarmBeats.

Pour obtenir de l’aide supplémentaire, contactez-nous à l’adresse farmbeatssupport@microsoft.com. Veillez à inclure le fichier **deployer.log** dans votre e-mail.

Pour télécharger le fichier **deployer.log**, effectuez les étapes suivantes :

1. Connectez-vous au **portail Azure** et sélectionnez votre abonnement et votre locataire Azure AD.
2. Lancez Cloud Shell dans le volet de navigation supérieur du Portail Azure.
3. Sélectionnez **Bash** comme expérience Cloud Shell préférée.
4. Sélectionnez l’icône en surbrillance puis, dans la liste déroulante, sélectionnez **Télécharger**.

    ![Projet FarmBeats](./media/troubleshoot-azure-farmbeats/download-deployer-log-1.png)

5. Dans le volet suivant, entrez le chemin de votre fichier **deployer.log**. Par exemple, entrez **farmbeats-deployer.log**.

## <a name="sensor-telemetry"></a>Données de télémétrie des capteurs

### <a name="cant-view-telemetry-data"></a>Impossible d’afficher les données de télémétrie

**Symptôme** : Des appareils ou des capteurs sont déployés, et vous avez lié FarmBeats à votre partenaire d’appareil. Toutefois, vous ne pouvez ni obtenir ni afficher les données de télémétrie sur FarmBeats.

**Action corrective** :

1. Accédez à votre groupe de ressources FarmBeats Datahub.   
2. Sélectionnez le **hub d’événements** (DatafeedEventHubNamespace), puis vérifiez le nombre de messages entrants.
3. Effectuez l'une des opérations suivantes :   
   - S’il n’y a *aucun message entrant*, contactez votre partenaire d’appareil.  
   - Si des *messages entrants* sont présents, contactez farmbeatssupport@microsoft.com. Attachez vos journaux DataHub et Accelerator et les données de télémétrie capturées.

Pour comprendre comment télécharger les journaux, consultez la section [« Collecter les journaux manuellement »](#collect-logs-manually).  

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>Chaîne de connexion Azure Event Hubs indisponible

**Action corrective** :

1. Dans Datahub Swagger, accédez à l’API Partner.
2. Sélectionnez **Get** (Obtenir) > **Try it out (Essayer)**  > **Execute** (Exécuter).
3. Notez l’ID partenaire du partenaire de capteur qui vous intéresse.
4. Revenez dans l’API Partner et sélectionnez **Get/\<ID>** .
5. Spécifiez l’ID partenaire obtenue à l’étape 3, puis sélectionnez **Exécuter**.

   La réponse de l’API contient normalement la chaîne de connexion Event Hubs.

### <a name="device-appears-offline"></a>L’appareil apparaît hors connexion

**Symptômes** : Les appareils sont installés et vous avez lié FarmBeats à votre partenaire d’appareil. Les appareils sont en ligne et envoient des données de télémétrie, mais ils apparaissent comme étant hors connexion.

**Action corrective** : L’intervalle de création de rapports n’est pas configuré pour cet appareil. Pour définir l’intervalle de création de rapports, contactez le fabricant de votre appareil. 

### <a name="error-deleting-a-device"></a>Erreur de suppression d’un appareil

Quand vous supprimez un appareil, vous pouvez être confronté à l’un des scénarios d’erreur suivants :  

**Message** : « L’appareil est référencé dans les capteurs : Un ou plusieurs capteurs sont associés à l’appareil. Supprimez les capteurs, puis supprimez l’appareil. »  

**Signification** : L’appareil est associé à plusieurs capteurs déployés dans la ferme.   

**Action corrective** :  

1. Supprimez les capteurs associés à l’appareil avec Accelerator.  
2. Si vous souhaitez associer les capteurs à un autre appareil, demandez à votre partenaire d’appareil d’en faire de même.  
3. Supprimez l’appareil à l’aide d’un appel `DELETE API`, puis définissez le paramètre force avec la valeur *true*.  

**Message** : « L’appareil est référencé dans les appareils en tant que ParentDeviceId : Un ou plusieurs appareils associés à cet appareil sont des appareils enfants. Supprimez-les, puis supprimez cet appareil. »  

**Signification** : D’autres appareils sont associés à votre appareil.  

**Action corrective** :

1. Supprimez les appareils associés à cet appareil spécifique.  
2. Supprimez l’appareil spécifique.  

    > [!NOTE]
    > Vous ne pouvez pas supprimer un appareil s’il est associé à des capteurs. Pour plus d’informations sur la suppression des capteurs associés, consultez la section « Supprimer un capteur » dans [Obtenir des données de capteur auprès de partenaires de capteur](get-sensor-data-from-sensor-partner.md).


## <a name="issues-with-jobs"></a>Problèmes liés aux travaux

### <a name="farmbeats-internal-error"></a>Erreur interne FarmBeats

**Message** : « Erreur interne FarmBeats, consultez le guide de résolution des problèmes pour plus d’informations. »

**Action corrective** : Ce problème peut provenir d’une défaillance temporaire dans le pipeline de données. Recréez le travail. Si l’erreur persiste, ajoutez le message d’erreur dans un billet sur le forum de FarmBeats ou contactez FarmBeatsSupport@microsoft.com.

## <a name="accelerator-troubleshooting"></a>Résolution des problèmes des accélérateurs

### <a name="access-control"></a>Contrôle d’accès

**Problème** : Vous recevez une erreur pendant que vous ajoutez une attribution de rôle.

**Message** : « Aucun utilisateur correspondant trouvé. »

**Action corrective** : Vérifiez l’ID d’e-mail pour lequel vous essayez d’ajouter une attribution de rôle. L’ID d’e-mail doit correspondre exactement à celui inscrit pour cet utilisateur dans Active Directory. Si l’erreur persiste, ajoutez le message d’erreur dans un billet sur le forum de FarmBeats ou contactez FarmBeatsSupport@microsoft.com.

### <a name="unable-to-log-in-to-accelerator"></a>Impossible de se connecter à l’accélérateur

**Message** : « Erreur : Vous n’êtes pas autorisé à appeler le service. Contactez l’administrateur pour obtenir une autorisation. »

**Action corrective** : Demandez à l’administrateur de vous autoriser à accéder au déploiement FarmBeats. Pour ce faire, vous pouvez effectuer un POST des API RoleAssignment ou utiliser Access Control dans le volet **Settings** (Paramètres) d’Accelerator.  

Si l’accès vous a déjà été accordé et que vous êtes confronté à cette erreur, réessayez en actualisant la page. Si l’erreur persiste, ajoutez le message d’erreur dans un billet sur le forum de FarmBeats ou contactez FarmBeatsSupport@microsoft.com.

![Projet FarmBeats](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Problèmes liés à Accelerator  

**Problème** : Vous recevez une erreur Accelerator dont la cause est inconnue.

**Message** : « Erreur : Une erreur inconnue s’est produite. »

**Action corrective** : Cette erreur se produit si la page reste inactive trop longtemps. Actualisez la page.  

Si l’erreur persiste, ajoutez le message d’erreur dans un billet sur le forum de FarmBeats ou contactez FarmBeatsSupport@microsoft.com.

**Problème** : FarmBeats Accelerator n’indique pas la dernière version, même après la mise à niveau de FarmBeatsDeployment.

**Action corrective** : Cette erreur se produit en raison de la persistance du rôle de travail du service dans le navigateur. Effectuez les actions suivantes :
1. Fermez tous les onglets de navigateur pour lesquels Accelerator est ouvert, puis fermez la fenêtre du navigateur.
2. Démarrez une nouvelle instance du navigateur et rechargez l’URI d’Accelerator. Cette action charge la nouvelle version d’Accelerator.

## <a name="sentinel-imagery-related-issues"></a>Sentinel : Problèmes liés à l’imagerie

### <a name="wrong-username-or-password"></a>Nom d’utilisateur ou mot de passe incorrect

**Message d’échec du travail** : « Une authentification complète est nécessaire pour accéder à cette ressource. »

**Action corrective** :

Effectuez l’une des actions suivantes :
- Réexécutez le programme d’installation pour mettre à niveau Datahub avec le nom d’utilisateur et le mot de passe corrects.
- Réexécutez le travail ayant échoué ou exécutez un travail d’indices satellite pour une plage de dates de 5 à 7 jours, puis vérifiez si le travail est réussi.

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Hub Sentinel : URL ou site incorrect inaccessible 

**Message d’échec du travail** : « Désolé, une erreur s’est produite. La page à laquelle vous tentez d’accéder est (temporairement) indisponible. » 

**Action corrective** :
1. Ouvrez [Sentinel](https://scihub.copernicus.eu/dhus/) dans votre navigateur pour voir si le site web est accessible. 
2. Si le site web n’est pas accessible, vérifiez si un pare-feu, un réseau d’entreprise ou tout autre logiciel de blocage empêche l’accès au site web, puis prenez les mesures nécessaires pour autoriser l’URL de Sentinel. 
3. Réexécutez le travail ayant échoué ou exécutez un travail d’indices satellite pour une plage de dates de 5 à 7 jours, puis vérifiez si le travail est réussi.  

### <a name="sentinel-server-down-for-maintenance"></a>Serveur Sentinel : Hors service pour cause de maintenance

**Message d’échec du travail** : « Le hub d’accès Copernicus Open sera bientôt disponible ! Nous sommes désolés pour ce désagrément, nous effectuons actuellement une opération de maintenance. Le portail sera bientôt de retour en ligne. » 

**Action corrective** :

Ce problème peut se produire si des activités de maintenance sont effectuées sur le serveur Sentinel.

1. Si un travail ou un pipeline échoue en raison d’une maintenance en cours d’exécution, soumettez à nouveau le travail après un certain temps. 

   Pour plus d’informations sur les activités de maintenance Sentinel planifiées ou non, accédez au site [Copernicus Open Access Hub News](https://scihub.copernicus.eu/news/).  
2. Réexécutez le travail ayant échoué ou exécutez un travail d’indices satellite pour une plage de dates de 5 à 7 jours, puis vérifiez si le travail est réussi.

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinel : Nombre maximal de connexions Sentinel atteint

**Message d’échec du travail** : « Nombre maximal de deux flux simultanés atteints par l’utilisateur ’\<nomutilisateur>’. »

**Signification** : Si un travail échoue car le nombre maximal de connexions a été atteint, le même compte Sentinel est utilisé dans un autre déploiement de logiciel.

**Action corrective** : Effectuez l’une des actions suivantes :
* Créez un compte Sentinel, puis réexécutez le programme d’installation pour mettre à niveau DataHub avec un nouveau nom d’utilisateur et un nouveau mot de passe Sentinel.  
* Réexécutez le travail ayant échoué ou exécutez un travail d’indices satellite pour une plage de dates de 5 à 7 jours, puis vérifiez si le travail est réussi.

### <a name="sentinel-server-refused-connection"></a>Serveur Sentinel : Connexion refusée 

**Message d’échec du travail** : « Le serveur a refusé la connexion à :  http://172.30.175.69:8983/solr/dhus. » 

**Action corrective** : Ce problème peut se produire si des activités de maintenance sont effectuées sur le serveur Sentinel. 
1. Si un travail ou un pipeline échoue en raison d’une maintenance en cours d’exécution, soumettez à nouveau le travail après un certain temps. 

   Pour plus d’informations sur les activités de maintenance Sentinel planifiées ou non, accédez au site [Copernicus Open Access Hub News](https://scihub.copernicus.eu/news/).  
2. Réexécutez le travail ayant échoué ou exécutez un travail d’indices satellite pour une plage de dates de 5 à 7 jours, puis vérifiez si le travail est réussi.

## <a name="collect-logs-manually"></a>Collecter les journaux manuellement

[Installez et déployez l’Explorateur Stockage Azure]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

### <a name="collect-azure-data-factory-job-logs-in-datahub"></a>Collecter les journaux de travaux Azure Data Factory dans Datahub
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans la zone **Rechercher**, recherchez le groupe de ressources FarmBeats Datahub.

    > [!NOTE]
    > Sélectionnez le groupe de ressources Datahub que vous avez spécifié durant l’installation de FarmBeats.

3. Dans le tableau de bord **Groupe de ressources**, recherchez le compte de stockage *datahublogs\** . Par exemple, recherchez **datahublogsmvxmq**.  
4. Dans la colonne **Nom**, sélectionnez le compte de stockage pour afficher le tableau de bord **Compte de stockage**.
5. Dans le volet **datahubblogs\*** , sélectionnez **Ouvrir dans l’Explorateur** pour afficher l’application **Explorateur Stockage Azure**.
6. Dans le volet gauche, sélectionnez **Conteneurs d’objets blob**, puis **job-logs**.
7. Sous l’onglet **job-logs**, sélectionnez **Télécharger**.
8. Téléchargez les journaux dans un dossier local sur votre ordinateur.
9. Envoyez le fichier .zip téléchargé par e-mail à l’adresse farmbeatssupport@microsoft.com.

    ![Projet FarmBeats](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-in-accelerator"></a>Collecter les journaux de travaux Azure Data Factory dans Accelerator

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans la zone **Rechercher**, recherchez le groupe de ressources FarmBeats Accelerator.

    > [!NOTE]
    > Sélectionnez le groupe de ressources Accelerator que vous avez spécifié durant l’installation de FarmBeats.

3. Dans le tableau de bord **Groupe de ressources**, recherchez le compte de stockage *storage\** . Par exemple, recherchez **storagedop4k\*** .
4. Sélectionnez le compte de stockage dans la colonne **Nom** pour afficher le tableau de bord **Compte de stockage**.
5. Dans le volet **storage\*** , sélectionnez **Ouvrir dans l’Explorateur** pour ouvrir l’application Explorateur Stockage Azure.
6. Dans le volet gauche, sélectionnez **Conteneurs d’objets blob**, puis **job-logs**.
7. Sous l’onglet **job-logs**, sélectionnez **Télécharger**.
8. Téléchargez les journaux dans un dossier local sur votre ordinateur.
9. Envoyez le fichier .zip téléchargé par e-mail à l’adresse farmbeatssupport@microsoft.com.


### <a name="collect-datahub-app-service-logs"></a>Collecter les journaux de service d’application Datahub

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans la zone **Rechercher**, recherchez le groupe de ressources FarmBeats Datahub.

    > [!NOTE]
    > Sélectionnez le groupe de ressources Datahub que vous avez spécifié durant l’installation de FarmBeats.

3. Dans le groupe de ressources, recherchez le compte de stockage *datahublogs\** . Par exemple, recherchez **fordatahublogsmvxmq\*** .
4. Sélectionnez le compte de stockage dans la colonne **Nom** pour afficher le tableau de bord **Compte de stockage**.
5. Dans le volet **datahubblogs\*** , sélectionnez **Ouvrir dans l’Explorateur** pour ouvrir l’application Explorateur Stockage Azure.
6. Dans le volet gauche, sélectionnez **Conteneurs d’objets blob**, puis **appinsights-logs**.
7. Dans le volet **appinsights-logs**, sélectionnez **Télécharger**.
8. Téléchargez les journaux dans un dossier local sur votre ordinateur.
9. Envoyez le fichier .zip téléchargé par e-mail à l’adresse farmbeatssupport@microsoft.com.

### <a name="collect-accelerator-app-service-logs"></a>Collecter les journaux de service d’application Accelerator

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans la zone **Rechercher**, recherchez le groupe de ressources FarmBeats Accelerator.

    > [!NOTE]
    > Sélectionnez le groupe de ressources FarmBeats Accelerator fourni durant l’installation de FarmBeats.

3. Dans le groupe de ressources, recherchez le compte de stockage *storage\** . Par exemple, recherchez **storagedop4k\*** .
4. Sélectionnez le compte de stockage dans la colonne **Nom** pour afficher le tableau de bord **Compte de stockage**.
5. Dans le volet **storage\*** , sélectionnez **Ouvrir dans l’Explorateur** pour ouvrir l’application Explorateur Stockage Azure.
6. Dans le volet gauche, sélectionnez **Conteneurs d’objets blob**, puis **appinsights-logs**.
7. Dans le volet **appinsights-logs**, sélectionnez **Télécharger**.
8. Téléchargez les journaux dans un dossier local sur votre ordinateur.
9. Envoyez le dossier téléchargé par e-mail à l’adresse farmbeatssupport@microsoft.com.

## <a name="known-issues"></a>Problèmes connus

## <a name="batch-related-issues"></a>Problèmes liés à Azure Batch

**Message d’erreur** : « Le quota régional de comptes Batch pour l’abonnement spécifié a été atteint. »

**Action corrective** : Augmentez le quota ou supprimez les comptes Batch inutilisés, puis réexécutez le déploiement.

### <a name="azure-active-directory-azure-ad-related-issues"></a>Problèmes liés à Azure Active Directory (Azure AD)

**Message d’erreur** : « Impossible de mettre à jour les paramètres requis pour Azure AD App d41axx40-xx21-4fbd-8xxf-97xxx9e2xxc0 : Privilèges insuffisants pour effectuer l’opération. Vérifiez que les paramètres ci-dessus sont configurés correctement pour Azure AD App. »

**Signification** : La configuration de l’inscription de l’application Azure AD n’a pas été effectuée correctement.  

**Action corrective** : Demandez à l’administrateur informatique (personne avec accès en lecture du locataire) d’utiliser notre [script](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect/tree/master/AppCreationScripts) pour créer l’inscription de l’application Azure AD. Ce script s’occupe également des étapes de configuration.

**Message d’erreur** : « Impossible de créer une application Active Directory ’\<nom d’application\>’ dans ce locataire : Un autre objet avec la même valeur pour les URI d’identificateur de propriété existe déjà. »

**Signification** : Une inscription d’application Azure AD portant le même nom existe déjà.

**Action corrective** : Supprimez l’inscription existante de l’application Azure AD ou réutilisez-la pour l’installation. Si vous réutilisez l’inscription d’application AzureAD existante, passez l’ID d’application et le secret client au programme d’installation et reprocédez au déploiement.

## <a name="issues-with-the-inputjson-file"></a>Problèmes avec le fichier input.json

**Erreur** : Erreur de lecture de l’entrée à partir du fichier *input.json*.

**Action corrective** : Ce problème est souvent dû au fait que le chemin ou le nom du fichier *input.json* n’est pas spécifié correctement dans le programme d’installation. Effectuez les corrections appropriées et faites une nouvelle tentative de déploiement.

**Erreur** : Erreur lors de l’analyse des valeurs dans le fichier *input.json*.

**Action corrective** : Ce problème se produit principalement en raison de valeurs incorrectes dans le fichier *input.json*. Effectuez les corrections appropriées et faites une nouvelle tentative de déploiement.

## <a name="high-cpu-usage"></a>Utilisation élevée du processeur

**Erreur** : Vous recevez une alerte par e-mail qui fait référence à une **alerte d’utilisation élevée du processeur**. 

**Action corrective** : 
1. Accédez à votre groupe de ressources FarmBeats Datahub.
2. Sélectionnez **App Service**.  
3. Accédez à la [page des prix App Service](https://azure.microsoft.com/pricing/details/app-service/windows/) Scale Up, puis sélectionnez un niveau tarifaire approprié.

## <a name="next-steps"></a>Étapes suivantes

Si vous rencontrez toujours des problèmes avec FarmBeats, contactez notre [forum de support](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats).
