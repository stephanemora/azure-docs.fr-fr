---
title: Créer un contrôleur de données Azure Arc dans le portail Azure
description: Créer un contrôleur de données Azure Arc dans le portail Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 04/07/2021
ms.topic: how-to
ms.openlocfilehash: 12d0997e677bcca423f32951e99a6202855104ad
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107030864"
---
# <a name="create-an-azure-arc-data-controller-in-the-azure-portal"></a>Créer un contrôleur de données Azure Arc dans le portail Azure

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="introduction"></a>Introduction

Vous pouvez utiliser le portail Azure pour créer un contrôleur de données Azure Arc.

La plupart des expériences de création pour Azure Arc commencent dans le portail Azure, même si la ressource à créer ou à gérer se trouve en dehors de l’infrastructure Azure. Le modèle d’expérience utilisateur dans ces cas, en particulier lorsqu’il n’existe aucune connexion directe entre Azure et votre environnement, consiste à utiliser le portail Azure pour générer un script qui peut ensuite être téléchargé et exécuté dans votre environnement afin d’établir une connexion sécurisée à Azure. Par exemple, les serveurs activés par Azure Arc suivent ce modèle pour [créer des serveurs activés par Arc](../servers/onboard-portal.md).

Lorsque vous utilisez le mode de connexion indirecte des services de données avec Azure Arc, vous pouvez utiliser le portail Azure pour générer un notebook qui peut ensuite être téléchargé et exécuté dans Azure Data Studio sur votre cluster Kubernetes. 

Lorsque vous utilisez le mode de connexion directe, vous pouvez approvisionner le contrôleur de données directement à partir du portail Azure. Pour en savoir plus, consultez [Modes de connectivité](connectivity.md).

## <a name="use-the-azure-portal-to-create-an-azure-arc-data-controller"></a>Utiliser le portail Azure pour créer un contrôleur de données Azure Arc

Pour créer un contrôleur de données Azure Arc à l’aide du portail Azure et d’Azure Data Studio, procédez comme suit.

1. Commencez par vous connecter à la [place de marché du portail Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/home/searchQuery/azure%20arc%20data%20controller).  Les résultats de la recherche dans la place de marché sont filtrés pour afficher le « contrôleur de données Azure Arc ».
1. Si la première étape n’a pas entré les critères de recherche. Accédez aux résultats de la recherche, puis cliquez sur « Contrôleur de données Azure Arc ».
1. Sélectionnez la vignette du Contrôleur de données Azure de la place de marché.
1. Cliquez sur le bouton **Créer**.
1. Sélectionnez le mode de connexion indirecte. En savoir plus sur les [Modes et exigences de connectivité](./connectivity.md). 
1. Examinez la configuration requise pour créer un contrôleur de données Azure Arc et installez tous les logiciels prérequis manquants, tels qu’Azure Data Studio et kubectl.
1. Cliquez sur le bouton **Suivant : Détails du contrôleur de données**.
1. Choisissez un abonnement, un groupe de ressources et un emplacement Azure comme vous le feriez pour toute autre ressource que vous créeriez dans le portail Azure. Dans ce cas, l’emplacement Azure que vous sélectionnez correspond à l’emplacement où les métadonnées relatives à la ressource seront stockées.  La ressource elle-même sera créée sur l’infrastructure de votre choix. Elle n’a pas besoin d’être sur l’infrastructure Azure.
1. Entrez un nom pour votre contrôleur de données.

1. Sélectionnez un profil de configuration de déploiement.
1. Cliquez sur le bouton **Ouvrir dans Azure Studio**.
1. L’écran suivant affiche un résumé de vos sélections et un bloc-notes généré.  Vous pouvez cliquer sur le bouton **Télécharger le bloc-notes d’approvisionnement** pour télécharger le bloc-notes.

   > [!IMPORTANT]
   > Sur la plateforme de conteneur Azure Red Hat OpenShift ou Red Hat OpenShift, vous devez appliquer la contrainte de contexte de sécurité avant de créer le contrôleur de données. Suivez les instructions de la rubrique [Appliquer une contrainte de contexte de sécurité pour les services de données avec Azure Arc sur OpenShift](how-to-apply-security-context-constraint.md).

1. Ouvrez le bloc-notes dans Azure Data Studio, puis cliquez sur le bouton **Exécuter tout** en haut.
1. Suivez les invites et les instructions du bloc-notes pour terminer la création du contrôleur de données.

## <a name="monitoring-the-creation-status"></a>Surveillance de l’état de la création

La création du contrôleur prend plusieurs minutes. Vous pouvez superviser la progression dans une autre fenêtre de terminal avec les commandes suivantes :

> [!NOTE]
>  Les exemples de commandes ci-dessous supposent que vous avez créé un contrôleur de données et un espace de noms Kubernetes avec le nom « arc ».  Si vous avez utilisé un autre nom pour le contrôleur de données/espace de noms, vous pouvez remplacer « arc » par ce nom.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

Vous pouvez également vérifier l’état de la création de n’importe quel pod en exécutant une commande comme celle qui figure ci-dessous.  C’est particulièrement utile pour résoudre les problèmes.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Résolution des problèmes de création

Si vous rencontrez des problèmes avec la création, consultez le [Guide de résolution des problèmes](troubleshoot-guide.md).