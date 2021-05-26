---
title: Déploiement DevOps pour Azure Logic Apps monolocataire
description: Découvrez le déploiement DevOps pour Azure Logic Apps monolocataire.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 05/25/2021
ms.openlocfilehash: bae25b29fb0244641f3f1db80d8f2679d2070777
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110369105"
---
# <a name="devops-deployment-for-single-tenant-azure-logic-apps"></a>Déploiement DevOps pour Azure Logic Apps monolocataire

Les applications ayant tendance à être de plus en plus des applications cloud distribuées et natives, les organisations gèrent davantage de composants distribués entre davantage d’environnements. Pour maintenir le contrôle et la cohérence, vous pouvez automatiser vos environnements et déployer davantage de composants plus rapidement et avec une confiance accrue au moyen des outils et processus DevOps.

Cet article fournit une introduction et une vue d’ensemble de l’expérience actuelle d’intégration continue et de déploiement continu (CI/CD) pour Azure Logic Apps monolocataire.

<a name="single-tenant-versus-multi-tenant"></a>

## <a name="single-tenant-versus-multi-tenant"></a>Comparaison entre l’architecture monolocataire et l’architecture multilocataire

Dans Azure Logic Apps *multilocataire*, le déploiement des ressources repose sur des modèles Azure Resource Manager (modèles ARM), qui combinent et gèrent le provisionnement des ressources à la fois pour les applications logiques et l’infrastructure. Dans Azure Logic Apps *monolocataire*, le déploiement devient plus facile, car vous pouvez séparer le provisionnement des ressources entre les applications et l’infrastructure.

Quand vous créez des applications logiques avec le type de ressource **Application logique (Standard)** , vos workflows reposent sur le runtime Azure Logic Apps monolocataire remanié. Ce runtime utilise le [modèle d’extensibilité d’Azure Functions](../azure-functions/functions-bindings-register.md) et est [hébergé en tant qu’extension sur le runtime d’Azure Functions](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564). Cette conception offre une portabilité, une flexibilité et des performances accrues pour vos applications logiques ainsi que d’autres fonctionnalités et avantages hérités de la plateforme Azure Functions et de l’écosystème Azure App Service.

Par exemple, vous pouvez empaqueter le runtime conteneurisé remanié et les workflows dans le cadre de votre application logique. Vous pouvez utiliser des étapes ou des tâches génériques qui génèrent, assemblent et compressent vos ressources d’application logique dans des artefacts prêts à être déployés. Pour déployer vos applications, copiez les artefacts dans l’environnement hôte, puis démarrez vos applications pour exécuter vos workflows. Ou bien, intégrez vos artefacts à des pipelines de déploiement en utilisant les outils et processus que vous connaissez et utilisez déjà. Par exemple, si votre scénario nécessite des conteneurs, vous pouvez conteneuriser vos applications logiques et les intégrer à vos pipelines existants.

Pour configurer et déployer vos ressources d’infrastructure, telles que les réseaux virtuels et la connectivité, vous pouvez continuer à utiliser des modèles ARM et provisionner séparément ces ressources avec d’autres processus et pipelines que vous utilisez à ces fins. 

Avec les options de génération et de déploiement standard, vous pouvez vous concentrer sur le développement d’applications séparément du déploiement d’infrastructure. Ainsi, vous obtenez un modèle de projet plus générique dans lequel vous pouvez appliquer de nombreuses options de déploiement similaires ou identiques à celles que vous utilisez pour une application générique. Vous bénéficierez également d’une expérience plus cohérente pour créer des pipelines de déploiement autour de vos projets d’application et pour exécuter les tests et validations requis avant la publication en production. Quelle que soit la pile de technologies que vous utilisez, vous pouvez déployer des applications logiques avec les outils de votre choix.

<a name="devops-deployment-features"></a>

## <a name="devops-deployment-capabilities"></a>Fonctionnalités de déploiement DevOps

Azure Logic Apps monolocataire hérite de nombreuses fonctionnalités et avantages hérités de la plateforme Azure Functions et de l’écosystème Azure App Service. Ces mises à jour incluent un tout nouveau modèle de déploiement et d’autres façons d’utiliser DevOps pour vos workflows d’application logique.

