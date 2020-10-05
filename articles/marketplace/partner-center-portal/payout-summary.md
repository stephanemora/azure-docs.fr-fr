---
title: Rapports de revenus - Place de marché commerciale Microsoft
description: Les rapports de revenus vous présentent en détail l’argent que vous a rapporté votre offre, notamment le montant et la date de paiement prévue.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: mingshen-ms
ms.author: mingshen
ms.date: 09/09/2020
ms.openlocfilehash: 62de9380578ca5a97f7b72f9939d5c2dd7392c0d
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2020
ms.locfileid: "90004219"
---
# <a name="payout-summaries"></a>Synthèse des revenus

La synthèse des revenus montre en détail l’argent que vous avez gagné avec Microsoft. Elle vous permet également de savoir à quels moments vous recevrez les paiements et quels montants vous percevrez.

Si vous vendez des produits sur la Place de marché Azure, vous trouverez également des informations sur les paiements qui ont bien été effectués dans cette synthèse des revenus. Pour plus d’informations sur les paiements de la Place de marché Azure, consultez [Recevoir un paiement dans la place de marché commerciale](./get-paid.md) et [Contrat de l’éditeur Microsoft](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4xqkx).

> [!NOTE]
> Pour être éligible au paiement, votre produit doit atteindre le seuil de paiement de 50 $. Pour plus d’informations, consultez [Contrat de l’éditeur Microsoft](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4xqkx).

