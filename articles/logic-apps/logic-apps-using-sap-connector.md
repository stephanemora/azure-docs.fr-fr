---
title: Se connecter aux systèmes SAP - Azure Logic Apps | Microsoft Docs
description: Comment accéder aux ressources SAP et les gérer en automatisant les flux de travail avec Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, LADocs
ms.topic: article
ms.date: 09/14/2018
tags: connectors
ms.openlocfilehash: 13268ec865ec72fce23df550619b199389096360
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47056503"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Se connecter aux systèmes SAP à partir d’Azure Logic Apps

Cet article explique comment accéder à vos ressources SAP locales à partir d’une application logique en utilisant le connecteur SAP ERP Central Component (ECC). Le connecteur SAP ECC prend en charge l’intégration de messages ou de données vers et depuis des systèmes SAP Netweaver via IDoc (Intermediate Document), BAPI (Business Application Programming Interface) ou RFC (Remote Function Call).

Le connecteur SAP ECC utilise la <a href="https://help.sap.com/saphelp_nwpi71/helpdata/en/e9/23c80d66d08c4c8c044a3ea11ca90f/frameset.htm">bibliothèque NCo (.Net Connector) SAP</a>, et fournit ces opérations ou actions :

- **Envoyer à SAP** : envoyer un IDoc ou appeler des fonctions BAPI sur tRFC dans des systèmes SAP.
- **Recevoir de SAP** : recevoir des IDoc ou des appels de fonction BAPI sur tRFC depuis des systèmes SAP.
- **Générer des schémas** : générer des schémas pour les artefacts SAP pour IDoc, BAPI ou RFC.

