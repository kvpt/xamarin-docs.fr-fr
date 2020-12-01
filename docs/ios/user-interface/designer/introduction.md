---
title: notions de base du concepteur iOS
description: Ce guide présente les Xamarin Designer pour iOS. Il montre comment utiliser le concepteur iOS pour disposer visuellement des contrôles, comment accéder à ces contrôles dans le code et comment modifier des propriétés.
ms.prod: xamarin
ms.assetid: E7045E41-0DEF-416B-BCDB-52502350F61C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 01/31/2018
ms.openlocfilehash: 07f3e313a743c9d95f8baf2be7a31f16c6ca4f7e
ms.sourcegitcommit: d1f0e0a9100548cfe0960ed2225b979cc1d7c28f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96439479"
---
# <a name="ios-designer-basics"></a>notions de base du concepteur iOS

_Ce guide présente les Xamarin Designer pour iOS. Il montre comment utiliser le concepteur iOS pour disposer visuellement des contrôles, comment accéder à ces contrôles dans le code et comment modifier des propriétés._

> [!WARNING]
> Le concepteur iOS va commencer à être supprimé dans Visual Studio 2019 version 16,8 et Visual Studio 2019 pour Mac version 8,8.
> La méthode recommandée pour créer des interfaces utilisateur iOS est directement sur un Mac exécutant Xcode. Pour plus d’informations, consultez [conception d’interfaces utilisateur avec Xcode](../storyboards/index.md). 

Le Xamarin Designer pour iOS est un concepteur d’interface visuel semblable au Interface Builder de Xcode et le Android Designer. Certaines de ses nombreuses fonctionnalités incluent une intégration transparente à Visual Studio pour Windows et Mac, la modification par glisser-déplacer, une interface pour la configuration des gestionnaires d’événements et la possibilité de restituer des contrôles personnalisés.

## <a name="requirements"></a>Spécifications

Le concepteur iOS est disponible dans Visual Studio pour Mac et Visual Studio 2017 et versions ultérieures sur Windows. Dans Visual Studio pour Windows, le concepteur iOS requiert une connexion à un hôte de build Mac correctement configuré, même si Xcode n’a pas besoin d’être en cours d’exécution.

Ce guide suppose que vous connaissez le contenu abordé dans les guides de [prise en main](~/ios/get-started/index.md).

<a name="how-it-works"></a>

## <a name="how-the-ios-designer-works"></a>Fonctionnement du concepteur iOS

Cette section décrit comment le concepteur iOS facilite la création d’une interface utilisateur et son connexion au code.

Le concepteur iOS permet aux développeurs de concevoir visuellement l’interface utilisateur d’une application. Comme indiqué dans le guide [Présentation des storyboards](~/ios/user-interface/storyboards/index.md) , un Storyboard décrit les écrans (contrôleurs d’affichage) qui composent une application, les éléments d’interface (vues) placés sur ces contrôleurs d’affichage et le déroulement global de navigation de l’application. 

Un contrôleur d’affichage se compose de deux parties : une représentation visuelle dans le concepteur iOS et une classe C# associée :

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

