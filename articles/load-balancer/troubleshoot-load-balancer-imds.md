---
title: Codes d’erreur courants pour Azure Instance Metadata Service (IMDS)
titleSuffix: Azure Load Balancer
description: Vue d’ensemble des codes d’erreur courants et des méthodes d’atténuation correspondantes pour Azure Instance Metadata Service (IMDS)
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: e932e211996a05b2740613381735a7de3492e5bf
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519182"
---
# <a name="error-codes-common-error-codes-when-using-imds-to-retrieve-load-balancer-information"></a>Codes d’erreur : Codes d’erreur courants lors de l’utilisation d’IMDS pour récupérer les informations de l’équilibreur de charge

Cet article décrit les erreurs de déploiement courantes et explique comment les résoudre lors de l’utilisation d’Azure Instance Metadata Service (IMDS).

## <a name="error-codes"></a>Codes d’erreur

| Code d'erreur | Message d’erreur | Détails et atténuation |
| --- | ---------- | ----------------- |
| 400 | Paramètre obligatoire manquant « \<ParameterName> ». Corrigez la requête et réessayez. | Le code d’erreur indique un paramètre manquant. </br> Pour plus d’informations sur l’ajout du paramètre manquant, consultez [Guide pratique pour récupérer les métadonnées de l’équilibreur de charge à l’aide d’Azure Instance Metadata Service (IMDS)](howto-load-balancer-imds.md#sample-request-and-response).
| 400 | La valeur du paramètre n’est pas autorisée ou la valeur de paramètre « \<ParameterValue> » n’est pas autorisée pour le paramètre « nom_du_paramètre ». Corrigez la requête et réessayez. | Le code d’erreur indique que le format de la demande n’est pas configuré correctement. </br> Pour plus d’informations, consultez [Guide pratique pour récupérer les métadonnées de l’équilibreur de charge à l’aide d’Azure Instance Metadata Service (IMDS)](howto-load-balancer-imds.md#sample-request-and-response) afin de corriger le corps de la demande et d’effectuer une nouvelle tentative. |
| 400 | Demande inattendue. Vérifiez les paramètres de la requête et réessayez. | Le code d’erreur indique que le format de la demande n’est pas configuré correctement. </br> Pour plus d’informations, consultez [Guide pratique pour récupérer les métadonnées de l’équilibreur de charge à l’aide d’Azure Instance Metadata Service (IMDS)](howto-load-balancer-imds.md#sample-request-and-response) afin de corriger le corps de la demande et d’effectuer une nouvelle tentative. |
| 404 | Aucune métadonnée d’équilibreur de charge n’a été trouvée. Vérifiez si votre machine virtuelle utilise un équilibreur de charge avec une référence SKU autre qu’une référence de base et réessayez plus tard. | Le code d’erreur indique que votre machine virtuelle n’est pas associée à un équilibreur de charge ou que l’équilibreur de charge est une référence SKU de base au lieu de standard. </br> Pour plus d’informations, consultez [Démarrage rapide : Créer un équilibreur de charge de base public pour équilibrer la charge de machines virtuelles en utilisant le portail Azure](quickstart-load-balancer-standard-public-portal.md?tabs=option-1-create-load-balancer-standard) afin de déployer un équilibreur de charge standard.|
| 404 | L’API est introuvable : Chemin = « \<UrlPath> », méthode = « \<Method> » | Le code d’erreur indique un problème de configuration du chemin. </br> Pour plus d’informations, consultez [Guide pratique pour récupérer les métadonnées de l’équilibreur de charge à l’aide d’Azure Instance Metadata Service (IMDS)](howto-load-balancer-imds.md#sample-request-and-response) afin de corriger le corps de la demande et d’effectuer une nouvelle tentative.|
| 405 | La méthode HTTP n’est pas autorisée : Chemin = « \<UrlPath> », méthode = « \<Method> » | Le code d’erreur indique un verbe HTTP non pris en charge. </br> Pour plus d’informations sur les verbes pris en charge, consultez [Service de métadonnées d’instance Azure](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#http-verbs). |
| 429 | Trop de demandes | Le code d’erreur indique une limite de débit. </br> Pour plus d’informations sur la limitation du débit, consultez [Service de métadonnées d’instance Azure](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#rate-limiting).|
| 400 | La longueur du corps de la demande est supérieure à MaxBodyLength :... | Le code d’erreur indique que la longueur de la demande est supérieure à MaxBodyLength. </br> Pour plus d’informations sur la longueur du corps, consultez [Guide pratique pour récupérer les métadonnées de l’équilibreur de charge à l’aide d’Azure Instance Metadata Service (IMDS)](howto-load-balancer-imds.md#sample-request-and-response).|
| 400 | La longueur de la clé du paramètre est supérieure à MaxParameterKeyLength : ... | Le code d’erreur indique une longueur de clé de paramètre supérieure à MaxParameterKeyLength. </br> Pour plus d’informations sur la longueur du corps, consultez [Guide pratique pour récupérer les métadonnées de l’équilibreur de charge à l’aide d’Azure Instance Metadata Service (IMDS)](howto-load-balancer-imds.md#sample-request-and-response). |
| 400 | La longueur de la valeur du paramètre est supérieure à MaxParameterValueLength : ... | Le code d’erreur indique une longueur de clé de paramètre supérieure à MaxParameterValueLength. </br> Pour plus d’informations sur la longueur de la valeur, consultez [Guide pratique pour récupérer les métadonnées de l’équilibreur de charge à l’aide d’Azure Instance Metadata Service (IMDS)](howto-load-balancer-imds.md#sample-request-and-response).|
| 400 | La longueur de la valeur de l’en-tête du paramètre est supérieure à MaxHeaderValueLength : ... | Le code d’erreur indique une longueur de valeur d’en-tête de paramètre supérieure à MaxHeaderValueLength. </br> Pour plus d’informations sur la longueur de la valeur, consultez [Guide pratique pour récupérer les métadonnées de l’équilibreur de charge à l’aide d’Azure Instance Metadata Service (IMDS)](howto-load-balancer-imds.md#sample-request-and-response).|
| 404 | L’API des métadonnées de l’équilibreur de charge n’est pas disponible pour le moment. Veuillez réessayer ultérieurement. | Le code d’erreur indique que l’API est peut-être en cours de provisionnement. Essayez votre demande ultérieurement. |
| 404 | /metadata/loadbalancer n’est pas disponible | Le code d’erreur indique que l’API est en cours d’activation. Essayez votre demande ultérieurement. |
| 503 | Service interne non disponible. Veuillez réessayer ultérieurement.  | Le code d’erreur indique que l’API est temporairement indisponible. Essayez votre demande ultérieurement. |
|  |  |

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [Azure Instance Metadata Service](../virtual-machines/windows/instance-metadata-service.md)

