---
title: Qu’est-ce que l’espace de travail ? -API de santé Azure
description: Cet article décrit une vue d’ensemble de l’espace de travail API Azure Healthcare.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 07/12/2021
ms.author: ginle
ms.openlocfilehash: 6eb5fa5186b7f28954724f27186b972f7efb08b0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121784919"
---
# <a name="what-is-healthcare-apis-preview-workspace"></a>Présentation de l’espace de travail API Healthcare (version préliminaire)

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

L’espace de travail API Azure Healthcare est un conteneur logique pour toutes vos instances de service de santé, telles que les services FHIR® (Fast Healthcare Interoperability Resources), les services d’imagerie et de communication numériques en médecine (DICOM®) et les connecteurs Internet des objets (IoT). L’espace de travail crée également une limite de conformité (HIPAA, HITRUST) dans laquelle les informations d’intégrité protégées peuvent voyager.

Vous pouvez approvisionner plusieurs services de données dans un espace de travail et, par conception, ils fonctionnent de façon transparente les uns avec les autres. Avec l’espace de travail, vous pouvez organiser toutes vos instances de santé API et gérer certains paramètres de configuration qui sont partagés entre tous les jeux de données et services sous-jacents, là où ils sont applicables.

## <a name="workspace-provisioning-process"></a>Processus d’approvisionnement de l’espace de travail
 
Un ou plusieurs espaces de travail peuvent être créés dans un groupe de ressources à partir de la Portail Azure ou à l’aide de scripts de déploiement. Un espace de travail API de santé, en tant qu’élément parent dans l’arborescence des services hiérarchiques, doit être préalablement créé avant qu’une ou plusieurs instances de service enfants puissent être créées.   
 
Un espace de travail ne peut pas être supprimé, sauf si toutes les instances de service enfants de l’espace de travail ont été supprimées. Cette fonctionnalité permet d’éviter toute suppression accidentelle d’instances de service. Toutefois, lorsqu’un groupe de ressources de l’espace de travail est supprimé, tous les espaces de travail et instances de service enfants au sein du groupe de ressources de l’espace de travail sont supprimés. 

## <a name="workspace-and-azure-region-selection"></a>Sélection de l’espace de travail et de la région Azure 
 
Lorsque vous créez un espace de travail, celui-ci doit être configuré pour une région Azure, qui peut être identique ou différente du groupe de ressources. La région ne peut pas être modifiée après la création de l’espace de travail. dans chaque espace de travail, tous les services api de santé (service FHIR, service DICOM et service IoT Connector) doivent être créés dans la région de l’espace de travail et ne peuvent pas être déplacés vers un autre espace de travail. 

## <a name="workspace-and-azure-healthcare-apis-service-instances"></a>Instances de service de l’espace de travail et des API de santé Azure 

Une fois l’espace de travail API Azure Healthcare créé, vous êtes maintenant prêt à créer une ou plusieurs instances de service à partir du Portail Azure. Vous pouvez créer plusieurs instances de service du même type ou de types différents dans un espace de travail. Dans l’espace de travail, vous pouvez appliquer des paramètres de configuration partagés à des instances de service enfants, qui sont décrites dans la section espace de travail et paramètres de configuration.

[![Groupe ](media/azure-resource-group.png) de ressources Azure ](media/azure-resource-group.png#lightbox)

En outre, les espaces de travail peuvent être créés à l’aide de Azure Resource Manager modèles de déploiement, un processus communément appelé infrastructure en tant que code (IaC). Cette option permet de personnaliser les modèles ARM et de terminer la création de l’espace de travail et la création de l’instance de service dans une étape combinée. 

Vous pouvez utiliser PowerShell, l’interface CLI, les scripts Terraform ou le kit de développement logiciel (SDK) .NET pour déployer le service API de santé. Pour créer une instance de service dans l’espace de travail, sélectionnez **créer** (service FHIR, service DICOM ou connecteurs IOT), puis entrez les détails du compte pour cette instance de service en cours de création.


## <a name="fhir-service"></a>Service FHIR

Le service FHIR comprend des API et des points de terminaison FHIR dans Azure pour l’accès aux données et le stockage au format de données FHIR. Un service FHIR gère les informations d’intégrité protégées (PHI) dans un environnement Cloud sécurisé et conforme. Le déploiement d’un service FHIR vous permet de rassembler des données de santé clinique à partir de plusieurs systèmes dans le Cloud Azure, en fonction de la norme de données interopérable [FHIR](https://www.hl7.org/fhir/index.html) publiée par HL7. Pour plus d’informations, consultez [à propos du service FHIR](./fhir/overview.md).

## <a name="dicom-service"></a>Service DICOM

Déployez un service DICOM pour importer des données d’imagerie médicale dans le Cloud à partir de n’importe quel système DICOMwebTM activé. Pour plus d’informations, consultez [vue d’ensemble du service DICOM](dicom/dicom-services-overview.md).

## <a name="iot-connector"></a>Connecteur IoT

le service IoT Connector vous permet d’ingérer des données d’appareil IoT à haute fréquence dans le service FHIR de manière évolutive, sécurisée et conforme. Pour plus d’informations, consultez [la page de documentation du connecteur IOT](./iot/index.yml).
 
## <a name="workspace-configuration-settings"></a>Paramètres de configuration de l’espace de travail

Certaines fonctionnalités sont configurées au niveau de l’espace de travail et s’appliquent à tous les services enfants au sein de cet espace de travail.

### <a name="application-monitoring"></a>Monitoring des applications

Azure Monitor vous aide à optimiser la disponibilité et les performances de vos applications et services. Il offre une solution complète pour collecter, analyser et exploiter des données de télémétrie de vos environnements cloud et locaux. Ces informations vous fournissent des informations sur la façon dont vos applications fonctionnent et vous permettent d’identifier et de résoudre de manière proactive les problèmes qui les affectent et les ressources dont elles dépendent. Pour plus d’informations sur la Azure Monitor, consultez [Azure Monitor vue d’ensemble](../azure-monitor/index.yml) de la documentation.

### <a name="role-based-access-control-rbac"></a>Contrôle d’accès en fonction du rôle

Le contrôle d’accès en fonction du rôle Azure (Azure RBAC) est un système qui offre une gestion précise de l’accès aux ressources Azure. Avec Azure RBAC, vous pouvez séparer les tâches au sein de votre équipe et accorder aux utilisateurs uniquement les accès nécessaires pour accomplir leur travail. En outre, il vous aide à gérer les utilisateurs qui ont accès aux ressources Azure, ce qu’ils peuvent faire avec ces ressources et les domaines auxquels ils ont accès. Pour plus d’informations, consultez la documentation [Azure RBAC](../role-based-access-control/index.yml) .


## <a name="next-steps"></a>Étapes suivantes

Pour commencer à utiliser les API de santé Azure, suivez le démarrage rapide de 5 minutes pour déployer un espace de travail.

>[!div class="nextstepaction"]
>[Déployer l’espace de travail dans le Portail Azure](healthcare-apis-quickstart.md)