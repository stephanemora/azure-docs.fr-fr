---
title: Sécuriser le trafic entre les workflows monolocataires et les réseaux virtuels
description: Sécurisez le trafic entre réseaux virtuels, comptes de stockage et flux de travail monolocataires dans Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 08/31/2021
ms.openlocfilehash: 658d8c8c43bd2795a6a25730ff85ffb6bbd3a63c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598172"
---
# <a name="secure-traffic-between-virtual-networks-and-single-tenant-workflows-in-azure-logic-apps-using-private-endpoints"></a>Sécuriser le trafic entre réseaux virtuels et flux de travail monolocataires dans Azure Logic Apps à l’aide de points de terminaison privés

Pour communiquer de manière sécurisée et privée entre le flux de travail de votre application logique et un réseau virtuel, vous pouvez configurer des *points de terminaison privés* pour le trafic entrant et utiliser une intégration de réseau virtuel pour le trafic sortant.

Un point de terminaison privé est une interface réseau qui se connecte de façon privée et sécurisée à un service basé sur Azure Private Link. Le service peut être un service Azure tel qu’Azure Logic Apps, Stockage Azure, Azure Cosmos DB, SQL, ou votre propre service Private Link. Le point de terminaison privé utilise une adresse IP privée de votre réseau virtuel, plaçant de fait le service dans votre réseau virtuel.

Cet article explique comment configurer l’accès via des points de terminaison privés pour le trafic entrant, le trafic sortant et la connexion à des comptes de stockage.

Pour plus d’informations, consultez la documentation suivante :

- [Qu’est-ce qu’Azure Private Endpoint ?](../private-link/private-endpoint-overview.md)

- [Qu’est-ce que Liaison privée Azure ?](../private-link/private-link-overview.md)

- [Qu’est-ce qu’un flux de travail d’application logique monolocataire dans Azure Logic Apps ?](single-tenant-overview-compare.md)

## <a name="prerequisites"></a>Prérequis

Vous devez disposer d’un réseau virtuel Azure nouveau ou existant qui comprend un sous-réseau sans délégation. Ce sous-réseau est utilisé pour déployer et allouer des adresses IP privées à partir du réseau virtuel.

Pour plus d’informations, consultez la documentation suivante :

- [Démarrage rapide : Créer un réseau virtuel au moyen du portail Azure](../virtual-network/quick-create-portal.md)

- [Qu’est-ce que la délégation de sous-réseau ?](../virtual-network/subnet-delegation-overview.md)

- [Ajouter ou supprimer une délégation de sous-réseau](../virtual-network/manage-subnet-delegation.md)

<a name="set-up-inbound"></a>

## <a name="set-up-inbound-traffic-through-private-endpoints"></a>Configurer le trafic entrant via des points de terminaison privés

Pour sécuriser le trafic entrant vers votre flux de travail, suivez les étapes de haut niveau suivantes :

1. Démarrez votre flux de travail avec un déclencheur intégré qui peut recevoir et traiter des demandes entrantes, telles que le déclencheur de demande ou le déclencheur HTTP + Webhook. Ce déclencheur configure votre flux de travail avec un point de terminaison pouvant être appelé.

1. Créer un point de terminaison privé dans votre réseau virtuel.

1. Effectuez des appels de test pour vérifier l’accès au point de terminaison. Pour appeler le flux de travail de votre application logique après avoir configuré ce point de terminaison, vous devez être connecté au réseau virtuel.

### <a name="prerequisites-for-inbound-traffic-through-private-endpoints"></a>Conditions préalables pour le trafic entrant via des points de terminaison privés

