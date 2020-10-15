---
title: Action de messagerie au sein de la supervision à distance - Azure | Microsoft Docs
description: Ce guide pratique vous montre comment ajouter une action de messagerie à une règle nouvelle ou existante.
author: dominicbetts
manager: hegate
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/12/2018
ms.topic: conceptual
ms.openlocfilehash: 2776b18dc3faded103b49e8ff58a8a0a6dccf144
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92074462"
---
# <a name="add-an-email-action"></a>Ajouter une action de messagerie

Les actions de messagerie permettent de garantir que vous ne manquez jamais une alerte. Vous pouvez ajouter une action de messagerie à une règle existante, ou quand vous créez une règle.

Pour effectuer les étapes décrites dans ce guide pratique, vous avez besoin d’une instance déployée de l’accélérateur de solution de supervision à distance dans votre abonnement Azure.

Pour créer ou modifier une règle, vous devez être un [**administrateur** ou disposer des autorisations appropriées](iot-accelerators-remote-monitoring-rbac.md).

## <a name="edit-an-existing-rule"></a>Modifier une règle existante

Effectuez les étapes suivantes pour ajouter une action de messagerie à une règle existante :

1. Accédez à votre solution de supervision à distance.

1. À partir du **tableau de bord**, accédez à la page **Règles** :

    ![Page Règles](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Cliquez sur la case à cocher en regard de la règle existante à modifier, puis sur **Modifier** en haut. Un panneau **Règle** modifiable s’affiche.

1. Dans la section **Action**, basculez **Email enabled** (Messagerie activée) sur **On** (Activée).

1. La première fois que vous activez une action de messagerie dans l’accélérateur de solution, vous devez [vous connecter à Outlook](#outlook).

1. Entrez une adresse e-mail dans la zone de destinataire et appuyez sur la touche **Entrée** pour chaque adresse e-mail à ajouter :

    ![Entrée d’adresse](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Entrez l’objet de l’e-mail.

1. Entrez toute remarque supplémentaire pour les destinataires de l’e-mail sous forme de texte brut. Vous pouvez utiliser la mise en forme HTML si vous [modifiez le modèle d’e-mail](#htmledit).

1. Assurez-vous que l’option **État de la règle** a la valeur **Activé**.

1. Cliquez sur **Appliquer**.

## <a name="create-a-new-rule"></a>Créer une nouvelle règle

Effectuez les étapes suivantes pour ajouter une action de messagerie quand vous créez une règle :

1. Accédez à votre solution de supervision à distance.

1. À partir du **tableau de bord**, accédez à la page **Règles** :

    ![Page Règles](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Effectuez les étapes de la [section Créer une règle](iot-accelerators-remote-monitoring-automate.md#create-a-rule). Effectuez ensuite les étapes de la section [Créer une règle avancée](iot-accelerators-remote-monitoring-automate.md#create-an-advanced-rule) jusqu’au stade où vous définissez un **niveau de gravité**. Ne cliquez pas encore sur **Appliquer**.

1. Dans la section **Action**, basculez **Email enabled** (Messagerie activée) sur **On** (Activée).

1. La première fois que vous activez une action de messagerie dans l’accélérateur de solution, vous devez [vous connecter à Outlook](#outlook).

1. Entrez une adresse e-mail dans la zone de destinataire et appuyez sur la touche **Entrée** pour chaque adresse e-mail à ajouter :

    ![Entrée d’adresse](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Entrez l’objet de l’e-mail.

1. Entrez toute remarque supplémentaire pour les destinataires de l’e-mail sous forme de texte brut. Vous pouvez utiliser la mise en forme HTML si vous [modifiez le modèle d’e-mail](#htmledit).

1. Assurez-vous que l’option **État de la règle** a la valeur **Activé**.

1. Cliquez sur **Appliquer**.

Votre règle avec une action de messagerie est maintenant activée. Chaque fois que l’action est déclenchée, un nouvel e-mail est envoyé aux destinataires.

## <a name="sign-in-to-outlook"></a>Se connecter à Outlook <a name="outlook"></a>

La première fois que vous activez une action de messagerie dans votre accélérateur de solution, vous devez vous connecter à Outlook. Cette action configure le compte e-mail qui envoie les notifications par e-mail.

> [!NOTE]
> Vous devez créer un compte Outlook spécifique uniquement pour les notifications de l’accélérateur de solution et utiliser ce compte quand vous activez votre première action de messagerie.

### <a name="contributor-role-outlook-setup"></a>Paramétrage d’Outlook pour le rôle Contributeur

Si une personne ayant le rôle **Contributeur** dans l’abonnement a déployé l’accélérateur de solution, l’application n’a pas les autorisations suffisantes pour configurer et vérifier les actions de messagerie via l’interface utilisateur web.

Avant de commencer, créez un compte Outlook à utiliser pour envoyer des notifications par e-mail à partir de votre accélérateur de solution.

Les étapes suivantes vous montrent comment configurer et vérifier les actions de messagerie manuellement :

1. Accédez au [portail Azure](https://portal.azure.com).

1. Accédez au groupe de ressources pour votre accélérateur de solution.

1. Cliquez sur **office365-connector** :

    ![ApiConnection](./media/iot-accelerators-remote-monitoring-email-actions/apiconnector1.png)

1. Cliquez sur la bannière pour commencer le processus d’autorisation :

    ![autoriser](./media/iot-accelerators-remote-monitoring-email-actions/connector1.png)

1. Cliquez sur **Autoriser**. Vous êtes invité à vous connecter. Le compte que vous utilisez pour vous connecter doit être l’adresse e-mail que l’application utilise pour envoyer des notifications par e-mail :

    ![Bouton Autoriser](./media/iot-accelerators-remote-monitoring-email-actions/authorize.png)

1. Cliquez sur **Enregistrer** au bas de la page. Votre autorisation est réussie si la bannière a disparu.

1. Pour changer l’adresse e-mail à partir de laquelle les notifications sont envoyées, cliquez sur **Modifier une connexion d’API**.

    ![changer d’adresse e-mail](./media/iot-accelerators-remote-monitoring-email-actions/editemail1.png)

### <a name="owner-role-outlook-setup"></a>Paramétrage d’Outlook pour le rôle Propriétaire

Si une personne ayant le rôle **Propriétaire** dans l’abonnement a déployé l’accélérateur de solution, l’application peut vérifier que les actions de messagerie ont été correctement configurées via l’interface utilisateur web.

Avant de commencer, créez un compte Outlook à utiliser pour envoyer des notifications par e-mail à partir de votre accélérateur de solution.

Les étapes suivantes vous aident à vous connecter et configurer les actions de messagerie :

1. Cliquez pour vous connecter à Outlook. Vous êtes redirigé vers le portail Azure :

   ![Se connecter à Outlook](./media/iot-accelerators-remote-monitoring-email-actions/owneroutlook-email.png)

1. Cliquez sur **Autoriser**. Vous êtes invité à vous connecter. Le compte que vous utilisez pour vous connecter doit être l’adresse e-mail que l’application utilise pour envoyer des notifications par e-mail :

1. Cliquez sur **Enregistrer**. Retournez à votre accélérateur de solution et actualisez la page.

1. Si vous avez correctement configuré la notification par e-mail, vous voyez ce message :

   ![Connexion à Outlook réussie](./media/iot-accelerators-remote-monitoring-email-actions/success-email.png)

## <a name="customize-the-email-html"></a>Personnaliser le modèle HTML d’e-mail <a name="htmledit"></a>

L’accélérateur de solution de supervision à distance fournit un modèle HTML de base prêt à l’emploi pour les e-mails des actions. Le modèle d’e-mail utilise les valeurs des paramètres des actions de messagerie. Voici un exemple d’e-mail :

![exemple d’e-mail](./media/iot-accelerators-remote-monitoring-email-actions/emailtemplate1.png)

Les étapes suivantes vous montrent comment modifier le modèle d’e-mail HTML. Par exemple, vous pouvez inclure plus d’informations ou ajouter des images personnalisées :

1. Clonez le dépôt GitHub de supervision à distance .NET ou Java :

1. Accédez à l’emplacement du modèle d’e-mail :
  
    `Dotnet: device-telemetry\ActionsAgent\data\EmailTemplate.html`
  
    `Java device-telemetry/app/resources/data/EmailTemplate.html`

1. Vous pouvez ajouter ou supprimer tous les paramètres dans ce modèle pour personnaliser le message. Vous pouvez également ajouter, supprimer ou remplacer des appels en fonction des besoins :

    Par exemple, dans le code .NET :  `emailTemplate = emailTemplate.Replace("${subject}", emailAction.GetSubject());`

    Par exemple, dans le code Java :  `this.emailTemplate.replace("${subject}", emailAction.GetSubject());`

1. Les paramètres dans le modèle prennent la forme `${...}`. Pour supprimer un paramètre, supprimez la ligne requise. Pour ajouter un paramètre, ajoutez une ligne avec la valeur à insérer.

1. Pour ajouter des images ou du texte personnalisé, mettez directement à jour le fichier EmailTemplate.HTML.

## <a name="throttling"></a>Limitation

L’accélérateur de solution de supervision à distance utilise Outlook pour envoyer des notifications par e-mail. Outlook limite le nombre d’e-mails envoyés à [30 e-mails par minute](/office365/servicedescriptions/exchange-online-service-description/exchange-online-limits#receiving-and-sending-limits). Les clients de messagerie qui reçoivent les e-mails peuvent également limiter le nombre d’e-mails reçus par minute. Vérifiez les limitations auprès de votre client de messagerie spécifique. Quand vous configurez la notification par e-mail pour une règle, la règle doit calculer les valeurs moyennes sur une période d’au moins une minute et non utiliser des valeurs instantanées :

![Calcul de la moyenne](./media/iot-accelerators-remote-monitoring-email-actions/calculation-email.png)

## <a name="next-steps"></a>Étapes suivantes

Ce guide vous a montré comment ajouter une action de messagerie à une règle nouvelle ou existante dans une solution de supervision à distance. Le guide vous a également montré comment modifier le code HTML qui définit le format du message.

L’étape suivante suggérée consiste à découvrir [comment utiliser les alertes et résoudre les problèmes liés aux appareils](iot-accelerators-remote-monitoring-maintain.md).