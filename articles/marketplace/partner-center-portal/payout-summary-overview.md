---
title: Vue d’ensemble de la synthèse des revenus – Place de marché Azure
description: La page Récapitulatif des paiements vous montre les détails de l’argent que vous avez gagné avec votre offre. Elle vous permet également de savoir à quels moments vous recevrez les paiements et quels montants vous percevrez.
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 2e7658e965931dd78a6db0e2c9fee36edffff98b
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87479810"
---
# <a name="payout-summary-overview"></a>Vue d’ensemble de la synthèse des revenus

La [synthèse des revenus](./payout-summary.md) montre en détail l’argent que vous avez gagné avec Microsoft. Elle vous permet également de savoir à quels moments vous recevrez les paiements et quels montants vous percevrez.

Si vous vendez des offres sur la Place de marché Azure, la synthèse des revenus vous présente également des informations sur les paiements qui ont bien été effectués. Pour plus d’informations sur le processus de paiement de la Place de marché Azure, consultez [Politiques concernant la participation à la Place de marché Azure](https://docs.microsoft.com/legal/marketplace/participation-policy) et [Contrat d’éditeur de la Place de marché Microsoft Azure](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4xqkx).

> [!NOTE]
> Pour être éligible au paiement, votre produit doit atteindre le [seuil de paiement](./payment-thresholds-methods-timeframes.md) de 50 $. Pour obtenir des détails sur le seuil de paiement, consultez le [Contrat d’éditeur de la Place de marché Microsoft Azure](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4xqkx).

Pour afficher les détails du paiement, connectez-vous à [Espace partenaires](https://partner.microsoft.com/dashboard/home) et sélectionnez l’icône de paiement dans l’angle supérieur droit de l’écran :

![Illustre l’icône Revenu dans l’angle supérieur droit du portail Espace partenaires.](./media/payout-overview.png)

## <a name="roles-and-permissions"></a>Rôles et autorisations

Voici les rôles et les autorisations permettant d’accéder au rapport de revenu :

| Rapports/pages | Propriétaire du compte | Manager | Développeur | Contributeur commercial | Collaborateur financier | Distributeur |
| --- | --- | --- | --- | --- | --- | --- |
| Rapport d’acquisition (comprenant des données en quasi-temps réel) | Affichage | Affichage | Aucun accès | Aucun accès | Affichage | Aucun accès |
| Rapport de commentaires/réponses | Peut afficher et envoyer des commentaires | Peut afficher et envoyer des commentaires | Peut afficher et envoyer des commentaires | Aucun accès | Aucun accès | Peut afficher et envoyer des commentaires |
| --- | --- | --- | --- | --- | --- | --- |
| Rapport d’intégrité (comprenant des données en quasi-temps réel) | Affichage | Affichage | Affichage | Affichage | Aucun accès | Aucun accès |
| Rapport d’utilisation | Affichage | Affichage | Affichage | Affichage | Aucun accès | Aucun accès |
| Compte de paiement | Peut mettre à jour | Aucun accès | Aucun accès | Aucun accès | Peut mettre à jour | Aucun accès |
| Profil fiscal | Peut mettre à jour | Aucun accès | Aucun accès | Aucun accès | Peut mettre à jour | Aucun accès |
| Récapitulatif des paiements | Affichage | Aucun accès | Aucun accès | Aucun accès | Affichage | Aucun accès  |
| | | | | | | |

## <a name="payment-schedules"></a>Calendriers de paiement

Pour plus d’informations sur les calendriers de paiement, notamment les périodes d’attente, la visibilité des partenaires et les cas où le client utilise une carte de crédit ou une facture, consultez la section [Calendriers de paiement](./payout-policy-details.md#payment-schedules) de la rubrique **Détails de paiement**.

## <a name="transaction-history-download-export"></a>Exportation du téléchargement de l’historique des transactions

Cette option permet de télécharger chaque ligne de revenu que vous voyez dans la page Historique des transactions. Elle inclut le type de revenu, la date, le montant de transaction associé, le client, le produit et d’autres détails transactionnels liés au programme d’incentives.

| Nom de la colonne | Description |
| --- | --- |
| earningId | Identificateur unique de chaque revenu |
| participantId | Identité principale du revenu partenaire dans le cadre du programme |
| participantIdType | ID des programmes d’incentives et vendeur si les programmes relèvent du Store et de la Place de marché Azure |
| participantName | Nom du partenaire de revenu |
| partnerCountryCode | Emplacement/pays/région du partenaire de revenu |
| programName | Nom du programme d’incentives/du Store |
| transactionId | Identificateur unique de la transaction |
| transactionCurrency | Devise dans laquelle la transaction cliente d’origine s’est produite (il ne s’agit pas de la devise de l’emplacement du partenaire) |
| transactionDate | Date de la transaction Utile pour les programmes où de nombreuses transactions contribuent à un revenu |
| transactionExchangeRate | Taux de change utilisé pour afficher le montant USD correspondant de la transaction |
| transactionAmount | Montant de la transaction dans la devise de transaction d’origine en fonction du revenu généré |
| transactionAmountUSD | Montant de la transaction en dollars américains (USD) |
| lever | Règle métier pour le revenu |
| earningRate | Taux d’incentives appliqué au montant de la transaction pour générer un revenu |
| quantité | Quantité facturée pour les programmes transactionnels. Varie selon le programme |
| quantityType | Type de quantité, par exemple : Quantité facturée, Utilisateurs actifs mensuels |
| earningType | Indique s’il s’agit de frais, d’une remise, d’une coopération, d’une vente, etc. |
| earningAmount | Montant du revenu dans la devise de la transaction d’origine |
| earningAmountUSD | Montant du revenu en USD |
| earningDate | Date du revenu |
| calculationDate | Date à laquelle le revenu a été calculé dans le système |
| earningExchangeRate | Taux de change utilisé pour afficher le montant USD correspondant |
| exchangeRateDate | Date du taux de change utilisée pour calculer le montant USD du revenu |
| paymentAmountWOTax | Montant du revenu (hors taxe) dans la devise d’arrivée pour les paiements &quot;Envoyés&quot; uniquement |
| paymentCurrency | Devise de destination choisie par le partenaire dans le profil de paiement. Affichée uniquement pour les paiements envoyés |
| paymentExchangeRate | Taux de change utilisé pour calculer paymentAmountWOTax dans la devise de paiement avec ExchangeRateDate |
| paymentId | Identificateur unique du paiement. Ce numéro est visible dans votre relevé bancaire |
| paymentStatus | État du paiement |
| paymentStatusDescription | Description du statut de paiement |
| customerId | Sera toujours vide |
| customerName | Sera toujours vide |
| partNumber | Sera toujours vide |
| ProductName | Nom du produit lié à la transaction |
| productId | Identificateur de produit unique |
| parentProductId | Identificateur de produit parent unique. S’il n’existe aucun produit parent pour la transaction, l’ID du produit parent = ID du produit. |
| parentProductName | Nom du produit parent. S’il n’existe aucun produit parent pour la transaction, le nom du produit parent = Nom du produit. |
| productType | Type de produit (par exemple, application, module complémentaire et jeu) |
| invoiceNumber | Numéro de facture (pour les contrats Entreprise uniquement) |
| resellerId | Identificateur du revendeur |
| resellerName | Nom du revendeur |
| transactionType | Type de transaction (par exemple, achat, remboursement, contrepassation et rétrofacturation) |
| localProviderSeller | Fournisseur local/vendeur d’enregistrement |
| taxRemitted | Montant des taxes versées (ventes, consommation, ou TVA/GST). |
| taxRemitModel | Tiers responsable du versement des taxes (ventes, consommation, ou TVA/GST). |
| storeFee | Montant retenu par Microsoft en guise de frais pour la mise à disposition de l’application ou du module complémentaire sur la Place de marché commerciale. |
| transactionPaymentMethod | Moyen de paiement client utilisé pour la transaction (par exemple, carte, facturation d’opérateur de téléphonie mobile et PayPal) |
| tpan | Réseau publicitaire tiers |
| customerCountry | Pays/Région du client |
| CustomerCity | Ville du client |
| customerState | État du client |
| customerZip | Code postal du client |
| ID de locataire | ID du locataire |
| externalReferenceId | Identificateur unique du programme |
| externalReferenceIdLabel | Étiquette de l’identificateur unique |
| transactionCountryCode | Code du pays/région dans lequel la transaction s’est produite |
| taxCountry | Pays/Région du client |
| taxState | État du client |
| taxCity | Ville du client |
| taxZipCode | Code postal du client |
| LicensingProgramName | Nom du programme de licence |
| Code du programme | Chaîne à mapper avec le nom du programme |
| earningAmountInLastPaymentCurrency | Montant du revenu dans la dernière devise de paiement (ce champ est vide si aucun paiement antérieur n’a été effectué) |
| lastPaymentCurrency | Dernière devise de paiement (ce champ est vide si aucun paiement antérieur n’a été effectué) |
| AssetId | Identificateur unique des commandes du client pour votre service de la place de marché. Il représente les lignes d’achat. Il peut y avoir plusieurs ressources. |
| OrderId | Lié à la facture d’un client |
| LineItemId | Ligne individuelle dans la facture d’un client |
| Pays/Région du client | Nom de pays/région fourni par le client. Il peut être différent du nom du pays ou de la région associé à l’abonnement Azure du client. |
| EmailAddress de client | Adresse e-mail fournie par le client. Elle peut être différente de l’adresse e-mail associée à l’abonnement Azure d’un client. |
| SkuId | ID de la référence SKU, tel que défini lors de la publication. Une offre peut englober de nombreuses références SKU, mais une référence SKU ne peut être associée qu’à une seule offre. |

> [!NOTE]
> Tous les rapports et insights associés à l’option de publication Transaction se trouvent dans la section Analytique de l’Espace partenaires.

## <a name="billing-questions-and-support"></a>Questions et support sur la facturation
Pour obtenir de l’aide sur la facturation, contactez le [support éditeur](https://partner.microsoft.com/support/v2/?stage=1) de la Place de marché commerciale.

## <a name="next-step"></a>étape suivante

- [Synthèse des revenus](./payout-summary.md)
