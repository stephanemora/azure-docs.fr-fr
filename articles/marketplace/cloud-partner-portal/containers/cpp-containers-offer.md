---
title: Offre d’image de conteneurs Azure | Place de marché Azure
description: Vue d’ensemble du processus de publication d’une offre de conteneur sur la Place de marché Microsoft Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: dsindona
ms.openlocfilehash: 9c0b4ca6e9a26f13d1539845ca9fb43f31a1a9dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281712"
---
# <a name="containers"></a>Containers

<table> <tr> <td>Cette section explique comment publier une image de conteneur sur la <a href="https://azuremarketplace.microsoft.com">Place de marché Azure</a>.  
Le type d’offre de conteneur prend en charge les images de conteneurs Docker provisionnées en tant qu’instances <a href="https://docs.microsoft.com/azure/aks/index">Azure Kubernetes Service</a> ou <a href="https://docs.microsoft.com/azure/container-instances/container-instances-overview">Azure Container Instances</a> et hébergées dans un référentiel <a href="https://docs.microsoft.com/azure/container-registry">Azure Container Registry </a>. </td> <td><img src="./media/container-icon.png"  alt="Azure container icon" /></td> </tr> </table>

## <a name="offer-components"></a>Composants de l’offre

Cette section décrit les éléments de la publication d’un conteneur et est conçue comme un guide de publication pour la Place de marché Microsoft Azure. La publication comprend les parties principales suivantes :

- [Conditions préalables](./cpp-prerequisites.md) : répertorie les exigences techniques et commerciales avant de créer ou de publier une offre de conteneur.
- [Créer l’offre](./cpp-create-offer.md) : répertorie les étapes requises pour créer une offre de conteneur à l’aide du portail Microsoft Cloud Partner.
- [Préparer les ressources techniques](./cpp-create-technical-assets.md) : comment créer les ressources techniques pour une solution de conteneur en tant qu’offre sur la Place de marché Azure.
- [Publier l’offre](./cpp-publish-offer.md) : explique comment publier l’offre sur la Place de marché Microsoft Azure.

## <a name="container-publishing-process"></a>Processus de publication de conteneur

Le schéma suivant illustre les étapes principales de la publication d’une offre de machine virtuelle.
![Étapes de publication d’une offre](./media/containers-offer-process.png)

Les étapes principales pour la publication d’une offre de conteneur sont les suivantes :

1. Créer l’offre : fournissez des informations détaillées sur l’offre. Ces informations incluent : la description de l’offre, les documents marketing, les informations de prise en charge et les spécifications des ressources.
2. Créer l’activité et les ressources techniques : créez les ressources de l’activité (documents juridiques et supports marketing) et les ressources techniques de la solution associée (images de conteneurs hébergées dans un registre Azure Container Registry).
3. Créer la référence SKU : créez la ou les références SKU associées à l’offre. Chaque image que vous prévoyez de publier doit avoir une référence SKU unique.
4. Certifier et publier l’offre : une fois l’offre et les ressources techniques finalisées, vous pouvez envoyer l’offre. Cette soumission commence le processus de publication. Pendant ce processus, la solution est testée, validée et certifiée, puis publiée sur la Place de marché Microsoft Azure.

## <a name="next-steps"></a>Étapes suivantes

Avant d’envisager ces étapes, vous devez respecter les [exigences techniques et commerciales](./cpp-prerequisites.md) de la publication d’un conteneur sur la Place de marché Microsoft Azure.
