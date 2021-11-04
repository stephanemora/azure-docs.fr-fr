---
title: Configuration des prix pour la facturation mensuelle dans la Place de marché Azure
description: Découvrez comment configurer les prix pour les machines virtuelles.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshah
ms.author: iqshah
ms.date: 09/28/2021
ms.openlocfilehash: 38eecfefab8bbb8690b8125ad034ca3c3b076a1a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131076525"
---
# <a name="configure-prices-for-usage-based-monthly-billing"></a>Configurer les prix pour une facturation mensuelle à l’usage

Le plan de facturation mensuelle basé sur l’utilisation facture le client en fonction de son utilisation horaire et est facturé tous les mois. Il s’agit de notre plan de « paiement à l’utilisation », où les clients sont facturés uniquement pour les heures qu’ils ont utilisées.
Lorsque vous sélectionnez ce plan, choisissez l’une des options de tarification suivantes :

- **Gratuit** - Votre offre de machine virtuelle est gratuite.
- **Taux fixe (recommandé)** - Votre offre de machine virtuelle est au même prix quel que soit le matériel sur lequel elle fonctionne.
- **Par cœur** - Le prix de votre offre de machine virtuelle est calculé selon le nombre de cœurs de processeur (vous nous donnez le prix d'un cœur de processeur et nous incrémentons le prix en fonction de la taille du matériel).
- **Par taille de cœur** - Attribue des prix en fonction du nombre de cœurs de processeur sur le matériel sur lequel la machine virtuelle est déployée.
- **Par marché et par taille de cœur** - Attribuez des prix en fonction du nombre de cœurs de processeur sur le matériel sur lequel la machine virtuelle déployée et également pour tous les marchés (la conversion des devises est effectuée par vous, l'éditeur ; cette option est plus facile si vous utilisez la fonction d'importation des prix).

Voici quelques éléments à prendre en compte lors de la sélection d’une option de tarification :

- Pour les quatre premières options, Microsoft effectue la conversion monétaire.
- Microsoft suggère d’utiliser un tarif forfaitaire pour les solutions logicielles.
- Les prix sont fixes. Par conséquent, une fois publiés, ils ne peuvent pas être ajustés. Toutefois, si vous souhaitez réduire les prix de vos offres de machine virtuelle, vous pouvez ouvrir un [ticket de support](./support.md).

## <a name="new-offering-pricing"></a>Nouvelle offre de tarification

Microsoft Azure ajoute régulièrement de nouvelles infrastructures de machine virtuelle. Parfois, nous ajoutons un ordinateur dont le nombre de processeurs n’était pas proposé auparavant. Microsoft détermine le prix de la nouvelle taille de cœur en fonction de la tarification précédente et l’ajoute en tant que prix suggéré.

Les éditeurs reçoivent un e-mail lorsque le prix est défini pour les nouvelles tailles de cœur, et ils ont un certain temps pour passer en revue et effectuer des ajustements en fonction des besoins. Après l’échéance, Microsoft publie les prix des tailles de cœur nouvellement ajoutées.

Si l’éditeur a choisi une taille Gratuit, Fixe ou Par cœur, alors le serveur de publication a déjà fourni les détails nécessaires sur la façon de tarifer l’offre pour les nouvelles tailles de cœurs et aucune action supplémentaire n’est nécessaire. Toutefois, si l’éditeur a précédemment sélectionné Taille par cœur, ou Par marché et taille de cœur, il doit contacter Microsoft avec les informations de tarification mises à jour.

## <a name="next-steps"></a>Étapes suivantes

- Si vous avez des questions, ouvrez un ticket auprès du [support](./support.md).