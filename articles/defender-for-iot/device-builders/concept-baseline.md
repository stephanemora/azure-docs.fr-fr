---
title: Vérifications personnalisées et de base de référence
description: Découvrez le concept de base de référence d'Azure Defender pour IoT.
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 1b8b9d62918e40262da6b3df48d0fece842e050f
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113015066"
---
# <a name="azure-defender-for-iot-baseline-and-custom-checks"></a>Base de référence d'Azure Defender pour IoT et vérifications personnalisées

Cet article explique en quoi consiste la base de référence de Defender pour IoT et synthétise toutes les propriétés associées des vérifications personnalisées de base de référence.

## <a name="baseline"></a>Ligne de base

Une base de référence établit un comportement standard pour chaque appareil et permet de déterminer plus facilement un comportement inhabituel ou un écart par rapport au comportement attendu.

## <a name="baseline-custom-checks"></a>Vérifications personnalisées de base de référence

Pour les vérifications personnalisées de référence, une liste personnalisée de vérifications est établie pour la base de référence de chaque appareil à l’aide du **jumeau d’identité de module** de l’appareil.

## <a name="setting-baseline-properties"></a>Définition des propriétés de la base de référence

1. Dans votre IoT Hub, recherchez et sélectionnez l’appareil que vous souhaitez modifier.

1. Cliquez sur l’appareil, puis sur le module **azureiotsecurity**.

1. Cliquez sur **Jumeau d’identité de module**.

1. Chargez le fichier de **vérifications personnalisées de base de référence** sur l’appareil.

1. Ajoutez des propriétés de ligne de base au micro-agent Defender-IoT et cliquez sur **Enregistrer**.

### <a name="baseline-custom-check-file-example"></a>Exemple de fichier de vérifications personnalisées de base de référence

Pour configurer les vérifications personnalisées de base de référence :

   ```json
    "desired": {
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
        "baselineCustomChecksEnabled": {
          "value" : true
        },
        "baselineCustomChecksFilePath": {
          "value" : "/home/user/full_path.xml"
        },
        "baselineCustomChecksFileHash": {
          "value" : "#hashexample!"
        }
      }
    },
   ```

## <a name="baseline-custom-check-properties"></a>Propriétés des vérifications personnalisées de base de référence

| Nom| Statut | Valeurs valides| Valeurs par défaut| Description |
|------|-----|------|-----|-----|
|baselineCustomChecksEnabled|Obligatoire : true |Valeurs valides : **Booléen** |Valeur par défaut : **false** |Intervalle de temps maximal avant l’envoi de messages de priorité élevée.|
|baselineCustomChecksFilePath |Obligatoire : true|Valeurs valides : **Chaîne**, **Null** |Valeur par défaut : **Null** |Chemin complet de la configuration XML de la base de référence|
|baselineCustomChecksFileHash |Obligatoire : true|Valeurs valides : **Chaîne**, **Null** |Valeur par défaut : **Null** |`sha256sum` du fichier de configuration XML. Pour plus d’informations, consultez la [référence sha256sum](https://linux.die.net/man/1/sha256sum). |

Pour voir d’autres exemples de base de référence, consultez l’[exemple de base de référence personnalisée 1](https://ascforiot.blob.core.windows.net/public/custom_baseline_example_hyperv_ubuntu1804.xml) et l’[exemple de base de référence personnalisée 2](https://ascforiot.blob.core.windows.net/public/oms_audits.xml).

## <a name="next-steps"></a>Étapes suivantes

- Accéder à vos [données de sécurité brutes](how-to-security-data-access.md)
- [Investiguer un appareil](how-to-investigate-device.md)
- Comprendre et explorer les [recommandations de sécurité](concept-recommendations.md)
- Comprendre et explorer les [alertes de sécurité](concept-security-alerts.md)
