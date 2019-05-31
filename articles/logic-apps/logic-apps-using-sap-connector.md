---
title: Se connecter aux systèmes SAP - Azure Logic Apps
description: Accéder et gérer des ressources SAP en automatisant les flux de travail avec Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, LADocs
ms.topic: article
ms.date: 05/09/2019
tags: connectors
ms.openlocfilehash: bccefec80ef3afd6d312bb1048d3be5d8e708728
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258148"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Se connecter aux systèmes SAP à partir d’Azure Logic Apps

Cet article explique comment vous pouvez accéder à vos ressources SAP en local à partir à l’intérieur d’une application logique à l’aide du connecteur SAP. Le connecteur fonctionne avec les versions classiques de SAP, telles que les systèmes R/3 et ECC locaux. Le connecteur permet également l’intégration avec les systèmes SAP basés sur HANA plus récents de SAP comme s/4 HANA, où qu’ils sont hébergés - sur site ou dans le cloud. Le connecteur SAP prend en charge l’intégration de message ou des données vers et à partir de systèmes SAP NetWeaver via IDoc (Intermediate Document) ou de Business Application Programming Interface (BAPI) ou de fonction RFC (Remote Call).

Le connecteur SAP utilise le [bibliothèque du connecteur SAP .NET (NCo)](https://support.sap.com/en/product/connectors/msnet.html) et fournit ces opérations ou les actions :

* **Envoyer à SAP** : Envoyer des IDoc sur tRFC, appeler des fonctions BAPI sur RFC ou appeler des RFC/tRFC dans les systèmes SAP.
* **Recevoir de SAP** : Recevoir des IDoc via tRFC, appeler des fonctions BAPI via tRFC ou appeler des RFC/tRFC dans les systèmes SAP.
* **Générer des schémas** : Créer des schémas pour les artefacts SAP IDoc, BAPI et RFC.

Pour toutes les opérations ci-dessus, le connecteur SAP prend en charge l'authentification de base via un nom d'utilisateur et un mot de passe. Le connecteur prend également en charge [réseau CRS (Communications sécurisées)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true), qui peut être utilisée pour SAP NetWeaver Single Sign-On, ou pour les fonctionnalités de sécurité supplémentaires fournies par un produit de sécurité externes.

Le connecteur SAP s’intègre aux systèmes SAP locaux via la [passerelle de données locale](../logic-apps/logic-apps-gateway-connection.md). Dans les scénarios d’envoi, par exemple, lors de l’envoi d’un message à partir d’applications logiques à un système SAP, la passerelle de données agit comme un client RFC et transfère les demandes provenant d’applications logiques à SAP.
De même, dans les scénarios de réception, la passerelle de données agit en tant que RFC serveur qui reçoit des demandes de SAP et la transfère à l’application logique.

Cet article explique comment créer des exemples d’applications logiques qui s’intègrent à SAP en couvrant les scénarios d’intégration décrits précédemment.

<a name="pre-reqs"></a>

## <a name="prerequisites"></a>Conditions préalables

Pour suivre cet article, vous avez besoin de ces éléments :

* Un abonnement Azure. Si vous n’avez pas encore d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/).

