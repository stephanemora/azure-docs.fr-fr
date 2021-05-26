---
title: Se connecter à un serveur IBM MQ
description: Connectez-vous à un serveur MQ local ou dans Azure à partir d’un workflow à l’aide d’Azure Logic Apps.
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, estfan, logicappspm
ms.topic: article
ms.date: 05/25/2021
tags: connectors
ms.openlocfilehash: e852f0ce1584a0858f9523c1ea055d4f2cd616d2
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110376252"
---
# <a name="connect-to-an-ibm-mq-server-from-a-workflow-in-azure-logic-apps"></a>Se connecter à un serveur IBM MQ depuis un workflow dans Azure Logic Apps

Le connecteur MQ vous permet de connecter vos workflows d’application logique à un serveur IBM MQ local ou dans Azure. Vous pouvez ensuite faire en sorte que vos workflows reçoivent et envoient des messages stockés sur votre serveur MQ. Cet article fournit un guide de prise en main de l’utilisation du connecteur MQ en expliquant comment se connecter à votre serveur MQ et ajouter une action MQ à votre workflow. Par exemple, vous pouvez commencer par parcourir un message dans une file d’attente, avant de tenter les autres actions.

Ce connecteur inclut un client Microsoft MQ qui communique avec un serveur MQ distant sur un réseau TCP/IP. Vous pouvez vous connecter aux versions IBM WebSphere MQ suivantes :

* MQ 7.5
* MQ 8.0
* MQ 9.0, 9.1 et 9.2

<a name="available-operations"></a>

## <a name="available-operations"></a>Opérations disponibles

* Azure Logic Apps multilocataire : quand vous créez une ressource d’*application logique (consommation)* , vous pouvez vous connecter à un serveur MQ seulement en utilisant le connecteur MQ **managé**. Le connecteur fournit seulement des actions, et non pas des déclencheurs.

* Azure Logic Apps monolocataire : quand vous créez un workflow d’application logique monolocataire, vous pouvez vous connecter à un serveur MQ en utilisant le connecteur MQ managé, qui inclut *seulement* des actions, ou en utilisant les opérations MQ *intégrées*, qui incluent des déclencheurs *et* des actions.

