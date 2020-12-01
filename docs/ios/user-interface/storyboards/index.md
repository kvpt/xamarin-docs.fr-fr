---
title: Conception d’interfaces utilisateur avec Xcode
description: En savoir plus sur la méthode recommandée pour créer des interfaces utilisateur iOS directement à l’aide de XCode sur un Mac.
ms.prod: xamarin
ms.assetid: af9f95db-5cd6-475d-867d-f73e1574e8fc
ms.technology: xamarin-ios
author: joshspicer
ms.author: jospicer
ms.date: 10/27/2020
ms.openlocfilehash: b25245250bd9ea17034ea8f6b11388a12fc13241
ms.sourcegitcommit: d1f0e0a9100548cfe0960ed2225b979cc1d7c28f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96439546"
---
# <a name="designing-user-interfaces-with-xcode"></a>Conception d’interfaces utilisateur avec Xcode

À compter de Visual Studio 2019 version 16,8 et Visual Studio pour Mac version 8,8, la méthode recommandée pour modifier les fichiers. Storyboard et. bec consiste à les modifier dans Xcode Interface Builder sur un Mac.

> [!NOTE]
> À compter de Visual Studio 2019 version 16,9, aucune méthode n’est prise en charge pour modifier les storyboards iOS sur Windows. Utilisez Visual Studio pour Mac et le Interface Builder Xcode pour continuer à créer vos interfaces utilisateur Xamarin. iOS.

Cet article traite des solutions courantes pour créer des interfaces utilisateur avec l’Interface Builder Xcode.  Cet article peut être particulièrement utile si vous avez précédemment modifié vos interfaces utilisateur avec le concepteur Xamarin. iOS. 

Pour obtenir une procédure pas à pas détaillée des storyboards, consultez [storyboards dans Xamarin. iOS](./indepth-storyboard.md).

## <a name="how-to-open-a-storyboard"></a>Comment ouvrir une table de montage séquentiel 

Ouvrez un fichier d’interface utilisateur iOS dans Visual Studio pour Mac en cliquant avec le bouton droit sur un fichier de Storyboard et en sélectionnant **Xcode Interface Builder**:

[![Sélectionnez Interface Builder](images/select-interface-builder.png)](images/select-interface-builder.png#lightbox)

Vous devez alors voir la fenêtre Xcode ouverte. Toutes les modifications enregistrées ici seront reflétées dans votre projet Visual Studio.

[![Fenêtre Xcode](images/xcode.png)](images/xcode.png#lightbox)

Pour plus d’informations sur le Interface Builder Xcode, consultez [Interface Builder intégré](https://developer.apple.com/xcode/interface-builder/).

## <a name="creating-a-new-control"></a>Création d’un nouveau contrôle

Pour créer un contrôle avec l’Interface Builder Xcode, commencez par sélectionner le Storyboard que vous souhaitez modifier. Ensuite, ouvrez la boîte de dialogue de la bibliothèque Xcode (**Afficher**  >  la **bibliothèque**) et faites glisser le contrôle vers votre table de montage séquentiel.

[![Sélecteur de bibliothèque](images/library-picker.png)](images/library-picker.png#lightbox)

Ensuite, ouvrez le fichier d’en-tête du contrôleur d’affichage correspondant.  Pour une application Xamarin. iOS vide, le Storyboard par défaut est appelé **main. Storyboard**. Le fichier de contrôleur d’affichage correspondant est appelé **ViewController.cs** dans Visual Studio avec un fichier d’en-tête **ViewController. h** correspondant quand vous l’affichez à partir de Xcode.

À partir de l’Interface Builder Xcode, ouvrez le Storyboard et le fichier d’en-tête du contrôleur d’affichage correspondant.  En maintenant la touche **CTRL** () enfoncée **^** , faites glisser le contrôle de la table de montage séquentiel vers le fichier de contrôleur d’affichage jusqu’à ce que Xcode vous invite à saisir une boîte de dialogue.

[![Contrôle de lien de démonstration](images/demo-link-control.gif)](images/demo-link-control.gif#lightbox)

Comme illustré ci-dessus, le code C# correspondant sera généré automatiquement dans le fichier code-behind du contrôleur d’affichage.  Vous pouvez maintenant accéder à ce contrôle dans votre projet Xamarin. iOS.

## <a name="editing-an-existing-controls-name"></a>Modification du nom d’un contrôle existant

Pour modifier le nom d’un contrôle existant à partir de l’Interface Builder Xcode et répercuter cette modification dans votre projet C#, accédez au fichier d’en-tête du contrôleur d’affichage approprié, cliquez avec le bouton droit et sélectionnez **Refactoriser**.   

[![Contrôle de refactorisation](images/refactor-control.png)](images/refactor-control.png#lightbox)

Votre fichier code-behind est régénéré avec le nouveau nom, ce qui vous permet d’accéder au contrôle par le biais du code de Visual Studio pour Mac.

## <a name="known-problems"></a>Problèmes connus

Cette section traite des problèmes connus.

### <a name="visual-studio-could-not-communicate-with-xcode"></a>« Visual Studio n’a pas pu communiquer avec Xcode »

Dans macOS Catalina ou version ultérieure, vous pouvez rencontrer l’erreur ci-dessous :

[![Impossible de communiquer Err](images/could-not-communicate.png)](images/could-not-communicate.png#lightbox)

Tout d’abord, dans les préférences système de votre Mac sous **sécurité & confidentialité > Automation**, assurez-vous que Visual Studio est listé et que **Xcode** est activé.

[![sécurité macOS](images/macos-security.png)](images/macos-security.png#lightbox)

Si **Xcode** est coché et que le message d’erreur s’affiche toujours, vous devrez peut-être réinitialiser les autorisations de confidentialité Visual Studio pour Mac.

Pour ce faire, vous pouvez lancer une fenêtre de terminal et émettre la commande suivante :

```bash
sudo tccutil reset All "com.microsoft.visual-studio"
```

Pour vous assurer que les modifications apportées ci-dessus prennent effet, réinitialisez le cinquième de votre Mac. Pour obtenir des instructions, consultez [Réinitialiser la mémoire NVRAM ou le cinquième sur votre Mac](https://support.apple.com/HT204063).
