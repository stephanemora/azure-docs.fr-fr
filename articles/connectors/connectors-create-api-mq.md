---
title: Se connecter à un serveur IBM MQ
description: Envoyer et récupérer des messages avec un serveur Azure ou un serveur IBM MQ local et Azure Logic Apps
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, logicappspm
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: 489f53a4f4c1c0d5bd782f42a9daf73217234793
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77118055"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Se connecter à un serveur IBM MQ depuis Azure Logic Apps

Le connecteur IBM MQ envoie et récupère les messages stockés dans un serveur IBM MQ local ou dans Azure. Ce connecteur inclut un client Microsoft MQ qui communique avec un serveur IBM MQ distant sur un réseau TCP/IP. Cet article offre un guide de démarrage pour l’utilisation du connecteur MQ. Vous pouvez commencer par parcourir un message dans une file d’attente, avant de tenter les autres actions.

Le connecteur IBM MQ inclut ces actions, mais ne fournit aucun déclencheur :

- Parcourir un seul message sans le supprimer du serveur IBM MQ
- Parcourir un lot de messages sans supprimer ceux-ci du serveur IBM MQ
- Recevoir un message unique et supprimer le message à partir du serveur IBM MQ
- Recevoir un lot de messages et supprimer les messages du serveur IBM MQ
- Envoyer un message unique au serveur IBM MQ

## <a name="prerequisites"></a>Conditions préalables requises

* Si vous utilisez un serveur MQ local, [installez la passerelle de données locale](../logic-apps/logic-apps-gateway-install.md) sur un serveur au sein de votre réseau. Pour que le connecteur MQ fonctionne, .NET Framework 4.6 doit également être installé sur le serveur sur lequel la passerelle de données locale est installée. Vous devez également créer une ressource dans Azure pour la passerelle de données locale. Pour plus d’informations, consultez [Configurer la connexion à la passerelle de données](../logic-apps/logic-apps-gateway-connection.md).

  Toutefois, si votre serveur MQ est disponible publiquement ou dans Azure, vous n’avez pas à utiliser la passerelle de données.

* Versions d’IBM WebSphere MQ Officiellement prises en charge :

  * MQ 7.5
  * MQ 8.0
  * MQ 9.0

