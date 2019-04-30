---
title: Se connecter aux systèmes SAP - Azure Logic Apps | Microsoft Docs
description: Comment accéder aux ressources SAP et les gérer en automatisant les flux de travail avec Azure Logic Apps
author: ecfan
ms.author: estfan
ms.date: 05/31/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, divswa, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: d677c0eae9c92f90783ed4ebd95a528b34c872ec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60847406"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Se connecter aux systèmes SAP à partir d’Azure Logic Apps

> [!NOTE]
> Ce connecteur SAP est planifié pour l’abandon. Veuillez utiliser ou migrer vers le [plus récente et plus avancé connecteur SAP](./logic-apps-using-sap-connector.md). 

Cet article explique comment accéder à vos ressources SAP à partir d’une application logique en utilisant les connecteurs du serveur d’applications SAP et du serveur de messagerie SAP. De cette façon, vous pouvez automatiser les tâches, les processus et les flux de travail qui gèrent vos données et ressources SAP en créant des applications logiques.

Cet exemple utilise une application logique que vous pouvez déclencher à l’aide d’une requête HTTP. L’application logique envoie un document intermédiaire (IDoc) à un serveur SAP et renvoie une réponse au demandeur qui a appelé l’application logique.
Les connecteurs SAP actuels disposent d’actions, mais pas de déclencheurs. Cet exemple utilise donc le [déclencheur de requête HTTP](../connectors/connectors-native-reqres.md) comme première étape du flux de travail de l’application logique. Pour obtenir des informations techniques spécifiques aux connecteurs SAP, consultez ces articles de référence : 

* <a href="https://docs.microsoft.com/connectors/sapapplicationserver/" target="blank">Connecteur de serveur d’applications SAP</a>
* <a href="https://docs.microsoft.com/connectors/sapmessageserver/" target="blank">Connecteur de serveur de messagerie SAP</a>

Si vous n’avez pas encore d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>.

## <a name="prerequisites"></a>Conditions préalables

Pour suivre cet article, vous avez besoin de ces éléments :

