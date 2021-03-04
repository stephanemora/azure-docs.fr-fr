---
title: Connecteur de gestion des services informatiques – Exportation sécurisée dans Azure Monitor - Configurations Azure
description: Cet article vous montre comment configurer Azure pour connecter vos produits/services ITSM à l’aide de l’exportation sécurisée dans Azure Monitor, afin de superviser et de gérer de manière centralisée les éléments de travail ITSM.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 01/03/2021
ms.openlocfilehash: 8eb9430e3d280c52cf84c61f0a44cb12152ac054
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037538"
---
# <a name="configure-azure-to-connect-itsm-tools-using-secure-export"></a>Configurer Azure pour connecter les outils ITSM à l’aide de l’exportation sécurisée

Cet article fournit des informations sur la configuration d’Azure afin d’utiliser l’exportation sécurisée.
Pour utiliser l’exportation sécurisée, procédez comme suit :

1. [Inscrire votre application auprès d’Azure AD.](./itsm-connector-secure-webhook-connections-azure-configuration.md#register-with-azure-active-directory)
1. [Définissez le principal du service.](./itsm-connector-secure-webhook-connections-azure-configuration.md#define-service-principal)
1. [Créer un groupe d’actions Webhook sécurisé](./itsm-connector-secure-webhook-connections-azure-configuration.md#create-a-secure-webhook-action-group)
1. Configurer votre environnement partenaire
    Secure Export prend en charge les connexions avec les outils ITSM suivants :
    * [ServiceNow](./itsmc-secure-webhook-connections-servicenow.md)
    * [BMC Helix](./itsmc-secure-webhook-connections-bmc.md)

## <a name="register-with-azure-active-directory"></a>S’inscrire auprès d’Azure Active Directory

Pour inscrire l’application auprès d’Azure AD, suivez ces étapes :

1. Suivez la procédure décrite dans [Inscrire une application auprès de la plateforme d’identités Microsoft](../../active-directory/develop/quickstart-register-app.md).
2. Dans Azure AD, sélectionnez **Exposer l’application**.
3. Sélectionnez **Définir** pour l’**URI d’ID d’application**.

   [![Capture d’écran de l’option permettant de définir l’URI de l’ID d’application.](media/itsm-connector-secure-webhook-connections-azure-configuration/azure-ad.png)](media/itsm-connector-secure-webhook-connections-azure-configuration/azure-ad-expand.png#lightbox)
4. Sélectionnez **Enregistrer**.

## <a name="define-service-principal"></a>Définir le principal du service

Le service Groupe d’actions est une application interne. Par conséquent, il a l’autorisation d’acquérir des jetons d’authentification auprès de votre application AAD pour pouvoir s’authentifier auprès de ServiceNow.
En guise d’étape facultative, vous pouvez définir le rôle d’application dans le manifeste de l’application créée, ce qui peut vous permettre de restreindre davantage l’accès de manière à ce que seules certaines applications ayant un rôle spécifique puissent envoyer des messages. Ce rôle doit être attribué au principal du service du Groupe d’actions (Requiert des privilèges d’administrateur de locataire).

Cette étape peut être effectuée à l’aide de la même [commande PowerShell](../alerts/action-groups.md#secure-webhook-powershell-script).

## <a name="create-a-secure-webhook-action-group"></a>Créer un groupe d’actions Webhook sécurisé

Une fois votre application inscrite auprès d’Azure AD, vous pouvez créer des éléments de travail dans votre outil ITSM, en fonction des alertes Azure et au moyen de l’action Webhook sécurisé dans des groupes d’actions.

Les groupes d’actions offrent une méthode modulaire et réutilisable de déclenchement d’actions pour les alertes Azure. Vous pouvez utiliser des groupes d’actions avec des alertes de métriques, des alertes de journal d’activité et des alertes Azure Log Analytics dans le portail Azure.
Pour en savoir plus sur les groupes d’actions, consultez [Créer et gérer des groupes d’actions dans le portail Azure](../alerts/action-groups.md).

Pour ajouter un Webhook à une action, suivez les instructions ci-dessous pour le Webhook sécurisé :

1. Dans le [portail Azure](https://portal.azure.com/), recherchez et sélectionnez **Monitor** Le volet **Moniteur** consolide tous vos paramètres et données de supervision dans une même vue.
2. Sélectionnez **Alertes** > **Gérer les actions**.
3. Sélectionnez [Ajouter un groupe d’actions](../alerts/action-groups.md#create-an-action-group-by-using-the-azure-portal) et renseignez les champs.
4. Entrez un nom dans la boîte **Nom du groupe d’actions** et entrez un nom dans la boîte **Nom court**. Le nom court est utilisé à la place du nom complet du groupe d’actions lorsque les notifications sont envoyées à l’aide de ce groupe.
5. Sélectionnez **Webhook sécurisé**.
6. Sélectionnez les informations suivantes :
   1. Sélectionnez l’ID d’objet de l’instance Azure Active Directory que vous avez inscrite.
   2. Pour l’URI, collez l’URL du webhook que vous avez copiée dans l’[environnement de l’outil ITSM](#configure-the-itsm-tool-environment).
   3. Définissez **Activer le schéma d’alerte commun** sur **Oui**. 

   L’illustration suivante montre la configuration d’un exemple d’action Webhook sécurisé :

   ![Capture d’écran illustrant une action Webhook sécurisé.](media/itsm-connector-secure-webhook-connections-azure-configuration/secure-webhook.png)

## <a name="configure-the-itsm-tool-environment"></a>Configurer l’environnement de l’outil ITSM

La configuration comporte deux étapes :

1. Obtenir l’URI pour la définition d’exportation sécurisée.
2. Définitions selon le flux de l’outil ITSM.

## <a name="next-steps"></a>Étapes suivantes

* [Configuration de l’exportation sécurisée ServiceNow](./itsmc-secure-webhook-connections-servicenow.md)
* [Configuration de l’exportation sécurisée BMC](./itsmc-secure-webhook-connections-bmc.md)
