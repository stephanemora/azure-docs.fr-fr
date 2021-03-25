---
title: Liste des alertes intégrées et personnalisées
description: Découvrez les alertes de sécurité et les actions de correction recommandées en utilisant les fonctionnalités et le service Defender pour IoT Hub.
ms.topic: conceptual
ms.date: 2/16/2021
ms.openlocfilehash: 73b3a1ca3e52f571ab7b531235650b6bda870691
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784558"
---
# <a name="defender-for-iot-hub-security-alerts"></a>Alertes de sécurité Defender pour IoT Hub

Defender pour IoT analyse continuellement votre solution IoT à l’aide d’analyses avancées et d’informations sur les menaces pour vous alerter en cas d’activités malveillantes.
En outre, vous pouvez créer des alertes personnalisées en fonction de votre connaissance du comportement attendu de l’appareil.
Une alerte sert d’indicateur de compromission potentielles et doit être examinée et corrigée.

Dans cet article, vous trouverez une liste d’alertes intégrées qui peuvent être déclenchées sur votre hub IoT.
Outre les alertes intégrées, Defender pour IoT vous permet de définir des alertes personnalisées basées sur le comportement attendu d’un IoT Hub et/ou d’un appareil.
Pour plus d’informations, consultez [Alertes personnalisables](concept-customizable-security-alerts.md).

## <a name="built-in-alerts-for-iot-hub"></a>Alertes intégrées pour IoT Hub

| severity | Nom | Description | Correction suggérée |
|--|--|--|--|
| Niveau de gravité **Moyen** |  |  |  |
| Ajout d’un nouveau certificat à un IoT Hub | Moyenne | Un certificat nommé \'%{DescCertificateName}\' a été ajouté à IoT Hub \'%{DescIoTHubName}\'. Si cette action a été effectuée par un tiers autorisé, cela peut être le signe d’une activité malveillante. | 1. Assurez-vous que le certificat a été ajouté par un tiers autorisé. <br> 2. S’il n’a pas été ajouté par un tiers autorisé, supprimez-le et signalez l’alerte à l’équipe responsable de la sécurité de l’organisation. |
| Suppression d’un certificat d’un IoT Hub | Moyenne | Un certificat nommé \'%{DescCertificateName}\' a été supprimé d’IoT Hub \'%{DescIoTHubName}\'. Si cette action a été effectuée par un tiers autorisé, cela peut être le signe d’une activité malveillante. | 1. Assurez-vous que le certificat a été supprimé par un tiers autorisé. <br> 2. S’il n’a pas été supprimé par un tiers autorisé, ajouter le à nouveau et signalez l’alerte à l’équipe responsable de la sécurité de l’organisation. |
| Détection d’une tentative infructueuse d’ajout d’un certificat à un IoT Hub | Moyenne | Échec de la tentative d’ajout du certificat \'%{DescCertificateName}\' à IoT Hub \'%{DescIoTHubName}\'. Si cette action a été effectuée par un tiers autorisé, cela peut être le signe d’une activité malveillante. | Assurez-vous que les autorisations de modification des certificats sont accordées uniquement aux tiers autorisés. |
| Détection d’une tentative infructueuse de suppression d’un certificat d’un IoT Hub | Moyenne | Échec de la tentative de suppression du certificat \'%{DescCertificateName}\' à IoT Hub \'%{DescIoTHubName}\'. Si cette action a été effectuée par un tiers autorisé, cela peut être le signe d’une activité malveillante. | Assurez-vous que les autorisations de modification des certificats sont accordées uniquement aux tiers autorisés. |
| Non-concordance de l’empreinte du certificat de l’appareil x.509 | Moyenne | L’empreinte du certificat de l’appareil x.509 ne correspondait pas à la configuration. | Examinez les alertes sur les appareils. Aucune action supplémentaire n’est requise. |
| Le certificat x.509 a expiré | Moyenne | Le certificat de l’appareil X.509 a expiré. | Il peut s’agir d’un appareil légitime dont le certificat est arrivé à expiration ou d’une tentative d’emprunt d’identité d’un appareil légitime. Si l’appareil légitime communique correctement, il s’agit probablement d’une tentative d’emprunt d’identité. |
| Niveau de gravité **Bas** |  |  |  |
| Détection d’une tentative d’ajout ou de modification d’un paramètre de diagnostic d’un IoT Hub | Faible | Une tentative d’ajout ou de modification d’un paramètre de diagnostic d’un IoT Hub a été détectée. Les paramètres de diagnostic permettent de recréer les pistes d’activité à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau. Si cette action n'a pas été effectuée par une partie autorisée, cela peut indiquer une activité malveillante. | 1. Assurez-vous que le certificat a été supprimé par un tiers autorisé.<br> 2. S’il n’a pas été supprimé par un tiers autorisé, ajoutez-le à nouveau et signalez l’alerte à l’équipe responsable de la sécurité de l’organisation. |
| Détection d’une tentative de suppression d’un paramètre de diagnostic d’un IoT Hub | Faible | Détection de %{DescAttemptStatusMessage}\' tentatives d’ajout ou de modification des paramètres de diagnostic \'%{DescDiagnosticSettingName}\' pour IoT Hub \'%{DescIoTHubName}\'. Le paramètre de diagnostic permet de recréer les pistes d’activité à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau. Si cette action n’a pas été effectuée par un tiers autorisé, cela peut être le signe d’une activité malveillante. | Assurez-vous que les autorisations de modification des paramètres de diagnostic sont accordées uniquement aux tiers autorisés. |
| Jeton SAS expiré | Faible | Un jeton SAS ayant expiré est utilisé par un appareil | Il peut s’agir d’un appareil légitime dont le jeton est arrivé à expiration ou d’une tentative d’emprunt d’identité d’un appareil légitime. Si l’appareil légitime communique correctement, il s’agit probablement d’une tentative d’emprunt d’identité. |
| Signature de jeton SAS non valide | Faible | Le jeton SAS utilisé par un appareil comporte une signature non valide. Le jeton SAS ne correspond ni à la clé primaire ni à la clé secondaire. | Examinez les alertes sur les appareils. Aucune action supplémentaire n’est requise. |

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble](overview.md) du service Defender pour IoT
- Découvrez comment [accéder à vos données de sécurité](how-to-security-data-access.md)
- En savoir plus sur [l’examen d’un appareil](how-to-investigate-device.md)
