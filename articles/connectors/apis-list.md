---
title: Connecteurs pour Azure Logic Apps | Microsoft Docs
description: Automatiser les flux de travail avec des connecteurs intégrés, API gérés, locaux, de compte d’intégration et d’entreprise pour Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 06/29/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 2bb3e2ce29037372395aa0b30e9f76f3e712667d
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096609"
---
# <a name="connectors-for-azure-logic-apps"></a>Connecteurs pour Azure Logic Apps

Les connecteurs jouent un rôle important lorsque vous créez des flux de travail automatisés avec Azure Logic Apps. En utilisant des connecteurs dans vos applications logiques, vous développez les fonctionnalités de vos applications locales et cloud pour effectuer des opérations sur les données que vous créez et dont vous disposez déjà. Logic Apps offre plus de 200 connecteurs. Cet article décrit les connecteurs les plus courants et les plus utilisés par des milliers d’applications et des millions d’exécutions pour le traitement des données et des informations.
Les connecteurs sont disponibles en tant que connecteurs intégrés ou gérés. 

* [**Intégrés**](#built-ins) : ces actions et déclencheurs intégrés vous permettent de créer des applications logiques qui s’exécutent selon des planifications personnalisées, communiquent avec d’autres points de terminaison, reçoivent et répondent aux requêtes et appellent des fonctions Azure, Azure API Apps (Web Apps), vos propres API gérées et publiées avec Gestion des API Azure et des applications logiques imbriquées qui peuvent recevoir des requêtes. Vous pouvez également utiliser des actions intégrées pour organiser et contrôler le flux de travail de votre application logique et exploiter vos données.

* **Connecteurs gérés** : ces connecteurs fournissent des déclencheurs et des actions pour accéder à d’autres services et systèmes. Pour certains connecteurs, vous devez d’abord créer les connexions gérées par Azure Logic Apps. Les connecteurs gérés sont organisés dans les groupes suivants :

  |   |   |
  |---|---|
  | [**Connecteurs API gérés**](#managed-api-connectors) | Créent des applications logiques qui utilisent des services tels que Stockage Blob Azure, Office 365, Dynamics, Power BI, OneDrive, Salesforce, SharePoint Online et bien d’autres encore. | 
  | [**Connecteurs locaux**](#on-premises-connectors) | Une fois la [passerelle de données locale][gateway-doc] installée et configurée, ces connecteurs permettent à vos applications logiques d’accéder aux systèmes locaux comme SQL Server, SharePoint Server, Oracle DB, les partages de fichiers et autres. | 
  | [**Connecteurs de compte d’intégration**](#integration-account-connectors) | Disponibles lorsque vous créez et payez un compte d’intégration, ces connecteurs transforment et valident le code XML, encodent et décodent les fichiers plats et traitent les messages entreprise-entreprise (B2B) avec les protocoles AS2, EDIFACT et X12. | 
  | [**Connecteurs d’entreprise**](#enterprise-connectors) | Fournissent un accès aux systèmes d’entreprise tels que SAP et IBM MQ pour un coût supplémentaire. |
  ||| 

  Par exemple, si vous utilisez Microsoft BizTalk Server, vos applications logiques peuvent se connecter et communiquer avec votre instance BizTalk Server en utilisant le [connecteur BizTalk Server](#on-premises-connectors). 
  Vous pouvez ensuite étendre ou exécuter des opérations de type BizTalk dans vos applications logiques à l’aide de [connecteurs de compte d’intégration](#integration-account-connectors). 

Pour des informations techniques sur les déclencheurs et les actions de rechercher chaque connecteur, définis par une description Swagger, ainsi que les limites le cas échéant, consultez les [Détails du connecteur](/connectors/). Pour des informations sur les coûts, consultez [Tarification Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) et [Modèle de tarification de Logic Apps](../logic-apps/logic-apps-pricing.md). 

<a name="built-ins"></a>

## <a name="built-ins"></a>Éléments intégrés

Logic Apps fournit des déclencheurs et des actions intégrés qui vous permettent de créer des flux de travail basés sur une planification, d’aider vos applications logiques à communiquer avec d’autres applications et services, de contrôler le flux de travail via vos applications logiques et de gérer ou manipuler des données. 

|   |   |   |   | 
|---|---|---|---| 
| [![Icône d’API][schedule-icon]<br/>**Planification**][recurrence-doc] | - Exécutez votre application logique selon une planification spécifiée, allant des récurrences basiques à complexes, avec le déclencheur **Recurrence** (Périodicité). <p>- Suspendez votre application logique pour une durée spécifiée avec l’action **Delay** (Différer). <p>- Suspendez votre application logique jusqu’à une date et une heure spécifiées avec l’action **Delay until** (Différer jusqu’à). | [![Icône d’API][http-icon]<br/>**HTTP**][http-doc] | Communiquez avec n’importe quel point de terminaison via HTTP avec des déclencheurs et des actions pour HTTP, HTTP + Swagger et HTTP + Webhook. | 
| [![Icône d’API][http-request-icon]<br/>**Requête**][http-request-doc] | - Appelez votre application logique à partir d’autres applications ou services, déclenchez lors des événements liés aux ressources Event Grid ou déclenchez lors des réponses aux alertes Azure Security Center avec le déclencheur **Requête**. <p>- Envoyez des réponses à une application ou un service avec l’action **Réponse**. | [![Icône d’API][batch-icon]<br/>**Lot**][batch-doc] | - Traitez les messages par lot avec le déclencheur **Batch messages** (Messages par lot). <p>- Appelez les applications logiques comportant déjà des déclencheurs par lot avec l’action **Send messages to batch** (Envoyer les messages au lot). | 
| [![Icône d’API][azure-functions-icon]<br/>**Azure Functions**][azure-functions-doc] | Appelez les fonctions Azure qui exécutent des extraits de code personnalisés (C# ou Node.js) à partir de vos applications logiques. | [![Icône d’API][azure-api-management-icon]</br>**Gestion des API Azure**][azure-api-management-doc] | Appelez les déclencheurs et les actions définis par vos propres API, gérées et publiées avec Gestion des API Azure. | 
| [![Icône d’API][azure-app-services-icon]<br/>**Azure App Services**][azure-app-services-doc] | Appelez Azure API Apps ou Web Apps, hébergées sur Azure App Service. Les déclencheurs et les actions définies par ces applications apparaissent comme les autres déclencheurs et actions de première classe lorsque Swagger est inclus. | [![Icône d’API][azure-logic-apps-icon]<br/>**Azure<br/>Logic Apps**][nested-logic-app-doc] | Appelez d’autres applications logiques qui commencent avec un déclencheur Requête. | 
||||| 

### <a name="control-workflow"></a>Contrôler le flux de travail

Les actions intégrées suivantes permettent de structurer et de contrôler les actions du flux de travail de votre application logique :

|   |   |   |   | 
|---|---|---|---| 
| [![Icône d’élément intégré][condition-icon]<br/>**Condition**][condition-doc] | Évaluez une condition et exécutez différentes actions selon que la condition est true ou false. | [![Icône d’élément intégré][for-each-icon]</br>**Boucle Foreach**][for-each-doc] | Effectuez les mêmes actions sur chaque élément dans un tableau. | 
| [![Icône d’élément intégré][scope-icon]<br/>**Étendue**][scope-doc] | Regroupez les actions en *étendues*, qui obtiennent leur propre état à la fin de l’exécution des actions dans l’étendue. | [![Icône d’élément intégré][switch-icon]</br>**Commutateur**][switch-doc] | Regroupez les actions en *cas*, auxquels sont affectées des valeurs uniques à l’exception du cas par défaut. Exécutez uniquement le cas dont la valeur affectée correspond au résultat d’une expression, d’un objet ou d’un jeton. Si aucune correspondance n’existe, exécutez le cas par défaut. | 
| [![Icône d’élément intégré][terminate-icon]<br/>**Terminate**][terminate-doc] | Arrêtez un flux de travail d’application logique qui fonctionne activement. | [![Icône d’élément intégré][until-icon]<br/>**Until**][until-doc] | Répétez les actions jusqu’à ce que la condition spécifiée ait la valeur true ou qu’un état ait changé. | 
||||| 

### <a name="manage-or-manipulate-data"></a>Gérer ou manipuler des données

Voici les actions intégrées pour travailler avec les sorties de données et leurs formats :  

|   |   | 
|---|---| 
| ![Icône d’élément intégré][data-operations-icon]<br/>**Opérations de données** | Effectuez des opérations avec les données : <p>- **Composer** : créez une sortie unique à partir de plusieurs entrées avec différents types. <br>- **Créer une table CSV** : créez une table CSV (valeurs séparées par des virgules) à partir d’un tableau avec des objets JSON. <br>- **Créer une table HTML** : créez une table HTML à partir d’un tableau avec des objets JSON. <br>- **Filtrer le tableau** : créez un tableau à partir des éléments d’un autre tableau correspondant à vos critères. <br>- **Joindre** : créez une chaîne à partir de tous les éléments d’un tableau et séparez ces éléments avec le séparateur spécifié. <br>- **Analyser JSON** : créez des jetons conviviaux à partir des propriétés et de leurs valeurs dans le contenu JSON afin de pouvoir utiliser ces propriétés dans votre flux de travail. <br>- **Sélectionner** : créez un tableau avec des objets JSON en transformant les éléments ou les valeurs d’un autre tableau et en mappant ces éléments sur les propriétés spécifiées. | 
| ![Icône d’élément intégré][date-time-icon]<br/>**Date Heure** | Effectuez des opérations avec les horodatages : <p>- **Ajouter au timestamp** : ajoutez le nombre spécifié d’unités à un timestamp. <br>- **Convertir le fuseau horaire** : convertissez un timestamp du fuseau horaire source au fuseau horaire cible. <br>- **Timestamp actuel** : renvoyez le timestamp actuel sous forme de chaîne. <br>- **Obtenir le timestamp futur** : renvoyez le timestamp actuel plus les unités de temps spécifiées. <br>- **Obtenir le timestamp passé** : renvoyez le timestamp actuel moins les unités de temps spécifiées. <br>- **Soustraire du timestamp** : soustrayez un nombre d’unités de temps d’un timestamp. |
| [![Icône d’élément intégré][variables-icon]<br/>**Variables**][variables-doc] | Effectuez des opérations avec les variables : <p>- **Ajouter à la variable du tableau** : insérez une valeur en tant que dernier élément dans un tableau stocké par une variable. <br>- **Ajouter à la variable de chaîne** : insérez une valeur en tant que dernier caractère dans une chaîne stockée par une variable. <br>- **Décrémenter une variable** : diminuez une variable d’une valeur constante. <br>- **Incrémenter une variable** : augmentez une variable d’une valeur constante. <br>- **Initialiser une variable** : créez une variable et déclarez son type de données et sa valeur initiale. <br>- **Définir une variable** : attribuez une valeur différente à une variable existante. |
|  |  | 

<a name="managed-api-connectors"></a>

## <a name="managed-api-connectors"></a>Connecteurs API gérés

Les connecteurs suivants sont les plus couramment utilisés pour automatiser les tâches, les processus et les flux de travail avec ces services ou systèmes :

|   |   |   |   | 
|---|---|---|---| 
| [![Icône d’API][azure-service-bus-icon]<br/>**Azure Service Bus**][azure-service-bus-doc] | Gérez les messages asynchrones, les sessions et les abonnements à une rubrique avec le connecteur le plus couramment utilisé dans Logic Apps. | [![Icône d’API][sql-server-icon]<br/>**SQL Server**][sql-server-doc] | Connectez-vous à votre serveur SQL local ou à Azure SQL Database dans le cloud pour gérer les enregistrements, exécuter des procédures stockées ou effectuer des requêtes. | 
| [![Icône d’API][office-365-outlook-icon]<br/>**Office 365<br/>Outlook**][office-365-outlook-doc] | Connectez-vous à votre compte de messagerie Office 365 pour pouvoir créer et gérer des e-mails, des tâches, des événements de calendrier et des réunions, des contacts, des requêtes et bien plus encore. | [![Icône d’API][azure-blob-storage-icon]<br/>**Stockage Blob<br/>Azure**][azure-blob-storage-doc] | Connectez-vous à votre compte de stockage pour pouvoir créer et gérer du contenu d’objet blob. | 
| [![Icône d’API][sftp-icon]<br/>**SFTP**][sftp-doc] | Connectez-vous aux serveurs SFTP auxquels vous avez accès depuis Internet pour pouvoir travailler avec vos fichiers et dossiers. | [![Icône d’API][sharepoint-online-icon]<br/>**SharePoint<br/>Online**][sharepoint-online-doc] | Connectez-vous à SharePoint Online pour pouvoir gérer des fichiers, des pièces jointes, des dossiers et bien plus encore. | 
| [![Icône d’API][dynamics-365-icon]<br/>**Dynamics 365<br/>CRM Online**][dynamics-365-doc] | Connectez-vous à votre compte Dynamics 365 pour pouvoir créer et gérer des enregistrements, des éléments et bien plus encore. | [![Icône d’API][ftp-icon]<br/>**FTP**][ftp-doc] | Connectez-vous aux serveurs FTP auxquels vous avez accès depuis Internet pour pouvoir travailler avec vos fichiers et dossiers. | 
| [![Icône d’API][salesforce-icon]<br/>**Salesforce**][salesforce-doc] | Connectez-vous à votre compte Salesforce pour pouvoir créer et gérer des éléments comme des enregistrements, des travaux, des objets et bien plus encore. | [![Icône d’API][twitter-icon]<br/>**Twitter**][twitter-doc] | Connectez-vous à votre compte Twitter pour pouvoir gérer vos tweets, vos abonnés, votre chronologie et bien plus encore. Enregistrez vos tweets dans SQL, Excel ou SharePoint. | 
| [![Icône d’API][azure-event-hubs-icon]<br/>**Event Hubs**][azure-event-hubs-doc] | Consommez et publiez des événements via un Event Hub. Par exemple, obtenez une sortie à partir de votre application logique à l’aide des Event Hubs, puis envoyez-la à un fournisseur d’analyses en temps réel. | [![Icône d’API][azure-event-grid-icon]<br/>**Azure Event**</br>**Grid**][azure-event-grid-doc] | Surveillez les événements publiés par une grille d’événements, par exemple, lorsque les ressources Azure ou les ressources tierces changent. | 
||||| 

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>Connecteurs locaux 

Les connecteurs suivants sont couramment utilisés pour fournir un accès aux données et aux ressources sur les systèmes locaux. Avant de créer une connexion à un système local, vous devez d’abord [télécharger, installer et configurer une passerelle de données locale][gateway-doc]. Cette passerelle fournit un canal de communication sécurisé sans avoir à configurer l’infrastructure réseau nécessaire. 

|   |   |   |   |   | 
|---|---|---|---|---| 
| ![Icône API][biztalk-server-icon]<br/>**BizTalk**</br> **Serveur** | [![Icône d’API][file-system-icon]<br/>**Système de</br> fichiers**][file-system-doc] | [![Icône d’API][ibm-db2-icon]<br/>**IBM DB2**][ibm-db2-doc] | [![Icône d’API][ibm-informix-icon]<br/>**IBM**</br>**Informix**][ibm-informix-doc] | ![Icône d’API][mysql-icon]<br/>**MySQL** | 
| [![Icône d’API][oracle-db-icon]<br/>**Oracle DB**][oracle-db-doc] | ![Icône d’API][postgre-sql-icon]<br/>**PostgreSQL** | [![Icône d’API][sharepoint-server-icon]<br/>**SharePoint</br> Server**][sharepoint-server-doc] | [![Icône d’API][sql-server-icon]<br/>**SQL</br> Server**][sql-server-doc] | ![Icône d’API][teradata-icon]<br/>**Teradata** | 
||||| 

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>Connecteurs de compte d’intégration 

Les connecteurs suivants permettent de créer des solutions entreprise-entreprise (B2B) avec vos applications logiques lorsque vous créez et payez un [compte d’intégration](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), qui est disponible via Enterprise Integration Pack (EIP) dans Azure. Avec ce compte, vous pouvez créer et stocker des artefacts B2B tels que les partenaires commerciaux, les contrats, les mappages, les schémas, les certificats et ainsi de suite. Pour utiliser ces artefacts, associez vos applications logiques avec votre compte d’intégration. Si vous utilisez déjà BizTalk Server, ces connecteurs vous sont peut-être familiers.

|   |   |   |   | 
|---|---|---|---| 
| [![Icône d’API][as2-icon]<br/>**Décodage</br> AS2**][as2-decode-doc] | [![Icône d’API][as2-icon]<br/>**Codage</br> AS2**][as2-encode-doc] | [![Icône d’API][edifact-icon]<br/>**Décodage</br> EDIFACT**][edifact-decode-doc] | [![Icône d’API][edifact-icon]<br/>**Codage</br> EDIFACT**][edifact-encode-doc] | 
| [![Icône d’API][flat-file-decode-icon]<br/>**Décodage</br> de fichier plat**][flat-file-decode-doc] | [![Icône d’API][flat-file-encode-icon]<br/>**Codage</br> de fichier plat**][flat-file-encode-doc] | [![Icône d’API][integration-account-icon]<br/>**Compte<br/> d’intégration**][integration-account-doc] | [![Icône d’API][liquid-icon]<br/>**Transformations**</br> **Liquid**][json-liquid-transform-doc] | 
| [![Icône d’API][x12-icon]<br/>**Décodage</br> X12**][x12-decode-doc] | [![Icône d’API][x12-icon]<br/>**Codage</br> X12**][x12-encode-doc] | [![Icône d’API][xml-transform-icon]<br/>**Transformations**</br> **XML**][xml-transform-doc] | [![Icône d’API][xml-validate-icon]<br/>**Validation <br/>XML**][xml-validate-doc] |  
||||| 

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>Connecteurs d’entreprise

Vos applications logiques peuvent accéder aux systèmes d’entreprise, tels que SAP et IBM MQ :

|   |   | 
|---|---| 
| [![Icône d’API][ibm-mq-icon]<br/>**IBM MQ**][ibm-mq-doc] | [![Icône d’API][sap-icon]<br/>**SAP**][sap-connector-doc] |
||| 

## <a name="more-about-triggers-and-actions"></a>Informations supplémentaires sur les déclencheurs et les actions

Certains connecteurs fournissent des *déclencheurs* qui préviennent votre application logique lorsque des événements spécifiques se produisent. Ainsi, lorsque ces événements se produisent, le déclencheur crée et exécute une instance de votre application logique. Par exemple, le connecteur FTP comporte un déclencheur « lors de l’ajout ou de la modification d’un fichier » qui démarre votre application logique lorsqu’un fichier est mis à jour. 

Logic Apps fournit les types de déclencheurs suivants :  

* *Déclencheurs d’interrogation* : ces déclencheurs interrogent votre service selon une fréquence spécifiée et vérifient l’existence de nouvelles données. 

  Quand de nouvelles données sont disponibles, une nouvelle instance de votre application logique est créée et s’exécute avec les données transmises en entrée. 

* *Déclencheurs d’émission* : ces déclencheurs écoutent les nouvelles données sur un point de terminaison ou attendent qu’un événement se produise, qui crée et exécute une nouvelle instance de votre application logique.

* *Déclencheur récurrence* : ce déclencheur crée et exécute une instance de votre application logique en fonction d’une planification spécifiée.

Les connecteurs fournissent également des *actions* qui effectuent des tâches dans le flux de travail de votre application. Par exemple, votre application logique peut lire des données et les utiliser par la suite. Plus spécifiquement, votre application logique peut rechercher des données client dans une base de données SQL et traiter ces données ultérieurement dans le flux de travail de votre application logique. 

Pour plus d’informations sur les déclencheurs et les actions, consultez la [Vue d’ensemble des connecteurs](connectors-overview.md). 

## <a name="custom-apis-and-connectors"></a>Connecteurs et API personnalisés 

Pour appeler des API qui exécutent du code personnalisé ou qui ne sont pas disponibles en tant que connecteurs, vous pouvez étendre la plateforme Logic Apps [en créant des applications API personnalisées](../logic-apps/logic-apps-create-api-app.md). Vous pouvez également [créer des connecteurs personnalisés](../logic-apps/custom-connector-overview.md) pour *n’importe quelle* API REST ou SOAP, ce qui rend ces API disponibles pour n’importe quelle application logique dans votre abonnement Azure.
Pour rendre les applications API ou les connecteurs personnalisés publics afin que tout le monde puisse les utiliser dans Azure, vous pouvez [soumettre des connecteurs à la certification Microsoft](../logic-apps/custom-connector-submit-certification.md).

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Pour voter pour des idées ou en soumettre pour Azure Logic Apps et les connecteurs, rendez-vous sur le [site de commentaires des utilisateurs de Logic Apps](http://aka.ms/logicapps-wish).

* Des articles ou des détails vous semblant importants manquent-ils dans les documents ? Si oui, vous pouvez contribuer en ajoutant des informations aux articles existants ou en écrivant le vôtre. Notre documentation est open source et hébergée sur GitHub. Prise en main dans le [référentiel GitHub](https://github.com/Microsoft/azure-docs) de la documentation Azure. 

## <a name="next-steps"></a>Étapes suivantes

* [Créez votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Créer des connecteurs personnalisés pour les applications logiques](https://docs.microsoft.com/connectors/custom-connectors/)
* [Créer des API personnalisées pour les applications logiques](../logic-apps/logic-apps-create-api-app.md)

<!--Misc doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Connexion à des sources de données locales à partir d’applications logiques avec la passerelle de données locale"

<!--Built-ins doc links-->
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Permet d’intégrer des applications logiques à Azure Functions"
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Créer une instance du service Gestion des API Azure pour gérer et publier vos API"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-hosted-api.md "Permet d’intégrer des applications logiques à App Service API Apps"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Envoyer des messages à partir de files d’attente et de rubriques Service Bus, et recevoir des messages de files d’attente et d’abonnements ServiceBus"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Traiter les messages en groupes ou sous forme de lots"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Évaluer une condition et exécuter différentes actions selon que la condition est true ou false"
[delay-doc]: ./connectors-native-delay.md "Effectuer des actions différées"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Effectuer les mêmes actions sur chaque élément dans un tableau"
[http-doc]: ./connectors-native-http.md "Passer des appels HTTP avec le connecteur HTTP"
[http-request-doc]: ./connectors-native-reqres.md "Ajouter des actions pour les demandes et les réponses HTTP à vos applications logiques"
[http-response-doc]: ./connectors-native-reqres.md "Ajouter des actions pour les demandes et les réponses HTTP à vos applications logiques"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Passer des appels HTTP avec le connecteur HTTP + Swagger"
[http-webook-doc]: ./connectors-native-webhook.md "Ajouter des actions Webhook HTTP et déclencher vos applications logiques"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Intégrer des applications logiques à des flux de travail imbriqués"
[query-doc]: ./connectors-native-query.md "Sélectionner et filtrer des tableaux avec l’action de requête"
[recurrence-doc]:  ./connectors-native-recurrence.md "Déclencher des actions récurrentes pour les applications logiques"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Organiser les actions en groupes, qui obtiennent leur propre état à la fin de l’exécution des actions dans le groupe" 
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Organiser les actions en cas, auxquels sont affectées des valeurs uniques. Exécuter uniquement le cas dont la valeur correspond au résultat d’une expression, d’un objet ou d’un jeton. Si aucune correspondance n’existe, exécuter le cas par défaut"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Arrêter ou annuler un flux de travail qu fonctionne activement pour votre application logique"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Répéter les actions jusqu’à ce que la condition spécifiée ait la valeur true ou qu’un état ait changé"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Effectuer des opérations avec des variables, comme initialiser, définir, incrémenter, décrémenter et ajouter à une variable de chaîne ou de tableau"

<!--Managed API doc links-->
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Gérer les fichiers de votre conteneur d’objets blob avec le connecteur Azure Blob Storage"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md " Surveiller les événements publiés par un Event Grid, par exemple, lorsque les ressources Azure ou les ressources tierces changent"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Connectez-vous à Azure Event Hubs. Recevez et envoyez des événements entre les applications logiques et Azure Event Hubs"
[box-doc]: ./connectors-create-api-box.md "Se connecter à Box. Télécharger, obtenir, supprimer, répertorier vos fichiers et bien plus encore"
[dropbox-doc]: ./connectors-create-api-dropbox.md "Se connecter à Dropbox. Télécharger, obtenir, supprimer, répertorier vos fichiers et bien plus encore"
[dynamics-365-doc]: ./connectors-create-api-crmonline.md "Se connecter à Dynamics CRM Online pour exploiter les données CRM Online"
[facebook-doc]: ./connectors-create-api-facebook.md "Se connecter à Facebook. Publier sur une timeline, obtenir un flux de page et bien plus encore."
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Se connecter à un système de fichiers local"
[ftp-doc]: ./connectors-create-api-ftp.md "Se connecter à un serveur FTP/FTPS pour les tâches FTP (notamment télécharger, obtenir et supprimer des fichiers)."
[github-doc]: ./connectors-create-api-github.md "Se connecter à GitHub et suivre les problèmes"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Se connecte à Google Agenda et permet de gérer un agenda."
[google-drive-doc]: ./connectors-create-api-googledrive.md "Se connecter à GoogleDrive pour utiliser vos données"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Se connecter à Google Sheets pour modifier vos feuilles"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Se connecter à Google Tasks pour gérer vos tâches"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Se connecter à IBM DB2 dans le nuage ou sur site. Mettre à jour une ligne, obtenir une table et bien plus encore"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Se connecter à Informix dans le nuage ou sur site. Lire une ligne, répertorier les tables et bien plus encore"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Se connecter à IBM MQ en local ou dans Azure pour envoyer et recevoir des messages"
[instagram-doc]: ./connectors-create-api-instagram.md "Se connecter à Instagram. Déclencher ou agir sur les événements"
[mailchimp-doc]: ./connectors-create-api-mailchimp.md "Se connecter à votre compte MailChimp. Gérer et automatiser les courriers électroniques"
[mandrill-doc]: ./connectors-create-api-mandrill.md "Se connecter à Mandrill pour la communication"
[microsoft-translator-doc]: ./connectors-create-api-microsofttranslator.md "Se connecter à Microsoft Translator. Traduire du texte, détecter les langues et bien plus encore" 
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Se connecter à votre compte Office 365. Envoyer et recevoir du courrier électronique, gérer votre calendrier et vos contacts et bien plus encore"
[office-365-users-doc]: ./connectors-create-api-office365-users.md 
[office-365-video-doc]: ./connectors-create-api-office365-video.md "Obtenir des informations vidéo, des listes et des canaux vidéo, et des URL de lecture pour les vidéos Office 365"
[onedrive-doc]: ./connectors-create-api-onedrive.md "Se connecter à votre compte Microsoft OneDrive personnel. Télécharger, supprimer, répertorier des fichiers et bien plus encore"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "Se connecter à votre compte Microsoft OneDrive professionnel. Télécharger, supprimer, répertorier vos fichiers et bien plus encore"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Connectez-vous à une base de données Oracle pour ajouter, insérer, supprimer des lignes et bien plus encore"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Se connecter à votre boîte aux lettres Outlook. Gérer votre courrier électronique, vos calendriers, vos contacts et bien plus encore"
[project-online-doc]: ./connectors-create-api-projectonline.md "Se connecter à Microsoft Project Online. Gérer vos projets, vos tâches, vos ressources et bien plus encore"
[rss-doc]: ./connectors-create-api-rss.md "Publier et récupérer des éléments de flux, déclencher des opérations lorsqu’un nouvel élément est publié sur un flux RSS."
[salesforce-doc]: ./connectors-create-api-salesforce.md "Se connecter à votre compte Salesforce. Gérer les comptes, les prospects, les opportunités et bien plus encore"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Se connecter à un système SAP local"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Se connecter à SendGrid. Envoyer un courrier électronique et gérer les listes des destinataires"
[sftp-doc]: ./connectors-create-api-sftp.md "Se connecter à votre compte SFTP. Télécharger, obtenir, supprimer des fichiers et bien plus encore"
[sharepoint-server-doc]: ./connectors-create-api-sharepointserver.md "Se connecter au serveur local SharePoint. Gérer des documents, des éléments de liste et bien plus encore"
[sharepoint-online-doc]: ./connectors-create-api-sharepointonline.md "Se connecter à SharePoint Online. Gérer des documents, des éléments de liste et bien plus encore"
[slack-doc]: ./connectors-create-api-slack.md "Se connecter à Slack et publier des messages sur les canaux Slack"
[smtp-doc]: ./connectors-create-api-smtp.md "Se connecter à un serveur SMTP et envoyer du courrier électronique avec des pièces jointes"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Se connecter à SparkPost pour la communication"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Se connecter à Azure SQL Database ou SQL Server. Créer, mettre à jour, obtenir et supprimer des entrées dans une table de base de données SQL."
[trello-doc]: ./connectors-create-api-trello.md "Se connecter à Trello. Gérer vos projets et organiser ce que vous voulez avec qui vous voulez"
[twilio-doc]: ./connectors-create-api-twilio.md "Se connecter à Twilio. Envoyer et obtenir des messages, obtenir des numéros disponibles, gérer des numéros de téléphone entrants et bien plus encore"
[twitter-doc]: ./connectors-create-api-twitter.md "Se connecter à Twitter. Consulter les fils d’actualité, publier des tweets et bien plus encore"
[wunderlist-doc]: ./connectors-create-api-wunderlist.md "Se connecter à Wunderlist. Gérer vos tâches et vos listes de tâches, tenir votre vie à jour et bien plus encore"
[yammer-doc]: ./connectors-create-api-yammer.md "Se connecter à Yammer. Publier des messages, obtenir de nouveaux messages et bien plus encore"
[youtube-doc]: ./connectors-create-api-youtube.md "Se connecter à YouTube. Gérer vos vidéos et vos canaux"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Découvrez l’intégration d’entreprise AS2."
[as2-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-as2-decode.md "Découvrez le décodage AS2 pour intégration d’entreprise"
[as2-encode-doc]:../logic-apps/logic-apps-enterprise-integration-as2-encode.md "Découvrez le codage AS2 pour intégration d’entreprise"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Découvrez le décodage EDIFACT pour intégration d’entreprise"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Découvrez le codage EDIFACT pour intégration d’entreprise"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Découvrez le fichier plat d’intégration d’entreprise."
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Découvrez le fichier plat d’intégration d’entreprise."
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Recherchez des schémas, des cartes, des partenaires et autres dans votre compte d’intégration"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "En savoir plus sur les transformations de JSON avec Liquid"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Découvrez l’intégration d’entreprise X12"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Découvrez le décodage X12 pour intégration d’entreprise"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Découvrez le codage X12 pour intégration d’entreprise"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Découvrez les transformations d’intégration d’entreprise."
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Découvrez la validation XML d’intégration d’entreprise."

<!-- Built-ins icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Managed API icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[basecamp-3-icon]: ./media/apis-list/basecamp.png
[bitbucket-icon]: ./media/apis-list/bitbucket.png
[bitly-icon]: ./media/apis-list/bitly.png
[biztalk-server-icon]: ./media/apis-list/biztalk.png
[blogger-icon]: ./media/apis-list/blogger.png
[box-icon]: ./media/apis-list/box.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dropbox-icon]: ./media/apis-list/dropbox.png
[dynamics-365-icon]: ./media/apis-list/dynamics-crm-online.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[facebook-icon]: ./media/apis-list/facebook.png
[file-system-icon]: ./media/apis-list/file-system.png
[ftp-icon]: ./media/apis-list/ftp.png
[github-icon]: ./media/apis-list/github.png
[google-calendar-icon]: ./media/apis-list/google-calendar.png
[google-drive-icon]: ./media/apis-list/google-drive.png
[google-sheets-icon]: ./media/apis-list/google-sheet.png
[google-tasks-icon]: ./media/apis-list/google-tasks.png
[hipchat-icon]: ./media/apis-list/hipchat.png
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-informix-icon]: ./media/apis-list/ibm-informix.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[insightly-icon]: ./media/apis-list/insightly.png
[instagram-icon]: ./media/apis-list/instagram.png
[instapaper-icon]: ./media/apis-list/instapaper.png
[jira-icon]: ./media/apis-list/jira.png
[mailchimp-icon]: ./media/apis-list/mailchimp.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[microsoft-translator-icon]: ./media/apis-list/microsoft-translator.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[office-365-users-icon]: ./media/apis-list/office-365-users.png
[office-365-video-icon]: ./media/apis-list/office-365-video.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[rss-icon]: ./media/apis-list/rss.png
[salesforce-icon]: ./media/apis-list/salesforce.png
[sap-icon]: ./media/apis-list/sap.png
[send-grid-icon]: ./media/apis-list/sendgrid.png
[sftp-icon]: ./media/apis-list/sftp.png
[sharepoint-online-icon]: ./media/apis-list/sharepoint-online.png
[sharepoint-server-icon]: ./media/apis-list/sharepoint-server.png
[slack-icon]: ./media/apis-list/slack.png
[smartsheet-icon]: ./media/apis-list/smartsheet.png
[smtp-icon]: ./media/apis-list/smtp.png
[sparkpost-icon]: ./media/apis-list/sparkpost.png
[sql-server-icon]: ./media/apis-list/sql.png
[teradata-icon]: ./media/apis-list/teradata.png
[todoist-icon]: ./media/apis-list/todoist.png
[trello-icon]: ./media/apis-list/trello.png
[twilio-icon]: ./media/apis-list/twilio.png
[twitter-icon]: ./media/apis-list/twitter.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[visual-studio-team-services-icon]: ./media/apis-list/visual-studio-team-services.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[wunderlist-icon]: ./media/apis-list/wunderlist.png
[yammer-icon]: ./media/apis-list/yammer.png
[youtube-icon]: ./media/apis-list/youtube.png

<!-- Enterprise Integration Pack icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png