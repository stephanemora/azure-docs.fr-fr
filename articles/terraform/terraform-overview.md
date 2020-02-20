---
title: Utilisation de Terraform avec Azure
description: Présentation de l’utilisation de Terraform pour le contrôle de version et le déploiement d’infrastructure Azure.
ms.topic: overview
ms.date: 10/26/2019
ms.openlocfilehash: 05b92fdf8c0a0f84d2f29b4aa7479850b2721441
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472160"
---
# <a name="terraform-with-azure"></a>Terraform avec Azure

[Hashicorp Terraform](https://www.terraform.io/) est un outil open source pour l’approvisionnement et la gestion d’infrastructure cloud. Il codifie l'infrastructure dans des fichiers de configuration qui décrivent la topologie des ressources cloud. Ces ressources incluent les machines virtuelles, les comptes de stockage et les interfaces réseau. L'interface CLI de Terraform fournit un mécanisme simple pour le déploiement et le contrôle de version des fichiers de configuration sur Azure.

Cet article décrit les avantages de l’utilisation de Terraform pour gérer une infrastructure Azure.

## <a name="automate-infrastructure-management"></a>Automatiser la gestion d’infrastructure

Les fichiers de configuration basés sur un modèle de Terraform vous permettent de définir, d’approvisionner et de configurer des ressources Azure de manière reproductible et prévisible. L’automatisation d’infrastructure présente plusieurs avantages :

- Elle réduit le risque d’erreur humaine lors du déploiement et de la gestion d’infrastructure.
- Elle permet de déployer un même modèle plusieurs fois afin de créer des environnements de développement, de test et de production identiques.
- Elle réduit le coût des environnements de développement et de test en permettant de les créer à la demande.

## <a name="understand-infrastructure-changes-before-being-applied"></a>Comprendre les modifications d'infrastructure avant leur application

À mesure qu’une topologie de ressource devient complexe, il peut s’avérer difficile de comprendre la signification et l’impact des modifications de l’infrastructure.

L'interface CLI de Terraform permet aux utilisateurs de valider et de prévisualiser les modifications d'infrastructure avant leur application. La prévisualisation des modifications d'infrastructure de façon sécurisée présente plusieurs avantages :
- Les membres de l’équipe peuvent collaborer plus efficacement parce qu’ils comprennent rapidement les modifications proposées et leur impact.
- Des modifications involontaires peuvent être interceptées tôt dans le processus de développement.

## <a name="deploy-infrastructure-to-multiple-clouds"></a>Déployer une infrastructure vers plusieurs clouds

Terraform peut déployer une infrastructure auprès de plusieurs fournisseurs de services cloud. Il permet aux développeurs d'utiliser des outils cohérents pour gérer chaque définition d'infrastructure.

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez une vue d’ensemble de Terraform et de ses avantages, voici les prochaines étapes suggérées :

- Commencez par [installer Terraform et le configurer pour utiliser Azure](terraform-install-configure.md).
- [Créez une machine virtuelle Azure à l’aide de Terraform](terraform-create-complete-vm.md).
- Explorez le [module Azure Resource Manager pour Terraform](https://www.terraform.io/docs/providers/azurerm/). 