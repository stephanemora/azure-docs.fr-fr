---
title: Configurer des applications - Azure App Service
description: Configuration d’une application dans Azure App Service
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: fb8dedac8b795ec127d7b4a14728d73c9397a1dd
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2019
ms.locfileid: "56807403"
---
# <a name="configure-apps-in-azure-app-service"></a>Configurer des applications dans Azure App Service

Cette rubrique explique comment configurer une application web, back-end mobile ou API à l’aide du [portail Azure].

## <a name="application-settings"></a>Paramètres de l’application
1. Dans le [Portail Azure], ouvrez le panneau de l’application.
2. Cliquez sur **Paramètres de l’application**.

![Paramètres de l’application][configure01]

Le panneau **Paramètres de l’application** regroupe différents paramètres sous plusieurs catégories.

### <a name="general-settings"></a>Paramètres généraux :
**Versions d'infrastructure**. Définissez ces options si votre application utilise l'une de ces infrastructures : 

* **.NET Framework** : définissez la version .NET Framework. 
* **PHP** : définissez la version PHP ou choisissez **INACTIF** pour désactiver PHP. 
* **Java** : sélectionnez la version Java ou choisissez **INACTIF** pour désactiver Java. Utilisez l’option **Conteneur Web** pour choisir entre les versions Tomcat et Jetty.
* **Python** : sélectionnez la version Python ou choisissez **INACTIF** pour désactiver Python.

Pour des raisons techniques, l’activation de Java pour votre application désactive les options .NET, PHP et Python.

<a name="platform"></a>
**Plateforme**. Indique si votre application s’exécute dans un environnement 32 bits ou 64 bits. L'environnement 64 bits demande le niveau De base ou Standard. Les niveaux Gratuit et Partagé s'exécutent uniquement dans un environnement 32 bits.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

