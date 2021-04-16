---
title: Créer un nom de domaine complet pour une machine virtuelle sur le Portail Azure
description: Apprenez à créer un nom de domaine complet (FQDN) pour une machine virtuelle dans le portail Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/01/2021
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b010070b7a45c24037c6de4648574c01b017d759
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107395"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Créer un nom de domaine complet dans le portail Azure pour une machine virtuelle Linux

Lorsque vous créez une machine virtuelle dans le [portail Azure](https://portal.azure.com), une ressource d’adresse IP publique est créée automatiquement pour la machine virtuelle. Vous utilisez cette adresse IP pour accéder à distance à la machine virtuelle. Bien que le portail ne crée pas de [nom de domaine complet](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN), vous pouvez en ajouter un après la création de la machine virtuelle. Cet article explique les étapes pour créer un nom DNS ou un nom de domaine complet. 

## <a name="create-a-fqdn"></a>Créer un nom de domaine complet
Cet article suppose que vous avez déjà créé une machine virtuelle. Si nécessaire, vous pouvez créer une machine virtuelle [Linux](./linux/quick-create-portal.md) ou [Windows](./windows/quick-create-portal.md) dans le portail. Une fois que votre machine virtuelle est en cours d’exécution, procédez comme suit :


1. Sélectionnez votre machine virtuelle dans le portail. 
1. Dans le menu de gauche, sélectionnez **Propriétés**.
1. Sous **Adresse IP publique\Étiquette du nom DNS**, sélectionnez votre adresse IP.
2. Sous **Étiquette du nom DNS**, entrez le préfixe à utiliser.
3. En haut de la page, sélectionnez **Enregistrer**.
4. Sélectionnez **Vue d'ensemble** dans le menu de gauche pour revenir à la vue d'ensemble de la machine virtuelle.
5. Vérifiez que le **nom DNS** s’affiche correctement. 

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez aussi gérer DNS avec des [zones DNS Azure](../dns/dns-getstarted-portal.md).

