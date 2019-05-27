---
title: Configurer des applications dans le portail - Azure App Service
description: Apprenez à configurer les paramètres communs pour une application app Service dans le portail Azure.
keywords: service d’application Azure, les application web, les paramètres de l’application, les variables d’environnement
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
ms.openlocfilehash: bcc970375120f76e4ec8a90f487d251296f92dba
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957909"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>Configurer une application app Service dans le portail Azure

Cette rubrique explique comment configurer les paramètres communs pour les applications web, principale mobile ou application API à l’aide du [Portail Azure].

## <a name="configure-app-settings"></a>Configuration des paramètres d’application

Dans App Service, vous utilisez des paramètres d’application tels que les variables d’environnement. Dans le [Portail Azure], accédez à la page de gestion de votre application. Dans le menu de gauche de l’application, cliquez sur **Configuration** > **paramètres de l’Application**.

![Paramètres de l’application](./media/configure-common/open-ui.png)

Pour les développeurs ASP.NET et ASP.NET Core, les paramètres d’application de paramètre dans App Service sont comparable `<appSettings>` dans *Web.config*, mais les valeurs dans App Service remplacent ceux dans *Web.config*. Vous pouvez conserver les paramètres de développement (par exemple, MySQL mot de passe local) dans *Web.config*, mais aucun secret de production (par exemple, mot de passe MySQL de Azure de base de données) au sein de App Service. Le même code utilise vos paramètres de développement lorsque vous déboguez localement, et elle utilise vos secrets de production lors du déploiement sur Azure.

