---
title: Vue d’ensemble d’Azure Logic Apps
description: Logic Apps est une plateforme cloud permettant de créer des workflows automatisés qui intègrent applications, données, services et systèmes pour des scénarios d’entreprise avec un minimum de code.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: overview
ms.custom: mvc
ms.date: 04/26/2021
ms.openlocfilehash: 377abf4809e01c3931de393ff97718e0638c5c36
ms.sourcegitcommit: 5f785599310d77a4edcf653d7d3d22466f7e05e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/27/2021
ms.locfileid: "108065495"
---
# <a name="what-is-azure-logic-apps"></a>Qu’est-ce qu’Azure Logic Apps

[Logic Apps](https://azure.microsoft.com/services/logic-apps) est une plateforme cloud qui vous aide à intégrer rapidement et facilement des applications, des données, des systèmes et des services en générant et en exécutant des [workflows](#logic-app-concepts) automatisés. Élément constitutif d’[Azure Integration Services](https://azure.microsoft.com/product-categories/integration/), Logic Apps offre un moyen plus simple de créer, d’héberger et de gérer des solutions d’intégration hautement scalables pour les scénarios d’entreprise et interentreprises (B2B) dans des environnements cloud, locaux et hybrides.

Cette liste décrit simplement quelques exemples de tâches, de processus métier et de charges de travail que vous pouvez automatiser avec le service Logic Apps :

* Router et traiter les commandes client sur l’ensemble des systèmes locaux et des services cloud.
* Planifier et envoyer des notifications par e-mail à l’aide d’Office 365 lorsqu’un événement spécifique se produit.
* Déplacer des fichiers chargés depuis un serveur SFTP ou FTP vers Stockage Azure.
* Superviser des tweets, analyser les sentiments et créer des alertes ou des tâches pour des éléments à passer en revue.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Introducing-Azure-Logic-Apps/player]

