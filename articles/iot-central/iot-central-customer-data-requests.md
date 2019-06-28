---
title: Fonctionnalités de demande de données client dans Azure IoT Central | Microsoft Docs
description: Fonctionnalités de demande de données client dans Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 04/24/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 4c63ac8d6b16b2a0476600f67c66b2ccc8dc2aab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64685557"
---
# <a name="summary-of-customer-data-request-features"></a>Résumé des fonctionnalités de demande de données client

Azure IoT Central est une solution SaaS (software-as-a-service) Internet des objets(IoT) entièrement managée, qui facilite la connexion, la surveillance et la gestion à grande échelle de vos ressources IoT, la création d’informations détaillées à partir de vos données IoT, et la prise de décisions éclairées.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identification des données client

Les ID d’objet Azure Active Directory servent à identifier les utilisateurs et à affecter les rôles. Le portail Azure IoT Central affiche les adresses e-mail des utilisateurs pour l’affectation des rôles, mais seul l’ID d’objet Azure Active Directory est stocké ; l’adresse e-mail est interrogée de façon dynamique à partir d’Azure Active Directory. Les administrateurs d’Azure IoT Central peuvent afficher, exporter et supprimer des utilisateurs d’application dans la section d’administration des utilisateurs d’une application Azure IoT Central.

Dans l’application, les adresses e-mail peuvent être configurées pour recevoir des alertes. Dans ce cas, ces adresses sont stockées dans IoT Central et doivent être gérées à partir de la page d’administration des comptes de l’application.

Au niveau des appareils, Microsoft ne conserve aucune information et n’a pas accès aux données permettant la corrélation entre l’appareil et l’utilisateur. Nombre des appareils gérés dans Azure IoT Central ne sont pas des appareils personnels, par exemple un distributeur automatique ou une machine à café. Cependant, les clients peuvent décider que certains appareils sont personnels et gérer eux-mêmes leurs propres systèmes de gestion des ressources ou de l’inventaire afin d’associer des appareils à des personnes. Azure IoT Central gère et stocke toutes les données associées aux appareils comme s’il s’agissait de données personnelles.

Lorsque vous utilisez les services d’entreprise Microsoft, Microsoft génère des informations appelées journaux d’activité générés par le système. Ces journaux d’activité, qui consignent les actions factuelles effectuées au sein du service et les données de diagnostic relatives aux appareils individuels, ne sont pas liés à l’activité de l’utilisateur. Les journaux d’activité générés par le système Azure IoT Central ne sont pas accessibles ou exportables par les administrateurs de l’application.

## <a name="deleting-customer-data"></a>Suppression des données client

La suppression de données utilisateur est uniquement possible via la page d’administration d’IoT Central. Les administrateurs d’applications peuvent sélectionner l’utilisateur à supprimer et cliquer sur **Supprimer** dans le coin supérieur droit de l’application afin de supprimer l’enregistrement. Les administrateurs d’une application peuvent également supprimer des comptes individuels qui ne sont plus associés à l’application en question.

Une fois supprimé, l’utilisateur ne reçoit plus aucune alerte par e-mail. Toutefois, son adresse e-mail doit être supprimée individuellement de chaque alerte configurée.

Pour plus d’informations, consultez [Configurer des règles et des actions pour votre appareil](tutorial-configure-rules.md).

## <a name="exporting-customer-data"></a>Exportation des données client

L’exportation de données utilisateur est uniquement possible via la page d’administration d’IoT Central. Les données client, y compris les rôles affectés, peuvent être sélectionnées, copiées puis collées par un administrateur de l’application.

## <a name="links-to-additional-documentation"></a>Liens vers la documentation complémentaire

Pour plus d’informations sur l’administration des comptes, notamment les définitions de rôles, consultez [Guide pratique pour administrer votre application](howto-administer.md).