En plus de la [configuration de réseau virtuel dans les conditions préalables de niveau supérieur](#prerequisites), vous devez disposer d’un flux de travail d’application logique, nouveau ou existant, basé sur un locataire unique, qui démarre avec un déclencheur intégré pouvant recevoir des demandes.

Par exemple, le déclencheur de demande crée un point de terminaison sur votre flux de travail, qui peut recevoir et gérer des demandes entrantes d’autres appelants, dont des flux de travail. Ce point de terminaison fournit une URL que vous pouvez utiliser pour appeler et déclencher le flux de travail. Pour cet exemple, les étapes se poursuivent avec le déclencheur de demande.

Pour plus d’informations, consultez la documentation suivante :

- [Créer des flux de travail d’application logique monolocataires dans Azure Logic Apps](create-single-tenant-workflows-azure-portal.md)

- [Recevoir des requêtes HTTPS entrantes et y répondre à l’aide d’Azure Logic Apps](../connectors/connectors-native-reqres.md)

### <a name="create-the-workflow"></a>Créer le flux de travail

1. Si vous ne l’avez pas encore fait, créez une application logique basée sur un seul locataire et un flux de travail vide.

1. Une fois le concepteur ouvert, ajoutez le déclencheur de demande comme première étape dans votre flux de travail.

   > [!NOTE]
   > Vous pouvez appeler des déclencheurs de demande et des déclencheurs de Webhook uniquement à partir de votre réseau virtuel. Les déclencheurs et actions de Webhook d’API managée ne fonctionnent pas, car ils requièrent un point de terminaison public pour recevoir des appels. 

1. En fonction des exigences de votre scénario, ajoutez de actions que vous souhaitez exécuter dans votre flux de travail.

1. Lorsque vous avez terminé, enregistrez votre flux de travail.

Pour plus d’informations, consultez [Créer des flux de travail d’application logique monolocataires dans Azure Logic Apps](create-single-tenant-workflows-azure-portal.md).

#### <a name="copy-the-endpoint-url"></a>Copier l’URL de point de terminaison

1. Dans le menu du flux de travail, sélectionnez **Vue d’ensemble**.

1. Sur la page **Vue d’ensemble**, copiez et enregistrez l’**URL du flux de travail** en vue d’une utilisation ultérieure.

   Pour déclencher le flux de travail, vous appelez ou envoyez une demande à cette URL.

1. Assurez-vous que l’URL fonctionne en appelant ou en envoyant une demande à l’URL. Vous pouvez utiliser n’importe quel outil pour envoyer la demande, par exemple, Postman.

### <a name="set-up-private-endpoint-connection"></a>Configurer une connexion de point de terminaison privé

1. Dans le menu de votre application logique, sous **Paramètres**, sélectionnez **Mise en réseau**.

1. Sur la page **Mise en réseau**, sous **Connexions de point de terminaison privé**, sélectionnez **Configurer vos connexions de point de terminaison privé**.

1. Sur la page **Connexions de point de terminaison privé**, sélectionnez **Ajouter**.

1. Dans le volet **Ajouter un point de terminaison privé** qui s’ouvre, fournissez les informations demandées sur le point de terminaison.

   Pour plus d’informations, consultez [Propriétés du point de terminaison privé](../private-link/private-endpoint-overview.md#private-endpoint-properties).

1. Après qu’Azure a correctement approvisionné le point de terminaison privé, réessayez d’appeler l’URL du flux de travail.

   Cette fois-ci, vous recevez une erreur `403 Forbidden` attendue, ce qui signifie que le point de terminaison privé est configuré et fonctionne correctement.

1. Pour vous assurer que la connexion fonctionne correctement, créez une machine virtuelle dans le même réseau virtuel qui possède le point de terminaison privé, puis essayez d’appeler le flux de travail de l’application logique.

### <a name="considerations-for-inbound-traffic-through-private-endpoints"></a>Considérations relatives au trafic entrant via des points de terminaison privés

- En cas d’accès depuis l’extérieur de votre réseau virtuel, l’affichage de la surveillance ne peut pas accéder aux entrées et sorties des déclencheurs et des actions.

- Le déploiement à partir de Visual Studio Code ou Azure CLI fonctionne uniquement à partir du réseau virtuel. Vous pouvez utiliser le centre de déploiement pour lier votre application logique à un dépôt GitHub. Vous pouvez ensuite utiliser l’infrastructure Azure pour créer et déployer votre code.

  Pour que l’intégration de GitHub fonctionne, supprimez le paramètre `WEBSITE_RUN_FROM_PACKAGE` de votre application logique ou définissez la valeur sur `0`.

- L’activation de Private Link n’affecte pas le trafic sortant, qui transite toujours par l’infrastructure App Service.

<a name="set-up-outbound"></a>

## <a name="set-up-outbound-traffic-through-private-endpoints"></a>Configurer le trafic sortant via des points de terminaison privés

Pour sécuriser le trafic sortant de votre application logique, vous pouvez intégrer votre application logique avec un réseau virtuel. Par défaut, le trafic sortant de votre application logique est uniquement affecté par les groupes de sécurité réseau et les itinéraires définis par l’utilisateur (UDR) quand vous accédez à une adresse privée, telle que `10.0.0.0/8`, `172.16.0.0/12` et `192.168.0.0/16`.

Si vous utilisez votre propre serveur de noms de domaine (DNS) avec votre réseau virtuel, définissez le paramètre d’application `WEBSITE_DNS_SERVER` de votre ressource d’application logique sur l’adresse IP de votre DNS. Si vous avez un DNS secondaire, ajoutez un autre paramètre d’application nommé `WEBSITE_DNS_ALT_SERVER` et définissez la valeur sur l’adresse IP de votre DNS. Par ailleurs, mettez à jour vos enregistrements DNS pour faire pointer vos points de terminaison privés vers votre adresse IP interne. Les points de terminaison privés envoient la recherche DNS à l’adresse privée, et non à l’adresse publique de la ressource spécifique. Pour en savoir plus, consultez [Points de terminaison privés – Intégrer votre application à un réseau virtuel Azure](../app-service/web-sites-integrate-with-vnet.md#private-endpoints).

> [!IMPORTANT]
> Pour que le runtime Azure Logic Apps fonctionne, vous devez disposer d’une connexion ininterrompue au stockage principal. Pour que les connecteurs gérés hébergés par Azure fonctionnent, vous devez disposer d’une connexion ininterrompue au service de l’API managée.

### <a name="considerations-for-outbound-traffic-through-private-endpoints"></a>Considérations relatives au trafic sortant via des points de terminaison privés

La configuration de l’intégration du réseau virtuel ne concerne que le trafic sortant. Pour sécuriser le trafic entrant, qui continue à utiliser le point de terminaison partagé App Service, consultez [Configurer le trafic entrant via des points de terminaison privés](#set-up-inbound).

Pour plus d’informations, consultez la documentation suivante :

- [Intégrer votre application à un réseau virtuel Azure](../app-service/web-sites-integrate-with-vnet.md)

- [Groupes de sécurité réseau](../virtual-network/network-security-groups-overview.md)

- [Routage du trafic de réseau virtuel](../virtual-network/virtual-networks-udr-overview.md)

## <a name="connect-to-storage-account-with-private-endpoints"></a>Se connecter à un compte de stockage avec des points de terminaison privés

Vous pouvez restreindre l’accès au compte de stockage pour que seules les ressources d’un réseau virtuel puissent se connecter. Le service Stockage Azure prend en charge l’ajout de points de terminaison privés à votre compte de stockage. Les flux de travail de votre application logique peuvent ensuite utiliser ces points de terminaison pour communiquer avec le compte de stockage. Pour plus d’informations, consultez [Utiliser des points de terminaison privés pour Stockage Azure](../storage/common/storage-private-endpoints.md).

> [!NOTE]
> Les étapes suivantes requièrent l’activation temporaire de l’accès public sur votre compte de stockage. Si vous ne pouvez pas activer l’accès public en raison des stratégies de votre organisation, vous pouvez toujours déployer votre application logique à l’aide d’un compte de stockage privé. Cependant, vous devez utiliser un modèle Azure Resource Manager (modèle ARM) pour le déploiement. Pour obtenir un exemple de modèle ARM, consultez [Déployer une application logique à l’aide d’un compte de stockage sécurisé avec des points de terminaison privés](https://github.com/VeeraMS/LogicApp-deployment-with-Secure-Storage).

1. Créez des points de terminaison privés différents pour chacun des services de stockage Table, File d’attente, Blob et Fichier.

1. Activez l’accès public temporaire sur votre compte de stockage lorsque vous déployez votre application logique.

   1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre ressource de compte de stockage.

   1. Dans le menu de la ressource de compte de stockage, sous **Sécurité + mise en réseau**, sélectionnez **Mise en réseau**.

   1. Dans le volet **Mise en réseau**, dans l’onglet **Pare-feux et réseaux virtuels**, sous **Autoriser l’accès depuis**, sélectionnez **Tous les réseaux**.

1. Déployez votre ressource d’application logique à l’aide du portail Azure ou de Visual Studio Code.

1. Une fois le déploiement terminé, activez l’intégration entre votre application logique et les points de terminaison privés sur le réseau virtuel ou le sous-réseau qui se connecte à votre compte de stockage.

   1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre ressource d’application logique.

   1. Dans le menu de la ressource d’application logique, sous **Paramètres**, sélectionnez **Mise en réseau**.

   1. Configurez les connexions nécessaires entre votre application logique et les adresses IP pour les points de terminaison privés.

   1. Pour accéder aux données de flux de travail de votre application logique sur le réseau virtuel, dans les paramètres de votre ressource d’application logique, définissez le paramètre `WEBSITE_CONTENTOVERVNET` sur `1`.

   Si vous utilisez votre propre serveur de noms de domaine (DNS) avec votre réseau virtuel, définissez le paramètre d’application `WEBSITE_DNS_SERVER` de votre ressource d’application logique sur l’adresse IP de votre DNS. Si vous avez un DNS secondaire, ajoutez un autre paramètre d’application nommé `WEBSITE_DNS_ALT_SERVER` et définissez la valeur sur l’adresse IP de votre DNS. Par ailleurs, mettez à jour vos enregistrements DNS pour faire pointer vos points de terminaison privés vers votre adresse IP interne. Les points de terminaison privés envoient la recherche DNS à l’adresse privée, et non à l’adresse publique de la ressource spécifique. Pour en savoir plus, consultez [Points de terminaison privés – Intégrer votre application à un réseau virtuel Azure](../app-service/web-sites-integrate-with-vnet.md#private-endpoints).

1. Après avoir appliqué ces paramètres d’application, vous pouvez supprimer l’accès public de votre compte de stockage.

## <a name="next-steps"></a>Étapes suivantes

- [Logic Apps partout : possibilités de mise en réseau avec Logic Apps (monolocataire)](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)
