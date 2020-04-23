---
title: Table Azure | Place de marché Azure
description: Configurer la gestion des prospects pour Azure Table.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: a36c411b9ababc42adb51d82a316df4252c01e24
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81251995"
---
# <a name="configure-lead-management-using-an-azure-table"></a>Configuration de la gestion des prospects à l’aide d’une table Azure

Si votre système de gestion de la relation client (CRM) n’est pas explicitement pris en charge dans l’Espace partenaires afin de recevoir des prospects de la Place de marché Azure et d’AppSource, vous pouvez utiliser une Table Azure pour gérer ces prospects. Vous pouvez ensuite décider d’exporter les données et de les importer dans votre système CRM. Les instructions de cet article décrivent le processus de création d’un compte de Stockage Azure et d’une Table Azure sous ce compte. De plus, vous pouvez créer un flux à l’aide de Microsoft Flow pour envoyer une notification par e-mail quand votre offre reçoit un prospect.

## <a name="configure-azure-table"></a>Configurer Table Azure

1. Si vous ne possédez pas de compte Azure, vous pouvez [créer un compte d’évaluation gratuit](https://azure.microsoft.com/pricing/free-trial/).
2. Une fois que votre compte Azure est actif, connectez-vous au [portail Microsoft Azure](https://portal.azure.com).
3. Dans le portail Microsoft Azure, créez un compte de stockage à l’aide de la procédure suivante.  
    1. Dans la barre de menus de gauche, sélectionnez **+ Créer une ressource**.  Le volet **Nouveau** (panneau) est affiché sur la droite.
    2. Sélectionnez **Stockage** dans le volet **Nouveau**.  Une liste **Sélection** est affichée sur la droite.
    3. Sélectionnez **Compte de stockage** pour commencer la création du compte.  Suivez les instructions de l’article [Créer un compte de stockage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

        ![Procédure de création d’un compte Azure Storage](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png)

        Pour en savoir plus sur les comptes de stockage, sélectionnez [Tutoriel de démarrage rapide](https://docs.microsoft.com/azure/storage/).  Pour plus d’informations sur la tarification du stockage, consultez [Tarification de Storage](https://azure.microsoft.com/pricing/details/storage/).

4. Attendez que votre compte de stockage soit approvisionné (ce processus prend généralement quelques minutes).  Ensuite, accédez à votre compte de stockage à partir de la page **Accueil** du portail Microsoft Azure, en sélectionnant **Voir toutes vos ressources** ou **Toutes les ressources** dans la barre de menus de navigation de gauche du Portail Microsoft Azure.

    ![Accéder à votre compte de stockage Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png)

5. Dans le volet de votre compte de stockage, sélectionnez **Clés d’accès**, puis copiez la valeur *Chaîne de connexion* pour la clé. Enregistrez cette valeur, car il s’agit de la valeur de *Chaîne de connexion de compte de stockage* que vous devez fournir dans le portail de publication afin de recevoir des prospects pour votre offre de la Place de marché. 

    Voici un exemple de chaîne de connexion :

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

    ![Clé de stockage Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png)

6. Dans le volet de votre compte de stockage, sélectionnez **Tables**, puis **+Table** pour créer une table. Entrez un nom pour votre table et sélectionnez **OK**. Enregistrez cette valeur car vous en aurez besoin si vous voulez configurer un Flux MS pour qu’il reçoive des notifications par e-mail lors de la réception de prospects.

    ![Tables Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    Vous pouvez utiliser l’[Explorateur de stockage Microsoft Azure](https://archive.codeplex.com/?p=azurestorageexplorer) ou tout autre outil pour afficher de votre table de stockage. Vous pouvez également exporter les données de la Table Azure. 

## <a name="optional-use-microsoft-flow-with-an-azure-table"></a>(Facultatif) Utiliser Microsoft Flow avec une Table Azure  

Vous pouvez utiliser [Microsoft Flow](https://docs.microsoft.com/flow/) pour automatiser les notifications chaque fois qu’un prospect est ajouté à la table Azure. Si vous ne possédez pas encore de compte, vous pouvez [vous inscrire pour en obtenir un gratuitement](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Exemple de notification de prospect

Utilisez cet exemple comme guide pour créer un simple flux qui envoie automatiquement une notification par e-mail lorsqu’un prospect est ajouté à une table Azure. Cet exemple définit une récurrence pour envoyer toutes les heures des informations sur le prospect si le stockage de la table est mis à jour.

1. Connectez-vous à votre compte Microsoft Flow.
2. Dans la barre de navigation de gauche, sélectionnez **Mes flux**.
3. Dans la barre de navigation supérieure, sélectionnez **+ Nouveau**.  
4. Dans la liste déroulante, sélectionnez **+ Planifié entièrement**

   ![Mes flux **+ Planifié entièrement**](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

5.    Dans la fenêtre *Créer un flux planifié*, sélectionnez l’intervalle « 1 » et la fréquence « heure » sous *Répéter tou(te)s les*. Par ailleurs, attribuez un nom au flux si vous le souhaitez. Sélectionnez **Create** (Créer).

>[!Note]
>Même si cet exemple utilise un intervalle d’une heure, vous pouvez sélectionner l’intervalle et la fréquence les plus adaptés à vos besoins professionnels.

![Créez un flux planifié.](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

6. Sélectionnez **+ Nouvelle étape**.
7. Dans la fenêtre *Choisir une action*, recherchez « obtenir l’heure passée », puis sélectionnez **Obtenir l’heure passée** sous Actions.

   ![Choisissez une action.](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

8. Dans la fenêtre **Obtenir l’heure passée**, choisissez la valeur 1 comme **intervalle**. Dans la liste déroulante **Unité de temps**, sélectionnez **Heure**.

    >[!Important]
    >Assurez-vous que cette unité d’intervalle et d’heure correspond à l’intervalle et à la fréquence que vous avez configurés pour la récurrence à l’étape 5.

    ![Définir l’intervalle Obtenir l'heure passée](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

>[!TIP] 
>Vous pouvez vérifier votre flux à tout moment pour confirmer que chaque étape est correctement configurée. Pour vérifier votre flux, sélectionnez **Vérificateur de flux** dans la barre de menus Flux.

Dans les étapes suivantes, vous allez vous connecter à votre table Azure et configurer la logique de traitement des nouveaux prospects.

9. Après l’étape Obtenir l’heure passée, sélectionnez **+ Nouvelle étape**, puis recherchez « Obtenir des entités » dans la fenêtre *Choisir une action*.
10. Sous **Actions**, sélectionnez **Obtenir des entités (Stockage Table Azure)** .
11.    Dans la fenêtre **Stockage Table Azure**, fournissez les informations dans les champs suivants, puis sélectionnez **Créer** :
* *Nom de la connexion* : donnez un nom significatif à la connexion que vous établissez entre ce flux et la table Azure.
* *Nom du compte de stockage* : indiquez le nom du compte de stockage pour votre table Azure. Vous trouverez cette information sur la page **Clés d’accès** du compte de stockage.
* *Clé de stockage partagée* : indiquez la valeur de clé de votre compte de stockage pour votre table Azure. Vous trouverez cette information sur la page **Clés d’accès** du compte de stockage.
    ![Stockage Table Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png).

La fenêtre *Obtenir des entités* s’affiche une fois que vous avez cliqué sur Créer. Sélectionnez **Afficher les options avancées** et complétez les champs suivants :
* *Table* : sélectionnez le nom de votre Stockage Table Azure (à partir de l’étape 6 des instructions de configuration d’une table Azure). La capture d’écran suivante montre l’invite lorsque la table « marketplaceleads » est sélectionnée pour cet exemple.
    ![Table Azure - Obtenir des entités](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png).

* *Requête de filtre* : sélectionnez ce champ et collez-y cette fonction : `Timestamp gt datetime'@{body('Get_past_time')}'` ![Table Azure - Obtenir des entités - Requête de filtre](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png).

12. Maintenant que vous avez effectué la configuration de la connexion à la table Azure, sélectionnez **Nouvelle étape** pour ajouter une condition afin de rechercher de nouveaux prospects dans la table Azure. 

13. Dans la fenêtre **Choisir une action**, choisissez **Actions**, puis sélectionnez le contrôle **Condition**.

    ![Table Azure - Choisir une action.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

14. Dans la fenêtre **Condition**, sélectionnez le champ **Choisir une valeur**, puis choisissez **Expression** dans la fenêtre contextuelle.

15. Collez `length(body('Get_entities')?['value'])` dans le champ ***fx***. Sélectionnez **OK** pour ajouter cette fonction. 

16. Pour terminer la configuration de la condition :
    1. Sélectionnez « est supérieur à » dans la liste déroulante.
    2. Entrer 0 comme valeur

        ![Table Azure - Condition.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

Dans les étapes suivantes, vous allez définir l’action à exécuter selon le résultat de la condition.

* Si la condition donne **If no**, vous n’avez rien à faire.
* Si la condition affiche la valeur **If yes**, déclenchez une action pour vous connecter à votre compte Office 365 afin d’envoyer un message électronique. 

17. Sélectionnez **Ajouter une action** sous **Si oui**.

    ![Table Azure - Condition, **Si oui**.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

18. Sélectionnez **Envoyer un e-mail (Office 365 Outlook)** .

    ![Table Azure - Condition, **Si oui**, envoyer un e-mail.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!Note]
    >Si vous souhaitez utiliser un autre fournisseur de messagerie, sélectionnez Envoyer une notification par e-mail comme action à la place. Les instructions vous montrent comment effectuer la configuration avec Office 365 Outlook, mais elles sont similaires pour un autre fournisseur de courrier.

19. Dans la fenêtre **Office 365 Outlook**, fournissez les informations dans les champs suivants :

    1. **À** : entrez l’adresse e-mail de toutes les personnes qui doivent recevoir cette notification.
    1. **Objet** : indiquez l’objet de l’e-mail. Par exemple : Nouveaux prospects !
    1. **Corps** : ajoutez le texte que vous souhaitez inclure dans chaque message électronique (facultatif) et collez-le dans le corps `body('Get_entities')?['value']`.

    >[!Note]
    >Vous pouvez insérer d’autres points de données statiques ou dynamiques au corps de ce message électronique.

    ![Table Azure, **Si oui**, fenêtre Office 365 Outlook.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

20. Sélectionnez **Enregistrer** pour enregistrer le flux. Microsoft Flow vérifie automatiquement si le flux contient des erreurs. Si aucune erreur n’est détectée, le flux s’exécute dès qu’il est enregistré.

La capture d’écran suivante montre à quoi devrait ressembler le flux final.

![Exemple de flux final.](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>Gérer votre flux

Il est facile de gérer un flux en cours d’exécution. Vous disposez d’un contrôle total sur votre flux. Par exemple, vous pouvez l’arrêter le modifier, afficher un historique des exécutions et obtenir des données analytiques. La capture d’écran suivante montre les options disponibles pour gérer un flux. 

 ![Gestion d’un flux](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

Le flux continue de s’exécuter jusqu'à ce que vous l’arrêtiez à l’aide de l’option **Désactiver le flux**.

Si vous ne recevez aucune notification de prospect par e-mail, cela signifie qu’aucun nouveau prospect n’a été ajouté à la table Azure. Si votre flux contient des erreurs, vous recevrez un e-mail similaire à celui de l’exemple dans la capture d’écran suivante.

 ![Notification par e-mail d’une erreur de flux](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>Configurer votre offre pour envoyer des prospects à la Table Azure

Lorsque vous êtes prêt à configurer les informations de gestion des prospects pour votre offre dans le portail de publication, procédez comme suit :

1. Accédez à la page **Configuration de l'offre** de votre offre.
2. Sélectionnez **Connexion** dans la section Gestion des prospects.
3. Dans la fenêtre contextuelle des détails de la connexion, sélectionnez **Table Azure** pour **Destination du prospect** et collez la chaîne de connexion à partir du compte de stockage Azure que vous avez créé précédemment dans le champ **Chaîne de connexion de compte de stockage**.
4. **E-mail du contact** : fournissez les e-mails des personnes de votre société qui doivent recevoir des notifications par e-mail lors de la réception d’un nouveau prospect. Vous pouvez fournir plusieurs e-mails en les séparant par un point-virgule.
5. Sélectionnez **OK**.

Pour vous assurer que vous vous êtes bien connecté à une destination de prospect, cliquez sur le bouton Valider. En cas de réussite, un prospect de test se trouve dans la destination du prospect.

>[!Note]
>Vous devez terminer la configuration du reste de l'offre et la publier avant de pouvoir recevoir des prospects pour cette offre.

Quand des prospects sont générés, Microsoft les envoie à la Table Azure. Si vous avez configuré un flux, un message est également envoyé à l’adresse e-mail que vous avez configurée.

![Gestion des prospects](./media/commercial-marketplace-lead-management-instructions-azure-table/lead-management.png)

![Gestion des prospects - détails de connexion](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

![Gestion des prospects - compte de stockage des détails de connexion](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

