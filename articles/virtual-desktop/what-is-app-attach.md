---
title: Vue d’ensemble de l’attachement d’application MSIX Azure Virtual Desktop – Azure
description: Qu’est-ce que l’attachement d’application MSIX ? Consultez cet article pour le découvrir.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: d55308876db0928bef4fdf3b89ace10305f7c47d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524200"
---
# <a name="what-is-msix-app-attach"></a>Qu’est-ce que l’attachement d’application MSIX ?

MSIX est un nouveau format d’empaquetage qui offre de nombreuses fonctionnalités destinées à améliorer l’expérience d’empaquetage pour toutes les applications Windows. Pour en savoir plus sur MSIX, consultez la [Présentation de MSIX](/windows/msix/overview).

L’attachement d’application MSIX permet de fournir des applications MSIX à des machines physiques et virtuelles. Il est toutefois différent du format MSIX normal, car il est spécialement conçu pour Azure Virtual Desktop. Cet article décrit ce qu’est l’attachement d’application MSIX et ce qu’il peut faire pour vous.

## <a name="application-delivery-options-in-azure-virtual-desktop"></a>Options de distribution d’applications dans Azure Virtual Desktop

Il existe plusieurs moyens de distribuer des applications dans Azure Virtual Desktop :

- Placer des applications dans une image principale
- Utiliser des outils comme SCCM ou Intune pour la gestion centralisée
- Approvisionnement dynamique d’applications (AppV, VMware AppVolumes ou Citrix AppLayering)
- Créer des outils ou des scripts personnalisés à l’aide de Microsoft et d’un outil tiers

## <a name="what-does-msix-app-attach-do"></a>Que fait l’attachement d’application MSIX ?

Dans un déploiement Azure Virtual Desktop, l’attachement d’application MSIX permet d’effectuer différentes actions :

- Créer une séparation entre les données utilisateur, le système d’exploitation et les applications à l’aide de [conteneurs MSIX](/windows/msix/msix-container).
- Éliminer le besoin de réempaquetage lors de la livraison d’applications de manière dynamique.
- Réduire le temps nécessaire pour qu’un utilisateur se connecte.
- Réduire les besoins et les coûts liés à l’infrastructure.

L’attachement d’application MSIX doit être applicable en dehors de l’infrastructure VDI ou SBC.

## <a name="traditional-app-layering-compared-to-msix-app-attach"></a>Couche d’application traditionnelle comparée à l’attachement d’application MSIX

Le tableau suivant compare la fonctionnalité clé de l’attachement d’application MSIX et de la couche d’application.

| Fonctionnalité | Couche d’application traditionnelle  | Attachement d’application MSIX  |
|-----|-----------------------------|--------------------|
| Format               | Les différentes technologies d’application en couches requièrent des formats propriétaires différents. | Fonctionne avec le format d’empaquetage MSIX natif.        |
| Surcharge du réempaquetage | Les formats propriétaires nécessitent le séquencement et le réempaquetage par mise à jour.         | Les applications publiées en tant que MSIX ne nécessitent pas de réempaquetage. Toutefois, si le package MSIX n’est pas disponible, la surcharge de réempaquetage s’applique toujours. |
| Écosystème            | Non applicable (par exemple, les fournisseurs ne livrent pas App-V)  | MSIX est la technologie standard de Microsoft adoptée par des partenaires ISV clés et des applications internes comme Office. Vous pouvez utiliser MSIX sur les bureaux virtuels et les ordinateurs Windows physiques. |
| Infrastructure       | Infrastructure supplémentaire requise (serveurs, clients, etc.) | Stockage uniquement   |
| Administration       | Nécessite une maintenance et une mise à jour   | Simplifie les mises à jour des applications |
| Expérience utilisateur      | Affecte le temps de connexion de l’utilisateur. Une limite existe entre l’état du système d’exploitation, l’état de l’application et les données utilisateur.  | Les applications livrées ne peuvent pas être distinguées des applications installées localement. |

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez en savoir plus sur l’attachement d’application MSIX, consultez notre [Glossaire](app-attach-glossary.md) et notre [FAQ](app-attach-faq.yml). Sinon, commencez avec [Configurer l’attachement d’application](app-attach.md).
