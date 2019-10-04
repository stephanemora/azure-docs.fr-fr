---
title: Attribuer des licences à un groupe - Azure Active Directory | Microsoft Docs
description: Comment affecter des licences à l’aide d’une gestion des licences de groupe Azure Active Directory
services: active-directory
keywords: Gestion des licences Azure AD
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a54d1ad3ab809f2a2f8df6ae0e30b1b061c2be1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60471271"
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>Affecter des licences aux utilisateurs par appartenance aux groupes dans Azure Active Directory

Cet article présente l’affectation de licences de produit à un groupe d’utilisateurs avec Azure Active Directory (Azure AD), puis la vérification que les licences ont été correctement reçues.

Dans cet exemple, le locataire contient un groupe de sécurité appelé **HR Department**. Ce groupe inclut tous les membres du département des ressources humaines (environ 1 000 utilisateurs). Vous souhaitez attribuer des licences Office 365 Entreprise E3 à l’ensemble du département. Le service Yammer Enterprise inclus dans le produit doit être temporairement désactivé jusqu’à ce que le département soit prêt à l’utiliser. L’administrateur souhaite également déployer des licences Enterprise Mobility + Security vers le même groupe d’utilisateurs.

> [!NOTE]
> Certains services Microsoft ne sont pas disponibles dans tous les emplacements. Avant de pouvoir attribuer une licence à un utilisateur, l’administrateur doit spécifier la propriété Emplacement d’utilisation sur l’utilisateur.
> 
> Pour l’affectation d’une licence à un groupe, tous les utilisateurs sans emplacement d’utilisation spécifié héritent de l’emplacement du répertoire. Si vos utilisateurs sont situés à plusieurs emplacements, nous vous recommandons de définir systématiquement l’emplacement d’utilisation dans le cadre de votre flux de création d’utilisateur dans Azure AD (par exemple, par le biais d’une configuration AAD Connect). Cela garantit que le résultat de l’affectation de licence est toujours correct et que les utilisateurs ne reçoivent pas de services à des emplacements non autorisés.

## <a name="step-1-assign-the-required-licenses"></a>Étape 1 : Affecter les licences requises

