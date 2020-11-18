---
title: Configurer des labos Projet Lead the Way avec Azure Lab Services
description: Découvrez comment configurer des labos pour enseigner dans le cadre de cours Project Lead the Way.
ms.topic: article
ms.date: 10/28/2020
ms.openlocfilehash: 8585d09759319eef04da5ed68fec603cfa390093
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94496227"
---
# <a name="set-up-labs-for-project-lead-the-way-classes"></a>Configurer des labos pour des cours Project Lead the Way

[Project Lead the Way (PLTW)](https://www.pltw.org/) est un organisme à but non lucratif qui propose des programmes éducatifs à l’intention des élèves du primaire à travers les États-Unis dans les domaines de l’informatique, de l’ingénierie et des sciences biomédicales.  Dans chaque cours PLTW, les élèves utilisent diverses applications logicielles dans le cadre de leurs travaux pratiques.  La plupart des applications logicielles nécessitent un processeur rapide voire, dans certains cas, un GPU.  Cet article explique comment configurer les labos pour les cours PLTW suivants, qui sont généralement dispensés à des élèves de cours moyen :

- **Introduction à la conception technique**

    Les élèves sont initiés au processus de conception technique, avec notamment l’utilisation du logiciel de [conception assistée par l’ordinateur (CAO) Inventor d’Autodesk](https://www.autodesk.com/products/inventor/new-features) pour la modélisation 3D.

- **Principes de l’ingénierie**
    
    Les élèvent découvrent les mécanismes de l’ingénierie, de la résistance structurelle/des matériaux et de l’automatisation.  Dans ce cours, les logiciels utilisés sont [MD Solids](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/MD+Solids/MD+Solids+Software+Installation+Guide.pdf), [West Point Bridge Designer](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/West+Point+Bridge+Builder/Installation+Guide+for+West+Point+Bridge+Designer.pdf) et la [simulation America’s Army](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/America's+Army/Installation+Guide+for+Americas+Army+Simulation+17-18.pdf).

- **Génie civil et architecture**

    Les élèves reçoivent un enseignement en construction et conception/développement de sites avec le logiciel de conception architecturale [Revit d’Autodesk](https://www.autodesk.com/products/revit/overview) pour la modélisation des données sur un bâtiment (BIM) en 3D.

- **Productique**

    Les élèvent explorent les processus de fabrication modernes qui font appel à la robotique et l’automation.   Dans ce cours, les élèves utilisent le logiciel de [CAO Inventor d’Autodesk](https://www.autodesk.com/products/inventor/new-features) et le logiciel de [fabrication assistée par ordinateur (CAM) Inventor d’Autodesk](https://www.autodesk.com/products/inventor-cam/overview). 

- **Électronique numérique**

    Les élèves étudient les circuits et les appareils à logique électronique à l’aide du logiciel de conception de circuits et de simulation [Multisim de National Instruments](https://www.ni.com/en-us/shop/electronic-test-instrumentation/application-software-for-electronic-test-and-instrumentation-category/what-is-multisim.html).

- **Conception technique et développement**

    Les étudiants participent à une solution de bout en bout combinant recherche, conception et tests qu’ils présentent à un groupe d’ingénieurs.  Dans ce cours, les élèves utilisent le logiciel de [CAO Inventor d’Autodesk](https://www.autodesk.com/products/inventor/new-features).

- **Notions de base de l’informatique**

    Les élèves sont initiés aux concepts et outils informatiques.  Après une phase initiale de programmation par blocs, ils passent au codage textuel en utilisant des environnements de codage tels que les [blocs VEXcode v5](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/VEXcode+V5+Blocks/VexCode+V5+Blocks+Installation+Guide.pdf).

- **Principes de l’informatique**
    
    Les étudiants développent leurs compétences en programmation avec [Python](https://www.python.org/) en utilisant l’[environnement de développement Visual Studio Code de Microsoft](https://code.visualstudio.com/). 

- **Informatique A**

    Dans ce cours, les étudiants aiguisent leurs compétences en programmation en apprenant à développer des applications mobiles.  Dans ce cours, ils apprennent [Java](https://www.java.com/) en utilisant l’[environnement de développement Visual Studio Code de Microsoft](https://code.visualstudio.com/).  Les élèves utilisent aussi un émulateur qui leur permet d’exécuter et tester le code de leur application mobile.  Pour savoir comment configurer un émulateur dans Azure Labs, contactez-nous à l’adresse azlabspilot@microsoft.com.

Consultez le site de PLTW pour obtenir la [liste complète des logiciels](https://www.pltw.org/pltw-software) pour chaque cours.

## <a name="lab-configuration"></a>Configuration du laboratoire
Pour configurer des labos pour PLTW, vous avez besoin d’un abonnement Azure et d’un compte Lab pour commencer. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer. Une fois que vous disposez d’un abonnement Azure, vous pouvez créer un compte lab dans Azure Lab Services. Pour plus d’informations sur la création d’un compte de laboratoire, consultez notre tutoriel sur [la configuration d’un compte de labo](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account). Vous pouvez également utiliser un compte Lab existant.

Dès lors que vous disposez d’un compte Lab, vous avez tout intérêt à créer des labos séparés pour chaque session d’un cours PLTW dispensé dans votre établissement.  Nous vous recommandons aussi de créer des images distinctes pour chaque type de cours PLTW.  Pour plus d’informations sur la façon de structurer vos labos et images, lisez le billet de blog suivant : [Moving from a Physical Lab to Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931).

### <a name="lab-account-settings"></a>Paramètres du compte Lab
Activez les paramètres décrits dans le tableau ci-dessous pour le compte lab. Pour plus d’informations sur l’activation des images de la Place de marché, consultez l’article [Spécifier les images de la Place de marché accessibles aux créateurs de labo](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images).

| Paramètres du compte lab | Instructions |
| -------------------- | ----- |
| Image de la Place de marché | Activez l’image Windows 10 Pro pour l’utiliser dans votre compte lab. |

### <a name="lab-settings"></a>Paramètres du labo
La taille de la machine virtuelle que nous conseillons d’utiliser pour les cours PLTW dépend des types de charges de travail qu’utilisent les élèves en cours.  Pour les cours ci-dessus, nous vous recommandons d’utiliser les tailles de machine virtuelle Grande et Petite avec GPU (visualisation).  Aidez-vous des recommandations figurant dans le tableau ci-dessous pour configurer des labos pour vos cours PLTW.

| Paramètre de labo | Valeur/instructions |
| ------------ | ------------------ |
|Taille de la machine virtuelle| **GPU de petite taille (visualisation)** .  Cette machine virtuelle est optimisée pour la visualisation à distance, la diffusion en continu, les jeux et l’encodage avec des infrastructures comme OpenGL ou DirectX.  Nous vous recommandons d’utiliser cette taille pour les cours PLTW suivants : Génie civil et architecture civile, Electronique numérique, Productique et Conception technique et développement.
|Taille de la machine virtuelle| **Grande**.  Cette taille est idéale pour les applications nécessitant des UC plus rapides, de meilleures performances du disque local, des bases de données volumineuses et des caches mémoire volumineux.  Nous vous recommandons d’utiliser cette taille pour les cours PLTW suivants : Introduction à la conception technique, Principes de l’ingénierie, Notions de base de l’informatique, Principes de l’informatique et Informatique A.

### <a name="licensing-server"></a>Serveur de licences
La plupart des logiciels utilisés dans les cours PLTW ci-dessus **_n’exigent pas_* _ d’accès à un serveur de licences.  Cependant, vous devrez accéder à un serveur de licences si vous envisagez d’utiliser le modèle de licence réseau d’Autodesk pour les logiciels suivants :
-   Revit
-   Inventor
-   Inventor CAM

Pour utiliser des licences réseau avec les logiciels Autodesk, [PLTW explique de manière détaillée comment](https://www.pltw.org/pltw-software) installer le gestionnaire de licences Autodesk sur votre serveur de licences.  Ce serveur de licences se trouve généralement sur votre réseau local ou est hébergé sur une machine virtuelle Azure au sein du réseau virtuel Azure.

Une fois votre serveur de licences configuré, vous devez [appairer le réseau virtuel](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network) avec votre [compte Lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account). Le peering du réseau doit être effectué _avant* de créer le labo pour permettre aux machines virtuelles du labo d’accéder au serveur de licences et inversement.

Les fichiers de licence générés par Autodesk incorporent l’adresse MAC du serveur de licences.  Si vous décidez d’héberger votre serveur de licences en utilisant une machine virtuelle Azure, il est important de vérifier que l’adresse MAC de votre serveur de licences ne change pas.   Dans le cas contraire, quand l’adresse MAC change, vos fichiers de licence doivent être regénérés.  Pour éviter que votre adresse MAC change, suivez ces conseils :

- [Définissez une adresse IP privée et une adresse MAC statiques](https://docs.microsoft.com/azure/lab-services/how-to-create-a-lab-with-shared-resource#static-private-ip-and-mac-address) pour la machine virtuelle Azure qui héberge votre serveur de licences.
- Veillez à configurer votre compte Lab et le réseau virtuel du serveur de licences dans une région\localisation qui dispose d’une capacité de machine virtuelle suffisante pour vous éviter d’avoir par la suite à déplacer ces ressources vers une nouvelle région\localisation.

De même, lisez l’article expliquant [comment configurer un serveur de licences en tant que ressource partagée](https://docs.microsoft.com/azure/lab-services/how-to-create-a-lab-with-shared-resource) pour plus d’informations.

### <a name="template-machine"></a>Machine modèle
Certains des fichiers d’installation dont vous avez besoin pour PLTW sont volumineux et leur copie au moment du téléchargement sur la machine modèle d’un labo prend un certain temps.

Au lieu de télécharger les fichiers d’installation sur la machine modèle et de tout y installer, nous vous recommandons de créer vos images PLTW dans votre environnement physique.  Vous pouvez ensuite importer les images dans Shared Image Gallery de façon à pouvoir utiliser ces images personnalisées pour créer vos labos.  Pour plus d’informations, lisez l’article suivant : [Charger une image personnalisée sur Shared Image Gallery](https://docs.microsoft.com/azure/lab-services/upload-custom-image-shared-image-gallery).

Sur la base de ces recommandations, voici les principales tâches à effectuer pour configurer un labo :

1. Dans votre environnement physique, créez l’image pour le cours.

    a.  Suivez les étapes détaillées de PLTW pour télécharger les fichiers d’installation et installer les logiciels nécessaires.

    > [!NOTE]    
    > Pendant l’installation des applications Autodesk, l’ordinateur sur lequel vous installez Autodesk doit pouvoir communiquer avec votre serveur de licences (l’assistant d’installation d’Autodesk vous invite à spécifier le nom d’ordinateur de la machine sur laquelle le serveur de licences est hébergé).  Si vous hébergez votre serveur de licences sur une machine virtuelle Azure, vous devrez peut-être attendre avant d’installer Autodesk sur la machine modèle du labo, le temps que l’assistant d’installation d’Autodesk accède à votre serveur de licences.

    b.  [Installez et configurez OneDrive](https://docs.microsoft.com/azure/lab-services/how-to-prepare-windows-template#install-and-configure-onedrive) (ou les autres options de sauvegarde utilisées par votre établissement).
    
    c.  [Installez et configurez Windows Update](https://docs.microsoft.com/azure/lab-services/how-to-prepare-windows-template#install-and-configure-updates).

1.  Chargez l’image personnalisée sur l’instance [Shared Image Gallery qui est rattachée à votre compte Lab](https://docs.microsoft.com/azure/lab-services/how-to-attach-detach-shared-image-gallery).

1.  Créez un labo et sélectionnez l’image personnalisée que vous avez chargée à l’étape précédente.

1.  Une fois le labo créé, démarrez et connectez-vous à la machine modèle pour vérifier que l’image fonctionne comme prévu.

1.  Enfin, publiez la machine modèle pour créer les machines virtuelles des élèves.

## <a name="student-devices"></a>Appareils des élèves
Vos élèves peuvent se connecter à leurs machines virtuelles de labo à partir d’ordinateurs Windows\Mac et de Chromebooks.  Vous trouverez des instructions pour chacune de ces options en suivant ces liens :

- [Se connecter à partir de Windows](https://docs.microsoft.com/azure/lab-services/how-to-use-classroom-lab#connect-to-the-vm)
- [Se connecter à partir d’un Mac](https://docs.microsoft.com/azure/lab-services/connect-virtual-machine-mac-remote-desktop)
- [Se connecter à partir d’un Chromebook](https://docs.microsoft.com/azure/lab-services/connect-virtual-machine-chromebook-remote-desktop)

## <a name="cost"></a>Coût
Abordons à présent la question du coût estimatif des cours PLTW ci-dessus.  Cette estimation n’inclut pas le coût lié à l’exécution d’un serveur de licences ou à l’utilisation de Shared Image Gallery.  Nous partirons sur l’hypothèse d’une classe composée de 25 élèves.  Nous prévoyons 20 heures de temps de classe.  Par ailleurs, chaque élève aura un quota de 10 heures pour faire des devoirs à la maison ou des travaux en dehors des heures de cours prévues.  Reportez-vous aux estimations de coût ci-dessous pour les tailles **Grande** et **Petite avec GPU (visualisation)** .

- **Machine virtuelle de grande taille**

    25 élèves x (20 heures prévues + 10 heures de quota) x 70 unités Lab x 0,01 USD par heure = 525,00 USD

- **GPU de petite taille (visualisation)**

    25 élèves x (20 heures prévues + 10 heures de quota) x 160 unités Lab x 0,01 USD par heure = 1 200,00 USD

> [!IMPORTANT] 
> L’estimation du coût est fournie à titre d’exemple uniquement.  Pour en savoir plus sur les tarifs actuels, consultez [Tarification Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

> [!NOTE] 
> La plupart des cours PLTW utilisent des applications accessibles via un navigateur, comme MIT App Inventor.  Ces applications basées sur navigateur n’ont pas besoin d’un processeur ou d’un GPU rapide et sont accessibles à partir de n’importe quel appareil disposant d’une connexion Internet.  Quand les élèves utilisent des applications de ce type, nous vous recommandons de leur demander d’utiliser le navigateur de leur appareil physique au lieu d’utiliser celui de leur machine virtuelle Lab.  Le fait de limiter l’utilisation des machines virtuelles Lab aux applications qui demandent un processeur ou un GPU rapide vous permettra de réduire les coûts.

## <a name="next-steps"></a>Étapes suivantes
Les étapes suivantes sont communes à la configuration de n’importe quel labo :

- [Ajout d'utilisateurs](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Définir un quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Définir un calendrier](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Envoyer par mail des liens d’inscription aux étudiants](how-to-configure-student-usage.md#send-invitations-to-users) 