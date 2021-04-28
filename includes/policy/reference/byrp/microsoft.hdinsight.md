---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c0a77896099bac5d4b3d819cfbd17ed339cb1b72
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869547"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les clusters Azure HDInsight doivent être injectés dans un réseau virtuel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0ab5b05-1c98-40f7-bb9e-dc568e41b501) |L’injection de clusters Azure HDInsight dans un réseau virtuel déverrouille les fonctionnalités avancées de sécurité et de mise en réseau de HDInsight, et vous permet de contrôler la configuration de la sécurité de votre réseau. |Audit, Désactivé, Refus |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_VNETInjection_Audit.json) |
|[Les clusters Azure HDInsight doivent utiliser les clés gérées par le client pour chiffrer les données au repos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F64d314f6-6062-4780-a861-c23e8951bee5) |Utilisez des clés gérées par le client pour gérer le chiffrement au repos de vos clusters Azure HDInsight. Par défaut, les données client sont chiffrées avec des clés gérées par le service, mais des clés gérées par le client sont généralement requises pour répondre aux normes de conformité réglementaire. Les clés gérées par le client permettent de chiffrer les données avec une clé Azure Key Vault que vous avez créée et dont vous êtes le propriétaire. Vous avez le contrôle total et la responsabilité du cycle de vie des clés, notamment leur permutation et leur gestion. Pour en savoir plus, rendez-vous sur [https://aka.ms/hdi.cmk](https://aka.ms/hdi.cmk). |Audit, Refuser, Désactivé |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_CMK_Audit.json) |
|[Les clusters Azure HDInsight doivent utiliser le chiffrement sur l’hôte pour chiffrer les données au repos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1fd32ebd-e4c3-4e13-a54a-d7422d4d95f6) |Le fait d’activer le chiffrement sur l’hôte vous aide à protéger et à préserver vos données de façon à répondre aux engagements de votre entreprise concernant la sécurité et la conformité. Lorsque vous activez le chiffrement sur l'hôte, les données stockées sur l'hôte de machine virtuelle sont chiffrées au repos et les flux sont chiffrés dans le service de stockage. |Audit, Refuser, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionAtHost_Audit.json) |
|[Les clusters Azure HDInsight doivent utiliser le chiffrement en transit pour chiffrer la communication entre les nœuds de cluster Azure HDInsight](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9da03a1-f3c3-412a-9709-947156872263) |Les données peuvent être falsifiées pendant leur transmission entre les nœuds de cluster Azure HDInsight. L’activation du chiffrement en transit résout les problèmes d’utilisation abusive et de falsification des données pendant leur transmission. |Audit, Refuser, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionInTransit_Audit.json) |
