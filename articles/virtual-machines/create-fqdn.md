---
title: Créer un nom de domaine complet pour une machine virtuelle sur le Portail Azure
description: Apprenez à créer un nom de domaine complet (FQDN) pour une machine virtuelle dans le portail Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 1/12/2020
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aadf4000da4ab88a3c3b4dee37d2179eb49d39d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98132062"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Créer un nom de domaine complet dans le portail Azure pour une machine virtuelle Linux

Lorsque vous créez une machine virtuelle dans le [portail Azure](https://portal.azure.com), une ressource d’adresse IP publique est créée automatiquement pour la machine virtuelle. Vous utilisez cette adresse IP pour accéder à distance à la machine virtuelle. Bien que le portail ne crée pas de [nom de domaine complet](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN), vous pouvez en ajouter un après la création de la machine virtuelle. Cet article explique les étapes pour créer un nom DNS ou un nom de domaine complet. 

## <a name="create-a-fqdn"></a>Créer un nom de domaine complet
Cet article suppose que vous avez déjà créé une machine virtuelle. Si nécessaire, vous pouvez créer une machine virtuelle [Linux](./linux/quick-create-portal.md) ou [Windows](./windows/quick-create-portal.md) dans le portail. Une fois que votre machine virtuelle est en cours d’exécution, procédez comme suit :


1. Sélectionnez votre machine virtuelle dans le portail. 
1. Dans le menu de gauche, sélectionnez **Configuration**
1. Sous **Étiquette du nom DNS**, entrez le préfixe à utiliser.
1. En haut de la page, sélectionnez **Enregistrer**.
1. Revenez au panneau de la vue d’ensemble de la machine virtuelle en sélectionnant **Vue d’ensemble** dans le menu de gauche. 
1. Vérifiez que le *nom DNS* s’affiche correctement. 

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez aussi gérer DNS avec des [zones DNS Azure](../dns/dns-getstarted-portal.md).

