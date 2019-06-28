---
title: Solution VMware de CloudSimple - Vue d’ensemble des machines virtuelles Azure
description: En savoir plus sur les machines virtuelles CloudSimple et sur leurs avantages.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 77a2901ae2d81f42780110b1576c1f32c7ff397b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65209487"
---
# <a name="cloudsimple-virtual-machines-overview"></a>Vue d’ensemble des machines virtuelles CloudSimple

CloudSimple vous permet de gérer des machines virtuelles VMware depuis le Portail Azure.  Un cluster ou une liste de ressources partagées à partir de votre cluster vSphere sont gérés via Azure, grâce à leur mappage à votre abonnement.  La machine virtuelle CloudSimple offre une gestion en libre-service des machines virtuelles VMware depuis le Portail Microsoft Azure.  

Pour créer une machine virtuelle CloudSimple depuis Azure, un modèle de machine virtuelle doit exister sur votre vCenter de cloud privé.  Le modèle est utilisé pour personnaliser le système d’exploitation et les applications.  Le modèle de machine virtuelle peut être renforcé pour répondre aux stratégies de sécurité d’entreprise.  Vous pouvez utiliser le modèle pour créer des machines virtuelles et les utiliser dans le Portail Microsoft Azure à l’aide d’un modèle en libre-service.

## <a name="benefits"></a>Avantages

Les machines virtuelles CloudSimple du Portail Microsoft Azure fournissent un mécanisme en libre-service pour que les utilisateurs puissent créer et gérer des machines virtuelles VMware.

* Créer une machine virtuelle CloudSimple sur votre vCenter de cloud privé
* Gérer les propriétés de la machine virtuelle
  * Ajouter/supprimer des disques
  * Ajouter/supprimer des cartes d’interface réseau
* Opérations d’alimentation de votre machine virtuelle CloudSimple
  * Mise sous tension et mise hors tension
  * Réinitialiser une machine virtuelle
* Supprimer une machine virtuelle

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [Utiliser des machines virtuelles VMware sur Azure](quickstart-create-vmware-virtual-machine.md)
* Découvrez comment [Mapper votre abonnement Azure](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)