---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 04/22/2019
ms.author: tarcher
ms.openlocfilehash: eb96027351cf244e9cd4404f702544411130db5e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "67177753"
---
[Azure Service Bus](/azure/service-bus-messaging/service-bus-messaging-overview) est un répartiteur de messages d’[intégration](https://azure.microsoft.com/product-categories/integration/) d’entreprise. Service Bus prend en charge deux types de communications : les files d’attente et les rubriques. 

Les files d’attente prennent en charge les communications asynchrones entre les applications. Une application envoie des messages à une file d’attente qui stocke les messages. L’application de réception se connecte ensuite pour lire les messages à partir de la file d’attente.

Les rubriques prennent en charge le modèle de publication-abonnement, ce qui permet une relation de un-à-plusieurs entre l’expéditeur du message et le ou les récepteurs de ce message.