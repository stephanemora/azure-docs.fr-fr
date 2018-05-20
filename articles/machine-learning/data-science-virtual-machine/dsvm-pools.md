---
title: Pools de machines virtuelles DSVM - Azure | Microsoft Docs
description: Déploiement de pools de machines virtuelles de science des données (DSVM) en tant que ressource partagée de l’équipe
keywords: formation approfondie, IA, outils de science des données, machine virtuelle de science des données, analyse géospatiale, processus de science des données en équipe
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 625154378fecbd4d45904fa5669adb866fc9487c
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2018
---
# <a name="creating-a-shared-pool-of-data-science-virtual-machines"></a>Création d’un pool partagé de machines virtuelles de science des données

Cet article explique comment créer un pool partagé de machines virtuelles de science des données (DSVM) afin qu’il puisse être utilisé par l’équipe. L’utilisation d’un pool partagé est avantageuse : meilleure utilisation des ressources, partage et collaboration facilités, et gestion plus efficace des ressources des machines virtuelles DSVM permise pour le département informatique. 

Nombre de méthodes et de technologies différentes peuvent être utilisées pour créer un pool de machines virtuelles DSVM.  Voici les principaux scénarios :

* Pool pour traitement par lots
* Pool de machines virtuelles interactives

## <a name="batch-processing-pool"></a>Pool pour traitement par lots
Si vous souhaitez configurer un pool de machines virtuelles DSVM principalement pour exécuter des travaux dans un lot en mode hors connexion, vous pouvez utiliser le service [Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/) ou [Azure Batch](https://docs.microsoft.com/azure/batch/). 

### <a name="azure-batch-ai"></a>Azure Batch AI
L’édition Ubuntu de la machine virtuelle DSVM est prise en charge en tant qu’une des images dans Azure Batch AI. Dans l’interface de ligne de commande Azure ou le Kit de développement logiciel (SDK) Python où vous créez le cluster Azure Batch AI, vous pouvez spécifier le paramètre ```image``` et le définir sur ```UbuntuDSVM```. Vous pouvez choisir le type de nœud de traitement que vous souhaitez, à savoir des instances GPU ou des instances d’UC seule, le nombre d’UC et la mémoire parmi un [large choix d’instances de machine virtuelle](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) disponibles dans Azure. Lorsque vous utilisez l’image de machine virtuelle DSVM Ubuntu dans Batch AI avec les nœuds basés sur GPU, tous les pilotes GPU nécessaires et les infrastructures de formation approfondie sont préinstallés, ce qui vous fait gagner un temps de préparation des nœuds Batch considérable. En fait, si vous procédez au développement de contenu sur une machine virtuelle DSVM Ubuntu de façon interactive, vous remarquerez que les nœuds Batch AI représentent l’exacte configuration de l’environnement. En général, lorsque vous créez un cluster Batch AI, vous créez également un partage de fichiers qui est monté par tous les nœuds et qui est utilisé pour l’entrée et la sortie des données ainsi que pour le stockage du code/des scripts de programme de traitement par lots. 

Une fois que le cluster Batch AI est créé, vous pouvez utiliser la même interface CLI ou le même Kit de développement logiciel (SDK) Python pour envoyer des travaux à exécuter. Vous ne payez que pour le temps utilisé pour l’exécution des programmes de traitement par lots. 

#### <a name="more-information"></a>Plus d’informations
* Procédure pas à pas d’utilisation [d’Azure CLI](https://docs.microsoft.com/azure/batch-ai/quickstart-cli) pour gérer Batch AI
* Procédure pas à pas d’utilisation de [Python](https://docs.microsoft.com/azure/batch-ai/quickstart-python) pour gérer Batch AI
* Les [recettes Batch AI](https://github.com/Azure/BatchAI) sont disponibles et montrent comment utiliser les infrastructures de formation approfondie/AI avec Batch AI.

## <a name="interactive-vm-pool"></a>Pool de machines virtuelles interactives

Un pool de machines virtuelles DSVM interactives qui sont partagées par l’équipe complète AI/science des données permet aux utilisateurs de se connecter à une instance disponible de la machine virtuelle DSVM au lieu de disposer d’une instance dédiée pour chaque ensemble d’utilisateurs. Cela permet une meilleure disponibilité et une plus grande efficacité de l’utilisation des ressources. 

[Azure Virtual Machine Scale Sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) est la technologie utilisée pour créer un pool de machines virtuelles interactives. Elle permet de créer et de gérer un groupe de machines virtuelles identiques, à charge équilibrée et à mise à l’échelle automatique. L’utilisateur se connecte à l’adresse IP ou DNS du pool principal. La session est automatiquement acheminée vers une machine virtuelle DSVM disponible dans le groupe identique. Comme les utilisateurs souhaitent un environnement semblable quelle que soit la machine virtuelle à laquelle ils se connectent, toutes les instances de la machine virtuelle du groupe identique montées sur un lecteur réseau partagé, comme un partage Azure Files ou NFS. L’espace de travail partagé de l’utilisateur est normalement conservé dans le magasin de fichiers partagé qui est monté sur chacune des instances. 

Un exemple de modèle Azure Resource Manager qui permet de créer un groupe identique de machines virtuelles comptant des instances de machines virtuelles DSVM Ubuntu se trouve dans [GitHub](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json). Un exemple de [fichier de paramètres](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) de modèle Azure Resource Manager figure également au même emplacement. 

Vous pouvez créer le groupe identique de machines virtuelles à partir du modèle Azure Resource Manager en spécifiant des valeurs appropriées pour le fichier de paramètres à l’aide d’Azure CLI. 

```
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```
Les commandes indiquées ci-dessus supposent que vous possédez une copie du fichier de paramètres avec des valeurs spécifiées pour votre instance du groupe identique de machines virtuelles, le nombre d’instances de machine virtuelle, les pointeurs renvoyant à vos fichiers Azure, ainsi que des informations d’identification pour le compte de stockage monté sur chaque machine virtuelle. Le fichier de paramètres est référencé localement dans la commande ci-dessus. Vous pouvez également transmettre des paramètres inclus ou inviter à les entrer dans votre script.  

Le modèle présenté ci-dessus active le protocole SSH et le port JupyterHub du groupe identique de machines virtuelles frontales au pool principal de machines virtuelles DSVM Ubuntu.  En tant qu’utilisateur, il vous suffit de vous connecter normalement à une machine virtuelle sur SSH ou JupyterHub. Comme la taille des instances de machine virtuelle peut être augmentée ou diminuée de façon dynamique, tout état doit être enregistré dans le partage Azure Files monté. La même approche peut être utilisée pour créer un pool de machines virtuelles DSVM Windows. 

Le [script permettant de monter Azure Files](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) est également disponible dans le référentiel GitHub des machines virtuelles DSVM Azure. Outre le montage d’Azure Files au niveau du point de montage spécifié dans le fichier de paramètres, il crée des liens symboliques supplémentaires vers le lecteur monté dans le répertoire de base de l’utilisateur initial. Un répertoire de blocs-notes propre à l’utilisateur figurant dans le dossier Azure Files partagé est également associé par un lien symbolique au répertoire ```$HOME/notebooks/remote```, ce qui permet à l’utilisateur d’accéder aux blocs-notes Jupyter, de les exécuter et de les enregistrer.  La même convention peut être utilisée lorsque vous créez des utilisateurs supplémentaires sur la machine virtuelle pour pointer vers l’espace de travail Jupyter de chaque utilisateur et le répertoire Azure Files partagé. 

Azure Virtual Machine Scale Sets prend en charge la mise à l’échelle automatique qui vous permet de définir des règles relatives au moment auquel créer des instances supplémentaires et aux circonstances dans lesquelles diminuer le nombre d’instances, voire ramener ce nombre à zéro pour économiser les coûts d’utilisation de matériel dans le cloud lorsque les machines virtuelles ne sont pas utilisées du tout . Les pages de documentation des groupes identiques de machines virtuelles fournissent des instructions détaillées de [mise à l’échelle automatique](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview).

## <a name="next-steps"></a>Étapes suivantes

* [Configurer une identité commune](dsvm-common-identity.md)
* [Stocker en toute sécurité les informations d’identification pour accéder aux ressources cloud](dsvm-secure-access-keys.md)