> [!NOTE]
> Si vous avez besoin d’aide concernant les paiements, notamment sur la configuration des comptes de paiement, les paiements manquants, la mise en attente des paiements ou d’autres sujets, contactez le support [ici](https://developer.microsoft.com/windows/support).

## <a name="access-the-payout-summary-pages"></a>Accéder aux pages de synthèse des revenus

Pour ouvrir l’une des pages de synthèse des revenus :

1. Sélectionnez l’icône Revenu dans le coin supérieur droit.
2. Sélectionnez **Historique des transactions**, **Paiements** ou **Exporter les données**.

## <a name="payments-page"></a>Page Paiements

Les totaux de cette page représentent tous les programmes auxquels vous participez. Vous pouvez filtrer par ID de participant, Programme, ID de paiement et Type de bénéfice. Les montants sont indiqués en dollars américains. La valeur payée est également affichée dans la devise d’arrivée.

| Domaine  | Description |
| --- | --- |
| Total payé cette année   | Montant total cumulé qui vous a été versé cette année, en dollars américains, pour tous vos programmes       |
| Prochain paiement estimé | Prochain paiement unique qui vous sera adressé (même si d’autres sont prévus sous peu), en dollars américains |
| Dernier paiement           | Montant (en dollars américains), nom du programme et programme de votre paiement le plus récent           |
| Paiements par source     | Nombre de paiements (en dollars américains) représentés par le programme au cours des 12 derniers mois           |
| Paiements               | Sélectionnez **Payé** ou **En attente**, puis triez comme vous le souhaitez. Pour plus d’informations sur un paiement spécifique, sélectionnez **Afficher**. Pour télécharger une copie du relevé des envois de paiements, sélectionnez **Télécharger**. Sachant que la mise à disposition des données de l’historique des transactions peut prendre jusqu’à 24 heures, vous ne verrez peut-être pas les bénéfices associés tout de suite. |
|||

Pour exporter des données de cette page, sélectionnez **Exporter** et suivez les instructions qui vous sont fournies dans la page Exporter les données.

## <a name="transaction-history-page"></a>Page Historique des transactions

Cette page affiche tous vos bénéfices individuels avec, pour chacun, la date, le type et le montant. Vous pouvez afficher ces informations pour une période déterminée et filtrer par ID d’inscription, Programme, ID de paiement, Type de bénéfice, Levier et Statut. Les données sont disponibles pour l’exercice actuel (du 1er juillet au 30 juin) et les deux exercices précédents.

Pour afficher plus de détails sur un bénéfice, sélectionnez la flèche vers le bas située dans la partie droite de la page. Vous obtenez alors le levier, le montant de chiffre d’affaires et le produit. Si l’une de ces données n’est pas disponible et que vous en avez besoin, contactez le [support](https://developer.microsoft.com/windows/support). Si le bénéfice est le résultat d’un ajustement et non d’une transaction, les champs de produit ne s’affichent pas.

Pour exporter des données de cette page, sélectionnez **Exporter** et suivez les instructions qui vous sont fournies dans la page Exporter les données. Les fichiers exportés à partir de la page Historique des transactions affichent les données dans la devise de la transaction, les bénéfices dans la devise de la transaction et en dollars américains, et la valeur payée dans la devise d’arrivée.

## <a name="payment-status"></a>État du paiement

| État du bénéfice | Motif | Intervention nécessaire du partenaire ? |
| --- | --- | --- |
| Non traité              | Le revenu est éligible au paiement. Il reste dans cet état pendant une période de réflexion telle que définie dans le guide du programme d’incentives. | Non                                                         |
| À venir                 | Des vérifications internes en attente sont générées par l’ordre de paiement avant le traitement du paiement                                                               | Non                                                         |
| Facture fiscale en attente      | Votre facture fiscale est incomplète ou non valide                                                                                                  | Vous devez mettre à jour votre facture fiscale avant de pouvoir être payé |
| Rejeté pendant la vérification   | Le paiement a été rejeté pendant la vérification                                                                                                     | Contactez le [Support Microsoft](https://developer.microsoft.com/windows/support) pour obtenir des détails                      |
| Échec                   | Le paiement a échoué en raison d’une erreur du système Microsoft                                                                                         | Contactez le [Support Microsoft](https://developer.microsoft.com/windows/support) pour obtenir des détails                      |
| En cours              | Le paiement est en cours                                                                                                                 | Non                                                         |
| Paiement incorrect        | Le recouvrement du paiement est en cours                                                                                                       | Non                                                         |
| Envoyé                     | Le paiement a été envoyé à votre banque                                                                                                     | Non                                                         |
| Retraitement             | Le paiement a rencontré une erreur du système Microsoft et est en cours de retraitement                                                                  | Non                                                         |
| Inversé                 | Le paiement a été annulé par votre banque et sera renvoyé au prochain cycle de paiement                                                     | Non                                                         |
| Facture fiscale rejetée     | Votre facture fiscale a été rejetée pendant la vérification. Tous les paiements en attente seront mis en attente tant que l’examen de la facture fiscale n’aura pas été effectué.                 | Contactez le [Support Microsoft](https://developer.microsoft.com/windows/support) pour obtenir des détails                      |
| Facture fiscale en cours de vérification | Votre facture fiscale est en cours de vérification. Votre paiement sera débloqué une fois que la facture fiscale aura été approuvée.                                   | Non                                                         |
| Rejeté                 | Le paiement a été rejeté par votre banque                                                                                                      | Contactez votre banque pour obtenir des détails.                             |
|||

## <a name="export-data-page"></a>Page Exporter les données

Suivez ces instructions pour exporter des données.

La page Exporter les données ne s’actualise pas automatiquement. Vous devrez peut-être l’actualiser manuellement pour afficher les données les plus récentes.

Votre filtre peut déclencher l’erreur **Pas de données disponibles**. Cela signifie probablement que vous avez laissé la période par défaut sélectionnée à trois mois, et que vous avez sélectionné un numéro de paiement correspondant à un revenu situé en dehors de cette période. Élargissez la période et réessayez.

## <a name="payments"></a>Paiements

![Exporter les paiements](./media/pc-export-payments.png)

Cette option vous permet de télécharger les paiements que vous avez reçus à votre banque pour un programme donné, les taxes associées et le montant cumulé des bénéfices. Ce rapport étant utilisé pour divers programmes de l’Espace partenaires, certaines colonnes peuvent ne pas s’appliquer à votre rapport. Ces colonnes sont signalées ci-dessous.

| Nom de la colonne | Description |
| --- | --- |
| participantID            | Identité principale du revenu partenaire dans le cadre du programme                                                                             |
| participantIDType        | En général, ID de programme pour les programmes d’incentives et ID de vendeur pour les programmes du Store                                                                |
| participantName          | Nom du partenaire de revenu                                                                                                               |
| programName              | Nom du programme d’incentives/du Store                                                                                                              |
| earned                   | Montant du chiffre d’affaires dans la devise d’arrivée pour le programme/ID de participant en question                                                                       |
| earnedUSD                | Montant de chiffres d’affaires enregistré pour le programme/ID de participant, en USD                                                                                      |
| withheldTax              | Montant des taxes retenues dans la devise d’arrivée pour le programme/ID de participant                                                               |
| salesTax                 | Montant total des taxes sur les ventes dans la devise d’arrivée pour le programme/ID de participant (s’applique uniquement aux programmes d’incentives)                   |
| serviceFeeTax            | Montant total de serviceFeeTax dans la devise d’arrivée pour le programme/ID de participant (s’applique uniquement aux programmes du Store et à la Place de marché Azure) |
| totalPayment             | Paiement total dans la devise locale à l’exclusion de la retenue à la source et en incluant les taxes sur les ventes (le cas échéant) pour le programme/ID de participant   |
| currencyCode             | Code de la devise d’arrivée                                                                                                                      |
| paymentMethod            | Mode de paiement utilisé pour payer le partenaire, par exemple, le virement bancaire électronique, l’avoir                                                     |
| paymentId                | Identificateur unique du paiement. Ce numéro figure généralement sur votre relevé bancaire (s’applique uniquement aux paiements SAP).              |
| paymentStatus            | État du paiement                                                                                                                            |
| paymentStatusDescription | Description conviviale de l’état du paiement                                                                                                    |
| paymentDate              | Date à laquelle le paiement a été envoyé par Microsoft                                                                                                      |
|||

## <a name="transaction-history"></a>Historique des transactions

![Exporter l’historique des transactions](./media/pc-export-transaction.png)

Cette option permet de télécharger chaque ligne de bénéfice que vous voyez dans la page Historique des transactions, le type de bénéfice, la date, le montant de transaction associé, le client, le produit et d’autres détails transactionnels applicables à vos programmes.

| Nom de la colonne | Description | Applicabilité pour les incentives/le Store/la Place de marché Azure |
| --- | --- | --- |
| earningId                      | Identificateur unique de chaque revenu                                                                                                       | Tous                                                            |
| participantId                  | Identité principale du revenu partenaire dans le cadre du programme                                                                            | Tous                                                            |
| participantIdType              | Principalement, ID des programmes d’incentives et ID de vendeur pour les programmes du Store et la Place de marché Azure                                          | Tous                                                            |
| participantName                | Nom du partenaire de revenu                                                                                                              | Tous                                                            |
| partnerCountryCode             | Lieu/pays/région du partenaire de revenu                                                                                                  | Tous                                                            |
| programName                    | Nom du programme d’incentives/du Store                                                                                                             | Tous                                                            |
| transactionId                  | Identificateur unique de la transaction                                                                                                    | Tous                                                            |
| transactionCurrency            | Devise dans laquelle la transaction du client d’origine a été effectuée (il ne s’agit pas de la devise locale du partenaire)                                     | Tous                                                            |
| transactionDate                | Date de la transaction Utile pour les programmes où de nombreuses transactions contribuent à un revenu                                           | Tous                                                            |
| transactionExchangeRate        | Date du taux de change utilisé pour afficher le montant en USD correspondant de la transaction                                                                 | Tous                                                            |
| transactionAmount              | Montant de la transaction dans la devise de transaction d’origine en fonction du revenu généré                                              | Tous                                                            |
| transactionAmountUSD           | Montant de la transaction en USD                                                                                                                | Tous                                                            |
| lever                          | Indique une règle métier pour le revenu                                                                                                  | Tous                                                            |
| earningRate                    | Taux d’incentives appliqué au montant de la transaction pour générer un revenu                                                                      | Tous                                                            |
| quantité                       | Varie selon le programme. Indique la quantité facturée pour les programmes transactionnels                                                            | Tous                                                            |
| quantityType                   | Indique le type de quantité, par exemple, Quantité facturée, Utilisateurs actifs mensuels                                                                             | Tous                                                            |
| earningType                    | Indique s’il s’agit de frais, d’une remise, d’une coopération, d’une vente, etc.                                                                                          | Tous                                                            |
| earningAmount                  | Montant du revenu dans la devise de la transaction d’origine                                                                                      | Tous                                                            |
| earningAmountUSD               | Montant du revenu en USD                                                                                                                    | Tous                                                            |
| earningDate                    | Date du revenu                                                                                                                      | Tous                                                            |
| calculationDate                | Date à laquelle le revenu a été calculé dans le système                                                                                            | Tous                                                            |
| earningExchangeRate            | Taux de change utilisé pour afficher le montant USD correspondant                                                                                  | Tous                                                            |
| exchangeRateDate               | Date du taux de change utilisée pour calculer le montant USD du revenu                                                                                   | Tous                                                            |
| paymentAmountWOTax             | Montant du revenu (hors taxe) dans la devise d’arrivée pour les paiements « Envoyés » uniquement                                                                 | Tous                                                            |
| paymentCurrency                | Devise de destination choisie par le partenaire dans le profil de paiement. Affichée uniquement pour les paiements envoyés                                                   | Tous                                                            |
| paymentExchangeRate            | Taux de change utilisé pour calculer paymentAmountWOTax dans la devise de paiement avec ExchangeRateDate                                            | Tous                                                            |
| claimId                        | Identificateur unique de la demande                                                                                                              | Incentives – certains programmes uniquement                                |
| planId                         | Identificateur unique du plan                                                                                                               | Incentives – certains programmes uniquement                                |
| paymentId                      | Identificateur unique du paiement. Ce numéro figure généralement sur votre relevé bancaire                                                 | Paiements SAP uniquement                                              |
| paymentStatus                  | État du paiement                                                                                                                           | Tous                                                            |
| paymentStatusDescription       | Description conviviale de l’état du paiement                                                                                                   | Tous                                                            |
| customerId                     | Sera toujours vide                                                                                                                     | Programmes d’incentives uniquement (exception : OEM) et la Place de marché Azure |
| customerName                   | Sera toujours vide                                                                                                                     | Programmes d’incentives uniquement (exception : OEM) et la Place de marché Azure |
| partNumber                     | Sera toujours vide                                                                                                                     | Certains programmes d’incentives et du Store et la Place de marché Azure        |
| ProductName                    | Nom de produit lié à la transaction                                                                                                       | Tous                                                            |
| productId                      | Identificateur de produit unique                                                                                                                | Store et Place de marché Azure                                    |
| parentProductId                | Identificateur de produit parent unique. S’il n’existe aucun produit parent pour la transaction, l’ID du produit parent = ID du produit. | Store et Place de marché Azure                                    |
| parentProductName              | Nom du produit parent. S’il n’existe aucun produit parent pour la transaction, le nom du produit parent = Nom du produit.   | Store et Place de marché Azure                                    |
| productType                    | Type de produit (par exemple, application, module complémentaire ou jeu)                                                                                        | Store et Place de marché Azure                                    |
| invoiceNumber                  | Numéro de facture (applicable à EA uniquement)                                                                                                  | Incentive et Place de marché Azure – certains programmes uniquement           |
| subscriptionId                 | Identificateur d’abonnement associé au client                                                                                         | Incentive – certains programmes uniquement                                 |
| subscriptionStartDate          | Date de début de l’abonnement                                                                                                                  | Incentive – certains programmes uniquement                                 |
| subscriptionEndDate            | Date de fin de l’abonnement                                                                                                                    | Incentive – certains programmes uniquement                                 |
| resellerId                     | Identificateur du revendeur                                                                                                                      | Incentive – certains programmes uniquement                                 |
| resellerName                   | Nom du revendeur                                                                                                                            | Incentive – certains programmes uniquement                                 |
| distributorId                  | Identificateur du serveur de distribution                                                                                                                   | Incentive – certains programmes uniquement                                 |
| distributorName                | Nom du serveur de distribution                                                                                                                         | Incentive – certains programmes uniquement                                 |
| agreementNumber                | Numéro de l’accord                                                                                                                         | Incentive – certains programmes uniquement                                 |
| agreementStartDate             | Date de début de l’accord                                                                                                                     | Incentive – certains programmes uniquement                                 |
| agreementEndDate               | Date de fin de l’accord                                                                                                                       | Incentive – certains programmes uniquement                                 |
| charge de travail                       | Charge de travail                                                                                                                                 | Incentive – certains programmes uniquement                                 |
| transactionType                | Type de transaction (par exemple achat, remboursement, contrepassation ou rétrofacturation)                                                               | Store et Place de marché Azure                                    |
| localProviderSeller            | Fournisseur local/vendeur d’enregistrement                                                                                                          | Store uniquement                                                     |
| taxRemitted                    | Montant des taxes versées (ventes, consommation ou TVA/GST)                                                                                   | Store et Place de marché Azure                                    |
| taxRemitModel                  | Tiers responsable du versement des taxes (ventes, consommation ou TVA/GST)                                                                    | Store uniquement                                                     |
| storeFee                       | Montant retenu par Microsoft en guise de frais pour la mise à disposition de l’application ou du module complémentaire sur le Store                                           | Store uniquement                                                     |
| transactionPaymentMethod       | Moyen de paiement client utilisé pour la transaction (par exemple, carte, facturation d’opérateur de téléphonie mobile ou PayPal)                                | Store et Place de marché Azure                                    |
| tpan                           | Indique le réseau publicitaire tiers                                                                                                     | Store – Annonces uniquement                                               |
| customerCountry                | Pays/région du client                                                                                                                         | Store et Place de marché Azure                                    |
| CustomerCity                   | Ville du client                                                                                                                            | Store et Place de marché Azure                                    |
| customerState                  | État du client                                                                                                                           | Store et Place de marché Azure                                    |
| customerZip                    | Code postal du client                                                                                                                 | Store et Place de marché Azure                                    |
| purchaseTypeCode               | Sera toujours vide                                                                                                                     | Programme d’incentives – AGI                                        |
| purchaseOrderType              | Sera toujours vide                                                                                                                     | Programme d’incentives – AGI                                        |
| purchaseOrderCoverageStartDate | Sera toujours vide                                                                                                                     | Programme d’incentives – AGI                                        |
| purchaseOrderCoverageEndDate   | Sera toujours vide                                                                                                                     | Programme d’incentives – AGI                                        |
| programOfferingLevel           |                                                                                                                                          | Programme d’incentives – AGI                                        |
| ID de locataire                       |                                                                                                                                          | Programmes d’incentives                                             |
| externalReferenceId            | Identificateur unique du programme                                                                                                        | Programmes de paiement direct (Incentive et Store)                      |
| externalReferenceIdLabel       | Étiquette de l’identificateur unique                                                                                                                  | Programmes de paiement direct (Incentive et Store)                      |
|||

## <a name="historical-statements"></a>Instructions historiques

![Exporter les instructions historiques](./media/pc-export-statements.png)

L’historique des transactions antérieures au 1er juillet 2019 est traité séparément. Les instructions utilisent les champs suivants à la place des champs actuels.

> [!NOTE]
> L’historique des transactions existant comporte une colonne appelée « Réservé » qui correspond à la colonne « Bénéfices » dans l’historique moderne, sauf qu’elle exclut tous les bénéfices dont le statut est « Paiement envoyé ».

> [!NOTE]
> Les filtres 3 mois, 6 mois ou 12 mois ne s’appliquent pas à la section **Instructions historiques**.

| Nom du champ | Description |
| --- | --- |
| Source du chiffre d’affaires          | Source de votre chiffre d’affaires en fonction de l’origine de la transaction (par exemple, Microsoft Store, Windows Phone Store, Windows Store 8 ou publicité)                  |
| ID de commande                | Identificateur de commande unique. Cet ID vous permet d’identifier les transactions d’achat avec leurs transactions de non-achat respectives, comme les remboursements ou les rétrofacturations. Les deux ont le même ID de commande. De même, dans le cas d’une charge fractionnée où plusieurs modes de paiement ont été utilisés pour un même achat, il vous permet de lier les transactions d’achat. |
| ID de transaction          | Identificateur de transaction unique.                                                                                                                                          |
| Date et heure de la transaction   | Date et heure auxquelles la transaction a eu lieu (UTC).                                                                                                                       |
| ID du produit parent       | Identificateur de produit parent unique. S’il n’existe aucun produit parent pour la transaction, l’ID du produit parent = ID du produit.                                |
| Product ID              | Identificateur de produit unique.                                                                                                                                              |
| Nom du produit parent     | Nom du produit parent. S’il n’existe aucun produit parent pour la transaction, le nom du produit parent = Nom du produit.                                  |
| Nom du produit            | Nom du produit                                                                                                                                                    |
| Type de produit            | Type de produit (par exemple, application, module complémentaire ou jeu)                                                                                                                       |
| Quantité                | Quand la source du chiffre d’affaires est le Microsoft Store pour Entreprises, la Quantité représente le nombre de licences achetées. Pour toutes les autres sources de chiffre d’affaires, la Quantité est toujours égale à 1. Même quand une même transaction est fractionnée en deux lignes en raison de l’utilisation de deux modes de paiement, chaque ligne présente une Quantité égale à 1. |
| Type de transaction        | Type de transaction (par exemple, achat, remboursement, contrepassation ou rétrofacturation)                                                                                              |
| Mode de paiement          | Moyen de paiement client utilisé pour la transaction (par exemple, carte, facturation d’opérateur de téléphonie mobile ou PayPal)                                                               |
| Pays/région        | Pays/région où la transaction a eu lieu                                                                                                                          |
| Fournisseur local/vendeur | Fournisseur local/vendeur d’enregistrement                                                                                                                                        |
| Devise de la transaction    | Devise de la transaction                                                                                                                                            |
| Montant de la transaction      | Montant de la transaction                                                                                                                                              |
| Taxes versées            | Montant des taxes versées (ventes, consommation ou TVA/GST)                                                                                                                  |
| Encaissements nets            | Montant de la transaction moins les taxes versées                                                                                                                                   |
| Frais du Store               | Pourcentage des encaissements nets retenu par Microsoft en guise de frais pour la mise à disposition de l’application ou du module complémentaire sur le Store                                                      |
| Revenus des applications            | Encaissements nets moins les frais du Store                                                                                                                                       |
| Taxes retenues          | Montant d’impôt sur le revenu retenu (inclus dans le fichier CSV **Réservé**)                                                                                                |
| Paiement                 | Revenus des applications moins la retenue à la source éventuellement applicable (montant affiché dans Devise de la transaction). Non inclus dans le fichier CSV **Réservé**.                               |
| Taux de change                 | Taux de change utilisé pour convertir la devise de la transaction en devise de paiement                                                                                         |
| Devise de paiement        | Devise dans laquelle votre paiement est effectué                                                                                                                                       |
| Paiement converti       | Montant du paiement converti en devise de paiement en utilisant le taux de change                                                                                                         |
| Modèle de paiement des taxes         | Tiers responsable du versement des taxes (ventes, consommation ou TVA/GST)                                                                                                   |
| Date et heure d’éligibilité   | Date et heure auxquelles le produit de la transaction devient éligible au paiement (UTC). Quand un paiement est créé, il comprend le produit de la transaction avec une date et heure d’éligibilité antérieures à la date de création du paiement (uniquement inclus dans le fichier CSV **Réservé**). |
| Charges                 | Présente une décomposition de tous les détails de charges agrégés dans la colonne Montant de la transaction (uniquement incluse pour la Place de marché Azure ; non incluse dans le fichier CSV **Réservé**). |
|||

## <a name="next-step"></a>Étape suivante

- [Détails de la politique de paiement](./payout-policy-details.md)
