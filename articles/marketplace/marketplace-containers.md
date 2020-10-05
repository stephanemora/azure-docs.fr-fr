---
title: Guide de publication des offres de conteneur dans la Place de marché Azure
description: Cet article décrit les conditions requises pour publier des conteneur sur la Place de marché Azure.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 09/04/2020
ms.openlocfilehash: c52fabcfc2ff22df2de6dd93f2543d625310baef
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89484339"
---
# <a name="publishing-guide-for-container-offers"></a>Guide de publication des offres de conteneur

Les offres de conteneur vous aident à publier votre image conteneur sur la Place de marché Azure. Utilisez ce guide pour comprendre les exigences propres à cette offre. 

Les offres de conteneur sont des offres de transaction qui sont déployées et facturées via la Place de marché Azure. L’option de référencement qu’un utilisateur voit est « Obtenir maintenant ».

Utilisez le type d’offre *Conteneur* si votre solution est une image conteneur Docker qui est configurée en tant qu’instance de service conteneur Azure basée sur Kubernetes. 

> [!NOTE]
> Parmi les exemples d’instances de service conteneur Azure basées sur Kubernetes, on peut citer Azure Kubernetes Service ou Azure Container Instances, le choix des clients Azure pour un runtime de conteneur basé sur Kubernetes.  

Microsoft prend actuellement en charge les modèles de licence BYOL (apportez votre propre licence) et gratuits.

## <a name="container-offer-requirements"></a>Exigences de l’offre conteneur

| Condition requise | Détails |  
|:--- |:--- |  
| Facturation et mesure | Prise en charge du modèle de facturation gratuit ou BYOL.<br><br> |  
| Image créée à partir d’un Dockerfile | Les images conteneur doivent être basées sur la spécification d’image Docker et générées à partir d’un Dockerfile.<br> <br>Pour plus d’informations sur la création d’images Docker, consultez la section « Utilisation » de la [documentation de référence de Dockerfile](https://docs.docker.com/engine/reference/builder/#usage).<br><br> |  
| Hébergement dans un référentiel Azure Container Registry | Les images conteneur doivent être hébergées dans un référentiel Azure Container Registry.<br> <br>Pour plus d’informations sur l’utilisation d’Azure Container Registry, consultez [Guide de démarrage rapide : Créer un registre de conteneurs privé à l’aide du Portail Azure](../container-registry/container-registry-get-started-portal.md).<br><br> |  
| Balisage d’images | Les images conteneur doivent contenir au moins une balise (nombre maximal de balises : 16).<br><br>Pour plus d’informations sur le balisage d’une image, consultez la page `docker tag` sur le site [Documentation Docker](https://docs.docker.com/engine/reference/commandline/tag).<br><br> |  

## <a name="next-steps"></a>Étapes suivantes

Si vous ne l’avez pas déjà fait, découvrez comment [développer votre activité dans le cloud avec la Place de marché Azure](https://azuremarketplace.microsoft.com/sell).

Pour vous inscrire à l’Espace partenaires et commencer à travailler dessus :

- [Connectez-vous à l’Espace partenaires](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) pour créer ou terminer votre offre.
- Consultez [Créer un conteneur Azure](./partner-center-portal/create-azure-container-offer.md) pour plus d’informations.
