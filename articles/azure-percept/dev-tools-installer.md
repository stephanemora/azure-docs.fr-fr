---
title: Vue d’ensemble du Dev Tools Pack Installer d’Azure Percept
description: Découvrir comment utiliser le Dev Tools Pack Installer pour accélérer le développement avancé avec Azure Percept
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: d684311ef959ac13f3be8bac7ffbbb06a741962a
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097701"
---
# <a name="dev-tools-pack-installer-overview"></a>Vue d’ensemble du Dev Tools Pack Installer

Le Dev Tools Pack Installer est une solution tout-en-un qui installe et configure tous les outils nécessaires au développement d’une solution de périphérie intelligente. Si vous avez déjà installé l’un des packages logiciels listés ci-dessous, le Dev Tools Pack Installer réinstalle ces packages pour que vos outils soient cohérents avec les versions logicielles du programme d’installation.

## <a name="mandatory-tools-installed"></a>Outils obligatoires installés

* [Visual Studio Code](https://code.visualstudio.com/)
* [Python 3.6 (Windows) ou 3.5 (Linux)](https://www.python.org/)
* [Docker 19.03](https://www.docker.com/)
* [PIP3](https://pip.pypa.io/en/stable/user_guide/)
* [TensorFlow 1.13](https://www.tensorflow.org/)
* [Azure Machine Learning SDK 1.1](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py)

## <a name="optional-tools-available-for-installation"></a>Outils facultatifs disponibles pour l’installation

* [Nvidia DeepStream SDK 5](https://developer.nvidia.com/deepstream-sdk) (Toolkit de développement de solutions pour accélérateurs Nvidia)
* [Intel OpenVino Toolkit 2020 2](https://docs.openvinotoolkit.org/) (Toolkit de développement de solutions pour accélérateurs Intel)
* [Lobe.ai](https://lobe.ai/)  
* [Streamlit](https://www.streamlit.io/)
* [Pytorch 1.4.0 (Windows) ou 1.2.0 (Linux)](https://pytorch.org/)
* [Miniconda3](https://docs.conda.io/en/latest/miniconda.html)
* [Chainer 5.2](https://chainer.org/)
* [Caffe](https://caffe.berkeleyvision.org/)
* [CUDA Toolkit 10.0.130](https://developer.nvidia.com/cuda-toolkit)
* [Microsoft Cognitive Toolkit 2.5.1](https://www.microsoft.com/research/product/cognitive-toolkit/?lang=fr_ca)

## <a name="known-issues"></a>Problèmes connus

- L’installation facultative de Caffe peut échouer si Docker ne s’exécute pas correctement sur le système. Si vous souhaitez installer Caffe, vérifiez que Docker est installé et en cours d’exécution avant de tenter l’installation de Caffe par le biais du Dev Tools Pack Installer. 

- L’installation de CUDA facultative échoue sur les systèmes incompatibles. Avant de tenter d’installer le [CUDA Toolkit 10.0.130](https://developer.nvidia.com/cuda-toolkit) par le biais du Dev Tools Pack Installer, vérifiez la compatibilité de votre système.

## <a name="minimum-requirements"></a>Configuration minimale requise

* Configuration minimale requise pour Docker :

    * Windows :
        * https://docs.docker.com/docker-for-windows/install/#system-requirements

        - Windows 10 64 bits : Professionnel, Entreprise ou Éducation (build 16299 ou ultérieure).

             Pour Windows 10 Famille, consultez Installer Docker Desktop sur Windows Famille.
           - Les fonctionnalités Windows Hyper-V et Conteneurs doivent être activées.
           - Vous devez disposer de la configuration matérielle suivante pour exécuter Client Hyper-V sur Windows 10 :

              - Processeur 64 bits avec [traduction d’adresses de deuxième niveau (SLAT)](https://en.wikipedia.org/wiki/Second_Level_Address_Translation)
              - 4 Go de RAM système
              - La prise en charge de la virtualisation matérielle au niveau du BIOS doit être activée dans les paramètres du BIOS. Pour plus d’informations, consultez Virtualisation.

        > [!NOTE]
        > Docker prend en charge Docker Desktop sur Windows selon le cycle de vie du support de Microsoft pour le système d’exploitation Windows 10. Pour plus d’informations, consultez la [fiche d’information sur le cycle de vie Windows](https://support.microsoft.com/help/13853/windows-lifecycle-fact-sheet).

    * Mac :
        * https://docs.docker.com/docker-for-mac/install/#system-requirements
       
        Pour installer Docker Desktop sur un Mac, celui-ci doit répondre aux exigences suivantes :
         
         - Au niveau matériel, votre **Mac doit être un modèle 2010 ou plus récent avec un processeur Intel**. Il doit également prndre en charge le matériel Intel pour la virtualisation de l’unité de gestion mémoire (MMU), notamment les tables de pages étendues (EPT) et le mode non restreint. Pour vérifier si votre ordinateur répond à cette exigence, exécutez la commande suivante dans un terminal : ```sysctl kern.hv_support```

        Si votre Mac prend en charge l’infrastructure Hypervisor, la commande imprime ```kern.hv_support: 1```.

         - Votre Mac doit exécuter **macOS version 10.14 ou ultérieure** (autrement dit, Mojave, Catalina ou Big Sur). Nous vous recommandons d’effectuer une mise à niveau vers la dernière version de macOS.

        Si vous rencontrez des problèmes après la mise à niveau de macOS vers la version 10.15, vous devez installer la version la plus récente de Docker Desktop compatible avec cette version de macOS.

        - Au moins 4 Go de RAM.
        - N’installez pas les versions antérieures à 4.3.30 de VirtualBox, celles-ci n’étant pas compatibles avec Docker Desktop.

        > [!NOTE]
        > Docker prend en charge Docker Desktop sur les versions les plus récentes de macOS. Autrement dit, la version actuelle de macOS et les deux versions précédentes. Quand une nouvelle version majeure de macOS est publiée, Docker cesse de prendre en charge la version la plus ancienne et prend en charge la dernière version de macOS (en plus des deux versions précédentes). Docker Desktop prend actuellement en charge macOS Mojave, Catalina et Big Sur.
        > 
        - Le programme d’installation n’est pas pris en charge sur Apple M1.

## <a name="instructions"></a>Instructions

1. Téléchargez le Dev Tools Pack Installer pour [Windows](https://go.microsoft.com/fwlink/?linkid=2132187), [Linux](https://go.microsoft.com/fwlink/?linkid=2132186) ou [Mac](https://go.microsoft.com/fwlink/?linkid=2132296).

1. Le lancement du programme d’installation varie en fonction de votre plateforme.

    1. Pour Windows :
    
        1. Cliquez sur **Dev-Tools-Pack-Installer** pour ouvrir l’Assistant Installation.
        
    1. Pour Mac :
    
        1. Après le téléchargement, déplacez le fichier Dev-Tools-Pack-Installer.app dans le dossier Applications.
        
        1. Cliquez sur **Dev-Tools-Pack-Installer.app** pour ouvrir l’Assistant Installation.
        
        1. Si vous recevez une boîte de dialogue de sécurité « développeur non identifié » :
        
            1. Accédez à Préférences système -> Sécurité et confidentialité -> Général, puis cliquez sur le bouton « Ouvrir quand même » en regard de « Dev-Tools-Pack-Installer.app ».
        
            1. Cliquez à nouveau sur l’icône Electron sur le Dock.
        
            1. Cliquez sur le bouton « Ouvrir » dans la boîte de dialogue de sécurité.
    
    1. Pour Linux :
    
        1. Quand le navigateur vous y invite, cliquez sur « Enregistrer » pour terminer le téléchargement du programme d’installation.
        
        1. Ajoutez des autorisations d’exécution à la méthode 1 (CommandLine) du fichier **.appimage** :
            
            1. Ouvrez le terminal Linux.
            
            1. Tapez la commande suivante dans le terminal pour accéder au dossier des téléchargements.
            
                1. cd ~/Downloads/
                
            1. Tapez la commande suivante dans le terminal pour rendre AppImage exécutable.
            
                1. chmod +x **Dev-Tools-Pack-Installer.AppImage**
                
            1. Tapez la commande suivante dans le terminal pour exécuter le programme d’installation.
            
                1. ./Dev-Tools-Pack-Installer.AppImage
        
        1. Ajoutez des autorisations d’exécution à la méthode 2 (UI) du fichier **.appimage** :
        
            1. Cliquez avec le bouton droit sur le fichier .appimage et sélectionnez Propriétés.
            
            1. Ouvrez l’onglet Autorisations.
            
            1. Cochez la case « Autoriser l’exécution du fichier en tant que programme ».
            
            1. Fermez les propriétés et ouvrez le fichier .appimage.

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

   1. Windows :
   
      1. Développez les icônes masquées de la barre d’état système :
      
         1. Développez les icônes de la barre d’état système si elles sont masquées :

            :::image type="content" source="./media/dev-tools-installer/system-tray.png" alt-text="Barre d’état système.":::
         
         1. Vérifiez que l’icône Docker Desktop de la station d’accueil indique « Docker Desktop est en cours d’exécution » :

            :::image type="content" source="./media/dev-tools-installer/docker-status-running.png" alt-text="État de Docker.":::
         
         1. Si vous ne voyez pas l’icône ci-dessus indiquée dans la barre d’état système, lancez Docker Desktop à partir du menu Démarrer.
         
         1. Si Docker vous invite à redémarrer, il est recommandé de fermer le programme d’installation et de le relancer une fois que le redémarrage est terminé et que Docker est dans un état d’exécution. Toutes les applications tierces installées doivent être détectées (elles ne sont pas réinstallées automatiquement).

## <a name="next-steps"></a>Étapes suivantes

Consultez le [référentiel de développement avancé Azure Percept](https://github.com/microsoft/azure-percept-advanced-development) pour passer au développement avancé avec Azure Percept DK.