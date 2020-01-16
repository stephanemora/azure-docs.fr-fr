---
title: API de traitement SaaS - Questions fréquentes (FAQ) | Place de marché Azure
description: Expériences de découverte et d'achat par les clients d'une offre SaaS sur la Place de marché Azure.
author: MaggiePucciEvans
manager: evansma
ms.author: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: d5b7a4e880cbd5dca73b3b091402e2c7dd3b7901
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934189"
---
# <a name="saas-fulfillment-apis---faq"></a>API de traitement SaaS - Questions fréquentes (FAQ)

Les conditions d'intégration à la Place de marché Azure pour permettre aux clients Azure de souscrire l'offre SaaS sont énumérées.

## <a name="discovery-experience"></a>Expérience de découverte

Une fois l'offre publiée, les utilisateurs Azure peuvent découvrir l'offre SaaS dans la Place de marché Azure. Vos clients pourront filtrer les offres en fonction du type de produit (SaaS) et découvrir les services SaaS qui les intéressent.

## <a name="purchase-experience"></a>Expérience d’achat

Si un utilisateur est intéressé par un service SaaS spécifique, il peut s'y abonner depuis la Place de marché Azure.

### <a name="what-does-it-mean-for-an-azure-user-to-subscribe-to-a-saas-offer-in-azure-marketplace"></a>Qu’implique le fait pour un utilisateur Azure de souscrire une offre SaaS sur la Place de marché Azure ?

Cet utilisateur peut alors consulter les conditions d'utilisation et la déclaration de confidentialité associées au service SaaS, et accepter de payer selon les conditions de facturation que vous, l'éditeur de l'offre SaaS, avez définies sur la facture Microsoft. Les utilisateurs peuvent utiliser leur profil de paiement existant dans Azure pour payer la consommation du service SaaS.

Cette méthode offre de nombreux avantages. Les clients peuvent désormais découvrir et s'abonner en un seul endroit en utilisant Microsoft Cloud Platform comme source de confiance, sans avoir à vérifier tous les logiciels ISV qu'ils souhaitent utiliser. De plus, les clients peuvent utiliser leur profil de paiement existant sans avoir à payer explicitement chaque logiciel ISV séparément.

### <a name="is-the-user-charged-automatically-when-the-offer-is-subscribed"></a>L'utilisateur est-il automatiquement facturé lors de la souscription de l'offre ?

En souscrivant l'offre SaaS, l'utilisateur a accepté de payer pour la consommation du service SaaS via la plate-forme Microsoft. Toutefois, les frais ne commenceront à s'appliquer que lorsque l'offre sera effectivement consommée. L'utilisateur doit se rendre sur votre offre SaaS et confirmer la création de son compte pour commencer à consommer l'offre. Vous demanderez ensuite à Microsoft de commencer à facturer l'abonnement SaaS de ce client.

### <a name="how-are-you-notified-when-a-user-subscribes-to-your-saas-offer"></a>Comment êtes-vous averti lorsqu'un utilisateur souscrit votre offre SaaS ?

Après avoir souscrit une offre, l'utilisateur Azure peut découvrir et gérer toutes ses offres sur Azure. Par défaut, l'état d'une offre SaaS nouvellement souscrite est **'Provisioning, fulfillment pending'** (Provisionnement, en attente de traitement). Dans cet état, l'utilisateur Azure sera invité à **'Configurer le compte'** , afin d’accéder à son expérience de gestion d'abonnement SaaS sur le portail Azure.

Lorsque l’utilisateur clique sur **'Configurer le compte'** , il est redirigé vers le site web du service SaaS. L'URL de redirection est fournie par l'éditeur au moment de la publication de l'offre. Cette page est appelée page d'accueil de l'éditeur. Les utilisateurs Azure devraient pouvoir se connecter à la page d’accueil SaaS à l’aide de leurs informations d'identification AAD existantes dans Azure.

Lorsque l'utilisateur Azure est redirigé vers la page d'accueil, un jeton est ajouté à l'URL de la requête. Ce jeton de courte durée est valide pendant 24 heures. Vous pouvez alors détecter la présence de ce jeton et appeler l'API Microsoft pour obtenir plus de contexte sur celui-ci.

![Flux d’abonnement du client](media/saas-metering-service-integration-flow-a.png)

Consultez le document [ API de traitement SaaS ](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) afin d’obtenir plus d'informations sur le contrat API pour le traitement des scénarios de transactions dans le cycle de vie d'une offre SaaS.

### <a name="how-do-you-know-the-saas-offer-to-which-the-user-subscribes-in-azure"></a>Comment identifier l'offre SaaS que l'utilisateur souscrit dans Azure ?

La réponse à l'API `Resolve` inclut des informations sur l'offre et le forfait associés à l'abonnement SaaS.

### <a name="how-can-the-azure-user-change-the-plan-associated-with-this-azure-subscription"></a>Comment l’utilisateur Azure peut-il modifier le forfait associé à cet abonnement Azure ?

* L’utilisateur Azure peut modifier le forfait associé à l’abonnement SaaS directement dans l’expérience SaaS, ou via la plate-forme Microsoft.

* Des conversions peuvent être effectuées à chaque étape du cycle de facturation. Vous devez accuser réception de toute conversion, et cette conversion s’appliquera dès qu'elle aura été confirmée.

* Les tarifs des forfaits prépayés ( **mensuels** ou  **annuels**) sont calculés au prorata. Tout dépassement constaté jusqu'au moment de la conversion sera reporté sur la facture suivante. Les nouveaux dépassements seront reportés en fonction du nouveau forfait.

>[!Note]
>Vous pouvez bloquer le passage à une version antérieure si vous ne souhaitez pas prendre en charge des chemins de conversion spécifiques.

La séquence ci-dessous montre le flux lorsqu'un client Azure change de forfait dans l'expérience SaaS :

![Flux de changement du forfait d’un client](media/saas-metering-service-integration-flow-b.png)

La séquence ci-dessous montre le flux lorsqu'un client Azure change de forfait dans la vitrine de Microsoft

![Flux de changement du forfait dans la vitrine du client](media/saas-metering-service-integration-flow-c.png)

### <a name="how-can-the-azure-user-unsubscribe-from-the-plan-associated-with-azure-subscription"></a>Comment l’utilisateur Azure peut-il se désabonner du forfait associé à un abonnement Azure ?

Un utilisateur Azure peut se désabonner d'une offre SaaS achetée directement dans l'expérience SaaS ou via la plate-forme Microsoft. Lorsque l'utilisateur se désabonne, il ne sera plus facturé à partir du prochain cycle de facturation.

La séquence ci-dessous montre le flux lorsqu'un client Azure se désabonne de l’offre SaaS dans l’expérience SaaS :

![Le client se désabonne dans l'expérience SaaS](media/saas-metering-service-integration-flow-d.png)

La séquence ci-dessous montre le flux lorsqu'un client Azure se désabonne dans la vitrine Microsoft :

![Le client se désabonne dans la vitrine de Microsoft](media/saas-metering-service-integration-flow-e.png)

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations, consultez [API du service de mesure de la consommation de la Place de marché](./marketplace-metering-service-apis.md).
