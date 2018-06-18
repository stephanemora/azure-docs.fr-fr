---
title: Fonctionnalités de demande de données client
author: dominicbetts
ms.author: dobett
manager: timlt
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: d6355926c8fac62b01c36d28265842b1233ce213
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629318"
---
# <a name="summary-of-customer-data-request-features"></a>Résumé des fonctionnalités de demande de données client

Le Service Azure IoT Hub Device Provisioning est un service cloud basé sur l’API REST conçu pour les clients d’entreprise qui permet l’approvisionnement automatique, fluide et sans contact d’appareils à IoT Hub en assurant la protection de l’appareil jusqu’au cloud.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Chaque appareil reçoit un identifiant d’inscription et un identifiant d’appareil attribué par un administrateur client. Les données transmises par ces appareils et les concernant sont basées sur ces identifiants. Microsoft ne conserve aucune information et n’a pas accès aux données permettant la corrélation entre ces appareils et un individu.

La plupart des appareils gérés dans Service Device Provisioning ne sont pas des appareils personnels. Il peut s’agir d’un thermostat de bureau ou d’un robot d’usine, par exemple. Cependant, les clients peuvent décider que certains appareils sont personnels et gérer eux-mêmes leurs propres méthodes de gestion des ressources ou de l’inventaire afin d’associer des appareils à des personnes. Le service Device Provisioning gère et stocke toutes les données associées aux appareils comme s’il s’agissait de données personnelles.

Les administrateurs clients peuvent utiliser le portail Azure ou les API REST du service pour répondre aux demandes d’informations en exportant ou en supprimant les données associées à un identifiant d’appareil ou d’inscription.

> [!NOTE]
> Des données supplémentaires sont stockées dans le service IoT Hub pour les appareils approvisionnés via le service Device Provisioning. Pour savoir comment répondre à une demande complète pour un appareil donné, consultez la [documentation de référence relative à IoT Hub](../iot-hub/iot-hub-customer-data-requests.md).

## <a name="deleting-customer-data"></a>Suppression des données client

Le service Device Provisioning stocke les inscriptions et les enregistrements d’inscription. Les inscriptions contiennent des informations sur les appareils qui peuvent être approvisionnés et les enregistrements d’inscription indiquent quels appareils sont déjà passés par le processus d’approvisionnement.

Les administrateurs clients peuvent supprimer des inscriptions à partir du portail Azure, ce qui supprime également tous les enregistrements d’inscription associés.

Pour plus d’informations, consultez le [guide pratique de gestion des inscriptions d’appareils](how-to-manage-enrollments.md).

Il est également possible de supprimer des inscriptions et des enregistrements d’inscription en utilisant des API REST :

* Pour supprimer les informations d’inscription d’un seul appareil, utilisez [Inscription de l’appareil - Supprimer](https://docs.microsoft.com/rest/api/iot-dps/deviceenrollment/delete).
* Pour supprimer les informations d’inscription d’un groupe d’appareils, utilisez [Inscription du groupe d’appareils - Supprimer](https://docs.microsoft.com/rest/api/iot-dps/deviceenrollmentgroup/delete).
* Pour supprimer les informations sur les appareils approvisionnés, utilisez [État de l’inscription - Supprimer l’état de l’inscription](https://docs.microsoft.com/rest/api/iot-dps/registrationstate/deleteregistrationstate).

## <a name="exporting-customer-data"></a>Exportation des données client

Le service Device Provisioning stocke les inscriptions et les enregistrements d’inscription. Les inscriptions contiennent des informations sur les appareils qui peuvent être approvisionnés et les enregistrements d’inscription indiquent quels appareils sont déjà passés par le processus d’approvisionnement.

Les administrateurs clients peuvent afficher les inscriptions et les enregistrements d’inscription via le portail Azure et les exporter en utilisant la fonction copier-coller.

Pour plus d’informations sur la gestion des inscriptions, consultez le [guide pratique de gestion des inscriptions d’appareils](how-to-manage-enrollments.md).

Il est également possible d’exporter des inscriptions et des enregistrements d’inscription en utilisant des API REST :

* Pour exporter les informations d’inscription d’un seul appareil, utilisez [Inscription de l’appareil - Obtenir](https://docs.microsoft.com/rest/api/iot-dps/deviceenrollment/get).
* Pour exporter les informations d’inscription d’un groupe d’appareils, utilisez [Inscription du groupe d’appareils - Obtenir](https://docs.microsoft.com/rest/api/iot-dps/deviceenrollmentgroup/get).
* Pour exporter les informations sur les appareils approvisionnés, utilisez [État de l’inscription - Obtenir l’état de l’inscription](https://docs.microsoft.com/rest/api/iot-dps/registrationstate/getregistrationstate).

> [!NOTE]
> Lorsque vous utilisez les services d’entreprise Microsoft, Microsoft génère des informations appelées journaux générés par le système. Certains journaux générés par le système du service Device Provisioning ne sont pas accessibles ou exportables par les administrateurs clients. Ces journaux, qui consignent les actions factuelles effectuées au sein du service et les données de diagnostic relatives aux appareils individuels.

## <a name="links-to-additional-documentation"></a>Liens vers la documentation complémentaire

La documentation complète sur les API du service Device Provisioning est accessible à l’adresse [https://docs.microsoft.com/rest/api/iot-dps](https://docs.microsoft.com/rest/api/iot-dps).

[Résumé des fonctionnalités de demande de données client](../iot-hub/iot-hub-customer-data-requests.md) d’IoT Hub.