[![Un contrôleur d’affichage dans le concepteur iOS](introduction-images/1-storyboardwithviewcontroller-vsmac.png "Un contrôleur d’affichage dans le concepteur iOS")](introduction-images/1-storyboardwithviewcontroller-vsmac-large.png#lightbox)

[![Code d’un contrôleur d’affichage](introduction-images/2-viewcontrollercode-vsmac.png "Code d’un contrôleur d’affichage")](introduction-images/2-viewcontrollercode-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Un contrôleur d’affichage dans le concepteur iOS](introduction-images/1-storyboardwithviewcontroller-vs.png "Un contrôleur d’affichage dans le concepteur iOS")](introduction-images/1-storyboardwithviewcontroller-vs-large.png#lightbox)

[![Code d’un contrôleur d’affichage](introduction-images/2-viewcontrollercode-vs.png "Code d’un contrôleur d’affichage")](introduction-images/2-viewcontrollercode-vs-large.png#lightbox)

-----

Dans son état par défaut, un contrôleur d’affichage ne fournit aucune fonctionnalité. elle doit être remplie avec des contrôles. Ces contrôles sont placés dans l’affichage du contrôleur d’affichage, la zone rectangulaire qui contient tout le contenu de l’écran. La plupart des contrôleurs d’affichage contiennent des contrôles communs, tels que des boutons, des étiquettes et des champs de texte, comme illustré dans la capture d’écran suivante, qui montre un contrôleur d’affichage contenant un bouton : 

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

[![Un contrôleur d’affichage contenant un bouton](introduction-images/3-viewcontrollerwithbutton-vsmac.png "Un contrôleur d’affichage contenant un bouton")](introduction-images/3-viewcontrollerwithbutton-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Un contrôleur d’affichage contenant un bouton](introduction-images/3-viewcontrollerwithbutton-vs.png "Un contrôleur d’affichage contenant un bouton")](introduction-images/3-viewcontrollerwithbutton-vs-large.png#lightbox)

-----

Certains contrôles, tels que les étiquettes contenant du texte statique, peuvent être ajoutés au contrôleur d’affichage et à gauche uniquement. Toutefois, plus souvent, les contrôles doivent être personnalisés par programmation. Par exemple, le bouton ajouté ci-dessus doit faire une action lorsqu’il est appuyé, de sorte qu’un gestionnaire d’événements doit être ajouté dans le code.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Pour pouvoir accéder et manipuler le bouton dans le code, il doit avoir un identificateur unique. Fournissez un identificateur unique en sélectionnant le bouton, en ouvrant la **panneau Propriétés** et en définissant son champ de **nom** sur une valeur telle que « submitButton » :

[![Définition du nom d’un bouton dans le Panneau Propriétés](introduction-images/4-settingbuttonname-vsmac.png "Définition du nom d’un bouton dans le Panneau Propriétés")](introduction-images/4-settingbuttonname-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Pour pouvoir accéder et manipuler le bouton dans le code, il doit avoir un identificateur unique. Fournissez un identificateur unique en sélectionnant le bouton, en ouvrant la **fenêtre Propriétés**, et en définissant son champ **Name** sur une valeur telle que « submitButton » :

[![Définition du nom d’un bouton dans la fenêtre Propriétés](introduction-images/4-settingbuttonname-vs.png "Définition du nom d’un bouton dans la fenêtre Propriétés")](introduction-images/4-settingbuttonname-vs-large.png#lightbox)

-----

Maintenant que le bouton a un nom, il est accessible dans le code. Mais comment cela fonctionne-t-il ?

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Dans l' **panneau solutions**, la navigation jusqu’à **ViewController.cs** et en cliquant sur l’indicateur de divulgation révèle que la définition de classe du contrôleur d’affichage s' `ViewController` étend sur deux fichiers, chacun contenant une définition de [classe partielle](/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods) :

[![Les deux fichiers qui composent la classe ViewController : ViewController.cs et ViewController.designer.cs](introduction-images/5-twoviewcontrollerfiles-vsmac.png "Les deux fichiers qui composent la classe ViewController : ViewController.cs et ViewController.designer.cs")](introduction-images/5-twoviewcontrollerfiles-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Dans l' **Explorateur de solutions**, la navigation jusqu’à **ViewController.cs** et en cliquant sur l’indicateur de divulgation révèle que la définition de classe du contrôleur d’affichage s' `ViewController` étend sur deux fichiers, chacun contenant une définition de [classe partielle](/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods) :

[![Les deux fichiers qui composent la classe ViewController : ViewController.cs et ViewController.designer.cs](introduction-images/5-twoviewcontrollerfiles-vs.png "Les deux fichiers qui composent la classe ViewController : ViewController.cs et ViewController.designer.cs")](introduction-images/5-twoviewcontrollerfiles-vs-large.png#lightbox)

-----

- **ViewController.cs** doit être rempli avec un code personnalisé associé à la `ViewController` classe. Dans ce fichier, la `ViewController` classe peut répondre à différentes méthodes de cycle de vie du contrôleur d’affichage iOS, personnaliser l’interface utilisateur et répondre aux entrées de l’utilisateur, telles que les clics de bouton.

- **ViewController.Designer.cs** est un fichier généré, créé par le concepteur IOS pour mapper l’interface créée visuellement au code. Étant donné que les modifications apportées à ce fichier seront remplacées, elles ne doivent pas être modifiées. Dans ce fichier, les déclarations de propriété permettent au code de la `ViewController` classe d’accéder, par **nom**, aux contrôles configurés dans le concepteur iOS. L’ouverture de **ViewController.Designer.cs** affiche le code suivant :

```csharp
namespace Designer
{
    [Register ("ViewController")]
    partial class ViewController
    {
        [Outlet]
        [GeneratedCode ("iOS Designer", "1.0")]
        UIKit.UIButton SubmitButton { get; set; }

        void ReleaseDesignerOutlets ()
        {
            if (SubmitButton != null) {
                SubmitButton.Dispose ();
                SubmitButton = null;
            }
        }
    }
}
```

La `SubmitButton` déclaration de propriété connecte la `ViewController` classe entière, pas seulement le fichier **ViewController.Designer.cs** , au bouton défini dans la table de montage séquentiel. Étant donné que **ViewController.cs** définit une partie de la `ViewController` classe, il a accès à `SubmitButton` .

La capture d’écran suivante montre que IntelliSense reconnaît désormais la `SubmitButton` référence dans **ViewController.cs**:

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

[![IntelliSense reconnaissant la référence SubmitButton](introduction-images/6-submitbuttonintellisense-vsmac.png "IntelliSense reconnaissant la référence SubmitButton")](introduction-images/6-submitbuttonintellisense-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![IntelliSense reconnaissant la référence SubmitButton](introduction-images/6-submitbuttonintellisense-vs.png "IntelliSense reconnaissant la référence SubmitButton")](introduction-images/6-submitbuttonintellisense-vs-large.png#lightbox)

-----

Cette section a démontré comment créer un bouton dans le concepteur iOS et comment accéder à ce bouton dans le code.

Le reste de ce document fournit une vue d’ensemble supplémentaire du concepteur iOS.

## <a name="ios-designer-basics"></a>notions de base du concepteur iOS

Cette section présente les composants du concepteur iOS et fournit une visite guidée de ses fonctionnalités.

### <a name="launching-the-ios-designer"></a>Lancement du concepteur iOS

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Les projets Xamarin. iOS créés avec Visual Studio pour Mac incluent une table de montage séquentiel. Pour afficher le contenu d’une table de montage séquentiel, double-cliquez sur le fichier. Storyboard dans le **panneau solutions**:

[![Une table de montage séquentiel ouverte dans le concepteur iOS](introduction-images/7-storyboardopen-vsmac.png "Une table de montage séquentiel ouverte dans le concepteur iOS")](introduction-images/7-storyboardopen-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

La plupart des projets Xamarin. iOS créés avec Visual Studio incluent une table de montage séquentiel. Pour afficher le contenu d’une table de montage séquentiel, double-cliquez sur le fichier. Storyboard dans le **Explorateur de solutions**:

[![Une table de montage séquentiel ouverte dans le concepteur iOS](introduction-images/7-storyboardopen-vs.png "Une table de montage séquentiel ouverte dans le concepteur iOS")](introduction-images/7-storyboardopen-vs-large.png#lightbox)

-----

<a name="iOS_Designer_features"></a>

### <a name="ios-designer-features"></a>fonctionnalités du concepteur iOS

Le concepteur iOS comprend six sections principales :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

[![Sections du concepteur iOS](introduction-images/8-sixpartsofiosdesigner-vsmac.png "Sections du concepteur iOS")](introduction-images/8-sixpartsofiosdesigner-vsmac-large.png#lightbox)

1. **Aire de conception** : espace de travail principal du concepteur iOS. Affiché dans la zone de document, il permet la construction visuelle d’interfaces utilisateur.
2. **Barre d’outils contraintes** : permet de basculer entre le mode d’édition de frame et le mode de modification des contraintes, deux méthodes différentes pour positionner les éléments dans une interface utilisateur.
3. **Boîte à outils** : répertorie les contrôleurs, les objets, les contrôles, les vues de données, les détecteurs de mouvement, les fenêtres et les barres qui peuvent être glissées sur l’aire de conception et ajoutées à une interface utilisateur.
4. **Panneau Propriétés** : affiche les propriétés du contrôle sélectionné, y compris l’identité, les styles visuels, l’accessibilité, la disposition et le comportement.
5. **Structure du document** : affiche l’arborescence des contrôles qui composent la disposition de l’interface en cours de modification. Cliquer sur un élément dans l’arborescence le sélectionne dans le concepteur iOS et affiche ses propriétés dans la  **panneau Propriétés**. Cela est pratique pour sélectionner un contrôle spécifique dans une interface utilisateur profondément imbriquée.
6. **Barre d’outils inférieure** : contient des options permettant de modifier le mode d’affichage du fichier. Storyboard ou. XIB dans le concepteur iOS, y compris l’appareil, l’orientation et le zoom.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Sections du concepteur iOS](introduction-images/8-sixpartsofiosdesigner-vs.png "Sections du concepteur iOS")](introduction-images/8-sixpartsofiosdesigner-vs-large.png#lightbox)

1. **Aire de conception** : espace de travail principal du concepteur iOS. Affiché dans la zone de document, il permet la construction visuelle d’interfaces utilisateur.
2. **Barre d’outils contraintes** : permet de basculer entre le mode d’édition de frame et le mode de modification des contraintes, deux méthodes différentes pour positionner les éléments dans une interface utilisateur.
3. **Boîte à outils** : répertorie les contrôleurs, les objets, les contrôles, les vues de données, les détecteurs de mouvement, les fenêtres et les barres qui peuvent être glissées sur l’aire de conception et ajoutées à une interface utilisateur.
4. **Fenêtre Propriétés** : affiche les propriétés du contrôle sélectionné, y compris l’identité, les styles visuels, l’accessibilité, la disposition et le comportement.
5. **Structure du document** : affiche l’arborescence des contrôles qui composent la disposition de l’interface en cours de modification. Cliquer sur un élément dans l’arborescence le sélectionne dans le concepteur iOS et affiche ses propriétés dans la  **fenêtre Propriétés**. Cela est pratique pour sélectionner un contrôle spécifique dans une interface utilisateur profondément imbriquée.
6. **Barre d’outils inférieure** : contient des options permettant de modifier le mode d’affichage du fichier. Storyboard ou. XIB dans le concepteur iOS, y compris l’appareil, l’orientation et le zoom.

-----

### <a name="design-workflow"></a>Flux de travail de conception

#### <a name="adding-a-control-to-the-interface"></a>Ajout d’un contrôle à l’interface

Pour ajouter un contrôle à une interface, faites-le glisser à partir de la **boîte à outils** et déposez-le sur l’aire de conception. Lors de l’ajout ou du positionnement d’un contrôle, les instructions verticales et horizontales mettent en surbrillance les positions de disposition couramment utilisées, telles que le centre vertical, le centre horizontal et les marges :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

![Sur l’aire de conception, les directives mettent en évidence les positions de disposition couramment utilisées](introduction-images/9-layoutguides-vsmac.png "Sur l’aire de conception, les directives mettent en évidence les positions de disposition couramment utilisées")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Sur l’aire de conception, les directives mettent en évidence les positions de disposition couramment utilisées](introduction-images/9-layoutguides-vs.png "Sur l’aire de conception, les directives mettent en évidence les positions de disposition couramment utilisées")

-----

La ligne en pointillés bleus de l’exemple ci-dessus fournit une indication horizontale sur l’alignement visuel du Centre pour faciliter la position du bouton.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

#### <a name="context-menu-commands"></a>Commandes du menu contextuel

Un menu contextuel est disponible à la fois sur l’aire de conception et dans la **structure du document**. Ce menu fournit des commandes pour le contrôle sélectionné et son parent, ce qui est utile lorsque vous utilisez des vues dans une hiérarchie imbriquée :

[![Menu contextuel sur l’aire de conception](introduction-images/10-contextmenudesignsurface-vsmac.png "Menu contextuel sur l’aire de conception")](introduction-images/10-contextmenudesignsurface-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

-----

### <a name="constraints-toolbar"></a>Barre d’outils contraintes

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

[![Barre d’outils contraintes](introduction-images/11-constraintstoolbar-vsmac.png "Barre d’outils contraintes")](introduction-images/11-constraintstoolbar-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Barre d’outils contraintes](introduction-images/11-constraintstoolbar-vs.png "Barre d’outils contraintes")](introduction-images/11-constraintstoolbar-vs-large.png#lightbox)

-----

La barre d’outils contraintes a été mise à jour et se compose à présent de deux contrôles : le mode d’édition de frame/mode de modification de contrainte bascule et le bouton mettre à jour les contraintes/mettre à jour les frames.

#### <a name="frame-editing-mode--constraint-editing-mode-toggle"></a>Mode de modification de frame/mode de modification de contrainte

Dans les versions précédentes du concepteur iOS, en cliquant sur une vue déjà sélectionnée sur l’aire de conception, basculez entre le mode de modification de l’image et le mode de modification des contraintes. À présent, un contrôle de basculement dans la barre d’outils contraintes change de mode d’édition.

- Mode d’édition de frame :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

![Bouton du mode d’édition de frame](introduction-images/12a-frameeditingmode-vsmac.png "Bouton du mode d’édition de frame")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Bouton du mode d’édition de frame](introduction-images/12a-frameeditingmode-vs.png "Bouton du mode d’édition de frame")

-----

- Mode de modification des contraintes :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

![Bouton mode de modification de contrainte](introduction-images/12b-constrainteditingmode-vsmac.png "Bouton mode de modification de contrainte")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Bouton mode de modification de contrainte](introduction-images/12b-constrainteditingmode-vs.png "Bouton mode de modification de contrainte")

-----

#### <a name="update-constraints--update-frames-button"></a>Bouton mettre à jour les contraintes/mettre à jour les frames

Le bouton mettre à jour les contraintes/mettre à jour les frames se trouve à droite du mode d’édition de frame/mode de modification de contrainte.

- En mode d’édition de frames, cliquez sur ce bouton pour ajuster les images des éléments sélectionnés en fonction de leurs contraintes.
- En mode de modification de contrainte, un clic sur ce bouton ajuste les contraintes de tous les éléments sélectionnés pour qu’ils correspondent à leurs frames.

### <a name="bottom-toolbar"></a>Barre d’outils inférieure

La barre d’outils inférieure permet de sélectionner l’appareil, l’orientation et le zoom utilisés pour afficher un storyboard ou un fichier. XIB dans le concepteur iOS :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

[![La barre d’outils inférieure, utilisée pour sélectionner un appareil et une orientation pour l’aire de conception](introduction-images/13-bottomtoolbar-vsmac.png "La barre d’outils inférieure, utilisée pour sélectionner un appareil et une orientation pour l’aire de conception")](introduction-images/13-bottomtoolbar-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![La barre d’outils inférieure, utilisée pour sélectionner un appareil et une orientation pour l’aire de conception](introduction-images/13-bottomtoolbar-vs.png "La barre d’outils inférieure, utilisée pour sélectionner un appareil et une orientation pour l’aire de conception")](introduction-images/13-bottomtoolbar-vs-large.png#lightbox)

-----

#### <a name="device-and-orientation"></a>Appareil et orientation

Une fois développée, la barre d’outils inférieure affiche tous les périphériques, les orientations et/ou les adaptations applicables au document actif. En cliquant dessus, vous modifiez la vue affichée sur l’aire de conception. 

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

[![La barre d’outils inférieure, développée pour afficher les appareils et les orientations](introduction-images/14-bottomtoolbarexpanded-vsmac.png "La barre d’outils inférieure, développée pour afficher les appareils et les orientations")](introduction-images/14-bottomtoolbarexpanded-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![La barre d’outils inférieure, développée pour afficher les appareils et les orientations](introduction-images/14-bottomtoolbarexpanded-vs.png "La barre d’outils inférieure, développée pour afficher les appareils et les orientations")](introduction-images/14-bottomtoolbarexpanded-vs-large.png#lightbox)

-----

Notez que la sélection d’un appareil et d’une orientation modifie uniquement la manière dont le concepteur iOS affiche un aperçu de la conception. Quelle que soit la sélection actuelle, les contraintes nouvellement ajoutées sont appliquées sur l’ensemble des appareils et des orientations, sauf si le bouton **modifier les traits** a été utilisé pour spécifier un autre élément.

Lorsque les [classes de taille](~/ios/user-interface/storyboards/unified-storyboards.md#size-classes) sont [activées](~/ios/user-interface/storyboards/unified-storyboards.md#enabling-size-classes), le bouton **modifier les traits** s’affiche dans la barre d’outils inférieure développée.  En cliquant sur le bouton **modifier les traits** , vous affichez les options de création d’une variante d’interface en fonction de la classe de taille représentée par l’appareil et l’orientation sélectionnés. Prenons les exemples suivants :

- Si **iPhone se**  /  **portrait**, est sélectionné, menu segue fournit des options pour créer une variation d’interface pour la classe compact Width, normal height. 
- Si **iPad Pro 9,7 "**  /  **mode**  /  **plein écran** est sélectionné, menu segue fournit des options pour créer une variation d’interface pour la largeur normale, classe de taille normale.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

[![La barre d’outils inférieure utilisée pour faire varier une interface par classe de taille](introduction-images/15-edittraitsbutton-vsmac.png "La barre d’outils inférieure utilisée pour faire varier une interface par classe de taille")](introduction-images/15-edittraitsbutton-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![La barre d’outils inférieure utilisée pour faire varier une interface par classe de taille](introduction-images/15-edittraitsbutton-vs.png "La barre d’outils inférieure utilisée pour faire varier une interface par classe de taille")](introduction-images/15-edittraitsbutton-vs-large.png#lightbox)

-----

#### <a name="zoom-controls"></a>Contrôles de zoom

L’aire de conception prend en charge le zoom à l’aide de plusieurs contrôles :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

![Contrôles de zoom dans la barre d’outils inférieure](introduction-images/16-zoomcontrols-vsmac.png "Contrôles de zoom dans la barre d’outils inférieure")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Contrôles de zoom dans la barre d’outils inférieure](introduction-images/16-zoomcontrols-vs.png "Contrôles de zoom dans la barre d’outils inférieure")

-----

Les contrôles incluent les éléments suivants :

1. Zoom pour ajuster
2. Faire un zoom arrière
3. Faire un zoom avant
4. Taille réelle (taille de 1:1 pixels)

Ces contrôles ajustent le zoom sur l’aire de conception. Ils n’affectent pas l’interface utilisateur de l’application au moment de l’exécution.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

### <a name="properties-pad"></a>Panneau Propriétés

Utilisez la **panneau Propriétés** pour modifier l’identité, les styles visuels, l’accessibilité et le comportement d’un contrôle. La capture d’écran suivante illustre les options de **panneau Propriétés** pour un bouton :

[![Panneau Propriétés d’un bouton](introduction-images/17-buttonpropertiespad-vsmac.png "Panneau Propriétés d’un bouton")](introduction-images/17-buttonpropertiespad-vsmac-large.png#lightbox)
#### <a name="properties-pad-sections"></a>Sections Panneau Propriétés

Le **panneau Propriétés** contient trois sections :

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

### <a name="properties-window"></a>Fenêtre Propriétés

Utilisez la **fenêtre Propriétés** pour modifier l’identité, les styles visuels, l’accessibilité et le comportement d’un contrôle. La capture d’écran suivante illustre les options de la **fenêtre Propriétés** d’un bouton :

[![Fenêtre Propriétés d’un bouton](introduction-images/17-buttonpropertieswindow-vs.png "Fenêtre Propriétés d’un bouton")](introduction-images/17-buttonpropertieswindow-vs-large.png#lightbox)

#### <a name="properties-window-sections"></a>Sections de la fenêtre Propriétés

La **fenêtre Propriétés** contient trois sections :

-----

1. **Widget** : propriétés principales du contrôle, telles que le nom, la classe, les propriétés de style, etc. Les propriétés pour gérer le contenu du contrôle sont généralement placées ici.
2. **Disposition** : les propriétés qui assurent le suivi de la position et de la taille du contrôle, y compris les contraintes et les frames, sont répertoriées ici.
3. **Événements** : les événements et les gestionnaires d’événements sont spécifiés ici. Utile pour la gestion des événements tels que la saisie tactile, le TAP, le glissement, etc. Les événements peuvent également être gérés directement dans le code.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

#### <a name="editing-properties-in-the-properties-pad"></a>Modification des propriétés dans le Panneau Propriétés

En plus de la modification visuelle sur l’aire de conception, le concepteur iOS prend en charge la modification des propriétés dans le **panneau Propriétés**. Les propriétés disponibles changent en fonction du contrôle sélectionné, comme illustré par les captures d’écran ci-dessous :

[![Propriétés du bouton](introduction-images/18a-buttonpropertiespad-vsmac.png "Propriétés du bouton")](introduction-images/18a-buttonpropertiespad-vsmac-large.png#lightbox)

[![Afficher les propriétés du contrôleur](introduction-images/18b-viewcontrollerpropertiespad-vsmac.png "Afficher les propriétés du contrôleur")](introduction-images/18b-viewcontrollerpropertiespad-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

#### <a name="editing-properties-in-the-properties-window"></a>Modification des propriétés dans la fenêtre Propriétés

En plus de la modification visuelle sur l’aire de conception, le concepteur iOS prend en charge la modification des propriétés dans la **fenêtre Propriétés**. Les propriétés disponibles changent en fonction du contrôle sélectionné, comme illustré par les captures d’écran ci-dessous :

[![Propriétés du bouton](introduction-images/18a-buttonpropertieswindow-vs.png "Propriétés du bouton")](introduction-images/18a-buttonpropertieswindow-vs-large.png#lightbox)

[![Afficher les propriétés du contrôleur](introduction-images/18b-viewcontrollerpropertieswindow-vs.png "Afficher les propriétés du contrôleur")](introduction-images/18b-viewcontrollerpropertieswindow-vs-large.png#lightbox)

-----

> [!IMPORTANT]
> La section identité du Panneau Propriétés affiche maintenant un champ **module** . Il est nécessaire de renseigner cette section uniquement en cas d’interopérabilité avec les classes SWIFT. Utilisez-le pour entrer un nom de module pour les classes SWIFT, qui sont des espaces de noms.

#### <a name="default-values"></a>Valeurs par défaut

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

De nombreuses propriétés de la **panneau Propriétés** affichent aucune valeur ou une valeur par défaut. Toutefois, le code de l’application peut toujours modifier ces valeurs. Le **panneau Propriétés** n’affiche pas de valeurs définies dans le code.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

De nombreuses propriétés de la **fenêtre Propriétés** n’affichent aucune valeur ou une valeur par défaut. Toutefois, le code de l’application peut toujours modifier ces valeurs. La **fenêtre Propriétés** n’affiche pas les valeurs définies dans le code.

-----

#### <a name="event-handlers"></a>Gestionnaires d’événements

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Pour spécifier des gestionnaires d’événements personnalisés pour différents événements, utilisez l’onglet **événements** de la **panneau Propriétés**. Par exemple, dans la capture d’écran ci-dessous, une `HandleClick` méthode gère l’événement d' **effleurement** du bouton :

[![Le Panneau Propriétés, avec un gestionnaire d’événements défini pour un bouton](introduction-images/19-buttonpropertiespadevents-vsmac.png "Le Panneau Propriétés, avec un gestionnaire d’événements défini pour un bouton")](introduction-images/19-buttonpropertiespadevents-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Pour spécifier des gestionnaires d’événements personnalisés pour différents événements, utilisez l’onglet **événements** de la **fenêtre Propriétés**. Par exemple, dans la capture d’écran ci-dessous, une `HandleClick` méthode gère l’événement d' **effleurement** du bouton :

[![Fenêtre Propriétés, avec un gestionnaire d’événements défini pour un bouton](introduction-images/19-buttonpropertieswindowevents-vs.png "Fenêtre Propriétés, avec un gestionnaire d’événements défini pour un bouton")](introduction-images/19-buttonpropertieswindowevents-vs-large.png#lightbox)

-----

Une fois qu’un gestionnaire d’événements a été spécifié, une méthode du même nom doit être ajoutée à la classe de contrôleur d’affichage correspondante. Dans le cas contraire, une `unrecognized selector` exception se produit lorsque le bouton est frappé :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

[![Exception de sélecteur non reconnue](introduction-images/20-unrecognizedselector-vsmac.png "Exception de sélecteur non reconnue")](introduction-images/20-unrecognizedselector-vsmac-large.png#lightbox)

Notez qu’une fois qu’un gestionnaire d’événements a été spécifié dans le **panneau Propriétés**, le concepteur iOS ouvre immédiatement le fichier de code correspondant et offre l’insertion de la déclaration de méthode. 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Exception de sélecteur non reconnue](introduction-images/20-unrecognizedselector-vs.png "Exception de sélecteur non reconnue")](introduction-images/20-unrecognizedselector-vs-large.png#lightbox)

-----

Pour obtenir un exemple qui utilise des gestionnaires d’événements personnalisés, reportez-vous au [Guide de prise en main Hello, iOS](~/ios/get-started/hello-ios/index.md).

### <a name="outline-view"></a>vue Structure

Le concepteur iOS peut également afficher la hiérarchie des contrôles d’une interface en tant que plan. Le plan est disponible en sélectionnant l’onglet **structure du document** , comme indiqué ci-dessous :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

[![Structure du document](introduction-images/21-buttonoutlineview-vsmac.png "Structure du document")](introduction-images/21-buttonoutlineview-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Structure du document](introduction-images/21-buttonoutlineview-vs.png "Structure du document")](introduction-images/21-buttonoutlineview-vs-large.png#lightbox)

-----

Le contrôle sélectionné en mode plan reste synchronisé avec le contrôle sélectionné sur l’aire de conception.  Cette fonctionnalité est utile pour sélectionner un élément dans une hiérarchie d’interface profondément imbriquée.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

## <a name="revert-to-xcode"></a>Revenir à Xcode

Il est possible d’utiliser le concepteur iOS et Xcode Interface Builder de façon interchangeable. Pour ouvrir une table de montage séquentiel ou un fichier. XIB dans Xcode Interface Builder, cliquez avec le bouton droit sur le fichier et sélectionnez **Ouvrir avec > Interface Builder Xcode**, comme illustré dans la capture d’écran ci-dessous :

[![Ouverture d’une table de montage séquentiel dans Xcode Interface Builder](introduction-images/22-openinxcodeinterfacebuilder-vsmac.png "Ouverture d’une table de montage séquentiel dans Xcode Interface Builder")](introduction-images/22-openinxcodeinterfacebuilder-vsmac-large.png#lightbox)

Après avoir apporté les modifications dans Xcode Interface Builder, enregistrez le fichier et revenez à Visual Studio pour Mac. Les modifications sont synchronisées avec le projet Xamarin. iOS.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="revert-to-xcode"></a>Revenir à Xcode

Il est possible d’utiliser le concepteur iOS et Xcode Interface Builder de façon interchangeable, mais Xcode Interface Builder est disponible uniquement sur Mac. Pour ouvrir une table de montage séquentiel ou un fichier. XIB dans Xcode Interface Builder sur un Mac, ouvrez la solution contenant le projet Xamarin. iOS dans [Visual Studio pour Mac](/visualstudio/mac/), cliquez avec le bouton droit sur le fichier et sélectionnez **ouvrir avec > Interface Builder Xcode**, comme illustré dans la capture d’écran ci-dessous :

[![Ouverture d’une table de montage séquentiel dans Xcode Interface Builder](introduction-images/22-openinxcodeinterfacebuilder-vsmac.png "Ouverture d’une table de montage séquentiel dans Xcode Interface Builder")](introduction-images/22-openinxcodeinterfacebuilder-vsmac-large.png#lightbox)

Après avoir apporté les modifications dans Xcode Interface Builder, enregistrez le fichier et revenez à Visual Studio pour Mac. Les modifications sont synchronisées avec le projet Xamarin. iOS.

-----

## <a name="xib-support"></a>prise en charge de. XIB

Le concepteur iOS prend en charge la création, la modification et la gestion des fichiers. XIB. Il s’agit de fichiers XML qui représenter une des vues personnalisées uniques qui peuvent être ajoutées à la hiérarchie d’affichage d’une application. Un fichier. XIB représente généralement l’interface pour une vue ou un écran unique dans une application, tandis qu’un Storyboard représente de nombreux écrans et les transitions entre eux.

Il existe de nombreuses opinions sur la solution : les fichiers. XIB, les storyboards, ou le code, fonctionnent mieux pour la création et la gestion d’une interface utilisateur. En réalité, il n’y a pas de solution parfaite et il est toujours intéressant de prendre en compte le meilleur outil pour le travail. Cela dit, les fichiers. XIB sont généralement les plus puissants lorsqu’ils sont utilisés pour représenter une vue personnalisée nécessaire à plusieurs endroits dans une application, par exemple une cellule d’affichage de tableau personnalisée. 

Vous trouverez plus de documentation sur l’utilisation des fichiers. XIB dans les recettes suivantes :

- [Utilisation du modèle View. XIB](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/templates/using_the_ios_view_xib_template)
- [Création d’un TableViewCell personnalisé à l’aide d’un. XIB](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/tables/custom-tableviewcell)
- [Création d’un écran de lancement à l’aide d’un. XIB](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/templates/launchscreen-xib)

Pour plus d’informations sur l’utilisation des storyboards, reportez-vous à la [Présentation des storyboards](~/ios/user-interface/storyboards/index.md).

Cet exemple et d’autres guides relatifs au concepteur iOS font référence à l’utilisation des storyboards comme approche standard pour la création d’interfaces utilisateur, puisque la plupart des modèles de projet Xamarin. iOS fournissent un Storyboard par défaut.

## <a name="summary"></a>Résumé

Ce guide fournit une introduction au concepteur iOS, décrivant ses fonctionnalités et détaillant les outils qu’il propose pour concevoir de magnifiques interfaces utilisateur.

## <a name="related-links"></a>Liens associés

- [Introduction aux storyboards](~/ios/user-interface/storyboards/index.md)
- [Procédures pas à pas pour les contrôles iOS](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Hello, iOS](~/ios/get-started/hello-ios/index.md)
- [Hello, iOS Multiscreen](~/ios/get-started/hello-ios-multiscreen/index.md)
- [Présentation de Android Designer](~/android/user-interface/android-designer/index.md)
- [Classes et méthodes partielles](/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods)
- [Plonger dans le Xamarin Designer pour iOS-évoluer 2014 (vidéo)](https://www.youtube.com/watch?v=W4H9uLjoEjM)
- [Utilisation du concepteur iOS pour créer un écran de lancement (vidéo)](https://university.xamarin.com/lightninglectures/using-the-ios-designer-to-create-a-launch-screen)
