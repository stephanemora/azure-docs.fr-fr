---
title: FAQ sur Seller Insights
description: Forum Aux Questions sur la fonctionnalité Seller Insights du portail Cloud Partner.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 011558baa43ee3db2803e9229d1d15df5158d668
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285382"
---
<a name="seller-insights-faq"></a>FAQ sur Seller Insights
===================

Cet article contient des conseils relatifs aux procédures utilisateur courantes à effectuer dans Seller Insights, ainsi que des réponses aux questions relatives à cette fonctionnalité.


<a name="find-definitions-for-the-values-in-the-downloaded-transaction-file"></a>Recherche des définitions correspondant aux valeurs du fichier téléchargé de transactions
------------------------------------------------------------------

Les définitions des valeurs métriques du fichier de transactions se trouvent dans l’article [Seller Insights Definitions](./si-insights-definitions-v4.md) (Définitions Seller Insights).


<a name="see-customer-details-of-transactions-for-which-ive-been-paid"></a>Consultation des détails des transactions des clients pour lesquelles j’ai été payé
-------------------------------------------------------------

Après avoir téléchargé vos transactions à partir du module Payout (Revenu), recherchez la colonne intitulée **Payout Status** (État de paiement), puis appliquez le filtre pour afficher uniquement la valeur « Paid Out » (Payé). Les colonnes suivantes affichent les détails du client : **Nom de l’entreprise**, **Adresse e-mail du client**, **Pays du client**, **État du client**, et **Code postal du client**.


<a name="calculate-my-open-accounts-receivable"></a>Calcul de mes comptes clients ouverts
-------------------------------------

Après avoir téléchargé vos transactions à partir du module Payout (Revenu), recherchez la colonne intitulée **Payout Status** (État de paiement), puis appliquez le filtre pour afficher uniquement les valeurs « Upcoming Payout » (Paiement à venir) et « Not Ready for Payout » (Non prêt au paiement). Additionnez ensuite le contenu de la colonne intitulée **Montant du paiement (DE)** .


<a name="calculate-revenue-by-customer-usage-period"></a>Calcul du revenu par période d’utilisation de client
------------------------------------------

Après avoir téléchargé vos transactions à partir du module Payout (Revenu), recherchez la colonne intitulée **Transaction Status** (État de transaction), puis appliquez le filtre pour afficher uniquement la valeur « Paid » (Payé).   Pour chaque transaction répertoriée, la colonne intitulée **Montant du paiement (DE)** représente le montant qui vous a été payé.  Pour estimer la période d’utilisation associée à la transaction, utilisez la colonne **Date de facturation**, qui est une approximation du dernier jour d’utilisation de la période à laquelle s’applique la transaction.


<a name="calculate-your-bad-debt"></a>Calcul de votre créance irrécouvrable
---------------------

Après avoir téléchargé vos transactions à partir du module Payout (Revenu), recherchez la colonne intitulée **Final Collection Status** (État final de la collecte), puis appliquez le filtre pour afficher uniquement la valeur « Write Off » (Annuler). Additionnez ensuite le contenu de la colonne intitulée **Montant du paiement (DE)** .


<a name="view-payout-or-customer-contact-information"></a>Affichage des coordonnées des clients ou des informations relatives aux revenus
-------------------------------------------

Connectez-vous en tant qu’utilisateur disposant du rôle Propriétaire et non Collaborateur. Seul le rôle Propriétaire affiche les informations relatives aux revenus et aux clients. Pour en savoir plus sur les rôles d’utilisateur, consultez l’article [Manage users](./cloud-partner-portal-manage-users.md) (Gestion des utilisateurs).


<a name="calculate-my-advance-payouts"></a>Calcul de mes paiements avancés
----------------------------

Après avoir téléchargé vos transactions à partir du module Payout (Revenu), recherchez la colonne intitulée **Transaction Type** (Type de transaction), puis appliquez le filtre pour afficher uniquement la valeur « Charge » (Facturer). Recherchez ensuite la colonne intitulée **Final Collection Status** (État final de la collecte), puis appliquez le filtre pour afficher uniquement la valeur « In Progress » (En cours). Pour finir, additionnez le contenu de la colonne **Montant du paiement (DE)** pour calculer toutes les avances qui vous ont été payées avant que la collecte ne soit effectuée auprès du client.


