---
title: Alertes de sécurité personnalisées basées sur des agents
titleSuffix: Azure Defender for IoT
description: Découvrez les alertes de sécurité personnalisables et les actions de correction recommandées en utilisant les fonctionnalités d’appareil et le service Defender pour IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/16/2021
ms.author: shhazam
ms.openlocfilehash: e885566dd067d70fd4800ca96b8729494464da85
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100640983"
---
# <a name="defender-for-iot-devices-custom-security-alerts"></a>Alertes de sécurité personnalisées d’appareil Defender pour IoT

Defender pour IoT analyse continuellement votre solution IoT à l’aide d’analyses avancées et d’informations sur les menaces pour vous alerter en cas d’activités malveillantes.

Nous vous encourageons à créer des alertes personnalisées en fonction de votre connaissance du comportement attendu de l’appareil afin de veiller à ce que les alertes servent d'indicateurs efficaces en cas de compromission potentielle au sein du déploiement et du paysage uniques de votre organisation.

Vous pouvez définir les alertes Defender pour IoT suivantes selon le comportement attendu de votre appareil IoT. Pour plus d’informations sur la personnalisation de chaque alerte, consultez [Créer des alertes personnalisées](quickstart-create-custom-alerts.md).

## <a name="agent-based-security-custom-alerts"></a>Alertes de sécurité personnalisées basées sur des agents

| severity | Nom de l’alerte | Source de données | Description | Correction suggérée |
|--|--|--|--|--|
| Faible | Alerte personnalisée : le nombre de connexions actives est en dehors de la plage autorisée | Module de sécurité classique, Azure RTOS | Le nombre de connexions actives dans une fenêtre de temps spécifique est en dehors de la plage autorisée actuellement configurée. | Examinez les journaux de l’appareil. Découvrez l’origine de la connexion et déterminez si elle est inoffensive ou malveillante. Si cela elle est malveillante, supprimez les programmes malveillants et identifiez la source. Si elles est inoffensive, ajoutez la source à la liste des connexions autorisées. |
| Faible | Alerte personnalisée : la connexion sortante vers une adresse IP non autorisée a été créée | Module de sécurité classique, Azure RTOS | Une connexion sortante a été créée à une adresse IP qui ne figure pas dans votre liste d’adresses IP autorisées. | Examinez les journaux de l’appareil. Découvrez l’origine de la connexion et déterminez si elle est inoffensive ou malveillante. Si cela elle est malveillante, supprimez les programmes malveillants et identifiez la source. Si elles est inoffensive, ajoutez la source à la liste des adresses IP autorisées. |
| Faible | Alerte personnalisée : le nombre de connexions locales ayant échoué est en dehors de la plage autorisée | Module de sécurité classique, Azure RTOS | Le nombre de nombre de connexions locales ayant échoué dans une fenêtre de temps spécifique est en dehors de la plage autorisée actuellement configurée. |  |
| Faible | Alerte personnalisée : connexion d’un utilisateur ne figurant pas sur la liste des utilisateurs autorisés | Module de sécurité classique, Azure RTOS | Un utilisateur local ne figurant pas sur la liste des utilisateurs autorisés s’est connecté à l’appareil. | Si vous enregistrez des données brutes, accédez à votre compte Log Analytics et utilisez les données pour examiner l’appareil, identifier la source, puis corriger la liste verte ou rouge pour ces paramètres. Si vous n’enregistrez pas actuellement de données brutes, accédez à l’appareil, puis corrigez la liste verte ou rouge pour ces paramètres. |
| Faible | Alerte personnalisée : un processus non autorisé a été exécuté | Module de sécurité classique, Azure RTOS | Un processus non autorisé a été exécuté sur l’appareil. | Si vous enregistrez des données brutes, accédez à votre compte Log Analytics et utilisez les données pour examiner l’appareil, identifier la source, puis corriger la liste verte ou rouge pour ces paramètres. Si vous n’enregistrez pas actuellement de données brutes, accédez à l’appareil, puis corrigez la liste verte ou rouge pour ces paramètres. |
|

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [personnaliser une alerte](quickstart-create-custom-alerts.md)
- [Vue d’ensemble](overview.md) du service Defender pour IoT
- Découvrez comment [accéder à vos données de sécurité](how-to-security-data-access.md)
- En savoir plus sur [l’examen d’un appareil](how-to-investigate-device.md)
