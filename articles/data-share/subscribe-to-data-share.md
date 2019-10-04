---
title: Tutoriel - Accepter et recevoir des données avec Azure Data Share Preview
description: Tutoriel - Accepter et recevoir des données avec Azure Data Share Preview
author: joannapea
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: fc63c1a0b3b496de8e5ecea58f79f1db9d872e80
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67838445"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share-preview"></a>Didacticiel : Accepter et recevoir des données avec Azure Data Share Preview

Dans ce tutoriel, vous allez apprendre à accepter une invitation de partage de données à l’aide d’Azure Data Share Preview. Vous allez apprendre à recevoir les données qui ont été partagées avec vous et à activer un intervalle d’actualisation régulier pour avoir la certitude de toujours disposer de la capture instantanée la plus récente des données partagées. 

> [!div class="checklist"]
> * Guide pratique pour accepter une invitation Azure Data Share Preview
> * Créer un compte Azure Data Share Preview
> * Spécifier une destination pour vos données
> * Créer un abonnement à votre partage de données pour l’actualisation planifiée

## <a name="prerequisites"></a>Prérequis
Avant de pouvoir accepter une invitation de partage de données, vous devez provisionner un certain nombre de ressources Azure listées ci-dessous. 

Vérifiez que tous les prérequis sont remplis avant d’accepter une invitation de partage de données. 

* Abonnement Azure : Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Compte Stockage Azure : Si vous n’en avez pas déjà, vous pouvez créer un [compte Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 
* Invitation Data Share : Invitation de Microsoft Azure dont l’objet est « Invitation Azure Data Share de **<yourdataprovider@domain.com>**  ».
* Autorisation d’ajouter l’attribution de rôle au compte de stockage, qui est présente dans l’autorisation *Microsoft. Authorization/Role Assignments/Write*. Cette autorisation existe dans le rôle propriétaire. 
* Inscription du fournisseur de ressources pour Microsoft. DataShare. Pour plus d’informations sur la façon de procéder, consultez la documentation sur les [fournisseurs de ressources Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services). 

> [!IMPORTANT]
> Pour recevoir et accepter une invitation Azure Data Share, vous devez d’abord vous inscrire auprès du fournisseur de ressources Microsoft.DataShare et être le propriétaire du compte de stockage dans lequel vous acceptez des données. Suivez les instructions décrites dans [Résoudre les problèmes liés à Azure Data Share Preview](data-share-troubleshoot.md) pour inscrire le fournisseur de ressources de partage de données et vous-même en tant que propriétaires du compte de stockage. 

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

![Paramètres de capture instantanée](./media/snapshot-settings.png "Paramètres de capture instantanée") 

Sélectionnez *Enregistrer*. 

## <a name="trigger-a-snapshot"></a>Déclencher une capture instantanée

Vous pouvez déclencher une capture instantanée sous l’onglet Received Shares -> Details (Partages reçus -> Détails) en sélectionnant **Trigger snapshot** (Déclencher la capture instantanée). Ici, vous pouvez déclencher une capture instantanée complète ou incrémentielle de vos données. Si vous recevez des données de votre fournisseur de données pour la première fois, sélectionnez l’option de copie complète. 

![Déclencher la capture instantanée](./media/trigger-snapshot.png "Déclencher la capture instantanée") 

Une fois que le dernier état d’exécution est *successful* (réussi), ouvrez le compte de stockage pour voir les données reçues. 

Pour vérifier quel est le compte de stockage que vous avez utilisé, sélectionnez **Datasets** (Jeux de données). 

![Jeux de données de consommateurs](./media/consumer-datasets.png "Mappage de jeux de données de consommateurs") 

## <a name="view-history"></a>Afficher l’historique
Pour voir un historique de vos captures instantanées, accédez à Received Shares -> History (Partages reçus -> Historique). Vous trouverez ici un historique de toutes les captures instantanées générées au cours des 60 derniers jours. 

## <a name="next-steps"></a>Étapes suivantes
Dans ce tutoriel, vous avez appris à accepter et recevoir une invitation Azure Data Share. Pour en savoir plus sur les concepts liés à Azure Data Share, consultez [Concepts : Terminologie propre à Azure Data Share](terminology.md).