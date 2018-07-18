---
title: Pools de machines virtuelles DSVM - Azure | Microsoft Docs
description: Déploiement de pools de Data Science VM (DSVM) en tant que ressource partagée de l’équipe
keywords: formation approfondie, IA, outils de science des données, machine virtuelle de science des données, analyse géospatiale, processus de science des données en équipe
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 0740ff7542d066442146b8e80e188ad5ba49a2b5
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309396"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Création d’un pool partagé de Data Science Virtual Machines

Cet article explique comment créer un pool partagé de Data Science Virtual Machines (DSVM) afin qu’il puisse être utilisé par l’équipe. L’utilisation d’un pool partagé est apporte certains avantages : meilleure utilisation des ressources, partage et collaboration facilités, et gestion plus efficace des ressources des machines virtuelles DSVM. 

Vous pouvez utiliser plusieurs méthodes et technologies pour créer un pool de DSVM. Cet article se concentre sur les pools pour le traitement par lots et les machines virtuelles interactives.

## <a name="batch-processing-pool"></a>Pool de traitement par lots
Si vous souhaitez configurer un pool de DSVM principalement pour exécuter des travaux dans un lot en mode hors connexion, vous pouvez utiliser le service [Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/) ou [Azure Batch](https://docs.microsoft.com/azure/batch/). Cet article se concentre sur Azure Batch AI.

L’édition Ubuntu de la machine virtuelle DSVM est prise en charge en tant qu’une des images dans Azure Batch AI. Dans l’interface de ligne de commande Azure ou le Kit de développement logiciel (SDK) Python où vous créez le cluster Azure Batch AI, vous pouvez spécifier le paramètre `image` et le définir sur `UbuntuDSVM`. Vous pouvez choisir le type de nœud de traitement que vous souhaitez, à savoir des instances GPU ou des instances d’UC seule, le nombre d’UC et la mémoire parmi un [large choix d’instances de machine virtuelle](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) disponibles dans Azure. 

Lorsque vous utilisez l’image DSVM Ubuntu dans Batch AI avec les nœuds basés sur GPU, tous les pilotes GPU nécessaires et les infrastructures de formation approfondie sont préinstallés. Cela vous fait gagner un temps de préparation des nœuds Batch considérable. En fait, si vous procédez au développement de contenu sur une DSVM Ubuntu de façon interactive, vous remarquerez que les nœuds Batch AI représentent l’exacte configuration de l’environnement. 

En général lorsque vous créez un cluster Batch AI, vous créez également un partage de fichiers monté par tous les nœuds. Celui-ci est utilisé pour l’entrée et la sortie des données ainsi que pour le stockage du code/des scripts de programme de traitement par lots. 

Une fois que le cluster Batch AI est créé, vous pouvez utiliser la même interface CLI ou le même Kit de développement logiciel (SDK) Python pour envoyer des travaux à exécuter. Vous ne payez que pour le temps utilisé pour l’exécution des programmes de traitement par lots. 

Pour plus d'informations, consultez les pages suivantes :
* Procédure pas à pas d’utilisation [d’Azure CLI](https://docs.microsoft.com/azure/batch-ai/quickstart-cli) pour gérer Batch AI
* Procédure pas à pas d’utilisation de [Python](https://docs.microsoft.com/azure/batch-ai/quickstart-python) pour gérer Batch AI
* [Recettes Batch AI](https://github.com/Azure/BatchAI) qui montrent comment utiliser les infrastructures d’apprentissage profond et d’AI avec Batch AI

## <a name="interactive-vm-pool"></a>Pool de machines virtuelles interactives

Un pool de machines virtuelles interactives qui sont partagées par l’équipe complète AI/science des données permet aux utilisateurs de se connecter à une instance disponible de la DSVM au lieu de disposer d’une instance dédiée pour chaque ensemble d’utilisateurs. Cette configuration permet une meilleure disponibilité et une plus grande efficacité d’utilisation des ressources. 

La technologie que vous utilisez pour créer un pool de machines virtuelles interactives s’appelle [groupe de machines virtuelles identiques Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/). Les groupes identiques vous permettent de créer et de gérer un groupe de machines virtuelles identiques, disposant d’une charge équilibrée et d’une mise à l’échelle automatique. 

L’utilisateur se connecte à l’adresse IP ou DNS du pool principal. La session est automatiquement acheminée vers une DSVM disponible dans le groupe identique. Comme les utilisateurs souhaitent un environnement semblable quelle que soit la machine virtuelle à laquelle ils se connectent, toutes les instances de la machine virtuelle dans le groupe identique sont montées sur un lecteur réseau partagé, comme un partage Azure Files ou NFS. L’espace de travail partagé de l’utilisateur est normalement conservé dans le magasin de fichiers partagé qui est monté sur chacune des instances. 

Un exemple de modèle Azure Resource Manager qui permet de créer un groupe identique comptant des instances de DSVM Ubuntu se trouve dans [GitHub](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json). Un exemple de [fichier de paramètres](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) de modèle Azure Resource Manager figure également au même emplacement. 

Vous pouvez créer le groupe identique à partir du modèle Azure Resource Manager en spécifiant des valeurs appropriées pour le fichier de paramètres dans Azure CLI. 

```
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```
Les commandes précédentes supposent que vous disposez :
* D’une copie du fichier de paramètres avec les valeurs spécifiées pour votre instance de groupe identique.
* Du nombre d’instances de machine virtuelle.
* De pointeurs vers le partage de fichiers Azure.
* Des informations d’identification pour le compte de stockage qui sera monté sur chaque machine virtuelle. 

Le fichier de paramètres est référencé en local dans les commandes. Vous pouvez également transmettre des paramètres inclus ou inviter à les entrer dans votre script.  

Le modèle présenté ci-dessus active le protocole SSH et le port JupyterHub du groupe identique front-end au pool de machines virtuelles DSVM Ubuntu back-end. En tant qu’utilisateur, il vous suffit de vous connecter normalement à la machine virtuelle sur SSH ou JupyterHub. Comme la taille des instances de machine virtuelle peut être augmentée ou diminuée de façon dynamique, tout état doit être enregistré dans le partage Azure Files monté. La même approche peut être utilisée pour créer un pool de DSVM Windows. 

Le [script permettant de monter le partage Azure Files](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) est également disponible dans le référentiel GitHub des machines virtuelles DSVM Azure. Le script monte le partage de fichiers Azure au niveau du point de montage spécifié dans le fichier de paramètres. Le script crée également des liens symboliques pour le lecteur monté dans le répertoire de base initial de l’utilisateur. Un répertoire Notebook spécifique à l’utilisateur figurant dans le partage de fichiers Azure fait l’objet d’un lien symbolique vers le répertoire `$HOME/notebooks/remote` afin que les utilisateurs puissent accéder, exécuter et enregistrer leurs bloc-notes Jupyter. Vous pouvez utiliser la même convention lorsque vous créez des utilisateurs supplémentaires sur la machine virtuelle pour pointer l’espace de travail Jupyter de chaque utilisateur vers le répertoire Azure Files partagé. 

Le groupe de machines virtuelles identiques prend en charge la mise à l’échelle automatique. Vous pouvez définir des règles qui déclenchent la création d’instances supplémentaires ou la suppression d’instances. Par exemple, vous pouvez descendre en puissance jusqu’à ne plus avoir d’instances afin d’économiser sur les coûts d’utilisation de matériel cloud lorsque les machines virtuelles ne sont pas du tout utilisées. Les pages de documentation des groupes identiques de machines virtuelles fournissent des instructions détaillées de [mise à l’échelle automatique](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview).

## <a name="next-steps"></a>Étapes suivantes

* [Configurer une identité commune](dsvm-common-identity.md)
* [Stocker en toute sécurité les informations d’identification pour accéder aux ressources cloud](dsvm-secure-access-keys.md)















