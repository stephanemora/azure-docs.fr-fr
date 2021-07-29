---
title: Vue d’ensemble du Dev Tools Pack Installer d’Azure Percept
description: Découvrir comment utiliser le Dev Tools Pack Installer pour accélérer le développement avancé avec Azure Percept
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: 49d5a6ed7b23169e655a02cdbb14df52a6e82231
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110099782"
---
# <a name="dev-tools-pack-installer-overview"></a>Vue d’ensemble du Dev Tools Pack Installer

Le Dev Tools Pack Installer est une solution tout-en-un qui installe et configure tous les outils nécessaires au développement d’une solution avancée de périphérie intelligente.

## <a name="mandatory-tools"></a>Outils obligatoires

* [Visual Studio Code](https://code.visualstudio.com/)
* [Python 3.6 ou version ultérieure](https://www.python.org/)
* [Docker 20.10](https://www.docker.com/)
* [PIP3 21.1](https://pip.pypa.io/en/stable/user_guide/)
* [TensorFlow 2.0](https://www.tensorflow.org/)
* [Azure Machine Learning SDK 1.2](/python/api/overview/azure/ml/)

## <a name="optional-tools"></a>Outils facultatifs

* [NVIDIA DeepStream SDK 5](https://developer.nvidia.com/deepstream-sdk) (Toolkit de développement de solutions pour accélérateurs NVIDIA)
* [Intel OpenVINO Toolkit 2021.3](https://docs.openvinotoolkit.org/) (Toolkit de développement de solutions pour accélérateurs Intel)
* [Lobe.ai 0.9](https://lobe.ai/)  
* [Streamlit 0.8](https://www.streamlit.io/)
* [Pytorch 1.4.0 (Windows) ou 1.2.0 (Linux)](https://pytorch.org/)
* [Miniconda 4.5](https://docs.conda.io/en/latest/miniconda.html)
* [Chainer 7.7](https://chainer.org/)
* [Caffe 1.0](https://caffe.berkeleyvision.org/)
* [CUDA Toolkit 11.2](https://developer.nvidia.com/cuda-toolkit)
* [Microsoft Cognitive Toolkit 2.5.1](https://www.microsoft.com/research/product/cognitive-toolkit/?lang=fr_ca)

## <a name="known-issues"></a>Problèmes connus

- Les installations Caffe, NVIDIA DeepStream SDK et Intel OpenVINO Toolkit facultatives peuvent échouer si la Docker ne fonctionne pas correctement. Pour installer ces outils facultatifs, assurez-vous que Docker est installé et en cours d’exécution avant d’effectuer les installations via le Dev Tools Pack Installer.

- Le Toolkit CUDA facultatif installé sur Mac a la version 10.0.130. CUDA Toolkit 11 ne prend plus en charge le développement ou l’exécution d’applications sur macOSity.

## <a name="docker-minimum-requirements"></a>Configuration minimale requise pour Docker

### <a name="windows"></a>Windows

- Windows 10 64 bits : Professionnel, Entreprise ou Éducation (build 16299 ou ultérieure).

- Les fonctionnalités Windows Hyper-V et Conteneurs doivent être activées. Vous devez disposer de la configuration matérielle suivante pour exécuter Hyper-V sur Windows 10 :

    - Processeur 64 bits avec [traduction d’adresses de deuxième niveau (SLAT)](https://en.wikipedia.org/wiki/Second_Level_Address_Translation)
    - RAM système de 4 Go
    - La prise en charge de la virtualisation matérielle au niveau du BIOS doit être activée dans les paramètres du BIOS. Pour plus d’informations, consultez Virtualisation.

> [!NOTE]
> Docker prend en charge Docker Desktop sur Windows selon le cycle de vie du support de Microsoft pour le système d’exploitation Windows 10. Pour plus d’informations, consultez la [fiche d’information sur le cycle de vie Windows](https://support.microsoft.com/help/13853/windows-lifecycle-fact-sheet).

En savoir plus sur [l’installation de Docker Desktop sur Windows](https://docs.docker.com/docker-for-windows/install/#install-docker-desktop-on-windows).

### <a name="mac"></a>Mac

- L’ordinateur Mac doit être un modèle 2010 ou plus récent avec les attributs suivants :
    - Processeur Intel
    - Prise en charge matérielle par Intel de la virtualisation de l’unité de gestion mémoire (MMU), notamment Extended Page Tables (EPT) et le mode non restreint. Pour vérifier si votre ordinateur répond à cette exigence, exécutez la commande suivante dans un terminal : ```sysctl kern.hv_support```. Si votre Mac prend en charge l’infrastructure Hypervisor, la commande imprime ```kern.hv_support: 1```.

- macOS version 10.14 ou ultérieure (Mojave, Catalina ou Big Sur). Nous vous recommandons d’effectuer une mise à niveau vers la dernière version de macOS. Si vous rencontrez des problèmes après la mise à niveau de macOS vers la version 10.15, vous devez installer la version la plus récente de Docker Desktop compatible avec cette version de macOS.

- Au moins 4 Go de RAM.

- N’installez PAS de version de VirtualBox antérieure à la version 4.3.30. Celles-ci ne sont pas compatibles avec Docker Desktop.

- Le programme d’installation n’est pas pris en charge sur Apple M1.

En savoir plus sur [l’installation de Docker Desktop sur Mac](https://docs.docker.com/docker-for-mac/install/#system-requirements).

## <a name="launch-the-installer"></a>Exécutez le programme d’installation

Téléchargez le Dev Tools Pack Installer pour [Windows](https://go.microsoft.com/fwlink/?linkid=2132187), [Linux](https://go.microsoft.com/fwlink/?linkid=2132186) ou [Mac](https://go.microsoft.com/fwlink/?linkid=2132296). Lancez le programme d’installation selon votre plateforme, comme décrit ci-dessous.

### <a name="windows"></a>Windows

1. Cliquez sur **Dev-Tools-Pack-Installer** pour ouvrir l’Assistant Installation.

### <a name="mac"></a>Mac

1. Après le téléchargement, déplacez le fichier **Dev-Tools-Pack-Installer.app** dans le dossier **Applications**.

1. Cliquez sur **Dev-Tools-Pack-Installer.app** pour ouvrir l’Assistant Installation.

1. Si vous recevez une boîte de dialogue de sécurité « développeur non identifié » :

    1. Accédez à **Préférences système** -> **Sécurité et confidentialité** -> **Général**, puis cliquez sur **Ouvrir quand même** à côté de **Dev-Tools-Pack-Installer.app**.
    1. Cliquez sur l’icône en forme d’électron.
    1. Cliquez sur **Ouvrir** dans la boîte de dialogue de sécurité.

### <a name="linux"></a>Linux

1. Quand le navigateur vous y invite, cliquez sur **Enregistrer** pour terminer le téléchargement du programme d’installation.

1. Ajoutez des autorisations d’exécution au fichier **.appimage** :

    1. Ouvrez un terminal Linux.

    1. Entrez la commande suivante dans le terminal pour accéder au dossier **Téléchargements** :

        ```bash
        cd ~/Downloads/
        ```

    1. Rendez l’AppImage exécutable :

        ```bash
        chmod +x Dev-Tools-Pack-Installer.AppImage
        ```

    1. Exécutez le programme d’installation :

        ```bash
        ./Dev-Tools-Pack-Installer.AppImage
        ```

1. Ajoutez des autorisations d’exécution au fichier **.appimage** :

    1. Cliquez avec le bouton droit sur le fichier .appimage et sélectionnez **Propriétés**.
    1. Ouvrez l’onglet **Autorisations**.
    1. Cochez la case à côté de l’option **Autoriser l’exécution du fichier en tant que programme**.
    1. Fermez **Propriétés** et ouvrez le fichier **.appimage**.

## <a name="run-the-installer"></a>Exécution du programme d’installation

1. Dans la page **Install Dev Tools Pack Installer**, cliquez sur **Afficher la licence** pour afficher les contrats de licence de chaque package logiciel inclus dans le programme d’installation. Si vous acceptez les termes du contrat de licence, cochez la case et cliquez sur **Suivant**.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-license-agreements.png" alt-text="Écran du contrat de licence dans le programme d’installation.":::

1. Cliquez sur **Déclaration de confidentialité** pour passer en revue la déclaration de confidentialité de Microsoft. Si vous acceptez les termes de la déclaration de confidentialité et que vous souhaitez envoyer des données de diagnostic à Microsoft, sélectionnez **Oui**, puis cliquez sur **Suivant**. Sinon, sélectionnez **Non**, puis cliquez sur **Suivant**.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-privacy-statement.png" alt-text="Écran de la déclaration de confidentialité dans le programme d’installation.":::

1. Dans la page **Configurer les composants**, sélectionnez les outils facultatifs que vous souhaitez installer (les outils obligatoires sont installés par défaut).

    1. Si vous utilisez le SoM Azure Percept Audio, qui fait partie du DK Azure Percept, veillez à installer Intel OpenVino Toolkit et Miniconda3.

    1. Cliquez sur **Installer** pour poursuivre l’installation.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-configure-components.png" alt-text="Écran du programme d’installation montrant les packages logiciels disponibles.":::

1. Une fois l’installation de tous les composants sélectionnés terminée, l’Assistant passe à la page **Fin de l’Assistant Installation**. Cliquez sur **Terminer** pour quitter le programme d’installation.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-finish.png" alt-text="Écran de fin du programme d’installation.":::

## <a name="docker-status-check"></a>Vérification de l’état de Docker

Si le programme d’installation vous demande de vérifier l’état de fonctionnement de Docker Desktop, effectuez les étapes suivantes :

### <a name="windows"></a>Windows

1. Développez les icônes masquées de la barre d’état système.

    :::image type="content" source="./media/dev-tools-installer/system-tray.png" alt-text="Barre d’état système.":::

1. Vérifiez que l’icône Docker Desktop indique **Docker Desktop est en cours d’exécution**.

    :::image type="content" source="./media/dev-tools-installer/docker-status-running.png" alt-text="État de Docker.":::

1. Si vous ne voyez pas l’icône ci-dessus indiquée dans la barre d’état système, lancez Docker Desktop à partir du menu Démarrer.

1. Si Docker vous invite à redémarrer, il est recommandé de fermer le programme d’installation et de le relancer une fois que le redémarrage est terminé et que Docker est dans un état d’exécution. Toutes les applications tierces installées doivent être détectées (elles ne sont pas réinstallées automatiquement).

## <a name="next-steps"></a>Étapes suivantes

Consultez le [référentiel de développement avancé Azure Percept](https://github.com/microsoft/azure-percept-advanced-development) pour passer au développement avancé avec Azure Percept DK.