**Web Sockets**. Sélectionnez la valeur **ACTIF** pour activer le protocole WebSocket, par exemple si votre application utilise [ASP.NET SignalR] ou [socket.io](https://socket.io/).

<a name="alwayson"></a>
**Toujours actif**. Par défaut, les applications sont déchargées si elles sont inactives pendant un certain temps. Cela permet au système d’économiser des ressources. En mode De base ou Standard, vous pouvez activer l’option **Toujours actif** pour garder l’application chargée en permanence. Si votre application exécute des WebJobs en continu ou après déclenchement par une expression CRON, activez l’option **Toujours actif**. Sinon, ils risquent de ne pas s’exécuter de façon fiable.

**Version de pipeline gérée**. Définit le [mode pipeline]d'IIS. Laissez la valeur par défaut, Intégré, sauf si vous avez une application web qui demande une version plus ancienne d’IIS.

**Version HTTP**. Utilisez la version **2.0** pour activer la prise en charge du protocole [HTTPS/2](https://wikipedia.org/wiki/HTTP/2). 

> [!NOTE]
> Les navigateurs les plus récents prennent en charge le protocole HTTP/2 sur TLS uniquement, alors que le trafic non chiffré continue d’utiliser HTTP/1.1. Pour vous assurer que les navigateurs clients se connectent à votre application par HTTP/2, [achetez un certificat de service d’application](web-sites-purchase-ssl-web-site.md) pour le domaine personnalisé de votre application ou [liez un certificat tiers](app-service-web-tutorial-custom-ssl.md).

**Affinité ARR**. Dans une application montée en charge en plusieurs instances de machine virtuelle, les cookies Affinité ARR garantissent l’acheminement du client vers la même instance pour la durée de vie de la session. Pour améliorer les performances des applications sans état, définissez cette option sur **Off**.   

**Basculement automatique**. Si vous activez le basculement automatique pour un emplacement de déploiement, App Service fera basculer l’application en production automatiquement quand vous enverrez (push) une mise à jour sur cet emplacement. Pour plus d’informations, consultez [Déployer vers des emplacements intermédiaires pour les applications dans Azure App Service](deploy-staging-slots.md).

### <a name="debugging"></a>Débogage
**Débogage à distance**. Active le débogage distant. Quand cette option est activée, vous pouvez utiliser le débogueur distant de Visual Studio pour vous connecter directement à votre application. Le débogage à distance reste activé pendant 48 heures. 

### <a name="app-settings"></a>Paramètres de l’application
Cette section contient des paires nom/valeur qui seront chargées par votre application au démarrage. 

* Dans le cas des applications .NET, ces paramètres sont inclus dans les `AppSettings` de votre configuration .NET au moment de l’exécution, en remplacement des paramètres existants. 
* Pour App Service sur Linux ou Web App pour conteneurs, si vous avez imbriqué une structure de clé json dans votre nom, par exemple `ApplicationInsights:InstrumentationKey`, vous avez besoin d’utiliser `ApplicationInsights__InstrumentationKey` comme nom de clé. Ainsi, notez le remplacement de `:` par `__` (autrement dit, un trait de soulignement double).
* Les applications PHP, Python, Java et Node peuvent accéder à ces paramètres sous forme de variables d'environnement au moment de l'exécution. Pour chaque paramètre d'application, deux variables d'environnement sont créées : l'une avec le nom spécifié par l'entrée du paramètre d'application, et l'autre avec le préfixe APPSETTING_. Elles contiennent toutes les deux la même valeur.

Une fois stockés, les paramètres d’application sont toujours chiffrés (chiffrement au repos).

Les paramètres d’application peuvent être résolus à partir de Key Vault à l’aide des [Références Key Vault](app-service-key-vault-references.md).

### <a name="connection-strings"></a>Chaînes de connexion
Chaînes de connexion des ressources liées. 

Pour les applications .NET, ces chaînes de connexion sont incluses dans les paramètres des `connectionStrings` de votre configuration .NET au moment de l’exécution, en remplacement des entrées existantes où la clé est identique à celle du nom de la base de données liée. 

Pour les applications PHP, Python, Java et Node, ces paramètres sont disponibles en tant que variables d'environnement au moment de l'exécution, avec le type de connexion comme préfixe. Les préfixes de la variable d'environnement sont les suivants : 

* SQL Server : `SQLCONNSTR_`
* MySQL : `MYSQLCONNSTR_`
* Base de données SQL : `SQLAZURECONNSTR_`
* Personnalisé : `CUSTOMCONNSTR_`

Par exemple, si une chaîne de connexion MySql se nomme `connectionstring1`, elle est accessible par le biais de la variable d’environnement `MYSQLCONNSTR_connectionString1`.

Une fois stockées, les chaînes de connexion sont toujours chiffrées (chiffrement au repos).

Les chaînes de connexion peuvent être résolues à partir de Key Vault à l’aide des [Références Key Vault](app-service-key-vault-references.md).

### <a name="default-documents"></a>Documents par défaut
Le document par défaut est la page web qui s’affiche à l’URL racine pour un site web.  Le premier fichier correspondant dans la liste est utilisé. 

Les applications peuvent utiliser des modules qui effectuent un routage en fonction de l’URL, au lieu de proposer du contenu statique. Dans ce cas, il n’existe pas de document par défaut.    

### <a name="handler-mappings"></a>Mappages de gestionnaires
Utilisez cette zone pour ajouter des processeurs de script personnalisés et gérer les requêtes portant sur des extensions de fichiers spécifiques. 

* **Extension**. Extension de fichier à gérer, par exemple *.php ou handler.fcgi. 
* **Chemin d'accès du processeur de script**. Chemin d'accès absolu du processeur de script. Les requêtes de fichiers qui correspondent à l'extension de fichier sont traitées par le processeur de script. Utilisez le chemin `D:\home\site\wwwroot` pour faire référence au répertoire racine de votre application Web.
* **Arguments supplémentaires**. Arguments de ligne de commande facultatifs pour le processeur de script. 

### <a name="virtual-applications-and-directories"></a>Applications et répertoires virtuels
Pour configurer des applications et des répertoires virtuels, spécifiez chaque répertoire virtuel et son chemin d’accès physique correspondant par rapport à la racine du site web. Vous pouvez éventuellement cocher la case **Application** pour marquer un répertoire virtuel comme application.

## <a name="enabling-diagnostic-logs"></a>Activation des journaux de diagnostic
Pour activer les journaux de diagnostic, procédez comme suit :

1. Dans le panneau de votre application, cliquez sur **Tous les paramètres**.
2. Cliquez sur **Journaux de diagnostic**. 

Options liées à l’écriture des journaux de diagnostic à partir d’une application web qui prend en charge la journalisation : 

* **Journalisation des applications**. Enregistre des journaux d’activité d’application dans le système de fichiers. La journalisation s’étend sur une période de 12 heures. 

**Niveau**. Lorsque la journalisation des applications est activée, cette option indique la quantité d’informations qui sera enregistrée (Erreur, Avertissement, Information ou Détaillé).

**Journalisation du serveur Web**. Les journaux d’activité sont enregistrés au format de fichier journal étendu W3C. 

**Messages d’erreur détaillés**. Enregistre des messages d’erreur détaillés dans des fichiers .htm. Les fichiers sont enregistrés sous /LogFiles/DetailedErrors. 

**Suivi des demandes ayant échoué**. Enregistre les demandes ayant échoué dans des fichiers XML. Les fichiers sont enregistrés sous /LogFiles/W3SVC*xxx*, où xxx est un identificateur unique. Ce dossier contient un fichier XSL et un ou plusieurs fichiers XML. Veillez à télécharger le fichier XSL, car il fournit des fonctionnalités de mise en forme et de filtrage du contenu des fichiers XML.

Pour afficher les fichiers journaux, vous devez créer des informations d’identification FTP, comme suit :

1. Dans le panneau de votre application, cliquez sur **Tous les paramètres**.
2. Cliquez sur **Informations d’identification du déploiement**.
3. Entrez un nom d'utilisateur et un mot de passe.
4. Cliquez sur **Enregistrer**.

![Définir les informations d’identification de déploiement][configure03]

Le nom d’utilisateur FTP complet est « app\username », où *app* représente le nom de votre application. Le nom d’utilisateur est listé dans le panneau de l’application, sous **Essentials**.

![Informations d’identification de déploiement FTP][configure02]

## <a name="other-configuration-tasks"></a>Autres tâches de configuration
### <a name="ssl"></a>SSL
En mode De base ou Standard, vous pouvez télécharger des certificats SSL pour un domaine personnalisé. Pour plus d’informations, consultez [Activer le protocole HTTPS pour une application](app-service-web-tutorial-custom-ssl.md). 

Pour afficher vos certificats téléchargés, cliquez sur **Tous les paramètres** > **Domaines personnalisés et SSL**.

### <a name="domain-names"></a>Noms de domaine
Ajoutez des noms de domaine personnalisés pour votre application. Pour plus d’informations, consultez [Configurer un nom de domaine personnalisé pour une application dans Azure App Service](app-service-web-tutorial-custom-domain.md).

Pour afficher vos noms de domaine, cliquez sur **Tous les paramètres** > **Domaines personnalisés et SSL**.

### <a name="deployments"></a>Déploiements
* Configurer un déploiement continu Consultez [Utilisation de Git pour déployer des applications dans Azure App Service](deploy-local-git.md).
* Emplacements de déploiement Consultez [Déployer sur des environnements intermédiaires pour Azure App Service].

Pour afficher vos emplacements de déploiement, cliquez sur **Tous les paramètres** > **Emplacements de déploiement**.

### <a name="monitoring"></a>Surveillance
En mode De base ou Standard, vous pouvez tester la disponibilité des points de terminaison HTTP ou HTTPS à partir de trois emplacements géo-distribués au maximum. Un test de surveillance échoue si le code de réponse HTTP est une erreur (4xx ou 5xx) ou si la réponse prend plus de 30 secondes. Un point de terminaison est considéré comme disponible s'il passe avec succès les différents tests de surveillance à partir de tous les emplacements spécifiés. 

Pour plus d’informations, consultez [Procédure : surveiller l’état d’un point de terminaison web].

## <a name="next-steps"></a>Étapes suivantes
* [Configuration d’un nom de domaine personnalisé dans Azure App Service]
* [Activer le protocole HTTPS pour une application dans Azure App Service]
* [Mettre à l’échelle une application dans Azure App Service]
* [Concepts de base de la supervision dans Azure App Service]
* [Modifier les paramètres du fichier applicationHost.config avec applicationHost.xdt](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET SignalR]: https://www.asp.net/signalr
[Portail Azure]: https://portal.azure.com/
[Configuration d’un nom de domaine personnalisé dans Azure App Service]: ./app-service-web-tutorial-custom-domain.md
[Déployer sur des environnements intermédiaires pour Azure App Service]: ./deploy-staging-slots.md
[Activer le protocole HTTPS pour une application dans Azure App Service]: ./app-service-web-tutorial-custom-ssl.md
[Procédure : surveiller l’état d’un point de terminaison web]: https://go.microsoft.com/fwLink/?LinkID=279906
[Concepts de base de la supervision dans Azure App Service]: ./web-sites-monitor.md
[mode pipeline]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Mettre à l’échelle une application dans Azure App Service]: ./web-sites-scale.md

<!-- IMG List -->

[configure01]: ./media/web-sites-configure/configure01.png
[configure02]: ./media/web-sites-configure/configure02.png
[configure03]: ./media/web-sites-configure/configure03.png
