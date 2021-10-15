---
title: Validation des services de données avec Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 07/30/2021
ms.topic: conceptual
author: MikeRayMSFT
ms.author: mikeray
description: Décrit le programme de validation pour les distributions Kubernetes pour les services de données avec Azure Arc.
keywords: Kubernetes, Arc, Azure, K8s, validation, data services, SQL Managed Instance
ms.openlocfilehash: 90158be8a28aae08cbc377aa776947096597a601
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273834"
---
# <a name="azure-arc-enabled-data-services-kubernetes-validation"></a>Validation Kubernetes des services de données avec Azure Arc

L’équipe des services de données avec Azure Arc a travaillé avec des partenaires du secteur afin de valider des distributions et des solutions spécifiques dans le cadre de l’hébergement de services de données avec Azure Arc. Cette validation étend la [validation Kubernetes avec Azure Arc](../kubernetes/validation-program.md) pour les services de données. Cet article identifie les solutions des partenaires, les versions, les versions de Kubernetes, les versions de SQL Server et les versions de PostgreSQL Hyperscale qui ont été vérifiées pour prendre en charge les services de données. 

Pour plus d’informations sur la validation de tous les composants avec Azure Arc, consultez [Présentation du programme de validation](../validation-program/overview.md).

> [!NOTE]
> À l’heure actuelle, SQL Managed Instance avec Azure Arc est en disponibilité générale dans certaines régions.
>
> PostgreSQL Hyperscale avec Azure Arc est disponible en préversion dans certaines régions.

## <a name="partners"></a>Partenaires

### <a name="dell"></a>Dell

|Solution et version | Version de Kubernetes | Version des services de données avec Azure Arc | Version SQL Server | Version de PostgreSQL Hyperscale
|-----|-----|-----|-----|-----|
| Dell EMC PowerFlex |1.19.7|v1.0.0_2021-07-30|SQL Server 2019 (15.0.4123) | |
| PowerFlex version 3.6 |1.19.7|v1.0.0_2021-07-30|SQL Server 2019 (15.0.4123) | |
| PowerFlex CSI version 1.4 |1.19.7|v1.0.0_2021-07-30|SQL Server 2019 (15.0.4123) | |
| PowerStore X|1.20.6|v1.0.0_2021-07-30|SQL Server 2019 (15.0.4123) |postgres 12.3 (Ubuntu 12.3-1) |
| PowerStore T|1.20.6|v1.0.0_2021-07-30|SQL Server 2019 (15.0.4123) |postgres 12.3 (Ubuntu 12.3-1)|

### <a name="nutanix"></a>Nutanix

|Solution et version | Version de Kubernetes | Version des services de données avec Azure Arc | Version SQL Server | Version de PostgreSQL Hyperscale
|-----|-----|-----|-----|-----|
| Karbon 2.2<br/>AOS : 5.19.1.5<br/>AHV : 20201105.1021<br/>PC : Version pc.2021.3.02<br/> | 1.19.8-0 | v1.0.0_2021-07-30 | SQL Server 2019 (15.0.4123)|postgres 12.3 (Ubuntu 12.3-1)|

### <a name="purestorage"></a>PureStorage

|Solution et version | Version de Kubernetes | Version des services de données avec Azure Arc | Version SQL Server | Version de PostgreSQL Hyperscale
|-----|-----|-----|-----|-----|
| Portworx Enterprise 2.7 | 1.20.7 | v1.0.0_2021-07-30 | SQL Server 2019 (15.0.4138)||

### <a name="red-hat"></a>Red Hat

|Solution et version | Version de Kubernetes | Version des services de données avec Azure Arc | Version SQL Server | Version de PostgreSQL Hyperscale
|-----|-----|-----|-----|-----|
| OpenShift 7.13 | 1.20.0 | v1.0.0_2021-07-30 | SQL Server 2019 (15.0.4138)|postgres 12.3 (Ubuntu 12.3-1)|

### <a name="vmware"></a>VMware

|Solution et version | Version de Kubernetes | Version des services de données avec Azure Arc | Version SQL Server | Version de PostgreSQL Hyperscale
|-----|-----|-----|-----|-----|
| TKGm v1.3.1 | 1.20.5 | v1.0.0_2021-07-30 | SQL Server 2019 (15.0.4123)|postgres 12.3 (Ubuntu 12.3-1)|

## <a name="data-services-validation-process"></a>Processus de validation des services de données

Le plug-in de services de données avec Azure Arc de Sonobuoy automatise l’approvisionnement et le test des services de données avec Azure Arc sur un cluster Kubernetes.

### <a name="prerequisites"></a>Configuration requise

Installez les outils : 

- [Azure Data CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata)
- [kubectl](https://kubernetes.io/docs/home/)
- [Azure Data Studio – build Insider](https://github.com/microsoft/azuredatastudio)

Créez un fichier config Kubernetes configuré pour accéder au cluster Kubernetes cible et définissez-le comme contexte actuel. La façon dont ce fichier est généré et importé localement sur votre ordinateur est différente d’une plateforme à l’autre. Voir [Kubernetes.io](https://kubernetes.io/docs/home/).

### <a name="process"></a>Processus

Les tests de conformité sont exécutés dans le cadre de la validation des services de données avec Azure Arc. L’une des conditions préalables à l’exécution de ces tests est de transmettre les tests Kubernetes avec Azure Arc pour la distribution Kubernetes utilisée.

Ces tests vérifient que le produit est conforme à la configuration requise pour l’exécution et l’exploitation des services de données. Cela permettra d’évaluer si le produit est prêt pour les déploiements d’entreprise.

Les tests pour les services de données couvrent les éléments suivants en mode de connexion indirecte :

1. Déployer le contrôleur de données en mode indirect
2. Déployer [SQL Managed Instance avec Azure Arc](create-sql-managed-instance.md)
3. Déployer [PostgreSQL Hyperscale avec Azure Arc](create-postgresql-hyperscale-server-group.md)
4. Effectuer un scale-out de [PostgreSQL Hyperscale](scale-out-in-postgresql-hyperscale-server-group.md)

D’autres tests seront ajoutés dans les futures versions des services de données avec Azure Arc.

## <a name="additional-information"></a>Informations supplémentaires

- [Présentation du programme de validation](../validation-program/overview.md)
- [Validation de Kubernetes avec Azure Arc](../kubernetes/validation-program.md)
- [Programme de validation d’Azure Arc – projet GitHub](https://github.com/Azure/azure-arc-validation/)

## <a name="next-steps"></a>Étapes suivantes

[Créer un contrôleur de données](create-data-controller.md)
