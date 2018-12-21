---
title: Configurer des applications Python sur Linux - Azure App Service
description: Ce tutoriel décrit les options relatives à la création et à la configuration d’une application Python pour Azure App Service sur Linux.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/09/2018
ms.author: astay;cephalin;kraigb
ms.custom: seodec18
ms.openlocfilehash: 1d9b0e356f0f65be44a533fe098282084b900d89
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53249632"
---
# <a name="configure-your-python-app-for-the-azure-app-service-on-linux"></a>Configurer votre application Python pour Azure App Service sur Linux

Cet article explique la façon dont [Azure App Service sur Linux](app-service-linux-intro.md) exécute des applications Python, et comment vous pouvez personnaliser le comportement d’App Service si nécessaire.

## <a name="set-python-version"></a>Définir la version de Python

Deux images de base sont disponibles : Python 3.6 et Python 3.7. Vous pouvez créer une application avec l’image basée sur Python souhaitée. Par exemple, pour créer une application avec Python 3.7, exécutez la commande suivante dans Cloud Shell :

```azurecli-interactive
az webapp create --resource-group <group_name> --plan <plan_name> --name <app_name> --runtime "PYTHON|3.7"
```

Pour remplacer la version de Python (basée sur une image) par Python 3.6, par exemple, exécutez la commande suivante dans Cloud Shell :

```azurecli-interactive
az webapp config set --resource-group <group_name> --name <app_name> --linux-fx-version "PYTHON|3.6"
```

Si vous avez besoin d’une autre version de Python, vous devez générer et déployer votre propre image conteneur à la place. Pour plus d’informations, consultez le [Guide pratique pour utiliser une image Docker personnalisée pour Web App pour conteneurs](tutorial-custom-docker-image.md).

## <a name="container-characteristics"></a>Caractéristiques du conteneur

