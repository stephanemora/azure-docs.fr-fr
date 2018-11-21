---
title: Affecter une autorisation d’accès aux données Azure Cost Management | Microsoft Docs
description: Cet article vous explique comment affecter une autorisation aux données Azure Cost Management pour différentes étendues d’accès.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/09/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: e32e281509da32d4816c9e137a462553891c82f1
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51686141"
---
# <a name="assign-access-to-cost-management-data"></a>Affecter une autorisation d’accès aux données Cost Management

Pour la plupart des utilisateurs, une combinaison d’autorisations accordées dans le portail Azure et le portail EA (Enterprise Portal) permet de définir le niveau d’accès d’un utilisateur aux données Azure Cost Management. Cet article vous explique comment affecter un accès aux données Cost Management. Une fois la combinaison d’autorisations affectée, l’utilisateur voit les données dans Cost Management en fonction de l’étendue à laquelle il a accès et de l’étendue qu’il a sélectionnée dans le portail Azure.

L’étendue sélectionnée par l’utilisateur est utilisée dans Cost Management pour regrouper les données et contrôler l’accès aux informations sur les coûts. Quand les utilisateurs emploient des étendues, ils n’effectuent pas plusieurs sélections. À la place, ils sélectionnent une étendue plus grande qui englobe les étendues enfants, puis appliquent un filtre pour voir ce qui les intéresse. Il est important de bien comprendre le fonctionnement du regroupement des données, car certaines personnes ne doivent pas avoir accès à l’étendue parente qui englobe les étendues enfants.

## <a name="cost-management-scopes"></a>Étendues de Cost Management

Pour afficher les données de coût, un utilisateur doit disposer au moins d’un accès en lecture à une ou plusieurs des étendues suivantes.

