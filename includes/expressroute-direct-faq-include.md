---
title: Fichier Include
description: Fichier Include
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f4a1937062f7e59cb3ef3f38610e077e8d36a3ac
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47005547"
---
### <a name="what-is-expressroute-direct"></a>Présentation d’ExpressRoute Direct

Avec ExpressRoute Direct, les clients ont la possibilité de se connecter directement au réseau international de Microsoft à partir d’emplacements d’appairage qui sont distribués stratégiquement dans le monde entier. ExpressRoute Direct offre une double connectivité de 100 Gbits/s qui prend en charge la connectivité Active/Active à grande échelle. 

### <a name="how-do-customers-connect-to-expressroute-direct"></a>Comment les clients peuvent-ils se connecter à ExpressRoute Direct ? 

Les clients doivent se faire aider de leurs opérateurs locaux et de leurs fournisseurs de colocalisation pour obtenir une connectivité aux routeurs ExpressRoute dans le but d’utiliser ExpressRoute Direct.

### <a name="what-locations-will-the-100gbps-expressroute-direct-be-available-for-public-preview"></a>Dans quelles régions la préversion publique d’ExpressRoute Direct 100 Gbits/s est disponible ? 

Un nombre limité de régions d’appairage ExpressRoute prennent en charge cette préversion publique. Les ports disponibles sont dynamiques et sont fournis par PowerShell pour afficher la capacité. Les régions, qui *sont susceptibles d’être modifiées selon la disponibilité*, sont les suivantes :

* Amsterdam
* Canberra
* Chicago
* Washington DC
* Dallas 
* Hong Kong (R.A.S.)
* Los Angeles
* New York
* Paris
* San Antonio
* Silicon Valley
* Singapour 

### <a name="what-is-the-sla-for-expressroute-direct"></a>Quel contrat SLA s’applique à ExpressRoute Direct ?

ExpressRoute Direct utilise la même [version d’entreprise d’ExpressRoute](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/). 

### <a name="what-scenarios-should-customers-consider-with-expressroute-direct"></a>Quels scénarios les clients peuvent-ils envisager avec ExpressRoute Direct ?  

ExpressRoute Direct fournit aux clients des paires de ports directs 100 Gbits/s pour la structure globale de Microsoft. Les scénarios qui apportent les plus grands bénéfices sont les suivants : ingestion massive de données, isolation physique des marchés réglementés et capacité dédiée pour les scénarios de rafales, tels que le rendu. 

### <a name="what-is-the-billing-model-for-expressroute-direct"></a>Quel modèle de facturation s’applique à ExpressRoute Direct ? 

Un montant fixe est facturé pour la paire de ports. Les circuits standard sont compris sans frais supplémentaires, tandis que les circuits Premium engendrent un petit supplément. La sortie est facturée pour chaque circuit, en fonction de la zone où se trouve l’emplacement d’appairage.