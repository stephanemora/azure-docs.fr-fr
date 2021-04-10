---
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: f88ca39a378e997bb72300188166192e3383f6f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102620268"
---
## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services)
* Une ressource Lecteur immersif configurée pour l’authentification Azure Active Directory. Suivez [ces instructions](../../how-to-create-immersive-reader.md) pour la configurer.  Vous aurez besoin de certaines des valeurs créées ici lors de la configuration des propriétés de l’environnement. Enregistrez la sortie de votre session dans un fichier texte pour référence ultérieure.
* [Git](https://git-scm.com/).
* [Kit SDK Lecteur immersif](https://github.com/microsoft/immersive-reader-sdk).
* [Python](https://www.python.org/downloads/) et [pip](https://docs.python.org/3/installing/index.html). À compter de Python 3.4, pip est inclus par défaut avec les programmes d’installation binaires Python.
* [Flask](https://flask.palletsprojects.com/en/1.0.x/).
* [Jinja](http://jinja.pocoo.org/docs/2.10/).
* [virtualenv](https://virtualenv.pypa.io/en/latest/) et [virtualenvwrapper-win pour Windows](https://pypi.org/project/virtualenvwrapper-win/) ou [virtualenvwrapper pour OSX](https://virtualenvwrapper.readthedocs.io/en/latest/).
* Le [module requests](https://pypi.org/project/requests/2.7.0/).
* Un IDE tel que [Visual Studio Code](https://code.visualstudio.com/).

## <a name="configure-authentication-credentials"></a>Configurer les informations d’identification d’authentification

Créez un fichier nommé **.env** et collez-y les noms et valeurs suivants. Spécifiez les valeurs fournies au moment de la création de votre ressource Lecteur immersif.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Ne validez pas ce fichier dans le contrôle de code source, car il contient des secrets qui ne doivent pas être rendus publics.

Sécurisez le point de terminaison de l’API **getimmersivereadertoken** à l’aide d’une méthode d’authentification comme [OAuth](https://oauth.net/2/). L’authentification empêche les utilisateurs non autorisés d’obtenir des jetons qu’ils pourraient utiliser avec votre service Lecteur immersif et la facturation du service. Cet aspect dépasse le cadre de ce tutoriel.

## <a name="create-a-python-web-app-on-windows"></a>Créer une application web Python sur Windows

Créez une application web Python à l’aide de `flask` sur Windows.

Installez [Git](https://git-scm.com/).

Après avoir installé Git, ouvrez une invite de commandes et clonez le dépôt Git du SDK Lecteur immersif dans un dossier sur votre ordinateur.

```cmd
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Installez [Python](https://www.python.org/downloads/).

Activez la case à cocher **Add Python to PATH**.

![Boîte de dialogue d’installation de Python sur Windows, étape 1](../../media/pythoninstallone.jpg)

Ajoutez les fonctionnalités facultatives souhaitées en activant les cases à cocher correspondantes sous **Optional Features**, puis sélectionnez **Next**.

![Boîte de dialogue d’installation de Python sur Windows, étape 2](../../media/pythoninstalltwo.jpg)

Choisissez **Customize install location** et définissez le chemin d’installation sur votre dossier racine, par exemple `C:\Python37-32\`. Sélectionnez **Installer**.

![Boîte de dialogue d’installation de Python sur Windows, étape 3](../../media/pythoninstallthree.jpg)

Après avoir installé Python, ouvrez une invite de commandes et utilisez `cd` pour accéder au dossier Scripts de Python.

```cmd
cd C:\Python37-32\Scripts
```

Installez Flask.

```cmd
pip install flask
```

Installez Jinja2. Il s’agit d’un moteur de modèles complet pour Python.

```cmd
pip install jinja2
```

Installez virtualenv. Cet outil permet de créer des environnements Python isolés.

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

Créez un environnement virtuel.

```cmd
mkvirtualenv advanced-python
```

Utilisez `cd` pour accéder au dossier racine de l’exemple de projet.

```cmd
cd C:\immersive-reader-sdk\js\samples\advanced-python
```

Connectez l’exemple de projet à l’environnement. Cette action mappe l’environnement virtuel nouvellement créé au dossier racine de l’exemple de projet.

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

Le préfixe `(advanced-python)` doit avoir disparu, car l’environnement est désactivé.

Pour réactiver l’environnement, exécutez `workon advanced-python` à partir du dossier racine de l’exemple de projet.

```cmd
workon advanced-python
```

### <a name="start-the-immersive-reader-with-sample-content"></a>Démarrer le Lecteur immersif avec un exemple de contenu

Lorsque l’environnement est actif, exécutez l’exemple de projet en entrant `flask run` à partir du dossier racine de l’exemple de projet.

```cmd
flask run
```

Ouvrez votre navigateur et accédez à http://localhost:5000.

## <a name="create-a-python-web-app-on-osx"></a>Créer une application web Python sur OSX

Créez une application web Python à l’aide de `flask` sur OSX.

Installez [Git](https://git-scm.com/).

Après avoir installé Git, ouvrez le terminal et clonez le dépôt Git du SDK Lecteur immersif dans un dossier sur votre ordinateur.

```bash
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Installez [Python](https://www.python.org/downloads/).

Le dossier racine Python, par exemple `Python37-32`, doit maintenant se trouver dans le dossier Applications.

Après avoir installé Python, ouvrez le terminal et utilisez `cd` pour accéder au dossier Scripts de Python.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Installer pip.

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

Exécutez le code suivant pour installer pip pour l’utilisateur actuellement connecté afin d’éviter les problèmes d’autorisations.

```bash
python get-pip.py --user
```

```bash
sudo nano /etc/paths
```

- Entrez votre mot de passe lorsque vous y êtes invité.
- Ajoutez le chemin de votre installation pip à votre variable PATH.
- Accédez au bas du fichier, puis entrez le chemin que vous souhaitez ajouter comme dernier élément de la liste, par exemple `PATH=$PATH:/usr/local/bin`.
- Tapez **CTRL+X** pour quitter.
- Entrez **Y** pour enregistrer la mémoire tampon modifiée.
- Et voilà ! Pour effectuer un test, entrez `echo $PATH` dans une nouvelle fenêtre de terminal.

Installez Flask.

```bash
pip install flask --user
```

Installez Jinja2. Il s’agit d’un moteur de modèles complet pour Python.

```bash
pip install Jinja2 --user
```

Installez virtualenv. Cet outil permet de créer des environnements Python isolés.

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

Choisissez un dossier dans lequel vous souhaitez conserver vos environnements virtuels et exécutez cette commande :

```bash
mkdir ~/.virtualenvs
```

Utilisez `cd` pour accéder au dossier de l’exemple d’application Python du SDK Lecteur immersif.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Créez un environnement virtuel.

```bash
mkvirtualenv -p /usr/local/bin/python3 advanced-python
```

Connectez l’exemple de projet à l’environnement. Cette action mappe l’environnement virtuel nouvellement créé au dossier racine de l’exemple de projet.

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

Le préfixe `(advanced-python)` doit avoir disparu, car l’environnement est désactivé.

Pour réactiver l’environnement, exécutez `workon advanced-python` à partir du dossier racine de l’exemple de projet.

```bash
workon advanced-python
```

## <a name="start-the-immersive-reader-with-sample-content"></a>Démarrer le Lecteur immersif avec un exemple de contenu

Lorsque l’environnement est actif, exécutez l’exemple de projet en entrant `flask run` à partir du dossier racine de l’exemple de projet.

```bash
flask run
```

Ouvrez votre navigateur et accédez à http://localhost:5000.

## <a name="next-steps"></a>Étapes suivantes

* Explorer le [SDK Lecteur Immersif](https://github.com/microsoft/immersive-reader-sdk) et la [référence du SDK Lecteur immersif](../../reference.md)
* Voir des exemples de code sur [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)
