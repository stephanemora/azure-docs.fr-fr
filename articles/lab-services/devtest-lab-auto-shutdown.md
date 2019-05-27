---
title: Gérer les stratégies d’arrêt automatique dans Azure DevTest Labs | Microsoft Docs
description: Découvrez comment définir la stratégie d’arrêt automatique pour un laboratoire afin que les machines virtuelles sont arrêtées automatiquement lorsqu’ils ne sont pas en cours d’utilisation.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2019
ms.author: spelluru
ms.openlocfilehash: 9adf8dd4a5a3c469ed130b29308a0d828aee40bf
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873989"
---
# <a name="manage-autoshutdown-policies-for-a-lab-in-azure-devtest-labs"></a>Gérer les stratégies d’arrêt automatique pour un laboratoire dans Azure DevTest Labs
Azure DevTest Labs vous permet de contrôler les coûts et de réduire le gaspillage dans vos laboratoires en gérant les stratégies (paramètres) de chacun d’entre eux. Cet article vous montre comment configurer la stratégie d’arrêt automatique pour un compte de laboratoire et de configurer les paramètres d’arrêt automatique pour un laboratoire dans le compte de laboratoire. Pour savoir comment définir chaque stratégie de laboratoire, consultez [Définir des stratégies de laboratoire dans Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

## <a name="set-auto-shutdown-policy-for-a-lab"></a>Définir la stratégie d’arrêt automatique pour un laboratoire
En tant que propriétaire d’un laboratoire, vous pouvez configurer une planification d’arrêt pour toutes les machines virtuelles de votre laboratoire. Ceci vous permet de réaliser des économies en stoppant l’exécution de machines inutilisées (inactives). Vous pouvez appliquer une stratégie d’arrêt centralisée à toutes les machines virtuelles de votre laboratoire et, également, épargner aux utilisateurs de votre laboratoire l’établissement d’une planification pour leurs machines individuelles. Cette fonctionnalité vous permet de définir la stratégie de planification de votre laboratoire, du contrôle inexistant au contrôle total, pour les utilisateurs de votre laboratoire. En tant que propriétaire de laboratoire, vous pouvez configurer cette stratégie en procédant comme suit :

1. Sur la page d’accueil de votre laboratoire, sélectionnez **Configuration et stratégies**.
2. Sélectionnez **Stratégie d'arrêt automatique** dans la section **Planifications** du menu de gauche.
3. Sélectionnez l'une des options. Les sections suivantes vous donnent plus de détails sur ces options : La stratégie définie s’applique uniquement aux nouvelles machines virtuelles créées dans le laboratoire et pas aux machines virtuelles déjà existants. 

    ![Automatiquement les options de stratégie d’arrêt](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

## <a name="configure-auto-shutdown-settings"></a>Configurer les paramètres d’arrêt automatique
La stratégie d’arrêt automatique permet de réduire les pertes en vous permettant de spécifier l’heure à laquelle l’arrêt de machines virtuelles du laboratoire.

Pour afficher (et modifier) les stratégies d’un laboratoire, procédez comme suit :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services**, puis **DevTest Labs** dans la liste.
3. Sélectionnez le laboratoire souhaité dans la liste des laboratoires.   
4. Sélectionnez **Configuration et stratégies**.

    ![Volet Paramètres de stratégie](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. L’atelier **Configuration et stratégies** volet, sélectionnez **arrêt automatique** sous **planifications**.
   
    ![Arrêt automatique](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. Sélectionnez **Activer** ou **Désactiver** pour activer ou désactiver cette stratégie.
7. Si vous activez cette stratégie, spécifiez l’heure (et le fuseau horaire) de l’arrêt pour toutes les machines virtuelles du laboratoire actif.
8. Spécifiez **Oui** ou **non** pour l’option Envoyer une notification 15 minutes avant l’heure d’arrêt automatique spécifiée. Si vous choisissez **Oui**, saisissez un point de terminaison de l’URL de Webhook ou une adresse e-mail spécifiant où vous désirez publier ou envoyer la notification. L’utilisateur reçoit une notification et peut retarder l’arrêt. Pour plus d’informations, consultez le [Notifications](#notifications) section. 
9. Sélectionnez **Enregistrer**.

    Par défaut, une fois activée, cette stratégie s’applique à toutes les machines virtuelles dans le laboratoire en cours. Pour supprimer ce paramètre à partir d’une machine virtuelle spécifique, ouvrez le volet de gestion de la machine virtuelle et modifiez son **arrêt automatique** paramètre.

### <a name="user-sets-a-schedule-and-can-opt-out"></a>L'utilisateur définit une planification qu'il peut refuser
Si vous définissez cette stratégie pour votre laboratoire, les utilisateurs du laboratoire peuvent remplacer ou refuser la planification du laboratoire. Cette option accorde aux utilisateurs du laboratoire un contrôle total sur la planification d’arrêt automatique de leurs machines virtuelles. Les utilisateurs du laboratoire ne voient aucune modification de leur page de planification d’arrêt automatique des machine virtuelles.

![Option de stratégie - 1 arrêt automatique](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>L'utilisateur définit une planification qu'il ne peut pas refuser
Si vous définissez cette stratégie pour votre laboratoire, les utilisateurs du laboratoire peuvent remplacer la planification du laboratoire. Ils ne peuvent cependant pas renoncer à la stratégie d’arrêt automatique. Cette option permet de s’assurer que chaque machine de votre laboratoire fait l’objet d’une planification d’arrêt automatique. Les utilisateurs du laboratoire peuvent mettre à jour la planification d’arrêt automatique de leurs machines virtuelles et définir des notifications d’arrêt.

![Option de stratégie - 2 arrêt automatique](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>L'utilisateur n'a aucun contrôle sur la planification définie par l'administrateur du laboratoire
Si vous définissez cette stratégie pour votre laboratoire, les utilisateurs du laboratoire ne peuvent ni remplacer ni refuser la planification du laboratoire. Cette option offre à l’administrateur du laboratoire le contrôle total sur la planification de chaque machine du laboratoire. Les utilisateurs du laboratoire ne peuvent configurer de notifications d’arrêt automatique que pour leurs machines virtuelles.

![Option de stratégie - 3 arrêt automatique](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="notifications"></a>Notifications
Une fois que l’arrêt automatique de la configurer par le propriétaire de laboratoire, notifications seront envoyées pour les utilisateurs du laboratoire 15 minutes avant l’arrêt automatique déclenchée si un de leurs machines virtuelles seront affectées. Cette option permet aux utilisateurs de laboratoire pour enregistrer leur travail avant l’arrêt. La notification fournit également des liens pour chaque machine virtuelle pour les actions suivantes :

- Pour l’instant, ignorez l’arrêt automatique
- Répéter l’arrêt automatique pour une heure ou 2 heures, afin qu’ils peuvent continuer à travailler sur la machine virtuelle.

Notification est envoyée via le point de terminaison de raccordement web configurés ou une adresse de messagerie spécifiée par les propriétaires de laboratoire dans les paramètres de l’arrêt automatique. Les Webhooks vous permettent de créer ou de configurer les intégrations s’abonner à certains événements. Lorsqu’un de ces événements est déclenché, dev/test enverra une charge utile HTTP POST vers l’URL configurée du webhook. Pour plus d’informations sur les webhooks, consultez [Créer une fonction Azure d’API ou de webhook](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

Nous vous recommandons d’utiliser les raccordements web, car elles sont largement pris en charge par différentes applications (par exemple, Slack, Azure Logic Apps et ainsi de suite.) et vous permet d’implémenter votre propre méthode pour envoyer des notifications. Par exemple, cet article vous montre comment obtenir la notification d’arrêt automatique des e-mails à l’aide d’Azure Logic Apps. Tout d’abord, nous allons passez rapidement en revue les étapes de base pour activer la notification d’arrêt automatique dans votre laboratoire.   

## <a name="create-a-logic-app-that-receives-email-notifications"></a>Créer une application logique qui reçoit des notifications par courrier électronique
[Azure Logic Apps](../logic-apps/logic-apps-overview.md) fournit de nombreux connecteurs d’out-of-the-box qui permet de facilement intègrent un service avec d’autres clients, tels que Office 365 et twitter. À un niveau élevé, les étapes pour configurer une application logique pour la notification par courrier électronique peuvent être divisées en quatre phases : 

- Créez une application logique. 
- Configurer le modèle intégré.
- Intégrer avec votre client de messagerie
- Obtenir l’URL du Webhook.

### <a name="create-a-logic-app"></a>Créer une application logique
Pour commencer, créez une application logique dans votre abonnement Azure en procédant comme suit :

1. Sélectionnez **+ créer une ressource** dans le menu de gauche, sélectionnez **intégration**, puis sélectionnez **application logique**. 

    ![Nouveau menu de l’application logique](./media/devtest-lab-auto-shutdown/new-logic-app.png)
2. Sur le **application logique - créer** page, procédez comme suit : 
    1. Entrez un **nom** de l’application logique.
    2. Sélectionnez votre **abonnement**Azure.
    3. Créez un **groupe de ressources** ou sélectionnez-en un. 
    4. Sélectionnez un **emplacement** de l’application logique. 

        ![Nouvelle application logique - paramètres](./media/devtest-lab-auto-shutdown/new-logic-app-page.png)
3. Dans le **Notifications**, sélectionnez **accéder à la ressource** sur la notification. 

    ![Accéder à la ressource](./media/devtest-lab-auto-shutdown/go-to-resource.png)
4. Sélectionnez **Concepteur d’application logique** sous **outils de déploiement** catégorie.

    ![Sélectionnez la requête/réponse HTTP](./media/devtest-lab-auto-shutdown/select-http-request-response-option.png)
5. Sur le **demande-réponse HTTP** page, sélectionnez **utiliser ce modèle**. 

    ![Choisissez d’utiliser cette option de modèle](./media/devtest-lab-auto-shutdown/select-use-this-template.png)
6. Copiez le JSON suivant dans le **schéma de JSON de corps de requête** section : 

    ```json
    {
        "$schema": "http://json-schema.org/draft-04/schema#",
        "properties": {
            "delayUrl120": {
                "type": "string"
            },
            "delayUrl60": {
                "type": "string"
            },
            "eventType": {
                "type": "string"
            },
            "guid": {
                "type": "string"
            },
            "labName": {
                "type": "string"
            },
            "owner": {
                "type": "string"
            },
            "resourceGroupName": {
                "type": "string"
            },
            "skipUrl": {
                "type": "string"
            },
            "subscriptionId": {
                "type": "string"
            },
            "text": {
                "type": "string"
            },
            "vmName": {
                "type": "string"
            }
        },
        "required": [
            "skipUrl",
            "delayUrl60",
            "delayUrl120",
            "vmName",
            "guid",
            "owner",
            "eventType",
            "text",
            "subscriptionId",
            "resourceGroupName",
            "labName"
        ],
        "type": "object"
    }
    ```
    
    ![Schéma JSON du corps de la demande](./media/devtest-lab-auto-shutdown/request-json.png)
7. Sélectionnez **+ nouvelle étape** dans le concepteur, procédez comme suit :
    1. Recherchez **Office 365 Outlook - envoyer un message électronique**. 
    2. Sélectionnez **envoyer un courrier électronique** de **Actions**. 
    
        ![Envoyer l’option d’e-mail](./media/devtest-lab-auto-shutdown/select-send-email.png)
    3. Sélectionnez **connectez-vous** pour se connecter à votre compte de messagerie. 
    4. Sélectionnez **TO** champ, puis choisissez propriétaire.
    5. Sélectionnez **sujet**et saisir un objet de la notification par courrier électronique. Exemple : « Arrêt de vmName machine pour laboratoire : labName. »
    6. Sélectionnez **corps**et définir le contenu du corps de la notification par courrier électronique. Par exemple : « vmName est planifiée pour arrêter des 15 dernières minutes. Ignorer cet arrêt en cliquant sur : URL. Arrêt de délai pendant une heure : delayUrl60. Arrêt de délai de 2 heures : delayUrl120. »

        ![Schéma JSON du corps de la demande](./media/devtest-lab-auto-shutdown/email-options.png)
1. Sélectionnez **Enregistrer** dans la barre d’outils. Maintenant, vous pouvez copier le **URL HTTP POST**. Sélectionnez le bouton Copier pour copier l’URL dans le Presse-papiers. 

    ![URL du WebHook](./media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="next-steps"></a>Étapes suivantes
Pour savoir comment définir toutes les stratégies, consultez [définir des stratégies de laboratoire dans Azure DevTest Labs](devtest-lab-set-lab-policy.md).