Les applications Python déployées sur App Service sur Linux s’exécutent dans un conteneur Docker défini dans le dépôt GitHub [Python 3.6](https://github.com/Azure-App-Service/python/tree/master/3.6.6) ou [Python 3.7](https://github.com/Azure-App-Service/python/tree/master/3.7.0).

Ce conteneur présente les caractéristiques suivantes :

- Les applications sont exécutées à l’aide de [Gunicorn WSGI HTTP Server](https://gunicorn.org/), en utilisant les arguments supplémentaires `--bind=0.0.0.0 --timeout 600`.

- Par défaut, l’image de base inclut l’infrastructure web Flask, mais le conteneur prend en charge les autres infrastructures compatibles WSGI et Python 3.7, telles que Django.

- Pour installer des packages supplémentaires, tels que Django, créez un fichier [*requirements.txt*](https://pip.pypa.io/en/stable/user_guide/#requirements-files) à la racine de votre projet en utilisant `pip freeze > requirements.txt`. Ensuite, publiez votre projet sur App Service à l’aide du déploiement Git, qui exécute automatiquement `pip install -r requirements.txt` dans le conteneur pour installer les dépendances de votre application.

## <a name="container-startup-process-and-customizations"></a>Processus de démarrage et personnalisations du conteneur

Lors du démarrage, App Service sur un conteneur Linux exécute les étapes suivantes :

1. Rechercher et appliquer une commande de démarrage personnalisée, si elle est fournie.
2. Vérifier l’existence d’un fichier *wsgi.py* dans l’application Django et, si tel est le cas, lancer Gunicorn à l’aide de ce fichier.
3. Rechercher un fichier nommé *application.py* et, s’il existe, lancer Gunicorn avec `application:app` en supposant qu’il s’agit d’une application Flask.
4. Si aucune autre application n’est trouvée, démarrer une application par défaut qui est créée dans le conteneur.

Les sections suivantes fournissent des détails supplémentaires sur chaque option.

### <a name="django-app"></a>Application Django

Pour les applications Django, App Service recherche un fichier nommé `wsgi.py` dans le code de votre application et exécute Gunicorn à l’aide de la commande suivante :

```bash
# <module> is the path to the folder containing wsgi.py
gunicorn --bind=0.0.0.0 --timeout 600 <module>.wsgi
```

Si vous recherchez un contrôle plus spécifique de la commande de démarrage, utilisez une [commande de démarrage personnalisée](#custom-startup-command) et remplacez `<module>` par le nom du module qui contient *wsgi.py*.

### <a name="flask-app"></a>Application Flask

Pour Flask, App Service recherche un fichier nommé *application.py* et démarre Gunicorn comme suit :

```bash
gunicorn --bind=0.0.0.0 --timeout 600 application:app
```

Si votre module d’application principal est contenu dans un autre fichier, utilisez un nom différent pour l’objet d’application. Si vous souhaitez fournir des arguments supplémentaires à Gunicorn, utilisez une [commande de démarrage personnalisée](#custom-startup-command). Cette section donne un exemple pour Flask en utilisant un code d’entrée dans *hello.py* et un objet d’application Flask nommé `myapp`.

### <a name="custom-startup-command"></a>Commande de démarrage personnalisée

Vous pouvez contrôler le comportement de démarrage du conteneur en fournissant une commande de démarrage Gunicorn personnalisée. Par exemple, si vous disposez d’une application Flask dont le module principal est *hello.py* et que l’objet d’application Flask dans le fichier est nommé `myapp`, la commande est la suivante :

```bash
gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
```

Si votre module principal est dans un sous-dossier, tel que `website`, précisez ce dossier grâce à l’argument `--chdir` :

```bash
gunicorn --bind=0.0.0.0 --timeout 600 --chdir website hello:myapp
```

Vous pouvez également ajouter à la commande tout argument supplémentaire pour Gunicorn, tel que `--workers=4`. Pour plus d’informations, consultez [Running Gunicorn](https://docs.gunicorn.org/en/stable/run.html) (Exécuter Gunicorn - docs.gunicorn.org).

Pour fournir une commande personnalisée, procédez comme suit :

1. Accédez à la page [Paramètres de l’application](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) sur le portail Azure.

1. Dans les paramètres **Runtime**, paramétrez l’option **Stack** (pile) sur **Python 3.7** et entrez la commande directement dans le champ **Startup file** (fichier de démarrage).

    Vous pouvez également enregistrer la commande dans un fichier texte à la racine de votre projet, utilisant un nom comme *startup.txt* (ou autre nom de votre choix). Déployez ensuite ce fichier vers App Service, puis remplacez le nom dans le champ **Startup file** (fichier de démarrage) par le nom de votre fichier. Cette option vous permet de gérer la commande dans votre référentiel de code source plutôt que d’utiliser le portail Azure.

1. Sélectionnez **Enregistrer**. App Service redémarre automatiquement, et après quelques secondes, vous devriez voir la commande de démarrage personnalisée appliquée.

> [!Note]
> App Service ignore les erreurs qui surviennent lors du traitement d’un fichier de commande personnalisé, puis continue son processus de démarrage en recherchant des applications Django et Flask. Si vous ne voyez pas le comportement attendu, vérifiez que votre fichier de démarrage est déployé sur App Service et qu’il ne contient pas d’erreurs.

### <a name="default-behavior"></a>Comportement par défaut

Si App Service ne trouve pas de commande personnalisée, d’application Django ou d’application Flask, une application par défaut en lecture seule, située dans le dossier _defaultsite/opt_, est exécutée. L’application par défaut se présente comme suit :

![App Service par défaut sur une page web Linux](media/how-to-configure-python/default-python-app.png)

## <a name="troubleshooting"></a>Résolution de problèmes

- **Vous voyez l’application par défaut après le déploiement du code de votre propre application.**  L’application par défaut s’affiche, soit parce que n’avez pas déployé le code de votre application vers App Service, soit parce qu’App Service n’a pas trouvé le code de votre application et a exécuté l’application par défaut à la place.
  - Redémarrez App Service, patientez 15 à 20 secondes et vérifiez de nouveau l’application.
  - Vérifiez que vous utilisez bien App Service pour Linux, et non une instance basée sur Windows. À partir de l’interface CLI, exécutez la commande `az webapp show --resource-group <resource_group_name> --name <app_service_name> --query kind`, en remplaçant `<resource_group_name>` et `<app_service_name>` en conséquence. `app,linux` doit être la sortie ; si ce n’est pas le cas, recréez l’App Service et choisissez Linux.
    - Utilisez SSH ou la console Kudu pour vous connecter directement à App Service et vérifiez que vos fichiers existent dans *site/wwwroot*. Si vos fichiers n’existent pas, reconsidérez votre processus de déploiement et redéployez l’application.
  - Si vos fichiers existent, cela signifie qu’App Service n’a pas été en mesure d’identifier votre fichier de démarrage. Vérifiez que votre application est structurée selon ce qu’App Service attend pour [Django](#django-app) ou [Flask](#flask-app), ou bien utilisez une [commande de démarrage personnalisée](#custom-startup-command).
  
- **Le message « Service indisponible » s’affiche dans le navigateur.** Le délai d’attente du navigateur a expiré en l’absence d’une réponse d’App Service. Cela indique qu’App Service a démarré le serveur Gunicorn, mais que les arguments qui spécifient le code d’application sont incorrects.
  - Actualisez le navigateur, en particulier si vous utilisez les niveaux tarifaires les plus bas pour votre Plan App Service. Par exemple, l’application peut prendre plus de temps à démarrer si vous utilisez des niveaux gratuits et répondre une fois que vous avez actualisé le navigateur.
  - Vérifiez que votre application est structurée selon ce qu’App Service attend pour [Django](#django-app) ou [Flask](#flask-app), ou bien utilisez une [commande de démarrage personnalisée](#custom-startup-command).
  - Utilisez SSH ou la Console Kudu pour vous connecter à App Service, puis examinez les journaux de diagnostic stockés dans le dossier *LogFiles*. Pour plus d’informations sur la journalisation, consultez [Activer la journalisation des diagnostics pour les applications web dans Azure App Service](../web-sites-enable-diagnostic-log.md).