Autres piles de langage, obtient la même manière, les paramètres d’application en tant que variables d’environnement lors de l’exécution. Pour les étapes spécifiques de la pile de langage, consultez :

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.JS](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Conteneurs personnalisés](containers/configure-custom-container.md#configure-environment-variables)

Une fois stockés, les paramètres d’application sont toujours chiffrés (chiffrement au repos).

> [!NOTE]
> Paramètres de l’application peuvent également être résolus à partir de [Key Vault](/azure/key-vault/) à l’aide de [fait référence à Key Vault](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Afficher les valeurs masquées

Par défaut, les valeurs de paramètres d’application sont masqués dans le portail pour la sécurité. Pour afficher une valeur masquée d’un paramètre d’application, cliquez sur le **valeur** champ de ce paramètre. Pour afficher les valeurs de tous les paramètres d’application, cliquez sur le **afficher la valeur** bouton.

### <a name="add-or-edit"></a>Ajouter ou modifier

Pour ajouter un nouveau paramètre d’application, cliquez sur **nouveau paramètre d’application**. Dans la boîte de dialogue, vous pouvez [respecter le paramètre de l’emplacement actuel](deploy-staging-slots.md#which-settings-are-swapped).

Pour modifier un paramètre, cliquez sur le **modifier** bouton situé à droite.

Lorsque vous avez terminé, cliquez sur **mise à jour**. N’oubliez pas de cliquer sur **enregistrer** dans le **Configuration** page.

> [!NOTE]
> Dans un conteneur de Linux par défaut ou un conteneur Linux personnalisé, telles que toute structure clé JSON imbriquée dans le nom de paramètre d’application `ApplicationInsights:InstrumentationKey` doit être configuré dans App Service en tant que `ApplicationInsights__InstrumentationKey` pour le nom de clé. En d’autres termes, un `:` doit être remplacé par `__` (trait de soulignement double).
>

### <a name="edit-in-bulk"></a>Modifier en bloc

Pour ajouter ou modifier les paramètres de l’application en bloc, cliquez sur le **avancé modifier** bouton. Lorsque vous avez terminé, cliquez sur **mise à jour**. N’oubliez pas de cliquer sur **enregistrer** dans le **Configuration** page.

Paramètres de l’application ont le format JSON suivants :

```json
[
  {
    "name": "<key-1>",
    "value": "<value-1>",
    "slotSetting": false
  },
  {
    "name": "<key-2>",
    "value": "<value-2>",
    "slotSetting": false
  },
  ...
]
```

## <a name="configure-connection-strings"></a>Configuration des chaînes de connexion

Dans le [Portail Azure], accédez à la page de gestion de l’application. Dans le menu de gauche de l’application, cliquez sur **Configuration** > **paramètres de l’Application**.

![Paramètres de l’application](./media/configure-common/open-ui.png)

Pour les développeurs ASP.NET et ASP.NET Core, sont des chaînes de connexion de paramètre dans App Service comparable `<connectionStrings>` dans *Web.config*, mais les valeurs que vous définissez dans App Service remplacent ceux dans *Web.config*. Vous pouvez conserver les paramètres de développement (par exemple, un fichier de base de données) dans *Web.config* et aucun secret de production (par exemple, les informations d’identification de base de données SQL)-safe dans App Service. Le même code utilise vos paramètres de développement lorsque vous déboguez localement, et elle utilise vos secrets de production lors du déploiement sur Azure.

Pour les autres piles de langage, il est préférable d’utiliser [paramètres de l’application](#configure-app-settings) au lieu de cela, étant donné que les chaînes de connexion requièrent une mise en forme spéciale dans les clés de variable dans afin d’accéder aux valeurs. Voici une exception près, toutefois : certains types de base de données Azure sont sauvegardés en même temps que l’application si vous configurez leurs chaînes de connexion dans votre application. Pour plus d’informations, consultez [les éléments sauvegardés](manage-backup.md#what-gets-backed-up). Si vous n’avez pas besoin cette sauvegarde automatisée, puis utiliser des paramètres de l’application.

Lors de l’exécution, les chaînes de connexion sont disponibles en tant que variables d’environnement, le préfixe avec les types de connexion suivants :

* SQL Server : `SQLCONNSTR_`
* MySQL : `MYSQLCONNSTR_`
* Base de données SQL : `SQLAZURECONNSTR_`
* Personnalisé : `CUSTOMCONNSTR_`

Par exemple, une chaîne de connexion MySql nommé *connectionstring1* est accessible en tant que la variable d’environnement `MYSQLCONNSTR_connectionString1`. Pour les étapes spécifiques de la pile de langage, consultez :

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.JS](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Conteneurs personnalisés](containers/configure-custom-container.md#configure-environment-variables)

Une fois stockées, les chaînes de connexion sont toujours chiffrées (chiffrement au repos).

> [!NOTE]
> Chaînes de connexion peuvent également être résolus à partir de [Key Vault](/azure/key-vault/) à l’aide de [fait référence à Key Vault](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Afficher les valeurs masquées

Par défaut, les valeurs pour les chaînes de connexion sont masquées dans le portail pour la sécurité. Pour afficher une valeur masquée d’une chaîne de connexion, cliquez simplement sur le **valeur** champ de cette chaîne. Pour afficher les valeurs de toutes les chaînes de connexion, cliquez sur le **afficher la valeur** bouton.

### <a name="add-or-edit"></a>Ajouter ou modifier

Pour ajouter une nouvelle chaîne de connexion, cliquez sur **nouvelle chaîne de connexion**. Dans la boîte de dialogue, vous pouvez [coller la chaîne de connexion à l’emplacement actuel](deploy-staging-slots.md#which-settings-are-swapped).

Pour modifier un paramètre, cliquez sur le **modifier** bouton situé à droite.

Lorsque vous avez terminé, cliquez sur **mise à jour**. N’oubliez pas de cliquer sur **enregistrer** dans le **Configuration** page.

### <a name="edit-in-bulk"></a>Modifier en bloc

Pour ajouter ou modifier des chaînes de connexion en bloc, cliquez sur le **avancé modifier** bouton. Lorsque vous avez terminé, cliquez sur **mise à jour**. N’oubliez pas de cliquer sur **enregistrer** dans le **Configuration** page.

Chaînes de connexion ont le format JSON suivants :

```json
[
  {
    "name": "name-1",
    "value": "conn-string-1",
    "type": "SQLServer",
    "slotSetting": false
  },
  {
    "name": "name-2",
    "value": "conn-string-2",
    "type": "PostgreSQL",
    "slotSetting": false
  },
  ...
]
```

<a name="platform"></a>
<a name="alwayson"></a>

## <a name="configure-general-settings"></a>Configurer les paramètres généraux

Dans le [Portail Azure], accédez à la page de gestion de l’application. Dans le menu de gauche de l’application, cliquez sur **Configuration** > **paramètres de l’Application**.

![Paramètres généraux :](./media/configure-common/open-general.png)

Ici, vous pouvez configurer certains paramètres courants pour l’application. Certains paramètres nécessitent que vous [mise à l’échelle jusqu'à des niveaux tarifaires supérieurs](web-sites-scale.md).

- **Paramètres de la pile**: La pile logicielle pour exécuter l’application, notamment la langue et les versions SDK. Pour les applications Linux et les applications de conteneur personnalisé, vous pouvez également définir une commande de démarrage facultatif ou d’un fichier.
- **Paramètres de plateforme**: Vous permet de configurer les paramètres pour la plateforme d’hébergement, notamment :
    - **Nombre de bits**: 32 bits ou 64 bits.
    - **Protocole WebSocket**: Pour [ASP.NET SignalR] ou [socket.io](https://socket.io/), par exemple.
    - **Always On**: Gardez à l’application chargée même lorsqu’il n’existe aucun trafic. Vous devez l’activer pour les tâches Web continues ou pour les tâches Web déclenchées à l’aide d’une expression CRON.
    - **Version de pipeline managé**: IIS [mode pipeline]. Affectez-lui la valeur **Classic** si vous avez une application héritée qui requiert une version antérieure de IIS.
    - **Version HTTP**: Utilisez la version **2.0** pour activer la prise en charge du protocole [HTTPS/2](https://wikipedia.org/wiki/HTTP/2).
    > [!NOTE]
    > Les navigateurs les plus récents prennent en charge le protocole HTTP/2 sur TLS uniquement, alors que le trafic non chiffré continue d’utiliser HTTP/1.1. Pour vous assurer que le client navigateurs vous connecter à votre application avec HTTP/2, soit [acheter un certificat App Service](web-sites-purchase-ssl-web-site.md) pour le domaine personnalisé de l’application ou [lier un certificat tiers](app-service-web-tutorial-custom-ssl.md).
    - **Affinité ARR**: Dans un déploiement multi-instance, assurez-vous que le client est acheminé vers la même instance pour la durée de vie de la session. Vous pouvez définir cette option sur **hors** pour les applications sans état.
- **Débogage**: Activer le débogage distant pour [ASP.NET](troubleshoot-dotnet-visual-studio.md#remotedebug), [ASP.NET Core](/visualstudio/debugger/remote-debugging-azure), ou [Node.js](containers/configure-language-nodejs.md#debug-remotely) applications. Cette option désactive automatiquement au bout de 48 heures.
- **Certificats clients entrants**: exiger des certificats clients dans [l’authentification mutuelle](app-service-web-configure-tls-mutual-auth.md).

## <a name="configure-default-documents"></a>Configurer des documents par défaut

Ce paramètre est uniquement pour les applications de Windows.

Dans le [Portail Azure], accédez à la page de gestion de l’application. Dans le menu de gauche de l’application, cliquez sur **Configuration** > **documents par défaut**.

![Paramètres généraux :](./media/configure-common/open-documents.png)

Le document par défaut est la page web qui s’affiche à l’URL racine pour un site Web. Le premier fichier correspondant dans la liste est utilisé. Pour ajouter un nouveau document par défaut, cliquez sur **nouveau document**. N’oubliez pas de cliquer sur **enregistrer**.

Si l’application utilise des modules qui acheminent basés sur l’URL au lieu de proposer du contenu statique, il n’est pas nécessaire pour les documents par défaut.

## <a name="configure-path-mappings"></a>Configurer les mappages de chemin d’accès

Dans le [Portail Azure], accédez à la page de gestion de l’application. Dans le menu de gauche de l’application, cliquez sur **Configuration** > **les mappages de chemin d’accès**.

![Paramètres généraux :](./media/configure-common/open-path.png)

Le **les mappages de chemin d’accès** présente des choses différentes en fonction du type de système d’exploitation.

### <a name="windows-apps-uncontainerized"></a>Applications Windows (uncontainerized)

Pour les applications Windows, vous pouvez personnaliser les mappages de gestionnaire IIS et les applications virtuelles et les répertoires.

Mappages de gestionnaires vous permettent d’ajouter des processeurs de script personnalisé pour gérer les demandes pour les extensions de fichier spécifique. Pour ajouter un gestionnaire personnalisé, cliquez sur **nouveau gestionnaire**. Configurez le gestionnaire comme suit :

- **Extension**. L’extension de fichier que vous souhaitez gérer, tels que  *\*.php* ou *handler.fcgi*.
- **Processeur de script**. Le chemin d’accès absolu du processeur de script pour vous. Les demandes de fichiers qui correspondent à l’extension de fichier sont traités par le processeur de script. Utilisez le chemin `D:\home\site\wwwroot` pour faire référence au répertoire racine de votre application Web.
- **Arguments**. Arguments de ligne de commande facultatifs pour le processeur de script.

Chaque application a le chemin d’accès de la racine par défaut (`/`) mappé à `D:\home\site\wwwroot`, où votre code est déployé par défaut. Si la racine de votre application se trouve dans un dossier différent, ou si votre référentiel possède plusieurs applications, vous pouvez modifier ou ajouter des applications virtuelles et des répertoires ici. Cliquez sur **nouvelle application virtuelle ou le répertoire**.

Pour configurer des applications virtuelles et des répertoires, spécifiez chaque répertoire virtuel et son chemin d’accès physique correspondant par rapport à la racine du site Web (`D:\home`). Vous pouvez éventuellement cocher la case **Application** pour marquer un répertoire virtuel comme application.

### <a name="containerized-apps"></a>Applications en conteneur

Vous pouvez [ajouter un stockage personnalisé pour votre application en conteneur](containers/how-to-serve-content-from-azure-storage.md). Les applications en conteneur incluent toutes les applications Linux ainsi que sur les conteneurs personnalisés Windows et Linux en cours d’exécution sur App Service. Cliquez sur **monter de stockage Azure nouveau** et configurez votre stockage personnalisé comme suit :

- **Nom** : Le nom complet.
- **Options de configuration**: **Base** ou **Advanced**.
- **Comptes de stockage** : Le compte de stockage avec le conteneur.
- **Type de stockage** : **Objets BLOB Azure** ou **Azure Files**.
  > [!NOTE]
  > Applications de conteneur Windows prennent uniquement en charge Azure Files.
- **Conteneur de stockage** : Configuration de base, le conteneur que vous souhaitez.
- **Nom de partage**: Pour une configuration avancée, le partage de fichiers nom.
- **Clé d’accès**: Pour la configuration avancée de la clé d’accès.
- **Chemin de montage**: Le chemin d’accès absolu dans votre conteneur de montage du stockage personnalisé.

Pour plus d’informations, consultez [traiter du contenu à partir du stockage Azure dans App Service sur Linux](containers/how-to-serve-content-from-azure-storage.md).

## <a name="configure-language-stack-settings"></a>Configurer les paramètres de langue de pile

Pour les applications Linux, consultez :

- [ASP.NET Core](containers/configure-language-dotnetcore.md)
- [Node.JS](containers/configure-language-nodejs.md)
- [PHP](containers/configure-language-php.md)
- [Python](containers/how-to-configure-python.md)
- [Java](containers/configure-language-java.md)
- [Ruby](containers/configure-language-ruby.md)

## <a name="configure-custom-containers"></a>Configurer des conteneurs personnalisés

Consultez [configurer un conteneur Linux personnalisé pour Azure App Service](containers/configure-custom-container.md)

## <a name="next-steps"></a>Étapes suivantes

- [Configuration d’un nom de domaine personnalisé dans Azure App Service]
- [Configurer des environnements intermédiaires dans Azure App Service]
- [Activer le protocole HTTPS pour une application dans Azure App Service]
- [Activer les journaux de Diagnostics](troubleshoot-diagnostic-logs.md)
- [Mettre à l’échelle une application dans Azure App Service]
- [Concepts de base de la supervision dans Azure App Service]
- [Modifier les paramètres du fichier applicationHost.config avec applicationHost.xdt](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET SignalR]: https://www.asp.net/signalr
[Portail Azure]: https://portal.azure.com/
[Configuration d’un nom de domaine personnalisé dans Azure App Service]: ./app-service-web-tutorial-custom-domain.md
[Configurer des environnements intermédiaires dans Azure App Service]: ./deploy-staging-slots.md
[Activer le protocole HTTPS pour une application dans Azure App Service]: ./app-service-web-tutorial-custom-ssl.md
[How to: Monitor web endpoint status]: https://go.microsoft.com/fwLink/?LinkID=279906
[Concepts de base de la supervision dans Azure App Service]: ./web-sites-monitor.md
[mode pipeline]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Mettre à l’échelle une application dans Azure App Service]: ./web-sites-scale.md
