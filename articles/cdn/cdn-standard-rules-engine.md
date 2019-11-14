---
title: Appliquer HTTPS à l’aide du moteur de règles standard Azure CDN | Microsoft Docs
description: Le moteur de règles standard vous permet de personnaliser la manière dont Azure CDN de Microsoft gère les requêtes HTTP, telles que le blocage de la remise de certains types de contenus, la définition d’une stratégie de mise en cache et la modification des en-têtes HTTP.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: b24c4a04e0c02258a918ee075066d90c22ea0c75
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615828"
---
# <a name="azure-cdn-standard-rules-engine"></a>Moteur de règles standard Azure CDN

> [!NOTE]
> Le moteur de règles standard est uniquement disponible pour Azure CDN de Microsoft. 

Le moteur de règles Azure CDN standard vous permet de personnaliser la gestion des requêtes HTTP. Par exemple, l’application de la distribution de contenu sur des protocoles spécifiques, la définition d’une stratégie de mise en cache ou la modification d’un en-tête HTTP. Ce didacticiel montre comment créer une règle qui redirige automatiquement vos utilisateurs vers HTTPS. 


## <a name="tutorial"></a>Didacticiel

1. Dans la page **Profil CDN** sur Azure CDN à partir des profils Microsoft, sélectionnez le point de terminaison pour lequel vous souhaitez configurer des règles.
  
2. Sélectionnez l’onglet **Moteur de règles** à gauche.
   
    Le panneau du moteur de règles s’affiche, avec la règle globale. 
   
    [![Page de nouvelles règles CDN](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > L'ordre dans lequel plusieurs règles sont répertoriées affecte la façon dont elles sont gérées. Une règle ultérieure peut remplacer les actions spécifiées par une règle antérieure.
   >

3. Cliquez sur le bouton **Ajouter une règle** et spécifiez un nom de règle. Les noms de règle doivent commencer par une lettre et contenir uniquement des chiffres et des lettres.

4. Identifiez le type de requêtes auxquelles la règle s’applique. Utilisez la liste déroulante pour sélectionner la condition de correspondance **Protocole de requête**, et utilisez la valeur **Est égal à** **HTTP**.
   
   [![Condition de correspondance de règle CDN](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > Plusieurs conditions de correspondance sont disponibles dans la liste déroulante. Pour obtenir la liste détaillée des conditions de correspondance, consultez [Conditions de correspondance du moteur de règles](cdn-standard-rules-engine-match-conditions.md).
   
5. Choisissez l’action qui sera appliquée aux demandes identifiées. Utilisez la liste déroulante pour sélectionner l’action **Redirection d’URL** et utilisez la valeur **Trouvé (302)** pour le type, et **HTTPS** pour le protocole. Laissez tous les autres champs vides pour utiliser les valeurs entrantes.
   
   [![Action de règle CDN](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > Plusieurs actions sont disponibles dans la liste déroulante. Pour obtenir une liste détaillée des actions, consultez [Actions du moteur de règles](cdn-standard-rules-engine-actions.md).

6. Sélectionnez **Enregistrer** pour enregistrer la nouvelle règle.  La nouvelle règle sera maintenant déployée.
   
   > [!IMPORTANT]
   > Les modifications de règles peuvent prendre jusqu’à 15 minutes avant d’être propagées à travers Azure CDN.
   >
   

## <a name="see-also"></a>Voir aussi

- [Vue d’ensemble d’Azure CDN](cdn-overview.md)
- [Informations de référence du moteur de règles standard](cdn-standard-rules-engine-reference.md)
- [Conditions de correspondance du moteur de règles standard](cdn-standard-rules-engine-match-conditions.md)
- [Actions du moteur de règles standard](cdn-standard-rules-engine-actions.md)
