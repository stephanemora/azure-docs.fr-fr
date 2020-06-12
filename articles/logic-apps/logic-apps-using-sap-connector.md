---
title: Se connecter à des systèmes SAP
description: Accéder aux ressources SAP et les gérer en automatisant les workflows avec Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, logicappspm
ms.topic: article
ms.date: 05/29/2020
tags: connectors
ms.openlocfilehash: 557e162d9d7f0238d5554c32cb3ae96885877dbe
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2020
ms.locfileid: "84220501"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Se connecter aux systèmes SAP à partir d’Azure Logic Apps

> [!IMPORTANT]
> Les connecteurs Serveur d’applications SAP et Serveur de messagerie SAP antérieurs ont été dépréciés le 29 février 2020. Le connecteur SAP actuel regroupe ces connecteurs SAP antérieurs : vous ne devez donc pas changer le type de connexion, il est entièrement compatible avec les connecteurs précédents, il offre de nombreuses fonctionnalités supplémentaires et continue d’utiliser la bibliothèque de connecteurs .NET SAP (SAP NCo).
>
> Pour les applications logiques qui utilisent les connecteurs plus anciens, [migrez vers le connecteur le plus récent](#migrate) avant la date de dépréciation. Sinon, ces applications logiques subiront des échecs d’exécution et ne seront pas en mesure d’envoyer des messages à votre système SAP.

Cet article explique comment accéder à vos ressources SAP locales à partir d’une application logique en utilisant le connecteur SAP. Le connecteur fonctionne avec les versions classiques de SAP, telles que les systèmes R/3 et ECC locaux. Le connecteur permet également l’intégration aux nouveaux systèmes SAP basés sur HANA, tels que S/4 HANA, qu’ils soient hébergés localement ou dans le cloud. Le connecteur SAP prend en charge l’intégration de messages ou de données vers et depuis des systèmes SAP NetWeaver via IDoc (Intermediate Document), BAPI (Business Application Programming Interface) ou RFC (Remote Function Call).

Le connecteur SAP utilise la [bibliothèque NCo (.NET Connector) SAP](https://support.sap.com/en/product/connectors/msnet.html) et fournit les actions suivantes :

* **Envoyer un message à SAP** : envoyer un IDoc sur tRFC, appeler des fonctions BAPI sur RFC ou appeler RFC/tRFC dans des systèmes SAP.

* **Quand un message est reçu de SAP** : recevoir un IDoc sur tRFC, appeler des fonctions BAPI sur tRFC ou appeler RFC/tRFC dans des systèmes SAP.

* **Générer des schémas** : générer des schémas pour les artefacts SAP pour IDoc, BAPI ou RFC.

Pour ces opérations, le connecteur SAP prend en charge l’authentification de base via un nom d’utilisateur et un mot de passe. Le connecteur prend également en charge [Secure Network Communications (SNC)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true). SNC peut être utilisé pour l’authentification unique SAP NetWeaver ou pour des fonctionnalités de sécurité supplémentaires fournies par un produit de sécurité externe.

Cet article explique comment créer des exemples d’applications logiques qui s’intègrent à SAP en couvrant les scénarios d’intégration décrits précédemment. Pour les applications logiques qui utilisent les connecteurs SAP plus anciens, cet article montre comment migrer vos applications logiques vers le connecteur SAP le plus récent.

<a name="pre-reqs"></a>

## <a name="prerequisites"></a>Prérequis

Pour suivre cet article, vous avez besoin de ces éléments :

* Un abonnement Azure. Si vous n’avez pas encore d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/).

* L’application logique à partir de laquelle vous souhaitez accéder à votre système SAP et un déclencheur qui démarre le flux de travail de votre application logique. Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md) et [Démarrage rapide : Créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Votre [serveur d’applications SAP](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) ou [serveur de messagerie SAP](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm).

* Le contenu du message que vous pouvez envoyer à votre serveur SAP, comme un exemple de fichier IDoc, doit être au format XML et inclure l’espace de noms pour l’action SAP que vous souhaitez utiliser.

* Pour utiliser le déclencheur **Quand un message est reçu de SAP**, vous devez également effectuer les étapes de configuration suivantes :

  * Configurez les autorisations de sécurité de votre passerelle SAP avec ce paramètre :

    `"TP=Microsoft.PowerBI.EnterpriseGateway HOST=<gateway-server-IP-address> ACCESS=*"`

  * Configurez la journalisation de sécurité de votre passerelle SAP, qui permet de rechercher les erreurs de liste de contrôle d’accès (ACL, access-control list) et n’est pas activée par défaut. Dans le cas contraire, vous recevez l’erreur suivante :

    `"Registration of tp Microsoft.PowerBI.EnterpriseGateway from host <host-name> not allowed"`

    Pour plus d’informations, consultez la rubrique d’aide SAP [Configuration de la journalisation de la passerelle](https://help.sap.com/erp_hcm_ias2_2015_02/helpdata/en/48/b2a710ca1c3079e10000000a42189b/frameset.htm).

<a name="multi-tenant"></a>

### <a name="multi-tenant-azure-prerequisites"></a>Conditions préalables pour une instance Azure mutualisée

Ces conditions préalables s’appliquent lorsque vos applications logiques s’exécutent dans une instance Azure mutualisée et que vous souhaitez utiliser le connecteur SAP managé, qui ne s’exécute pas en mode natif dans un [environnement de service d’intégration (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Dans le cas contraire, si vous utilisez un ISE de niveau Premium et souhaitez utiliser le connecteur SAP qui s’exécute en mode natif dans l’ISE, consultez [Conditions préalables de l’environnement de service d’intégration (ISE)](#sap-ise).

Le connecteur SAP managé (non-ISE) s’intègre aux systèmes SAP locaux via la [passerelle de données locale](../logic-apps/logic-apps-gateway-connection.md). Par exemple, dans les scénarios d’envoi de messages, lorsqu’un message est envoyé d’une application logique à un système SAP, la passerelle de données agit comme un client RFC et transfère les demandes reçues de l’application logique à SAP. De même, dans les scénarios de réception de messages, la passerelle de données agit en tant que serveur RFC qui reçoit des demandes de SAP et les transfère à l’application logique.

* [Téléchargez et installez la passerelle de données locale](../logic-apps/logic-apps-gateway-install.md) sur votre ordinateur local. Ensuite, [créez une ressource de passerelle Azure](../logic-apps/logic-apps-gateway-connection.md#create-azure-gateway-resource) pour cette passerelle dans le portail Azure. La passerelle vous permet d’accéder en toute sécurité aux données et ressources locales.

  Comme meilleure pratique, nous vous recommandons d’utiliser une version prise en charge de la passerelle de données locale. Microsoft publie une nouvelle version chaque mois. Actuellement, Microsoft prend en charge les six dernières versions. Si vous rencontrez un problème avec votre passerelle, essayez de[la mettre à niveau vers la dernière version](https://aka.ms/on-premises-data-gateway-installer), qui peut inclure des mises à jour pour résoudre votre problème.

* [Téléchargez et installez la dernière bibliothèque de client SAP](#sap-client-library-prerequisites) sur le même ordinateur que la passerelle de données locale.

<a name="sap-ise"></a>

### <a name="integration-service-environment-ise-prerequisites"></a>Conditions préalables pour l’environnement de service d’intégration (ISE)

Ces conditions préalables s’appliquent lorsque vos applications logiques s’exécutent dans un [environnement de service d’intégration (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) au niveau Premium (et non au niveau du développeur) et que vous souhaitez utiliser le connecteur SAP qui s’exécute en mode natif dans un ISE. Un ISE fournit un accès aux ressources protégées par un réseau virtuel Azure et offre d’autres connecteurs ISE natifs qui permettent aux applications logiques d’accéder directement à des ressources locales sans utiliser la passerelle de données locale.

> [!NOTE]
> Bien que le connecteur ISE SAP soit visible au sein d’un ISE au niveau du développeur, les tentatives d’installation du connecteur échouent.

1. Si vous ne disposez pas déjà d’un compte de stockage Azure et d’un conteneur d’objets blob, créez ce conteneur à l’aide du [portail Azure](../storage/blobs/storage-quickstart-blobs-portal.md) ou d’[Explorateur Stockage Azure](../storage/blobs/storage-quickstart-blobs-storage-explorer.md).

1. [Téléchargez et installez la dernière bibliothèque de client SAP](#sap-client-library-prerequisites) sur votre ordinateur local. Vous devez disposer des fichiers d’assembly suivants :

   * libicudecnumber.dll
   * rscp4n.dll
   * sapnco.dll
   * sapnco_utils.dll

1. Créez un fichier .zip qui comprend ces assemblys et chargez ce package dans votre conteneur d’objets blob dans Stockage Azure.

1. Dans le portail Azure ou Explorateur Stockage Azure, accédez à l’emplacement du conteneur dans lequel vous avez chargé le fichier .zip.

1. Copiez l’URL de cet emplacement, en veillant à inclure le jeton de signature d’accès partagé.

   Dans le cas contraire, le jeton de signature d’accès partagé n’est pas autorisé et le déploiement du connecteur ISE SAP échoue.

1. Avant de pouvoir utiliser le connecteur ISE SAP, vous devez installer et déployer le connecteur dans votre ISE.

   1. Dans le [portail Azure](https://portal.azure.com), recherchez et ouvrez votre ISE.
   
   1. Dans le menu ISE, sélectionnez **Connecteurs managés** > **Ajouter**. Dans la liste des connecteurs, recherchez et sélectionnez **SAP**.
   
   1. Dans le volet **Ajouter un nouveau connecteur managé**, dans la zone **Package SAP**, collez l’URL du fichier .zip qui contient les assemblys SAP. *Veillez à inclure le jeton de signature d’accès partagé.*

   1. Sélectionnez **Créer** lorsque vous avez terminé.

   Pour plus d’informations, consultez [Ajouter des connecteurs ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment).

1. Si votre instance SAP et votre ISE se trouvent dans des réseaux virtuels différents, vous devez également [associer ces réseaux par Peering](../virtual-network/tutorial-connect-virtual-networks-portal.md) afin que le réseau virtuel de votre ISE soit connecté au réseau virtuel de votre instance SAP.

<a name="sap-client-library-prerequisites"></a>

### <a name="sap-client-library-prerequisites"></a>Conditions préalables pour la bibliothèque de client SAP

* Veillez à installer la dernière version du [connecteur SAP (NCo 3.0) pour Microsoft .NET 3.0.22.0 compilé avec .NET Framework 4.0 – Windows 64 bits (x64)](https://softwaredownloads.sap.com/file/0020000001000932019). Les versions antérieures peuvent entraîner des problèmes de compatibilité. Pour plus d’informations, consultez [les versions de la bibliothèque de client SAP](#sap-library-versions).

* Par défaut, le programme d’installation SAP place les fichiers d’assembly dans le dossier d’installation par défaut. Vous devez copier ces fichiers d’assembly vers un autre emplacement, en fonction de votre scénario, comme suit :

  Pour les applications logiques qui s’exécutent dans un ISE, suivez les étapes décrites dans les [conditions préalables pour l’environnement de service d’intégration](#sap-ise). Pour les applications logiques qui s’exécutent dans une instance Azure mutualisée et utilisent la passerelle de données locale, copiez les fichiers d’assembly du dossier d’installation par défaut vers le dossier d’installation de la passerelle de données. Si vous rencontrez des problèmes avec la passerelle de données, passez en revue les points suivants :

  * Vous devez installer la version 64 bits pour la bibliothèque de client SAP, car la passerelle de données s’exécute uniquement sur des systèmes 64 bits. Dans le cas contraire, vous obtenez une erreur « image incorrecte », car le service hôte de la passerelle de données ne prend pas en charge les assemblys 32 bits.

  * Si votre connexion SAP échoue avec le message d’erreur « Vérifiez vos informations de compte et vos autorisations, puis réessayez », les fichiers d’assembly peuvent se trouver au mauvais emplacement. Veillez à copier les fichiers d’assembly dans le dossier d’installation de la passerelle de données.

    Pour vous aider à résoudre le problèmes, [utilisez la visionneuse du journal de liaison d’assembly .NET](https://docs.microsoft.com/dotnet/framework/tools/fuslogvw-exe-assembly-binding-log-viewer), qui vous permet de vérifier que les fichiers d’assembly se trouvent à l’emplacement approprié. Si vous le souhaitez, vous pouvez sélectionner l’option **Inscription du Global Assembly Cache** lors de l’installation de la bibliothèque de client SAP.

<a name="sap-library-versions"></a>

#### <a name="sap-client-library-versions"></a>Versions de la bibliothèque de client SAP

Les versions antérieures du NCo SAP peuvent subir un interblocage quand plusieurs messages IDoc sont envoyés en même temps. Cette situation bloque tous les messages envoyés ultérieurement à la destination SAP, ce qui engendre une expiration des messages.

Voici les relations entre la bibliothèque de client SAP, le .NET Framework, le runtime .NET et la passerelle :

* L’adaptateur SAP Microsoft et le service hôte de la passerelle utilisent tous deux .NET Framework 4.5.

* Le NCo SAP pour .NET Framework 4.0 fonctionne avec les processus qui utilisent un runtime .NET 4.0 à 4.7.1.

* Le NCo SAP pour .NET Framework 2.0 fonctionne avec les processus qui utilisent un runtime .NET 2.0 à 3.5, mais ne fonctionne plus avec la passerelle la plus récente.

### <a name="secure-network-communications-prerequisites"></a>Conditions préalables pour Secure Network Communications

Si vous utilisez la passerelle de données locale avec la couche logicielle Secure Network Communications (SNC) facultative, qui est prise en charge uniquement dans une instance Azure mutualisée, vous devez également configurer ces paramètres :

* Si vous utilisez SNC avec l’authentification unique (SSO), assurez-vous que la passerelle de données s’exécute en tant qu’utilisateur mappé à l’utilisateur SAP. Pour modifier le compte par défaut, sélectionnez **Changer de compte**, puis entrez les informations d’identification de l’utilisateur.

  ![Modifier le compte de la passerelle de données](./media/logic-apps-using-sap-connector/gateway-account.png)

* Si vous activez SNC avec un produit de sécurité externe, copiez la bibliothèque ou les fichiers SNC sur le même ordinateur que celui sur lequel la passerelle de données est installée. [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos et NTLM sont des exemples de produits SNC.

Pour plus d’informations sur l’activation de SNC pour la passerelle de données, consultez [Activer Secure Network Communications](#secure-network-communications).

<a name="migrate"></a>

## <a name="migrate-to-current-connector"></a>Migrer vers le connecteur actuel

Pour migrer d’un connecteur SAP (non ISE) managé antérieur vers le connecteur SAP managé actuel, procédez comme suit :

1. Si vous ne l’avez pas déjà fait, mettez à jour votre [passerelle de données locale](https://www.microsoft.com/download/details.aspx?id=53127) afin de disposer de la version la plus récente. Pour plus d’informations, consultez [Installer une passerelle de données locale pour Azure Logic Apps](../logic-apps/logic-apps-gateway-install.md).

1. Dans l’application logique qui utilise l’ancien connecteur SAP, supprimez l’action **Envoyer à SAP**.

1. À partir du connecteur SAP le plus récent, ajoutez l’action **Envoyer un message à SAP**. Avant de pouvoir utiliser cette action, vous devez recréer la connexion à votre système SAP.

1. Lorsque vous avez terminé, enregistrez votre application logique.

<a name="add-trigger"></a>

## <a name="send-message-to-sap"></a>Envoyer un message à SAP

Cet exemple utilise une application logique que vous pouvez déclencher à l’aide d’une requête HTTP. L’application logique envoie un IDoc à un serveur SAP et retourne une réponse au demandeur qui a appelé l’application logique.

### <a name="add-an-http-request-trigger"></a>Ajouter un déclencheur de requête HTTP

Dans Azure Logic Apps, chaque application logique doit démarrer avec un [déclencheur](../logic-apps/logic-apps-overview.md#logic-app-concepts), qui s’active lorsqu’un événement spécifique se produit ou lorsqu’une condition particulière est remplie. Chaque fois que le déclencheur s’active, le moteur Logic Apps crée une instance d’application logique et lance l’exécution du flux de travail de votre application.

> [!NOTE]
> Lorsqu’une application logique reçoit des paquets IDoc de SAP, le [déclencheur de requête](https://docs.microsoft.com/azure/connectors/connectors-native-reqres) ne prend pas en charge le schéma XML « brut » généré par la documentation WE60 IDoc de SAP. Toutefois, le schéma XML « brut » est pris en charge pour les scénarios qui envoient des messages d’applications logiques *à* SAP. Vous pouvez utiliser le déclencheur de requête avec le XML IDoc de SAP, mais pas avec IDoc sur RFC. Vous pouvez également transformer le XML au format requis. 

Dans cet exemple, vous allez créer une application logique avec un point de terminaison dans Azure afin de pouvoir envoyer des *requêtes HTTP POST* à votre application logique. Lorsque votre application logique reçoit ces requêtes HTTP, le déclencheur est activé et passe à l’étape suivante de votre flux de travail.

1. Dans le [portail Azure](https://portal.azure.com), créez une application logique vide, ce qui ouvre le Concepteur d’application logique.

1. Dans la zone de recherche, entrez `http request` en guise de filtre. Dans la liste **Déclencheurs**, sélectionnez **Lors de la réception d’une requête HTTP**.

   ![Ajouter un déclencheur de requête HTTP](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Enregistrez maintenant votre application logique pour pouvoir générer une URL de point de terminaison pour votre application logique. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**.

   L’URL du point de terminaison s’affiche désormais dans votre déclencheur, par exemple :

   ![Générer une URL pour le point de terminaison](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-an-sap-action"></a>Ajouter une action SAP

Dans Azure Logic Apps, une [action](../logic-apps/logic-apps-overview.md#logic-app-concepts) est une étape de votre flux de travail qui suit un déclencheur ou une autre action. Si vous n’avez pas encore ajouté de déclencheur à votre application logique et que vous souhaitez suivre cet exemple, [ajoutez le déclencheur décrit dans cette section](#add-trigger).

1. Dans le Concepteur d’application logique, sous le déclencheur, sélectionnez **Nouvelle étape**.

   ![Ajouter une nouvelle étape à l’application logique](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. Dans la zone de recherche, entrez `sap` en guise de filtre. Dans la liste **Actions**, sélectionnez **Envoyer un message à SAP**.
  
   ![Sélectionner l’action « Envoyer un message à SAP »](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   Vous pouvez aussi sélectionner l’onglet **Entreprise** et sélectionner l’action SAP.

   ![Sélectionner l’action « Envoyer un message à SAP » sous l’onglet Entreprise](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. Si votre connexion existe déjà, passez à l’étape suivante afin de configurer votre action SAP. Toutefois, si vous êtes invité à entrer les détails de la connexion, fournissez ces informations pour pouvoir créer une connexion à votre serveur SAP local.

   1. Entrez un nom pour la connexion.

   1. Si vous utilisez la passerelle de données, procédez comme suit :
   
      1. Dans la section **Passerelle de données**, sous **Abonnement**, sélectionnez d’abord l’abonnement Azure pour la ressource de passerelle de données que vous avez créée dans le portail Azure pour l’installation de votre passerelle de données.
   
      1. Sous **Passerelle de connexion**, sélectionnez votre ressource de passerelle de données dans Azure.

   1. Continuez à fournir des informations sur la connexion. Pour la propriété **Type de connexion**, suivez l’étape selon que la propriété est définie sur **Serveur d’applications** ou sur **Groupe** :
   
      * Pour **Serveur d’applications**, les propriétés suivantes, qui apparaissent habituellement comme facultatives, sont obligatoires :

        ![Créer une connexion au serveur d’applications SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Pour **Groupe**, les propriétés suivantes, qui apparaissent habituellement comme facultatives, sont obligatoires :

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

      Pour plus d’informations sur les opérations IDoc, consultez [Schémas de message pour les opérations IDoc](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

   1. Cliquez dans la zone **Message d’entrée** pour afficher la liste du contenu dynamique. Dans cette liste, sous **Lors de la réception d’une demande HTTP**, sélectionnez le champ **Corps**.

      Cette étape inclut le contenu du corps de votre déclencheur de requête HTTP et envoie ce résultat à votre serveur SAP.

      ![Sélectionner la propriété « Body » de déclencheur](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      Une fois que vous avez terminé, votre action SAP ressemble à cet exemple :

      ![Terminer l’action SAP](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Enregistrez votre application logique. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**.

<a name="add-response"></a>

### <a name="add-an-http-response-action"></a>Ajouter une action de réponse HTTP

Ajoutez maintenant une action de réponse au flux de travail de votre application logique et incluez le résultat de l’action SAP. De cette façon, votre application logique renvoie les résultats à partir de votre serveur SAP au demandeur d’origine.

1. Dans le Concepteur d’application logique, sous l’action SAP, sélectionnez **Nouvelle étape**.

1. Dans la zone de recherche, entrez `response` en guise de filtre. Dans la liste **Actions**, sélectionnez **Réponse**.

1. Cliquez dans la zone **Corps** pour afficher la liste du contenu dynamique. Dans cette liste, sous **Envoyer un message à SAP**, sélectionnez le champ **Corps**.

   ![Terminer l’action SAP](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Enregistrez votre application logique.

### <a name="test-your-logic-app"></a>Tester votre application logique

1. Si votre application logique n’est pas déjà activée, dans le menu associé, sélectionnez **Vue d’ensemble**. Dans la barre d’outils, sélectionnez **Activer**.

1. Dans la barre d’outils du concepteur, sélectionnez **Exécuter**. Cette étape démarre manuellement votre application logique.

1. Déclenchez votre application logique en envoyant une requête HTTP POST à l’URL de votre déclencheur de requête HTTP.
Incluez le contenu du message avec votre requête. Pour envoyer la requête, vous pouvez utiliser un outil tel que [Postman](https://www.getpostman.com/apps).

   Pour cet article, la requête envoie un fichier IDoc, qui doit être au format XML et inclure l’espace de noms de l’action SAP que vous utilisez, par exemple :

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. Une fois votre requête HTTP envoyée, attendez la réponse de votre application logique.

   > [!NOTE]
   > Votre application logique peut expirer si toutes les étapes nécessaires pour la réponse ne se terminent pas dans la [limite de délai d’attente des requêtes](./logic-apps-limits-and-config.md). Si cette situation se produit, les requêtes peuvent être bloquées. Pour vous aider à diagnostiquer les problèmes, découvrez comment vous pouvez [vérifier et surveiller vos applications logiques](../logic-apps/monitor-logic-apps.md).

Vous venez de créer une application logique qui peut communiquer avec votre serveur SAP. Maintenant que vous avez configuré une connexion SAP pour votre application logique, vous pouvez explorer d’autres actions SAP disponibles, telles que BAPI et RFC.

<a name="receive-from-sap"></a>

## <a name="receive-message-from-sap"></a>Recevoir le message de SAP

Cet exemple utilise une application logique qui se déclenche quand l’application reçoit un message provenant d’un système SAP.

### <a name="add-an-sap-trigger"></a>Ajouter un déclencheur SAP

1. Dans le portail Azure, créez une application logique vide, qui ouvre le Concepteur d’application logique.

1. Dans la zone de recherche, entrez `sap` en guise de filtre. Dans la liste **Déclencheurs**, sélectionnez **Quand un message est reçu de SAP**.

   ![Ajouter un déclencheur SAP](./media/logic-apps-using-sap-connector/add-sap-trigger-logic-app.png)

   Vous pouvez aussi sélectionner l’onglet **Entreprise**, puis sélectionner le déclencheur :

   ![Ajouter un déclencheur SAP à partir de l’onglet Entreprise](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. Si votre connexion existe déjà, passez à l’étape suivante afin de configurer votre action SAP. Toutefois, si vous êtes invité à entrer les détails de la connexion, fournissez ces informations pour pouvoir créer une connexion à votre serveur SAP local.

   1. Entrez un nom pour la connexion.

   1. Si vous utilisez la passerelle de données, procédez comme suit :

      1. Dans la section **Passerelle de données**, sous **Abonnement**, sélectionnez d’abord l’abonnement Azure pour la ressource de passerelle de données que vous avez créée dans le portail Azure pour l’installation de votre passerelle de données.

      1. Sous **Passerelle de connexion**, sélectionnez votre ressource de passerelle de données dans Azure.

   1. Continuez à fournir des informations sur la connexion. Pour la propriété **Type de connexion**, suivez l’étape selon que la propriété est définie sur **Serveur d’applications** ou sur **Groupe** :

      * Pour **Serveur d’applications**, les propriétés suivantes, qui apparaissent habituellement comme facultatives, sont obligatoires :

        ![Créer une connexion au serveur d’applications SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Pour **Groupe**, les propriétés suivantes, qui apparaissent habituellement comme facultatives, sont obligatoires :

        ![Créer une connexion au serveur de messagerie SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Par défaut, le typage fort est utilisé pour rechercher des valeurs non valides en effectuant une validation XML par rapport au schéma. Ce comportement peut vous aider à détecter les problèmes plus tôt. L’option **Types sécurisés** est disponible pour la compatibilité descendante et ne vérifie que la longueur de chaîne. Apprenez-en davantage sur l’[option Types sécurisés](#safe-typing).

   1. Quand vous avez terminé, sélectionnez **Créer**.

      Logic Apps configure et teste votre connexion pour vérifier son bon fonctionnement.

1. Spécifiez les [paramètres nécessaires](#parameters) en fonction de la configuration de votre système SAP.

   Vous pouvez si vous le souhaitez fournir une ou plusieurs actions SAP. Cette liste d’actions spécifie les messages que le déclencheur reçoit de votre serveur SAP. Une liste vide spécifie que le déclencheur reçoit tous les messages. Si la liste comporte plus d’un message, le déclencheur reçoit seulement les messages spécifiés dans la liste. Tous les autres messages envoyés depuis votre serveur SAP sont rejetés.

   Vous pouvez sélectionner une action SAP dans le sélecteur de fichiers :

   ![Ajouter une action SAP à une application logique](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   Vous pouvez aussi spécifier une action manuellement :

   ![Entrer manuellement une action SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

   Voici un exemple qui montre comment l’action apparaît quand vous configurez le déclencheur pour recevoir plusieurs messages.

   ![Exemple de déclencheur qui reçoit plusieurs messages](media/logic-apps-using-sap-connector/example-trigger.png)

   Pour plus d’informations sur l’action SAP, consultez [Schémas de message pour les opérations IDoc](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

1. Enregistrez maintenant votre application logique pour commencer à recevoir des messages de votre système SAP. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**.

Votre application logique est maintenant prête à recevoir des messages de votre système SAP.

> [!NOTE]
> Le déclencheur SAP n’est pas un déclencheur d’interrogation : il s’agit d’un déclencheur basé sur un webhook. Si vous utilisez la passerelle de données, le déclencheur est appelé depuis la passerelle de données seulement s’il existe un message : aucune interrogation n’est donc nécessaire.

<a name="parameters"></a>

#### <a name="parameters"></a>Paramètres

Avec les entrées de chaîne et de nombre simples, le connecteur SAP accepte les paramètres de table suivants (entrées `Type=ITAB`) :

* Paramètres de direction de table, d’entrée et de sortie, pour les versions SAP antérieures.
* Modification des paramètres, qui remplacent les paramètres de direction de table pour les versions SAP plus récentes.
* Paramètres de table hiérarchique

### <a name="test-your-logic-app"></a>Tester votre application logique

1. Pour déclencher votre application logique, envoyez un message depuis votre système SAP.

1. Dans le menu de l’application logique, sélectionnez **Vue d’ensemble**. Examinez **Historique des exécutions** pour identifier toutes les nouvelles exécutions de votre application logique.

1. Ouvrez la dernière exécution, qui montre le message envoyé depuis votre système SAP dans la section des sorties du déclencheur.

## <a name="receive-idoc-packets-from-sap"></a>Recevoir des paquets d’IDocs de SAP

Vous pouvez configurer SAP pour [envoyer des IDocs sous forme de paquets](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/en-US/4ab38886549a6d8ce10000000a42189c.html), à savoir sous forme de lots ou de groupes d’IDocs. Pour recevoir des paquets d’IDocs, le connecteur SAP, et plus particulièrement le déclencheur, ne nécessite aucune configuration supplémentaire. Toutefois, pour traiter chacun des éléments d’un paquet d’IDocs après la réception du paquet par le déclencheur, des étapes supplémentaires sont nécessaires afin de fractionner le paquet en IDocs individuels.

Voici un exemple montrant comment extraire des IDocs individuels d’un paquet à l’aide de la [fonction `xpath()`](./workflow-definition-language-functions-reference.md#xpath) :

1. Avant de commencer, vous devez disposer d'une application logique avec un déclencheur SAP. Si vous n’avez pas encore cette application logique, suivez les étapes précédentes de cette rubrique pour [configurer une application logique avec un déclencheur SAP](#receive-from-sap).

   Par exemple :

   ![Ajouter un déclencheur SAP à une application logique](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. Récupérez l’espace de noms racine à partir de l’IDoc XML que votre application logique reçoit de SAP. Pour extraire cet espace de noms du document XML, ajoutez une étape qui crée une variable de chaîne locale et stocke cet espace de noms à l'aide d'une expression `xpath()` :

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![Obtenir l’espace de noms racine à partir d’IDoc](./media/logic-apps-using-sap-connector/get-namespace.png)

1. Pour extraire un IDoc individuel, ajoutez une étape qui crée une variable de tableau et stocke la collection d’IDocs à l’aide d’une autre expression `xpath()` :

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![Récupération de tableau d'éléments](./media/logic-apps-using-sap-connector/get-array.png)

   La variable de tableau rend chaque IDoc disponible pour que votre application logique puisse les traiter individuellement en procédant à une énumération sur la collection. Dans cet exemple, l’application logique transfère chaque IDoc vers un serveur SFTP à l’aide d’une boucle :

   ![Envoyer un IDoc au serveur SFTP](./media/logic-apps-using-sap-connector/loop-batch.png)

   Chaque IDoc doit inclure l’espace de noms racine, ce qui explique pourquoi le contenu du fichier est enveloppé dans un élément `<Receive></Receive` avec l’espace de noms racine avant l’envoi de l’IDoc à l’application située en aval ou, dans ce cas, au serveur SFTP.

Vous pouvez utiliser le modèle de démarrage rapide correspondant à ce modèle en sélectionnant celui-ci dans le concepteur d'applications logiques lorsque vous créez une nouvelle application logique.

![Sélectionner un modèle d’application logique de lot](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>Générer des schémas pour les artefacts dans SAP

Cet exemple utilise une application logique que vous pouvez déclencher à l’aide d’une requête HTTP. L’action SAP envoie une demande à un système SAP pour générer les schémas des artefacts IDoc et BAPI spécifiés. Les schémas retournés dans la réponse sont chargés dans un compte d’intégration avec le connecteur Azure Resource Manager.

### <a name="add-an-http-request-trigger"></a>Ajouter un déclencheur de requête HTTP

1. Dans le portail Azure, créez une application logique vide, qui ouvre le Concepteur d’application logique.

1. Dans la zone de recherche, entrez `http request` en guise de filtre. Dans la liste **Déclencheurs**, sélectionnez **Lors de la réception d’une requête HTTP**.

   ![Ajouter un déclencheur de requête HTTP](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Enregistrez maintenant votre application logique pour pouvoir générer une URL de point de terminaison pour votre application logique.
Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**.

   L’URL du point de terminaison s’affiche désormais dans votre déclencheur, par exemple :

   ![Générer une URL pour le point de terminaison](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>Ajouter une action SAP pour générer des schémas

1. Dans le Concepteur d’application logique, sous le déclencheur, sélectionnez **Nouvelle étape**.

   ![Ajouter une nouvelle étape à l’application logique](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. Dans la zone de recherche, entrez `sap` en guise de filtre. Dans la liste **Actions**, sélectionnez **Générer des schémas**.
  
   ![Ajouter l’action « Générer des schémas » à l’application logique](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   Vous pouvez aussi sélectionner l’onglet **Entreprise** et sélectionner l’action SAP.

   ![Sélectionner l’action d’envoi à SAP à partir de l’onglet Entreprise](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. Si votre connexion existe déjà, passez à l’étape suivante afin de configurer votre action SAP. Toutefois, si vous êtes invité à entrer les détails de la connexion, fournissez ces informations pour pouvoir créer une connexion à votre serveur SAP local.

   1. Entrez un nom pour la connexion.

   1. Dans la section **Passerelle de données**, sous **Abonnement**, sélectionnez d’abord l’abonnement Azure pour la ressource de passerelle de données que vous avez créée dans le portail Azure pour l’installation de votre passerelle de données. 
   
   1. Sous **Passerelle de connexion**, sélectionnez votre ressource de passerelle de données dans Azure.

   1. Continuez à fournir des informations sur la connexion. Pour la propriété **Type de connexion**, suivez l’étape selon que la propriété est définie sur **Serveur d’applications** ou sur **Groupe** :
   
      * Pour **Serveur d’applications**, les propriétés suivantes, qui apparaissent habituellement comme facultatives, sont obligatoires :

        ![Créer une connexion au serveur d’applications SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Pour **Groupe**, les propriétés suivantes, qui apparaissent habituellement comme facultatives, sont obligatoires :

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

   Pour plus d’informations sur l’action SAP, consultez [Schémas de message pour les opérations IDoc](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

1. Enregistrez votre application logique. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**.

### <a name="test-your-logic-app"></a>Tester votre application logique

1. Dans la barre d’outils du concepteur, sélectionnez **Exécuter** pour déclencher une exécution de votre application logique.

1. Ouvrez l’exécution et vérifiez les résultats pour l’action **Générer des schémas**.

   Les sorties montrent les schémas générés pour la liste de messages spécifiée.

### <a name="upload-schemas-to-an-integration-account"></a>Charger des schémas sur un compte d’intégration

Vous pouvez aussi télécharger ou stocker les schémas générés dans des référentiels, comme un objet blob, un stockage ou un compte d’intégration. Les comptes d’intégration offrent une expérience privilégiée avec d’autres actions XML : cet exemple montre donc comment charger des schémas dans un compte d’intégration pour la même application logique avec le connecteur Azure Resource Manager.

1. Dans le Concepteur d’application logique, sous le déclencheur, sélectionnez **Nouvelle étape**.

1. Dans la zone de recherche, entrez `Resource Manager` en guise de filtre. Sélectionnez **Créer ou mettre à jour une ressource**.

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

1. Enregistrez votre application logique. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**.

### <a name="test-your-logic-app"></a>Tester votre application logique

1. Dans la barre d’outils du concepteur, sélectionnez **Exécuter** pour déclencher manuellement votre application logique.

1. Après une exécution réussie, accédez au compte d’intégration et vérifiez l’existence des schémas générés.

<a name="secure-network-communications"></a>

## <a name="enable-secure-network-communications"></a>Activer Secure Network Communications

Avant de commencer, assurez-vous de respecter les [conditions préalables](#pre-reqs) précédemment mentionnées, qui s’appliquent uniquement lorsque vous utilisez la passerelle de données et que vos applications logiques s’exécutent dans une instance Azure mutualisée :

* Assurez-vous que la passerelle de données locale est installée sur un ordinateur qui se trouve dans le même réseau que votre système SAP.

* Pour l’authentification unique (SSO), la passerelle de données s’exécute en tant qu’utilisateur mappé à un utilisateur SAP.

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

## <a name="advanced-scenarios"></a>Scénarios avancés

### <a name="confirm-transaction-explicitly"></a>Confirmer une transaction explicitement

Quand vous envoyez des transactions à SAP depuis Logic Apps, cet échange se fait en deux étapes, comme décrit dans le document SAP, [Transactional RFC Server Programs](https://help.sap.com/doc/saphelp_nwpi71/7.1/en-US/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true). Par défaut, l’action **Envoyer à SAP** gère à la fois les étapes de transfert de la fonction et de la confirmation de la transaction dans un même appel. Le connecteur SAP vous donne la possibilité de découpler ces étapes. Vous pouvez envoyer un IDoc et, au lieu de confirmer automatiquement la transaction, vous pouvez utiliser l’action explicite **Confirmer l’ID de transaction**.

Cette possibilité de découpler la confirmation de l’ID de transaction est utile quand vous ne voulez pas dupliquer les transactions dans SAP, par exemple dans les scénarios où des échecs peuvent se produire pour des raisons comme des problèmes réseau. En confirmant l’ID de transaction séparément, la transaction n’est effectuée qu’une seule fois dans votre système SAP.

Voici un exemple montrant ce modèle :

1. Créez une application logique vide et ajoutez un déclencheur HTTP.

1. À partir du connecteur SAP, ajoutez l’action **Envoyer l’IDOC**. Spécifiez les détails de l’IDoc que vous envoyez à votre système SAP.

1. Pour confirmer explicitement l’ID de transaction dans une étape distincte, dans le champ **Confirmer le TID**, sélectionnez **Non**. Pour le champ facultatif **GUID de l’ID de transaction**, vous pouvez spécifier manuellement la valeur, ou faire en sorte que le connecteur génère et retourne automatiquement ce GUID dans la réponse de l’action Envoyer l’IDOC.

   ![Propriétés de l’action Envoyer l’IDOC](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. Pour confirmer explicitement l’ID de transaction, ajoutez l’action **Confirmer l’ID de transaction**. Cliquez dans la zone **ID de transaction** pour faire apparaître la liste des contenus dynamiques. Dans cette liste, sélectionnez la valeur **ID de transaction** qui est retournée depuis l’action **Envoyer l’IDOC**.

   ![Action Confirmer l’ID de transaction](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   Après l’exécution de cette étape, la transaction actuelle est marquée comme terminée aux deux extrémités, sur le côté connecteur SAP et sur le côté système SAP.

## <a name="known-issues-and-limitations"></a>Problèmes connus et limitations

Voici les problèmes et limitations connus pour le connecteur SAP (non-ISE) managé :

* Le déclencheur SAP ne prend pas en charge les clusters de passerelle de données. Dans certains cas de basculement, le nœud de la passerelle de données qui communique avec le système SAP peut différer du nœud actif, ce qui entraîne un comportement inattendu. Pour les scénarios d’envoi, les clusters de passerelles de données sont pris en charge.

* Le connecteur SAP ne prend actuellement pas en charge les chaînes de routeur SAP. La passerelle de données locale doit exister sur le même réseau local que le système SAP que vous voulez connecter.

## <a name="connector-reference"></a>Référence de connecteur

Pour plus d’informations techniques sur ce connecteur, notamment au sujet des déclencheurs, des actions et des limites décrits dans le fichier Swagger du connecteur, consultez la [page de référence du connecteur](https://docs.microsoft.com/connectors/sap/).

> [!NOTE]
> Pour les applications logiques utilisées dans un [environnement de service d’intégration (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), la version de ce connecteur avec l’étiquette ISE applique les [limites de messages de l’ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) à la place.

## <a name="next-steps"></a>Étapes suivantes

* [Connectez-vous à des systèmes locaux](../logic-apps/logic-apps-gateway-connection.md) à partir d’Azure Logic Apps.
* Découvrez comment valider, transformer et utiliser d’autres opérations message avec [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md).
* Découvrez d’autres [connecteurs Logic Apps](../connectors/apis-list.md).
