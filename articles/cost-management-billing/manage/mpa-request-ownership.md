---
title: Obtenir la propriété de facturation d’abonnements Azure dans le cadre d’un Contrat Partenaire Microsoft (MPA)
description: Découvrez comment demander la propriété de facturation des abonnements Azure à partir d’autres utilisateurs.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: banders
ms.openlocfilehash: 6a4b215b2b386754bae2c1bbec171ed4bc558a93
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048210"
---
# <a name="get-billing-ownership-of-azure-subscriptions-to-your-mpa-account"></a>Obtenir la propriété de facturation des abonnements Azure associés à votre compte MPA

Pour fournir une seule facture regroupant les services managés et la consommation Azure, le fournisseur de solutions cloud peut prendre la propriété de la facturation des abonnements Azure à ses clients qui ont un Contrat Entreprise direct.

Cette fonctionnalité est disponible uniquement pour les partenaires CSP à facturation directe qui sont certifiés [Azure Expert MSP](https://partner.microsoft.com/membership/azure-expert-msp). Elle est soumise à la gouvernance et aux stratégies Microsoft, et peut nécessiter un examen et une approbation supplémentaires pour certains clients.

Pour demander la propriété de facturation, vous devez avoir le rôle **Administrateur général** ou **Agents d’administration**. Pour plus d’informations, consultez [Espace partenaires - Affecter des rôles et des autorisations aux utilisateurs](https://docs.microsoft.com/partner-center/permissions-overview).

Cet article concerne les comptes de facturation associés aux Contrats Partenaire Microsoft. Ces comptes sont créés pour permettre aux fournisseurs de solutions Cloud (ou CSP) de gérer la facturation de leurs clients dans la nouvelle expérience de commerce. La nouvelle expérience est disponible uniquement pour les partenaires ayant au moins un client qui a accepté un Contrat Client Microsoft (MCA) et qui possède un plan Azure. [Vérifiez si vous avez accès à un Contrat Partenaire Microsoft](#check-access-to-a-microsoft-partner-agreement).

## <a name="prerequisites"></a>Conditions préalables requises

1. Établissez une [relation de revendeur](https://docs.microsoft.com/partner-center/request-a-relationship-with-a-customer) avec le client. Vérifiez dans la [vue d’ensemble des autorisations régionales CSP](https://docs.microsoft.com/partner-center/regional-authorization-overview) que le locataire client et le locataire partenaire se trouvent dans les mêmes régions autorisées.  

2. [Assurez-vous que le client a accepté le Contrat Client Microsoft ](https://docs.microsoft.com/partner-center/confirm-customer-agreement).

3. Configurez le [plan Azure](https://docs.microsoft.com/partner-center/purchase-azure-plan) pour le client. Si le client effectue des achats par l’entremise de plusieurs revendeurs, vous devez configurer un plan Azure pour chaque relation client-revendeur.

## <a name="request-billing-ownership"></a>Demander la propriété de facturation

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide des informations d’identification de l’agent d’administration CSP.

2. Recherchez **Gestion des coûts + facturation**.

   ![Capture d’écran montrant une recherche relative à la gestion des coûts et à la facturation sur le portail Microsoft Azure](./media/mpa-request-ownership/search-cmb.png)

3. Sélectionnez **Clients** dans la partie gauche, puis sélectionnez un client dans la liste.

   ![Capture d’écran montrant la sélection de clients](./media/mpa-request-ownership/mpa-select-customers.png)        

4. Sélectionnez **Demandes de transfert** dans la partie inférieure gauche, puis sélectionnez **Ajouter une nouvelle demande**.

   ![Capture d’écran montrant la sélection des demandes de transfert](./media/mpa-request-ownership/mpa-select-transfer-requests.png)

5. Entrez l’adresse e-mail de l’utilisateur dans l’organisation du client qui doit accepter la demande de transfert. L’utilisateur doit être propriétaire d’un compte dans un Contrat Entreprise. Sélectionnez **Envoyer la demande de transfert**.

   ![Capture d’écran montrant l’envoi d’une demande de transfert](./media/mpa-request-ownership/mpa-send-transfer-requests.png)

6. L’utilisateur reçoit un e-mail contenant des instructions pour l’examen de votre demande de transfert.

   ![Capture d’écran de l’e-mail invitant à examiner une demande de transfert](./media/mpa-request-ownership/mpa-review-transfer-request-email.png)

7. Pour approuver la demande de transfert, l’utilisateur sélectionne le lien figurant dans l’e-mail, puis suit les instructions.

    ![Capture d’écran de l’e-mail invitant à examiner une demande de transfert](./media/mpa-request-ownership/mpa-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>Vérifier l’état d’une demande de transfert

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Recherchez **Gestion des coûts + facturation**.

   ![Capture d’écran montrant une recherche relative à la gestion des coûts et à la facturation sur le portail Microsoft Azure](./media/mpa-request-ownership/billing-search-cost-management-billing.png)

3. Sélectionnez **Clients** dans la partie gauche.

   ![Capture d’écran montrant la sélection de clients](./media/mpa-request-ownership/mpa-select-customers.png)

4. Dans la liste, sélectionnez le client pour lequel vous avez envoyé la demande de transfert.

5. Sélectionnez **Demandes de transfert** dans la partie inférieure gauche. La page Demandes de transfert affiche les informations suivantes :

    ![Capture d’écran montrant la liste des demandes de transfert](./media/mpa-request-ownership/mpa-select-transfer-requests-for-status.png)

   |Colonne|Définition|
   |---------|---------|
   |Date de la demande|Date à laquelle la demande de transfert a été envoyée|
   |Recipient|Adresse e-mail de l’utilisateur auquel vous avez envoyé la demande de transfert de la propriété de facturation|
   |Date d'expiration|Date d’expiration de la demande|
   |Statut|Statut de la demande de transfert|

    La demande de transfert peut avoir l’un des statuts suivants :

   |Statut|Définition|
   |---------|---------|
   |En cours|L’utilisateur n’a pas accepté la demande de transfert.|
   |Traitement en cours|L’utilisateur a approuvé la demande de transfert. La facturation des abonnements que l’utilisateur a sélectionnés est en cours de transfert vers votre compte.|
   |Completed| La facturation des abonnements que l’utilisateur a sélectionnés a été transférée vers votre compte.|
   |Transfert terminé avec des erreurs|Le traitement de la demande est terminé, mais la facturation de certains abonnements que l’utilisateur a sélectionnés n’a pas pu être transférée.|
   |Expiré|L’utilisateur n’ayant pas accepté la demande à temps, celle-ci a expiré.|
   |Opération annulée|Une personne ayant accès à la demande de transfert a annulé celle-ci.|
   |Refusée|L’utilisateur a refusé la demande de transfert.|

6. Sélectionnez une demande de transfert pour en afficher les détails. La page des détails du transfert affiche les informations suivantes : ![Capture d’écran qui montre la liste des abonnements transférés](./media/mpa-request-ownership/mpa-transfer-completed.png)

   |Colonne  |Définition|
   |---------|---------|
   |ID de la demande de transfert|ID unique de votre demande de transfert. Si vous envoyez une demande de support, partagez l’ID avec le support Azure pour accélérer le traitement de votre demande|
   |Transfert demandé le|Date à laquelle la demande de transfert a été envoyée|
   |Transfert demandé par|Adresse e-mail de l’utilisateur qui a envoyé la demande de transfert|
   |La requête de transfert expire le| Date d’expiration de la demande de transfert|
   |Adresse e-mail du destinataire|Adresse e-mail de l’utilisateur auquel vous avez envoyé la demande de transfert de la propriété de facturation|
   |Lien du transfert envoyé au destinataire|URL envoyée à l’utilisateur pour l’examen de la demande de transfert|

## <a name="supported-subscription-types"></a>Types d’abonnements pris en charge

Vous pouvez demander à obtenir la propriété de facturation des types d’abonnement répertoriés ci-dessous.

- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)\*
- [Contrat Entreprise Microsoft](https://azure.microsoft.com/pricing/enterprise-agreement/)

\* Un abonnement Dev/Test doit d’abord être converti en offre Contrat entreprise via un ticket de support. Un abonnement Enterprise Dev/Test sera facturé au tarif du paiement à l’utilisation après avoir été transféré. Toute remise offerte via l’offre Enterprise Dev/Test du Contrat Entreprise du client ne sera pas disponible pour le partenaire CSP.

## <a name="additional-information"></a>Informations supplémentaires

La section suivante fournit des informations supplémentaires sur le transfert d’abonnements.

### <a name="no-service-downtime"></a>Aucune interruption de service.

Les services Azure compris dans l’abonnement ne subissent aucune interruption. Nous effectuons la transition de la relation de facturation uniquement pour les abonnements Azure que l’utilisateur sélectionne pour le transfert.

### <a name="disabled-subscriptions"></a>Abonnements désactivés

Les abonnements désactivés ne peuvent pas être transférés. Le transfert de propriété de facturation ne peut être effectué que pour des abonnements en état inactif.

### <a name="azure-resources-transfer"></a>Transfert de ressources Azure

Toutes les ressources associées aux abonnements, telles que les machines virtuelles, les disques et les sites web sont transférées.

### <a name="azure-marketplace-products-transfer"></a>Transfert de produits de la Place de marché Azure

Les produits de la Place de marché Azure qui sont disponibles pour les abonnements gérés par les fournisseurs de solutions Cloud (CSP) sont transférés en même temps que leurs abonnements respectifs. Les abonnements contenant des produits de la Place de marché Azure qui ne sont pas activés pour les fournisseurs CSP ne peuvent pas être transférés.

### <a name="azure-reservations-transfer"></a>Transfert de réservations Azure

Les réservations Azure ne suivent pas automatiquement les abonnements. Pour déplacer les réservations, [contactez le support Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="access-to-azure-services"></a>Accès aux services Azure

L’accès pour les utilisateurs, groupes ou principaux de service existants qui ont été affectés avec [Azure RBAC (contrôle d’accès en fonction du rôle)](../../role-based-access-control/overview.md) n’est pas affecté lors de la transition. Le partenaire n’obtient aucun nouvel accès RBAC aux abonnements.  

Les partenaires doivent demander au client un accès aux abonnements.  Les partenaires doivent avoir un accès [Administrateur pour le compte de (AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) ou [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/concepts/cloud-solution-provider) pour ouvrir des tickets de support.

### <a name="azure-support-plan"></a>Plan de support Azure

Le support Azure n’est pas transféré avec les abonnements. Si l’utilisateur transfère tous les abonnements Azure, demandez-leur d’annuler leur plan de support. Après le transfert, le partenaire CSP est responsable du support. Le client doit se rapprocher du partenaire CSP pour toute demande de support.  

### <a name="charges-for-transferred-subscription"></a>Frais de transfert d’abonnement

Le propriétaire de facturation d’origine des abonnements est responsable de tous les frais déclarés jusqu’à ce que le transfert soit terminé. Vous êtes responsable des frais déclarés à compter de la réalisation effective du transfert. Certains frais peuvent avoir été exposés avant le transfert mais déclarés ultérieurement. Ces frais apparaissent dans votre facture.

### <a name="cancel-a-transfer-request"></a>Annuler une demande de transfert

Vous pouvez annuler une demande de transfert jusqu’à ce qu’elle soit approuvée ou refusée. Pour annuler une demande de transfert, accédez à la [page des détails du transfert](#check-the-transfer-request-status) et sélectionnez Annuler en bas de celle-ci.

### <a name="software-as-a-service-saas-transfer"></a>Transfert SaaS (Software as a Service)

Les produits SaaS ne sont pas transférés avec les abonnements. Demandez à l’utilisateur de [contacter le support Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour transférer la propriété de facturation de produits SaaS. En même temps que la propriété de facturation, l’utilisateur peut transférer la propriété de la ressource. La propriété de ressource vous permet d’effectuer des opérations de gestion telles que la suppression et l’affichage des détails du produit. Pour transférer la propriété d’une ressource, l’utilisateur doit en être le propriétaire sur le produit SaaS.

### <a name="additional-approval-for-certain-customers"></a>Approbation supplémentaire pour certains clients

Certaines demandes de transfert émises par les clients peuvent nécessiter un processus d’examen supplémentaire de la part de Microsoft en raison de la nature de la structure des inscriptions d’entreprise actuelles des clients. Dans ce cas, le partenaire est averti de ces exigences quand il essaie d’envoyer une invitation à des clients. Les partenaires sont invités à collaborer avec leur responsable de développement partenaire et l’équipe de compte du client pour effectuer ce processus d’examen.

### <a name="azure-subscription-directory"></a>Répertoire des abonnements Azure
Le répertoire des abonnements Azure qui sont transférés doit correspondre au répertoire du client qui a été sélectionné lors de l’établissement de la relation CSP.

Si ces deux répertoires ne correspondent pas, les abonnements ne peuvent pas être transférés. Vous devez alors soit établir une nouvelle relation de revendeur CSP avec le client en sélectionnant le répertoire approprié des abonnements Azure, soit changer le répertoire des abonnements Azure pour qu’il corresponde au répertoire de la relation CSP-client. Pour plus d’informations, consultez [Associer un abonnement existant à un annuaire Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory#to-associate-an-existing-subscription-to-your-azure-ad-directory).


## <a name="check-access-to-a-microsoft-partner-agreement"></a>Vérifier l’accès à un Contrat Partenaire Microsoft
[!INCLUDE [billing-check-mpa](../../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez toujours besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.

## <a name="next-steps"></a>Étapes suivantes

- La propriété de facturation des abonnements Azure vous a été transférée. Suivez les frais liés à ces abonnements dans le [Portail Microsoft Azure](https://portal.azure.com).

- Demandez au client un accès aux abonnements Azure transférés. [Gérez l’accès aux ressources Azure à l’aide du contrôle d’accès en fonction du rôle (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
