---
title: Se connecter aux systèmes SAP - Azure Logic Apps
description: Accéder aux ressources SAP et les gérer en automatisant les workflows avec Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, LADocs
ms.topic: article
ms.date: 05/09/2019
tags: connectors
ms.openlocfilehash: 8232bf90b4dc160583959345a257846aaabad690
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67458927"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Se connecter aux systèmes SAP à partir d’Azure Logic Apps

Cet article explique comment accéder à vos ressources SAP locales à partir d’une application logique en utilisant le connecteur SAP. Le connecteur fonctionne avec les versions classiques de SAP, telles que les systèmes R/3 et ECC locaux. Le connecteur permet également l’intégration aux nouveaux systèmes SAP basés sur HANA, tels que S/4 HANA, qu’ils soient hébergés localement ou dans le cloud. Le connecteur SAP prend en charge l’intégration de messages ou de données vers et depuis des systèmes SAP NetWeaver via IDoc (Intermediate Document), BAPI (Business Application Programming Interface) ou RFC (Remote Function Call).

Le connecteur SAP utilise la [bibliothèque NCo (.NET Connector) SAP](https://support.sap.com/en/product/connectors/msnet.html), et fournit ces opérations ou actions :

* **Envoyer à SAP** : envoyer un IDoc sur tRFC, appeler des fonctions BAPI sur RFC ou appeler RFC/tRFC dans des systèmes SAP.
* **Recevoir de SAP** : recevoir un IDoc sur tRFC, appeler des fonctions BAPI sur tRFC ou appeler RFC/tRFC dans des systèmes SAP.
* **Générer des schémas** : générer des schémas pour les artefacts SAP pour IDoc, BAPI ou RFC.

Pour ces opérations, le connecteur SAP prend en charge l’authentification de base via un nom d’utilisateur et un mot de passe. Le connecteur prend également en charge [Secure Network Communications (SNC)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true). SNC peut être utilisé pour l’authentification unique SAP NetWeaver ou pour des fonctionnalités de sécurité supplémentaires fournies par un produit de sécurité externe.

Le connecteur SAP s’intègre aux systèmes SAP locaux via la [passerelle de données locale](../logic-apps/logic-apps-gateway-connection.md). Dans les scénarios d’envoi, par exemple lors de l’envoi d’un message depuis une application logique à un système SAP, la passerelle de données agit comme un client RFC et transfère les demandes reçues de l’application logique à SAP.
De même, dans les scénarios de réception, la passerelle de données agit en tant que serveur RFC qui reçoit des demandes de SAP et les transfère à l’application logique.

Cet article explique comment créer des exemples d’applications logiques qui s’intègrent à SAP en couvrant les scénarios d’intégration décrits précédemment.

<a name="pre-reqs"></a>

## <a name="prerequisites"></a>Prérequis

Pour suivre cet article, vous avez besoin de ces éléments :

* Un abonnement Azure. Si vous n’avez pas encore d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/).
* L’application logique à partir de laquelle vous souhaitez accéder à votre système SAP et un déclencheur qui démarre le flux de travail de votre application logique. Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md) et [Démarrage rapide : Créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).
* Votre [serveur d’applications SAP](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) ou [serveur de messagerie SAP](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm).
* Téléchargez et installez la dernière [passerelle de données locale](https://www.microsoft.com/download/details.aspx?id=53127) sur n’importe quel ordinateur local. Assurez-vous de configurer votre passerelle dans le portail Azure avant de continuer. La passerelle vous permet d’accéder en toute sécurité aux données et ressources locales. Pour plus d’informations, consultez [Installer une passerelle de données locale pour Azure Logic Apps](../logic-apps/logic-apps-gateway-install.md).
* Si vous utilisez SNC avec l’authentification unique, assurez-vous que la passerelle s’exécute en tant qu’utilisateur qui est mappé à l’utilisateur SAP. Pour modifier le compte par défaut, sélectionnez **Changer de compte**, puis entrez les informations d’identification de l’utilisateur.

  ![Modifier le compte de passerelle](./media/logic-apps-using-sap-connector/gateway-account.png)

* Si vous activez SNC avec un produit de sécurité externe, copiez la bibliothèque SNC ou les fichiers sur le même ordinateur sur lequel la passerelle est installée. [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos et NTLM sont des exemples de produits SNC.
* Téléchargez et installez la bibliothèque cliente SAP la plus récente, actuellement [Connecteur SAP (NCo) 3.0.21.0 pour Microsoft .NET Framework 4.0 et Windows 64 bits (x64)](https://softwaredownloads.sap.com/file/0020000001865512018), sur le même ordinateur que la passerelle de données locale. Installez cette version ou une version ultérieure pour ces raisons :

  * Les versions antérieures du NCo SAP peuvent subir un interblocage quand plusieurs messages IDoc sont envoyés en même temps. Cette situation bloque tous les messages envoyés ultérieurement à la destination SAP, ce qui engendre une expiration des messages.
  * La passerelle de données locale s’exécute uniquement sur les systèmes 64 bits. Dans le cas contraire, vous obtenez une erreur « image incorrecte », car le service hôte de la passerelle de données ne prend pas en charge les assemblys 32 bits.
  * Le service hôte de la passerelle de données et l’adaptateur SAP Microsoft utilisent tous deux .NET Framework 4.5. Le NCo SAP pour .NET Framework 4.0 fonctionne avec les processus qui utilisent un runtime .NET 4.0 à 4.7.1. Le NCo SAP pour .NET Framework 2.0 fonctionne avec les processus qui utilisent un runtime .NET 2.0 à 3.5, mais ne fonctionne plus avec la passerelle de données locale la plus récente.

* Le contenu du message que vous pouvez envoyer à votre serveur SAP, comme un exemple de fichier IDoc, doit être au format XML et inclure l’espace de noms pour l’action SAP que vous souhaitez utiliser.

<a name="add-trigger"></a>

## <a name="send-to-sap"></a>Envoyer à SAP

Cet exemple utilise une application logique que vous pouvez déclencher à l’aide d’une requête HTTP. L’application logique envoie un IDoc à un serveur SAP et retourne une réponse au demandeur qui a appelé l’application logique. 

### <a name="add-an-http-request-trigger"></a>Ajouter un déclencheur de requête HTTP

Dans Azure Logic Apps, chaque application logique doit démarrer avec un [déclencheur](../logic-apps/logic-apps-overview.md#logic-app-concepts), qui s’active lorsqu’un événement spécifique se produit ou lorsqu’une condition particulière est remplie. Chaque fois que le déclencheur s’active, le moteur Logic Apps crée une instance d’application logique et lance l’exécution du flux de travail de votre application.

Dans cet exemple, vous allez créer une application logique avec un point de terminaison dans Azure afin de pouvoir envoyer des *requêtes HTTP POST* à votre application logique. Lorsque votre application logique reçoit ces requêtes HTTP, le déclencheur est activé et passe à l’étape suivante de votre flux de travail.

1. Dans le [portail Azure](https://portal.azure.com), créez une application logique vide, ce qui ouvre le Concepteur d’application logique.

1. Dans la zone de recherche, saisissez le filtre « http request ». Dans la liste **Déclencheurs**, sélectionnez **Lors de la réception d’une requête HTTP**.

   ![Ajouter un déclencheur de requête HTTP](./media/logic-apps-using-sap-connector/add-trigger.png)

1. Enregistrez maintenant votre application logique pour pouvoir générer une URL de point de terminaison pour votre application logique. Dans la barre d’outils du concepteur, sélectionnez **Enregistrer**.

   L’URL du point de terminaison s’affiche désormais dans votre déclencheur, par exemple :

   ![Générer une URL pour le point de terminaison](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-an-sap-action"></a>Ajouter une action SAP

Dans Azure Logic Apps, une [action](../logic-apps/logic-apps-overview.md#logic-app-concepts) est une étape de votre flux de travail qui suit un déclencheur ou une autre action. Si vous n’avez pas encore ajouté de déclencheur à votre application logique et que vous souhaitez suivre cet exemple, [ajoutez le déclencheur décrit dans cette section](#add-trigger).

1. Dans le Concepteur d’application logique, sous le déclencheur, sélectionnez **Nouvelle étape**.

   ![Sélectionner « Nouvelle étape »](./media/logic-apps-using-sap-connector/add-action.png)

1. Dans la zone de recherche, entrez « sap » comme filtre. Dans la liste **Actions**, sélectionnez **Envoyer un message à SAP**.
  
   ![Sélectionner une action d’envoi à SAP](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   Au lieu d’effectuer une recherche, vous pouvez aussi choisir l’onglet **Entreprise** et sélectionner l’action SAP.

   ![Sélectionner l’action d’envoi à SAP à partir de l’onglet Entreprise](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. Si vous êtes invité à entrer les informations de connexion, créez votre connexion SAP maintenant. Sinon, si votre connexion existe déjà, passez à l’étape suivante afin de configurer votre action SAP.

   **Créer une connexion SAP locale**

    1. Fournissez les informations de connexion pour votre serveur SAP. Pour la propriété **Passerelle de données**, sélectionnez la passerelle de données que vous avez créée dans le portail Azure lors de l’installation de la passerelle.

         - Si la propriété **Type de connexion** est définie sur **Serveur d’applications**, ces propriétés, qui apparaissent habituellement facultatives, sont obligatoires :

            ![Créer une connexion au serveur d’applications SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

         - Si la propriété **Type de connexion** est définie sur **Groupe**, ces propriétés, qui apparaissent habituellement facultatives, sont obligatoires :

            ![Créer une connexion au serveur de messagerie SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

           Par défaut, le typage fort est utilisé pour rechercher des valeurs non valides en effectuant une validation XML par rapport au schéma. Ce comportement peut vous aider à détecter les problèmes plus tôt. L’option **Types sécurisés** est disponible pour la compatibilité descendante et ne vérifie que la longueur de chaîne. Apprenez-en davantage sur l’[option Types sécurisés](#safe-typing).

    1. Quand vous avez terminé, sélectionnez **Créer**.

       Logic Apps configure et teste votre connexion pour vérifier son bon fonctionnement.

1. Maintenant, recherchez et sélectionnez une action à partir de votre serveur SAP.

    1. Dans la zone **Action SAP**, sélectionnez l’icône de dossier. Dans la liste des fichiers, recherchez et sélectionnez l’action que vous voulez utiliser. Pour naviguer dans la liste, utilisez les flèches.

       Cet exemple sélectionne un IDoc avec le type **Commandes**.

       ![Rechercher et sélectionner l’action IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

       Si vous ne trouvez pas l’action que vous souhaitez, vous pouvez entrer un chemin d’accès manuellement, par exemple :

       ![Fournir manuellement le chemin d’accès à l’action IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

       > [!TIP]
       > Spécifiez la valeur de l’**action SAP** via l’éditeur d’expressions. De cette façon, vous pouvez utiliser la même action pour différents types de messages.

       Pour plus d’informations sur les opérations IDoc, consultez [Schémas de message pour les opérations IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

    1. Cliquez dans la zone **Message d’entrée** pour afficher la liste du contenu dynamique. Dans cette liste, sous **Lors de la réception d’une demande HTTP**, sélectionnez le champ **Corps**.

       Cette étape inclut le contenu du corps de votre déclencheur de requête HTTP et envoie ce résultat à votre serveur SAP.

       ![Sélectionnez le champ « Corps ».](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

       Une fois que vous avez terminé, votre action SAP ressemble à cet exemple :

       ![Terminer l’action SAP](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Enregistrez votre application logique. Dans la barre d’outils du concepteur, sélectionnez **Enregistrer**.

<a name="add-response"></a>

### <a name="add-an-http-response-action"></a>Ajouter une action de réponse HTTP

Ajoutez maintenant une action de réponse au flux de travail de votre application logique et incluez le résultat de l’action SAP. De cette façon, votre application logique renvoie les résultats à partir de votre serveur SAP au demandeur d’origine.

1. Dans le Concepteur d’application logique, sous l’action SAP, sélectionnez **Nouvelle étape**.

1. Dans la zone de recherche, saisissez le filtre « response ». Dans la liste **Actions**, sélectionnez **Réponse**.

1. Cliquez dans la zone **Corps** pour afficher la liste du contenu dynamique. Dans cette liste, sous **Envoyer un message à SAP**, sélectionnez le champ **Corps**.

   ![Terminer l’action SAP](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Enregistrez votre application logique.

### <a name="test-your-logic-app"></a>Tester votre application logique

1. Si votre application logique n’est pas déjà activée, dans le menu associé, sélectionnez **Vue d’ensemble**. Dans la barre d’outils, sélectionnez **Activer**.

1. Dans la barre d’outils du concepteur, sélectionnez **Exécuter**. Cette étape démarre manuellement votre application logique.

1. Déclenchez votre application logique en envoyant une requête HTTP POST à l’URL de votre déclencheur de requête HTTP.
Incluez le contenu du message avec votre requête. Pour envoyer la requête, vous pouvez utiliser un outil tel que [Postman](https://www.getpostman.com/apps).

   Pour cet article, la requête envoie un fichier IDoc, qui doit être au format XML et inclure l’espace de noms de l’action SAP que vous utilisez, par exemple :

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. Une fois votre requête HTTP envoyée, attendez la réponse de votre application logique.

   > [!NOTE]
   > Votre application logique peut expirer si toutes les étapes nécessaires pour la réponse ne se terminent pas dans la [limite de délai d’attente des requêtes](./logic-apps-limits-and-config.md). Si cette situation se produit, les requêtes peuvent être bloquées. Pour vous aider à diagnostiquer les problèmes, découvrez comment vous pouvez [vérifier et surveiller vos applications logiques](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Vous venez de créer une application logique qui peut communiquer avec votre serveur SAP. Maintenant que vous avez configuré une connexion SAP pour votre application logique, vous pouvez explorer d’autres actions SAP disponibles, telles que BAPI et RFC.

## <a name="receive-from-sap"></a>Recevoir de SAP

Cet exemple utilise une application logique qui se déclenche quand l’application reçoit un message provenant d’un système SAP.

### <a name="add-an-sap-trigger"></a>Ajouter un déclencheur SAP

1. Dans le portail Azure, créez une application logique vide, qui ouvre le Concepteur d’application logique.

1. Dans la zone de recherche, entrez « sap » comme filtre. Dans la liste **Déclencheurs**, sélectionnez **Quand un message est reçu de SAP**.

   ![Ajouter un déclencheur SAP](./media/logic-apps-using-sap-connector/add-sap-trigger.png)

   Vous pouvez aussi accéder à l’onglet **Entreprise** et sélectionner le déclencheur :

   ![Ajouter un déclencheur SAP à partir de l’onglet Entreprise](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. Si vous êtes invité à entrer les informations de connexion, créez votre connexion SAP maintenant. Si votre connexion existe déjà, passez à l’étape suivante afin de configurer votre action SAP.

   **Créer une connexion SAP locale**

   - Fournissez les informations de connexion pour votre serveur SAP. Pour la propriété **Passerelle de données**, sélectionnez la passerelle de données que vous avez créée dans le portail Azure lors de l’installation de la passerelle.

      - Si la propriété **Type de connexion** est définie sur **Serveur d’applications**, ces propriétés, qui apparaissent habituellement facultatives, sont obligatoires :

         ![Créer une connexion au serveur d’applications SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      - Si la propriété **Type de connexion** est définie sur **Groupe**, ces propriétés, qui apparaissent habituellement facultatives, sont obligatoires :

          ![Créer une connexion au serveur de messagerie SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Par défaut, le typage fort est utilisé pour rechercher des valeurs non valides en effectuant une validation XML par rapport au schéma. Ce comportement peut vous aider à détecter les problèmes plus tôt. L’option **Types sécurisés** est disponible pour la compatibilité descendante et ne vérifie que la longueur de chaîne. Apprenez-en davantage sur l’[option Types sécurisés](#safe-typing).

1. Spécifiez les paramètres nécessaires en fonction de la configuration de votre système SAP.

   Vous pouvez si vous le souhaitez fournir une ou plusieurs actions SAP. Cette liste d’actions spécifie les messages que le déclencheur reçoit de votre serveur SAP via la passerelle de données. Une liste vide spécifie que le déclencheur reçoit tous les messages. Si la liste comporte plus d’un message, le déclencheur reçoit seulement les messages spécifiés dans la liste. Tous les autres messages envoyés depuis votre serveur SAP sont rejetés par la passerelle.

   Vous pouvez sélectionner une action SAP dans le sélecteur de fichiers :

   ![Sélectionner une action SAP](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   Vous pouvez aussi spécifier une action manuellement :

   ![Entrer manuellement une action SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png) 

   Voici un exemple qui montre comment l’action apparaît quand vous configurez le déclencheur pour recevoir plusieurs messages.

   ![Exemple de déclencheur](media/logic-apps-using-sap-connector/example-trigger.png)  

   Pour plus d’informations sur l’action SAP, consultez [Schémas de message pour les opérations IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

1. Enregistrez maintenant votre application logique pour commencer à recevoir des messages de votre système SAP.
Dans la barre d’outils du concepteur, sélectionnez **Enregistrer**.

Votre application logique est maintenant prête à recevoir des messages de votre système SAP.

> [!NOTE]
> Le déclencheur SAP n’est pas un déclencheur d’interrogation : il s’agit d’un déclencheur basé sur un webhook. Le déclencheur est appelé depuis la passerelle seulement s’il existe un message : aucune interrogation n’est donc nécessaire.

### <a name="test-your-logic-app"></a>Tester votre application logique

1. Pour déclencher votre application logique, envoyez un message depuis votre système SAP.

1. Dans le menu de l’application logique, sélectionnez **Vue d’ensemble**. Examinez **Historique des exécutions** pour identifier toutes les nouvelles exécutions de votre application logique.

1. Ouvrez la dernière exécution, qui montre le message envoyé depuis votre système SAP dans la section des sorties du déclencheur.

## <a name="generate-schemas-for-artifacts-in-sap"></a>Générer des schémas pour les artefacts dans SAP

Cet exemple utilise une application logique que vous pouvez déclencher à l’aide d’une requête HTTP. L’action SAP envoie une demande à un système SAP pour générer les schémas des artefacts IDoc et BAPI spécifiés. Les schémas retournés dans la réponse sont chargés dans un compte d’intégration avec le connecteur Azure Resource Manager.

### <a name="add-an-http-request-trigger"></a>Ajouter un déclencheur de requête HTTP

1. Dans le portail Azure, créez une application logique vide, qui ouvre le Concepteur d’application logique.

1. Dans la zone de recherche, saisissez le filtre « http request ». Dans la liste **Déclencheurs**, sélectionnez **Lors de la réception d’une requête HTTP**.

   ![Ajouter un déclencheur de requête HTTP](./media/logic-apps-using-sap-connector/add-trigger.png)

1. Enregistrez maintenant votre application logique pour pouvoir générer une URL de point de terminaison pour votre application logique.
Dans la barre d’outils du concepteur, sélectionnez **Enregistrer**.

   L’URL du point de terminaison s’affiche désormais dans votre déclencheur, par exemple :

   ![Générer une URL pour le point de terminaison](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>Ajouter une action SAP pour générer des schémas

1. Dans le Concepteur d’application logique, sous le déclencheur, sélectionnez **Nouvelle étape**.

   ![Sélectionner « Nouvelle étape »](./media/logic-apps-using-sap-connector/add-action.png)

1. Dans la zone de recherche, entrez « sap » comme filtre. Dans la liste **Actions**, sélectionnez **Générer des schémas**.
  
   ![Sélectionner une action d’envoi à SAP](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   Vous pouvez aussi choisir l’onglet **Entreprise** et sélectionner l’action SAP.

   ![Sélectionner l’action d’envoi à SAP à partir de l’onglet Entreprise](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. Si vous êtes invité à entrer les informations de connexion, créez votre connexion SAP maintenant. Si votre connexion existe déjà, passez à l’étape suivante afin de configurer votre action SAP.

   **Créer une connexion SAP locale**

    1. Fournissez les informations de connexion pour votre serveur SAP. Pour la propriété **Passerelle de données**, sélectionnez la passerelle de données que vous avez créée dans le portail Azure lors de l’installation de la passerelle.

       - Si la propriété **Type de connexion** est définie sur **Serveur d’applications**, ces propriétés, qui apparaissent habituellement facultatives, sont obligatoires :

         ![Créer une connexion au serveur d’applications SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

       - Si la propriété **Type de connexion** est définie sur **Groupe**, ces propriétés, qui apparaissent habituellement facultatives, sont obligatoires :

         ![Créer une connexion au serveur de messagerie SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

        Par défaut, le typage fort est utilisé pour rechercher des valeurs non valides en effectuant une validation XML par rapport au schéma. Ce comportement peut vous aider à détecter les problèmes plus tôt. L’option **Types sécurisés** est disponible pour la compatibilité descendante et ne vérifie que la longueur de chaîne. Apprenez-en davantage sur l’[option Types sécurisés](#safe-typing).

    1. Quand vous avez terminé, sélectionnez **Créer**. 
   
       Logic Apps configure et teste votre connexion pour vérifier son bon fonctionnement.

1. Indiquez le chemin de l’artefact pour lequel vous voulez générer le schéma.

   Vous pouvez sélectionner l’action SAP dans le sélecteur de fichiers :

   ![Sélectionner une action SAP](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   Vous pouvez aussi entrer l’action manuellement :

   ![Entrer manuellement une action SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   Pour générer des schémas pour plusieurs artefacts, spécifiez les détails de l’action SAP pour chaque artefact, par exemple :

   ![Sélectionner Ajouter un nouvel élément](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![Afficher deux éléments](media/logic-apps-using-sap-connector/schema-generator-example.png)

   Pour plus d’informations sur l’action SAP, consultez [Schémas de message pour les opérations IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

1. Enregistrez votre application logique. Dans la barre d’outils du concepteur, sélectionnez **Enregistrer**.

### <a name="test-your-logic-app"></a>Tester votre application logique

1. Dans la barre d’outils du concepteur, sélectionnez **Exécuter** pour déclencher une exécution de votre application logique.

1. Ouvrez l’exécution et vérifiez les résultats pour l’action **Générer des schémas**.

   Les sorties montrent les schémas générés pour la liste de messages spécifiée.

### <a name="upload-schemas-to-an-integration-account"></a>Charger des schémas sur un compte d’intégration

Vous pouvez aussi télécharger ou stocker les schémas générés dans des référentiels, comme un objet blob, un stockage ou un compte d’intégration. Les comptes d’intégration offrent une expérience privilégiée avec d’autres actions XML : cet exemple montre donc comment charger des schémas dans un compte d’intégration pour la même application logique avec le connecteur Azure Resource Manager.

1. Dans le Concepteur d’application logique, sous le déclencheur, sélectionnez **Nouvelle étape**.

1. Dans la zone de recherche, entrez « Resource Manager » comme filtre. Sélectionnez **Créer ou mettre à jour une ressource**.

   ![Sélectionner une action Azure Resource Manager](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Entrez les détails de l’action, notamment votre abonnement Azure, le groupe de ressources Azure et le compte d’intégration. Pour ajouter des jetons SAP aux champs, cliquez dans les zones pour ces champs, puis sélectionnez dans la liste de contenu dynamique qui s’affiche.

    1. Ouvrez la liste **Ajouter un nouveau paramètre**, puis sélectionnez les champs **Emplacement** et **Propriétés**.

    1. Fournissez des détails pour ces nouveaux champs comme indiqué dans cet exemple.

       ![Entrer les détails pour l’action Azure Resource Manager](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   L’action SAP **Générer les schémas** génère des schémas sous forme de collection : le concepteur ajoute donc automatiquement une boucle **For each** à l’action. Voici un exemple qui montre comment cette action apparaît :

   ![Action Azure Resource Manager avec une boucle « for each »](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)  

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

1. Enregistrez votre application logique. Dans la barre d’outils du concepteur, sélectionnez **Enregistrer**.

### <a name="test-your-logic-app"></a>Tester votre application logique

1. Dans la barre d’outils du concepteur, sélectionnez **Exécuter** pour déclencher manuellement votre application logique.

1. Après une exécution réussie, accédez au compte d’intégration et vérifiez l’existence des schémas générés.

## <a name="enable-secure-network-communications"></a>Activer Secure Network Communications

Avant de commencer, vérifiez que vous vous conformez aux [prérequis](#pre-reqs) précédemment listés :

* La passerelle de données locale est installée sur un ordinateur qui se trouve sur le même réseau que votre système SAP.
* Pour l’authentification unique, la passerelle s’exécute en tant qu’utilisateur qui est mappé à un utilisateur SAP.
* La bibliothèque SNC qui fournit les fonctions de sécurité supplémentaire est installée sur le même ordinateur que la passerelle de données. [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos et NTLM en sont des exemples.

Pour activer SNC pour vos demandes vers ou depuis le système SAP, cochez la case **Utiliser SNC** dans la connexion SAP et renseignez ces propriétés :

   ![Configurer SNC SAP dans la connexion](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | Propriété | Description |
   |----------| ------------|
   | **SNC Library Path** (Chemin de la bibliothèque SNC) | Nom ou chemin de la bibliothèque SNC relatif au chemin absolu ou à l’emplacement d’installation NCo. `sapsnc.dll`, `.\security\sapsnc.dll` ou `c:\security\sapsnc.dll` en sont des exemples. |
   | **SNC SSO** (Authentification unique SNC) | Lorsque vous vous connectez via SNC, l’identité SNC est généralement utilisée pour authentifier l’appelant. Une autre option consiste à substituer cette identité afin que les informations d’utilisateur et de mot de passe puissent être utilisées pour authentifier l’appelant, mais la ligne est toujours chiffrée. |
   | **Mon nom SNC** | Dans la plupart des cas, cette propriété peut être omise. La solution SNC installée connaît généralement son propre nom SNC. Uniquement pour les solutions qui prennent en charge plusieurs identités, vous devrez peut-être spécifier l’identité à utiliser pour ce serveur ou cette destination spécifique. |
   | **Nom du partenaire SNC** | Nom du SNC back-end. |
   | **Qualité de protection SNC** | Qualité de service à utiliser pour la communication SNC de ce serveur ou cette destination spécifique. La valeur par défaut est définie par le système back-end. La valeur maximale est définie par le produit de sécurité utilisé pour SNC. |
   |||

   > [!NOTE]
   > Ne définissez pas les variables d’environnement SNC_LIB et SNC_LIB_64 sur l’ordinateur où vous avez la passerelle de données et la bibliothèque SNC. Si elles sont définies, elles sont prioritaires sur la valeur de la bibliothèque SNC passée via le connecteur.

<a name="safe-typing"></a>

## <a name="safe-typing"></a>Types sécurisés

Par défaut, quand vous créez votre connexion SAP, le typage fort est utilisé pour rechercher des valeurs non valides en effectuant une validation XML par rapport au schéma. Ce comportement peut vous aider à détecter les problèmes plus tôt. L’option **Types sécurisés** est disponible pour la compatibilité descendante et ne vérifie que la longueur de chaîne. Si vous choisissez **Types sécurisés**, les types DATS et TIMS dans SAP sont traités en tant que chaînes plutôt que comme leurs équivalents XML, `xs:date` et `xs:time`, où `xmlns:xs="http://www.w3.org/2001/XMLSchema"`. Les types sécurisés affectent le comportement pour la génération de schémas dans sa globalité, le message d’envoi pour à la fois la charge utile « envoyé » et la réponse « reçu » ainsi que le déclencheur. 

Quand le typage fort est utilisé (l’option**Types sécurisés** n’est pas activée), le schéma mappe les types DATS and TIMS à des types XML plus simples :

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

Lorsque vous envoyez des messages en utilisant un typage fort, la réponse DATS et TIMS est conforme au format de type XML correspondant :

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

Quand l’option**Types sécurisés** est activée, le schéma mappe les types DATS and TIMS aux champs de chaîne XML avec des restrictions de longueur uniquement, par exemple :

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="8" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="6" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
```

Lorsque les messages sont envoyés avec l’option**Types sécurisés** activée, la réponse DATS et TIMS ressemble à cet exemple :

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```


## <a name="known-issues-and-limitations"></a>Problèmes connus et limitations

Voici les problèmes et limitations connus pour le connecteur SAP :

* Un seul appel ou message Envoyer à SAP fonctionne avec tRFC. Le modèle de validation BAPI, qui permet par exemple d’effectuer plusieurs appels tRFC dans la même session, n’est pas pris en charge.
* Le déclencheur SAP ne prend pas en charge la réception des IDoc par lots provenant de SAP. Cette action peut entraîner des échecs de connexion de RFC entre votre système SAP et la passerelle de données.
* Le déclencheur SAP ne prend pas en charge les clusters de passerelle de données. Dans certains cas de basculement, le nœud de la passerelle de données qui communique avec le système SAP peut différer du nœud actif, ce qui entraîne un comportement inattendu. Pour les scénarios d’envoi, les clusters de passerelles de données sont pris en charge.
* Le connecteur SAP ne prend actuellement pas en charge les chaînes de routeur SAP. La passerelle de données locale doit exister sur le même réseau local que le système SAP que vous voulez connecter.

## <a name="connector-reference"></a>Référence de connecteur

Pour obtenir des détails techniques sur les déclencheurs, les actions et les limites, qui sont décrits par la description OpenAPI du connecteur (anciennement Swagger), consultez la [page de référence du connecteur](/connectors/sap/).

## <a name="next-steps"></a>Étapes suivantes

* [Connectez-vous à des systèmes locaux](../logic-apps/logic-apps-gateway-connection.md) à partir d’Azure Logic Apps.
* Découvrez comment valider, transformer et utiliser d’autres opérations message avec [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md).
* Découvrez d’autres [connecteurs Logic Apps](../connectors/apis-list.md).