<a name="calculate-customer-refunds"></a>Calcul des remboursements client
--------------------------

Après avoir téléchargé vos transactions à partir du module Payout (Revenu), recherchez la colonne intitulée **Final Collection Status** (État final de la collecte), puis appliquez le filtre pour afficher uniquement la valeur « Refund » (Rembourser). Additionnez le contenu de la colonne **Montant des frais (DE)** pour calculer tous les remboursements traités pour vos clients.


<a name="identify-which-transactions-involved-a-microsoft-channel-partner"></a>Identification des transactions impliquant un partenaire Microsoft Channel Partner
----------------------------------------------------------------

Toutes les transactions indiquées dans la colonne **Azure License Type** (Type de licence Azure) et filtrées pour afficher les valeurs « Enterprise through Reseller » (Entreprise par l’intermédiaire d’un revendeur) et « Cloud Solution Provider » (Fournisseur de solutions Cloud) impliquent un partenaire de distribution Microsoft. Pour plus d’informations sur le partenaire, vous pouvez rechercher ses **Nom de revendeur** et **Adresse e-mail de revendeur** dans les modules Payout (Revenu) et Client téléchargés.


<a name="identify-trial-usage-and-trial-conversions"></a>Identification des utilisations et conversions des versions d’évaluation
------------------------------------------

Les modules téléchargés Commande, Utilisation et Payout (Revenu) contiennent désormais la colonne **Date de fin d’essai** pour vous aider à comprendre la date de fin de la période d’évaluation correspondant à cette commande spécifique, le cas échéant. Pour afficher l’utilisation des versions d’évaluation et les commandes, recherchez la colonne **SKU Billing Type** (Type de facturation des références SKU) dans les modules téléchargés, puis appliquez le filtre pour afficher uniquement la valeur « Trial » (Version d’évaluation). Pour afficher les conversions des versions d’évaluation, recherchez la colonne **Trial End Date** (Date de fin d’essai) dans les modules téléchargés, puis appliquez le filtre pour afficher uniquement les commandes pour lesquelles **Trial End Date** (Date de fin d’essai) est postérieure à la date du jour et la colonne **Cancel Date** (Date d’annulation) est vide ou postérieure à **Trial End Date** (Date de fin d’essai).


<a name="when-is-my-monthly-payout-calculated"></a>Date de calcul du revenu mensuel
------------------------------------

Vos revenus vous sont remis au plus tard le 15 de chaque mois pour tous les montants prêts à être payés au dernier jour civil du mois précédent. Le troisième jour du mois, Microsoft calcule le montant du revenu du mois précédent et met à jour toutes les transactions facturées applicables dans votre module téléchargé avec la valeur « Upcoming Payout » (Paiement à venir) indiquée dans la colonne **Payout Status** (État de paiement). Ces transactions restent définies sur cet état tant que la demande de paiement n’a pas été envoyée à votre compte bancaire. À cet instant, leur valeur **Payout Status** (État de paiement) est mise à jour sur « Paid Out » (Payé), et la colonne « Date de paiement » est mise à jour de façon à indiquer la date à laquelle nous avons envoyé la demande de paiement à votre banque.


<a name="calculate-customer-acquisition-and-loss"></a>Calcul de l’acquisition et de la perte de clients
---------------------------------------

Pour consulter la date à laquelle un client a acheté pour la première fois l’une de vos offres, recherchez la colonne **Date d’acquisition** dans le module Client téléchargé. De même, vous pouvez consulter la date après laquelle un client n’a plus acquis aucune de vos offres en recherchant la colonne **Date Lost** (Date de perte) dans le module Client téléchargé.


<a name="finding-more-help"></a>Aide complémentaire
-----------------

- [Définitions Seller Insights](./si-insights-definitions-v4.md) : rechercher les définitions des données et des métriques

- [Getting started with Seller Insights](./si-getting-started.md) (Prise en main de Seller Insights) : présentation de la fonctionnalité Seller Insights.

