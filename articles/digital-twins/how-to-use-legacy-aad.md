---
title: Utiliser l’inscription d’applications AAD existantes pour Azure Digital Twins | Microsoft Docs
description: Cet article explique comment utiliser l’ancienne méthode de création de l’inscription d’applications avec Azure Active Directory pour la configuration d’Azure Digital Twins.
author: alinamstanciu
manager: philmea
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: alinast
ms.openlocfilehash: ed6c540cedd21bb5154f8746050d5eaa9444b818
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484722"
---
# <a name="register-your-azure-digital-twins-app-with-azure-active-directory-legacy"></a>Inscrire votre application Azure Digital Twins avec l’ancien Azure Active Directory

Cet article explique comment utiliser l’ancienne ou la nouvelle méthode pour enregistrer votre exemple d’application dans Azure Active Directory (Azure AD) afin que celui-ci puisse accéder à votre instance Digital Twins. Vous devriez essayer cette méthode au cas où la nouvelle méthode d’inscription d’applications Azure AD ne fonctionne pas pour votre configuration.

[!INCLUDE [Digital Twins legacy AAD](../../includes/digital-twins-permissions-legacy.md)]

## <a name="next-steps"></a>Étapes suivantes

Une fois votre application inscrite auprès d’AAD, elle peut se connecter à votre instance Digital Twins et vous aider à avancer dans votre scénario. Consultez le [Guide de démarrage rapide](quickstart-view-occupancy-dotnet.md#build-application) ou le [tutoriel](tutorial-facilities-setup.md#configure-the-digital-twins-sample) pour plus d’informations sur les étapes suivantes. 