1. Connectez-vous au [**Centre d’administration Azure AD**](https://aad.portal.azure.com) avec un compte administrateur de licences. Pour gérer les licences, le compte doit être un rôle administrateur de licences, administrateur d’utilisateurs ou administrateur général.

2. Sélectionnez **Licences** afin d’ouvrir un volet vous permettant de visualiser et de gérer tous les produits sous licence du locataire.

4. Sous **Tous les produits**, sélectionnez Office 365 Enterprise E5 et Enterprise Mobility + Security E3 en sélectionnant les noms de produits. Pour démarrer l’affectation, sélectionnez **Affecter** en haut du volet.

   ![Sélectionnez les produits pour attribuer les licences](./media/licensing-groups-assign/all-products-assign.png)
  
5. Dans le volet **Affecter une licence**, cliquez sur **Utilisateurs et groupes** pour ouvrir une liste des utilisateurs et des groupes.

6. Sélectionnez un utilisateur ou un groupe et utilisez le bouton **Sélectionner** en bas du volet pour confirmer votre sélection.

7. Dans le volet **Affecter une licence**, cliquez sur **Options d’affectation** qui affiche tous les plans de services inclus dans les deux produits que nous avons sélectionnés précédemment. Recherchez **Yammer Enterprise** et définissez-le sur **Désactivé** pour désactiver ce service à partir de la licence du produit. Confirmez en cliquant sur **OK** en bas des **options de licence**.

   ![Sélectionnez les plans de service pour les licences](./media/licensing-groups-assign/assignment-options.png)
  
8. Pour terminer l’affectation, dans le volet **Affecter une licence**, cliquez sur **Affecter** au bas du volet.

9. Une notification indiquant l’état et le résultat du processus s’affiche dans l’angle supérieur droit. Si l’affectation au groupe n’a pas pu être effectuée (par exemple en raison de licences existantes dans le groupe), cliquez sur la notification pour afficher les détails de l’échec.

Lorsque vous attribuez des licences à un groupe, Azure AD traite tous les membres existants de ce groupe. Ce processus peut prendre un certain temps, en fonction de la taille du groupe. L’étape suivante décrit comment vérifier que le processus est terminé, et comment déterminer si une attention supplémentaire est requise pour résoudre des problèmes.

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>Étape 2 : Vérifier que l’affectation initiale est terminée

1. Accédez à **Azure Active Directory** > **Groupes**. Sélectionnez le groupe auquel les licences ont été assignées.

2. Dans le volet du groupe, sélectionnez **Licences**. Cela vous permet de vérifier rapidement si les licences ont été entièrement affectées aux utilisateurs et s’il existe des erreurs que vous devez examiner. Les informations suivantes sont disponibles :

   - Liste des licences du produit qui sont actuellement affectées au groupe. Sélectionnez une entrée pour afficher les services spécifiques qui ont été activés et pour apporter des modifications.

   - État des dernières modifications de licences effectuées sur le groupe (si les modifications sont en cours de traitement ou si le traitement a été effectué sur tous les membres de l’utilisateur).

   - Informations sur les utilisateurs qui se trouvent en état d’erreur parce qu’il n’a pas été possible de leur affecter des licences.

   ![erreurs de licence et état des licences](./media/licensing-groups-assign/assignment-errors.png)

3. Pour des informations plus détaillées sur le traitement des licences, consultez **Azure Active Directory** > **Utilisateurs et groupes** > *nom du groupe* > **Journaux d’audit**. Notez les activités suivants :

   - Activité : **Commencer à appliquer une licence basée sur un groupe à des utilisateurs**. Elle est consignée lorsque le système sélectionne la modification d’affectation de licences sur le groupe et commence à l’appliquer à tous les membres utilisateur. Elle contient des informations sur la modification qui a été apportée.

   - Activité : **Terminer l’application d’une licence basée sur le groupe à des utilisateurs**. Elle est consignée lorsque le système termine le traitement de tous les utilisateurs du groupe. Elle contient un résumé du nombre d’utilisateurs qui ont été traités avec succès et du nombre d’utilisateurs auxquels les licences du groupe n’ont pas pu être affectées.

   [Lisez cette section](licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity) pour en savoir plus sur l’utilisation des journaux d’audit pour analyser les modifications apportées par une gestion des licences basée sur un groupe.

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>Étape 3 : Rechercher des problèmes de licences et les résoudre

1. Accédez à **Azure Active Directory** > **Groupes** et recherchez les groupes auquel les licences ont été affectées.
2. Dans le volet du groupe, sélectionnez **Licences**. La notification en haut du volet indique que des licences n’ont pas pu être affectées à 10 utilisateurs. Ouvrez cette notification pour accéder à une liste de tous les utilisateurs associés à une erreur de licence pour ce groupe.
3. La colonne **Échec d’affectations** indique que les deux licences de produit n’ont pas pu être affectées aux utilisateurs. La colonne **Raison principale de l’échec** contient la cause de l’échec. Dans ce cas, il s’agit de **Plans de service en conflit**.

   ![licences qui n’ont pas pu être affectées](./media/licensing-groups-assign/failed-assignments.png)

4. Sélectionnez un utilisateur pour ouvrir le volet **Licences**. Ce volet répertorie toutes les licences qui sont actuellement affectées à l’utilisateur. Dans cet exemple, l’utilisateur a hérité de la licence Office 365 Enterprise E1 du groupe **Kiosk users**. Cette licence est en conflit avec la licence E3 que le système a tenté d’appliquer à partir du groupe **HR Department**. Par conséquent, aucune des licences de ce groupe n’a été assignée à l’utilisateur.

   ![Voir tous les conflits de licence pour un utilisateur](./media/licensing-groups-assign/user-license-view.png)

5. Pour résoudre ce conflit, nous supprimons l’utilisateur du groupe **Kiosk users**. Lorsqu’Azure AD a traité la modification, les licences **HR Department** sont affectées correctement.

   ![Licences correctement affectées ici](./media/licensing-groups-assign/license-correctly-assigned.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’ensemble de fonctionnalités de gestion des licences par le biais des groupes, consultez les articles suivants :

* [What is group-based licensing in Azure Active Directory? (Présentation des licences basées sur le groupe dans Azure Active Directory)](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Identification et résolution des problèmes de licence pour un groupe dans Azure Active Directory](licensing-groups-resolve-problems.md)
* [Migration des utilisateurs individuels sous licence vers les licences basées sur les groupes dans Azure Active Directory](licensing-groups-migrate-users.md)
* [Guide pratique pour migrer des utilisateurs entre des licences de produit à l’aide de la gestion de licences basée sur des groupes dans Azure Active Directory](licensing-groups-change-licenses.md)
* [Azure Active Directory group-based licensing additional scenarios (Autres scénarios de licence basée sur le groupe Azure Active Directory)](../active-directory-licensing-group-advanced.md)
* [Exemples PowerShell pour les licences basées sur les groupes dans Azure Active Directory](licensing-ps-examples.md)
