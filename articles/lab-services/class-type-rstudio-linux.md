---
title: Configurer un laboratoire avec R et RStudio sur Linux à l’aide d’Azure Lab Services
description: Découvrez comment configurer des laboratoires pour enseigner R à l’aide de RStudio sur Linux
author: emaher
ms.topic: article
ms.date: 08/25/2021
ms.author: enewman
ms.openlocfilehash: 14e30572ab620b36730c725c7ac5a2e762947366
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124755507"
---
# <a name="set-up-a-lab-to-teach-r-on-linux"></a>Configurer un laboratoire pour enseigner R sur Linux

[R](https://www.r-project.org/about.html) est un langage open source utilisé pour le calcul statistique et les graphiques.  Il est utilisé à des fins d’analyse statistique, de la génétique à l’analyse des données financières, en passant par le traitement du langage naturel.  Le langage R offre une expérience de [ligne de commande interactive](https://cran.r-project.org/doc/manuals/r-release/R-intro.html#Invoking-R-from-the-command-line).  [RStudio](https://www.rstudio.com/products/rstudio/) est un environnement de développement intégré (IDE) interactif disponible pour le langage R.  La version gratuite fournit des outils d’édition de code, une expérience de débogage intégrée et des outils de développement de package.

Cet article se concentre uniquement sur RStudio et R en tant que bloc de construction pour une classe nécessitant l’utilisation du calcul statistique.  Les types de classes [Deep Learning](class-type-deep-learning-natural-language-processing.md) et [Python et Jupyter Notebooks](class-type-jupyter-notebook.md) configurent RStudio différemment.  Chaque article décrit comment utiliser l’image du marketplace [Data Science Virtual Machine pour Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-US/marketplace/apps/microsoft-dsvm.ubuntu-1804) qui comprend de nombreux [outils liés à la science des données](../machine-learning/data-science-virtual-machine/tools-included.md), dont RStudio, préinstallés.  

## <a name="lab-account-configuration"></a>Configuration de compte de laboratoire

Pour configurer ce labo, vous avez besoin d’un abonnement Azure et d’un compte Lab pour commencer. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer. Une fois que vous disposez d’un abonnement Azure, vous pouvez créer un compte lab dans Azure Lab Services. Pour plus d’informations sur la création d’un compte de laboratoire, consultez notre tutoriel sur [la configuration d’un compte de labo](./tutorial-setup-lab-account.md). Vous pouvez également utiliser un compte Lab existant.

### <a name="lab-account-settings"></a>Paramètres du compte Lab

Activez les paramètres de votre compte Lab (cf. tableau suivant). Pour plus d’informations sur l’activation des images de la Place de marché Azure, consultez [Spécification des images de la Place de marché Azure accessibles aux créateurs d’instances Lab](./specify-marketplace-images.md).

| Paramètres du compte lab | Instructions |
| -------------------- | ----- |
| Images de la Place de marché | Ubuntu Server 18.04 LTS |
| [Activer un réseau virtuel pair](how-to-connect-peer-virtual-network.md) | Activez si :<ul><li>La classe requiert un R Server partagé.</li><li>La classe requiert des fichiers de données volumineux que vous souhaitez stocker en externe, non sur la machine virtuelle de l’étudiant.</li></ul> |

> [!IMPORTANT]
> Si vous choisissez d’activer un réseau virtuel pair, vous devez le faire avant la création du laboratoire.

## <a name="lab-configuration"></a>Configuration du laboratoire

Pour obtenir des instructions sur la création d’un laboratoire et l’application des paramètres nécessaires, consultez [Tutoriel : Configurer un laboratoire de classe](tutorial-setup-classroom-lab.md).  Lors de la création du laboratoire, appliquez les paramètres suivants :

| Paramètre de labo | Valeur et description |
| ------------ | ------------------ |
| Taille de la machine virtuelle | GPU de petite taille (calcul)|
| Image de machine virtuelle | Ubuntu Server 18.04 LTS |
| Activer la connexion Bureau à distance | Vous ne devez activer ce paramètre que si vous choisissez d’utiliser le protocole RDP.  Ce paramètre n’est pas nécessaire si vous choisissez [X2Go pour vous connecter à des machines de laboratoire](how-to-use-remote-desktop-linux-student.md).  Vous devez vous connecter à la machine virtuelle Linux à l’aide du protocole SSH la première fois, puis installer les packages RDP/X2Go et GUI.  Pour plus d’informations, consultez [Utiliser le Bureau à distance graphique pour des machines virtuelles Linux](how-to-enable-remote-desktop-linux.md). |

## <a name="external-resource-configuration"></a>Configuration de ressource externe

Certaines classes requièrent que des fichiers, tels que des fichiers de données volumineux, soient stockés en externe.  Pour plus d’informations sur les options et des instructions d’installation, consultez [Utiliser le stockage de fichiers externes dans Azure Lab Services](how-to-attach-external-storage.md).

Si vous optez pour un R Server partagé pour les étudiants, le serveur doit être configuré avant la création du laboratoire.  Pour plus d’informations sur la configuration d’un serveur partagé, consultez [Guide pratique pour créer un labo avec une ressource partagée dans Azure Lab Services](how-to-create-a-lab-with-shared-resource.md).  Pour obtenir des instructions sur la création d’un RStudio Server, consultez [Télécharger RStudio Server pour Debian et Ubuntu](https://www.rstudio.com/products/rstudio/download-server/debian-ubuntu/) et [Accès à RStudio Server open source](https://support.rstudio.com/hc/en-us/articles/200552306-Getting-Started).

## <a name="template-configuration"></a>Configuration du modèle

Une fois la machine modèle créée, démarrez-la et connectez-vous à celle-ci pour [installer R](https://docs.rstudio.com/resources/install-r/), [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) et éventuellement [X2Go Server](https://wiki.x2go.org/doku.php/doc:installation:x2goserver).  

Commençons pas mettre à jour apt et mettre à niveau les packages existants sur la machine.

```bash
sudo apt update 
sudo apt upgrade
```

### <a name="install-x2go-server"></a>Installer X2Go Server

Si vous choisissez d’utiliser X2Go, [installez le serveur](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/X2GoRemoteDesktop#install-x2go-server).  Vous devez commencer par vous [connecter à l’aide du protocole ssh](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-ssh) pour installer le composant serveur.  Cela fait, vous pouvez effectuer le reste de la configuration après [connexion à l’aide du client X2Go](how-to-use-remote-desktop-linux-student.md).

L’installation par défaut de X2Go n’est pas compatible avec RStudio.  Pour contourner ce problème, mettez à jour le fichier d’options x2goagent.

1. Modifiez le fichier `/etc/x2go/x2goagent.options`.  N’oubliez pas de modifier le fichier en tant que sudo.
    1. Supprimez le commentaire de la ligne : `X2GO_NXAGENT_DEFAULT_OPTIONS+=" -extension GLX"`
    1. Commentez la ligne : `X2GO_NXAGENT_DEFAULT_OPTIONS+=" -extension GLX"`
2. Redémarrez le serveur X2Go de façon à utiliser les nouvelles options.

    ```bash
    sudo systemctl restart x2goserver
    ```

Vous pouvez également générer les bibliothèques requises en suivant les instructions fournies dans [Glx Xlib workaround for X2Go](https://wiki.x2go.org/doku.php/wiki:development:glx-xlib-workaround).

### <a name="install-r"></a>Installer R

Il existe plusieurs façons d’installer R sur la machine virtuelle.  Nous allons installer R à partir du référentiel Comprehensive R Archive Network (CRAN).  Il fournit les versions les plus récentes de R. Une fois que ce référentiel ajouté à notre machine, vous pouvez installer R et de nombreux autres packages associés.

Nous devons ajouter le référentiel CRAN. Les commandes sont modifiées conformément aux [instructions rapides concernant les packages Ubuntu pour R](https://cran.rstudio.com/bin/linux/ubuntu/).

```bash
#download helper packages
sudo apt install --no-install-recommends software-properties-common dirmngr
# download and add the signing key (by Michael Rutter) for these repos
sudo wget -q "https://cloud.r-project.org/bin/linux/ubuntu/marutter_pubkey.asc" -O /etc/apt/trusted.gpg.d/cran_ubuntu_key.asc
#add repository
sudo add-apt-repository "deb https://cloud.r-project.org/bin/linux/ubuntu bionic-cran40/"
```

Vous pouvez maintenant installer R en exécutant la commande suivante :

```bash
sudo apt install r-base
```

### <a name="install-rstudio"></a>Installer RStudio

Maintenant que R est installé localement, vous pouvez installer l’environnement de développement intégré (IDE) RStudio.  Nous allons installer la version gratuite de RStudio Desktop.  Pour découvrir toutes les versions disponibles, consultez [Téléchargements RStudio](https://www.rstudio.com/products/rstudio/download/).

1. [Importez la clé de signature de code](https://www.rstudio.com/code-signing/) pour RStudio.

    ```bash
    sudo gpg --keyserver keyserver.ubuntu.com  --recv-keys 3F32EE77E331692F
    ```

2. Téléchargez le fichier [Debian Linux Package file (.deb) for R Studio](https://www.rstudio.com/products/rstudio/download/#download) pour Ubuntu.  Le fichier sera au format suivant `rstudio-{version}-amd64.deb`.  Par exemple :

    ```bash
    export rstudiover="1.4.1717"
    wget --quiet -O rstudio.deb https://download1.rstudio.org/desktop/bionic/amd64/rstudio-$rstudiover-amd64.deb
    ```

3. Utilisez gdebi pour installer RStudio.   Veillez à utiliser le chemin d’accès au fichier pour indiquer à apt que vous installez un fichier local.

    ```bash
    sudo apt install gdebi-core 
    echo "y" | gdebi rstudio.deb –quiet
    ```

### <a name="cran-packages"></a>Packages CRAN

Il est maintenant temps d’installer tous les [packages cran](https://cloud.r-project.org/web/packages/available_packages_by_name.html) souhaités.  Commencez par ajouter le [référentiel actuel R 4.0 ou ’c2d4u’ ultérieur](https://cran.rstudio.com/bin/linux/ubuntu/#get-5000-cran-packages).

```bash
sudo add-apt-repository ppa:c2d4u.team/c2d4u4.0+
```

Utilisez la commande `install.packages(“package name”)` dans une session R interactive, comme indiqué dans l’article [quick list of useful R packages](https://support.rstudio.com/hc/articles/201057987-Quick-list-of-useful-R-packages).  Vous pouvez également utiliser l’option de menu Tools -> Install Packages dans RStudio.

Si vous avez besoin d’aide pour trouver un package, consultez la [liste des packages par tâche](https://cran.r-project.org/web/views/) ou la [liste alphabétique des packages](https://cloud.r-project.org/web/packages/available_packages_by_name.html).

## <a name="cost"></a>Coût

Penchons-nous sur un exemple d’estimation du coût pour ce cours.  Supposons que vous ayez une classe de 25 élèves. Chaque élève dispose de 20 heures de cours programmées.  Chaque élève dispose également d'un quota de 10 heures pour les devoirs et activités à la maison en dehors des heures de cours programmées.  La taille de machine virtuelle que nous avons choisie était **GPU de petite taille (calcul)** , soit 139 unités de laboratoire.

25 étudiants &times; (20 heures planifiées + 10 heures de quota) &times; 139 unités Lab &times; 0,01 USD par heure = 1042,5 USD

> [!IMPORTANT]
> L’estimation du coût est fournie à titre d’exemple uniquement.  Pour connaître les tarifs actuels, consultez [Tarifs Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="next-steps"></a>Étapes suivantes

L’image du modèle peut à présent être publiée dans le labo. Pour plus d'informations, consultez [Publier le modèle de machine virtuelle](how-to-create-manage-template.md#publish-the-template-vm).

Au fil de la configuration de votre instance Lab, consultez les articles suivants :

- [Ajout d'utilisateurs](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Définir les quotas](how-to-configure-student-usage.md#set-quotas-for-users)
- [Définir un calendrier](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Envoyer par mail des liens d’inscription aux étudiants](how-to-configure-student-usage.md#send-invitations-to-users)