| **Portée** | **Définition** | **Accès obligatoire pour afficher les données** | **Paramètre de Contrat Entreprise (EA) prérequis** | **Regroupe les données dans** |
| --- | --- | --- | --- | --- |
| <sup>Premier</sup> compte de facturation | [https://ea.azure.com](https://ea.azure.com/) | Administrateur d'entreprise | Aucun | Abonnements du contrat entreprise |
| department | [https://ea.azure.com](https://ea.azure.com/) | Administrateur de service | Activation de l’option d’**affichage des frais pour l’administrateur de service** | Abonnements appartenant à un compte d’inscription lié au service |
| <sup>Deuxième</sup> compte d’inscription | [https://ea.azure.com](https://ea.azure.com/) | Propriétaire du compte | Activation de l’option d’**affichage des frais pour le propriétaire du compte** | Abonnements à partir du compte d’inscription |
| Groupe d’administration | [https://portal.azure.com](https://portal.azure.com/) | Lecteur Cost Management (ou Lecteur) | Activation de l’option d’**affichage des frais pour le propriétaire du compte** | Abonnements en dessous du groupe d’administration |
| Abonnement | [https://portal.azure.com](https://portal.azure.com/) | Lecteur Cost Management (ou Lecteur) | Activation de l’option d’**affichage des frais pour le propriétaire du compte** | Ressources/groupes de ressources compris dans l’abonnement |
| Groupe de ressources | [https://portal.azure.com](https://portal.azure.com/) | Lecteur Cost Management (ou Lecteur) | Activation de l’option d’**affichage des frais pour le propriétaire du compte** | Ressources comprises dans le groupe de ressources |

<sup>1</sup> Le compte de facturation est souvent désigné sous le terme de Contrat Entreprise ou Compte d’inscription.

<sup>2</sup> Le compte d’inscription est souvent désigné sous le terme de propriétaire du compte.

## <a name="enable-access-to-costs-in-the-ea-portal"></a>Activer l’accès aux coûts dans le portail EA

Pour l’étendue du compte de facturation, l’option d’**affichage des frais pour l’administrateur de service** doit être **activée** dans le portail EA. Pour toutes les autres étendues, l’option d’**affichage des frais pour le propriétaire du compte** doit être **activée** dans le portail EA.

Pour activer une option :

1. Connectez-vous au portail EA sur [https://ea.azure.com](https://ea.azure.com) avec un compte administrateur d’entreprise.
2. Sélectionnez **Gérer** dans le volet gauche.
3. Pour les étendues de Cost Management auxquelles vous souhaitez fournir l’accès, activez l’option d’**affichage des frais pour l’administrateur de service** et/ou d’**affichage des frais pour le propriétaire du compte**.  
    ![Onglet Inscription montrant les options d’affichage des frais pour l’administrateur de service et pour le propriétaire du compte](./media/assign-access-acm-data/ea-portal-enrollment-tab.png)

Une fois les options d’affichage des frais activées, la plupart des étendues nécessitent également une configuration des autorisations de contrôle d’accès en fonction du rôle (RBAC) dans le portail Azure.

## <a name="enterprise-administrator-role"></a>Rôle d’administrateur d’entreprise

Par défaut, un administrateur d’entreprise a accès au compte de facturation (Contrat Entreprise/compte d’inscription) et à toutes les autres étendues, qui sont des étendues enfants. L’administrateur d’entreprise affecte aux autres utilisateurs une autorisation d’accès aux étendues. Pour garantir la continuité de l’activité, le fait d’avoir toujours deux utilisateurs avec un accès d’administrateur d’entreprise est une bonne pratique. Les sections suivantes sont des exemples de procédures pas à pas, où l’administrateur d’entreprise affecte aux autres utilisateurs une autorisation d’accès aux étendues.

## <a name="assign-billing-account-scope-access"></a>Affecter une autorisation d’accès à l’étendue du compte de facturation

L’accès à l’étendue du compte de facturation nécessite une autorisation d’administrateur d’entreprise dans le portail EA. L’administrateur d’entreprise a accès à l’affichage des coûts pour l’ensemble des inscriptions EA ou pour plusieurs inscriptions. Aucune action n’est nécessaire dans le portail Azure pour l’étendue du compte de facturation.

1. Connectez-vous au portail EA sur [https://ea.azure.com](https://ea.azure.com) avec un compte administrateur d’entreprise.
2. Sélectionnez **Gérer** dans le volet gauche.
3. Sous l’onglet **Inscription**, sélectionnez l’inscription à gérer.  
    ![Portail EA](./media/assign-access-acm-data/ea-portal.png)
4. Cliquez sur **+ Ajouter un administrateur**.
5. Dans la zone Ajouter un administrateur, sélectionnez le type d’authentification, puis tapez l’adresse e-mail de l’utilisateur.
6. Si l’utilisateur doit avoir un accès en lecture seule aux données de coût et d’utilisation, sous **Lecture seule**, sélectionnez **Oui**.  Sinon, sélectionnez **Non**.
7. Cliquez sur **Ajouter** pour créer le compte.  
    ![Zone Ajouter un administrateur](./media/assign-access-acm-data/add-admin.png)

Un délai pouvant aller jusqu’à 30 minutes est parfois nécessaire pour que le nouvel utilisateur puisse accéder aux données dans Cost Management.

### <a name="assign-department-scope-access"></a>Affecter une autorisation d’accès à l’étendue de service

L’accès à l’étendue de service nécessite un accès d’administrateur de service (affichage des frais pour l’administrateur de service) dans le portail EA. L’administrateur de service a accès à l’affichage des données de coût et d’utilisation associées à un ou plusieurs services.  Les données du service incluent tous les abonnements appartenant à un compte d’inscription et liés au service. Aucune action n’est nécessaire dans le portail Azure.

1. Connectez-vous au portail EA sur [https://ea.azure.com](https://ea.azure.com) avec un compte administrateur d’entreprise.
2. Sélectionnez **Gérer** dans le volet gauche.
3. Sous l’onglet **Inscription**, sélectionnez l’inscription à gérer.
4. Cliquez sur l’onglet **Service**, puis sur **Ajouter un administrateur**.
5. Dans la zone d’ajout d’un administrateur de service, sélectionnez le type d’authentification, puis tapez l’adresse e-mail de l’utilisateur.
6. Si l’utilisateur doit avoir un accès en lecture seule aux données de coût et d’utilisation, sous **Lecture seule**, sélectionnez **Oui**.  Sinon, sélectionnez **Non**.
7. Sélectionnez les services auxquels vous souhaitez accorder une autorisation d’administration.
8. Cliquez sur **Ajouter** pour créer le compte.  
    ![Zone d’ajout d’administrateur de service](./media/assign-access-acm-data/add-depart-admin.png)

## <a name="assign-enrollment-account-scope-access"></a>Affecter une autorisation d’accès à l’étendue du compte d’inscription

L’accès à l’étendue du compte d’inscription nécessite un accès de propriétaire de compte (affichage des frais pour le propriétaire du compte) dans le portail EA. Le propriétaire du compte peut afficher les données de coût et d’utilisation associés à un compte d’inscription. Les données du compte d’inscription comprennent tous les abonnements Azure associés à l’inscription. Aucune action n’est nécessaire dans le portail Azure.

1. Connectez-vous au portail EA sur [https://ea.azure.com](https://ea.azure.com) avec un compte administrateur d’entreprise.
2. Sélectionnez **Gérer** dans le volet gauche.
3. Sous l’onglet **Inscription**, sélectionnez l’inscription à gérer.
4. Cliquez sur l’onglet **Compte**, puis sur **Ajouter un compte**.
5. Dans la zone Ajouter un compte, sélectionnez le **Service** auquel le compte doit être associé, ou laissez ce dernier non affecté.
6. Sélectionnez le type d’authentification, puis tapez le nom du compte.
7. Tapez l’adresse e-mail de l’utilisateur, puis indiquez éventuellement le centre de coûts.
8. Cliquez sur **Ajouter** pour créer le compte.  
    ![Zone Ajouter un compte](./media/assign-access-acm-data/add-account.png)

## <a name="assign-management-group-scope-access"></a>Affecter une autorisation d’accès à l’étendue du groupe d’administration

L’accès à une étendue de groupe d’administration nécessite au minimum une autorisation de lecteur Cost Management (ou lecteur). Vous pouvez configurer l’autorisation d’accès au groupe d’administration dans le portail Azure. Vous devez disposer au moins d’une autorisation de contributeur du groupe d’administration pour permettre l’accès à d’autres utilisateurs. De plus, vous devez également avoir activé le paramètre d’**affichage des frais pour le propriétaire du compte** dans le portail EA.

1. Connectez-vous au portail Azure sur [http://portal.azure.com](http://portal.azure.com).
2. Dans la barre latérale, sélectionnez **Tous les services**, recherchez _groupes d’administration_, puis sélectionnez **groupes d’administration**.
3. Sélectionnez le groupe d’administration dans la hiérarchie.
4. En regard du nom de votre groupe d’administration, cliquez sur **Détails**.
5. Dans le volet gauche, sélectionnez **Contrôle d’accès (IAM)**.
6. Cliquez sur **Add**.
7. Sous **Rôle**, sélectionnez **Lecteur Cost Management**.
8. Sous **Attribuer l’accès à**, sélectionnez **Utilisateur, groupe ou application Azure AD**.
9. Pour affecter une autorisation d’accès, recherchez, puis sélectionnez l’utilisateur.
10. Cliquez sur **Enregistrer**.  
    ![Zone Ajouter des autorisations](./media/assign-access-acm-data/add-permissions.png)

## <a name="assign-subscription-scope-access"></a>Affecter une autorisation d’accès à l’étendue d’abonnement

L’accès à un abonnement nécessite au minimum une autorisation de lecteur Cost Management (ou lecteur). Vous pouvez configurer l’autorisation d’accès à un abonnement dans le portail Azure. Vous devez disposer au moins d’une autorisation de contributeur de l’abonnement pour permettre l’accès à d’autres utilisateurs. De plus, vous devez également avoir activé le paramètre d’**affichage des frais pour le propriétaire du compte** dans le portail EA.

1. Connectez-vous au portail Azure sur [http://portal.azure.com](http://portal.azure.com).
2. Dans la barre latérale, sélectionnez **Tous les services**, recherchez _abonnements_, puis sélectionnez **Abonnements**.
3. Sélectionnez votre abonnement.
4. Dans le volet gauche, sélectionnez **Contrôle d’accès (IAM)**.
5. Cliquez sur **Add**.
6. Sous **Rôle**, sélectionnez **Lecteur Cost Management**.
7. Sous **Attribuer l’accès à**, sélectionnez **Utilisateur, groupe ou application Azure AD**.
8. Pour affecter une autorisation d’accès, recherchez, puis sélectionnez l’utilisateur.
9. Cliquez sur **Enregistrer**.

## <a name="assign-resource-group-scope-access"></a>Affecter une autorisation d’accès à l’étendue du groupe de ressources

L’accès à un groupe de ressources nécessite au minimum une autorisation de lecteur Cost Management (ou lecteur). Vous pouvez configurer l’autorisation d’accès à un groupe de ressources dans le portail Azure. Vous devez disposer au moins d’une autorisation de contributeur du groupe de ressources pour permettre l’accès à d’autres utilisateurs. De plus, vous devez également avoir activé le paramètre d’**affichage des frais pour le propriétaire du compte** dans le portail EA.

1. Connectez-vous au portail Azure sur [http://portal.azure.com](http://portal.azure.com).
2. Dans la barre latérale, sélectionnez **Tous les services**, recherchez _groupes de ressources_, puis sélectionnez **Groupes de ressources**.
3. Sélectionnez votre groupe de ressources.
4. Dans le volet gauche, sélectionnez **Contrôle d’accès (IAM)**.
5. Cliquez sur **Add**.
6. Sous **Rôle**, sélectionnez **Lecteur Cost Management**.
7. Sous **Attribuer l’accès à**, sélectionnez **Utilisateur, groupe ou application Azure AD**.
8. Pour affecter une autorisation d’accès, recherchez, puis sélectionnez l’utilisateur.
9. Cliquez sur **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

- Si vous n’avez pas encore effectué le premier guide de démarrage rapide relatif à Cost Management, lisez-le à partir de [Démarrer l’analyse des coûts](quick-acm-cost-analysis.md).
