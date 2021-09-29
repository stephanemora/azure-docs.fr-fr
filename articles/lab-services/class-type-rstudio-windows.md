---
title: Configurer un laboratoire avec R et RStudio sur Windows à l’aide d’Azure Lab Services
description: Découvrez comment configurer des laboratoires pour enseigner R à l’aide de RStudio sur Windows
author: emaher
ms.topic: article
ms.date: 08/26/2021
ms.author: enewman
ms.openlocfilehash: 5d081e21723fa73d84beec12e8602234ad5e8074
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124757379"
---
# <a name="set-up-a-lab-to-teach-r-on-windows"></a>Configurer un laboratoire pour enseigner R sur Windows

[R](https://www.r-project.org/about.html) est un langage open source utilisé pour le calcul statistique et les graphiques.  Il est utilisé à des fins d’analyse statistique, de la génétique à l’analyse des données financières, en passant par le traitement du langage naturel.  Le langage R offre une expérience de [ligne de commande interactive](https://cran.r-project.org/doc/manuals/r-release/R-intro.html#Invoking-R-from-the-command-line).  [RStudio](https://www.rstudio.com/products/rstudio/) est un environnement de développement intégré (IDE) interactif disponible pour le langage R.  La version gratuite fournit des outils d’édition de code, une expérience de débogage intégrée et des outils de développement de package.

Cet article se concentre uniquement sur RStudio et R en tant que bloc de construction pour une classe nécessitant l’utilisation du calcul statistique.  Les types de classes [Deep Learning](class-type-deep-learning-natural-language-processing.md) et [Python et Jupyter Notebooks](class-type-jupyter-notebook.md) configurent RStudio différemment.  Chaque article décrit comment utiliser l’image du marketplace [Data Science Virtual Machine pour Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-US/marketplace/apps/microsoft-dsvm.ubuntu-1804) qui comprend de nombreux [outils liés à la science des données](../machine-learning/data-science-virtual-machine/tools-included.md), dont RStudio, préinstallés.  

## <a name="lab-account-configuration"></a>Configuration de compte de laboratoire

Pour configurer ce labo, vous avez besoin d’un abonnement Azure et d’un compte Lab pour commencer. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer. Une fois que vous disposez d’un abonnement Azure, vous pouvez créer un compte lab dans Azure Lab Services. Pour plus d’informations sur la création d’un compte de laboratoire, consultez notre tutoriel sur [la configuration d’un compte de labo](./tutorial-setup-lab-account.md). Vous pouvez également utiliser un compte Lab existant.

### <a name="lab-account-settings"></a>Paramètres du compte Lab

Activez les paramètres de votre compte Lab (cf. tableau suivant).

| Paramètres du compte lab | Instructions |
| -------------------- | ----- |
| [Activer un réseau virtuel pair](how-to-connect-peer-virtual-network.md) | Activez si :<ul><li>La classe requiert un R Server partagé.</li><li>La classe requiert des fichiers de données volumineux que vous souhaitez stocker en externe, non sur la machine virtuelle de l’étudiant.</li></ul> |

> [!IMPORTANT]
> Si vous choisissez d’activer un réseau virtuel pair, vous devez le faire avant la création du laboratoire.

## <a name="lab-configuration"></a>Configuration du laboratoire

Pour obtenir des instructions sur la création d’un laboratoire et l’application des paramètres nécessaires, consultez [Tutoriel : Configurer un laboratoire de classe](tutorial-setup-classroom-lab.md).  Lors de la création du laboratoire, appliquez les paramètres suivants :

| Paramètre de labo | Valeur et description |
| ------------ | ------------------ |
| Taille de la machine virtuelle | GPU de petite taille (calcul)|
| Image de machine virtuelle | Windows 10 Pro. Version 2004 |

## <a name="external-resource-configuration"></a>Configuration de ressource externe

Certaines classes requièrent que des fichiers, tels que des fichiers de données volumineux, soient stockés en externe.  Pour plus d’informations sur les options et des instructions d’installation, consultez [Utiliser le stockage de fichiers externes dans Azure Lab Services](how-to-attach-external-storage.md).

Si vous optez pour un RStudio Server partagé pour les étudiants, le serveur doit être configuré avant la création du laboratoire.  Pour plus d’informations sur la configuration d’un serveur partagé, consultez [Guide pratique pour créer un labo avec une ressource partagée dans Azure Lab Services](how-to-create-a-lab-with-shared-resource.md).  Pour obtenir des instructions sur la création d’un RStudio Server, consultez [Télécharger RStudio Server pour Debian et Ubuntu](https://www.rstudio.com/products/rstudio/download-server/debian-ubuntu/) et [Accès à RStudio Server open source](https://support.rstudio.com/hc/en-us/articles/200552306-Getting-Started).

## <a name="template-configuration"></a>Configuration du modèle

Une fois la machine modèle créée, démarrez-la et connectez-vous à celle-ci pour [installer R](https://docs.rstudio.com/resources/install-r/) et [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/).  

### <a name="install-r"></a>Installer R

1. Téléchargez le [programme d’installation le plus récent pour R pour Windows](https://cran.r-project.org/bin/windows/base/release.html).  Pour obtenir la liste complète des versions disponibles, consultez la [page de téléchargement de R pour Windows](https://cran.r-project.org/bin/windows/base/).
2. Exécutez le programme d’installation.
    1. Dans l’invite **Select Setup Language (Sélectionner le langage d’installation)** , choisissez le langage souhaité et sélectionnez **OK**.
    2. Dans la page **Informations** du programme d’installation, lisez le contrat de licence.  Sélectionnez **Next (Suivant)** pour accepter le contrat et continuer.
    3. Dans la page **Select Destination Location (Sélectionner l’emplacement de destination)** , acceptez l’emplacement d’installation par défaut et sélectionnez **Next (Suivant)** .
    4. Dans la page **Select Components (Sélectionner des composants)** , vous pouvez éventuellement désactiver l’option **Fichiers 32 bits**.  Pour plus d’informations sur l’exécution des versions 32 bits et 62 bits de R, consultez la question fréquente [Est-il possible d’installer les versions 32 bits et 64 bits de R sur le même ordinateur ?](https://cran.r-project.org/bin/windows/base/rw-FAQ.html#Can-both-32_002d-and-64_002dbit-R-be-installed-on-the-same-machine_003f) du Forum aux Questions.
    5. Dans la page **Startup options (Options de démarrage)** , laissez l’option de démarrage définie sur **No (accept defaults) (Non (accepter les valeurs par défaut))** .  Si vous souhaitez que l’interface utilisateur graphique R utilise des fenêtres distinctes (SDI) ou de texte brut, choisissez la case d’option **Yes (customize startup) (Oui (personnaliser le démarrage))** et modifiez les options de démarrage dans les pages suivantes de l’Assistant.
    6. Dans la page **Sélectionner le dossier Menu Démarrer**, sélectionnez **Suivant**.
    7. Dans la page **Select Additional Tasks (Sélectionner des tâches supplémentaires)** , vous pouvez éventuellement sélectionner **Create a desktop shortcut (Créer un raccourci sur le bureau)** .  Sélectionnez **Suivant**.
    8. Dans la page **Installing (Installation)** , attendez que l’installation se termine.
    9. Dans la page **Completing the R for Windows (Finalisation de l’installation de R pour Windows)** , sélectionnez **Finish (Terminer)** .

Vous pouvez également exécuter l’installation de R à l’aide de PowerShell.  L’exemple de code montre comment installer R sans le composant 32 bits et ajoute une icône de bureau pour la version la plus récente de R. Pour obtenir la liste complète des options de ligne de commande pour le programme d’installation, consultez [Paramètres de ligne de commande du programme d’installation](https://jrsoftware.org/ishelp/index.php?topic=setupcmdline).

```powershell
#Avoid prompt to setup Internet Explorer if we must parse download page
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Internet Explorer\Main" -Name "DisableFirstRunCustomize" -Value 2

$outputfile = "R-win.exe"

$result = Invoke-WebRequest "https://cran.r-project.org/bin/windows/base/release.html" -OutFile $outputfile -PassThru

#Check if we need to parse the result ourselves, to find the latest version of R
if ($result.StatusCode -eq '200' -and $result.Headers["Content-Type"] -eq 'text/html')
{
    $metaTag = $result.ParsedHtml.Head.children | Where-Object {$_.nodeName -eq 'META'}
    if ($metaTag.content  -match "R-\d+\.\d+\.\d+-win.exe"){
        $outputfile = $Matches.0

        #Download latest version
        Invoke-WebRequest "https://cran.r-project.org/bin/windows/base/$outputfile" -OutFile $outputfile
    }else{
        Write-Error "Unable to find latest version of R installer.  Go to https://cran.r-project.org/bin/windows/base/release.html to download manually."
    }
}

#Install Silently
$installPath = Get-Item -Path $outputfile
Start-Process -FilePath $installPath.FullName -ArgumentList "/VERYSILENT /LOG=r-install.log /NORESTART /COMPONENTS=""main,x64,translations"" /MERGETASKS=""desktopicon"" /LANG=""en""" -NoNewWindow -Wait
```

### <a name="install-rstudio"></a>Installer RStudio

Maintenant que R est installé localement, vous pouvez installer l’environnement de développement intégré (IDE) RStudio.  Nous allons installer la version gratuite de RStudio Desktop.  Pour découvrir toutes les versions disponibles, consultez [Téléchargements RStudio](https://www.rstudio.com/products/rstudio/download/).

1. Téléchargez le [programme d’installation pour R Studio pour Windows 10](https://www.rstudio.com/products/rstudio/download/#download).  Le fichier du programme d’installation est au format `rstudio-{version}.exe`.  
2. Exécutez le programme d’installation de RStudio.
    1. Dans la page **Welcome to RStudio Setup (Bienvenue sur la configuration de RStudio)** de l’Assistant **RStudio Setup (Configuration de RStudio)** , sélectionnez **Next (Suivant)** .
    2. Dans la page **Choose Install Location (Choisir l’emplacement d’installation)** , sélectionnez **Next (Suivant)** .
    3. Dans la page **Choose Start Menu Folder** (Choisir le dossier du menu Démarrer), sélectionnez **Installer**.
    4. Dans la page **Installing (Installation)** , attendez que l’installation se termine.
    5. Dans la page **Completing RStudio Setup (Finalisation de l’installation de RStudio)** , sélectionnez **Finish (Terminer)** .

Pour exécuter les étapes d’installation de RStudio à l’aide de PowerShell, exécutez les commandes suivantes.  Consultez [Téléchargements RStudio](https://www.rstudio.com/products/rstudio/download/) pour vérifier que la version de RStudio est disponible avant d’exécuter les commandes.

```powershell
$rstudiover="1.4.1717"
$outputfile = "RStudio-$rstudiover.exe"

#Download installer executable
Invoke-WebRequest "https://download1.rstudio.org/desktop/windows/RStudio-$rstudiover.exe" -OutFile $outputfile

#Install RStudio silently
$installPath = Get-Item -Path $outputfile
Start-Process -FilePath $installPath.FullName -ArgumentList "/S" -NoNewWindow -Wait
```

### <a name="cran-packages"></a>Packages CRAN

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