* L’application logique à partir de laquelle vous souhaitez accéder à votre système SAP et un déclencheur qui démarre le flux de travail de votre application logique. Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md) et [Démarrage rapide : Créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Votre [serveur d’applications SAP](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) ou [serveur de messagerie SAP](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm)

* Téléchargez et installez la dernière [passerelle de données locale](https://www.microsoft.com/download/details.aspx?id=53127) sur n’importe quel ordinateur local. Assurez-vous de configurer votre passerelle dans le portail Azure avant de continuer. La passerelle vous permet en toute sécurité l’accès des données localement et ressources. Pour plus d’informations, consultez [Installer la passerelle de données locale pour Azure Logic Apps](../logic-apps/logic-apps-gateway-install.md).

* Si vous utilisez SNC avec unique Sign-On (SSO), assurez-vous que la passerelle s’exécute en tant qu’utilisateur qui est mappé vers l’utilisateur SAP. Pour modifier le compte par défaut, sélectionnez **modifier compte**, puis entrez les informations d’identification de l’utilisateur.

  ![Modifier le compte de passerelle](./media/logic-apps-using-sap-connector/gateway-account.png)

* Si vous activez SNC avec un produit de sécurité externes, copiez la bibliothèque SNC ou les fichiers sur le même ordinateur sur lequel la passerelle est installée. Voici quelques exemples de produits SNC [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos, NTLM, et ainsi de suite.

* Téléchargez et installez la dernière bibliothèque cliente SAP, qui est actuellement [le connecteur SAP (NCo) 3.0.21.0 pour Microsoft .NET Framework 4.0 et Windows 64 bits (x64)](https://softwaredownloads.sap.com/file/0020000001865512018), sur le même ordinateur que la passerelle de données sur site. Installez cette version ou une version ultérieure pour ces raisons :

  * Les versions antérieures du NCo SAP peuvent subir un interblocage quand plusieurs messages IDoc sont envoyés en même temps. Cette situation bloque tous les messages envoyés ultérieurement à la destination SAP, ce qui engendre une expiration des messages.

  * La passerelle de données locale s’exécute uniquement sur les systèmes 64 bits. Dans le cas contraire, vous obtenez une erreur « image incorrecte », car le service hôte de la passerelle de données ne prend pas en charge les assemblys 32 bits.

  * Le service hôte de la passerelle de données et l’adaptateur SAP Microsoft utilisent tous deux .NET Framework 4.5. Le NCo SAP pour .NET Framework 4.0 fonctionne avec les processus qui utilisent un runtime .NET 4.0 à 4.7.1. Le NCo SAP pour .NET Framework 2.0 fonctionne avec les processus qui utilisent un runtime .NET 2.0 à 3.5 et ne fonctionne plus avec la passerelle de données locale la plus récente.

* Contenu du message que vous pouvez envoyer à votre serveur SAP, comme un exemple de fichier IDoc. Ce contenu doit être au format XML et inclure l’espace de noms pour l’action SAP que vous souhaitez utiliser.

<a name="add-trigger"></a>

## <a name="send-to-sap"></a>Envoyer à SAP

Cet exemple utilise une application logique que vous pouvez déclencher à l’aide d’une requête HTTP. L’application logique envoie un document intermédiaire (IDoc) à un serveur SAP et renvoie une réponse au demandeur qui a appelé l’application logique. 

### <a name="add-http-request-trigger"></a>Ajouter un déclencheur de requête HTTP

Dans Azure Logic Apps, chaque application logique doit démarrer avec un [déclencheur](../logic-apps/logic-apps-overview.md#logic-app-concepts), qui s’active lorsqu’un événement spécifique se produit ou lorsqu’une condition particulière est remplie. Chaque fois que le déclencheur s’active, le moteur Logic Apps crée une instance d’application logique et lance l’exécution du flux de travail de votre application.

Dans cet exemple, vous allez créer une application logique avec un point de terminaison dans Azure afin de pouvoir envoyer des *requêtes HTTP POST* à votre application logique. Lorsque votre application logique reçoit ces requêtes HTTP, le déclencheur est activé et passe à l’étape suivante de votre flux de travail.

1. Dans le [portail Azure](https://portal.azure.com), créez une application logique vide, ce qui ouvre le Concepteur d’application logique.

1. Dans la zone de recherche, saisissez le filtre « http request ». Dans la liste des déclencheurs, sélectionnez ce déclencheur : **Lors de la réception d’une requête HTTP**

   ![Ajouter un déclencheur de requête HTTP](./media/logic-apps-using-sap-connector/add-trigger.png)

1. Enregistrez maintenant votre application logique pour pouvoir générer une URL de point de terminaison pour votre application logique. Dans la barre d’outils du concepteur, choisissez **Enregistrer**.

   L’URL du point de terminaison s’affiche désormais dans votre déclencheur, par exemple :

   ![Générer une URL pour le point de terminaison](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-sap-action"></a>Ajouter une action SAP

Dans Azure Logic Apps, une [action](../logic-apps/logic-apps-overview.md#logic-app-concepts) est une étape de votre flux de travail qui suit un déclencheur ou une autre action. Si vous n’avez pas encore ajouté de déclencheur à votre application logique et que vous souhaitez suivre cet exemple, [ajoutez le déclencheur décrit dans cette section](#add-trigger).

1. Dans le Concepteur d’application logique, sous le déclencheur, choisissez **nouvelle étape**.

   ![Choisir « Nouvelle étape »](./media/logic-apps-using-sap-connector/add-action.png)

1. Dans la zone de recherche, entrez « sap » comme filtre. Dans la liste des actions, sélectionnez cette action : **Envoyer un message à SAP**
  
   ![Sélectionner une action d’envoi à SAP](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   Au lieu d’effectuer une recherche, vous pouvez aussi choisir l’onglet **Entreprise** et sélectionner l’action SAP.

   ![Sélectionner l’action d’envoi à SAP à partir de l’onglet Entreprise](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. Si vous êtes invité à entrer les informations de connexion, créez votre connexion SAP maintenant. Sinon, si votre connexion existe déjà, passez à l’étape suivante afin de configurer votre action SAP.

   **Créer une connexion SAP locale**

   1. Fournissez les informations de connexion pour votre serveur SAP. Pour la propriété **Passerelle de données**, sélectionnez la passerelle de données que vous avez créée dans le portail Azure lors de l’installation de la passerelle.

      Si la propriété **Type de connexion** est définie sur **Serveur d’applications**, ces propriétés, qui apparaissent habituellement facultatives, sont obligatoires :

      ![Créer une connexion au serveur d’applications SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      Si la propriété **Type de connexion** est définie sur **Groupe**, ces propriétés, qui apparaissent habituellement facultatives, sont obligatoires :

      ![Créer une connexion au serveur de messagerie SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Par défaut, le typage fort est utilisé pour vérifier les valeurs non valides en effectuant la validation XML par rapport au schéma. Ce comportement peut vous aider à détecter les problèmes plus tôt. Le **tapant sécurisé** option est disponible pour la compatibilité descendante et ne vérifie que la longueur de chaîne. En savoir plus sur la [ **tapant sécurisé** option](#safe-typing).

   1. Lorsque vous êtes prêt, choisissez **Créer**.

      Logic Apps configure et teste votre connexion pour vérifier son bon fonctionnement.

1. Maintenant, recherchez et sélectionnez une action à partir de votre serveur SAP.

   1. Dans le **Action SAP** , sélectionnez l’icône de dossier. Dans la liste des fichiers, recherchez et sélectionnez l’action que vous voulez utiliser. Pour naviguer dans la liste, utilisez les flèches.

      Cet exemple sélectionne un IDoc avec **commandes** type.

      ![Rechercher et sélectionner l’action IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      Si vous ne trouvez pas l’action que vous souhaitez, vous pouvez entrer un chemin d’accès manuellement, par exemple :

      ![Fournir manuellement le chemin d’accès à l’action IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > Spécifiez la valeur de l’action SAP via l’éditeur d’expression. De cette façon, vous pouvez utiliser la même action pour différents types de messages.

      Pour plus d’informations sur les opérations IDoc, consultez [Schémas de message pour les opérations IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

   1. Cliquez dans la zone **Message d’entrée** pour afficher la liste du contenu dynamique. Dans cette liste, sous **Lors de la réception d’une demande HTTP**, sélectionnez le champ **Corps**.

      Cette étape inclut le contenu du corps de votre déclencheur de requête HTTP et envoie ce résultat à votre serveur SAP.

      ![Sélectionnez le champ « Corps ».](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      Une fois que vous avez terminé, votre action SAP ressemble à cet exemple :

      ![Terminer l’action SAP](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Enregistrez votre application logique. Dans la barre d’outils du concepteur, choisissez **Enregistrer**.

<a name="add-response"></a>

### <a name="add-http-response-action"></a>Ajouter une action de réponse HTTP

Ajoutez maintenant une action de réponse au flux de travail de votre application logique et incluez le résultat de l’action SAP. De cette façon, votre application logique renvoie les résultats à partir de votre serveur SAP au demandeur d’origine.

1. Dans le Concepteur d’application logique, sous l’action de SAP, choisissez **nouvelle étape**.

1. Dans la zone de recherche, saisissez le filtre « response ». Dans la liste des actions, sélectionnez cette action : **Réponse**

1. Cliquez dans la zone **Corps** pour afficher la liste du contenu dynamique. Dans cette liste, sous **envoyer un message à SAP**, sélectionnez le **corps** champ.

   ![Terminer l’action SAP](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Enregistrez votre application logique.

### <a name="test-your-logic-app"></a>Tester votre application logique

1. Si votre application logique n’est pas déjà activée, dans le menu de votre application logique, choisissez **Vue d’ensemble**. Dans la barre d’outils, choisissez **Activer**.

1. Dans la barre d’outils du Concepteur d’application logique, choisissez **Exécuter**. Cette étape démarre manuellement votre application logique.

1. Déclenchez votre application logique en envoyant une requête HTTP POST à l’URL de votre déclencheur de requête HTTP et incluez le contenu du message avec votre requête. Pour envoyer la requête, vous pouvez utiliser un outil tel que [Postman](https://www.getpostman.com/apps).

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

Félicitations, vous venez de créer une application logique qui peut communiquer avec votre serveur SAP. Maintenant que vous avez configuré une connexion SAP pour votre application logique, vous pouvez explorer d’autres actions SAP disponibles, telles que BAPI et RFC.

## <a name="receive-from-sap"></a>Recevoir de SAP

Cet exemple utilise une application logique qui se déclenche lors de la réception d’un message provenant d’un système SAP.

### <a name="add-sap-trigger"></a>Ajouter un déclencheur SAP

1. Dans le portail Azure, créez une application logique vide, qui ouvre le Concepteur d’application logique.

1. Dans la zone de recherche, entrez « sap » comme filtre. Dans la liste des déclencheurs, sélectionnez ce déclencheur : **Quand un message est reçu de SAP**

   ![Ajouter un déclencheur SAP](./media/logic-apps-using-sap-connector/add-sap-trigger.png)

   Alternativement, vous pouvez accéder à la **Enterprise** et sélectionnez le déclencheur :

   ![Ajouter un déclencheur SAP à partir de l’onglet de l’entreprise](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. Si vous êtes invité à entrer les informations de connexion, créez votre connexion SAP maintenant. Sinon, si votre connexion existe déjà, passez à l’étape suivante afin de configurer votre action SAP.

   **Créer une connexion SAP locale**

   1. Fournissez les informations de connexion pour votre serveur SAP. Pour la propriété **Passerelle de données**, sélectionnez la passerelle de données que vous avez créée dans le portail Azure lors de l’installation de la passerelle.

      Si la propriété **Type de connexion** est définie sur **Serveur d’applications**, ces propriétés, qui apparaissent habituellement facultatives, sont obligatoires :

      ![Créer une connexion au serveur d’applications SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      Si la propriété **Type de connexion** est définie sur **Groupe**, ces propriétés, qui apparaissent habituellement facultatives, sont obligatoires :

      ![Créer une connexion au serveur de messagerie SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Par défaut, le typage fort est utilisé pour vérifier les valeurs non valides en effectuant la validation XML par rapport au schéma. Ce comportement peut vous aider à détecter les problèmes plus tôt. Le **tapant sécurisé** option est disponible pour la compatibilité descendante et ne vérifie que la longueur de chaîne. En savoir plus sur la [ **tapant sécurisé** option](#safe-typing).

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
Dans la barre d’outils du concepteur, choisissez **Enregistrer**.

Votre application logique est maintenant prête à recevoir des messages de votre système SAP.

> [!NOTE]
> Le déclencheur SAP n’est pas un déclencheur d’interrogation : il s’agit d’un déclencheur basé sur un webhook. Le déclencheur est appelé depuis la passerelle seulement s’il existe un message : aucune interrogation n’est donc nécessaire.

### <a name="test-your-logic-app"></a>Tester votre application logique

1. Pour déclencher votre application logique, envoyez un message depuis votre système SAP.

1. Dans le menu de l’application logique, choisissez **Vue d’ensemble**, et consultez **Historique des exécutions** pour les nouvelles exécutions de votre application logique.

1. Ouvrez la dernière exécution, qui montre le message envoyé depuis votre système SAP dans la section des sorties du déclencheur.

## <a name="generate-schemas-for-artifacts-in-sap"></a>Générer des schémas pour les artefacts dans SAP

Cet exemple utilise une application logique que vous pouvez déclencher à l’aide d’une requête HTTP. L’action SAP envoie une demande à un système SAP pour générer les schémas des artefacts IDoc (Intermediate Document) et BAPI spécifiés. Les schémas retournés dans la réponse sont chargés dans un compte d’intégration avec le connecteur Azure Resource Manager.

### <a name="add-http-request-trigger"></a>Ajouter un déclencheur de requête HTTP

1. Dans le portail Azure, créez une application logique vide, qui ouvre le Concepteur d’application logique.

1. Dans la zone de recherche, saisissez le filtre « http request ». Dans la liste des déclencheurs, sélectionnez ce déclencheur : **Lors de la réception d’une requête HTTP**

   ![Ajouter un déclencheur de requête HTTP](./media/logic-apps-using-sap-connector/add-trigger.png)

1. Enregistrez maintenant votre application logique pour pouvoir générer une URL de point de terminaison pour votre application logique.
Dans la barre d’outils du concepteur, choisissez **Enregistrer**.

   L’URL du point de terminaison s’affiche désormais dans votre déclencheur, par exemple :

   ![Générer une URL pour le point de terminaison](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-sap-action-to-generate-schemas"></a>Ajouter une action SAP pour générer des schémas

1. Dans le Concepteur d’application logique, sous le déclencheur, choisissez **nouvelle étape**.

   ![Choisir « Nouvelle étape »](./media/logic-apps-using-sap-connector/add-action.png)

1. Dans la zone de recherche, entrez « sap » comme filtre. Dans la liste des actions, sélectionnez cette action : **Générer les schémas**
  
   ![Sélectionner une action d’envoi à SAP](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   Vous pouvez aussi choisir l’onglet **Entreprise** et sélectionner l’action SAP.

   ![Sélectionner l’action d’envoi à SAP à partir de l’onglet Entreprise](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. Si vous êtes invité à entrer les informations de connexion, créez votre connexion SAP maintenant. Sinon, si votre connexion existe déjà, passez à l’étape suivante afin de configurer votre action SAP.

   **Créer une connexion SAP locale**

   1. Fournissez les informations de connexion pour votre serveur SAP. Pour la propriété **Passerelle de données**, sélectionnez la passerelle de données que vous avez créée dans le portail Azure lors de l’installation de la passerelle.

      Si la propriété **Type de connexion** est définie sur **Serveur d’applications**, ces propriétés, qui apparaissent habituellement facultatives, sont obligatoires :

      ![Créer une connexion au serveur d’applications SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      Si la propriété **Type de connexion** est définie sur **Groupe**, ces propriétés, qui apparaissent habituellement facultatives, sont obligatoires :

      ![Créer une connexion au serveur de messagerie SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Par défaut, le typage fort est utilisé pour vérifier les valeurs non valides en effectuant la validation XML par rapport au schéma. Ce comportement peut vous aider à détecter les problèmes plus tôt. Le **tapant sécurisé** option est disponible pour la compatibilité descendante et ne vérifie que la longueur de chaîne. En savoir plus sur la [ **tapant sécurisé** option](#safe-typing).

   1. Lorsque vous êtes prêt, choisissez **Créer**. 
   
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

1. Enregistrez votre application logique. Dans la barre d’outils du concepteur, choisissez **Enregistrer**.

### <a name="test-your-logic-app"></a>Tester votre application logique

1. Dans la barre d’outils du concepteur, choisissez **Exécuter** pour déclencher une exécution de votre application logique.

1. Ouvrez l’exécution et vérifier les résultats pour le **générer des schémas** action.

   Les sorties montrent les schémas générés pour la liste de messages spécifiée.

### <a name="upload-schemas-to-integration-account"></a>Charger des schémas dans le compte d’intégration

Vous pouvez aussi télécharger ou stocker les schémas générés dans des référentiels, comme un objet blob, un stockage ou un compte d’intégration. Les comptes d’intégration offrent une expérience privilégiée avec d’autres actions XML : cet exemple montre donc comment charger des schémas dans un compte d’intégration pour la même application logique avec le connecteur Azure Resource Manager.

1. Dans le Concepteur d’application logique, sous le déclencheur, sélectionnez **Nouvelle étape**.

1. Dans la zone de recherche, entrez « Resource Manager » comme filtre. Sélectionnez cette action : **Créer ou mettre à jour une ressource**

   ![Sélectionner une action Azure Resource Manager](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Entrez les détails de l’action, y compris votre abonnement Azure, le groupe de ressources Azure et le compte d’intégration. Pour ajouter des jetons SAP pour les champs, cliquez dans les cases pour ces champs, puis sélectionnez dans la liste de contenu dynamique qui s’affiche.

   1. Ouvrez le **ajouter un nouveau paramètre** liste, puis sélectionnez le **emplacement** et **propriétés** champs.

   1. Fournissent des détails pour ces nouveaux champs comme indiqué dans cet exemple.

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

1. Enregistrez votre application logique. Dans la barre d’outils du concepteur, choisissez **Enregistrer**.

### <a name="test-your-logic-app"></a>Tester votre application logique

1. Dans la barre d’outils du concepteur, choisissez **Exécuter** pour déclencher manuellement votre application logique.

1. Après une réussite exécuter, accédez au compte d’intégration et vérifier que les schémas générés sont présents.

## <a name="enable-secure-network-communications-snc"></a>Activer les Communications réseau sécurisées (CRS)

Avant de commencer, assurez-vous que vous avez rempli précédemment répertoriées [conditions préalables](#pre-reqs):

* La passerelle de données sur site est installée sur un ordinateur qui se trouve dans le même réseau que votre système SAP.

* Pour l’authentification unique, la passerelle s’exécute en tant qu’utilisateur qui est mappé à l’utilisateur SAP.

* Bibliothèque SNC qui fournit les fonctions de sécurité supplémentaire a été installé sur le même ordinateur en tant que passerelle de données. Certains de ces exemples incluent [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos, NTLM, et ainsi de suite.

Pour activer les SNC pour vos demandes vers ou à partir du système SAP, sélectionnez le **utiliser SNC** case à cocher dans la connexion SAP et fournissez ces propriétés :

   ![Configurer des SNC SAP dans la connexion](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | Propriété | Description |
   |----------| ------------|
   | **Bibliothèque SNC** | Nom de la bibliothèque SNC ou chemin d’accès relatif à l’emplacement d’installation NCo ou chemin d’accès absolu. Par exemple, `sapsnc.dll` ou `.\security\sapsnc.dll` ou `c:\security\sapsnc.dll` |
   | **SNC SSO** | Lors de la connexion via SNC, l’identité SNC est généralement utilisée pour authentifier l’appelant. Une autre option consiste à substituer afin que les informations d’utilisateur et mot de passe peuvent être utilisées pour authentifier l’appelant, mais la ligne est toujours chiffrée. |
   | **SNC mon nom** | Dans la plupart des cas, cette propriété peut être omise. La solution CRS installée sait généralement son propre nom CRS. Uniquement pour les solutions prenant en charge de « plusieurs identités », vous devrez peut-être spécifier l’identité à utiliser pour ce serveur ou de destination particulier. |
   | **Nom du partenaire SNC** | Le nom du serveur principal SNC |
   | **Qualité SNC de Protection** | Qualité de Service à utiliser pour la communication SNC ce particulier/du serveur de destination. Valeur par défaut est définie par le système back-end. La valeur maximale est définie par le produit de sécurité utilisé pour les SNC. |
   |||

   > [!NOTE]
   > Variables d’environnement SNC_LIB et SNC_LIB_64 ne doivent pas être définies sur l’ordinateur où vous avez passerelle de données et de la bibliothèque SNC. Si la valeur, ils seraient prioritaire sur la valeur de la bibliothèque SNC passée via le connecteur.

<a name="safe-typing"></a>

## <a name="safe-typing"></a>Tapant sans échec

Par défaut, lorsque vous créez votre connexion SAP, un typage fort est utilisé pour vérifier les valeurs non valides en effectuant la validation XML par rapport au schéma. Ce comportement peut vous aider à détecter les problèmes plus tôt. Le **tapant sécurisé** option est disponible pour la compatibilité descendante et ne vérifie que la longueur de chaîne. Si vous choisissez **tapant sécurisé**, les types de fichiers DAT et Tim dans SAP sont traités en tant que chaînes plutôt que comme leurs équivalents XML, `xs:date` et `xs:time` où `xmlns:xs="http://www.w3.org/2001/XMLSchema"`. Tapant sécurisé affecte le comportement de tous les de génération de schéma, l’envoi de messages pour la charge utile « reçu » et la réponse « reçu » et déclencheur. 

Lorsqu’un typage fort est utilisé (**tapant sécurisé** n’est ne pas activée), le schéma mappe les types de fichiers DAT et Tim sur des types XML plus simples :

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

Lors de l’envoi des messages à l’aide d’un typage fort, la réponse DATS et TIM est conforme au format de type XML correspondant :

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

Lorsque **tapant sécurisé** est activé, le schéma mappe les fichiers DAT et les types de TIM au format XML par exemple des champs avec uniquement, les restrictions de longueur de chaîne :

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

Lors de l’envoi de messages avec **tapant sécurisé** activé, la réponse DATS et Tim ressemble à cet exemple :

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```


## <a name="known-issues-and-limitations"></a>Problèmes connus et limitations

Voici les problèmes et limitations connus pour le connecteur SAP :

* Un seul appel ou message Envoyer à SAP fonctionne avec tRFC. Le modèle de validation BAPI (Business Application Programming Interface), qui permet par exemple d’effectuer plusieurs appels de tRFC dans la même session, n’est pas pris en charge.

* Le déclencheur SAP ne prend pas en charge la réception des IDOC par lots provenant de SAP. Cette action peut entraîner des échecs de connexion de RFC entre votre système SAP et la passerelle de données.

* Le déclencheur SAP ne prend pas en charge les clusters de passerelle de données. Dans certains cas de basculement, le nœud de la passerelle de données qui communique avec le système SAP peut différer du nœud actif, ce qui entraîne un comportement inattendu. Pour les scénarios d’envoi, les clusters de passerelles de données sont pris en charge.

* Le connecteur SAP ne prend actuellement pas en charge les chaînes de routeur SAP. La passerelle de données locale doit exister sur le même réseau local que le système SAP que vous voulez connecter.

## <a name="connector-reference"></a>Référence de connecteur

Pour obtenir des détails techniques sur les déclencheurs, actions et limites, qui sont décrits par OpenAPI du connecteur (anciennement Swagger) description, consultez le [page de référence du connecteur](/connectors/sap/).

## <a name="next-steps"></a>Étapes suivantes

* [Se connecter à des systèmes locaux](../logic-apps/logic-apps-gateway-connection.md) à partir d’applications logiques
* Découvrez comment valider, transformer et utiliser d’autres opérations message dans [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md).
* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)
