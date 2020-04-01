---
title: Validation des alertes (fichier de test EICAR) dans Azure Security Center | Microsoft Docs
description: Ce document est conçu pour vous aider à valider des alertes de sécurité dans Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 5146878adf10e452f38fecb115ec40792ffa84f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139995"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Validation des alertes (fichier de test EICAR) dans Azure Security Center
Ce document est conçu pour vous apprendre à vérifier si votre système est correctement configuré pour les alertes dans Azure Security Center.

## <a name="what-are-security-alerts"></a>Que sont les alertes de sécurité ?
Les alertes sont les notifications que Security Center génère lorsqu'il détecte des menaces sur vos ressources. Il hiérarchise et répertorie les alertes ainsi que les informations vous permettant d’analyser rapidement le problème. Security Center fournit également des suggestions sur la manière dont vous pouvez corriger les problèmes liés à une attaque.
Pour plus d’informations, consultez [Alertes de sécurité dans Security Center](security-center-alerts-overview.md) et [Gestion et résolution des alertes de sécurité](security-center-managing-and-responding-alerts.md)

## <a name="alert-validation"></a>Validation de l’alerte

* [Windows](#validate-windows)
* [Linux](#validate-linux)
* [Kubernetes](#validate-kubernetes)

## <a name="validate-alerts-on-windows-vms"></a>Valider des alertes sur des machines virtuelles Windows <a name="validate-windows"></a>

Après avoir installé l’agent de Security Center sur votre ordinateur, suivez les étapes ci-dessous depuis l’ordinateur sur lequel vous voulez configurer l’alerte de la ressource attaquée :

1. Copiez un exécutable (par exemple, **calc.exe**) sur le bureau de votre ordinateur ou dans le répertoire de votre choix et renommez-le **ASC_AlertTest_662jfi039N.exe**.
1. Ouvrez l’invite de commandes et exécutez ce fichier avec un argument (un faux nom d’argument), tel que : ```ASC_AlertTest_662jfi039N.exe -foo```
1. Patientez 5 à 10 minutes puis ouvrez les alertes dans Security Center. Une alerte similaire à l'[exemple](#alert-validate) ci-dessous doit s'afficher :

> [!NOTE]
> En examinant cette alerte de test, veillez à ce que la valeur du champ **Arguments Auditing Enabled** (Audit pour arguments activé) soit **true**. S’il est **false**, vous devez activer la ligne de commande d’audit pour arguments. Pour ce faire, utilisez la commande suivante :
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="validate-alerts-on-linux-vms"></a>Valider des alertes sur des machines virtuelles Linux <a name="validate-linux"></a>

Après avoir installé l’agent de Security Center sur votre ordinateur, suivez les étapes ci-dessous depuis l’ordinateur sur lequel vous voulez configurer l’alerte de la ressource attaquée :
1. Copiez un fichier exécutable dans un emplacement approprié et renommez-le **. / asc_alerttest_662jfi039n**, par exemple :

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Ouvrez l’invite de commandes et exécutez ce fichier :

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Patientez 5 à 10 minutes puis ouvrez les alertes dans Security Center. Une alerte similaire à l'[exemple](#alert-validate) ci-dessous doit s'afficher :

### <a name="alert-example"></a>Exemple d’alerte <a name="alert-validate"></a>

![Exemple de validation d'alerte](./media/security-center-alert-validation/security-center-alert-validation-fig2.png) 


## <a name="validate-alerts-on-kubernetes"></a>Valider des alertes sur Kubernetes<a name="validate-kubernetes"></a>

Si vous utilisez la fonctionnalité de préversion Security Center de l’intégration d’Azure Kubernetes Service, exécutez la commande kubectl suivante pour tester le fonctionnement de vos alertes :

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

Pour plus d’informations sur l’intégration d’Azure Kubernetes Service et Azure Security Center, consultez [cet article](azure-kubernetes-service-integration.md).

## <a name="next-steps"></a>Étapes suivantes
Cet article vous a présenté le processus de validation des alertes. Maintenant que vous êtes familiarisé avec la validation, vous pouvez consulter les articles suivants :

* [Validation de la détection des menaces d’Azure Key Vault dans Azure Security Center](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) : découvrez comment gérer des alertes et résoudre les incidents de sécurité dans Security Center.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Présentation des alertes de sécurité dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) : en savoir plus sur les différents types d’alertes de sécurité.