<a name="local-development-testing"></a>

### <a name="local-development-and-testing"></a>Développement et test locaux

Quand vous utilisez Visual Studio Code avec l’extension Azure Logic Apps (Standard), vous pouvez développer, générer et exécuter localement les workflows d’application logique monolocataire dans votre environnement de développement sans avoir à effectuer de déploiement sur Azure. Vous pouvez également exécuter vos workflows partout où Azure Functions peut s’exécuter. Par exemple, si votre scénario nécessite des conteneurs, vous pouvez conteneuriser vos applications logiques et les déployer en tant que conteneurs.

Cette fonctionnalité est une amélioration majeure et procure un avantage substantiel par rapport au modèle multilocataire, ce qui vous oblige à développer sur une ressource en cours d’exécution existante dans Azure.

<a name="separate-concerns"></a>

### <a name="separate-concerns"></a>Séparer les responsabilités

Le modèle monolocataire vous donne la possibilité de séparer les responsabilités entre l’application et l’infrastructure sous-jacente. Par exemple, vous pouvez développer, générer, compresser et déployer votre application séparément en tant qu’artefact immuable sur différents environnements. Les workflows d’application logique ont généralement un « code d’application » que vous mettez à jour plus souvent que l’infrastructure sous-jacente. En séparant ces couches, vous pouvez vous concentrer davantage sur la création du workflow de votre application logique et consacrer moins de temps au déploiement des ressources nécessaires dans plusieurs environnements.

![Diagramme conceptuel montrant des pipelines de déploiement distincts pour les applications et l’infrastructure.](./media/devops-deployment-single-tenant/deployment-pipelines-logic-apps.png)

<a name="resource-structure"></a>

### <a name="logic-app-resource-structure"></a>Structure des ressources d’application logique

[!INCLUDE [Visual Studio Code - logic app resource structure](../../includes/logic-apps-single-tenant-resource-structure.md)]

### <a name="logic-app-project-structure"></a>Structure du projet d’application logique

[!INCLUDE [Visual Studio Code - logic app project structure](../../includes/logic-apps-single-tenant-project-structure-visual-studio-code.md)]

<a name="deployment-containers"></a>

### <a name="container-deployment"></a>Déploiement de conteneur

Azure Logic Apps monolocataire prend en charge le déploiement sur des conteneurs, ce qui signifie que vous pouvez conteneuriser vos workflows d’application logique et les exécuter partout où les conteneurs peuvent s’exécuter. Une fois que vous avez conteneurisé votre application, le déploiement fonctionne essentiellement comme tout autre conteneur déployé et géré par vos soins.

