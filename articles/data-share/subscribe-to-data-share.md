---
title: 'Didacticiel : Accepter et recevoir des données - Azure Data Share'
description: Didacticiel - Accepter et recevoir des données avec Azure Data Share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 9c24f54fe846459187488b0a65b2582914e25e2a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499349"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Didacticiel : Accepter et recevoir des données avec Azure Data Share  

Dans ce didacticiel, vous allez apprendre à accepter une invitation de partage de données à l’aide d’Azure Data Share. Vous allez apprendre à recevoir les données qui ont été partagées avec vous et à activer un intervalle d’actualisation régulier pour avoir la certitude de toujours disposer de la capture instantanée la plus récente des données partagées. 

> [!div class="checklist"]
> * Guide pratique pour accepter une invitation Azure Data Share
> * Créer un compte Azure Data Share
> * Spécifier une destination pour vos données
> * Créer un abonnement à votre partage de données pour l’actualisation planifiée

## <a name="prerequisites"></a>Prérequis
Avant de pouvoir accepter une invitation de partage de données, vous devez provisionner un certain nombre de ressources Azure listées ci-dessous. 

Vérifiez que tous les prérequis sont remplis avant d’accepter une invitation de partage de données. 

* Abonnement Azure : Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Invitation Data Share : Invitation de Microsoft Azure dont l’objet est « Invitation Azure Data Share de **<yourdataprovider@domain.com>**  ».

### <a name="receive-data-into-a-storage-account"></a>Recevoir des données dans un compte de stockage : 

