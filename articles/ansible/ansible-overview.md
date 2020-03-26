---
title: Utiliser Ansible avec Azure
description: Présentation de l’utilisation d’Ansible pour automatiser l’approvisionnement du cloud, la gestion de la configuration et le déploiement des applications.
keywords: ansible, azure, devops, présentation, approvisionnement du cloud, gestion de la configuration, déploiement des applications, modules ansible, playbooks ansible
ms.topic: overview
ms.date: 04/30/2019
ms.openlocfilehash: e9d5b8858f052083b157c7d4809fe21018518bcd
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "77193563"
---
# <a name="using-ansible-with-azure"></a>Utiliser Ansible avec Azure

[Ansible](https://www.ansible.com) est un produit open source qui automatise l’approvisionnement du cloud, la gestion de la configuration et le déploiement des applications. Il vous permet d’approvisionner les machines virtuelles, les conteneurs et le réseau, ainsi que des infrastructures cloud complètes. De plus, Ansible vous permet d’automatiser le déploiement et la configuration de ressources dans votre environnement.

Cet article présente certains des avantages de l’utilisation d’Ansible avec Azure.

## <a name="ansible-playbooks"></a>Playbooks Ansible

Les [playbooks Ansible](https://docs.ansible.com/ansible/latest/playbooks.html) vous permettent de demander à Ansible de configurer votre environnement. Les playbooks sont codés à l’aide de YAML afin d’être compréhensibles par l’homme. La section Tutoriels offre de nombreux exemples d’utilisation de playbooks pour installer et configurer les ressources Azure. 

## <a name="ansible-modules"></a>Modules Ansible

Ansible comprend une suite de [modules Ansible](https://docs.ansible.com/ansible/latest/modules_by_category.html) qui sont exécutés directement sur des hôtes distants ou par le biais de [playbooks](https://docs.ansible.com/ansible/latest/playbooks.html). Les utilisateurs peuvent créer leurs propres modules. Les modules sont utilisés pour contrôler les ressources système, telles que les services, les packages ou les fichiers, ou pour exécuter des commandes système.

Pour interagir avec les services Azure, Ansible comprend une suite de [modules cloud Ansible](https://docs.ansible.com/ansible/list_of_cloud_modules.html#azure). Ces modules vous permettent de créer et d’organiser votre infrastructure sur Azure. 

## <a name="migrate-existing-workload-to-azure"></a>Migrer la charge de travail existante vers Azure

Une fois que vous utilisez Ansible pour définir votre infrastructure, vous pouvez appliquer le playbook de votre application permettant à Azure de mettre automatiquement à l’échelle votre environnement en fonction des besoins. 

## <a name="automate-cloud-native-application-in-azure"></a>Automatiser une application cloud native dans Azure

Ansible vous permet d’automatiser des applications cloud natives dans Azure à l’aide des microservices Azure comme [Azure Functions](https://azure.microsoft.com//services/functions/) et [Kubernetes sur Azure](https://azure.microsoft.com/services/container-service/kubernetes/).  

## <a name="manage-deployments-with-dynamic-inventory"></a>Gérer des déploiements avec l’inventaire dynamique

Via sa fonctionnalité [d’inventaire dynamique](https://docs.ansible.com/ansible/intro_dynamic_inventory.html), Ansible vous permet d’extraire l’inventaire des ressources Azure. Vous pouvez ensuite étiqueter vos déploiements Azure existants et les gérer via Ansible.

## <a name="additional-azure-marketplace-options"></a>Autres options de Place de marché Microsoft Azure

L’outil [Ansible Tower](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) est une image Place de marché Azure de Red Hat. 

Ansible Tower est une interface utilisateur et un tableau de bord basés sur le web pour Ansible qui présente les caractéristiques suivantes :

* Vous permet de définir le contrôle d’accès en fonction du rôle, la planification des tâches et la gestion graphique des stocks. 
* Inclut une API REST et une interface CLI pour vous permettre d’insérer Tower dans des outils et des processus existants. 
* Prend en charge la sortie en temps réel des exécutions de playbooks. 
* Chiffre les informations d’identification (comme les clés SSH et Azure) afin que vous puissiez déléguer des tâches sans exposer les informations d’identification.

## <a name="ansible-module-and-version-matrix-for-azure"></a>Matrice de version et module Ansible pour Azure

Ansible comprend une suite de modules à utiliser dans le provisionnement et la configuration des ressources Azure. Ces ressources incluent les machines virtuelles, les groupes identiques, les services réseau et les services de conteneur. La [matrice Ansible](./ansible-matrix.md) liste les modules Ansible pour Azure et les versions d’Ansible dans lesquelles ils sont fournis.

## <a name="next-steps"></a>Étapes suivantes

- [Démarrage rapide : Déployer le modèle de solution Ansible pour Azure sur CentOS](./ansible-deploy-solution-template.md)
- [Démarrage rapide : Configurer des machines virtuelles Linux dans Azure avec Ansible](./ansible-install-configure.md)