Pour obtenir des exemples qui incluent Azure DevOps, consultez [CI/CD pour les conteneurs](https://azure.microsoft.com/solutions/architecture/cicd-for-containers/).

<a name="app-settings-parameters"></a>

### <a name="app-settings-and-parameters"></a>Paramètres d’application et paramètres

Dans Azure Logic Apps multilocataire, les modèles ARM posent un défi quand vous devez gérer des variables d’environnement pour des applications logiques dans différents environnements de développement, de test et de production. Tout ce qui se trouve dans un modèle ARM est défini lors du déploiement. Si vous ne devez changer ne serait-ce qu’une seule variable, vous devez tout redéployer.

Dans Azure Logic Apps monolocataire, vous pouvez appeler et référencer vos variables d’environnement au moment de l’exécution en utilisant les paramètres d’application et les paramètres, ce qui évite les redéploiements aussi fréquents.

<a name="managed-connectors-built-in-operations"></a>

## <a name="managed-connectors-and-built-in-operations"></a>Connecteurs managés et opérations intégrées

L’écosystème Azure Logic Apps fournit des [centaines de connecteurs managés par Microsoft](/connectors/connector-reference/connector-reference-logicapps-connectors) et des opérations intégrées dans le cadre d’un regroupement en constante évolution que vous pouvez utiliser dans Azure Logic Apps monlocataire. La façon dont Microsoft gère ces connecteurs et les opérations intégrées demeure presque la même dans Azure Logic Apps monolocataire.

L’amélioration la plus importante est que le service monolocataire rend davantage de connecteurs managés populaires également disponibles en tant qu’opérations intégrées. Par exemple, vous pouvez utiliser des opérations intégrées pour Azure Service Bus, Azure Event Hubs, SQL et autres. Dans le même temps, les versions du connecteur managé sont toujours disponibles et continuent de fonctionner.

Les connexions que vous créez en utilisant des opérations intégrées sont appelées connexions intégrées ou *connexions de fournisseur de services*. Les opérations intégrées et leurs connexions s’exécutent localement dans le même processus que celui qui exécute vos workflows. Les unes comme les autres sont hébergées sur le runtime Logic Apps remanié. En revanche, les connexions managées, ou les connexions d’API, sont créées et exécutées séparément en tant que ressources Azure, que vous déployez en utilisant des modèles ARM. Ainsi, les opérations intégrées et leurs connexions offrent de meilleures performances en raison de leur proximité avec vos workflows. Cette conception fonctionne également bien avec les pipelines de déploiement, car les connexions du fournisseur de services sont empaquetées dans le même artefact de build.

Dans Visual Studio Code, quand vous utilisez le concepteur pour développer ou apporter des modifications à vos workflows, le moteur de Logic Apps génère automatiquement les métadonnées de connexion nécessaires dans le fichier **connections.json** de votre projet. Les sections suivantes décrivent les trois types de connexions que vous pouvez créer dans vos workflows. Chaque type de connexion a une structure JSON différente, ce qui est important à comprendre, car les points de terminaison changent quand vous passez d’un environnement à un autre.

<a name="service-provider-connections"></a>

### <a name="service-provider-connections"></a>Connexion de fournisseur de services

Quand vous utilisez une opération intégrée pour un service tel qu’Azure Service Bus ou Azure Event Hubs dans Azure Logic Apps monolocataire, vous créez une connexion de fournisseur de services qui s’exécute dans le même processus que votre workflow. Cette infrastructure de connexion est hébergée et gérée dans le cadre de votre application logique, et les paramètres d’application stockent les chaînes de connexion pour toutes les opérations intégrées basées sur le fournisseur de services utilisées par vos workflows.

Dans votre projet d’application logique, chaque workflow a un fichier workflow.json qui contient la définition JSON sous-jacente du workflow. Cette définition de workflow référence ensuite les chaînes de connexion nécessaires dans le fichier connections.json de votre projet.

L’exemple suivant montre comment la connexion du fournisseur de services pour une opération Service Bus intégrée apparaît dans le fichier connections.json de votre projet :

```json
"serviceProviderConnections": {
   "{service-bus-connection-name}": {
      "parameterValues": {
         "connectionString": "@appsetting('servicebus_connectionString')"
      },
      "serviceProvider": {
         "id": "/serviceProviders/serviceBus"
      },
      "displayName": "{service-bus-connection-name}"
   },
   ...
}
```

<a name="managed-connections"></a>

### <a name="managed-connections"></a>Connexions managées

Quand vous utilisez un connecteur managé pour la première fois dans votre workflow, vous êtes invité à créer une connexion d’API managée pour le service ou le système cible et à authentifier votre identité. Ces connecteurs sont managés par l’écosystème de connecteurs partagés dans Azure. Les connexions d’API existent et s’exécutent en tant que ressources distinctes dans Azure.

Dans Visual Studio Code, pendant que vous continuez à créer et à développer votre workflow avec le concepteur, le moteur de Logic Apps crée automatiquement les ressources nécessaires dans Azure pour les connecteurs managés dans votre workflow. Le moteur ajoute automatiquement ces ressources de connexion au groupe de ressources Azure que vous avez conçu pour contenir votre application logique.

L’exemple suivant montre comment une connexion d’API pour le connecteur Service Bus managé s’affiche dans le fichier connections.json de votre projet :

```json
"managedApiConnections": {
   "{service-bus-connection-name}": { 
      "api": {
         "id": "/subscriptions/{subscription-ID}/providers/Microsoft.Web/locations/{region}/managedApis/servicebus"
      },
      "connection": { 
         "id": "/subscriptions/{subscription-ID}/resourcegroups/{resource-group-name}/providers/Microsoft.Web/connections/servicebus"
      }, 
      "connectionRuntimeUrl": "{connection-runtime-URL}",
      "authentication": { 
         "type": "Raw",
         "scheme": "Key",
         "parameter": "@appsetting('servicebus_1-connectionKey')"
      },
   },
   <...>
}
```

<a name="azure-functions-connections"></a>

### <a name="azure-functions-connections"></a>Connexions Azure Functions

Pour appeler des fonctions créées et hébergées dans Azure Functions, vous utilisez l’opération Azure Functions intégrée. Les métadonnées de connexion pour les appels Azure Functions sont différentes des autres connexions intégrées. Ces métadonnées sont stockées dans le fichier connections.json de votre projet d’application logique, mais présentent un aspect différent :

```json
"functionConnections": {
   "{function-operation-name}": {
      "function": { 
         "id": "/subscriptions/{subscription-ID}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{function-app-name}/functions/{function-name}"
      },
      "triggerUrl": "{function-url}",
      "authentication": {
        "type": "QueryString",
         "name": "Code",
         "value": "@appsetting('azureFunctionOperation_functionAppKey')"
      }, 
      "displayName": "{functions-connection-display-name}"
   },
   <...>
}
```

## <a name="authentication"></a>Authentification

Dans Azure Logic Apps monolocataire, le modèle d’hébergement pour les workflows d’application logique est un locataire unique dans lequel vos charges de travail bénéficient d’une isolation plus grande que dans le modèle multilocataire. De plus, le runtime Azure Logic Apps monolocataire est portable, ce qui signifie que vous pouvez exécuter vos workflows partout où Azure Functions peut s’exécuter. Toutefois, cette conception exige que les applications logiques puissent authentifier leur identité afin de pouvoir accéder à l’écosystème de connecteurs managés dans Azure. Vos applications ont également besoin des autorisations appropriées pour exécuter des opérations lors de l’utilisation de connexions managées.

Par défaut, chaque application logique monolocataire a une identité managée affectée par le système automatiquement activée. Cette identité diffère des informations d’identification d’authentification ou de la chaîne de connexion utilisées pour la création d’une connexion. Lors de l’exécution, votre application logique utilise cette identité pour authentifier ses connexions par le biais des stratégies d’accès Azure. Si vous désactivez cette identité, les connexions ne fonctionneront pas au moment de l’exécution.

Les sections suivantes fournissent des informations supplémentaires sur les types d’authentification que vous pouvez utiliser pour authentifier les connexions managées, selon l’endroit où s’exécute votre application logique. Pour chaque connexion managée, le fichier connections.json de votre projet d’application logique a un objet `authentication` qui spécifie le type d’authentification que votre application logique peut utiliser pour authentifier cette connexion managée.

### <a name="managed-identity"></a>Identité managée

Pour une application logique hébergée et exécutée dans Azure, une [identité managée](create-managed-service-identity.md) est le type d’authentification par défaut et recommandé à utiliser pour authentifier les connexions managées qui sont hébergées et exécutées dans Azure. Dans le fichier connections.json de votre projet d’application logique, la connexion managée a un objet `authentication` qui spécifie `ManagedServiceIdentity` comme type d’authentification :

```json
"authentication": {
   "type": "ManagedServiceIdentity"
}
```

### <a name="raw"></a>Brute

Pour les applications logiques qui s’exécutent dans votre environnement de développement local avec Visual Studio Code, des clés d’authentification brutes sont utilisées pour authentifier les connexions managées qui sont hébergées et exécutées dans Azure. Ces clés sont conçues uniquement pour le développement, pas pour la production, et expirent au terme de 7 jours. Dans le fichier connections.json de votre projet d’application logique, la connexion managée a un objet `authentication` qui spécifie les informations d’authentification suivantes :

```json
"authentication": {
   "type": "Raw", 
   "scheme": "Key", 
   "parameter": "@appsetting('connectionKey')"
 }
```

## <a name="next-steps"></a>Étapes suivantes

- [Configurer le déploiement DevOps pour Azure Logic Apps monolocataire](set-up-devops-deployment-single-tenant-azure-logic-apps.md)
