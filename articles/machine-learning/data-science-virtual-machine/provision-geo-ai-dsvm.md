---
title: 'Démarrage rapide : Créer une instance Geo AI de Data Science Virtual Machine'
titleSuffix: Azure Data Science Virtual Machine
description: Configurez et créez un environnement Geo AI Data Science Virtual Machine dans Azure à des fins d’analytique géospatiale et de Machine Learning.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/13/2019
ms.openlocfilehash: f3ff9bd64f54d8f83fd1889078e8a4c01827d135
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77525887"
---
# <a name="quickstart-set-up-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>Démarrage rapide : Configurer une machine virtuelle d’intelligence artificielle géographique sur Azure 

La Data Science Virtual Machine d’intelligence artificielle géographique (Geo-DSVM) est une extension de la célèbre [Azure Data Science Virtual Machine](https://aka.ms/dsvm), spécialement configurée pour associer intelligence artificielle et analytique géospatiale. L’analyse géospatiale de la machine virtuelle est alimentée par [ArcGIS Pro](https://www.arcgis.com/features/index.html). La Data Science Virtual Machine (DSVM) permet l’entraînement rapide de modèles de machine learning et même de deep learning. Pour développer ces modèles, elle utilise des données enrichies d’informations géographiques. La Geo-DSVM est prise en charge uniquement sur la DSVM Windows 2016. 

Outils d’IA inclus dans Geo-DSVM :

- Éditions GPU d’infrastructures de deep learning populaires telles que Microsoft Cognitive Toolkit, TensorFlow, Keras, Caffe2 et Chainer
- Outils permettant d’acquérir et de prétraiter des données d’image et des données texte
- Outils dédiés aux activités de développement, tels que Microsoft Machine Learning Server Developer Edition, Anaconda Python, les notebooks Jupyter pour Python et R, les IDE pour Python et R, et les bases de données SQL
- Logiciel de bureau ArcGIS Pro ESRI, avec les interfaces Python et R, prenant en charge les données géospatiales de vos applications d’IA
 

## <a name="create-your-geo-ai-data-science-vm"></a>Créer une machine virtuelle de science des données Geo AI

Pour créer une instance de la Geo AI Data Science VM, effectuez les étapes suivantes :

1. Accédez à la liste des machines virtuelles présentes sur le [portail Azure](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows).
1. Sélectionnez **Créer** au bas de l’écran pour générer un Assistant :

   ![create-geo-ai-dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)

1. L’Assistant nécessite une entrée pour chacune des quatre étapes. Pour plus d’informations sur cette entrée, consultez la section suivante.

### <a name="wizard-details"></a>Détails de l’Assistant ###

**Paramètres de base**:

- **Name** : Nom du serveur de science des données que vous créez.
    
- **Nom d’utilisateur** : ID de connexion du compte d’administrateur.
    
- **Mot de passe** : mot de passe du compte Administrateur.
    
- **Abonnement**: Si vous disposez de plusieurs abonnements, sélectionnez celui qui sera associé à la création et à la facturation de la machine.
    
- **Groupe de ressources** : vous pouvez en créer un nouveau ou utiliser un groupe de ressources Azure **vide** dans votre abonnement.
    
- **Emplacement** : sélectionnez le centre de données le plus adapté. Généralement, il s’agit de celui qui détient la plupart de vos données ou qui est le plus proche de votre localisation physique pour favoriser l’accès le plus rapide au réseau. Si vous envisagez d’utiliser le deep learning sur un GPU, vous devez choisir un emplacement dans Azure qui contient des instances de machines virtuelles GPU de série NC. Actuellement, ces emplacements sont les suivants : **USA Est, USA Centre Nord, USA Centre Sud, USA Ouest 2, Europe Nord, Europe Ouest**. Pour obtenir la liste la plus récente, accédez à la page [Produits Azure par région](https://azure.microsoft.com/regions/services/) et recherchez **Série NC** sous **Calculer**. 
    
    
**Paramètres**: Sélectionnez l’une des tailles de machine virtuelle GPU de série NC si vous envisagez d’utiliser le deep learning sur un GPU de votre Geo DSVM. Sinon, vous pouvez choisir l’une des instances basées sur le processeur. Créez un compte de stockage pour votre machine virtuelle. 
       
**Résumé**: Vérifiez que toutes les informations que vous avez saisies sont correctes.
    
**Acheter**: Pour démarrer le processus de provisionnement, cliquez sur **Acheter**. Les conditions de service vous sont communiquées via un lien. La machine virtuelle ne génère pas de frais supplémentaires au-delà des frais de calcul pour la taille de serveur que vous avez choisie à l’étape de définition du paramètre **Taille**. 
 
 >[!NOTE]
 > Le provisionnement doit prendre environ 20 à 30 minutes. L’état de l’approvisionnement est affiché sur le portail Azure.

 
## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>Accès à une machine virtuelle de science des données Geo AI

 Après la création de votre machine virtuelle, vous pouvez commencer à utiliser les outils qui y sont installés et préconfigurés. Pour la plupart des outils, vous disposez d’icônes sur le Bureau et de vignettes dans le menu de démarrage. Vous pouvez accéder à la machine virtuelle via le bureau distant en utilisant les informations d’identification du compte d’administrateur que vous avez configurées dans la section **Paramètres de base**.

 
## <a name="using-arcgis-pro-installed-in-the-vm"></a>Utilisation du logiciel ArcGIS Pro installé sur la machine virtuelle

Le logiciel de bureau ArcGIS Pro est préinstallé sur la Geo-DSVM et l’environnement est préconfiguré pour fonctionner avec tous les outils figurant sur la DSVM. Lorsque vous démarrez ArcGIS, vous êtes invité à entrer les informations d’identification de votre compte ArcGIS. Si vous disposez déjà d’un compte ArcGIS et que vous possédez des licences pour ce logiciel, vous pouvez utiliser vos informations d’identification existantes.  

![Connexion à ArcGIS](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

Sinon, vous pouvez souscrire un nouveau compte et une nouvelle licence ArcGIS, ou obtenir un [essai gratuit](https://www.arcgis.com/features/free-trial.html). 

![Évaluation gratuite d’ArcGIS](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

Après avoir souscrit un compte ArcGIS standard ou un essai gratuit, vous pouvez autoriser ArcGIS Pro pour votre compte en suivant les instructions fournies dans [Getting started with ArcGIS Pro](https://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf).

Une fois connecté au logiciel de bureau ArcGIS Pro avec votre compte ArcGIS, vous êtes prêt à commencer à utiliser les outils de science des données installés et configurés sur la machine virtuelle dans le cadre de vos projets d’analytique géospatiale et de machine learning.

## <a name="next-steps"></a>Étapes suivantes

Commencez à utiliser la Geo AI Data Science VM en lisant la ressource suivante :

* [Utiliser la machine virtuelle de sciences des données Geo AI](use-geo-ai-dsvm.md)
