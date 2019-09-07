---
title: Contrôles standard dans Xamarin. Mac
description: Cet article traite de l’utilisation des contrôles AppKit standard, tels que les boutons, les étiquettes, les champs de texte, les cases à cocher et les contrôles segmentés dans une application Xamarin. Mac. Il décrit comment les ajouter à une interface avec Interface Builder et interagir avec eux dans le code.
ms.prod: xamarin
ms.assetid: d2593883-d255-431f-9781-75f04d8cecea
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: 18ce7eb4dcd68596027f739579fd5a4b70497829
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772702"
---
# <a name="standard-controls-in-xamarinmac"></a>Contrôles standard dans Xamarin. Mac

_Cet article traite de l’utilisation des contrôles AppKit standard, tels que les boutons, les étiquettes, les champs de texte, les cases à cocher et les contrôles segmentés dans une application Xamarin. Mac. Il décrit comment les ajouter à une interface avec Interface Builder et interagir avec eux dans le code._

Lorsque vous travaillez C# avec et .net dans une application Xamarin. Mac, vous avez accès aux mêmes contrôles AppKit qu’un développeur qui travaille en *objective-C* et *Xcode* . Comme Xamarin. Mac s’intègre directement à Xcode, vous pouvez utiliser la _Interface Builder_ de Xcode pour créer et gérer vos contrôles AppKit (ou éventuellement les créer directement dans C# le code).

Les contrôles AppKit sont les éléments d’interface utilisateur utilisés pour créer l’interface utilisateur de votre application Xamarin. Mac. Ils se composent d’éléments tels que des boutons, des étiquettes, des champs de texte, des cases à cocher et des contrôles segmentés, et qui entraînent des actions instantanées ou des résultats visibles quand un utilisateur les manipule.

