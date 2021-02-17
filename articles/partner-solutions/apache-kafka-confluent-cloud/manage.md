---
title: Gérer un Confluent Cloud – Solutions partenaires Azure
description: Cet article décrit la gestion d’un Confluent Cloud sur le portail Azure. Comment configurer l’authentification unique, supprimer une organisation Confluent et obtenir de l’assistance.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/08/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: f8a54096ecda4729f7070120a02be3055f933cea
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99989120"
---
# <a name="manage-the-confluent-cloud-resource"></a>Gérer la ressource Confluent Cloud

Cet article explique comment gérer votre instance d’Apache Kafka pour Confluent Cloud sur Azure. Il montre comment configurer l’authentification unique (SSO), supprimer une organisation Confluent et créer une demande de support.

## <a name="single-sign-on"></a>Authentification unique

Pour implémenter l’authentification unique pour votre organisation, votre administrateur client peut importer l’application de galerie. Cette étape est facultative. Pour plus d’informations sur l’importation d’une application, consultez [Démarrage rapide : Ajout d’une application à un locataire Azure Active Directory (Azure AD)](../../active-directory/manage-apps/add-application-portal.md). Quand l’administrateur client importe l’application, les utilisateurs n’ont pas besoin de consentir explicitement à l’autorisation d’accès pour le portail Confluent Cloud.

Pour activer l’authentification unique, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Accédez à la **Vue d’ensemble** de votre instance de la ressource Confluent Cloud.
1. Sélectionnez le lien pour **Gérer sur le Confluent Cloud**.

   :::image type="content" source="media/sso-link.png" alt-text="Authentification unique du portail Confluent.":::

1. Si l’administrateur client n’a pas importé l’application de galerie pour le consentement à l’authentification unique, accordez des autorisations et un consentement. Cette étape n’est nécessaire que la première fois que vous accédez au lien vers **Gérer sur Confluent Cloud**.
1. Choisissez un compte de Azure AD pour l’authentification unique sur le portail Confluent Cloud.
1. Une fois le consentement fourni, vous êtes redirigé vers le portail Confluent Cloud.

## <a name="set-up-cluster"></a>Configurer un cluster

Pour plus d’informations sur la configuration de votre cluster, consultez [Créer un cluster dans Confluent Cloud – Documentation Confluent](https://docs.confluent.io/cloud/current/clusters/create-cluster.html).

## <a name="delete-confluent-organization"></a>Supprimer une organisation Confluent

Lorsque vous n’avez plus besoin de votre ressource Confluent Cloud, supprimez-la ressource dans Azure et Confluent Cloud.

Pour supprimer les ressources dans Azure :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez **Toutes les ressources** et **Filtrer par le nom** de la ressource Confluent Cloud ou le **Type de ressource** _Organisation Confluent_. Dans le portail Azure, recherchez le nom de la ressource.
1. Dans la **Vue d’ensemble** de la ressource, sélectionnez **Supprimer**.

    :::image type="content" source="media/delete-resources-icon.png" alt-text="Icône de suppression de ressource.":::

1. Pour confirmer la suppression, tapez le nom de la ressource, puis sélectionnez **Supprimer**.

    :::image type="content" source="media/delete-resources-prompt.png" alt-text="Invite pour confirmer la suppression de la ressource.":::

Pour supprimer la ressource sur Confluent Cloud, consultez la documentation relative aux [Environnements Confluent Cloud – Documentation Confluent](https://docs.confluent.io/current/cloud/using/environments.html) et [Concepts de base de Confluent Cloud – Documentation Confluent](https://docs.confluent.io/current/cloud/using/cloud-basics.html).

Le cluster et toutes les données qu’il contient sont définitivement supprimés. Si votre contrat comprend une clause de rétention des données, Confluent conserve vos données pendant la période spécifiée dans les [Conditions d’utilisation du service – Documentation Confluent](https://www.confluent.io/confluent-cloud-tos).

Vous êtes facturé au prorata de l’utilisation jusqu’à l’heure de suppression du cluster. Une fois votre cluster supprimé définitivement, Confluent vous envoie une confirmation par e-mail.

## <a name="get-support"></a>Obtenir de l’aide

Pour soumettre une demande de support à Confluent, contactez le [Support Confluent](https://support.confluent.io) ou soumettez une demande via le portail, comme indiqué ci-dessous.

> [!NOTE]
> Pour les tout nouveaux utilisateurs, réinitialisez votre mot de passe avant de vous connecter au portail du support Confluent. Si vous n’avez pas de compte Confluent Cloud, envoyez un e-mail à `cloud-support@confluent.io` pour obtenir une assistance supplémentaire.

Sur le portail, vous pouvez envoyer une demande par le biais de l’aide et du support Azure, ou directement à partir de votre instance d’Apache Kafka pour Confluent Cloud sur Azure.

Pour soumettre une demande par le biais de l’aide et du support Azure, procédez comme suit :

1. Sélectionnez **Aide + Support**.
1. Sélectionnez **Créer une demande de support**.
1. Dans le formulaire, sélectionnez **Technique** comme **Type de problème**. Sélectionnez votre abonnement. Dans la liste des services, sélectionnez **Confluent sur Azure**.

    :::image type="content" source="media/support-request-help.png" alt-text="Création d’une demande de support à partir de l’aide.":::

Pour soumettre une demande à partir de votre ressource, procédez comme suit :

1. Dans le portail Azure, sélectionnez votre organisation Confluent.
1. Dans le menu du côté gauche de l’écran, sélectionnez **Nouvelle demande de support**.
1. Pour créer une demande de support, sélectionnez le lien vers le **portail Confluent**.

    :::image type="content" source="media/support-request.png" alt-text="Création d’une demande de support à partir de l’instance.":::

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir de l’aide pour la résolution des problèmes, consultez [Résolution des problèmes des solutions Apache Kafka pour Confluent Cloud](troubleshoot.md).
