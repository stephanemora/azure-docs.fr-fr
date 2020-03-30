---
title: Utilisation de Chef avec Azure
description: Présentation de l’utilisation de Chef pour configurer et tester votre infrastructure Azure
keywords: azure, chef, devops, machines virtuelles, présentation, automate
ms.date: 02/22/2020
ms.topic: article
ms.openlocfilehash: c22faa69bf8f42111d328a9c156dc1a2432731b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586340"
---
# <a name="using-chef-with-azure"></a>Utilisation de Chef avec Azure
[Chef](https://www.chef.io) est une plateforme d’automatisation puissante qui transforme une infrastructure de machine virtuelle Azure en code. Chef automatise la façon dont l’infrastructure est configurée, déployée et gérée sur votre réseau, quelle que soit sa taille.

Cet article décrit les avantages de l’utilisation de Chef pour gérer une infrastructure Azure.

## <a name="chef-extension-on-azure"></a>Extension Chef sur Azure
Approvisionnez une machine virtuelle avec un client Chef en cours d’exécution en tant que service en arrière-plan avec l’[extension Chef](https://docs.microsoft.com/azure/chef/chef-extension-portal) dans le [portail Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040). Une fois approvisionnées, ces machines virtuelles sont prêtes à être gérées par un serveur Chef.

## <a name="chef-cloud-shell"></a>Cloud Shell Chef
Utilisez la station de travail Chef directement dans Azure Cloud Shell. Exécutez tous les utilitaires Chef et InSpec à partir de Cloud Shell. Vous pouvez utiliser les commandes Chef à partir de :

* [chef](https://docs.chef.io/ctl_chef.html)
* [kitchen](https://docs.chef.io/ctl_kitchen.html)
* [inspec](https://www.inspec.io/docs/reference/cli/)
* [knife](https://docs.chef.io/knife.html)
* [cookstyle](https://docs.chef.io/cookstyle.html)
* [chef-run](https://www.chef.sh/docs/chef-workstation/getting-started/)

Combinez nos utilitaires de commande avec les autres outils disponibles dans Cloud Shell, tels que `git`, `az-cli`, et `terraform`, et écrivez l'automatisation de l'infrastructure et de la conformité à partir du navigateur.

## <a name="automate-infrastructure-apps-and-compliance-with-one-platform"></a>Automatiser l’infrastructure, les applications et la conformité à l’aide d’une même plateforme
Pour être compétitives sur le marché du numérique, les entreprises doivent allier rapidité et sécurité. Chef et Microsoft s’associent pour aider les particuliers, les équipes et les entreprises à atteindre tous ces objectifs. À l’aide d’une seule plateforme, Chef Automate, vous pouvez maintenant automatiser et distribuer de manière continue votre infrastructure, vos applications et assurer la conformité au sein de votre environnement Microsoft.

## <a name="test-drive-chef-automate-on-azure"></a>Test drive de Chef Automate dans Azure
La [solution Chef de la Place de marché Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/chef-software.chef-automate), prise en charge par Chef, vous permet de créer, de déployer et de gérer votre infrastructure et vos applications de façon collaborative. En un clic, vous accédez instantanément à toutes les fonctionnalités commerciales de Chef Automate, vous bénéficiez d’une visibilité de bout en bout de votre flotte, vous garantissez une conformité continue et vous gérez toutes les modifications en un seul flux de travail unifié.

## <a name="next-steps"></a>Étapes suivantes

* [Créer une machine virtuelle Windows dans Azure avec Chef](chef-automation.md)
