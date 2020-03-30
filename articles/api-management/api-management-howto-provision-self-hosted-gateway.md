---
title: Approvisionner une passerelle auto-hébergée dans Gestion des API | Microsoft Docs
description: Découvrez comment approvisionner une passerelle auto-hébergée dans Gestion des API Azure.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: d33c5f75234ad7165a9062ecc3bb2a00d502f8c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74075284"
---
# <a name="provision-a-self-hosted-gateway-in-azure-api-management"></a>Approvisionner une passerelle auto-hébergée dans Gestion des API Azure

L’approvisionnement d’une ressource de passerelle dans votre instance Gestion des API Azure est un composant requis pour le déploiement d’une passerelle auto-hébergée. Cet article décrit les étapes d’approvisionnement d’une ressource de passerelle dans Gestion des API.

> [!NOTE]
> La fonctionnalité de passerelle auto-hébergée est en version préliminaire. Pendant la version préliminaire, la passerelle auto-hébergée est disponible uniquement pour les niveaux Développeur et Premium, sans frais supplémentaires. Le niveau Développeur est limité à un seul déploiement de passerelle auto-hébergée.

## <a name="prerequisites"></a>Conditions préalables requises

Suivez le guide de démarrage rapide suivant : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="provision-a-self-hosted-gateway"></a>Approvisionner une passerelle auto-hébergée

1. Sélectionnez les **Passerelles** sous **Paramètres**.
2. Cliquez sur **+ Ajouter**.
3. Entrez le **Nom** et la **Région** de la passerelle.
> [!TIP]
> **Région** spécifie l’emplacement prévu des nœuds de passerelle qui seront associés à cette ressource de passerelle. Elle est sémantiquement équivalente à une propriété similaire associée à une ressource Azure, mais peut être affectée à une valeur de chaîne arbitraire.

4. Le cas échéant, entrez une **Description** de la ressource de passerelle.
5. Le cas échéant, sélectionnez **+** sous **API** pour associer une ou plusieurs API à la ressource de passerelle.
> [!TIP]
> Vous pouvez associer et supprimer une API d’une passerelle sous l’onglet **Paramètres** de l’API.

> [!IMPORTANT]
> Par défaut, aucune des API existantes n’est associée à la nouvelle ressource de passerelle. Par conséquent, les tentatives d’appels de ces API via la nouvelle passerelle produiront des erreurs de type `404 Resource Not Found`.

6. Cliquez sur **Ajouter**.

La ressource de passerelle a maintenant été approvisionnée dans votre instance Gestion des API. Vous pouvez procéder au déploiement de la passerelle.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la passerelle auto-hébergée, consultez [Vue d’ensemble de la passerelle auto-hébergée Gestion des API](self-hosted-gateway-overview.md)
* Apprenez-en davantage pour [Déployer une passerelle auto-hébergée sur Kubernetes](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
* Apprenez-en davantage sur [Déployer une passerelle auto-hébergée sur Docker](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