[![](standard-controls-images/intro01.png "Exemple d’écran principal d’application")](standard-controls-images/intro01.png#lightbox)

Dans cet article, nous allons aborder les bases de l’utilisation des contrôles AppKit dans une application Xamarin. Mac. Nous vous recommandons vivement d’utiliser l’article [Hello, Mac](~/mac/get-started/hello-mac.md) , en particulier la [Présentation de Xcode et Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) et les sections [actions et actions](~/mac/get-started/hello-mac.md#outlets-and-actions) , car il aborde les concepts et les techniques clés que nous allons utiliser dans. Cet article.

Vous pouvez également jeter un coup d’œil à la section [exposition des C# classes/méthodes à Objective-C](~/mac/internals/how-it-works.md) du document [Internals Xamarin. Mac.](~/mac/internals/how-it-works.md) elle explique également les `Register` commandes `Export` et utilisées pour relier vos C# classes à Objets objective-C et éléments d’interface utilisateur.

<a name="Introduction_to_Controls_and_Views" />

## <a name="introduction-to-controls-and-views"></a>Présentation des contrôles et des vues

macOS (anciennement Mac OS X) fournit un ensemble standard de contrôles d’interface utilisateur par le biais de l’infrastructure AppKit. Ils se composent d’éléments tels que des boutons, des étiquettes, des champs de texte, des cases à cocher et des contrôles segmentés, et qui entraînent des actions instantanées ou des résultats visibles quand un utilisateur les manipule.

Tous les contrôles AppKit ont une apparence standard et intégrée qui convient à la plupart des utilisations, certains spécifient une autre apparence pour une utilisation dans une zone de cadre de fenêtre ou dans un contexte d' _effet vibrateur_ , par exemple dans une zone de barre latérale ou dans un centre de notifications sélection.

Apple suggère les recommandations suivantes lors de l’utilisation de contrôles AppKit :

- Évitez de mélanger les tailles de contrôle dans la même vue.
- En général, évitez de redimensionner les contrôles verticalement.
- Utilisez la police système et la taille de texte appropriée dans un contrôle.
- Utilisez l’espacement approprié entre les contrôles.

Pour plus d’informations, consultez la section [à propos des contrôles et des vues](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1) dans les instructions relatives à l' [interface humaine OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)d’Apple.

<a name="Using_Controls_in_a_Window_Frame" />

### <a name="using-controls-in-a-window-frame"></a>Utilisation de contrôles dans un frame de fenêtre

Il existe un sous-ensemble de contrôles AppKit qui incluent un style d’affichage qui leur permet d’être inclus dans la zone Frame d’une fenêtre. Pour obtenir un exemple, consultez la barre d’outils de l’application de messagerie :

[![](standard-controls-images/mailapp.png "Frame de fenêtre Mac")](standard-controls-images/mailapp.png#lightbox)

- **Bouton** `NSButton` de`NSTexturedRoundedBezelStyle`texture ronde-a avec un style.
- **Contrôle segmenté arrondi texturé** -A `NSSegmentedControl` `NSSegmentStyleTexturedRounded`avec un style.
- **Contrôle segmenté arrondi texturé** -A `NSSegmentedControl` `NSSegmentStyleSeparated`avec un style.
- **Menu contextuel** `NSPopUpButton` `NSTexturedRoundedBezelStyle`avec texture ronde-a avec un style.
- **Menu déroulant** `NSPopUpButton` `NSTexturedRoundedBezelStyle`avec texture ronde-a avec un style.
- **Barre de recherche** - `NSSearchField`A.

Apple suggère les recommandations suivantes lors de l’utilisation de contrôles AppKit dans un frame de fenêtre :

- N’utilisez pas de styles de contrôle spécifiques à un frame de fenêtre dans le corps de la fenêtre.
- N’utilisez pas de contrôles de corps de fenêtre ou de styles dans le frame de fenêtre.

Pour plus d’informations, consultez la section [à propos des contrôles et des vues](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1) dans les instructions relatives à l' [interface humaine OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)d’Apple.

<a name="Creating_a_User_Interface_in_Interface_Builder" />

## <a name="creating-a-user-interface-in-interface-builder"></a>Création d’une interface utilisateur dans Interface Builder

Lorsque vous créez une application de cacao Xamarin. Mac, vous recevez une fenêtre vide standard par défaut. Cette fenêtre est définie dans un `.storyboard` fichier inclus automatiquement dans le projet. Pour modifier votre conception Windows, dans le **Explorateur de solutions**, double-cliquez `Main.storyboard` sur le fichier :

[![](standard-controls-images/edit01.png "Sélection de la table de montage séquentiel principale dans le Explorateur de solutions")](standard-controls-images/edit01.png#lightbox)

Cette opération ouvre la conception de la fenêtre dans le Interface Builder de Xcode :

[![](standard-controls-images/edit02.png "Modification de la table de montage séquentiel dans Xcode")](standard-controls-images/edit02.png#lightbox)

Pour créer votre interface utilisateur, vous allez faire glisser des éléments d’interface utilisateur (contrôles AppKit) de l' **inspecteur de bibliothèque** vers l' **éditeur d’interface** dans Interface Builder. Dans l’exemple ci-dessous, un contrôle de **vue fractionnée verticale** a été médicament à partir de l' **inspecteur de bibliothèque** et placé dans la fenêtre de l' **éditeur d’interface**:

[![](standard-controls-images/edit03.png "Sélection d’un affichage fractionné à partir de la bibliothèque")](standard-controls-images/edit03.png#lightbox)

Pour plus d’informations sur la création d’une interface utilisateur dans Interface Builder, consultez notre [Introduction à Xcode et à](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) la documentation de Interface Builder.

<a name="Sizing_and_Positioning" />

### <a name="sizing-and-positioning"></a>Dimensionnement et positionnement

Une fois qu’un contrôle a été inclus dans l’interface utilisateur, utilisez l' **éditeur de contrainte** pour définir son emplacement et sa taille en entrant des valeurs manuellement et contrôlez la position et la taille du contrôle automatiquement lorsque la fenêtre ou la vue parente est redimensionnée :

[![](standard-controls-images/edit04.png "Définition des contraintes")](standard-controls-images/edit04.png#lightbox)

Utilisez les **I-poutres rouges** autour de l’extérieur de la zone **Autoresizing** pour _coller_ un contrôle à un emplacement donné (x, y). Exemple : 

[![](standard-controls-images/edit05.png "Modification d’une contrainte")](standard-controls-images/edit05.png#lightbox)

Spécifie que le contrôle sélectionné (dans l’éditeur de l'**interface**de l' **affichage** & des hiérarchies) sera bloqué sur l’emplacement supérieur et droit de la fenêtre ou de la vue, car elle est redimensionnée ou déplacée. 

Autres éléments des propriétés du contrôle de l’éditeur, telles que la hauteur et la largeur :

[![](standard-controls-images/edit06.png "Définition de la hauteur")](standard-controls-images/edit06.png#lightbox)

Vous pouvez également contrôler l’alignement des éléments avec des contraintes à l’aide de l' **éditeur d’alignement**:

[![](standard-controls-images/edit07.png "Éditeur d’alignement")](standard-controls-images/edit07.png#lightbox)

> [!IMPORTANT]
> Contrairement à iOS où (0, 0) est l’angle supérieur gauche de l’écran, dans macOS (0,0) est l’angle inférieur gauche. Cela est dû au fait que macOS utilise un système de coordonnées mathématique avec les valeurs numériques qui augmenteront de la valeur vers le haut et vers la droite. Vous devez tenir compte de ce point lorsque vous placez des contrôles AppKit sur une interface utilisateur.

<a name="Setting_a_Custom_Class" />

### <a name="setting-a-custom-class"></a>Définition d’une classe personnalisée

Il peut arriver que vous utilisiez des contrôles AppKit dont vous aurez besoin pour effectuer une sous-classe et un contrôle existant et créer une version personnalisée de cette classe. Par exemple, la définition d’une version personnalisée de la liste source :

```csharp
using System;
using AppKit;
using Foundation;

namespace AppKit
{
    [Register("SourceListView")]
    public class SourceListView : NSOutlineView
    {
        #region Computed Properties
        public SourceListDataSource Data {
            get {return (SourceListDataSource)this.DataSource; }
        }
        #endregion

        #region Constructors
        public SourceListView ()
        {

        }

        public SourceListView (IntPtr handle) : base(handle)
        {

        }

        public SourceListView (NSCoder coder) : base(coder)
        {

        }

        public SourceListView (NSObjectFlag t) : base(t)
        {

        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

        }
        #endregion

        #region Public Methods
        public void Initialize() {

            // Initialize this instance
            this.DataSource = new SourceListDataSource (this);
            this.Delegate = new SourceListDelegate (this);

        }

        public void AddItem(SourceListItem item) {
            if (Data != null) {
                Data.Items.Add (item);
            }
        }
        #endregion

        #region Events
        public delegate void ItemSelectedDelegate(SourceListItem item);
        public event ItemSelectedDelegate ItemSelected;

        internal void RaiseItemSelected(SourceListItem item) {
            // Inform caller
            if (this.ItemSelected != null) {
                this.ItemSelected (item);
            }
        }
        #endregion
    }
}
```

Où l' `[Register("SourceListView")]` instruction expose la `SourceListView` classe à Objective-C afin que puisse être utilisé dans Interface Builder. Pour plus d’informations, consultez la section [exposition C# des classes/méthodes à Objective-C](~/mac/internals/how-it-works.md) du [document Internals Xamarin. Mac.](~/mac/internals/how-it-works.md) elle explique les `Register` commandes `Export` et utilisées pour relier vos C# classes à Objets objective-C et éléments d’interface utilisateur.

Avec le code ci-dessus en place, vous pouvez faire glisser un contrôle AppKit du type de base que vous étendez sur l’aire de conception (dans l’exemple ci-dessous, une **liste source**), basculer vers l' **inspecteur d’identité** et définir la **classe personnalisée** sur le nom vous l’avez exposé à objective- `SourceListView`C (exemple) :

[![](standard-controls-images/edit10.png "Définition d’une classe personnalisée dans Xcode")](standard-controls-images/edit10.png#lightbox)

<a name="Exposing_Outlets_and_Actions" />

### <a name="exposing-outlets-and-actions"></a>Exposition des prises et des actions

Pour qu’un contrôle AppKit soit accessible dans C# le code, il doit être exposé en tant que **sortie** ou **action**. Pour ce faire, sélectionnez le contrôle donné dans la **hiérarchie** de l’interface ou l’éditeur de l' **interface** et basculez vers la **vue Assistant** ( `.h` Assurez-vous que le de votre fenêtre est sélectionné pour modification) :

[![](standard-controls-images/edit11.png "Sélection du fichier approprié à modifier")](standard-controls-images/edit11.png#lightbox)

Ctrl +-faites glisser à partir du contrôle AppKit `.h` sur le fichier d’offre pour commencer à créer une **sortie** ou une **action**:

[![](standard-controls-images/edit12.png "Glissement pour créer un orifice ou une action")](standard-controls-images/edit12.png#lightbox)

Sélectionnez le type d’exposition à créer et donnez un **nom**à la **sortie** ou à l' **action** : 

[![](standard-controls-images/edit13.png "Configuration de la sortie ou de l’action")](standard-controls-images/edit13.png#lightbox)

Pour plus d’informations sur l’utilisation des **prises** et des **actions**, consultez la section [sorties et actions](~/mac/get-started/hello-mac.md#outlets-and-actions) de notre [Introduction à Xcode et à](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) la documentation de Interface Builder.

<a name="Synchronizing_Changes_with_Xcode" />

### <a name="synchronizing-changes-with-xcode"></a>Synchronisation des modifications avec Xcode

Lorsque vous revenez à Visual Studio pour Mac à partir de Xcode, toutes les modifications que vous avez apportées à Xcode seront automatiquement synchronisées avec votre projet Xamarin. Mac.

`SplitViewController.designer.cs` Si vous sélectionnez dans la **Explorateur de solutions** vous pouvez voir comment votre **sortie** et votre **action** ont été raccordées dans notre C# code :

[![](standard-controls-images/sync01.png "Synchronisation des modifications avec Xcode")](standard-controls-images/sync01.png#lightbox)

Notez comment la définition dans le `SplitViewController.designer.cs` fichier :

```csharp
[Outlet]
AppKit.NSSplitViewItem LeftController { get; set; }

[Outlet]
AppKit.NSSplitViewItem RightController { get; set; }

[Outlet]
AppKit.NSSplitView SplitView { get; set; }
```

Aligner avec la définition dans le `MainWindow.h` fichier dans Xcode :

```csharp
@interface SplitViewController : NSSplitViewController {
    NSSplitViewItem *_LeftController;
    NSSplitViewItem *_RightController;
    NSSplitView *_SplitView;
}

@property (nonatomic, retain) IBOutlet NSSplitViewItem *LeftController;

@property (nonatomic, retain) IBOutlet NSSplitViewItem *RightController;

@property (nonatomic, retain) IBOutlet NSSplitView *SplitView;
```

Comme vous pouvez le voir, Visual Studio pour Mac écoute les `.h` modifications apportées au fichier, puis synchronise automatiquement ces modifications dans le fichier respectif `.designer.cs` pour les exposer à votre application. Vous remarquerez peut `SplitViewController.designer.cs` -être également que est une classe partielle, de sorte que `SplitViewController.cs` Visual Studio pour Mac n’a pas à modifier, ce qui remplacerait les modifications que nous avons apportées à la classe.

Normalement, vous n’avez jamais besoin d' `SplitViewController.designer.cs` ouvrir le vous-même, il a été présenté ici à titre éducatif uniquement.

> [!IMPORTANT]
> Dans la plupart des cas, Visual Studio pour Mac affiche automatiquement les modifications apportées à Xcode et les synchronise sur votre projet Xamarin. Mac. Dans le cas isolé où la synchronisation ne se produit pas automatiquement, revenez à Xcode, puis à nouveau à Visual Studio pour Mac. Cela déclenche normalement un cycle de synchronisation.

<a name="Working_with_Buttons" />

## <a name="working-with-buttons"></a>Utilisation des boutons

AppKit fournit plusieurs types de bouton qui peuvent être utilisés dans la conception de votre interface utilisateur. Pour plus d’informations, consultez la section des [boutons](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsButtons.html#//apple_ref/doc/uid/20000957-CH48-SW1) des instructions de l' [interface utilisateur d’Apple OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/buttons01.png "Exemple des différents types de bouton")](standard-controls-images/buttons01.png#lightbox)

Si un bouton a été exposé par le biais d’une **prise**, le code suivant répond à l’opération :

```csharp
ButtonOutlet.Activated += (sender, e) => {
        FeedbackLabel.StringValue = "Button Outlet Pressed";
};
```

Pour les boutons qui ont été exposésvia des actions `public partial` , une méthode est automatiquement créée pour vous avec le nom que vous avez choisi dans Xcode. Pour répondre à l' **action**, terminez la méthode partielle de la classe sur laquelle l' **action** a été définie. Exemple :

```csharp
partial void ButtonAction (Foundation.NSObject sender) {
    // Do something in response to the Action
    FeedbackLabel.StringValue = "Button Action Pressed";
}
```

Pour les boutons qui ont un État ( **par exemple, activé ou** **désactivé**), l’État peut être vérifié ou `State` défini avec la `NSCellStateValue` propriété par rapport à l’énumération. Exemple :

```csharp
DisclosureButton.Activated += (sender, e) => {
    LorumIpsum.Hidden = (DisclosureButton.State == NSCellStateValue.On);
};
```

Où `NSCellStateValue` peut être :

- **On** -le bouton fait l’objet d’un push ou le contrôle est sélectionné (par exemple, une coche dans une case à cocher).
- **Off** -le bouton n’a pas fait l’objet d’un push ou le contrôle n’est pas sélectionné.
- **Mixte** : un mélange d' **États activés et** **désactivés** .

<a name="Mark-a-Button-as-Default-and-Set-Key-Equivalent" />

### <a name="mark-a-button-as-default-and-set-key-equivalent"></a>Marquer un bouton comme valeur par défaut et définir l’équivalent de la clé

Pour tout bouton que vous avez ajouté à une conception d’interface utilisateur, vous pouvez marquer ce bouton comme bouton _par défaut_ qui sera activé quand l’utilisateur appuie sur la touche **Retour/entrée** du clavier. Dans macOS, ce bouton reçoit une couleur d’arrière-plan bleue par défaut.

Pour définir un bouton par défaut, sélectionnez-le dans le Interface Builder de Xcode. Ensuite, dans l' **inspecteur d’attribut**, sélectionnez le champ **clé équivalente** et appuyez sur la touche **Retour/entrée** :

[![](standard-controls-images/buttons03.png "Modification de la clé équivalente")](standard-controls-images/buttons03.png#lightbox)

De même, vous pouvez assigner n’importe quelle séquence clé qui peut être utilisée pour activer le bouton à l’aide du clavier au lieu de la souris. Par exemple, en appuyant sur les touches de commande C dans l’image ci-dessus.

Lorsque l’application est exécutée et que la fenêtre avec le bouton est clé et Focus, si l’utilisateur appuie sur la commande-C, l’action du bouton est activée (comme si l’utilisateur avait cliqué sur le bouton).

<a name="Working_with_Checkboxes_and_Radio_Buttons" />

## <a name="working-with-checkboxes-and-radio-buttons"></a>Utilisation des cases à cocher et des cases d’option

AppKit fournit plusieurs types de cases à cocher et de groupes de cases d’option qui peuvent être utilisés dans la conception de votre interface utilisateur. Pour plus d’informations, consultez la section des [boutons](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsButtons.html#//apple_ref/doc/uid/20000957-CH48-SW1) des instructions de l' [interface utilisateur d’Apple OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/buttons02.png "Exemple des types de cases à cocher disponibles")](standard-controls-images/buttons02.png#lightbox)

Les cases à cocher et les cases d’option (exposées via **des** **prises**) ont un État (par exemple, activé ou **désactivé**), l’état `State` peut être vérifié `NSCellStateValue` ou défini avec la propriété par rapport à l’énumération. Exemple :

```csharp
AdjustTime.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Adjust Time: {0}",AdjustTime.State == NSCellStateValue.On);
};
```

Où `NSCellStateValue` peut être :

- **On** -le bouton fait l’objet d’un push ou le contrôle est sélectionné (par exemple, une coche dans une case à cocher).
- **Off** -le bouton n’a pas fait l’objet d’un push ou le contrôle n’est pas sélectionné.
- **Mixte** : un mélange d' **États activés et** **désactivés** .

Pour sélectionner un bouton dans un groupe de cases d’option, exposez la case d' option pour sélectionner comme `State` sortie et définissez sa propriété. Par exemple :

```csharp
partial void SelectCar (Foundation.NSObject sender) {
    TransportationCar.State = NSCellStateValue.On;
    FeedbackLabel.StringValue = "Car Selected";
}
```

Pour obtenir une collection de cases d’option pour agir en tant que groupe et gérer automatiquement l’état sélectionné, créez une **action** et associez-lui chaque bouton du groupe :

![](standard-controls-images/buttons04.png "Création d’une action")

Ensuite, attribuez un `Tag` unique à chaque case d’option dans l' **inspecteur d’attribut**:

![](standard-controls-images/buttons05.png "Modification d’une balise de case d’option")

Enregistrez vos modifications et revenez à Visual Studio pour Mac, ajoutez le code pour gérer l' **action** à laquelle toutes les cases d’option sont attachées :

```csharp
partial void NumberChanged(Foundation.NSObject sender)
{
    var check = sender as NSButton;
    Console.WriteLine("Changed to {0}", check.Tag);
}
```

Vous pouvez utiliser la `Tag` propriété pour voir quelle case d’option a été sélectionnée.

<a name="Working_with_Menu_Controls" />

## <a name="working-with-menu-controls"></a>Utilisation des contrôles menu

AppKit fournit plusieurs types de contrôles de menu qui peuvent être utilisés dans la conception de votre interface utilisateur. Pour plus d’informations, consultez la section [commandes de menu](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlswithMenus.html#//apple_ref/doc/uid/20000957-CH100-SW1) des indications relatives à l’interface utilisateur d’Apple [OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/menu01.png "Exemples de contrôles de menu")](standard-controls-images/menu01.png#lightbox)

<a name="Providing-Menu-Control-Data" />

### <a name="providing-menu-control-data"></a>Fourniture de données de contrôle de menu

Les contrôles de menu disponibles pour macOS peuvent être configurés pour remplir la liste déroulante à partir d’une liste interne (qui peut être prédéfinie dans Interface Builder ou renseignée via du code) ou en fournissant votre propre source de données externe personnalisée.

<a name="Working-with-Internal-Data" />

#### <a name="working-with-internal-data"></a>Utilisation des données internes

En plus de définir des éléments dans Interface Builder, les contrôles de menu `NSComboBox`(tels que) fournissent un ensemble complet de méthodes qui vous permettent d’ajouter, de modifier ou de supprimer les éléments de la liste interne qu’ils maintiennent :

- `Add`-Ajoute un nouvel élément à la fin de la liste.
- `GetItem`-Retourne l’élément à l’index donné.
- `Insert`-Insère un nouvel élément dans la liste à l’emplacement donné.
- `IndexOf`-Retourne l’index de l’élément donné.
- `Remove`-Supprime l’élément donné de la liste.
- `RemoveAll`: Supprime tous les éléments de la liste.
- `RemoveAt`: Supprime l’élément au niveau de l’index donné.
- `Count`-Retourne le nombre d’éléments dans la liste.

> [!IMPORTANT]
> Si vous utilisez une source de données extern (`UsesDataSource = true`), l’appel de l’une des méthodes ci-dessus lèvera une exception.

<a name="Working-with-an-External-Data-Source" />

#### <a name="working-with-an-external-data-source"></a>Utilisation d’une source de données externe

Au lieu d’utiliser les données internes intégrées pour fournir les lignes pour votre contrôle de menu, vous pouvez éventuellement utiliser une source de données externe et fournir votre propre magasin de stockage pour les éléments (par exemple, une base de données SQLite).

Pour utiliser une source de données externe, vous allez créer une instance de la source de données du contrôle menu`NSComboBoxDataSource` (par exemple) et remplacer plusieurs méthodes pour fournir les données nécessaires :

- `ItemCount`-Retourne le nombre d’éléments dans la liste.
- `ObjectValueForItem`-Retourne la valeur de l’élément pour un index donné.
- `IndexOfItem`-Retourne l’index pour la valeur de l’élément.
- `CompletedString`-Retourne la première valeur de l’élément correspondant pour la valeur de l’élément partiellement typé. Cette méthode est appelée uniquement si la saisie semi-automatique a`Completes = true`été activée ().

Pour plus d’informations, consultez la section [bases de données et comboboxs](~/mac/app-fundamentals/databases.md#Databases-and-ComboBoxes) du document [utilisation des bases de données](~/mac/app-fundamentals/databases.md) .

<a name="Adjusting-the-Lists-Appearance" />

### <a name="adjusting-the-lists-appearance"></a>Ajustement de l’apparence de la liste

Les méthodes suivantes sont disponibles pour ajuster l’apparence du contrôle de menu :

- `HasVerticalScroller`-Si `true`, le contrôle affichera une barre de défilement verticale. 
- `VisibleItems`-Ajuster le nombre d’éléments affichés lorsque le contrôle est ouvert. La valeur par défaut est cinq (5).
- `IntercellSpacing`-Ajuster la quantité d’espace autour d’un élément donné en fournissant `NSSize` un où `Width` le spécifie les marges gauche `Height` et droite et spécifie l’espace avant et après un élément.
- `ItemHeight`: Spécifie la hauteur de chaque élément de la liste.

Pour les types de liste déroulante de `NSPopupButtons`, le premier élément de menu fournit le titre du contrôle. Par exemple : 

[![](standard-controls-images/menu02.png "Un exemple de contrôle de menu")](standard-controls-images/menu02.png#lightbox)

Pour modifier le titre, exposez cet élément en tant que **sortie** et utilisez un code similaire à ce qui suit :

```csharp
DropDownSelected.Title = "Item 1";
```

<a name="Manipulating-the-Selected-Items" />

### <a name="manipulating-the-selected-items"></a>Manipulation des éléments sélectionnés

Les méthodes et propriétés suivantes vous permettent de manipuler les éléments sélectionnés dans la liste du contrôle Menu :

- `SelectItem`-Sélectionne l’élément à l’index donné.
- `Select`-Sélectionnez la valeur de l’élément donné.
- `DeselectItem`-Désélectionne l’élément à l’index donné.
- `SelectedIndex`-Retourne l’index de l’élément actuellement sélectionné.
- `SelectedValue`-Retourne la valeur de l’élément actuellement sélectionné.

Utilisez le `ScrollItemAtIndexToTop` pour présenter l’élément à l’index donné en haut de la liste et le `ScrollItemAtIndexToVisible` pour faire défiler jusqu’à la liste jusqu’à ce que l’élément à l’index spécifié soit visible.

<a name="Responding to Events" />

### <a name="responding-to-events"></a>Réponse aux événements

Les contrôles de menu fournissent les événements suivants pour répondre à l’interaction de l’utilisateur :

- `SelectionChanged`-Est appelé quand l’utilisateur a sélectionné une valeur dans la liste.
- `SelectionIsChanging`-Est appelé avant que le nouvel élément sélectionné par l’utilisateur devienne la sélection active.
- `WillPopup`-Est appelé avant l’affichage de la liste déroulante d’éléments.
- `WillDismiss`-Est appelé avant la fermeture de la liste déroulante d’éléments.

Pour `NSComboBox` les contrôles, ils incluent tous les mêmes événements `NSTextField`que, tels que l' `Changed` événement qui est appelé chaque fois que l’utilisateur modifie la valeur du texte dans la zone de liste déroulante.

Si vous le souhaitez, vous pouvez répondre aux éléments de menu de données internes définis dans Interface Builder être sélectionnés en joignant l’élément à une **action** et utiliser du code semblable au suivant pour répondre à une **action** déclenchée par l’utilisateur :

```csharp
partial void ItemOne (Foundation.NSObject sender) {
    DropDownSelected.Title = "Item 1";
    FeedbackLabel.StringValue = "Item One Selected";
}
```

Pour plus d’informations sur l’utilisation des menus et des contrôles de menu, consultez nos [menus](~/mac/user-interface/menu.md) et [boutons contextuels et la documentation des listes déroulantes](~/mac/user-interface/menu.md) .

<a name="Working_with_Selection_Controls" />

## <a name="working-with-selection-controls"></a>Utilisation des contrôles de sélection

AppKit fournit plusieurs types de contrôles de sélection qui peuvent être utilisés dans la conception de votre interface utilisateur. Pour plus d’informations, consultez la section [contrôles de sélection](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsSelection.html#//apple_ref/doc/uid/20000957-CH49-SW1) des instructions de l’interface utilisateur d’Apple [OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/select01.png "Exemples de contrôles de sélection")](standard-controls-images/select01.png#lightbox)

Il existe deux façons de suivre le moment où un contrôle de sélection a une interaction avec l’utilisateur, en l’exposant comme une **action**. Exemple :

```csharp
partial void SegmentButtonPressed (Foundation.NSObject sender) {
    FeedbackLabel.StringValue = string.Format("Button {0} Pressed",SegmentButtons.SelectedSegment);
}
```

Ou en attachant un **délégué** à l' `Activated` événement. Exemple :

```csharp
TickedSlider.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Stepper Value: {0:###}",TickedSlider.IntValue);
};
```

Pour définir ou lire la valeur d’un contrôle Selection, utilisez la `IntValue` propriété. Exemple :

```csharp
FeedbackLabel.StringValue = string.Format("Stepper Value: {0:###}",TickedSlider.IntValue);
```

Les contrôles de spécialité (tels que les outils de couleur et d’image) ont des propriétés spécifiques pour leurs types de valeur. Par exemple :

```csharp
ColorWell.Color = NSColor.Red;
ImageWell.Image = NSImage.ImageNamed ("tag.png");

```

Le `NSDatePicker` possède les propriétés suivantes pour travailler directement avec la date et l’heure :

- **DateValue** : la valeur de date et d' `NSDate`heure actuelle en tant que.
- **Local** : emplacement de l’utilisateur sous la `NSLocal`forme d’un.
- **TimeInterval** : valeur d’heure sous la `Double`forme d’un.
- **TimeZone** : fuseau `NSTimeZone`horaire de l’utilisateur.

<a name="Working_with_Indicator_Controls" />

## <a name="working-with-indicator-controls"></a>Utilisation des contrôles d’indicateur

AppKit fournit plusieurs types de contrôles indicateur qui peuvent être utilisés dans la conception de votre interface utilisateur. Pour plus d’informations, consultez la section [contrôles d’indicateur](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsIndicators.html#//apple_ref/doc/uid/20000957-CH50-SW1) des indications relatives à l' [interface humaine OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)d’Apple. 

[![](standard-controls-images/level01.png "Exemples de contrôles d’indicateur")](standard-controls-images/level01.png#lightbox)

Il existe deux façons de suivre le moment où un contrôle indicateur a une interaction avec l’utilisateur, soit en l’exposant en tant qu' **action** **, soit** en joignant un `Activated` **délégué** à l’événement. Exemple :

```csharp
LevelIndicator.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Level: {0:###}",LevelIndicator.DoubleValue);
};
```

Pour lire ou définir la valeur du contrôle d’indicateur, utilisez la `DoubleValue` propriété. Exemple :

```csharp
FeedbackLabel.StringValue = string.Format("Rating: {0:###}",Rating.DoubleValue);
```

Les indicateurs de progression indéterminés et asynchrones doivent être animés lorsqu’ils sont affichés. Utilisez la `StartAnimation` méthode pour démarrer l’animation quand elles sont affichées. Exemple :

```csharp
Indeterminate.StartAnimation (this);
AsyncProgress.StartAnimation (this);
```

L’appel `StopAnimation` de la méthode arrêtera l’animation.

<a name="Working_with_Text_Controls" />

## <a name="working-with-text-controls"></a>Utilisation des contrôles de texte

AppKit fournit plusieurs types de contrôles de texte qui peuvent être utilisés dans la conception de votre interface utilisateur. Pour plus d’informations, consultez la section [contrôles de texte](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsText.html#//apple_ref/doc/uid/20000957-CH51-SW1) des instructions de l’interface utilisateur d’Apple [OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/text01.png "Exemples de contrôles de texte")](standard-controls-images/text01.png#lightbox)

Pour les champs de`NSTextField`texte (), les événements suivants peuvent être utilisés pour suivre l’interaction de l’utilisateur :

- **Changed** -est déclenché chaque fois que l’utilisateur modifie la valeur du champ. Par exemple, sur chaque caractère tapé.
- **EditingBegan** -est déclenché lorsque l’utilisateur sélectionne le champ à modifier.
- **EditingEnded** : lorsque l’utilisateur appuie sur la touche entrée dans le champ ou quitte le champ.

Utilisez la `StringValue` propriété pour lire ou définir la valeur du champ. Exemple :

```csharp
FeedbackLabel.StringValue = string.Format("User ID: {0}",UserField.StringValue);
```

Pour les champs qui affichent ou modifient des valeurs numériques, `IntValue` vous pouvez utiliser la propriété. Exemple :

```csharp
FeedbackLabel.StringValue = string.Format("Number: {0}",NumberField.IntValue);
```

`NSTextView` Fournit une zone d’affichage et de modification de texte complète avec une mise en forme intégrée. Comme un `NSTextField`, utilisez la `StringValue` propriété pour lire ou définir la valeur de la zone.

Pour obtenir un exemple d’utilisation complexe des affichages de texte dans une application Xamarin. Mac, consultez l' [exemple d’application SourceWriter](https://docs.microsoft.com/samples/xamarin/mac-samples/sourcewriter). SourceWriter est un éditeur de code source simple qui assure la prise en charge de la complétion de code et de la coloration syntaxique de base.

Le code SourceWriter a été entièrement commenté et, le cas échéant, des liens ont être fournis entre les principales technologies ou méthodes et des informations pertinentes dans la documentation des guides Xamarin.Mac.

<a name="Working_with_Content_Views" />

## <a name="working-with-content-views"></a>Utilisation des vues de contenu

AppKit fournit plusieurs types d’affichages de contenu qui peuvent être utilisés dans la conception de votre interface utilisateur. Pour plus d’informations, consultez la section [affichages du contenu](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsView.html#//apple_ref/doc/uid/20000957-CH52-SW1) des [instructions relatives à l’interface humaine OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)d’Apple.

[![](standard-controls-images/content01.png "Exemple d’affichage de contenu")](standard-controls-images/content01.png#lightbox)

<a name="Popovers" />

### <a name="popovers"></a>Popovers

Un menu segue est un élément d’interface utilisateur temporaire qui fournit des fonctionnalités directement liées à un contrôle spécifique ou à une zone à l’écran. Un menu segue flotte au-dessus de la fenêtre qui contient le contrôle ou la zone à laquelle il est associé, et sa bordure inclut une flèche pour indiquer le point à partir duquel il est apparu.

Pour créer un menu Segue, procédez comme suit :

1. Ouvrez le `.storyboard` fichier de la fenêtre auquel vous souhaitez ajouter un menu segue en double-cliquant dessus dans le **Explorateur de solutions**
2. Faites glisser un **contrôleur d’affichage** de l’inspecteur de **bibliothèque** vers l’éditeur d' **interface**: 

    [![](standard-controls-images/content02.png "Sélection d’un contrôleur d’affichage à partir de la bibliothèque")](standard-controls-images/content02.png#lightbox)
3. Définissez la taille et la disposition de la **vue personnalisée**: 

    [![](standard-controls-images/content04.png "Modification de la disposition")](standard-controls-images/content04.png#lightbox)
4. Cliquez avec le contrôle, puis faites glisser la source de la fenêtre contextuelle sur le **contrôleur d’affichage**: 

    [![](standard-controls-images/content05.png "Glissement pour créer un segue")](standard-controls-images/content05.png#lightbox)
5. Sélectionnez **menu segue** dans le menu contextuel : 

    [![](standard-controls-images/content06.png "Définition du type segue")](standard-controls-images/content06.png#lightbox)
6. Enregistrez vos modifications et revenez à Visual Studio pour Mac pour effectuer une synchronisation avec Xcode.

<a name="Tab_Views" />

### <a name="tab-views"></a>Vues d’onglets

Les vues d’onglets se composent d’une liste d’onglets (qui ressemble à un contrôle segmenté) associée à un ensemble de vues appelées _volets_. Lorsque l’utilisateur sélectionne un nouvel onglet, le volet qui lui est associé s’affiche. Chaque volet contient son propre ensemble de contrôles.

Quand vous utilisez une vue d’onglet dans le Interface Builder de Xcode, utilisez l' **inspecteur d’attribut** pour définir le nombre d’onglets :

[![](standard-controls-images/content08.png "Modification du nombre d’onglets")](standard-controls-images/content08.png#lightbox)

Sélectionnez chaque onglet dans la **hiérarchie** de l’interface pour définir son **titre** et ajouter des éléments d’interface utilisateur à son **volet**:

[![](standard-controls-images/content09.png "Modification des onglets dans Xcode")](standard-controls-images/content09.png#lightbox)

<a name="Data_Binding_AppKit_Controls" />

## <a name="data-binding-appkit-controls"></a>Contrôles de AppKit des liaisons de données

En utilisant le codage clé-valeur et les techniques de liaison de données dans votre application Xamarin. Mac, vous pouvez réduire la quantité de code que vous devez écrire et maintenir pour remplir et utiliser des éléments d’interface utilisateur. Vous avez également l’avantage de dissocier vos données de stockage (modèle de_données_) de votre interface utilisateur frontale (_Model-View-Controller_), ce qui vous permet de gérer plus facilement la conception d’applications plus souple.

Le codage de clé-valeur (KVC) est un mécanisme permettant d’accéder indirectement aux propriétés d’un objet, à l’aide de clés (chaînes spécialement mises en forme) pour identifier les propriétés au lieu d'`get/set`y accéder via des variables d’instance ou des méthodes d’accesseur (). En implémentant des accesseurs conformes de codage de clé-valeur dans votre application Xamarin. Mac, vous accédez à d’autres fonctionnalités macOS telles que l’observation de la valeur clé (KVO), la liaison de données, les données de base, les liaisons de cacao et la scriptabilité.

Pour plus d’informations, consultez la section [liaison de données simple](~/mac/app-fundamentals/databinding.md#Simple_Data_Binding) de notre documentation sur la [liaison de données et le codage de clé-valeur](~/mac/app-fundamentals/databinding.md) .

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a décrit en détail l’utilisation des contrôles AppKit standard, tels que les boutons, les étiquettes, les champs de texte, les cases à cocher et les contrôles segmentés dans une application Xamarin. Mac. Il m’a expliqué de les ajouter à la conception de l’interface utilisateur dans l’Interface Builder de Xcode, en les exposant au code via des prises et des C# actions et en utilisant des contrôles AppKit dans le code.

## <a name="related-links"></a>Liens associés

- [MacControls (exemple)](https://docs.microsoft.com/samples/xamarin/mac-samples/maccontrols)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Windows](~/mac/user-interface/window.md)
- [Liaison de données et codage de clé-valeur](~/mac/app-fundamentals/databinding.md)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [À propos des contrôles et des vues](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1)
