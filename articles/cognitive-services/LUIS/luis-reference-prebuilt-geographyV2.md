---
title: Informations de référence sur l’entité prédéfinie geographyV2 - LUIS
titleSuffix: Azure Cognitive Services
description: Cet article contient des informations sur l’entité prédéfinie geographyV2 dans Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: 3559bc02944f88f486104d4d9553f0c45a1f1754
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46983410"
---
# <a name="geographyv2-entity"></a>Entité geographyV2
L’entité prédéfinie geographyV2 détecte les emplacements géographiques. Étant donné que cette entité est déjà entraînée, vous n’avez pas besoin d’ajouter d’exemples d’énoncés contenant geographyV2 aux intentions de l’application. L’entité geographyV2 est prise en charge pour la [culture](luis-reference-prebuilt-entities.md) Anglais.

## <a name="subtypes"></a>Sous-types
Les emplacements géographiques ont des sous-types :

|Subtype|Objectif|
|--|--|
|`poi`|Point d’intérêt|
|`city`|Nom de la ville|
|`countryRegion`|Nom du pays ou de la région|
|`continent`|Nom du continent|
|`state`|Nom de l’état ou de la province|


## <a name="resolution-for-geographyv2-entity"></a>Résolution de l’entité geographyV2
L’exemple suivant montre la résolution de l’entité **builtin.geographyV2**.

```JSON
{
    "query": "Carol is visiting the sphinx in gizah egypt in africa before heading to texas",
    "topScoringIntent": {
        "intent": "None",
        "score": 0.8008023
    },
    "intents": [
        {
            "intent": "None",
            "score": 0.8008023
        }
    ],
    "entities": [
        {
            "entity": "the sphinx",
            "type": "builtin.geographyV2.poi",
            "startIndex": 18,
            "endIndex": 27
        },
        {
            "entity": "gizah",
            "type": "builtin.geographyV2.city",
            "startIndex": 32,
            "endIndex": 36
        },
        {
            "entity": "egypt",
            "type": "builtin.geographyV2.countryRegion",
            "startIndex": 38,
            "endIndex": 42
        },
        {
            "entity": "africa",
            "type": "builtin.geographyV2.continent",
            "startIndex": 47,
            "endIndex": 52
        },
        {
            "entity": "texas",
            "type": "builtin.geographyV2.state",
            "startIndex": 72,
            "endIndex": 76
        },
        {
            "entity": "carol",
            "type": "builtin.personName",
            "startIndex": 0,
            "endIndex": 4
        }
    ]
} 
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les entités [email](luis-reference-prebuilt-email.md), [number](luis-reference-prebuilt-number.md) et [ordinal](luis-reference-prebuilt-ordinal.md). 