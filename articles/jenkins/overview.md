---
title: Vue d’ensemble de Jenkins et Azure
description: Hébergez la build Jenkins, déployez le serveur Automation dans Azure et utilisez les ressources de calcul et de stockage Azure pour étendre vos pipelines CI/CD (intégration continue et déploiement continu).
keywords: jenkins, azure, devops, overview
ms.topic: overview
ms.date: 10/23/2019
ms.openlocfilehash: a9297ebc116d75cfe1d4f37d4e9ada7d5198beae
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77620180"
---
# <a name="azure-and-jenkins"></a>Azure et Jenkins

[Jenkins](https://jenkins.io/) est un serveur Automation open source connu utilisé pour configurer l’intégration et la livraison continues pour vos projets de logiciels. Vous pouvez héberger votre déploiement Jenkins sur Azure ou étendre votre configuration Jenkins existante à l’aide des ressources Azure. Des plug-ins Jenkins sont également disponibles pour simplifier l’intégration/la livraison continues de vos applications vers Azure.

Cet article est une présentation de l’utilisation d’Azure avec Jenkins. Il décrit les principales fonctionnalités Azure disponibles pour les utilisateurs de Jenkins. Pour plus d’informations sur la prise en main de votre propre serveur Jenkins dans Azure, consultez [Créer un serveur Jenkins sur Azure](install-jenkins-solution-template.md).

## <a name="host-your-jenkins-servers-in-azure"></a>Héberger vos serveurs Jenkins dans Azure

Hébergez Jenkins dans Azure pour centraliser l’automatisation de votre build et mettre à l’échelle votre déploiement à mesure que les besoins de vos projets de logiciels augmentent. Vous pouvez déployer Jenkins dans Azure avec ce qui suit :
 
- [Modèle de solution Jenkins](install-jenkins-solution-template.md) dans Place de marché Microsoft Azure.
- [Machines virtuelles Azure](/azure/virtual-machines/linux/overview). Consultez notre [didacticiel](tutorial-jenkins-github-docker-cicd.md) pour créer une instance de Jenkins sur une machine virtuelle.
- Sur un cluster Kubernetes qui exécute [Azure Container Service](/azure/container-service/kubernetes/container-service-kubernetes-walkthrough), consultez nos [procédures](/azure/container-service/kubernetes/container-service-kubernetes-jenkins).

Surveillez et gérez votre déploiement Azure Jenkins à l’aide des [journaux Azure Monitor](/azure/log-analytics/log-analytics-overview) et d’[Azure CLI](/cli/azure).

## <a name="scale-your-build-automation-on-demand"></a>Mettre à l’échelle l’automatisation de votre build à la demande

Ajoutez des agents de build à votre déploiement Jenkins existant pour mettre à l’échelle la capacité de votre build Jenkins à mesure que le nombre de builds et la complexité de vos travaux et pipelines augmentent. Vous pouvez exécuter ces agents de build sur des machines virtuelles Azure à l'aide du [plug-in Agents de machine virtuelle Azure](https://plugins.jenkins.io/azure-vm-agents). Consultez notre [didacticiel](/azure/jenkins/jenkins-azure-vm-agents) pour plus d’informations.

Une fois configuré avec un [principal du service Azure](/azure/azure-resource-manager/resource-group-overview), les travaux et pipelines Jenkins peuvent utiliser ces informations d’identification pour accomplir ce qui suit :

- Stocker en toute sécurité et archiver des artefacts de build [Stockage Azure](/azure/storage/common/storage-introduction) à l'aide du [plug-in Stockage Azure](https://plugins.jenkins.io/windows-azure-storage). Consultez les [procédures de stockage Jenkins](storage-java-jenkins-continuous-integration-solution.md) pour en savoir plus.
- Gérer et configurer des ressources Azure avec l’[interface de ligne de commande Azure](/azure/jenkins/execute-cli-jenkins-pipeline).

## <a name="deploy-your-code-into-azure-services"></a>Déployer votre code dans les services Azure

Utilisez les plug-ins Jenkins pour déployer vos applications sur Azure dans le cadre de vos pipelines d’intégration/de livraison continues Jenkins. Le déploiement dans [Azure App Service](/azure/app-service/) et [Azure Container Service](/azure/container-service/kubernetes/) vous permet d’organiser, de tester et de mettre en production des mises à jour de vos applications sans gestion de l’infrastructure sous-jacente.

 Des plug-ins sont disponibles pour être déployés sur les services et environnements suivants :

- [Azure App Service sur Linux](/azure/app-service/containers/app-service-linux-intro). Consultez le [didacticiel](java-deploy-webapp-tutorial.md) pour commencer.
- [Azure App Service](/azure/app-service/overview). Consultez les [procédures](deploy-Jenkins-app-service-plugin.md) pour commencer.
