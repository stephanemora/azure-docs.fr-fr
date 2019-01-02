---
title: Table Azure | Microsoft Docs
description: Configurer la gestion des prospects pour Azure Table.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: c4537709181398e401ade67b831bc2d26a99221f
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53193584"
---
# <a name="lead-management-instructions-for-azure-table"></a>Instructions de gestion des prospects pour une table Azure

Cet article explique comment configurer Table Azure pour le stockage des prospects commerciaux. Table Azure vous permet de stocker et de personnaliser les informations sur le client.

## <a name="to-configure-azure-table"></a>Configurer Table Azure

1.  Si vous ne possédez pas de compte Azure, vous pouvez [créer un compte d’évaluation gratuit](https://azure.microsoft.com/pricing/free-trial/).

2.  Une fois que votre compte Azure est actif, connectez-vous au [portail Azure](https://portal.azure.com).
3.  Sur le portail Azure, créez un compte de stockage. La capture d’écran suivante montre comment créer un compte de stockage. Pour plus d’informations sur la tarification du stockage, consultez [Tarification de Storage](https://azure.microsoft.com/pricing/details/storage/).

    ![Procédure de création d’un compte Azure Storage](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

4.  Copiez la chaîne de connexion du compte de stockage pour la clé, puis collez-la dans le champ **Chaîne de connexion de compte de stockage** sur le portail Cloud Partner. Voici un exemple de chaîne de connexion : `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net `
    
    ![Clé de stockage Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

Vous pouvez utiliser l’[Explorateur de stockage Microsoft Azure](http://azurestorageexplorer.codeplex.com/) ou tout autre outil pour afficher de votre table de stockage. Vous pouvez également exporter les données de Table Azure.
données.

## <a name="optional-use-microsoft-flow-with-an-azure-table"></a>**(Facultatif)**  Utiliser Microsoft Flow avec une table Azure

Vous pouvez utiliser [Microsoft Flow](https://docs.microsoft.com/flow/) pour automatiser les notifications chaque fois qu’un prospect est ajouté à la table Azure. Si vous ne possédez pas encore un compte Azure, vous pouvez [vous inscrire pour en obtenir un gratuitement](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Exemple de notification de prospect

Utilisez cet exemple comme guide pour créer un simple flux qui envoie automatiquement une notification par e-mail lorsqu’un prospect est ajouté à une table Azure. Cet exemple définit une récurrence pour envoyer toutes les heures des informations sur le prospect si le stockage de la table est mis à jour.

1. Connectez-vous à votre compte Microsoft Flow.
2. Dans la barre de navigation de gauche, sélectionnez **Mes flux**.
3. Dans la barre de navigation supérieure, sélectionnez **+ Nouveau**.  
4. Dans la liste déroulante, sélectionnez **+ Créer entièrement**
5. Sous Créer un flux entièrement, sélectionnez **Créer entièrement**.

   ![Créer un flux entièrement](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-create-from-blank.png)

6. Dans la barre de recherche des connecteurs et des déclencheurs, sélectionnez **Déclencheurs**.
7. Sous **Déclencheurs**, sélectionnez **Récurrence**.
8. Dans la fenêtre **Récurrence**, conservez le paramètre par défaut 1 comme **intervalle**. Dans la liste déroulante **Fréquence**, sélectionnez **Heure**.

   >[!NOTE] 
   >Bien que cet exemple utilise un intervalle de 1 heure, vous pouvez sélectionner l’intervalle et la fréquence qui conviennent le mieux à vos besoins.

   ![Définir une fréquence de 1 heure pour la récurrence](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-recurrence-dropdown.png)

9. Sélectionnez **+ Nouvelle étape**.
10. Recherchez « Obtenir l’heure passée », puis sélectionnez **Obtenir l’heure passée** sous Actions. 

    ![Rechercher et sélectionner l’action Obtenir l'heure passée](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-search-getpasttime.png)

11. Dans la fenêtre **Obtenir l’heure passée**, choisissez la valeur 1 comme **intervalle**.  Dans la liste déroulante **Unité de temps**, sélectionnez **Heure**.
    >[!IMPORTANT] 
    >Assurez-vous que cette unité d’intervalle et d’heure correspond à l’intervalle et à la fréquence que vous avez configurés pour la récurrence.

    ![Définir l’intervalle Obtenir l'heure passée](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getpast-time.png)

    >[!TIP] 
    >Vous pouvez vérifier votre flux à tout moment pour confirmer que chaque étape est correctement configurée. Pour vérifier votre flux, sélectionnez **Vérificateur de flux** dans la barre de menus Flux.

Dans les étapes suivantes, vous allez vous connecter à votre table Azure et configurer la logique de traitement afin de gérer de nouveaux prospects.

1. Après l’étape Obtenir l'heure passée, sélectionnez **+ Nouvelle étape**, puis recherchez « Obtenir des entités ».
2. Sous **Actions**, sélectionnez **Obtenir des entités**, puis sélectionnez **Afficher les options avancées**.
3. Dans la fenêtre **Obtenir des entités**, fournissez les informations dans les champs suivants :

   - **Table** : entrez le nom de votre stockage de table Azure. La capture d’écran suivante montre l’invite lorsque la valeur « MarketPlaceLeads » est entrée pour cet exemple. 

     ![Choisir une valeur personnalisée pour le nom de la table Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-table-name.png)

   - **Requête de filtre** : cliquez sur ce champ pour afficher l’icône Obtenir l'heure passée dans une fenêtre contextuelle. Sélectionnez **Heure passée** afin d’utiliser cette valeur comme horodateur pour filtrer la requête. Vous pouvez également coller cette fonction dans le champ : `gt datetime'@{body('Get_past_time')}'`

     ![Configurer la fonction de requête de filtre](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-filterquery.png)

4. Sélectionnez **Nouvelle étape** pour ajouter une condition afin de rechercher de nouveaux prospects dans la table Azure.

   ![Utiliser l’option Nouvelle étape pour ajouter une condition à l’analyse de la table Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-add-filterquery-new-step.png)

5. Dans la fenêtre **Choisir une action**, choisissez **Actions**, puis sélectionnez le contrôle **Condition**.

     ![Ajouter un contrôle de condition](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-action-condition-control.png)

6. Dans la fenêtre **Condition**, sélectionnez le champ **Choisir une valeur**, puis choisissez **Expression** dans la fenêtre contextuelle.
7. Collez `length(body('Get_entities')?['value'])` dans le champ ***fx***. Sélectionnez **OK** pour ajouter cette fonction. Pour terminer la configuration de la condition :

   - Sélectionnez « est supérieur à » dans la liste déroulante.
   - Entrer 0 comme valeur 

     ![Ajouter une fonction à la condition](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-fx0.png)

8. Définissez l’action à exécuter selon le résultat de la condition.

     ![Définir une action selon les résultats de la condition](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-pick-action.png)

9. Si la condition affiche la valeur **If no**, vous n’avez rien à faire. 
10. Si la condition affiche la valeur **If yes**, déclenchez une action pour vous connecter à votre compte Office 365 afin d’envoyer un message électronique. Sélectionnez **Ajouter une action**.
11. Sélectionnez **Envoyer un message électronique**. 
12. Dans la fenêtre **Envoyer un message électronique**, fournissez les informations dans les champs suivants :

    - **À** : entrez l’adresse e-mail de toutes les personnes qui doivent recevoir cette notification.
    - **Objet** : indiquez l’objet de l’e-mail. Par exemple :  Nouveaux prospects !
    - **Corps**:   ajoutez le texte que vous souhaitez inclure dans chaque message électronique (facultatif) et collez-le dans le corps `('Get_entities')?['value']` en tant que fonction pour insérer des informations sur le prospect.

      >[!NOTE] 
      >Vous pouvez insérer d’autres points de données statiques ou dynamiques au corps de ce message électronique.

       ![Configurer la messagerie pour la notification des prospects](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

13. Sélectionnez **Enregistrer** pour enregistrer le flux. Microsoft Flow vérifiera automatiquement si le flux contient des erreurs. Si aucune erreur n’est détectée, votre flux s’exécute dès qu’il est enregistré.

La capture d’écran suivante montre à quoi devrait ressembler le flux final.

 ![Séquence de flux finale](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

### <a name="managing-your-flow"></a>Gestion de votre flux

Vous pouvez facilement gérer un flux en cours d’exécution.  Vous disposez d’un contrôle total sur votre flux. Par exemple, vous pouvez l’arrêter le modifier, afficher un historique des exécutions et obtenir des données analytiques. La capture d’écran suivante montre les options disponibles pour gérer un flux. 

 ![Gestion d’un flux](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

Le flux continue de s’exécuter jusqu'à ce que vous l’arrêtiez à l’aide de l’option **Désactiver le flux**.

Si vous ne recevez aucune notification de prospect par courrier électronique, cela signifie que les nouveaux prospects n’ont pas été ajoutés à la table Azure. Si votre flux contient des erreurs, vous recevrez un message électronique similaire à celui de l’exemple dans la capture d’écran suivante.

 ![Notification par e-mail d’une erreur de flux](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)

## <a name="next-steps"></a>Étapes suivantes

[Configurer des prospects](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)