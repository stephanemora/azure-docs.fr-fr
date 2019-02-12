---
title: Utilisation de Terraform avec Azure
description: Présentation de l’utilisation de Terraform pour le contrôle de version et le déploiement d’infrastructure Azure.
services: terraform
ms.service: terraform
keywords: terraform, devops, vue d’ensemble, planifier, appliquent, automatiser
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 08/31/2018
ms.openlocfilehash: 962631728f96e0551f9cc18d5d835928e5a7705a
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2019
ms.locfileid: "55567969"
---
# <a name="terraform-with-azure"></a>Terraform avec Azure

[Hashicorp Terraform](https://www.terraform.io/) est un outil open source pour l’approvisionnement et la gestion d’infrastructure cloud. Il codifie un infrastructure dans des fichiers de configuration décrivant la topologie de ressources cloud telles que des machines virtuelles, des comptes de stockage et des interfaces réseau. L’interface de ligne de commande (CLI) de Terraform fournit un mécanisme simple pour le déploiement et le contrôle de version des fichiers de configuration vers Azure ou tout autre cloud pris en charge.

Cet article décrit les avantages de l’utilisation de Terraform pour gérer une infrastructure Azure.

## <a name="automate-infrastructure-management"></a>Automatiser la gestion d’infrastructure

Les fichiers de configuration basés sur un modèle de Terraform vous permettent de définir, d’approvisionner et de configurer des ressources Azure de manière reproductible et prévisible. L’automatisation d’infrastructure présente plusieurs avantages :

- Elle réduit le risque d’erreur humaine lors du déploiement et de la gestion d’infrastructure.
- Elle permet de déployer un même modèle plusieurs fois afin de créer des environnements de développement, de test et de production identiques.
- Elle réduit le coût des environnements de développement et de test en permettant de les créer à la demande.

## <a name="understand-infrastructure-changes-before-they-are-applied"></a>Comprendre les modifications d’infrastructure avant de les appliquer 

À mesure qu’une topologie de ressource devient complexe, il peut s’avérer difficile de comprendre la signification et l’impact des modifications de l’infrastructure.

Terraform fournit une interface de ligne de commande (CLI) qui permet aux utilisateurs de valider et d’afficher un aperçu des modifications de l’infrastructure avant de les déployer. L’affichage d’un aperçu des modifications de l’infrastructure de façon sécurisée et productive présente plusieurs avantages :
- Les membres de l’équipe peuvent collaborer plus efficacement parce qu’ils comprennent rapidement les modifications proposées et leur impact.
- Des modifications involontaires peuvent être interceptées tôt dans le processus de développement.


## <a name="deploy-infrastructure-to-multiple-clouds"></a>Déployer une infrastructure vers plusieurs clouds

Terraform est un choix d’outil populaire pour les scénarios de clouds multiples, où une infrastructure similaire est déployée sur Azure et des fournisseurs de cloud supplémentaires ou des centres de données locaux. Il permet aux développeurs d’utiliser les mêmes outils et fichiers de configuration pour gérer l’infrastructure sur plusieurs fournisseurs de cloud.

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez une vue d’ensemble de Terraform et de ses avantages, voici les prochaines étapes suggérées :

- Commencez par [installer Terraform et le configurer pour utiliser Azure](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure).
- [Créez une machine virtuelle Azure à l’aide de Terraform](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-create-complete-vm).
- Explorez le [module Azure Resource Manager pour Terraform](https://www.terraform.io/docs/providers/azurerm/). 