* L’application logique à partir de laquelle vous souhaitez accéder à votre système SAP et un déclencheur qui démarre le flux de travail de votre application logique. Actuellement, les connecteurs SAP fournissent uniquement les actions. Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md) et [Démarrage rapide : Créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Votre <a href="https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server" target="_blank">serveur d’applications SAP</a> ou <a href="https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm" target="_blank">serveur de messagerie SAP</a>

* Téléchargez et installez la dernière [passerelle de données locale](https://www.microsoft.com/download/details.aspx?id=53127) sur n’importe quel ordinateur local. Assurez-vous de configurer votre passerelle dans le portail Azure avant de continuer. La passerelle vous permet d’accéder en toute sécurité aux données et ressources locales. Pour plus d’informations, consultez [Installer la passerelle de données locale pour Azure Logic Apps](../logic-apps/logic-apps-gateway-install.md).

* Téléchargez et installez la bibliothèque cliente SAP la plus récente, (actuellement <a href="https://softwaredownloads.sap.com/file/0020000000086282018" target="_blank">Connecteur SAP (NCo) 3.0.20.0 pour Microsoft .NET Framework 4.0 et Windows 64 bits (x64)</a>) sur le même ordinateur que la passerelle de données locale. Installez cette version ou une version ultérieure pour ces raisons :

  * Les versions antérieures du NCo SAP peuvent subir un blocage lorsque plusieurs messages IDoc sont envoyés en même temps. 
  Cette situation bloque tous les messages envoyés ultérieurement à la destination SAP, ce qui engendre une expiration des messages.

  * La passerelle de données locale s’exécute uniquement sur les systèmes 64 bits. 
  Dans le cas contraire, vous obtenez une erreur « image incorrecte », car le service hôte de la passerelle de données ne prend pas en charge les assemblys 32 bits.

  * Le service hôte de la passerelle de données et l’adaptateur SAP Microsoft utilisent tous deux .NET Framework 4.5. Le NCo SAP pour .NET Framework 4.0 fonctionne avec les processus qui utilisent un runtime .NET 4.0 à 4.7.1. 
  Le NCo SAP pour .NET Framework 2.0 fonctionne avec les processus qui utilisent un runtime .NET 2.0 à 3.5 et ne fonctionne plus avec la passerelle de données locale la plus récente.

* Contenu du message que vous pouvez envoyer à votre serveur SAP, comme un exemple de fichier IDoc. Ce contenu doit être au format XML et inclure l’espace de noms pour l’action SAP que vous souhaitez utiliser.

<a name="add-trigger"></a>

## <a name="add-http-request-trigger"></a>Ajouter un déclencheur de requête HTTP

Dans Azure Logic Apps, chaque application logique doit démarrer avec un [déclencheur](../logic-apps/logic-apps-overview.md#logic-app-concepts), qui s’active lorsqu’un événement spécifique se produit ou lorsqu’une condition particulière est remplie. Chaque fois que le déclencheur s’active, le moteur Logic Apps crée une instance d’application logique et lance l’exécution du flux de travail de votre application.

Dans cet exemple, vous allez créer une application logique avec un point de terminaison dans Azure afin de pouvoir envoyer des *requêtes HTTP POST* à votre application logique. Lorsque votre application logique reçoit ces requêtes HTTP, le déclencheur est activé et passe à l’étape suivante de votre flux de travail.

1. Dans le portail Azure, créez une application logique vide, qui ouvre le Concepteur d’application logique. 

2. Dans la zone de recherche, saisissez le filtre « http request ». Dans la liste des déclencheurs, sélectionnez ce déclencheur : **Requête - Lors de la réception d’une requête HTTP**

   ![Ajouter un déclencheur de requête HTTP](./media/logic-apps-using-sap-connector-old/add-trigger.png)

3. Enregistrez maintenant votre application logique pour pouvoir générer une URL de point de terminaison pour votre application logique.
Dans la barre d’outils du concepteur, choisissez **Enregistrer**. 

   L’URL du point de terminaison s’affiche désormais dans votre déclencheur, par exemple :

   ![Générer une URL pour le point de terminaison](./media/logic-apps-using-sap-connector-old/generate-http-endpoint-url.png)

<a name="add-action"></a>

## <a name="add-sap-action"></a>Ajouter une action SAP

Dans Azure Logic Apps, une [action](../logic-apps/logic-apps-overview.md#logic-app-concepts) est une étape de votre flux de travail qui suit un déclencheur ou une autre action. Si vous n’avez pas encore ajouté de déclencheur à votre application logique et que vous souhaitez suivre cet exemple, [ajoutez le déclencheur décrit dans cette section](#add-trigger).

1. Dans le Concepteur d’application logique, sous le déclencheur, sélectionnez **Nouvelle étape** > **Ajouter une action**.

   ![Ajouter une action](./media/logic-apps-using-sap-connector-old/add-action.png) 

2. Dans la zone de recherche, saisissez le filtre « sap server ». Dans la liste des actions, sélectionnez l’action pour votre serveur SAP : 

   * **Serveur d’applications SAP - Envoyer à SAP**
   * **Serveur de messagerie SAP - Envoyer à SAP**

   Cet exemple utilise cette action : **Serveur d’applications SAP - Envoyer à SAP**

   ![Sélectionnez « Serveur d’applications SAP » ou « Serveur de messagerie SAP ».](media/logic-apps-using-sap-connector-old/select-sap-action.png)

3. Si vous êtes invité à entrer les informations de connexion, créez votre connexion SAP maintenant. Sinon, si votre connexion existe déjà, passez à l’étape suivante afin de configurer votre action SAP. 

   **Créer une connexion SAP locale**

   1. Pour **Passerelles**, sélectionnez **Se connecter via une passerelle de données locale** afin d’afficher les propriétés de la connexion locale.

   2. Fournissez les informations de connexion pour votre serveur SAP. 
   Pour la propriété **gateway**, sélectionnez la passerelle de données que vous avez créée dans le portail Azure lors de votre installation de passerelle, par exemple :

      **Serveur d’applications SAP**

      ![Créer une connexion au serveur d’applications SAP](./media/logic-apps-using-sap-connector-old/create-SAP-app-server-connection.png)  

      **Serveur de messagerie SAP**

      ![Créer une connexion au serveur de messagerie SAP](media/logic-apps-using-sap-connector-old/create-SAP-message-server-connection.png) 

   2. Lorsque vous êtes prêt, choisissez **Créer**.

      Logic Apps configure et teste votre connexion pour vérifier son bon fonctionnement.

4. Maintenant, recherchez et sélectionnez une action à partir de votre serveur SAP. 

   1. Dans la zone **Action SAP**, choisissez l’icône de dossier. 
   À partir de la liste des dossiers, recherchez et sélectionnez l’action que vous souhaitez utiliser. 

      Dans cet exemple, nous sélectionnons la catégorie **IDOC** pour l’action IDoc. 

      ![Rechercher et sélectionner l’action IDoc](./media/logic-apps-using-sap-connector-old/SAP-app-server-find-action.png)

      Si vous ne trouvez pas l’action que vous souhaitez, vous pouvez entrer un chemin d’accès manuellement, par exemple :

      ![Fournir manuellement le chemin d’accès à l’action IDoc](./media/logic-apps-using-sap-connector-old/SAP-app-server-manually-enter-action.png)

      Pour plus d’informations sur les opérations IDoc, consultez [Schémas de message pour les opérations IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

   2. Cliquez dans la zone **Message d’entrée** pour afficher la liste du contenu dynamique. 
   Dans cette liste, sous **Lors de la réception d’une demande HTTP**, sélectionnez le champ **Corps**. 

      Cette étape inclut le contenu du corps de votre déclencheur de requête HTTP et envoie ce résultat à votre serveur SAP.

      ![Sélectionnez le champ « Corps ».](./media/logic-apps-using-sap-connector-old/SAP-app-server-action-select-body.png)

      Une fois que vous avez terminé, votre action SAP ressemble à cet exemple :

      ![Terminer l’action SAP](./media/logic-apps-using-sap-connector-old/SAP-app-server-complete-action.png)

6. Enregistrez votre application logique. Dans la barre d’outils du concepteur, choisissez **Enregistrer**.

<a name="add-response"></a>

## <a name="add-http-response-action"></a>Ajouter une action de réponse HTTP

Ajoutez maintenant une action de réponse au flux de travail de votre application logique et incluez le résultat de l’action SAP. De cette façon, votre application logique renvoie les résultats à partir de votre serveur SAP au demandeur d’origine. 

1. Dans le Concepteur d’application logique, sous l’action SAP, sélectionnez **Nouvelle étape** > **Ajouter une action**.

2. Dans la zone de recherche, saisissez le filtre « response ». Dans la liste des actions, sélectionnez cette action : **Requête - réponse**

3. Cliquez dans la zone **Corps** pour afficher la liste du contenu dynamique. Dans cette liste, sous **Envoyer à SAP**, sélectionnez le champ **Corps**. 

   ![Terminer l’action SAP](./media/logic-apps-using-sap-connector-old/select-sap-body-for-response-action.png)

4. Enregistrez votre application logique. 

## <a name="test-your-logic-app"></a>Tester votre application logique

1. Si votre application logique n’est pas déjà activée, dans le menu de votre application logique, choisissez **Vue d’ensemble**. Dans la barre d’outils, choisissez **Activer**. 

2. Dans la barre d’outils du Concepteur d’application logique, choisissez **Exécuter**. Cette étape démarre manuellement votre application logique.

3. Déclenchez votre application logique en envoyant une requête HTTP POST à l’URL de votre déclencheur de requête HTTP et incluez le contenu du message avec votre requête. Pour envoyer la requête, vous pouvez utiliser un outil tel que [Postman](https://www.getpostman.com/apps). 

   Pour cet article, la requête envoie un fichier IDoc, qui doit être au format XML et inclure l’espace de noms de l’action SAP que vous utilisez, par exemple : 

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//620/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

4. Une fois votre requête HTTP envoyée, attendez la réponse de votre application logique.

> [!NOTE]
> Votre application logique peut expirer si toutes les étapes nécessaires pour la réponse ne se terminent pas dans la [limite de délai d’attente des requêtes](./logic-apps-limits-and-config.md). Si cette situation se produit, les requêtes peuvent être bloquées. Pour vous aider à diagnostiquer les problèmes, découvrez comment vous pouvez [vérifier et surveiller vos applications logiques](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Félicitations, vous venez de créer une application logique qui peut communiquer avec votre serveur SAP. Maintenant que vous avez configuré une connexion SAP pour votre application logique, vous pouvez explorer d’autres actions SAP disponibles, telles que BAPI et RFC.

## <a name="connector-reference"></a>Référence de connecteur

Pour obtenir des détails techniques sur le connecteur, comme décrit dans les fichiers Swagger des connecteurs, consultez les articles de référence suivants : 

* [Serveur d’applications SAP](/connectors/sapapplicationserver/)
* [Serveur de messagerie SAP](/connectors/sapmessageserver/)

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* [Se connecter à des systèmes locaux](../logic-apps/logic-apps-gateway-connection.md) à partir d’applications logiques
* Découvrez comment valider, transformer et utiliser d’autres opérations message dans [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md).
* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)
