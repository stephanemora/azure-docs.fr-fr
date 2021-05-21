---
title: Créer une machine virtuelle en zone avec le portail Azure
description: Créer une machine virtuelle dans une zone de disponibilité avec le portail Azure
documentationcenter: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 5/10/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: 3abbe547b8788d468c2273f037d52c9d61e3f9ce
ms.sourcegitcommit: 1b19b8d303b3abe4d4d08bfde0fee441159771e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109752947"
---
# <a name="create-a-virtual-machine-in-an-availability-zone-using-the-azure-portal"></a>Créer une machine virtuelle dans une zone de disponibilité à l’aide du portail Azure

Cet article aborde l’utilisation du portail Azure pour créer une machine virtuelle dans une zone de disponibilité Azure. Une [zone de disponibilité](../../availability-zones/az-overview.md) est une zone physiquement séparée dans une région Azure. Utilisez les zones de disponibilité pour protéger vos applications et vos données dans l’éventualité peu probable d’une défaillance ou d’une perte d’un centre de données entier.

Pour utiliser une zone de disponibilité, créez votre machine virtuelle dans une [région Azure prise en charge](../../availability-zones/az-region.md).

## <a name="sign-in-to-azure"></a>Connexion à Azure 

1. Connectez-vous au portail Azure sur https://portal.azure.com.

1. Cliquez sur **Créer une ressource** > **Calcul** > **Machine virtuelle**. 

3. Saisissez les informations de la machine virtuelle. Le nom d’utilisateur et le mot de passe vous serviront pour vous connecter à la machine virtuelle. Le mot de passe doit contenir au moins 12 caractères et satisfaire aux [exigences de complexité définies](faq.md#what-are-the-password-requirements-when-creating-a-vm). 

4. Choisissez une région, par exemple USA Est 2, qui prend en charge des zones de disponibilité. 

5. Sous **Options de disponibilité**, sélectionnez la liste déroulante **Zone de disponibilité**. 

1. Sous **Zone de disponibilité**, sélectionnez une zone dans la liste déroulante.
        
4. Choisissez une taille pour la machine virtuelle. Sélectionnez une taille recommandée, ou filtrez sur la base de fonctionnalités. Confirmez que la taille est disponible dans la zone que vous souhaitez utiliser.

6. Finissez de renseigner les informations de votre machine virtuelle. Quand vous avez terminé, sélectionnez **Vérifier + créer**.

7. Une fois les informations vérifiées, sélectionnez **Créer**.

1. Une fois la machine virtuelle créée, vous pouvez voir la zone de disponibilité répertoriée dans la **section Essentials** sur la page de la machine virtuelle.

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Confirmer la zone du disque managé et de l’adresse IP

Lorsque la machine virtuelle est déployée dans une zone de disponibilité, un disque managé pour la machine virtuelle est créé dans la même zone de disponibilité. Par défaut, une adresse IP publique est également créée dans cette zone.

Vous pouvez confirmer les paramètres de zone pour ces ressources dans le portail.  

1. Sélectionnez **Disques** dans le menu de gauche, puis sélectionnez le disque du système d’exploitation. La page du disque comprend des détails sur l’emplacement et la zone de disponibilité du disque.

1. De retour sur la page de la machine virtuelle, sélectionnez l’IP publique. Dans le menu de gauche, sélectionnez **Propriétés**. La page de propriétés inclut des détails sur l’emplacement et sur la zone de disponibilité de l’IP publique.

    
## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à créer une machine virtuelle dans une zone de disponibilité. Apprenez-en davantage sur la [disponibilité](../availability.md) des machines virtuelles Azure.