Pour plus d’informations sur la différence entre un connecteur géré et les opérations intégrées, consultez les [termes clés dans Logic Apps](../logic-apps/logic-apps-overview.md#logic-app-concepts).

#### <a name="managed"></a>[Managé](#tab/managed)

La liste suivante décrit uniquement certaines des opérations managées disponibles pour MQ :

* Parcourir un message unique ou un tableau de messages sans le supprimer du serveur MQ. Pour plusieurs messages, vous pouvez spécifier le nombre maximal de messages à renvoyer par lot. Sinon, tous les messages sont retournés.
* Supprimer un seul message ou un tableau de messages du serveur MQ.
* Recevoir un message unique ou un tableau de messages, puis le supprimer du serveur MQ.
* Envoyez un message unique au serveur MQ.

Pour toutes les opérations du connecteur managé et autres informations techniques, telles que les propriétés, les limites, etc., consultez la [page de référence du connecteur MQ](/connectors/mq/).

#### <a name="built-in"></a>[Intégrée](#tab/built-in)

La liste suivante décrit uniquement certaines des opérations intégrées disponibles pour MQ :

* Quand un message est disponible dans une file d’attente, effectuez une action.
* Quand un ou plusieurs messages sont reçus d’une file d’attente (saisie semi-automatique), effectuez une action.
* Quand un ou plusieurs messages sont reçus d’une file d’attente (Peek-lock), effectuez une action.
* Recevoir un seul message ou un tableau de messages d’une file d’attente. Pour plusieurs messages, vous pouvez spécifier le nombre maximal de messages à renvoyer par lot et la taille de lot maximale en ko.
* Envoyer un seul message ou un tableau de messages au serveur MQ.

Ces opérations MQ intégrées offrent également les fonctionnalités suivantes, ainsi que les avantages de toutes les autres fonctionnalités de Logic Apps dans le [service Logic Apps à un seul locataire](../logic-apps/single-tenant-overview-compare.md) :

* Chiffrement TLS (Transport Layer Security) pour les données en transit
* Encodage de message pour les opérations d’envoi et de réception
* Prise en charge de l’intégration du réseau virtuel Azure lorsque votre application logique utilise le plan Azure Functions Premium

---

## <a name="limitations"></a>Limites

Le connecteur MQ n’utilise pas le champ **Format** du message et n’effectue pas de conversions de jeux de caractères. Le connecteur place uniquement les données qui apparaissent dans le champ de message dans un message JSON, et envoie le message.

## <a name="prerequisites"></a>Prérequis

* Un compte et un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Si vous utilisez un serveur MQ local, [installez la passerelle de données locale](../logic-apps/logic-apps-gateway-install.md) sur un serveur au sein de votre réseau. Pour que le connecteur MQ fonctionne, le serveur sur lequel vous installez la passerelle de données locale doit également avoir .NET Framework 4.6 installé.

  Après avoir installé la passerelle, vous devez également créer une ressource de passerelle de données dans Azure. Le connecteur MQ utilise cette ressource pour accéder à votre serveur MQ. Pour plus d’informations, consultez [Configurer la connexion à la passerelle de données](../logic-apps/logic-apps-gateway-connection.md). 

  > [!NOTE]
  > Vous n’avez pas besoin de la passerelle dans les scénarios suivants :
  > 
  > * Vous allez utiliser les opérations intégrées, et non le connecteur géré.
  > * Votre serveur MQ est disponible publiquement ou disponible dans Azure.

* Le workflow d’application logique où vous souhaitez accéder à votre serveur MQ. Votre ressource d’application logique doit avoir le même emplacement que votre ressource de passerelle dans Azure.

  Le connecteur MQ n’a aucun déclencheur, donc soit votre workflow doit déjà démarrer avec un déclencheur, soit vous devez d’abord ajouter un déclencheur à votre workflow. Par exemple, vous pouvez utiliser le [déclencheur Recurrence](../connectors/connectors-native-recurrence.md).

  Si vous débutez avec Azure Logic Apps, essayez ce [guide de démarrage rapide pour créer un exemple de workflow d’application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md), qui s’exécute dans le service Logic Apps mutualisé.

<a name="create-connection"></a>

## <a name="create-an-mq-connection"></a>Créer une connexion MQ 

Lorsque vous ajoutez une action MQ pour la première fois, vous êtes invité à créer une connexion à votre serveur MQ.

> [!NOTE]
> Actuellement, le connecteur MQ prend uniquement en charge l’authentification serveur, et pas l’authentification client. Pour plus d’informations, consultez [Problèmes de connexion et d’authentification](#connection-problems).

#### <a name="managed"></a>[Managé](#tab/managed)

1. Si vous vous connectez à un serveur MQ local, sélectionnez **Se connecter via une passerelle de données locale**.

1. Fournissez les informations de connexion pour votre serveur MQ.

   | Propriété | Local ou Azure | Description |
   |----------|----------------------|-------------|
   | **Passerelles** | Local uniquement | Sélectionnez l’option **Se connecter via la passerelle de données locale**. |
   | **Nom de connexion** | Les deux | Nom à utiliser pour votre connexion |
   | **Serveur** | Les deux | L’une des valeurs suivantes : <p><p>- Nom d’hôte du serveur MQ <br>- Adresse IP suivie d’un deux-points et du numéro de port |
   | **Nom du gestionnaire de file d’attente** | Les deux | Le gestionnaire de file d’attente que vous souhaitez utiliser |
   | **Nom du canal** | Les deux | Canal pour la connexion au gestionnaire de file d’attente |
   | **Nom de file d’attente par défaut** | Les deux | Nom par défaut de la file d’attente |
   | **Se connecter en tant que** | Les deux | Nom d’utilisateur pour la connexion au serveur MQ |
   | **Nom d’utilisateur** | Les deux | Vos informations d’identification - nom d’utilisateur |
   | **Mot de passe** | Les deux | Vos informations d’identification - mot de passe |
   | **Activer le protocole SSL ?** | Local uniquement | Utiliser Transport Layer Security (TLS) et Secure Sockets Layer (SSL) |
   | **Passerelle - Abonnement** | Local uniquement | L’abonnement Azure à associer à votre ressource de passerelle dans Azure |
   | **Passerelle - Passerelle de connexion** | Local uniquement | Ressource de passerelle à utiliser |
   ||||

   Par exemple :

   ![Capture d’écran montrant les détails de la connexion MQ gérée.](media/connectors-create-api-mq/managed-connection-properties.png)

1. Sélectionnez **Créer** lorsque vous avez terminé.

#### <a name="built-in"></a>[Intégrée](#tab/built-in)

1. Fournissez les informations de connexion pour votre serveur MQ.

   | Propriété | Local ou Azure | Description |
   |----------|----------------------|-------------|
   | **Nom de connexion** | Les deux | Nom à utiliser pour votre connexion |
   | **Nom du serveur** | Les deux | Le nom ou l’adresse IP du serveur MQ |
   | **Numéro de port** | Les deux | Numéro de port TCP pour la connexion au gestionnaire de file d’attente sur l’hôte |
   | **Channel** | Les deux | Canal pour la connexion au gestionnaire de file d’attente |
   | **Nom du gestionnaire de file d’attente** | Les deux | Le gestionnaire de file d’attente que vous souhaitez utiliser |
   | **Nom de file d’attente par défaut** | Les deux | Nom par défaut de la file d’attente |
   | **Se connecter en tant que** | Les deux | Nom d’utilisateur pour la connexion au serveur MQ |
   | **Nom d’utilisateur** | Les deux | Vos informations d’identification - nom d’utilisateur |
   | **Mot de passe** | Les deux | Vos informations d’identification - mot de passe |
   | **Utiliser TLS** | Les deux | Utiliser TLS (Transport Layer Security) |
   ||||

   Par exemple :

   ![Capture d’écran montrant les détails de la connexion MQ intégrée.](media/connectors-create-api-mq/built-in-connection-properties.png)

1. Sélectionnez **Créer** lorsque vous avez terminé.

---

<a name="add-action"></a>

## <a name="add-an-mq-action"></a>Ajouter une action MQ

Dans Azure Logic Apps, une action suit le déclencheur ou une autre action et effectue une opération dans votre workflow. Les étapes suivantes décrivent la manière générale d’ajouter une action, par exemple **Parcourir un seul message**.

1. Dans le concepteur Logic Apps, ouvrez votre workflow, s’il n’est pas déjà ouvert.

1. Dans le déclencheur ou une autre action, ajoutez une nouvelle étape.

   Pour ajouter une étape entre des étapes, déplacez votre souris sur la flèche. Cliquez sur le signe (+) qui s’affiche, puis sélectionnez **Ajouter une action**.

1. Dans la zone de recherche des opérations, entrez `mq`. Dans la liste des actions, sélectionnez l’action **Parcourir le message**.

1. Si vous êtes invité à créer une connexion à votre serveur MQ, [fournissez les informations de connexion demandées](#create-connection).

1. Dans l’action, fournissez les valeurs de propriété dont l’action a besoin.

   Pour ajouter d’autres propriétés, ouvrez la liste **Ajouter un nouveau paramètre**, puis sélectionnez les propriétés que vous souhaitez ajouter.

1. Lorsque c’est chose faite, dans la barre d’outils du concepteur, sélectionnez **Enregistrer**.

1. Pour tester votre workflow, sélectionnez **Exécuter** dans la barre d’outils du concepteur.

   Une fois l’exécution terminée, le concepteur affiche l’historique des exécutions du workflow ainsi que l’état de l’étape.

1. Pour passer en revue les entrées et les sorties de chaque étape exécutée (non ignorée), développez ou sélectionnez l’étape.

   * Pour examiner d’autres détails d’entrée, sélectionnez **Afficher les entrées brutes**.
   * Pour examiner d’autres détails de sortie, sélectionnez **Afficher les sorties brutes**. Si vous définissez **IncludeInfo** sur **true**, une sortie plus détaillée est incluse.

## <a name="troubleshoot-problems"></a>Résoudre les problèmes

### <a name="failures-with-browse-or-receive-actions"></a>Échecs avec des actions de navigation ou de réception

Si vous exécutez une action de navigation ou de réception sur une file d’attente vide, l’action échoue avec les sorties d’en-tête suivantes :

![Erreur MQ « Aucun message »](media/connectors-create-api-mq/mq-no-message-error.png)

<a name="connection-problems"></a>

### <a name="connection-and-authentication-problems"></a>Problèmes de connexion et d’authentification

Lorsque votre workflow tente de se connecter à votre serveur MQ local, vous pourriez recevoir cette erreur :

`"MQ: Could not Connect the Queue Manager '<queue-manager-name>': The Server was expecting an SSL connection."`

* Si vous utilisez le connecteur MQ directement dans Azure, le serveur MQ doit utiliser un certificat émis par une [autorité de certification](https://www.ssl.com/faqs/what-is-a-certificate-authority/) approuvée.

* Le serveur MQ requiert la définition de la spécification de chiffrement à utiliser pour les connexions TLS. Toutefois, à des fins de sécurité et pour inclure les meilleures suites de sécurité, le système d’exploitation Windows envoie un ensemble de spécifications de chiffrement prises en charge.

  Le système d’exploitation sur lequel le serveur MQ s’exécute choisit les suites à utiliser. Pour que la configuration corresponde, vous devez modifier le programme d’installation du serveur MQ afin que la spécification de chiffrement corresponde à l’option choisie dans la négociation TLS.

  Lorsque vous essayez la connexion, le serveur MQ enregistre un message d’événement qui indique que la tentative de connexion a échoué, car le serveur MQ a utilisé la mauvaise spécification de chiffrement. Le message d’événement contient la spécification de chiffrement que le serveur MQ a choisie dans la liste. Mettez à jour la spécification de chiffrement dans la configuration du canal pour qu’elle corresponde à la spécification de chiffrement du message d’événement.

## <a name="connector-reference"></a>Référence de connecteur

Pour toutes les opérations du connecteur managé et autres informations techniques, telles que les propriétés, les limites, etc., consultez la [page de référence du connecteur MQ](/connectors/mq/).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)
