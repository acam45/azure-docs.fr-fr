---
title: Accéder à un laboratoire dans Azure DevTest Labs | Microsoft Docs
description: Dans ce didacticiel, vous accédez au laboratoire créé à l’aide d’Azure DevTest Labs, revendiquez des machines virtuelles, vous les utilisez, puis vous cessez de les revendiquer.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: ab52206230c4dfe2d92c97f1e291ee00a086c570
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49470861"
---
# <a name="tutorial-access-a-lab-in-azure-devtest-labs"></a>Didacticiel : accéder à un laboratoire dans Azure DevTest Labs
Dans ce didacticiel, vous utilisez le laboratoire créé dans le [didacticiel : créer un laboratoire dans Azure DevTest Labs](tutorial-create-custom-lab.md).

Dans ce tutoriel, vous allez effectuer les actions suivantes :

> [!div class="checklist"]
> * Revendiquer une machine virtuelle dans le laboratoire
> * Connexion à la machine virtuelle
> * Cesser de revendiquer la machine virtuelle

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="access-the-lab"></a>Accéder à l’atelier

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez **Toutes les ressources** dans le menu de gauche. 
3. Sélectionnez **DevTest Labs** comme type de ressource. 
4. Sélectionnez le laboratoire. 

    ![Sélectionner le laboratoire](./media/tutorial-use-custom-lab/search-for-select-custom-lab.png)

## <a name="claim-a-vm"></a>Revendiquer une machine virtuelle

1. Dans la liste des **machines virtuelles pouvant être réclamées**, sélectionnez **...** (points de suspension), puis sélectionnez **Revendiquer la machine**.

    ![Revendiquer une machine virtuelle](./media/tutorial-use-custom-lab/claim-virtual-machine.png)
1. Vérifiez que la présence de la machine virtuelle dans la liste **Mes machines virtuelles**.

    ![Ma machine virtuelle](./media/tutorial-use-custom-lab/my-virtual-machines.png)

## <a name="connect-to-the-vm"></a>Connexion à la machine virtuelle

1. Sélectionnez votre machine virtuelle dans la liste. Vous voyez la **page de la machine virtuelle** pour votre machine virtuelle. Sélectionnez **Se connecter** sur la barre d’outils.

    ![Connexion à la machine virtuelle](./media/tutorial-use-custom-lab/connect-button.png)
2. Enregistrer le fichier **RDP** téléchargé sur votre disque dur et utilisez-le pour vous connecter à la machine virtuelle. Spécifiez le nom d’utilisateur et le mot de passe que vous avez indiqués en créant la machine virtuelle à la section précédente. 

    > [!NOTE] 
    > Pour vous connecter à une machine virtuelle Linux, l’accès par protocole SSH et/ou RDP doit être activé pour la machine virtuelle. Pour les étapes à suivre pour vous connecter à une machine virtuelle Linux via un protocole RDP, consultez [Installer et configurer le Bureau à distance pour se connecter à une machine virtuelle Linux dans Azure](../virtual-machines/linux/use-remote-desktop.md). 


## <a name="unclaim-the-vm"></a>Cesser de revendiquer la machine virtuelle
Une fois que vous avez fini d’utiliser la machine virtuelle, cessez de la revendiquer en suivant ces étapes : 

1. Sur la page de la machine virtuelle, sélectionnez **Cesser de revendiquer** sur la barre d’outils. 

    ![Cesser de revendiquer une machine virtuelle](./media/tutorial-use-custom-lab/unclaim-vm-menu.png)
1. La machine virtuelle est arrêtée avant que la revendication ne cesse. 

    ![État de fin de revendication](./media/tutorial-use-custom-lab/unclaim-status.png) 
1. Une fois l’opération de fin de revendication terminée, vous voyez la machine virtuelle dans la liste des **machines virtuelles pouvant être réclamées** en bas. 
    
## <a name="next-steps"></a>Étapes suivantes
Ce didacticiel vous a montré comment accéder à et utiliser un laboratoire créé à l’aide d’Azure DevTest Labs. Pour plus d’informations sur l’accès et l’utilisation de machines virtuelles dans un laboratoire personnalisé, consultez 

> [!div class="nextstepaction"]
> [Procédure : utiliser des machines virtuelles dans un laboratoire](devtest-lab-add-vm.md)

