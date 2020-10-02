---
title: Service Azure IoT Hub Device Provisioning - Attestation de certificat X.509
description: Décrit les concepts spécifiques à l’utilisation de l’attestation de certificat X.509 avec le service de provisionnement des appareils (DPS) et IoT Hub
author: wesmc7777
ms.author: wesmc
ms.date: 09/14/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 911f819343f675ebe0a2604d912e6e26aa646eb5
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90532954"
---
# <a name="x509-certificate-attestation"></a>Attestation de certificat X.509

Cet article donne une vue d’ensemble des concepts impliqués lors du provisionnement d’appareils à l’aide de l’attestation de certificat X.509. Cet article concerne toutes les personnes impliquées dans la préparation d’un appareil pour le déploiement.

Les certificats X.509 peuvent être stockés dans un module de sécurité matériel HSM.

> [!TIP]
> Nous vous recommandons vivement d’utiliser un module HSM avec les appareils pour stocker en toute sécurité des secrets, comme le certificat X.509, sur vos appareils en production.


## <a name="x509-certificates"></a>Certificats X.509

Utiliser des certificats X.509 comme mécanisme d’attestation est un excellent moyen de mettre à l’échelle la production et de simplifier le provisionnement des appareils. Les certificats X.509 sont généralement organisés en une chaîne d’approbation de confiance dans laquelle chaque certificat est signé par la clé privée du certificat plus élevé suivant, et ainsi de suite, pour aboutir à un certificat racine auto-signé. Cette organisation établit une chaîne déléguée de confiance allant du certificat racine généré par une autorité de certification (CA) racine approuvée jusqu’au certificat « feuille » d’entité finale installé sur un appareil, en passant par chaque autorité de certification intermédiaire. Pour plus d’informations, consultez [Authentification des appareils à l’aide de certificats d’autorité de certification X.509](/azure/iot-hub/iot-hub-x509ca-overview). 

La chaîne d’approbation représente souvent une hiérarchie logique ou physique associée aux appareils. Par exemple, un fabricant peut :
- émettre un certificat d’autorité de certification racine autosigné,
- utiliser le certificat racine pour générer un certificat d’autorité de certification intermédiaire unique pour chaque usine,
- utiliser le certificat de chaque usine pour générer un certificat d’autorité de certification intermédiaire unique pour chaque ligne de production de l’usine,
- et enfin utiliser le certificat de ligne de production pour générer un certificat (d’entité finale) d’appareil unique pour chacun des appareils fabriqués sur la ligne. 

Pour plus d’informations, consultez [Informations conceptuelles sur les certificats de l’autorité de certification X.509 dans l’industrie IoT](/azure/iot-hub/iot-hub-x509ca-concept). 

### <a name="root-certificate"></a>Certificat racine

