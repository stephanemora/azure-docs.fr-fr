---
title: Disponibilité des régions et résidence des données pour Azure Communication Services
description: Découvrir la résidence des données et les questions relatives à la confidentialité sur Azure Communication Services
author: chpalm
manager: anvalent
services: azure-communication-services
ms.author: chpalm
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 9ccc192bb5cf4c5e183e21add8e73d9e096d281c
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108136010"
---
# <a name="region-availability-and-data-residency"></a>Disponibilité des régions et résidence des données

Azure Communication Services s’engage à aider nos clients à respecter leurs conditions en matière de confidentialité et de données personnelles. En tant que développeur qui utilise Communication Services avec une relation directe avec des êtres humains à l’aide de l’application, vous contrôlez potentiellement leurs données. Étant donné qu’Azure Communication Services stocke et chiffre ces données au repos en votre nom, nous traitons probablement ces données. Cette page récapitule la manière dont le service conserve les données et dont vous pouvez les identifier, les exporter et les supprimer.

## <a name="data-residency"></a>Résidence des données

Quand vous créez une ressource Communication Services, vous spécifiez une **zone géographique** (et non un centre de données Azure). Toutes les données stockées par Communication Services au repos sont conservées dans cette zone géographique, dans un centre de données sélectionné en interne par Communication Services. Les données peuvent transiter ou être traitées dans d’autres zones géographiques. Ces points de terminaison globaux sont nécessaires pour fournir de hautes performances et une faible latence aux utilisateurs finaux, où qu’ils se trouvent.

## <a name="data-residency-and-events"></a>Résidence et sécurité des données

Toutes les rubriques du système Event Grid qui sont configurées avec Azure Communication Services sont créées à un emplacement global. Pour garantir la fiabilité de la distribution, les rubriques globales du système Event Grid peuvent stocker les données d’événement dans n’importe quel centre de données Microsoft. Quand vous configurez Event Grid avec Azure Communication Services, vous distribuez vos données d’événement vers Event Grid, qui est une ressource Azure dont vous avez le contrôle. Même si Azure Communication Services est configuré pour utiliser Azure Event Grid, vous êtes responsable de la gestion de votre ressource Event Grid et des données qu’elle contient.

## <a name="relating-humans-to-azure-communication-services-identities"></a>Association d’êtres humains à des identités Azure Communication Services

Votre application gère la relation entre des utilisateurs humains et des identités Communication Service. Quand vous voulez supprimer des données pour un utilisateur humain, vous devez supprimer les données qui impliquent toutes les identités Communication Services mises en corrélation pour cet utilisateur.

Il existe deux catégories de données Communication Services :
- **Données d’API.** Ces données sont créées et gérées par les API Communication Services ; les messages de conversation gérés par le biais des API de conversation en sont un bon exemple.
- **Journaux Azure Monitor.** Ces données sont créées par le service et gérées par le biais de la plateforme de données Azure Monitor. Ces données incluent des données de télémétrie et des métriques pour vous permettre de comprendre votre utilisation de Communication Services. Elles ne sont pas gérées par les API Communication Service.

## <a name="api-data"></a>Données d’API

### <a name="identities"></a>Identities

Azure Communication Services gère un répertoire d’identités. Utilisez l’API [DeleteIdentity](/rest/api/communication/communicationidentity/delete) pour les supprimer. La suppression d’une identité révoque tous les jetons d’accès associés et supprime leurs messages de conversation. Pour plus d’informations sur la suppression d’une identité, [consultez cette page](../quickstarts/access-tokens.md).

- DeleteIdentity

### <a name="azure-resource-manager"></a>Azure Resource Manager

L’utilisation du portail Azure ou d’API Azure Resource Manager avec Communication Services peut créer des données personnelles. [Utilisez cette page pour découvrir comment gérer des données personnelles dans des systèmes Azure Resource Manager.](../../azure-resource-manager/management/resource-manager-personal-data.md)

### <a name="telephone-number-management"></a>Gestion des numéros de téléphone

Azure Communication Services gère un répertoire de numéros de téléphone associés à une ressource Communication Services. Utilisez les [API d’administration de numéros de téléphone](/rest/api/communication/phonenumbers) pour récupérer des numéros de téléphone et les supprimer :

- `Get All Phone Numbers`
- `Release Phone Number`

### <a name="chat"></a>Conversation

Les threads et les messages de conversation sont conservés jusqu’à ce qu’ils soient explicitement supprimés. Un fil totalement inactif, sans aucun participant, est automatiquement supprimé au bout de 30 jours. Utilisez des [API de conversation](/rest/api/communication/chat/chatthread) pour obtenir, lister, mettre à jour et supprimer des messages.

- `Get Thread`
- `Get Message`
- `List Messages`
- `Update Message`
- `Delete Thread`
- `Delete Message`

### <a name="sms"></a>SMS

Les SMS envoyés et reçus sont temporairement traités par le service et ne sont pas conservés.

### <a name="pstn-voice-calling"></a>Appel vocal RTC

La communication audio et vidéo est traitée temporairement par le service et aucune donnée n’est conservée dans votre ressource, à l’exception des journaux Azure Monitor.

### <a name="internet-voice-and-video-calling"></a>Appel vocal et appel vidéo par Internet

La communication audio et vidéo est traitée temporairement par le service et aucune donnée n’est conservée dans votre ressource, à l’exception des journaux Azure Monitor.

## <a name="azure-monitor-and-log-analytics"></a>Azure Monitor et Log Analytics

Azure Communication Services alimente les données de journalisation Azure Monitor pour comprendre l’intégrité opérationnelle et l’utilisation du service. Certains de ces journaux incluent des numéros de téléphone et des identités Communication Services comme données de champ. Pour supprimer des données potentiellement personnelles, [suivez ces procédures pour Azure Monitor](../../azure-monitor/logs/personal-data-mgmt.md). Vous pouvez également configurer [la période de conservation par défaut pour Azure Monitor](../../azure-monitor/logs/manage-cost-storage.md).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Demandes des titulaires des données en lien avec des données Azure dans le cadre du RGPD et du CCPA](/microsoft-365/compliance/gdpr-dsr-azure)
- [Centre de gestion de la confidentialité Microsoft](https://www.microsoft.com/trust-center/privacy/data-location)
- [Carte interactive Azure - Où se trouvent mes données client ?](https://azuredatacentermap.azurewebsites.net/)