Le connecteur SAP s’intègre aux systèmes SAP locaux via la [passerelle de données locale](https://www.microsoft.com/download/details.aspx?id=53127). Dans les scénarios d’envoi, par exemple lors de l’envoi d’un message depuis Logic Apps à un système SAP, la passerelle de données agit comme un client RFC et transfère les demandes reçues de Logic Apps à SAP.
De même, dans les scénarios de réception, la passerelle de données agit en tant que serveur RFC qui reçoit des demandes de SAP et les transfère à l’application logique. 

Cet article explique comment créer des exemples d’applications logiques qui s’intègrent à SAP en couvrant les scénarios d’intégration décrits précédemment.

## <a name="prerequisites"></a>Prérequis

Pour suivre cet article, vous avez besoin de ces éléments :

* Un abonnement Azure. Si vous n’avez pas encore d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>.

* L’application logique à partir de laquelle vous souhaitez accéder à votre système SAP et un déclencheur qui démarre le flux de travail de votre application logique. Si vous ne connaissez pas les applications logiques, consultez les sections [Présentation d’Azure Logic Apps](../logic-apps/logic-apps-overview.md) et [Démarrage rapide : créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Votre <a href="https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server" target="_blank">serveur d’applications SAP</a> ou <a href="https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm" target="_blank">serveur de messagerie SAP</a>

* Téléchargez et installez la dernière [passerelle de données locale](https://www.microsoft.com/download/details.aspx?id=53127) sur n’importe quel ordinateur local. Assurez-vous de configurer votre passerelle dans le portail Azure avant de continuer. La passerelle vous permet d’accéder en toute sécurité aux données et ressources locales. Pour plus d’informations, consultez [Installer la passerelle de données locale pour Azure Logic Apps](../logic-apps/logic-apps-gateway-install.md).

* Téléchargez et installez la bibliothèque cliente SAP la plus récente, (actuellement <a href="https://softwaredownloads.sap.com/file/0020000000086282018" target="_blank">Connecteur SAP (NCo) 3.0.20.0 pour Microsoft .NET Framework 4.0 et Windows 64 bits (x64)</a>) sur le même ordinateur que la passerelle de données locale. Installez cette version ou une version ultérieure pour ces raisons :

  * Les versions antérieures du NCo SAP peuvent subir un interblocage quand plusieurs messages IDoc sont envoyés en même temps. 
  Cette situation bloque tous les messages envoyés ultérieurement à la destination SAP, ce qui engendre une expiration des messages.

  * La passerelle de données locale s’exécute uniquement sur les systèmes 64 bits. 
  Dans le cas contraire, vous obtenez une erreur « image incorrecte », car le service hôte de la passerelle de données ne prend pas en charge les assemblys 32 bits.

  * Le service hôte de la passerelle de données et l’adaptateur SAP Microsoft utilisent tous deux .NET Framework 4.5. Le NCo SAP pour .NET Framework 4.0 fonctionne avec les processus qui utilisent un runtime .NET 4.0 à 4.7.1. 
  Le NCo SAP pour .NET Framework 2.0 fonctionne avec les processus qui utilisent un runtime .NET 2.0 à 3.5 et ne fonctionne plus avec la passerelle de données locale la plus récente.

* Contenu du message que vous pouvez envoyer à votre serveur SAP, comme un exemple de fichier IDoc. Ce contenu doit être au format XML et inclure l’espace de noms pour l’action SAP que vous souhaitez utiliser.

<a name="add-trigger"></a>

## <a name="send-to-sap"></a>Envoyer à SAP

Cet exemple utilise une application logique que vous pouvez déclencher à l’aide d’une requête HTTP. L’application logique envoie un document intermédiaire (IDoc) à un serveur SAP et renvoie une réponse au demandeur qui a appelé l’application logique. 

### <a name="add-http-request-trigger"></a>Ajouter un déclencheur de requête HTTP

Dans Azure Logic Apps, chaque application logique doit démarrer avec un [déclencheur](../logic-apps/logic-apps-overview.md#logic-app-concepts), qui s’active lorsqu’un événement spécifique se produit ou lorsqu’une condition particulière est remplie. Chaque fois que le déclencheur s’active, le moteur Logic Apps crée une instance d’application logique et lance l’exécution du flux de travail de votre application.

Dans cet exemple, vous allez créer une application logique avec un point de terminaison dans Azure afin de pouvoir envoyer des *requêtes HTTP POST* à votre application logique. Lorsque votre application logique reçoit ces requêtes HTTP, le déclencheur est activé et passe à l’étape suivante de votre flux de travail.

1. Dans le [portail Azure](https://portal.azure.com), créez une application logique vide, ce qui ouvre le Concepteur d’application logique. 

2. Dans la zone de recherche, saisissez le filtre « http request ». Dans la liste des déclencheurs, sélectionnez ce déclencheur : **Requête - Lors de la réception d’une demande HTTP**

   ![Ajouter un déclencheur de requête HTTP](./media/logic-apps-using-sap-connector/add-trigger.png)

3. Enregistrez maintenant votre application logique pour pouvoir générer une URL de point de terminaison pour votre application logique.
Dans la barre d’outils du concepteur, choisissez **Enregistrer**. 

   L’URL du point de terminaison s’affiche désormais dans votre déclencheur, par exemple :

   ![Générer une URL pour le point de terminaison](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-sap-action"></a>Ajouter une action SAP

Dans Azure Logic Apps, une [action](../logic-apps/logic-apps-overview.md#logic-app-concepts) est une étape de votre flux de travail qui suit un déclencheur ou une autre action. Si vous n’avez pas encore ajouté de déclencheur à votre application logique et que vous souhaitez suivre cet exemple, [ajoutez le déclencheur décrit dans cette section](#add-trigger).

1. Dans le Concepteur d’application logique, sous le déclencheur, sélectionnez **Nouvelle étape** > **Ajouter une action**.

   ![Ajouter une action](./media/logic-apps-using-sap-connector/add-action.png) 

2. Dans la zone de recherche, entrez « sap » comme filtre. Dans la liste des actions, sélectionnez cette action : **Envoyer un message à SAP**
  
   ![Sélectionner une action d’envoi à SAP](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   Au lieu d’effectuer une recherche, vous pouvez aussi choisir l’onglet **Entreprise** et sélectionner l’action SAP.

   ![Sélectionner l’action d’envoi à SAP à partir de l’onglet Entreprise](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

3. Si vous êtes invité à entrer les informations de connexion, créez votre connexion SAP maintenant. Sinon, si votre connexion existe déjà, passez à l’étape suivante afin de configurer votre action SAP. 

   **Créer une connexion SAP locale**

   1. Fournissez les informations de connexion pour votre serveur SAP. 
   Pour la propriété **Passerelle de données**, sélectionnez la passerelle de données que vous avez créée dans le portail Azure lors de l’installation de la passerelle.

      Si la propriété **Type de connexion** est définie sur **Serveur d’applications**, ces propriétés, qui apparaissent habituellement facultatives, sont obligatoires :

      ![Créer une connexion au serveur d’applications SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png) 

      Si la propriété **Type de connexion** est définie sur **Groupe**, ces propriétés, qui apparaissent habituellement facultatives, sont obligatoires : 

      ![Créer une connexion au serveur de messagerie SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png) 

   2. Lorsque vous êtes prêt, choisissez **Créer**. 
   
      Logic Apps configure et teste votre connexion pour vérifier son bon fonctionnement.

4. Maintenant, recherchez et sélectionnez une action à partir de votre serveur SAP. 

   1. Dans la zone **Action SAP**, choisissez l’icône de dossier. 
   Dans la liste des fichiers, recherchez et sélectionnez l’action que vous voulez utiliser. 
   Pour naviguer dans la liste, utilisez les flèches.

      Cet exemple sélectionne un IDoc avec le type **Commande**. 

      ![Rechercher et sélectionner l’action IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      Si vous ne trouvez pas l’action que vous souhaitez, vous pouvez entrer un chemin d’accès manuellement, par exemple :

      ![Fournir manuellement le chemin d’accès à l’action IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > Spécifiez la valeur de l’action SAP via l’éditeur d’expression. De cette façon, vous pouvez utiliser la même action pour différents types de messages.

      Pour plus d’informations sur les opérations IDoc, consultez [Schémas de message pour les opérations IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

   2. Cliquez dans la zone **Message d’entrée** pour afficher la liste du contenu dynamique. 
   Dans cette liste, sous **Lors de la réception d’une demande HTTP**, sélectionnez le champ **Corps**. 

      Cette étape inclut le contenu du corps de votre déclencheur de requête HTTP et envoie ce résultat à votre serveur SAP.

      ![Sélectionnez le champ « Corps ».](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      Une fois que vous avez terminé, votre action SAP ressemble à cet exemple :

      ![Terminer l’action SAP](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

5. Enregistrez votre application logique. Dans la barre d’outils du concepteur, choisissez **Enregistrer**.

<a name="add-response"></a>

### <a name="add-http-response-action"></a>Ajouter une action de réponse HTTP

Ajoutez maintenant une action de réponse au flux de travail de votre application logique et incluez le résultat de l’action SAP. De cette façon, votre application logique renvoie les résultats à partir de votre serveur SAP au demandeur d’origine. 

1. Dans le Concepteur d’application logique, sous l’action SAP, sélectionnez **Nouvelle étape** > **Ajouter une action**.

2. Dans la zone de recherche, saisissez le filtre « response ». Dans la liste des actions, sélectionnez cette action : **Requête - réponse**

3. Cliquez dans la zone **Corps** pour afficher la liste du contenu dynamique. Dans cette liste, sous **Envoyer à SAP**, sélectionnez le champ **Corps**. 

   ![Terminer l’action SAP](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

4. Enregistrez votre application logique. 

### <a name="test-your-logic-app"></a>Tester votre application logique

1. Si votre application logique n’est pas déjà activée, dans le menu de votre application logique, choisissez **Vue d’ensemble**. Dans la barre d’outils, choisissez **Activer**. 

2. Dans la barre d’outils du Concepteur d’application logique, choisissez **Exécuter**. Cette étape démarre manuellement votre application logique.

3. Déclenchez votre application logique en envoyant une requête HTTP POST à l’URL de votre déclencheur de requête HTTP et incluez le contenu du message avec votre requête. Pour envoyer la requête, vous pouvez utiliser un outil tel que [Postman](https://www.getpostman.com/apps). 

   Pour cet article, la requête envoie un fichier IDoc, qui doit être au format XML et inclure l’espace de noms de l’action SAP que vous utilisez, par exemple : 

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

4. Une fois votre requête HTTP envoyée, attendez la réponse de votre application logique.

   > [!NOTE]
   > Votre application logique peut expirer si toutes les étapes nécessaires pour la réponse ne se terminent pas dans la [limite de délai d’attente des requêtes](./logic-apps-limits-and-config.md). Si cette situation se produit, les requêtes peuvent être bloquées. Pour vous aider à diagnostiquer les problèmes, découvrez comment vous pouvez [vérifier et surveiller vos applications logiques](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Félicitations, vous venez de créer une application logique qui peut communiquer avec votre serveur SAP. Maintenant que vous avez configuré une connexion SAP pour votre application logique, vous pouvez explorer d’autres actions SAP disponibles, telles que BAPI et RFC.

## <a name="receive-from-sap"></a>Recevoir de SAP

Cet exemple utilise une application logique qui se déclenche lors de la réception d’un message provenant d’un système SAP. 

### <a name="add-sap-trigger"></a>Ajouter un déclencheur SAP

1. Dans le portail Azure, créez une application logique vide, qui ouvre le Concepteur d’application logique. 

2. Dans la zone de recherche, entrez « sap » comme filtre. Dans la liste des déclencheurs, sélectionnez **Quand un message est reçu de SAP**.

   ![Ajouter un déclencheur SAP](./media/logic-apps-using-sap-connector/add-sap-trigger.png)

   Vous pouvez aussi accéder à l’onglet Entreprise et sélectionner le déclencheur.

   ![Ajouter un déclencheur SAP à partir de l’onglet Entreprise](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

3. Si vous êtes invité à entrer les informations de connexion, créez votre connexion SAP maintenant. Sinon, si votre connexion existe déjà, passez à l’étape suivante afin de configurer votre action SAP. 

   **Créer une connexion SAP locale**

   1. Fournissez les informations de connexion pour votre serveur SAP. 
   Pour la propriété **Passerelle de données**, sélectionnez la passerelle de données que vous avez créée dans le portail Azure lors de l’installation de la passerelle.

      Si la propriété **Type de connexion** est définie sur **Serveur d’applications**, ces propriétés, qui apparaissent habituellement facultatives, sont obligatoires :

      ![Créer une connexion au serveur d’applications SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png) 

      Si la propriété **Type de connexion** est définie sur **Groupe**, ces propriétés, qui apparaissent habituellement facultatives, sont obligatoires :

      ![Créer une connexion au serveur de messagerie SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

4. Spécifiez les paramètres nécessaires en fonction de la configuration de votre système SAP. 

   Vous pouvez si vous le souhaitez fournir une ou plusieurs actions SAP. 
   Cette liste d’actions spécifie les messages que le déclencheur reçoit de votre serveur SAP via la passerelle de données. 
   Une liste vide spécifie que le déclencheur reçoit tous les messages. 
   Si la liste comporte plus d’un message, le déclencheur reçoit seulement les messages spécifiés dans la liste. Tous les autres messages envoyés depuis votre serveur SAP sont rejetés par la passerelle.

   Vous pouvez sélectionner une action SAP dans le sélecteur de fichiers :

   ![Sélectionner une action SAP](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   Vous pouvez aussi spécifier une action manuellement :

   ![Entrer manuellement une action SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png) 

   Voici un exemple qui montre comment l’action apparaît quand vous configurez le déclencheur pour recevoir plusieurs messages.

   ![Exemple de déclencheur](media/logic-apps-using-sap-connector/example-trigger.png)  

   Pour plus d’informations sur l’action SAP, consultez [Schémas de message pour les opérations IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

5. Enregistrez maintenant votre application logique pour commencer à recevoir des messages de votre système SAP.
Dans la barre d’outils du concepteur, choisissez **Enregistrer**. 

Votre application logique est maintenant prête à recevoir des messages de votre système SAP. 

> [!NOTE]
> Le déclencheur SAP n’est pas un déclencheur d’interrogation : il s’agit d’un déclencheur basé sur un webhook. Le déclencheur est appelé depuis la passerelle seulement s’il existe un message : aucune interrogation n’est donc nécessaire. 

### <a name="test-your-logic-app"></a>Tester votre application logique

1. Pour déclencher votre application logique, envoyez un message depuis votre système SAP.

2. Dans le menu de l’application logique, choisissez **Vue d’ensemble**, et consultez **Historique des exécutions** pour les nouvelles exécutions de votre application logique. 

3. Ouvrez la dernière exécution, qui montre le message envoyé depuis votre système SAP dans la section des sorties du déclencheur.

## <a name="generate-schemas-for-artifacts-in-sap"></a>Générer des schémas pour les artefacts dans SAP

Cet exemple utilise une application logique que vous pouvez déclencher à l’aide d’une requête HTTP. L’action SAP envoie une demande à un système SAP pour générer les schémas des artefacts IDoc (Intermediate Document) et BAPI spécifiés. Les schémas retournés dans la réponse sont chargés dans un compte d’intégration avec le connecteur Azure Resource Manager.

### <a name="add-http-request-trigger"></a>Ajouter un déclencheur de requête HTTP

1. Dans le portail Azure, créez une application logique vide, qui ouvre le Concepteur d’application logique. 

2. Dans la zone de recherche, saisissez le filtre « http request ». Dans la liste des déclencheurs, sélectionnez ce déclencheur : **Requête - Lors de la réception d’une demande HTTP**

   ![Ajouter un déclencheur de requête HTTP](./media/logic-apps-using-sap-connector/add-trigger.png)

3. Enregistrez maintenant votre application logique pour pouvoir générer une URL de point de terminaison pour votre application logique.
Dans la barre d’outils du concepteur, choisissez **Enregistrer**. 

   L’URL du point de terminaison s’affiche désormais dans votre déclencheur, par exemple :

   ![Générer une URL pour le point de terminaison](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-sap-action-to-generate-schemas"></a>Ajouter une action SAP pour générer des schémas

1. Dans le Concepteur d’application logique, sous le déclencheur, sélectionnez **Nouvelle étape** > **Ajouter une action**.

   ![Ajouter une action](./media/logic-apps-using-sap-connector/add-action.png) 

2. Dans la zone de recherche, entrez « sap » comme filtre. Dans la liste des actions, sélectionnez cette action : **Générer des schémas**
  
   ![Sélectionner une action d’envoi à SAP](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   Vous pouvez aussi choisir l’onglet **Entreprise** et sélectionner l’action SAP.

   ![Sélectionner l’action d’envoi à SAP à partir de l’onglet Entreprise](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

3. Si vous êtes invité à entrer les informations de connexion, créez votre connexion SAP maintenant. Sinon, si votre connexion existe déjà, passez à l’étape suivante afin de configurer votre action SAP. 

   **Créer une connexion SAP locale**

   1. Fournissez les informations de connexion pour votre serveur SAP. 
   Pour la propriété **Passerelle de données**, sélectionnez la passerelle de données que vous avez créée dans le portail Azure lors de l’installation de la passerelle.

      Si la propriété **Type de connexion** est définie sur **Serveur d’applications**, ces propriétés, qui apparaissent habituellement facultatives, sont obligatoires :

      ![Créer une connexion au serveur d’applications SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png) 

      Si la propriété **Type de connexion** est définie sur **Groupe**, ces propriétés, qui apparaissent habituellement facultatives, sont obligatoires :
   
      ![Créer une connexion au serveur de messagerie SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png) 

   2. Lorsque vous êtes prêt, choisissez **Créer**. Logic Apps configure et teste votre connexion pour vérifier son bon fonctionnement.

4. Indiquez le chemin de l’artefact pour lequel vous voulez générer le schéma.

   Vous pouvez sélectionner l’action SAP dans le sélecteur de fichiers :

   ![Sélectionner une action SAP](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   Vous pouvez aussi entrer l’action manuellement :

   ![Entrer manuellement une action SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png) 

   Pour générer des schémas pour plusieurs artefacts, spécifiez les détails de l’action SAP pour chaque artefact, par exemple :

   ![Sélectionner Ajouter un nouvel élément](media/logic-apps-using-sap-connector/schema-generator-array-pick.png) 

   ![Afficher deux éléments](media/logic-apps-using-sap-connector/schema-generator-example.png) 

   Pour plus d’informations sur l’action SAP, consultez [Schémas de message pour les opérations IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

5. Enregistrez votre application logique. Dans la barre d’outils du concepteur, choisissez **Enregistrer**.

### <a name="test-your-logic-app"></a>Tester votre application logique

1. Dans la barre d’outils du concepteur, choisissez **Exécuter** pour déclencher une exécution de votre application logique.

2. Ouvrez l’exécution et vérifiez les résultats pour l’action **Générer le schéma**. 

   Les sorties montrent les schémas générés pour la liste de messages spécifiée.

### <a name="upload-schemas-to-integration-account"></a>Charger des schémas dans le compte d’intégration

Vous pouvez aussi télécharger ou stocker les schémas générés dans des référentiels, comme un objet blob, un stockage ou un compte d’intégration. Les comptes d’intégration offrent une expérience privilégiée avec d’autres actions XML : cet exemple montre donc comment charger des schémas dans un compte d’intégration pour la même application logique avec le connecteur Azure Resource Manager.

1. Dans le Concepteur d’application logique, sous le déclencheur, choisissez **Nouvelle étape** > **Ajouter une action**. Dans la zone de recherche, entrez « resource manager » comme filtre. Sélectionnez cette action : **Créer ou mettre à jour une ressource**

   ![Sélectionner une action Azure Resource Manager](media/logic-apps-using-sap-connector/select-arm-action.png) 

2. Entrez les détails, notamment votre abonnement Azure, le groupe de ressources Azure et le compte d’intégration. Pour les autres champs, suivez l’exemple ci-dessous.

   ![Entrer les détails pour l’action Azure Resource Manager](media/logic-apps-using-sap-connector/arm-action.png)

   L’action SAP **Générer les schémas** génère des schémas sous forme de collection : le concepteur ajoute donc automatiquement une boucle **For each** à l’action. 
   Voici un exemple qui montre comment cette action apparaît :

   ![Action Azure Resource Manager avec une boucle « for each »](media/logic-apps-using-sap-connector/arm-action-foreach.png)  

   > [!NOTE]
   > Les schémas utilisent un format codé en base64. Pour télécharger les schémas dans un compte d’intégration, ils doivent être décodés avec la fonction `base64ToString()`. Voici un exemple qui montre le code pour l’élément `"properties"` :
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

3. Enregistrez votre application logique. Dans la barre d’outils du concepteur, choisissez **Enregistrer**.

### <a name="test-your-logic-app"></a>Tester votre application logique

1. Dans la barre d’outils du concepteur, choisissez **Exécuter** pour déclencher manuellement votre application logique.

2. Après une exécution réussie, accédez au compte d’intégration et vérifiez l’existence des schémas générés.

## <a name="known-issues-and-limitations"></a>Problèmes connus et limitations

Voici les problèmes et limitations connus pour le connecteur SAP :

* Le déclencheur SAP ne prend pas en charge la réception des IDOC par lots provenant de SAP. Cette action peut entraîner des échecs de connexion de RFC entre votre système SAP et la passerelle de données.

* Le déclencheur SAP ne prend pas en charge les clusters de passerelle de données. Dans certains cas de basculement, le nœud de la passerelle de données qui communique avec le système SAP peut différer du nœud actif, ce qui entraîne un comportement inattendu. Pour les scénarios d’envoi, les clusters de passerelles de données sont pris en charge.

* Dans les scénarios de réception, le retour d’une réponse non null n’est pas pris en charge. Une application logique avec un déclencheur et une action de réponse provoque un comportement inattendu. 

* Un seul appel ou message Envoyer à SAP fonctionne avec tRFC. Le modèle de validation BAPI (Business Application Programming Interface), qui permet par exemple d’effectuer plusieurs appels de tRFC dans la même session, n’est pas pris en charge.

* Les appels RFC avec des pièces jointes ne sont pas pris en charge pour les actions Envoyer à SAP et Générer des schémas.

* Le connecteur SAP ne prend actuellement pas en charge les chaînes de routeur SAP. La passerelle de données locale doit exister sur le même réseau local que le système SAP que vous voulez connecter.

* La conversion pour une valeur absente (null), vide, minimale et maximale pour les champs SAP DATS et TIMS est susceptible de changer dans les mises à jour à venir de la passerelle de données locale.

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* [Se connecter à des systèmes locaux](../logic-apps/logic-apps-gateway-connection.md) à partir d’applications logiques
* Découvrez comment valider, transformer et utiliser d’autres opérations message dans [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md).
* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)
