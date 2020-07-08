---
title: 'Tutoriel : Lancer le lecteur immersif à l’aide de Python'
titleSuffix: Azure Cognitive Services
description: Dans ce tutoriel, vous allez créer une application Python qui lance le lecteur immersif.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: dylankil
ms.custom: tracking-python
ms.openlocfilehash: ea9e6e1f06bacd28a4e9894039de824c3a5b4af4
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86044938"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-python-sample-project"></a>Tutoriel : Lancer le lecteur immersif à l’aide de l’exemple de projet Python

Dans la [présentation](./overview.md), vous avez appris ce qu’est le Lecteur immersif et comment il implémente des techniques éprouvées pour améliorer la compréhension de la lecture pour les apprenants en langue, les lecteurs émergents et les étudiants présentant des difficultés d’apprentissage. Ce tutoriel explique comment créer une application web Python qui lance le lecteur immersif. Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer une application web Python avec Pip, Flask, Jinja et virtualenv à l’aide d’un exemple de projet
> * Obtenir un jeton d’accès
> * Lancer le Lecteur immersif avec un exemple de contenu

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

* Une ressource Lecteur immersif configurée pour l’authentification Azure Active Directory. Suivez [ces instructions](./how-to-create-immersive-reader.md) pour la configurer. Vous aurez besoin de certaines des valeurs créées ici lors de la configuration des propriétés de l’environnement. Enregistrez la sortie de votre session dans un fichier texte pour référence ultérieure.
* [Git](https://git-scm.com/)
* [Kit SDK Lecteur immersif](https://github.com/microsoft/immersive-reader-sdk)
* [Python](https://www.python.org/downloads/) et [pip](https://docs.python.org/3/installing/index.html). À compter de Python 3.4, pip est inclus par défaut avec les programmes d’installation binaires Python.
* [Flask](https://flask.palletsprojects.com/en/1.0.x/)
* [Jinja](http://jinja.pocoo.org/docs/2.10/)
* [virtualenv](https://virtualenv.pypa.io/en/latest/) et [virtualenvwrapper-win pour Windows](https://pypi.org/project/virtualenvwrapper-win/) ou [virtualenvwrapper pour OSX](https://virtualenvwrapper.readthedocs.io/en/latest/)
* [Module requests](https://pypi.org/project/requests/2.7.0/)
* Un IDE tel que [Visual Studio Code](https://code.visualstudio.com/)

## <a name="configure-authentication-credentials"></a>Configurer les informations d’identification d’authentification

Créez un fichier appelé _.env_, puis collez-y le code suivant, en indiquant les valeurs fournies au moment de la création de votre ressource de lecteur immersif.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Veillez à ne pas valider ce fichier dans le contrôle de code source, car il contient des secrets qui ne doivent pas être rendus publics.

Le point de terminaison d’API **getimmersivereadertoken** doit être sécurisé derrière une certaine forme d’authentification (par exemple [OAuth](https://oauth.net/2/)) pour empêcher les utilisateurs non autorisés d’obtenir des jetons à utiliser auprès de vos services Facturation et Lecteur immersif ; ce travail dépasse le cadre de ce tutoriel.

## <a name="create-a-python-web-app-on-windows"></a>Créer une application web Python sur Windows

Créez une application web Python à l’aide de `flask` sur Windows.

Installez [Git](https://git-scm.com/).

Une fois Git installé, ouvrez une invite de commandes et clonez le dépôt Git du kit SDK Lecteur immersif dans un dossier sur votre ordinateur

```cmd
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Installez [Python](https://www.python.org/downloads/).

Cochez la case Add Python to PATH (Ajouter Python à PATH).

![Boîte de dialogue d’installation de Python sur Windows, étape 1](./media/pythoninstallone.jpg)

Ajoutez des fonctionnalités facultatives en cochant les cases, puis cliquez sur le bouton Suivant.

![Boîte de dialogue d’installation de Python sur Windows, étape 2](./media/pythoninstalltwo.jpg)

Choisissez Installation personnalisée et définissez le chemin d’installation en tant que votre dossier racine, par exemple `C:\Python37-32\`, puis cliquez sur le bouton Installer.

![Boîte de dialogue d’installation de Python sur Windows, étape 3](./media/pythoninstallthree.jpg)

Une fois l’installation de Python terminée, ouvrez une invite de commandes et `cd` dans le dossier Scripts Python.

```cmd
cd C:\Python37-32\Scripts
```

Installez Flask.

```cmd
pip install flask
```

Installez Jinja2. Moteur de modèles complet pour Python.

```cmd
pip install jinja2
```

Installez virtualenv. Outil permettant de créer des environnements Python isolés.

```cmd
pip install virtualenv
```

Installez virtualenvwrapper-win. L’idée derrière virtualenvwrapper consiste à faciliter l’utilisation de virtualenv.

```cmd
pip install virtualenvwrapper-win
```

Installez le module requests. Le module requests est une bibliothèque HTTP sous licence Apache2, écrite en Python.

```cmd
pip install requests
```

Installez le module python-dotenv. Ce module lit la paire clé-valeur à partir du fichier .env et les ajoute à la variable d’environnement.

```cmd
pip install python-dotenv
```

Créez un environnement virtuel

```cmd
mkvirtualenv advanced-python
```

`cd` dans le dossier racine de l’exemple de projet.

```cmd
cd C:\immersive-reader-sdk\js\samples\advanced-python
```

Connectez l’exemple de projet à l’environnement. Cela mappe l’environnement virtuel nouvellement créé au dossier racine de l’exemple de projet.

```cmd
setprojectdir .
```

Activez l’environnement virtuel.

```cmd
activate
```

Le projet doit maintenant être actif et l’invite de commandes affiche un résultat similaire à `(advanced-python) C:\immersive-reader-sdk\js\samples\advanced-python>`.

Désactivez l’environnement.

```cmd
deactivate
```

Le préfixe `(advanced-python)` doit avoir disparu, car l’environnement est maintenant désactivé.

Pour réactiver l’environnement, exécutez `workon advanced-python` à partir du dossier racine de l’exemple de projet.

```cmd
workon advanced-python
```

### <a name="launch-the-immersive-reader-with-sample-content"></a>Lancer le Lecteur immersif avec un exemple de contenu

Lorsque l’environnement est actif, exécutez l’exemple de projet en entrant `flask run` à partir du dossier racine de l’exemple de projet.

```cmd
flask run
```

Ouvrez votre navigateur et accédez à _http://localhost:5000_ .

## <a name="create-a-python-web-app-on-osx"></a>Créer une application web Python sur OSX

Créez une application web Python à l’aide de `flask` sur OSX.

Installez [Git](https://git-scm.com/).

Une fois Git installé, ouvrez le terminal et clonez le dépôt Git du kit SDK Lecteur immersif dans un dossier sur votre ordinateur

```bash
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Installez [Python](https://www.python.org/downloads/).

Le dossier racine Python, par exemple `Python37-32`, doit maintenant se trouver dans le dossier Applications.

Une fois l’installation de Python terminée, ouvrez le terminal et `cd` dans le dossier Scripts Python.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Installer pip.

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

Exécutez ensuite la commande suivante pour installer pip pour l’utilisateur actuellement connecté afin d’éviter les problèmes d’autorisations.

```bash
python get-pip.py --user
```

```bash
sudo nano /etc/paths
```

- Entrez votre mot de passe lorsque vous y êtes invité.
- Ajoutez le chemin de votre installation pip à votre variable PATH.
- Accédez au bas du fichier, puis entrez le chemin que vous souhaitez ajouter en tant que dernier élément de la liste, par exemple `PATH=$PATH:/usr/local/bin`.
- Appuyez sur Ctrl+x pour quitter.
- Entrez `Y` pour enregistrer la mémoire tampon modifiée.
- Et voilà ! Pour effectuer un test, dans une nouvelle fenêtre de terminal, tapez : `echo $PATH`.

Installez Flask.

```bash
pip install flask --user
```

Installez Jinja2. Moteur de modèles complet pour Python.

```bash
pip install Jinja2 --user
```

Installez virtualenv. Outil permettant de créer des environnements Python isolés.

```bash
pip install virtualenv --user
```

Installez virtualenvwrapper. L’idée derrière virtualenvwrapper consiste à faciliter l’utilisation de virtualenv.

```bash
pip install virtualenvwrapper --user
```

Installez le module requests. Le module requests est une bibliothèque HTTP sous licence Apache2, écrite en Python.

```bash
pip install requests --user
```

Installez le module python-dotenv. Ce module lit la paire clé-valeur à partir du fichier .env et les ajoute à la variable d’environnement.

```bash
pip install python-dotenv --user
```

Choisissez un dossier dans lequel vous souhaitez conserver vos environnements virtuels et exécutez cette commande

```bash
mkdir ~/.virtualenvs
```

`cd` dans le dossier de l’exemple d’application Python du kit SDK Lecteur immersif.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Créez un environnement virtuel

```bash
mkvirtualenv -p /usr/local/bin/python3 advanced-python
```

Connectez l’exemple de projet à l’environnement. Cela mappe l’environnement virtuel nouvellement créé au dossier racine de l’exemple de projet.

```bash
setprojectdir .
```

Activez l’environnement virtuel.

```bash
activate
```

Le projet doit maintenant être actif et l’invite de commandes affiche un résultat similaire à `(advanced-python) /immersive-reader-sdk/js/samples/advanced-python>`.

Désactivez l’environnement.

```bash
deactivate
```

Le préfixe `(advanced-python)` doit avoir disparu, car l’environnement est maintenant désactivé.

Pour réactiver l’environnement, exécutez `workon advanced-python` à partir du dossier racine de l’exemple de projet.

```bash
workon advanced-python
```

## <a name="launch-the-immersive-reader-with-sample-content"></a>Lancer le Lecteur immersif avec un exemple de contenu

Lorsque l’environnement est actif, exécutez l’exemple de projet en entrant `flask run` à partir du dossier racine de l’exemple de projet.

```bash
flask run
```

Ouvrez votre navigateur et accédez à _http://localhost:5000_ .

## <a name="next-steps"></a>Étapes suivantes

* Explorer le [SDK Lecteur Immersif](https://github.com/microsoft/immersive-reader-sdk) et la [référence du SDK Lecteur immersif](./reference.md)
* Voir des exemples de code sur [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)
