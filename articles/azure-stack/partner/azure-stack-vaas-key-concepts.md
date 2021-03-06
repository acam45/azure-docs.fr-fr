---
title: Concepts clés de la validation en tant que service Azure Stack | Microsoft Docs
description: Décrit les concepts clés de la Validation en tant que Service Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 3eb46e493ce2fd6a8d759cabf2de968b802af2e7
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49650899"
---
# <a name="validation-as-a-service-key-concepts"></a>Concepts clés de la Validation en tant que service

Cet article décrit les concepts clés de la Validation en tant que Service (VaaS).

## <a name="solutions"></a>solutions

Une solution VaaS est une solution Azure Stack associée à une nomenclature matérielle (BoM) particulière. Elle agit comme un conteneur pour les workflows qui s’exécutent sur la solution Azure Stack.

### <a name="create-a-solution-in-the-vaas-portal"></a>Créer une solution dans le portail VaaS

1. Connectez-vous au [portail VaaS](https://azurestackvalidation.com).
2. Dans le tableau de bord des solutions, sélectionnez **Nouvelle solution**.
3. Entrez un nom pour la solution. Pour des suggestions de dénomination, consultez [Naming convention for VaaS workflows](azure-stack-vaas-best-practice.md#naming-convention-for-vaas-solutions) (Conventions d’affectation de noms pour les flux de travail VaaS).
4. Sélectionnez **Enregistrer** pour créer la solution.

## <a name="workflows"></a>Flux de travail

Un flux de travail VaaS intervient dans le contexte d’une solution VaaS. Il représente un ensemble de suites de tests qui testent les fonctionnalités d’un déploiement Azure Stack. Un flux de travail doit être créé pour chaque déploiement ou mise à jour logicielle d’une solution Azure Stack.

Les flux de travail sont classés par type de scénario de test. Concernant les tests non officiels, le flux de travail **Passe de test** vous permet de sélectionner les tests à partir de tous les outils annexes VaaS disponibles. Dans le test officiel, les flux de travail de **validation** ciblent des scénarios de test spécifiques sélectionnés par Microsoft.

![Vignettes de flux de travail VaaS](media/tile_all-workflows.png)

> [!NOTE]
> Le flux de travail **Validation du Package** prend actuellement en charge deux scénarios : [Valider les packages OEM](azure-stack-vaas-validate-oem-package.md) et [Valider les mises à jour logicielles issues de Microsoft](azure-stack-vaas-validate-microsoft-updates.md).

Pour plus d’informations sur les types de workflow, consultez l’article [Qu’est-ce que la validation en tant que service pour Azure Stack ?](azure-stack-vaas-overview.md).

### <a name="getting-started-with-vaas-workflows"></a>Prise en main des flux de travail VaaS

1. Sur le tableau de bord des solutions, créez une solution ou sélectionnez une solution existante. Cela actualise et permet de générer des vignettes de workflow.
2. Pour créer un nouveau flux de travail, sélectionnez **Démarrer** sur n’importe quelle vignette. Pour des informations spécifiques à chaque flux de travail, consultez les articles suivants :
    - Passe de test : [Démarrage rapide : utiliser le portail de validation en tant que service pour planifier votre premier test](azure-stack-vaas-schedule-test-pass.md)
    - Validation des solutions : [Valider une nouvelle solution Azure Stack](azure-stack-vaas-validate-solution-new.md)
    - Validation du package : [Valider les mises à jour logicielles issues de Microsoft](azure-stack-vaas-validate-microsoft-updates.md)
    - Validation du package : [Valider les packages OEM](azure-stack-vaas-validate-oem-package.md)

3. Pour gérer ou surveiller un flux de travail existant, sélectionnez **Gérer** au niveau de la vignette de flux de travail. Sélectionnez le nom du flux de travail et utilisez le bouton **Modifier** pour afficher les propriétés ou modifier les paramètres de test communs.

Pour plus d’informations sur les propriétés et paramètres du workflow, consultez l’article [Paramètres de flux de travail communs dans la validation en tant que service Azure Stack](azure-stack-vaas-parameters.md).

## <a name="tests"></a>Tests

Un test dans VaaS se compose d’une suite d’actions exécutées sur une solution Azure Stack. Les tests ont différents rôles identifiés par une catégorie, par exemple fonctionnel ou fiabilité, et ciblent un ou plusieurs services d’Azure Stack. Chaque test définit son propre ensemble de paramètres, dont certains sont spécifiés par des paramètres communs du flux de travail contenant.

Pour plus d’informations sur les tests de gestion et de surveillance, consultez [Surveiller un test avec le service Validation en tant que service pour Azure Stack](azure-stack-vaas-monitor-test.md).

Pour plus d’informations sur les paramètres de test, consultez l’article [Paramètres de flux de travail communs dans la validation en tant que service Azure Stack](azure-stack-vaas-parameters.md).

## <a name="agents"></a>Agents

Un agent VaaS conduit l’exécution des tests. Deux types d’agents exécutent les tests VaaS :

- L’**agent cloud**. Il s’agit de l’agent par défaut disponible dans VaaS. Aucune installation n’est requise, mais cela nécessite une connectivité entrante à votre environnement. De plus, les points de terminaison Azure Stack doivent pouvoir être résolus à l’aide d’internet. Certains tests ne sont pas compatibles avec l’agent cloud.
- Un **agent local**. Il vous permet d’exécuter la validation dans les scénarios où la connectivité entrante à votre environnement n’est pas possible. Certains tests requièrent d’être exécutés via l’agent local.

Les agents locaux ne sont pas liés à une solution Azure Stack ou VaaS spécifique. Comme meilleure pratique, ils doivent être exécutés hors d’un environnement Azure Stack.

Pour obtenir des instructions sur l’ajout d’un agent local, consultez [Deploy the local agent](azure-stack-vaas-local-agent.md) (déployer l’agent local).

## <a name="next-steps"></a>Étapes suivantes

- [Best practices for Validation as a Service](azure-stack-vaas-best-practice.md) (Meilleures pratiques concernant la validation en tant que service)