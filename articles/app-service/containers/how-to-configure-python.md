---
title: Configurer l’image Python intégrée dans Azure App Service
description: Ce tutoriel décrit les options relatives à la création et à la configuration d’une application Python sur Azure App Service avec l’image Python intégrée.
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
ms.date: 09/25/2018
ms.author: astay;cephalin
ms.custom: mvc
ms.openlocfilehash: 9316805993b81e4d2511e833e0cc8f240807a1f9
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228546"
---
# <a name="configure-built-in-python-image-in-azure-app-service-preview"></a>Configurer l’image Python intégrée dans Azure App Service (préversion)

Cet article montre comment configurer l’image Python intégrée dans [App Service sur Linux](app-service-linux-intro.md) pour exécuter vos applications Python.

## <a name="python-version"></a>Version Python

Le runtime Python dans App Service sur Linux utilise la version `python-3.7.0`.

## <a name="supported-frameworks"></a>Frameworks pris en charge

Toutes les versions des frameworks web conformes à l’interface WSGI (Web Server Gateway Interface) et compatibles avec le runtime `python-3.7` sont prises en charge.

## <a name="package-management"></a>Gestion des packages

Durant la publication Git, le moteur Kudu recherche [requirements.txt](https://pip.pypa.io/en/stable/user_guide/#requirements-files) à la racine du dépôt et installe automatiquement les packages dans Azure à l’aide de `pip`.

Pour générer ce fichier avant la publication, accédez à la racine du dépôt et exécutez la commande suivante dans votre environnement Python :

```bash
pip freeze > requirements.txt
```

## <a name="configure-your-python-app"></a>Configurer votre application Python

L’image Python intégrée dans App Service utilise le serveur [Gunicorn](http://gunicorn.org/) pour exécuter votre application Python. Gunicorn est un serveur HTTP WSGI Python pour UNIX. App Service configure automatiquement Gunicorn pour les projets Django et Flask.

### <a name="django-app"></a>Application Django

Si vous publiez un projet Django contenant un module `wsgi.py`, Azure appelle automatiquement Gunicorn à l’aide de la commande suivante :

```bash
gunicorn <path_to_wsgi>
```

### <a name="flask-app"></a>Application Flask

Si vous publiez une application Flask et que le point d’entrée est dans un module `application.py` ou `app.py`, Azure appelle automatiquement Gunicorn à l’aide des commandes suivantes, respectivement :

```bash
gunicorn application:app
```

Ou 

```bash
gunicorn app:app
```

### <a name="customize-start-up"></a>Personnaliser le démarrage

Pour définir un point d’entrée personnalisé pour votre application, commencez par créer un fichier _.txt_ avec une commande Gunicorn personnalisée et placez-le à la racine de votre projet. Par exemple, pour démarrer le serveur avec le module _helloworld.py_ et la variable `app`, créez un fichier _startup.txt_ avec le contenu suivant :

```bash
gunicorn helloworld:app
```

Dans la page [Paramètres d’application](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), sélectionnez **Python | 3.7** comme **Pile d’exécution** et indiquez le nom du **Fichier de démarrage** créé à l’étape précédente. Par exemple, _startup.txt_.
