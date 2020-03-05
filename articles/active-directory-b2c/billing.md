---
title: Modèle de facturation pour Azure Active Directory B2C
description: En savoir plus sur le modèle de facturation pour les utilisateurs actifs mensuels (MAU) Azure AD B2C et la façon d’activer la facturation pour un abonnement Azure spécifique.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 30ecb1e6faa29482a8d69dd1d08e4f127f515596
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190005"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Modèle de facturation pour Azure Active Directory B2C

L’utilisation d’Azure Active Directory B2C (Azure AD B2C) est facturée sur un abonnement Azure lié et utilise un modèle de facturation pour les utilisateurs actifs mensuels (MAU). Découvrez comment lier une ressource Azure AD B2C à un abonnement et comment le modèle de facturation MAU fonctionne dans les sections suivantes.

> [!IMPORTANT]
> Cet article ne contient pas d’informations de tarification. Pour obtenir les informations les plus récentes sur les prix et la facturation, consultez [Tarification Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="monthly-active-users-mau-billing"></a>Facturation pour les utilisateurs actifs mensuels

La facturation Azure AD B2C est mesurée en fonction du nombre d’utilisateurs uniques avec une activité d’authentification au cours d’un mois civil ; c’est ce que nous appelons la facturation pour les utilisateurs actifs mensuels (MAU).

À compter du **1er novembre 2019**, tous les locataires Azure AD B2C nouvellement créés sont facturés par utilisateurs actifs mensuels (MAU). Les locataires existants qui sont [liés à un abonnement](#link-an-azure-ad-b2c-tenant-to-a-subscription) le 1er novembre 2019 ou après sont facturés par utilisateurs actifs mensuels (MAU).

Si vous avez déjà un locataire Azure AD B2C qui a été lié à un abonnement avant le 1er novembre 2019, vous pouvez choisir d’effectuer une des opérations suivantes :

* Effectuer une mise à niveau vers le modèle de facturation par utilisateurs actifs mensuels (MAU), ou
* Rester sur le modèle de facturation par authentification

### <a name="upgrade-to-monthly-active-users-billing-model"></a>Effectuer une mise à niveau vers le modèle de facturation par utilisateurs actifs mensuels

Les propriétaires d’abonnements Azure qui disposent d’un accès administratif à la ressource Azure AD B2C peuvent basculer vers le modèle de facturation MAU. Les options de facturation sont configurées dans votre ressource Azure AD B2C.

Le passage à la facturation des utilisateurs actifs mensuels (MAU) **n’est pas réversible**. Une fois que vous avez converti une ressource Azure AD B2C en modèle de facturation MAU, vous ne pouvez pas rétablir cette ressource au modèle de facturation par authentification.

Voici comment effectuer le basculement vers la facturation MAU pour une ressource Azure AD B2C existante :

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant que propriétaire d’abonnements.
1. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur, puis l’annuaire Azure AD B2C que vous voulez mettre à niveau avec la facturation MAU.<br/>
    ![Filtre de répertoire et d’abonnement dans le portail Azure](./media/billing/portal-mau-01-select-b2c-directory.png)
1. Dans le menu de gauche, sélectionnez **Azure AD B2C**. Ou sélectionnez **Tous les services**, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sur la page **Vue d’ensemble** du locataire Azure AD B2C, sélectionnez le lien sous **Nom de la ressource**. Vous êtes dirigé vers la ressource Azure AD B2C dans votre locataire Azure AD.<br/>
    ![Lien de ressource Azure AD B2C mis en surbrillance dans le portail Azure](./media/billing/portal-mau-02-b2c-resource-link.png)
1. Dans la page **Vue d’ensemble** de la ressource Azure AD B2C, sous **Unités facturables**, sélectionnez le lien **Par authentification (changer en MAU)** .<br/>
    ![Lien Changer en MAU mis en surbrillance dans le portail Azure](./media/billing/portal-mau-03-change-to-mau-link.png)
1. Sélectionnez **Confirmer** pour terminer la mise à niveau vers la facturation MAU.<br/>
    ![Boîte de dialogue de confirmation de facturation MAU dans le portail Azure](./media/billing/portal-mau-04-confirm-change-to-mau.png)

### <a name="what-to-expect-when-you-transition-to-mau-billing-from-per-authentication-billing"></a>À quoi s’attendre quand vous passez à la facturation MAU à partir de la facturation par authentification

Le compteur MAU est activé dès que vous, le propriétaire de l’abonnement ou de la ressource, confirmez la modification. Votre facture mensuelle reflète les unités d’authentification facturées jusqu’à la modification et les nouvelles unités MAU à compter de la modification.

Les utilisateurs ne sont pas comptés deux fois pendant le mois de transition. Les utilisateurs actifs uniques qui s’authentifient avant la modification sont facturés par authentification dans un mois civil. Ces mêmes utilisateurs ne sont pas inclus dans le calcul MAU pour le reste du cycle de facturation de l’abonnement. Par exemple :

* Le locataire Contoso B2C a 1 000 utilisateurs. 250 utilisateurs sont actifs dans un mois donné. L’administrateur de l’abonnement passe de la facturation par authentification à celle par utilisateurs actifs mensuels (MAU) le 10 du mois.
* La facturation du 1er au 10 est facturée à l’aide du modèle par authentification.
  * Si 100 utilisateurs se connectent au cours de cette période (du 1er au 10), ces utilisateurs sont marqués comme *payants pour le mois*.
* La facturation à compter du 10 (date effective de la transition) est facturée au tarif MAU.
  * Si 150 utilisateurs supplémentaires se connectent au cours de cette période (du 10 au 30), seuls 150 utilisateurs supplémentaires sont facturés.
  * L’activité continue des 100 premiers utilisateurs n’affecte pas la facturation pour le reste du mois civil.

Pendant la période de facturation de la transition, le propriétaire de l’abonnement verra probablement des entrées pour les deux méthodes (par authentification et par MAU) s’afficher dans le relevé de facturation de l’abonnement Azure :

* Une entrée pour l’utilisation jusqu’à la date/heure de la modification qui reflète la valeur par authentification.
* Une entrée pour l’utilisation après la modification qui reflète les utilisateurs actifs mensuels (MAU).

Pour obtenir les informations les plus récentes sur les prix et la facturation de l’utilisation d’Azure AD B2C, consultez [Tarification Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>Lier un locataire Azure AD B2C à un abonnement

Les frais d’utilisation pour Azure Active Directory B2C (Azure AD B2C) sont facturés à un abonnement Azure. Quand un locataire Azure AD B2C est créé, l’administrateur du locataire doit lier explicitement le locataire Azure AD B2C à un abonnement Azure.

Le lien de l’abonnement est obtenu en créant une *ressource* Azure AD B2C dans l’abonnement Azure cible. Plusieurs ressources Azure AD B2C peuvent être créées dans un seul abonnement Azure, ainsi que d’autres ressources Azure, comme des machines virtuelles, des comptes de stockage et des applications logiques. Vous pouvez voir toutes les ressources au sein de l’abonnement en accédant au locataire Azure Active Directory (Azure AD) auquel l’abonnement est associé.

Un abonnement lié à un locataire Azure AD B2C peut être utilisé pour la facturation de l’utilisation d’Azure AD B2C ou d’autres ressources Azure, y compris des ressources Azure AD B2C supplémentaires. Il ne peut pas être utilisé pour ajouter d’autres services Azure basés sur une licence ou des licences Office 365 au sein du locataire Azure AD B2C.

### <a name="prerequisites"></a>Prérequis

* [Abonnement Azure](https://azure.microsoft.com/free/)
* [Locataire Azure AD B2C](tutorial-create-tenant.md) que vous souhaitez lier à un abonnement
  * Vous devez être un administrateur du locataire
  * Le locataire ne doit pas déjà être lié à un abonnement

### <a name="create-the-link"></a>Créer le lien

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur, puis l’annuaire qui contient l’abonnement Azure que vous souhaitez utiliser (et *non* votre locataire Azure AD B2C).
1. Sélectionnez **Créer une ressource**, entrez `Active Directory B2C` dans le champ **Rechercher sur la Place de marché**, puis sélectionnez **Azure Active Directory B2C**.
1. Sélectionnez **Créer**
1. Sélectionnez **Lier un locataire Azure AD B2C existant à mon abonnement Azure**.
1. Sélectionnez un **locataire Azure AD B2C** dans la liste déroulante. Seuls les locataires pour lesquels vous êtes un administrateur général et qui ne sont pas déjà liés à un abonnement sont affichés. Le **nom de la ressource Azure AD B2C** est rempli avec le nom de domaine du locataire Azure AD B2C que vous sélectionnez.
1. Sélectionnez un **abonnement** Azure actif dont vous êtes administrateur.
1. Sous **Groupe de ressources**, sélectionnez **Créer**, puis spécifiez **l’emplacement du groupe de ressources**. Les paramètres de groupe de ressources n’ont ici aucun impact sur l’emplacement, les performances ou l’état de facturation du locataire Azure AD B2C.
1. Sélectionnez **Create** (Créer).
    ![Page de création de la ressource Azure AD B2C dans le portail Azure](./media/billing/portal-01-create-b2c-resource-page.png)

Une fois ces étapes effectuées pour chacun un locataire Azure AD B2C, votre abonnement Azure est facturé conformément aux détails de votre contrat Azure Direct ou Enterprise, le cas échéant.

### <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Gérer vos ressources de locataire Azure AD B2C

Une fois que vous avez créé une ressource Azure AD B2C dans l’abonnement Azure, une nouvelle ressource du type « Locataire B2C » doit apparaître aux côtés de vos autres ressources Azure.

Vous pouvez utiliser cette ressource pour :

* Accéder à l’abonnement pour afficher les informations de facturation
* Obtient l’ID de locataire du locataire Azure AD B2C au format GUID
* accéder à votre locataire Azure AD B2C ;
* envoyer une demande de support ;
* déplacer votre ressource de locataire Azure AD B2C vers un autre abonnement Azure ou groupe de ressources

![Page des paramètres des ressources B2C dans le portail Azure](./media/billing/portal-02-b2c-resource-overview.png)

### <a name="regional-restrictions"></a>Restrictions régionales

Si vous avez établi des restrictions régionales pour la création de ressources Azure dans votre abonnement, cette restriction peut vous empêcher de créer la ressource Azure AD B2C.

Pour atténuer ce problème, allégez vos restrictions régionales.

## <a name="azure-cloud-solution-providers-csp-subscriptions"></a>Abonnements aux fournisseurs de solutions Cloud Azure

Les abonnements aux fournisseurs de solutions Cloud Azure sont pris en charge dans Azure AD B2C. La fonctionnalité est disponible à l’aide des API ou du portail Azure pour Azure AD B2C et pour toutes les ressources Azure. Les administrateurs des abonnements aux fournisseurs de solutions cloud peuvent lier, déplacer et supprimer des relations avec Azure AD B2C comme pour toutes les ressources Azure.

La gestion d’Azure AD B2C à l’aide du contrôle d’accès en fonction du rôle n’est pas affectée par l’association entre le locataire Azure AD B2C et un abonnement à un fournisseur de solutions cloud Azure. Le contrôle d’accès en fonction du rôle est obtenu à l’aide de rôles basés sur le locataire, et non de rôles basés sur l’abonnement.

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Changer l’abonnement de facturation du locataire Azure AD B2C

Les locataires Azure AD B2C peuvent être déplacés vers un autre abonnement si les abonnements source et de destination existent au sein du même locataire Azure Active Directory.

Pour savoir comment déplacer des ressources Azure telles que votre locataire Azure AD B2C vers un autre abonnement, consultez [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md).

Avant de lancer le déplacement, veillez à lire la totalité de l’article pour bien comprendre les limitations et les exigences liées à un déplacement de ce type. En plus des instructions pour déplacer les ressources, il inclut des informations critiques, comme une check-list à consulter avant le déplacement et la validation de l’opération de déplacement.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir les informations les plus récentes sur la tarification, consultez les [Tarification Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
