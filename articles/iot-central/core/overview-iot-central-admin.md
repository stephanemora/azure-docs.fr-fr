---
title: Guide de l’administrateur Azure IoT Central
description: Azure IoT Central est une plateforme d’applications IoT qui simplifie la création de solutions IoT. Cet article propose une vue d’ensemble du rôle Administrateur dans IoT Central.
author: philmea
ms.author: philmea
ms.date: 03/25/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: c355902eae6aed1504b64dd12d056c6059771e57
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108759416"
---
# <a name="iot-central-administrator-guide"></a>Guide de l’administrateur IoT Central

Une application IoT Central vous permet de surveiller et de gérer des millions d’appareils tout au long de leur cycle de vie. Ce guide est destiné aux administrateurs qui gèrent des applications IoT Central.

Dans IoT Central, un administrateur :

- Gère les utilisateurs et les rôles dans l’application.
- Gère la sécurité, telle que l’authentification des appareils.
- Configure les paramètres d’application.
- Met à niveau les applications.
- Exporte et partage les applications.
- Supervise l’intégrité des applications.

## <a name="users-and-roles"></a>Utilisateurs et rôles

IoT Central utilise un système de contrôle d’accès en fonction du rôle pour gérer les autorisations des utilisateurs dans une application. IoT Central a trois rôles intégrés pour les administrateurs, les créateurs de solutions et les opérateurs. Un administrateur peut créer des rôles personnalisés avec des ensembles spécifiques d’autorisations. Un administrateur est chargé d’ajouter des utilisateurs à une application et de leur attribuer un rôle.

Pour en savoir plus, consultez [Gérer les utilisateurs et les rôles dans votre application IoT Central](howto-manage-users-roles.md).

## <a name="application-security"></a>Sécurité des applications

Les appareils qui se connectent à votre application IoT Central utilisent généralement des certificats X.509 ou des signatures d’accès partagé (SAP) en guise d’informations d’identification. L’administrateur gère les certificats ou les clés de groupe dont les informations d’identification des appareils sont issues.

Pour plus d’informations, consultez [Inscription de groupe par certificat X.509](concepts-get-connected.md#x509-group-enrollment), [Inscription de groupe par SAP](concepts-get-connected.md#sas-group-enrollment) et [Renouvellement des certificats d’appareil X.509](how-to-roll-x509-certificates.md).

L’administrateur peut également créer et gérer les jetons d’API qu’une application cliente utilise pour s’authentifier auprès de votre application IoT Central. Les applications clientes utilisent l’API REST pour interagir avec IoT Central.

## <a name="configure-an-application"></a>Configurer une application

L’administrateur peut configurer le comportement et l’apparence d’une application IoT Central. Pour plus d'informations, consultez les rubriques suivantes :

- [Modifier le nom et l’URL de l’application](howto-administer.md#change-application-name-and-url)
- [Personnaliser l’interface utilisateur](howto-customize-ui.md)
- [Déplacer une application vers un autre plan tarifaire](howto-view-bill.md)
- [Configurer les chargements de fichiers](howto-configure-file-uploads.md)

## <a name="export-an-application"></a>Exporter une application

Un administrateur peut :

- Créer une copie d’une application s’il a juste besoin d’une copie dupliquée de l’application. Par exemple, il peut avoir besoin d’une copie dupliquée pour effectuer des tests.
- Créer un modèle d’application à partir d’une application existante s’il envisage de créer plusieurs copies.

Pour en savoir plus, consultez [Exporter votre application Azure IoT](howto-use-app-templates.md).

## <a name="migrate-to-a-new-version"></a>Migrer vers une nouvelle version

Un administrateur peut migrer une application vers une version plus récente. Actuellement, une application nouvellement créée est une application v3. Un administrateur peut avoir besoin de migrer une application v2 vers une application v3.

Pour en savoir plus, consultez [Migrer votre application IoT Central v2 vers v3](howto-migrate.md).

## <a name="monitor-application-health"></a>Superviser l’intégrité de l’application

Un administrateur peut utiliser les métriques d’IoT Central pour évaluer l’intégrité des appareils connectés et l’intégrité des exportations de données en cours d’exécution.

Pour afficher les métriques, un administrateur peut utiliser des graphiques dans le portail Azure, une API REST ou des requêtes PowerShell ou Azure CLI.

Pour plus d’informations, consultez [Surveiller l’intégrité globale des appareils connectés à une application IoT Central](howto-monitor-application-health.md).

## <a name="tools"></a>Outils

La plupart des outils que vous utilisez en tant qu’administrateur sont disponibles dans la section **Administration** de chaque application IoT Central. Vous pouvez également utiliser les outils suivants pour effectuer certaines tâches administratives :

- [Azure CLI](howto-manage-iot-central-from-cli.md)
- [Azure PowerShell](howto-manage-iot-central-from-powershell.md)
- [Azure portal](howto-manage-iot-central-from-portal.md)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à administrer votre application Azure IoT Central, nous vous recommandons d’en apprendre davantage sur la [gestion des utilisateurs et des rôles](howto-manage-users-roles.md) dans Azure IoT Central.