Un certificat racine est un certificat X.509 auto-signé représentant une autorité de certification (CA). Il constitue la dernière étape, ou ancre d’approbation, de la chaîne d’approbation. Les certificats racine peuvent être émis automatiquement par une organisation ou achetés à partir d’une autorité de certification racine. Pour plus d’informations, consultez [Obtenir des certificats d’autorité de certification X.509](/azure/iot-hub/iot-hub-security-x509-get-started#get-x509-ca-certificates). Le certificat racine peut également être appelé « certificat d’autorité de certification racine ».

### <a name="intermediate-certificate"></a>Certificat intermédiaire

Un certificat intermédiaire est un certificat X.509 qui a été signé par le certificat racine (ou par un autre certificat intermédiaire contenant le certificat racine dans sa chaîne). Le dernier certificat intermédiaire dans une chaîne est utilisé pour signer le certificat feuille. Un certificat intermédiaire peut également être appelé « certificat d’autorité de certification intermédiaire ».

### <a name="end-entity-leaf-certificate"></a>Certificat « feuille» d’entité finale

Le certificat feuille, ou certificat d’entité finale, identifie le détenteur du certificat. Il possède le certificat racine dans sa chaîne d’approbation, ainsi que zéro ou plusieurs certificats intermédiaires. Le certificat feuille n’est pas utilisé pour signer d’autres certificats. Il identifie de façon unique l’appareil auprès du service de provisionnement et est parfois appelé « certificat d’appareil ». Pendant l’authentification, l’appareil utilise la clé privée associée à ce certificat pour répondre à une preuve de vérification de possession émise par le service.

Les certificats feuilles utilisés avec une entrée [Inscription individuelle](./concepts-service.md#individual-enrollment) nécessitent que le **Nom du sujet** soit défini sur l’ID d’inscription de l’entrée Inscription individuelle. Les certificats feuilles utilisés avec une entrée [Groupe d’inscription](./concepts-service.md#enrollment-group) doivent avoir le **Nom du sujet** défini sur l’ID d’appareil souhaité qui s’affichera dans les **Enregistrements d’inscription** pour l’appareil authentifié dans le groupe d’inscription.

Pour plus d’informations, consultez [Authentification d’appareils signés avec des certificats d’autorité de certification X.509](/azure/iot-hub/iot-hub-x509ca-overview#authenticating-devices-signed-with-x509-ca-certificates).

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>Contrôle de l’accès des appareils au service de provisionnement avec des certificats X.509

Le service de provisionnement expose deux types d’entrée d’inscription que vous pouvez utiliser pour contrôler l’accès des appareils qui recourent au mécanisme d’attestation X.509 :  

- Les entrées [d’inscription individuelle](./concepts-service.md#individual-enrollment) sont configurées avec le certificat d’appareil associé à un appareil spécifique. Ces entrées contrôlent l’inscription d’appareils spécifiques.
- Les entrées de [groupe d’inscriptions](./concepts-service.md#enrollment-group) sont associées à un certificat d’autorité de certification intermédiaire ou racine spécifique. Ces entrées contrôlent les inscriptions de tous les appareils dont la chaîne de chaîne d'approbation contient ce certificat racine ou intermédiaire. 

Quand un appareil se connecte au service de provisionnement, le service privilégie les entrées d’inscription plus spécifiques par rapport aux entrées d’inscription moins spécifiques. Autrement dit, si une inscription individuelle pour l’appareil existe, le service de provisionnement applique cette entrée. S’il n’existe aucune inscription individuelle pour l’appareil et qu’il existe un groupe d’inscriptions pour le premier certificat intermédiaire dans la chaîne d’approbation de l’appareil, le service applique cette entrée et ainsi de suite le long de la chaîne jusqu’à la racine. Le service applique la première entrée applicable qu’il trouve, comme suit :

- Si la première entrée d’inscription trouvée est activée, le service provisionne l’appareil.
- Si la première entrée d’inscription trouvée est désactivée, le service ne provisionne pas l’appareil.  
- Si aucune entrée d’inscription n’est trouvée pour aucun des certificats dans la chaîne d’approbation de l’appareil, le service ne provisionne pas l’appareil. 

Ce mécanisme et la structure hiérarchique des chaînes d’approbation vous permettent de contrôler en toute souplesse l’accès des appareils individuels, ainsi que des groupes d’appareils. Par exemple, imaginez cinq appareils avec les chaînes d’approbation suivantes : 

- *Appareil 1* : certificat racine -> certificat A -> certificat de l’appareil 1
- *Appareil 2* : certificat racine -> certificat A -> certificat de l’appareil 2
- *Appareil 3* : certificat racine -> certificat A -> certificat de l’appareil 3
- *Appareil 4* : certificat racine -> certificat B -> certificat de l’appareil 4
- *Appareil 5* : certificat racine -> certificat B -> certificat de l’appareil 5

Au départ, vous pouvez créer une entrée de groupe d’inscriptions activée unique pour le certificat racine afin de permettre l’accès pour les cinq appareils. Si, par la suite, le certificat B est compromis, vous pouvez créer une entrée de groupe d’inscriptions désactivée pour le certificat B afin d’empêcher l’inscription de *l’appareil 4* et de *l’appareil 5*. Si, un peu plus tard, *l’appareil 3* est compromis, vous pouvez créer une entrée d’inscription individuelle désactivée pour son certificat. Cette opération révoque l’accès de *l’appareil 3*, mais permet quand même à *l’appareil 1* et à *l’appareil 2* de s’inscrire.