---
title: Stockage Table Azure | Place de marché Azure
description: Configurer la gestion des prospects dans le stockage Table Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: dsindona
ms.openlocfilehash: cee28c65b97d4ac163f78b746dc7be9ee210843c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280352"
---
# <a name="lead-management-instructions-for-table-storage"></a>Instructions de gestion des prospects pour le stockage Table

Cet article explique comment configurer le stockage Table Azure pour gérer les prospects. Le stockage Table vous permet de stocker et de modifier les informations client.

## <a name="configure-table-storage"></a>Configuration du stockage de table

1. Si vous ne possédez pas de compte Azure, vous pouvez [créer un compte d’évaluation gratuit](https://azure.microsoft.com/pricing/free-trial/).
1. Une fois votre compte actif, connectez-vous au [portail Microsoft Azure](https://portal.azure.com).
1. Dans le portail Azure, procédez comme suit :  
    1. Dans le volet de gauche, sélectionnez **+ Créer une ressource**. Le volet **Nouveau** s’ouvre.
    1. Dans le volet **Nouveau**, sélectionnez **Stockage**. Une liste de **suggestions** s'ouvre à droite.
    1. Sélectionnez **Compte de stockage**. Suivez ensuite les instructions contenues dans [Créer un compte de stockage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

    ![Créer un compte de stockage Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

    Pour en savoir plus sur les comptes de stockage, consultez [Tutoriels de démarrage rapide](https://docs.microsoft.com/azure/storage/). Pour obtenir des informations de tarification, consultez [Tarification Stockage Azure](https://azure.microsoft.com/pricing/details/storage/).

1. Attendez que votre compte de stockage soit approvisionné, ce qui prend généralement quelques minutes. Ensuite, accédez au compte à partir de la page d’accueil du portail Azure : Sélectionnez **Voir toutes vos ressources** ou **Toutes les ressources** dans le volet de navigation.

    ![Accéder à votre compte de stockage Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azure-storage-access.png)

1. À partir du volet de votre compte de stockage, copiez la chaîne de connexion du compte de stockage pour la clé. Collez-la dans le champ **Chaîne de connexion** pour le compte de stockage dans la portail Cloud Partner.

    Exemple de chaîne de connexion :

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

      ![Clé de stockage Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

Vous pouvez utiliser l'[Explorateur Stockage Microsoft Azure](https://azurestorageexplorer.codeplex.com/) ou tout autre outil pour afficher les données de votre stockage de table. Vous pouvez également en exporter des données.

## <a name="use-microsoft-flow-with-table-storage-optional"></a>Utiliser Microsoft Flow avec Stockage Table (*facultatif*)

Vous pouvez utiliser [Microsoft Flow](https://docs.microsoft.com/flow/) pour envoyer automatiquement des notifications lorsqu’un prospect est ajouté à votre stockage de table. Si vous n’avez pas encore de compte Microsoft Flow, [inscrivez-vous pour bénéficier d’un compte gratuit](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Exemple de notification de prospect

Cet exemple montre comment créer un flux de base. Le flux envoie automatiquement une notification par e-mail lorsque de nouveaux prospects sont ajoutés à votre stockage de table.

1. Connectez-vous à votre compte Microsoft Flow.
1. À gauche du volet de navigation, sélectionnez **Mes flux**.
1. Dans la barre de navigation supérieure, sélectionnez **+ Nouveau**.  
1. Dans la liste déroulante, sélectionnez **+ Créer entièrement**.
1. Sous **Créer un flux entièrement**, sélectionnez **Créer entièrement**.

   ![Créer un flux entièrement](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-create-from-blank.png)

1. Dans la barre de recherche des connecteurs et des déclencheurs, sélectionnez **Déclencheurs**.
1. Sous **Déclencheurs**, sélectionnez **Récurrence**.
1. Dans la fenêtre **Récurrence**, conservez le paramètre par défaut **1** comme **intervalle**. Dans la liste déroulante **Fréquence**, sélectionnez **Heure**.

   >[!NOTE] 
   >Cet exemple utilise un intervalle d’une heure. Vous pouvez cependant sélectionner un intervalle et une fréquence mieux adaptés aux besoins de votre entreprise.

   ![Définir une fréquence de 1 heure pour la récurrence](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-recurrence-dropdown.png)

1. Sélectionnez **+ Nouvelle étape**.
1. Recherchez **Obtenir l’heure passée**, puis sélectionnez **Obtenir l’heure passée** sous **Choisir une action**.

    ![Rechercher et sélectionner l’action « Obtenir l'heure passée »](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-search-getpasttime.png)

1. Dans la fenêtre **Obtenir l’heure passée**, définissez l'**intervalle** sur **1**.  Dans la liste déroulante **Unité de temps**, sélectionnez **Heure**.
    >[!IMPORTANT] 
    >Assurez-vous que l'**intervalle** et l'**unité de temps** correspondent à l’intervalle et à la fréquence que vous avez configurés pour la récurrence (étape 8).

    ![Définir l’intervalle Obtenir l'heure passée](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getpast-time.png)

    >[!TIP] 
    >Vous pouvez vérifier votre flux à tout moment pour vous assurer que chaque étape est correctement configurée : Sélectionnez **Vérificateur de flux** dans la barre de menus Flux.

Dans les étapes suivantes, vous allez vous connecter à votre table de stockage et configurer la logique de traitement afin de gérer de nouveaux prospects.

1. Après l’étape **Obtenir l'heure passée**, sélectionnez **+ Nouvelle étape**, puis recherchez **Obtenir des entités**.
1. Sous **Actions**, sélectionnez **Obtenir des entités**, puis sélectionnez **Afficher les options avancées**.
1. Dans la fenêtre **Obtenir des entités**, complétez les champs suivants :

   - **Table** : nom de votre stockage de table. L’image suivante montre « MarketPlaceLeads » entré :

     ![Choisir une valeur personnalisée pour le nom de la table Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-table-name.png)

   - **Requête de filtre** : lorsque vous sélectionnez ce champ, l’icône **Obtenir l’heure passée** s'affiche dans une fenêtre contextuelle. Sélectionnez **Heure passée** afin d'utiliser cette valeur en tant que timestamp pour filtrer la requête. Vous pouvez également coller la fonction suivante dans le champ :
   
      `CreatedTime Timestamp gt datetime'@{body('Get_past_time')}'` 

     ![Configurer la fonction de requête de filtre](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-filterquery.png)

1. Sélectionnez **Nouvelle étape** pour ajouter une condition afin de rechercher de nouveaux prospects dans votre stockage de table.

   ![Utiliser « Nouvelle étape » pour ajouter une condition à l’analyse du stockage de table](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-add-filterquery-new-step.png)

1. Dans la fenêtre **Choisir une action**, choisissez **Actions**, puis **Contrôle des conditions**.

     ![Ajouter un contrôle de condition](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-action-condition-control.png)

1. Dans la fenêtre **Condition**, sélectionnez **Choisir une valeur**, puis **Expression** dans la fenêtre contextuelle.
1. Collez `length(body('Get_entities')?['value'])` dans le champ ***fx***. Sélectionnez **OK** pour ajouter cette fonction. 



     ![Ajouter une fonction à la condition](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-fx0.png)

1. Définissez l’action à exécuter selon le résultat de la condition.

    1. Sélectionnez **est supérieur à** dans la liste déroulante.
   1. Entrez **0** comme valeur.

     ![Définir une action selon les résultats de la condition](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-pick-action.png)

1. Si la condition affiche « If no », vous n’avez rien à faire.

    Si la condition affiche « If yes », déclenchez une action pour vous connecter à votre compte Office 365 afin d’envoyer un message électronique :
   1. Sélectionnez **Ajouter une action**.
   1. Sélectionnez **Envoyer un message électronique**.
   1. Dans la fenêtre **Envoyer un message électronique**, complétez les champs suivants :

      - **À** : adresse e-mail de toutes les personnes qui recevront la notification.
      - **Objet** : objet du courrier. Par exemple : *Nouveaux prospects !*
      - **Corps** : texte que vous souhaitez inclure dans chaque courrier (facultatif). Collez-le également dans `body('Get_entities')?['value']` comme fonction pour insérer des informations sur les prospects.

        >[!NOTE] 
        >Vous pouvez insérer d’autres points de données statiques ou dynamiques dans le corps de ce courrier.

      ![Configurer la messagerie pour la notification des prospects](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

1. Sélectionnez **Enregistrer** pour enregistrer le flux. Microsoft Flow vérifie automatiquement s'il contient des erreurs. Si aucune erreur n’est détectée, le flux s’exécute dès qu’il est enregistré.

    L'image suivante montre à quoi doit ressembler le flux final.

    [![Séquence de flux finale](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end-thmb.png)](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

    (*Sélectionnez l’image pour l’agrandir.* )

### <a name="manage-your-flow"></a>Gérer votre flux

Vous pouvez facilement gérer votre flux une fois celui-ci en cours d'exécution. Vous disposez d’un contrôle total sur votre flux. Par exemple, vous pouvez l’arrêter le modifier, afficher un historique des exécutions et obtenir des données analytiques. L'image suivante montre les options de gestion de flux.

 ![Options de gestion de flux](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

Le flux continue de s’exécuter jusqu'à ce que vous sélectionniez **Désactiver le flux**.

Si vous ne recevez aucune notification de prospect par courrier, cela signifie qu'aucun nouveau prospect n’a été ajouté au stockage de table.
En cas d'échec du flux, vous recevez un courrier similaire à l'exemple suivant :

 ![Notification par e-mail d’une erreur de flux](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)

## <a name="next-steps"></a>Étapes suivantes

[Configurer des prospects](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)
