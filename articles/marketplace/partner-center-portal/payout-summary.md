---
title: Récapitulatif des paiements de la place de marché commerciale | Place de marché Azure
description: La page Récapitulatif des paiements vous montre les détails de l’argent que vous avez gagné avec votre offre. Elle vous permet également de savoir quand vous recevrez des paiements et le montant que vous allez percevoir.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.topic: guide
ms.date: 12/10/2019
ms.openlocfilehash: 5a930dfb38007349155581424d03ee7b3e7a6b46
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730200"
---
# <a name="payout-reporting"></a>Rapports de paiement

La page [**Récapitulatif des paiements**](https://docs.microsoft.com/windows/uwp/publish/payout-summary) montre les détails de l’argent que vous avez gagné avec Microsoft. Elle vous permet également de savoir quand vous recevrez des paiements et le montant que vous allez percevoir.

Si vous vendez des offres sur la Place de marché Azure, **Récapitulatif des paiements** vous présente également des informations sur les paiements qui vous ont été versés. Pour plus d’informations sur le processus de paiement de la Place de marché Azure, consultez [Politiques concernant la participation à la Place de marché Microsoft Azure](https://go.microsoft.com/fwlink/p/?LinkId=722436) et [Contrat d’éditeur de la Place de marché Microsoft Azure](https://go.microsoft.com/fwlink/p/?LinkID=699560).

> [!NOTE]
> Pour être éligible au paiement, votre produit doit atteindre le [seuil de paiement](payment-thresholds-methods-timeframes.md) de 50 $. Pour plus d’informations sur le seuil de paiement, consultez cette page pour prendre connaissance du [Contrat d’éditeur de la Place de marché Microsoft Azure](https://go.microsoft.com/fwlink/p/?LinkID=699560).

- [Rôles et autorisation d'accès au rapport de paiement](#roles-and-permission-to-access-the-payout-report)
- [Rapport de paiement : différence entre le portail Cloud Partner et l’Espace partenaires](#payout-report-difference-between-cloud-partner-portal-and-partner-center)
- [Types de clients](#customer-types)
- [Relation entre le paiement et l’utilisation](#corelation-between-payout-and-usage)
- [Téléchargement de l’historique des transactions](#transaction-history-download-export)
- [Questions et support sur la facturation](#billing-questions-and-support)

## <a name="roles-and-permission-to-access-the-payout-report"></a>Rôles et autorisation d'accès au rapport de paiement

| Rapports/pages    | Propriétaire du compte    | Manager  | Développeur | Contributeur commercial |  Collaborateur financier | Distributeur |
|------------------|------------------|----------|-----------|----|----|-----|
| Rapport d'acquisition (y compris les données en temps quasi réel) | Affichage | Affichage | Aucun accès | Aucun accès | Affichage | Aucun accès |
| Rapport de commentaires/réponses | Peut afficher et envoyer des commentaires | Peut afficher et envoyer des commentaires | Peut afficher et envoyer des commentaires | Aucun accès | Aucun accès | Peut afficher et envoyer des commentaires |
| Rapport d’intégrité (y compris les données en temps quasi réel) | Affichage | Affichage | Affichage | Affichage | Aucun accès | Aucun accès |
| Rapport d’utilisation | Affichage | Affichage | Affichage | Affichage | Aucun accès | Aucun accès |
| Compte de paiement | Peut mettre à jour | Aucun accès | Aucun accès | Aucun accès | Peut mettre à jour | Aucun accès |
| Profil fiscal | Peut mettre à jour | Aucun accès | Aucun accès | Aucun accès | Peut mettre à jour | Aucun accès |
| Récapitulatif des paiements | Affichage | Aucun accès | Aucun accès | Aucun accès | Affichage | Aucun accès |

## <a name="payout-report-difference-between-cloud-partner-portal-and-partner-center"></a>Rapport de paiement : différence entre le portail Cloud Partner et l’Espace partenaires

| | Portail des partenaires cloud | Espace partenaire |
|---------|---------|---------|
| Liens | [https://cloudpartner.azure.com/](https://cloudpartner.azure.com) | [https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory](https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory) et [https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments](https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments) |
| Navigation | Rapports de paiement fournis dans le paiement des insights | Rapports de paiement fournis dans l’Espace partenaires – Icône de paiement |
| Étendue | <ul> <li>La transaction par article est visible, quand la collecte en cours, quand elle est terminée et quand elle est payée </li> <li>Rapports : montre tous les articles une fois que le bon de commande est créé, notamment la collecte et la facturation en cours, ainsi que l’état de la collecte et les articles qui ne peuvent pas encore être payés. </li> </ul> | <ul> <li>Montre les articles une fois qu’ils sont considérés comme des revenus éligibles.</li> <li>Les clients paient d’abord Microsoft, puis les éditeurs de logiciels indépendants peuvent voir le rapport de paiement commencer.</li> <li>Le rapport de paiement ne montre pas la collecte ni la facturation en cours.  </li> </ul>  |
| Transaction non prête pour le paiement | Facturation en cours | Estimation du prochain paiement : Le paiement est dans l’état non traité.  |
| État de paiement |  | Non traité : <br> Le revenu est éligible au paiement. Il reste dans cet état pendant une période de réflexion telle que définie dans le guide du programme d’incentives. <br> <br> Ensuite : <br> Des vérifications internes en attente sont générées par l’ordre de paiement avant le traitement du paiement. <br> <br> Envoyé : <br> Le paiement a été envoyé à votre banque. |

## <a name="customer-types"></a>Types de clients

### <a name="enterprise-agreement"></a>Contrat Entreprise

Les clients sans Contrat Entreprise sont facturés au mois pour les licences logicielles de la Place de marché. Les clients ayant un Contrat Entreprise sont facturés au mois, mais reçoivent une seule facture par trimestre.

### <a name="credit-cards-and-monthly-invoice"></a>Cartes de crédit et facture mensuelle

Les clients peuvent également payer à l’aide d’une carte de crédit et d’une facture mensuelle. Dans ce cas, vos frais de licence logicielle sont facturés tous les mois.

### <a name="csp-and-direct-pay-users"></a>CSP et utilisateurs à paiement direct

Exemple d’un client qui fait un achat avec une carte de crédit.

## <a name="corelation-between-payout-and-usage"></a>Relation entre le paiement et l’utilisation

|Description    |    Date  | Commandes/utilisation  | Paiement |
|----------|----------|-----------|-------------|
|Période de commande   | 15 août 2019 au 30 août 2019 | **Commandes des attributs de corrélation** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **Utilisation** <br> <ul> <li>CustomerId </li> <li>Nom du client</li> <li>(UsageReference)PurchaseRecordId/LineItemId</li> <li> Estimation des frais d’extension <br> Paiement estimé (DE) </li> </ul> |  |
|Échéance (mois)   | 30 août 2019 | | |
|Date de facturation | 1er septembre 2019 | | |
|Date de paiement du client | 1er septembre 2019 | | |
|Période de dépôt (cartes de crédit uniquement, 30 jours) | 1er septembre 2019 au 30 septembre 2019 | | **Commandes des attributs de corrélation :** <br> <ul><li>AssetId</li> <li>ID de client</li> <li> Nom du client</li> </ul> <br> **Utilisation** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Nom du client</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **État de paiement :** Non traité |
|Début de la période de collecte | 1er septembre 2019 | | |
|Fin de la période de collecte (30 jours au maximum) | 30 septembre 2019 | | |
|Date de calcul du paiement (tous les mois le 15) | 1er octobre 2019 | | **Attributs de corrélation** <br> <ul><li>AssetId</li> <li>ID de client</li> <li>Nom du client</li> </ul> <br> **Utilisation** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Nom du client</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **État de paiement :** UpComing |
|Date de paiement | 15 octobre 2019 | | **Attributs de corrélation** <br> <ul><li>AssetId</li> <li>ID de client</li> <li> Nom du client</li> </ul> <br> **Utilisation** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Nom du client</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **État de paiement :** Paiement envoyé |

### <a name="enterprise-agreement-quarterlymonthly-customers"></a>Contrat Entreprise (clients trimestriels/mensuels)

| Description |    Date  | Usage | Paiement |
|----------|----------|---------|-----------|
|Période de commande | 15 août 2019 au 30 août 2019 | **Commandes des attributs de corrélation** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **Rapport d’utilisation** <br> <ul> <li>CustomerId </li> <li>Nom du client</li> <li>(UsageReference)PurchaseRecordId/LineItemId</li> <li> Estimation des frais d’extension <br> Paiement estimé (DE) </li> </ul> | |
|Échéance (trimestre) | 30 septembre 2019 | | |
|Date de facturation | 15 octobre 2019 | | |
|Période de dépôt (cartes de crédit uniquement, 30 jours) | n/a | | |
|Début de la période de collecte | 15 octobre 2019 | | |
|Cartes de crédit uniquement, 30 jours | 1er novembre 2019 au 30 novembre 2019 | | |
|Fin de la période de collecte (90 jours au maximum) | 15 janvier 2020 | | |
|Date de paiement du client | 30 décembre 2019 | | |
|Calcul du paiement | 15 janvier 2020 | | |
|Date de paiement | 15 février 2020 | | **Pour les clients basés sur le trimestre** <br> <br> **Rapport sur les commandes** <br> <ul><li>AssetId</li> <li>ID de client</li> <li> Nom du client</li> </ul> <br> **Utilisation** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Nom du client</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **État de paiement :** envoyé |

## <a name="transaction-history-download-export"></a>Exportation du téléchargement de l’historique des transactions

Cette option permet de télécharger chaque article de revenu que vous voyez dans la page d’historique des transactions, le type de revenu, la date, le montant associé de la transaction, le client, le produit et d’autres détails transactionnels applicables au programme d’incentives.

| Nom de la colonne     | Description    |
|-------------|-------------------------------|
| earningId                      | Identificateur unique de chaque revenu                                                                                                       |
| participantId                  | Identité principale du revenu partenaire dans le cadre du programme                                                                            |
| participantIdType              | Principalement, ID des programmes d’incentives et ID de vendeur pour les programmes du Store et la Place de marché Azure                                          |
| participantName                | Nom du partenaire de revenu                                                                                                              |
| partnerCountryCode             | Emplacement/pays du partenaire de revenu                                                                                                  |
| programName                    | Nom du programme d’incentives/du Store                                                                                                             |
| transactionId                  | Identificateur unique de la transaction                                                                                                    |
| transactionCurrency            | Devise dans laquelle la transaction cliente d’origine s’est produite (il ne s’agit pas de la devise de l’emplacement du partenaire)                                     |
| transactionDate                | Date de la transaction Utile pour les programmes où de nombreuses transactions contribuent à un revenu                                           |
| transactionExchangeRate        | Taux de change utilisé pour afficher le montant USD correspondant de la transaction                                                                 |
| transactionAmount              | Montant de la transaction dans la devise de transaction d’origine en fonction du revenu généré                                              |
| transactionAmountUSD           | Montant de la transaction en USD                                                                                                                |
| lever                          | Indique une règle métier pour le revenu                                                                                                  |
| earningRate                    | Taux d’incentives appliqué au montant de la transaction pour générer un revenu                                                                      |
| quantité                       | Varie selon le programme. Indique la quantité facturée pour les programmes transactionnels                                                            |
| quantityType                   | Indique le type de quantité, par exemple : Quantité facturée, utilisateurs actifs mensuels                                                                                     |
| earningType                    | Indique s’il s’agit de frais, de remise, de coop, de vente, etc.                                                                                          |
| earningAmount                  | Montant du revenu dans la devise de la transaction d’origine                                                                                      |
| earningAmountUSD               | Montant du revenu en USD                                                                                                                    |
| earningDate                    | Date du revenu                                                                                                                      |
| calculationDate                | Date à laquelle le revenu a été calculé dans le système                                                                                            |
| earningExchangeRate            | Taux de change utilisé pour afficher le montant USD correspondant                                                                                  |
| exchangeRateDate               | Date du taux de change utilisée pour calculer le montant USD du revenu                                                                                   |
| paymentAmountWOTax             | Montant du revenu (hors taxe) dans la devise de destination pour les paiements « envoyés » uniquement                                                                 |
| paymentCurrency                | Devise de destination choisie par le partenaire dans le profil de paiement. Affichée uniquement pour les paiements envoyés                                                   |
| paymentExchangeRate            | Taux de change utilisé pour calculer paymentAmountWOTax dans la devise de paiement avec ExchangeRateDate                                            |
| paymentId            | Identificateur unique du paiement. Ce numéro est visible dans votre relevé bancaire                                            |
| paymentStatus            | État du paiement                                            |
| paymentStatusDescription            | Description conviviale de l’état du paiement                                            |
| customerId                     | Sera toujours vide                                                                                                                     |
| customerName                   | Sera toujours vide                                                                                                                     |
| partNumber                     | Sera toujours vide                                                                                                                     |
| ProductName                    | Nom de produit lié à la transaction                                                                                                       |
| productId                      | Identificateur de produit unique                                                                                                                |
| parentProductId                | Identificateur de produit parent unique. Remarque : s’il n’existe aucun produit parent pour la transaction, l’ID du produit parent = ID du produit. |
| parentProductName              | Nom du produit parent. Remarque : s’il n’existe aucun produit parent pour la transaction, le nom du produit parent = Nom du produit.   |
| productType                    | Type de produit (par exemple application, module complémentaire, jeu, etc.)                                                                                        |
| invoiceNumber                  | Numéro de facture (applicable à EA uniquement)                                                                                                  |
| resellerId                     | Identificateur du revendeur                                                                                                                      |
| resellerName                   | Nom du revendeur                                                                                                                            |
| transactionType                | Type de transaction (achat, remboursement, contrepassation, rétrofacturation, etc.)                                                               |
| localProviderSeller            | Fournisseur local/vendeur d’enregistrement                                                                                                          |
| taxRemitted                    | Montant des taxes versées (ventes, consommation, ou TVA/GST).                                                                                   |
| taxRemitModel                  | Tiers responsable du versement des taxes (ventes, consommation, ou TVA/GST).                                                                    |
| storeFee                       | Montant retenu par Microsoft comme frais pour rendre l’application ou le module complémentaire disponible dans le Store.                                            |
| transactionPaymentMethod       | Moyen de paiement client utilisé pour la transaction (par exemple carte, facturation d’opérateur de téléphonie mobile, PayPal, etc.)                                |
| tpan                           | Indique le réseau publicitaire tiers                                                                                                     |
| customerCountry                | Pays du client                                                                                                                         |
| CustomerCity                   | Ville du client                                                                                                                            |
| customerState                  | État du client                                                                                                                           |
| customerZip                    | Code postal du client                                                                                                                 |
| ID de locataire                       |                                                                                                                                          |
| externalReferenceId            | Identificateur unique du programme                                                                                                        |
| externalReferenceIdLabel       | Étiquette de l’identificateur unique                                                                                                                  |
| transactionCountryCode       | Code du pays dans lequel la transaction s’est produite                                                                                                                  |
| taxCountry       | Pays du client de la vente                                                                                                                  |
| taxState       | État du client de la vente                                                                                                                  |
| taxCity       | Ville du client de la vente                                                                                                                  |
| taxZipCode       | Code postal du client de la vente                                                                                                                  |
| LicensingProgramName       |                                                                                                                   |
| Code du programme       | Chaîne à mapper avec le nom du programme                                                                                                                   |
| earningAmountInLastPaymentCurrency       | Montant du revenu dans la dernière devise de paiement (ce champ sera vide si aucun paiement précédent n’a été effectué)                                                                                                                   |
| lastPaymentCurrency       | Dernière devise de paiement (ce champ sera vide si aucun paiement précédent n’a été effectué)                                                                                                                   |
| AssetId       | Identificateur unique des commandes du client pour votre service de la place de marché.  Il représente les articles achetés traités. Il peut y avoir plusieurs ressources.                                                                                                                   |
| OrderId       | Associé à la facture d’un client                                                                                                                   |
| LineItemId       | Ligne individuelle dans la facture d’un client                                                                                                                   |
| Pays du client       | Nom de pays fourni par le client.  Il peut être différent du pays associé à l’abonnement Azure d’un client.                                                                                                                   |
| EmailAddress de client       | Adresse e-mail fournie par le client final.  Elle peut être différente de l’adresse e-mail associée à l’abonnement Azure d’un client.                                                                                                                   |
| SkuId       | ID de la référence SKU, tel que défini lors de la publication. Une offre peut englober de nombreuses références SKU, mais une référence SKU ne peut être associée qu’à une seule offre.                                                                                                                   |

>[!Note]
>Tous les rapports et insights associés à l’option de publication Transaction sont disponibles dans la section Insights du portail Cloud Partner ou dans la section Analytics de l'Espace partenaires.

## <a name="billing-questions-and-support"></a>Questions et support sur la facturation

Pour obtenir de l'aide sur les questions de facturation, contactez le [support de l'éditeur de la place de marché commerciale](https://partner.microsoft.com/support/v2/?stage=1).