* L’application logique dans laquelle vous souhaitez ajouter l’action MQ. Cette application logique doit utiliser le même emplacement que la connexion de votre passerelle de données locale et vous devez avoir un déclencheur qui démarre votre workflow. 

  Le connecteur MQ ne possède aucun déclencheur. Vous devez donc d’abord en ajouter un à votre application logique. Par exemple, vous pouvez utiliser le déclencheur Recurrence. Si vous débutez avec les applications logiques, essayez ce [Démarrage rapide pour créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

## <a name="browse-a-single-message"></a>Parcourir un seul message

1. Dans votre application logique, sous le déclencheur ou une autre action, choisissez **Nouvelle étape**. 

1. Dans la zone de recherche, entrez « mq », puis sélectionnez cette action : **Parcourir un message**

   ![Parcourir un message](media/connectors-create-api-mq/Browse_message.png)

1. À défaut de connexion MQ existante, créez la connexion :  

   1. Dans l’action, sélectionnez l’option **Se connecter via la passerelle de données locale**.
   
   1. Renseignez les propriétés de votre serveur MQ.  

      Pour **Serveur**, vous pouvez entrer le nom du serveur MQ, ou l’adresse IP suivie par un signe deux-points et le numéro de port.
    
   1. Ouvrez la liste des **passerelles**, ce qui affiche toutes les connexions de passerelle précédemment configurées. Sélectionnez votre passerelle.
    
   1. Lorsque vous êtes prêt, choisissez **Créer**. 
   
      Votre condition ressemble à cet exemple :

      ![Propriétés de connexion](media/connectors-create-api-mq/Connection_Properties.png)

1. Configurez les propriétés de l’action :

   * **Queue** (File d’attente) : Spécifiez une file d’attente différente de la connexion.

   * **MessageId**, **CorrelationId**, **GroupId**et d’autres propriétés : Recherchez un message basé sur les différentes propriétés de message MQ

   * **IncludeInfo** : Définissez la propriété sur **True** pour inclure des informations supplémentaires dans la sortie. Ou bien définissez la propriété sur **False** pour ne pas inclure des informations supplémentaires dans la sortie.

   * **Timeout** (Expiration du délai) : Entrer une valeur pour déterminer la durée d’attente de l’arrivée d’un message dans une file d’attente vide. Si aucune valeur n’est entrée, le premier message dans la file d’attente est récupéré et aucun temps n’est consacré à l’attente de l’affichage d’un message.

     ![Parcourir les propriétés d’un message](media/connectors-create-api-mq/Browse_message_Props.png)

1. **Enregistrez** vos modifications, puis **exécutez** votre application logique.

   ![Enregistrer et exécuter](media/connectors-create-api-mq/Save_Run.png)

   Une fois l’exécution terminée, les étapes de l’exécution sont affichées et vous pouvez consulter la sortie.

1. Pour passer en revue les détails de chaque étape, choisissez la coche verte. Pour consulter plus d’informations sur les données de sortie, choisissez **Afficher les sorties brutes**.

   ![Parcourir une sortie de message](media/connectors-create-api-mq/Browse_message_output.png)  

   Voici un exemple de sortie brute :

   ![Parcourir une sortie brute de message](media/connectors-create-api-mq/Browse_message_raw_output.png)

1. Si vous définissez l’option **IncludeInfo** sur true, la sortie suivante s’affiche :

   ![Parcourir les informations include d’un message](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Parcourir plusieurs messages

L’action **Parcourir des messages** inclut une option **BatchSize** permettant d’indiquer le nombre de messages à retourner à partir de la file d’attente.  Si l’option **BatchSize** ne comporte aucune entrée, tous les messages sont retournés. La sortie retournée est un tableau de messages.

1. Lorsque vous ajoutez l’action **Parcourir les messages**, la première connexion précédemment configurée est sélectionnée par défaut. Pour créer une nouvelle connexion, choisissez **Modifier la connexion**. Ou sélectionnez une autre connexion.

1. Lorsque l’exécution de l’application logique se termine, voici un exemple de sortie de l’action **Parcourir les messages** :

   ![Parcourir la sortie des messages](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-single-message"></a>Recevoir un seul message

L’action **Recevoir un message** a les mêmes entrées et sorties que l’action **Parcourir un message**. Lorsque vous utilisez l’action **Recevoir un message**, le message est supprimé de la file d’attente.

## <a name="receive-multiple-messages"></a>Recevoir plusieurs messages

L’action **Recevoir des messages** a les mêmes entrées et sorties que l’action **Parcourir des messages**. Lorsque vous utilisez l’action **Recevoir des messages**, les messages sont supprimés de la file d’attente.

S’il n’existe aucun message dans la file d’attente lorsque vous effectuez une opération de recherche ou de réception, l’opération échoue en affichant cette sortie :  

![Erreur MQ Aucun message](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-message"></a>Envoyer un message

Lorsque vous ajoutez l’action **Envoyer des messages**, la première connexion précédemment configurée est sélectionnée par défaut. Pour créer une nouvelle connexion, choisissez **Modifier la connexion**. Ou sélectionnez une autre connexion.

1. Sélectionnez un type de message valide : **Datagramme**, **Réponse**, ou **Demande**  

   ![Propriétés d’envoi des messages](media/connectors-create-api-mq/Send_Msg_Props.png)

1. Lorsque l’exécution de l’application logique se termine, voici un exemple de sortie de l’action **Envoyer des messages** :

   ![Sortie Envoyer un message](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-reference"></a>Référence de connecteur

Pour obtenir des détails techniques sur les actions et les limites, qui sont décrits par la description OpenAPI du connecteur (anciennement Swagger), consultez la [page de référence du connecteur](/connectors/mq/).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)
