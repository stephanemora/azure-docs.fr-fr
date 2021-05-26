---
title: Clés gérées par le client - Azure Video Analyzer
description: Vous pouvez utiliser une clé gérée par le client (autrement dit, apporter votre propre clé) avec Azure Video Analyzer.
author: anilmur
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 05/04/2021
ms.openlocfilehash: 6e41b4d411f4f0101c160e48a10abd12385e7c8d
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110385891"
---
# <a name="customer-managed-keys-with-azure-video-analyzer"></a>Clés gérées par le client avec Azure Video Analyzer

Bring Your Own Key (BYOK) est une initiative à l’échelle d’Azure qui aide les clients à déplacer leurs charges de travail vers le cloud. Les clés gérées par le client permettent aux clients d’adhérer aux réglementations du secteur en matière de conformité et d’améliorer l’isolation des locataires d’un service. Le fait de donner le contrôle des clés de chiffrement aux clients est un moyen de réduire l’accès et le contrôle inutiles et d’améliorer la confiance dans les services Microsoft.

## <a name="keys-and-key-management"></a>Clés et gestion des clés

Une clé de compte est créée pour tous les comptes Video Analyzer. Par défaut, cette clé de compte est chiffrée par une clé système appartenant à Video Analyzer (c’est-à-dire une clé gérée par le système). À la place, vous pouvez utiliser votre propre clé avec Azure Video Analyzer. Dans ce cas, votre clé de compte est chiffrée avec votre clé. Les stratégies d’accès et les métadonnées de ressource vidéo sont chiffrées à l’aide de la clé de compte.

Video Analyzer utilise une identité managée affectée par l’utilisateur pour lire votre clé à partir d’un coffre de clés qui vous appartient. Vous devez fournir l’identité managée affectée par l’utilisateur quand vous créez ou mettez à jour le compte Video Analyzer, et attribuer le [contrôle d’accès en fonction du rôle Azure] approprié../../role-based-access-control/overview.md) sur le coffre de clés. Pour Video Analyzer, le coffre de clés doit être dans la même région que le compte, et doit avoir la suppression réversible et la protection contre la suppression définitive activées.

Votre clé peut être une clé RSA 2048, 3072 ou 4096, et les clés HSM et logicielles sont toutes deux prises en charge.

> [!NOTE]
> Les clés EC ne sont pas prises en charge.

Vous pouvez spécifier un nom et une version de clé, ou simplement un nom de clé. Si vous utilisez seulement un nom de clé, Video Analyzer utilise la dernière version de la clé. Les nouvelles versions de clés de client sont détectées automatiquement et la clé de compte est à nouveau chiffrée.

> [!WARNING]
> Video Analyzer supervise l’accès à la clé de client. Si la clé de client devient inaccessible (par exemple, la clé ou le coffre de clés ont été supprimés ou l’accès accordé a été supprimé), Video Analyzer définit l’état du compte sur Clé de client inaccessible (ce qui a pour effet de désactiver le compte). Vous pouvez supprimer le compte ou restaurer la clé de compte pour reprendre l’accès.

## <a name="double-encryption"></a>Double chiffrement

Video Analyzer protège vos données sensibles à l’aide du chiffrement double conformément aux pratiques standard d’Azure. Consultez [Chiffrement double Azure](../../security/fundamentals/double-encryption.md). Pour les données au repos, la première couche de chiffrement utilise une clé gérée par le client ou une clé gérée par Microsoft en fonction du paramètre `encryption` sur le compte. La deuxième couche de chiffrement des données au repos est fournie automatiquement à l’aide d’une clé gérée par Microsoft distincte.

> [!NOTE]
> Le chiffrement double est activé automatiquement sur le compte Video Analyzer. Toutefois, vous devez configurer séparément la clé gérée par le client et le chiffrement double sur votre compte de stockage. Consultez [Chiffrement du stockage](../../storage/common/storage-service-encryption.md).


## <a name="next-steps"></a>Étapes suivantes

Consultez [Identité managée](managed-identity.md)
