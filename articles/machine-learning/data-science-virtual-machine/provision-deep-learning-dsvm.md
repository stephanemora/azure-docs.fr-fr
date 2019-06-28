---
title: Créer une machine virtuelle Data Science Virtual Machine pour le Deep Learning
titleSuffix: Azure
description: Configurez et créez une instance Deep Learning Data Science Virtual Machine dans Azure à des fins d’analytique et d’apprentissage automatique.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 318df03c7c4447d051dfa396098462c0f8bbf423
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65410432"
---
# <a name="provision-a-deep-learning-virtual-machine-on-azure"></a>Provisionner une machine virtuelle DLVM dans Azure 

La machine virtuelle DLVM (Deep Learning Virtual Machine) est une variante de la célèbre machine virtuelle [DSVM (Data Science Virtual Machine)](https://aka.ms/dsvm). Elle a été spécialement configurée pour faciliter l'utilisation des instances de machine virtuelle basées sur GPU visant à former rapidement des modèles d’apprentissage profond. Elle est prise en charge avec DSVM Windows 2016 ou Ubuntu comme base. La machine virtuelle DLVM partage les mêmes images de machines virtuelles principales, ce qui explique la richesse des outils qui sont disponibles avec la machine virtuelle DSVM. 

La machine virtuelle DLVM contient plusieurs outils d’intelligence artificielle, notamment les éditions GPU des frameworks d’apprentissage profond courants tels que Microsoft Cognitive Toolkit, TensorFlow, Keras, Caffe2 ou Chainer. Elle contient également des outils permettant d’acquérir et de prétraiter des images et des données textuelles, des outils pour la modélisation et le développement de science des données, tels que Microsoft R Server Developer Edition, Anaconda Python, les bloc-notes Jupyter pour Python et R, les IDE pour Python et R et les bases de données SQL, ainsi que de nombreux autres outils d’apprentissage automatique et de science des données. 

## <a name="create-your-deep-learning-virtual-machine"></a>Créer une instance Deep Learning Virtual Machine
Voici les étapes de création d’une instance Deep Learning Virtual Machine : 

1. Accédez à la liste des machines virtuelles présentes sur le [portail Azure](https://portal.azure.com/#create/microsoft-ads.dsvm-deep-learningtoolkit
).
2. Sélectionnez le bouton **Créer** au bas de l’écran pour accéder à un Assistant.![create-dlvm](./media/dlvm-provision-wizard.PNG)
3. L’Assistant utilisé pour créer l’instance DLVM nécessite les **entrées** de chacune des **quatre étapes** énumérées à droite de cette figure. Voici les entrées nécessaires à la configuration de chacune de ces étapes :

   <a name="basics"></a>   
   1. **Concepts de base**
      
      1. **Nom** : nom du serveur de science des données que vous créez.
      2. **Sélectionner le type de système d’exploitation de la machine virtuelle Deep Learning** : choisissez Windows ou Linux (pour une machine virtuelle DSVM ayant une base Windows 2016 ou Ubuntu Linux).
      2. **Nom d’utilisateur** : ID de connexion du compte Administrateur.
      3. **Mot de passe** : mot de passe du compte Administrateur.
      4. **Abonnement**: Si vous disposez de plusieurs abonnements, sélectionnez celui qui sera associé à la création et à la facturation de la machine.
      5. **Groupe de ressources** : vous pouvez en créer un nouveau ou utiliser un groupe de ressources Azure **vide** dans votre abonnement.
      6. **Emplacement** : sélectionnez le centre de données le plus adapté. Généralement, il s’agit du centre de données qui héberge la plupart de vos données ou du centre de données le plus proche de votre emplacement physique afin d’accélérer l’accès au réseau 
      
      > [!NOTE]
      > La machine virtuelle DLVM prend en charge toutes les instances de machine virtuelle GPU de série NC et ND. Lorsque vous approvisionnez la machine virtuelle DLVM, vous devez choisir l’un des emplacements d’Azure qui dispose de processeurs GPU. Dans la page [Azure Products by Region](https://azure.microsoft.com/regions/services/) (Produits Azure par région), consultez les emplacements disponibles et recherchez **Série NC**, **Série NCv2**, **Série NCv3** ou **Série ND** sous **Compute**. 

   1. **Paramètres**: sélectionnez une taille de machine virtuelle GPU de série NC (NC, NCv2 ou NCv3) ou ND adaptée à vos exigences fonctionnelles et à votre budget. Créez un compte de stockage pour votre machine virtuelle.  ![dlvm-settings](./media/dlvm-provision-step-2.PNG)
   
   1. **Résumé**: Vérifiez que toutes les informations que vous avez saisies sont correctes.

   1. **Acheter**: cliquez sur **Acheter** pour commencer l’approvisionnement. Les conditions de la transaction vous sont communiquées via un lien. La machine virtuelle n'est pas assortie de frais supplémentaires au-delà du calcul de la taille de serveur que vous avez choisie à l'étape **Taille** . 

> [!NOTE]
> L’approvisionnement prend environ 10 à 20 minutes. L’état de l’approvisionnement est affiché sur le portail Azure.
> 


## <a name="how-to-access-the-deep-learning-virtual-machine"></a>Comment accéder à l’instance Deep Learning Virtual Machine

### <a name="windows-edition"></a>Édition Windows
Une fois la machine virtuelle créée, vous pouvez vous y connecter à l’aide du bureau distant en utilisant les informations d’identification du compte administrateur que vous avez configurées dans la section **Paramètres de base** précédente. 

### <a name="linux-edition"></a>Édition Linux

Une fois la machine virtuelle créée, vous pouvez vous y connecter avec SSH. Utilisez les informations d’identification de compte que vous avez créées dans la section [**Paramètres de base**](#basics) de l’étape 3 de l’interface de l’interpréteur de commandes texte. Pour plus d’informations sur les connexions SSH aux machines virtuelles Azure, consultez [Installer et configurer le Bureau à distance pour effectuer une connexion à une machine virtuelle Linux dans Azure](/azure/virtual-machines/linux/use-remote-desktop). Sur un client Windows, vous pouvez télécharger un outil client SSH tel que [Putty](https://www.putty.org). Si vous préférez un bureau graphique (système Windows X), vous pouvez utiliser le transfert X11 sur Putty ou installer le client X2Go. 

> [!NOTE]
> Lors de tests, le client X2Go a obtenu de meilleures performances que le transfert X11. Nous recommandons d’utiliser le client X2Go pour une interface de bureau graphique.
> 
> 

#### <a name="installing-and-configuring-x2go-client"></a>Installation et configuration du client X2Go
La machine virtuelle DLVM Linux est déjà provisionnée avec le serveur X2Go et elle est prête à accepter des connexions client. Pour vous connecter au bureau graphique de la machine virtuelle Linux, effectuez les opérations suivantes sur votre client :

1. Téléchargez et installez le client X2Go pour votre plateforme cliente sur [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient).    
2. Exécutez le client X2Go et sélectionnez **New Session**(Nouvelle session). Une fenêtre de configuration avec plusieurs onglets s’ouvre. Entrez les paramètres de configuration suivants :
   * **Onglet Session**:
     * **Hôte** : nom d’hôte ou adresse IP de votre machine virtuelle DSVM Linux.
     * **Connexion** : nom d’utilisateur sur la machine virtuelle Linux.
     * **Port SSH** : conservez la valeur par défaut (22).
     * **Type de session** : remplacez la valeur par **XFCE**. Les instances Linux de DSVM prennent uniquement en charge XFCE Desktop.
   * **Onglet Multimédia** : vous pouvez désactiver l’impression client et la prise en charge du son si vous n’en avez pas besoin.
   * **Dossiers partagés** : si vous voulez que des répertoires de vos ordinateurs clients soient montés sur la machine virtuelle Linux, ajoutez ceux que vous souhaitez partager avec la machine virtuelle sous cet onglet.

Une fois connecté à la machine virtuelle à l’aide du client SSH ou du bureau graphique XFCE par le biais du client X2Go, vous pouvez commencer à utiliser les outils installés et configurés sur la machine virtuelle. Sur XFCE, vous pouvez voir les icônes de bureau et raccourcis du menu d’applications de la plupart des outils.

Une fois votre machine virtuelle créée et approvisionnée, vous pouvez commencer à utiliser les outils qui y sont installés et configurés. Pour la plupart des outils, vous disposez d'icônes de bureau et de vignettes dans le menu de démarrage. 