Pour accéder en toute sécurité aux opérations et les exécuter en temps réel sur différentes sources de données, faites votre choix dans une [galerie en constante évolution](/connectors/connector-reference/connector-reference-logicapps-connectors) de [connecteurs gérés par Microsoft](#logic-app-concepts), par exemple :

* Des services Azure tels que le Stockage Blob et Service Bus
* Des services Office tels qu’Outlook, Excel et SharePoint
* Des serveurs de base de données tels que SQL et Oracle
* Des systèmes d’entreprise tels que SAP et IBM MQ
* Des partages de fichiers tels que FTP et SFTP

Pour communiquer avec un point de terminaison de service quelconque, exécuter votre propre code, organiser votre workflow ou manipuler des données, vous pouvez utiliser des [déclencheurs et des actions intégrés](#logic-app-concepts) qui s’exécutent en mode natif dans le service Logic Apps. Par exemple, les déclencheurs intégrés incluent Request, HTTP et Recurrence. Les actions intégrées incluent Condition, For each, Exécuter du code JavaScript et des opérations qui appellent des fonctions Azure, des applications web ou des applications API hébergées dans Azure, ainsi que d’autres workflows Logic Apps.

Pour les scénarios d’intégration B2B, Logic Apps intègre les fonctionnalités de [BizTalk Server](/biztalk/core/introducing-biztalk-server). Vous pouvez créer un [compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md) dans lequel vous définissez des partenaires commerciaux, des contrats, des schémas, des mappages et d’autres artefacts B2B. Lorsque vous liez ce compte à une application logique, vous pouvez créer des workflows qui fonctionnent avec ces artefacts et échangent des messages à l’aide de protocoles tels qu’AS2, EDIFACT et X12.

Pour plus d’informations sur la façon dont les workflows peuvent accéder aux applications, aux données, aux services et aux systèmes et les utiliser, consultez la documentation suivante :

* [Connecteurs pour Azure Logic Apps](../connectors/apis-list.md)
* [Connecteurs managés pour Azure Logic Apps](../connectors/built-in.md)
* [Déclencheurs et actions intégrés pour Azure Logic Apps](../connectors/managed.md)
* [Solutions d’intégration d’entreprise B2B avec Azure Logic Apps](logic-apps-enterprise-integration-overview.md)

<a name="logic-app-concepts"></a>

## <a name="key-terms"></a>Termes clés

* **Workflow** : série d’étapes définissant une tâche ou un processus, commençant par un déclencheur individuel, suivi d’une ou de plusieurs actions.

* **Déclencheur** : première étape au début de chaque workflow, qui spécifie la condition à respecter avant d’exécuter les actions figurant dans le workflow. Par exemple, un événement déclencheur peut être de recevoir un e-mail dans votre boîte de réception ou de détecter un nouveau fichier dans un compte de stockage.

* **Action** : chaque étape qui suit le déclencheur et exécute une opération dans un workflow.

* **Connecteur managé** : connecteur géré par Microsoft qui fournit l’accès à une application, des données, un service ou un système spécifiques. Avant de pouvoir les utiliser, la plupart des connecteurs managés vous demandent d’abord de créer une connexion à partir de votre workflow et d’authentifier votre identité.

  Par exemple, vous pouvez démarrer un workflow à l’aide d’un déclencheur ou inclure une action qui fonctionne avec le Stockage Blob Azure, Office 365, Salesforce ou les serveurs SFTP. Pour plus d’informations, passez en revue [Connecteurs managés pour Azure Logic Apps](../connectors/managed.md).

* **Déclencheur ou action intégré** : opération Logic apps exécutée en mode natif qui fournit un moyen de contrôler la planification ou la structure de votre workflow, d’exécuter votre propre code, de gérer ou de manipuler des données ou d’effectuer d’autres tâches dans votre workflow. La plupart des opérations intégrées ne sont associées à aucun service ni système. Beaucoup ne vous obligent pas non plus à créer d’abord une connexion à partir de votre workflow ni à authentifier votre identité. Des opérations intégrées sont également disponibles pour quelques services, systèmes et protocoles, tels qu’Azure Functions, la gestion des API Azure, Azure App Services, etc.

  Par exemple, vous pouvez démarrer presque n’importe quel workflow selon une planification en utilisant le déclencheur Recurrence. Vous pouvez aussi faire en sorte que votre workflow attende jusqu’à ce qu’il soit appelé quand vous utilisez le déclencheur Request. Pour plus d’informations, consultez [Déclencheurs et actions intégrés pour Azure Logic Apps](../connectors/built-in.md).

<a name="how-do-logic-apps-work"></a>

## <a name="how-logic-apps-work"></a>Comment fonctionnent les applications logiques

Dans une application logique, chaque workflow démarre avec un [déclencheur](#logic-app-concepts) individuel. Un déclencheur se déclenche quand une condition est satisfaite, par exemple quand un événement spécifique se produit ou quand les données répondent à des critères spécifiques. De nombreux déclencheurs incluent des [fonctionnalités de planification](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md) qui contrôlent la fréquence d’exécution de votre workflow. Après le déclencheur, une ou plusieurs [actions](#logic-app-concepts) exécutent des opérations. Par exemple, elles traitent, gèrent ou convertissent des données qui transitent par le workflow ou qui font passer le workflow à l’étape suivante.

Par exemple, le workflow suivant commence avec un déclencheur Dynamics qui a une condition intégrée nommée **Lorsqu’un enregistrement est mis à jour**. Les actions incluent la transformation de données XML, l’appel d’une application web de mise à jour des données, l’évaluation d’une condition qui contrôle les actions à entreprendre et l’envoi d’un e-mail de notification avec les résultats. Lorsque le déclencheur détecte un événement qui répond à la condition, le déclencheur se déclenche et les actions figurant dans le workflow commencent à s’exécuter. Chaque fois que le déclencheur se déclenche, le service Logic Apps crée une instance de workflow qui exécute ces actions.

![Concepteur d’applications logiques – Exemple de workflow](./media/logic-apps-overview/azure-logic-apps-designer.png)

Vous pouvez créer visuellement des workflows à l’aide du concepteur d’applications logiques dans le portail Azure, dans Visual Studio ou dans Visual Studio Code. Chaque workflow a également une définition sous-jacente qui est décrite à l’aide de JSON (JavaScript Object Notation). Si vous préférez, vous pouvez créer et personnaliser des workflows en utilisant l’éditeur en mode code dans le portail Azure, dans Visual Studio ou dans Visual Studio Code. Pour certaines tâches de création et de gestion, Logic Apps fournit une prise en charge des commandes Azure PowerShell et Azure CLI. Pour un déploiement automatisé, Logic Apps prend en charge les modèles Azure Resource Manager.

## <a name="why-use-logic-apps"></a>Pourquoi utiliser des applications logiques

Avec des entreprises de plus en plus portées sur la numérisation, la plateforme Logic Apps vous aide à connecter facilement et rapidement des systèmes hérités, modernes et innovants, en fournissant des API prédéfinies en tant que connecteurs gérés par Microsoft. De cette façon, vous pouvez vous concentrer sur la logique métier et les fonctionnalités de vos applications. Vous n’avez pas à vous soucier de la création, de l’hébergement, de la mise à l’échelle, de la gestion, de la maintenance et de la surveillance de vos applications. Logic Apps gère ces problèmes pour vous. De plus, vous payez uniquement ce que vous utilisez, avec un [modèle de tarification](../logic-apps/logic-apps-pricing.md) basé sur la consommation.

En règle générale, vous n’aurez pas à écrire de code. Toutefois, si vous devez en écrire, vous pouvez créer des extraits de code avec [Azure Functions](../azure-functions/functions-overview.md) et exécuter ce code à la demande à partir de workflows. En outre, si vos workflows ont besoin d’interagir avec des événements issus de services Azure, d’applications personnalisées ou d’autres solutions, vous pouvez utiliser [Azure Event Grid](../event-grid/overview.md) avec vos workflows pour la surveillance, le routage et la publication d’événements.

Logic Apps, Functions et Event Grid sont entièrement gérés par Microsoft Azure, vous évitant ainsi d’avoir à vous soucier de la compilation, de l’hébergement, de la mise à l’échelle, de la gestion, de la surveillance et du maintien de vos solutions. Avec la possibilité de créer des [applications et des solutions « sans serveur »](../logic-apps/logic-apps-serverless-overview.md), vous pouvez vous concentrer uniquement sur la logique métier. Ces services sont automatiquement redimensionnés pour répondre à vos besoins, rendre les intégrations plus rapides et vous aider à créer des applications cloud fiables avec un minimum de code.

Pour savoir comment les sociétés ont amélioré leur flexibilité et la concentration sur leurs activités principales grâce à la combinaison de Logic Apps avec d’autres services Azure et des produits Microsoft, consultez ces [témoignages](https://aka.ms/logic-apps-customer-stories).

Voici de plus amples détails sur les fonctionnalités et les avantages de Logic Apps :

### <a name="visually-build-workflows-with-easy-to-use-tools"></a>Concevoir visuellement des flux de travail avec des outils simples à utiliser

Gagnez du temps et simplifiez les processus complexes avec des outils de conception visuelle. Créez des workflows complets à l’aide du Concepteur d’applications logiques via votre navigateur dans le portail Azure ou dans Visual Studio. Démarrez votre flux de travail à l’aide d’un déclencheur et ajoutez des actions de la [galerie des connecteurs](../connectors/apis-list.md).

### <a name="get-started-faster-with-logic-apps-templates"></a>Démarrer plus rapidement à l’aide des modèles Logic Apps

Créez rapidement des solutions courantes plus rapidement lorsque vous choisissez des flux de travail prédéfinis dans la [galerie de modèles](../logic-apps/logic-apps-create-logic-apps-from-templates.md). Les modèles vont de la connectivité simple pour les applications software-as-a-service (SaaS) aux solutions avancées B2B avec des modèles « pour vous amuser ». Apprenez à [créer des applications logiques à partir de modèles prédéfinis](../logic-apps/logic-apps-create-logic-apps-from-templates.md).

### <a name="connect-disparate-systems-across-different-environments"></a>Connecter des systèmes disparates au sein d’environnements différents

Certains modèles et processus sont faciles à décrire, mais difficiles à implémenter dans le code. Les workflows Logic Apps vous aident à connecter en toute transparence différents systèmes entre des environnements cloud et locaux. Par exemple, vous pouvez connecter une solution marketing cloud à un système de facturation local, ou centraliser la messagerie entre les API et les systèmes avec un Service Bus d’entreprise. La plateforme Logic Apps offre un moyen rapide, fiable et cohérent de fournir des solutions réutilisables et reconfigurables pour ces scénarios.

### <a name="first-class-support-for-enterprise-integration-and-b2b-scenarios"></a>Prise en charge de premier ordre de l’intégration en entreprise et des scénarios B2B

Les entreprises et les organisations communiquent électroniquement entre elles à l’aide des protocoles et de formats de message standard mais différents, tels que X12, AS2 et EDIFACT. Avec les fonctionnalités du pack [EIP (Enterprise Integration Pack)](../logic-apps/logic-apps-enterprise-integration-overview.md), vous pouvez créer des workflows qui convertissent les formats de message utilisés par vos partenaires en formats interprétables et utilisables par les systèmes de votre organisation. Logic Apps gère ces échanges en douceur et en sécurité avec le chiffrement et les signatures numériques.

Commencez par vos systèmes et services actuels et augmentez de façon incrémentielle à votre propre rythme. Lorsque vous êtes prêt, Logic Apps et le EIP vous aident à mettre en œuvre et à monter en puissance vers des scénarios d’intégration plus matures, grâce à ces fonctionnalités et bien plus encore :

* Profiter de ces produits et services :

  * [Microsoft BizTalk Server](/biztalk/core/introducing-biztalk-server)
  * [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)
  * [Azure Functions](../azure-functions/functions-overview.md)
  * [Gestion des API Azure](../api-management/api-management-key-concepts.md)

* Traiter les [messages XML](../logic-apps/logic-apps-enterprise-integration-xml.md)

* Gérer les [fichiers plats](../logic-apps/logic-apps-enterprise-integration-flatfile.md)

* Échanger des messages avec les protocoles [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md), [AS2](../logic-apps/logic-apps-enterprise-integration-as2.md), et [X12](../logic-apps/logic-apps-enterprise-integration-x12.md)

* Stockez et gérez ces artefacts B2B et plus à un seul emplacement avec les [comptes d’intégration](./logic-apps-enterprise-integration-create-integration-account.md) :

  * [Partenaires](../logic-apps/logic-apps-enterprise-integration-partners.md)
  * [Contrats](../logic-apps/logic-apps-enterprise-integration-agreements.md) 
  * [Mappages de transformation XML](../logic-apps/logic-apps-enterprise-integration-maps.md)
  * [Schémas de validation XML](../logic-apps/logic-apps-enterprise-integration-schemas.md)

Par exemple, si vous utilisez Microsoft BizTalk Server, les applications logiques peuvent communiquer avec votre instance BizTalk Server en utilisant le [connecteur BizTalk Server](../connectors/managed.md#on-premises-connectors). Vous pouvez alors étendre ou exécuter des opérations BizTalk dans vos workflows en incluant des [connecteurs de compte d’intégration](../connectors/managed.md#integration-account-connectors), qui sont disponibles avec le pack EIP (Enterprise Integration Pack).

Dans l’autre sens, BizTalk Server peut se connecter et communiquer avec les applications logiques en utilisant [Microsoft BizTalk Server Adapter pour Logic Apps](https://www.microsoft.com/download/details.aspx?id=54287). Découvrez comment [configurer et utiliser BizTalk Server Adapter](/biztalk/core/logic-app-adapter) dans votre instance BizTalk Server.

### <a name="write-once-reuse-often"></a>Écrire une seule fois pour réutiliser souvent

Créez vos applications logiques en tant que modèles Azure Resource Manager pour pouvoir [automatiser le déploiement d’applications logiques](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) dans plusieurs environnements et régions.

### <a name="access-resources-inside-azure-virtual-networks"></a>Accéder aux ressources situées dans les réseaux virtuels Azure

Logic Apps peut accéder à des ressources sécurisées, par exemple des machines virtuelles et d’autres systèmes ou services, dans un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md) quand vous créez un [*environnement ISE* (environnement de service d’intégration)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Un environnement ISE est une instance dédiée du service Logic Apps qui utilise des ressources dédiées et s’exécute séparément du service Logic Apps multilocataire « mondial ».

L’exécution d’applications logiques dans votre propre instance dédiée distincte aide à réduire l’impact que d’autres locataires Azure peuvent avoir sur le niveau de performance de vos applications. Cet impact est également appelé [effet « voisins bruyants »](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors). Un ISE offre également les avantages suivants :

* Vous avez vos propres adresses IP statiques, qui sont distinctes des adresses IP statiques partagées par les applications logiques dans le service multilocataire. Vous pouvez également configurer une adresse IP sortante publique, statique et prévisible pour communiquer avec les systèmes de destination. De cette façon, vous n’avez pas besoin de configurer d’ouvertures de pare-feu supplémentaires sur ces systèmes de destination pour chaque environnement ISE.

* Des limites accrues quant à la durée d’exécution, la conservation du stockage, le débit, le délai d’attente des requêtes et réponses HTTP, la taille des messages et les requêtes de connecteur personnalisé. Pour plus d’informations, consultez [Limites et configuration pour Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md).

Quand vous créez un ISE, Azure l’*injecte* ou le déploie sur votre réseau virtuel Azure. Vous pouvez ensuite utiliser cet ISE comme emplacement pour les applications logiques et les comptes d’intégration qui ont besoin d’un accès. Pour plus d’informations sur la création d’un environnement ISE, consultez [Se connecter à des réseaux virtuels Azure à partir d’Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

### <a name="built-in-extensibility"></a>Extensibilité intégrée

Si vous ne trouvez pas le connecteur souhaité pour exécuter du code personnalisé, vous pouvez étendre les applications logiques en créant et en appelant vos propres extraits de code à la demande via [Azure Functions](../azure-functions/functions-overview.md). Créez vos propres [API](../logic-apps/logic-apps-create-api-app.md) et [connecteurs personnalisés](../logic-apps/custom-connector-overview.md), que vous pouvez appeler à partir d’applications logiques.

### <a name="pay-only-for-what-you-use"></a>Payez uniquement pour ce que vous utilisez
  
Logic Apps utilise un [modèle tarifaire](../logic-apps/logic-apps-pricing.md) basé sur la consommation, sauf si vous avez déjà des applications logiques créées avec des plans App Service.

En savoir plus sur Logic Apps avec ces vidéos d’introduction :

* [Intégration avec Logic Apps - passer de zéro à héros](https://channel9.msdn.com/Events/Build/2017/C9R17)
* [Intégration d’entreprise avec Microsoft Azure Logic Apps](https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK2188)
* [Concevoir des processus d’entreprise avancés avec Logic Apps](https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3179)

## <a name="how-does-logic-apps-differ-from-functions-webjobs-and-power-automate"></a>En quoi Logic Apps diffère de Functions, WebJobs et Power Automate ?

Tous ces services vous aident à « coller » et connecter des systèmes hétérogènes. Chaque service ayant des avantages, combiner leurs fonctionnalités est la meilleure façon de créer rapidement un système d’intégration évolutif et complet. Pour plus d’informations, consultez [Choisir entre Logic Apps, Functions, WebJobs et Power Automate](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="get-started"></a>Bien démarrer

Logic Apps est l’un des nombreux services hébergés sur Microsoft Azure. Avant de commencer, vous avez besoin d’un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/).

Si vous avez un abonnement Azure, essayez ce [guide de démarrage rapide pour créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md), qui surveille le nouveau contenu sur un site web via un flux RSS et envoie un e-mail lorsqu’un nouveau contenu apparaît.

## <a name="next-steps"></a>Étapes suivantes

* [Contrôler le trafic à l’aide d’une application logique planifiée](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
* En savoir plus sur [les solutions serverless avec Azure](../logic-apps/logic-apps-serverless-overview.md)
* En savoir plus sur [l’intégration B2B avec Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