* Compte Stockage Azure : Si vous n’en avez pas déjà, vous pouvez créer un [compte Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 
* Autorisation d’ajouter l’attribution de rôle au compte de stockage, qui est présente dans l’autorisation *Microsoft. Authorization/Role Assignments/Write*. Cette autorisation existe dans le rôle propriétaire. 
* Inscription du fournisseur de ressources pour Microsoft. DataShare. Pour plus d’informations sur la façon de procéder, consultez la documentation sur les [fournisseurs de ressources Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services). 

> [!IMPORTANT]
> Pour recevoir et accepter une invitation Azure Data Share, vous devez d’abord vous inscrire auprès du fournisseur de ressources Microsoft.DataShare et être le propriétaire du compte de stockage dans lequel vous acceptez des données. Suivez les instructions décrites dans [Résoudre les problèmes liés à Azure Data Share](data-share-troubleshoot.md) pour inscrire le fournisseur de ressources de partage de données et vous-même en tant que propriétaires du compte de stockage. 

### <a name="receive-data-into-a-sql-based-source"></a>Recevoir des données dans une source SQL :

* Autorisation permettant au MSI de partage de données d’accéder à la base de données Azure SQL ou à l’entrepôt de données Azure SQL. Pour ce faire, procédez comme suit : 
    1. Définissez-vous comme administrateur Azure Active Directory pour le serveur.
    1. Connectez-vous à la base de données ou à l’entrepôt de données Azure SQL avec Azure Active Directory.
    1. Utilisez l’Éditeur de requêtes (préversion) pour exécuter le script suivant afin d’ajouter le MSI Data Share en tant que db_owner. Vous devez vous connecter avec Active Directory et non avec l’authentification SQL Server. 

```sql
    create user <share_acct_name> from external provider;     
    exec sp_addrolemember db_owner, <share_acct_name>; 
```      
Notez que *<share_acc_name>* est le nom de votre compte Data Share. Si vous n’avez pas encore créé de compte Data Share, vous pourrez le faire plus tard.         

* Accès au pare-feu SQL Server de l’adresse IP du client : Pour ce faire, procédez comme suit : 1. Accédez à *Pare-feux et réseaux virtuels* 1. Cliquez sur le bouton **activer** pour autoriser l’accès à Azure Services. 

Une fois ces prérequis effectués, vous êtes en mesure de recevoir des données dans votre SQL Server.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="open-invitation"></a>Ouvrir l’invitation

Recherchez dans votre boîte de réception une invitation émanant de votre fournisseur de données. L’invitation provient de Microsoft Azure et s’intitule **Invitation Azure Data Share de <yourdataprovider@domain.com>** . Notez le nom de partage pour vérifier que vous acceptez le partage approprié, au cas où il existerait plusieurs invitations. 

Sélectionnez **Afficher l’invitation** pour voir votre invitation dans Azure. Vous accédez à votre vue des partages reçus.

![Invitations](./media/invitations.png "Liste des invitations") 

Sélectionnez le partage que vous souhaitez voir. 

## <a name="accept-invitation"></a>Accepter l’invitation
Veillez à passer en revue tous les champs, notamment les **Conditions d’utilisation**. Si vous acceptez les conditions d’utilisation, vous devez cocher la case indiquant que vous donnez votre accord. 

![Conditions d’utilisation](./media/terms-of-use.png "Conditions d’utilisation") 

Sous *Target Data Share Account* (Compte Data Share cible), sélectionnez l’abonnement et le groupe de ressources à utiliser pour le déploiement de votre partage de données. 

Pour le champ **Data Share Account** (Compte Data Share), sélectionnez **Create new** (Créer) si vous n’avez pas de compte Data Share existant. Sinon, sélectionnez un compte Data Share existant dans lequel vous souhaitez accepter votre partage de données. 

Pour le champ *Received Share Name* (Nom de partage reçu), vous pouvez conserver la valeur par défaut spécifiée par le fournisseur de données, ou spécifier le nouveau nom du partage reçu. 

![Compte Data Share cible](./media/target-data-share.png "Compte Data Share cible") 

Une fois que vous avez accepté les conditions d’utilisation et spécifié un emplacement pour votre partage, sélectionnez *Accept and Configure* (Accepter et configurer). Si vous avez choisi cette option, un abonnement de partage est créé. L’écran suivant vous demande de sélectionner le compte de stockage cible où copier vos données. 

![Options d’acceptation](./media/accept-options.png "Options d’acceptation") 

Si vous préférez accepter l’invitation maintenant et configurer votre stockage plus tard, sélectionnez *Accept and Configure later* (Accepter et configurer plus tard). Cette option vous permet de configurer votre compte de stockage cible plus tard. Pour poursuivre la configuration de votre stockage plus tard, consultez la page [Guide pratique pour configurer votre compte de stockage](how-to-configure-mapping.md) afin de connaître le détail des étapes à suivre pour reprendre la configuration de votre partage de données. 

Si vous ne souhaitez pas accepter l’invitation, sélectionnez *Reject* (Rejeter). 

## <a name="configure-storage"></a>Configurer le stockage
Sous *Target Storage Settings* (Paramètres du stockage cible), sélectionnez l’abonnement, le groupe de ressources et le compte de stockage à utiliser pour la réception de vos données. 

![Paramètres du stockage cible](./media/target-storage-settings.png "Stockage cible") 

Pour recevoir des actualisations régulières de vos données, veillez à activer les paramètres de capture instantanée. Notez que vous ne voyez la planification des paramètres de capture instantanée que si votre fournisseur de données l’a incluse dans le partage de données. 

![Paramètres d’instantané](./media/snapshot-settings.png "Paramètres d’instantané") 

Sélectionnez *Enregistrer*. 

> [!IMPORTANT]
> Si vous recevez des données SQL et que vous souhaitez les recevoir dans une source SQL, consultez notre guide pratique [Configurer un mappage de jeux de données](how-to-configure-mapping.md) pour savoir comment configurer un serveur SQL Server comme destination de votre jeu de données. 

## <a name="trigger-a-snapshot"></a>Déclencher une capture instantanée

Vous pouvez déclencher une capture instantanée sous l’onglet Received Shares -> Details (Partages reçus -> Détails) en sélectionnant **Trigger snapshot** (Déclencher la capture instantanée). Ici, vous pouvez déclencher une capture instantanée complète ou incrémentielle de vos données. Si vous recevez des données de votre fournisseur de données pour la première fois, sélectionnez l’option de copie complète. 

![Déclencher un instantané](./media/trigger-snapshot.png "Déclencher un instantané") 

Une fois que le dernier état d’exécution est *successful* (réussi), ouvrez le compte de stockage pour voir les données reçues. 

Pour vérifier quel est le compte de stockage que vous avez utilisé, sélectionnez **Datasets** (Jeux de données). 

![Jeux de données consommateur](./media/consumer-datasets.png "Mappage de jeu de données consommateur") 

## <a name="view-history"></a>Afficher l’historique
Pour voir un historique de vos captures instantanées, accédez à Received Shares -> History (Partages reçus -> Historique). Vous trouverez ici un historique de toutes les captures instantanées générées au cours des 60 derniers jours. 

## <a name="next-steps"></a>Étapes suivantes
Dans ce tutoriel, vous avez appris à accepter et recevoir une invitation Azure Data Share. Pour en savoir plus sur les concepts liés à Azure Data Share, consultez [Concepts : Terminologie propre à Azure Data Share](terminology.md).