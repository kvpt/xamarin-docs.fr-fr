---
title: Orientation de l’appareil
description: Cet article explique comment les applications de Xamarin.Forms disposition l’aspect souhaité dans les orientations portrait et paysage.
ms.prod: xamarin
ms.assetid: 11A1D327-2DF3-4F3B-810D-6C95B71D27B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/09/2015
ms.openlocfilehash: f7526b6cecebadd30e95718b7e537026a6557adf
ms.sourcegitcommit: f43d5ecafd19cbc5cce39201916a83927a34617a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2020
ms.locfileid: "78292429"
---
# <a name="device-orientation"></a>Orientation de l’appareil

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-responsivelayout)

Il est important de prendre en compte la façon dont votre application sera utilisée et comment l’orientation paysage peut être incorporée pour améliorer l’expérience utilisateur. Dispositions individuelles peuvent être conçues pour prendre en charge plusieurs orientations et mieux utiliser l’espace disponible. Au niveau de l’application, rotation peut être désactivée ou activée.

<a name="Controlling_Orientation" />

## <a name="controlling-orientation"></a>Contrôler l’Orientation

Lorsque vous utilisez Xamarin.Forms, la méthode prise en charge du contrôle de l’orientation de l’appareil est d’utiliser les paramètres pour chaque projet individuel.

### <a name="ios"></a>iOS

Sur iOS, l’orientation de l’appareil est configurée pour les applications à l’aide du fichier **info. plist** . Ce fichier inclut les paramètres de l’orientation pour iPhone et iPod, ainsi que les paramètres pour iPad si l’application inclut en tant que cible. Vous trouverez ci-dessous des instructions spécifiques à votre IDE. Utilisez les options de l’IDE en haut de ce document pour sélectionner les instructions que vous aimeriez voir :

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Dans Visual Studio, ouvrez le projet iOS et ouvrez le fichier **info. plist**. Le fichier s’ouvre dans un panneau de configuration, en commençant par l’onglet des informations de déploiement iPhone :

![iPhone, informations de déploiement dans Visual Studio](device-orientation-images/orientation-vs-iphone.png)

Pour configurer l’orientation de iPad, sélectionnez l’onglet **informations de déploiement iPad** en haut à gauche du panneau, puis sélectionnez l’une des orientations disponibles :

![Orientations de l’appareil pris en charge dans Visual Studio](device-orientation-images/orientation-vs-ipad.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Dans Visual Studio pour Mac, ouvrez le projet iOS et ouvrez le fichier **info. plist**. Sous l’onglet **application** , les sections seront disponibles pour définir l’orientation :

![iPhone, informations de déploiement dans Visual Studio pour Mac](device-orientation-images/orientation-xam-ui.png)

Si vous préférez modifier les valeurs à l’aide d’une interface de l’éditeur de clé-valeur, sélectionnez l’onglet **Source**> en bas de l’écran :

![Prise en charge des Orientations de l’appareil dans Visual Studio pour Mac](device-orientation-images/orientation-xam-source.png)

-----

### <a name="android"></a>Android

Pour contrôler l’orientation sur Android, ouvrez **MainActivity.cs** et définissez l’orientation à l’aide de l’attribut en décorant la classe `MainActivity` :

```csharp
namespace MyRotatingApp.Droid
{
    [Activity (Label = "MyRotatingApp.Droid", Icon = "@drawable/icon", Theme = "@style/MainTheme", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation, ScreenOrientation = ScreenOrientation.Landscape)] //This is what controls orientation
    public class MainActivity : FormsAppCompatActivity
    {
        protected override void OnCreate (Bundle bundle)
...
```

Xamarin.Android prend en charge plusieurs options permettant de spécifier l’orientation :

- L' &ndash; **paysage** force l’orientation de l’application à être paysage, quelles que soient les données de capteur.
- L' &ndash; **portrait** force l’orientation de l’application à être portrait, indépendamment des données de capteur.
- L' **utilisateur** &ndash; entraîne la présentation de l’application à l’aide de l’orientation préférée de l’utilisateur.
- **Derrière** &ndash; fait que l’orientation de l’application est identique à l’orientation de l' [activité](xref:Android.App.Activity) qui se trouve derrière elle.
- Le **capteur** &ndash; entraîne la détermination de l’orientation de l’application par le capteur, même si l’utilisateur a désactivé la rotation automatique.
- **SensorLandscape** &ndash; fait en sorte que l’application utilise l’orientation paysage tout en utilisant les données de capteur pour modifier la direction d’affichage de l’écran (afin que l’écran ne soit pas visible à l’envers).
- **SensorPortrait** &ndash; fait en sorte que l’application utilise l’orientation portrait tout en utilisant les données de capteur pour modifier la direction d’affichage de l’écran (afin que l’écran ne soit pas visible à l’envers).
- **ReverseLandscape** &ndash; fait en sorte que l’application utilise l’orientation paysage, face à la direction opposée d’habitude, afin de s’afficher « à l’envers ».
- **ReversePortrait** &ndash; fait en sorte que l’application utilise l’orientation portrait, face à la direction opposée d’habitude, afin de s’afficher « à l’envers ».
- **FullSensor** &ndash; fait en sorte que l’application s’appuie sur des données de capteur pour sélectionner l’orientation correcte (parmi les 4 possibles).
- **FullUser** &ndash; fait en sorte que l’application utilise les préférences d’orientation de l’utilisateur. Si la rotation automatique est activée, toutes les 4 orientations peuvent être utilisées.
- **UserLandscape** &ndash; _\[pas pris en charge\]_ force l’application à utiliser l’orientation paysage, à moins que l’utilisateur n’ait activé la rotation automatique, auquel cas elle utilise le capteur pour déterminer l’orientation. Cette option s’arrête la compilation.
- **UserPortrait** &ndash; _\[pas pris en charge\]_ force l’application à utiliser l’orientation portrait, à moins que l’utilisateur n’ait activé la rotation automatique, auquel cas elle utilise le capteur pour déterminer l’orientation. Cette option s’arrête la compilation.
- **Verrouillé** &ndash; _\[pas pris en charge\]_ oblige l’application à utiliser l’orientation de l’écran, quel que soit son lancement, sans répondre aux modifications de l’orientation physique de l’appareil. Cette option s’arrête la compilation.

Notez que les API natives Android fournissent un contrôle important sur la gestion de l’orientation, y compris les options qui contredisent explicitement l’utilisateur exprimée préférences.

### <a name="universal-windows-platform"></a>Plateforme Windows universelle

Sur la plateforme Windows universelle (UWP), les orientations prises en charge sont définies dans le fichier **Package. appxmanifest** . Ouvrant le manifeste révèle un panneau de configuration où des orientations prises en charge peuvent être sélectionnées.

<a name="Reacting_to_Changes_in_Orientation" />

## <a name="reacting-to-changes-in-orientation"></a>Réagir aux modifications dans l’Orientation

Xamarin.Forms ne propose pas de tous les événements natifs pour avertir votre application de changements d’orientation dans le code partagé. Toutefois,[Xamarin. Essentials](~/essentials/index.md) contient une classe [`DeviceDisplay`] qui fournit des notifications de changements d’orientation.

Pour détecter les orientations sans Xamarin. Essentials, surveillez l’événement `SizeChanged` du `Page`, qui se déclenche lorsque la largeur ou la hauteur de l' `Page` change. Lorsque la largeur de la `Page` est supérieure à la hauteur, l’appareil est en mode paysage. Pour plus d’informations, consultez [afficher une image en fonction de l’orientation de l’écran](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/screen-orientation).

Il est également possible de remplacer la méthode [`OnSizeAllocated`](xref:Xamarin.Forms.Page.OnSizeAllocated*) sur une `Page`, en insérant ici toute logique de modification de la disposition. La méthode `OnSizeAllocated` est appelée chaque fois qu’une nouvelle taille est allouée à un `Page`, ce qui se produit à chaque rotation de l’appareil. Notez que l’implémentation de base de `OnSizeAllocated` exécute des fonctions de disposition importantes. il est donc important d’appeler l’implémentation de base dans la substitution :

```csharp
protected override void OnSizeAllocated(double width, double height)
{
    base.OnSizeAllocated(width, height); //must be called
}
```

Échec pour effectuer cette étape entraîne une page non fonctionnel.

Notez que la méthode `OnSizeAllocated` peut être appelée plusieurs fois lors de la rotation d’un appareil. Modification de votre disposition chaque fois est inutile de ressources et peut entraîner le scintillement. Envisagez d’utiliser une variable d’instance au sein de votre page pour déterminer si l’orientation est en mode paysage ou portrait et redessiner uniquement lorsqu’il existe une modification :

```csharp
private double width = 0;
private double height = 0;

protected override void OnSizeAllocated(double width, double height)
{
    base.OnSizeAllocated(width, height); //must be called
    if (this.width != width || this.height != height)
    {
        this.width = width;
        this.height = height;
        //reconfigure layout
    }
}
```

Une fois qu’une modification de l’orientation de l’appareil a été détectée, vous souhaiterez ajouter ou supprimer des affichages supplémentaires à votre interface utilisateur de réagir aux changements de l’espace disponible. Par exemple, considérez la calculatrice intégrée sur chaque plateforme en portrait :

![](device-orientation-images/calculator-portrait.png "Calculator Application in Portrait")

et paysage :

![](device-orientation-images/calculator-landscape.png "Calculator Application in Landscape")

Notez que les applications tirer parti de l’espace disponible en ajoutant davantage de fonctionnalités en mode paysage.

<a name="Responsive_Layout" />

## <a name="responsive-layout"></a>Disposition dynamique

Il est possible d’interfaces de conception à l’aide de mises en page intégrés afin qu’ils passent normalement lors de la rotation de l’appareil. Lorsque vous concevez des interfaces qui continueront à être attrayante lors de la réponse aux modifications de l’orientation envisager les règles générales suivantes :

- **Soyez attentif aux ratios** &ndash; les changements d’orientation peuvent entraîner des problèmes lorsque certaines hypothèses sont prises en ce qui concerne les ratios. Par exemple, une vue ayant suffisamment d’espace disque à 1/3 de l’espace vertical d’un écran en mode portrait ne correspondre pas 1/3 de l’espace vertical en mode paysage.
- Soyez **vigilant avec les valeurs absolues &ndash; les** valeurs absolues (en pixels) qui ont un sens dans le portrait peuvent ne pas avoir de sens en mode paysage. Lorsque les valeurs absolues sont nécessaires, utilisez dispositions imbriquées pour isoler leur impact. Par exemple, il serait raisonnable d’utiliser des valeurs absolues dans un `TableView` `ItemTemplate` lorsque le modèle d’élément a une hauteur uniforme garantie.

Les règles ci-dessus s’appliquent également lorsque l’implémentation des interfaces pour plusieurs tailles d’écran et sont généralement considérés comme des meilleures pratiques. Le reste de ce guide explique des exemples spécifiques de dispositions réactives à l’aide de chacune des mises en page principales dans Xamarin.Forms.

> [!NOTE]
> Pour plus de clarté, les sections suivantes montrent comment implémenter des dispositions réactives à l’aide d’un seul type de `Layout` à la fois. Dans la pratique, il est souvent plus simple de mélanger `Layout`s pour obtenir une disposition souhaitée à l’aide de la `Layout` la plus simple ou la plus intuitive pour chaque composant.

### <a name="stacklayout"></a>StackLayout

Tenez compte de l’application suivante, affichée en portrait :

![](device-orientation-images/photo-stack-portrait.png "Photo Application in Portrait")

et paysage :

![](device-orientation-images/photo-stack-landscape.png "Photo Application in Landscape")

Ceci se produit avec le XAML suivant :

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.StackLayoutPageXaml"
Title="Stack Photo Editor - XAML">
    <ContentPage.Content>
        <StackLayout Spacing="10" Padding="5" Orientation="Vertical"
        x:Name="outerStack"> <!-- can change orientation to make responsive -->
            <ScrollView>
                <StackLayout Spacing="5" HorizontalOptions="FillAndExpand"
                    WidthRequest="1000">
                    <StackLayout Orientation="Horizontal">
                        <Label Text="Name: " WidthRequest="75"
                            HorizontalOptions="Start" />
                        <Entry Text="deer.jpg"
                            HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                    <StackLayout Orientation="Horizontal">
                        <Label Text="Date: " WidthRequest="75"
                            HorizontalOptions="Start" />
                        <Entry Text="07/05/2015"
                            HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                    <StackLayout Orientation="Horizontal">
                        <Label Text="Tags:" WidthRequest="75"
                            HorizontalOptions="Start" />
                        <Entry Text="deer, tiger"
                            HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                    <StackLayout Orientation="Horizontal">
                        <Button Text="Save" HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                </StackLayout>
            </ScrollView>
            <Image  Source="deer.jpg" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Certains C# sont utilisés pour modifier l’orientation d' `outerStack` en fonction de l’orientation de l’appareil :

```csharp
protected override void OnSizeAllocated (double width, double height){
    base.OnSizeAllocated (width, height);
    if (width != this.width || height != this.height) {
        this.width = width;
        this.height = height;
        if (width > height) {
            outerStack.Orientation = StackOrientation.Horizontal;
        } else {
            outerStack.Orientation = StackOrientation.Vertical;
        }
    }
}
```

Notez les points suivants :

- `outerStack` est ajusté pour présenter l’image et les contrôles sous la forme d’une pile horizontale ou verticale en fonction de l’orientation, afin de tirer le meilleur parti de l’espace disponible.

### <a name="absolutelayout"></a>AbsoluteLayout

Tenez compte de l’application suivante, affichée en portrait :

![](device-orientation-images/photo-abs-portrait.png "Photo Application in Portrait")

et paysage :

![](device-orientation-images/photo-abs-landscape.png "Photo Application in Landscape")

Ceci se produit avec le XAML suivant :

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.AbsoluteLayoutPageXaml"
Title="AbsoluteLayout - XAML" BackgroundImageSource="deer.jpg">
    <ContentPage.Content>
        <AbsoluteLayout>
            <ScrollView AbsoluteLayout.LayoutBounds="0,0,1,1"
                AbsoluteLayout.LayoutFlags="PositionProportional,SizeProportional">
                <AbsoluteLayout>
                    <Image Source="deer.jpg"
                        AbsoluteLayout.LayoutBounds=".5,0,300,300"
                        AbsoluteLayout.LayoutFlags="PositionProportional" />
                    <BoxView Color="#CC1A7019" AbsoluteLayout.LayoutBounds=".5
                        300,.7,50" AbsoluteLayout.LayoutFlags="XProportional
                        WidthProportional" />
                    <Label Text="deer.jpg" AbsoluteLayout.LayoutBounds = ".5
                        310,1, 50" AbsoluteLayout.LayoutFlags="XProportional
                        WidthProportional" HorizontalTextAlignment="Center" TextColor="White" />
                </AbsoluteLayout>
            </ScrollView>
            <Button Text="Previous" AbsoluteLayout.LayoutBounds="0,1,.5,60"
                AbsoluteLayout.LayoutFlags="PositionProportional
                    WidthProportional"
                BackgroundColor="White" TextColor="Green" BorderRadius="0" />
            <Button Text="Next" AbsoluteLayout.LayoutBounds="1,1,.5,60"
                AbsoluteLayout.LayoutFlags="PositionProportional
                    WidthProportional" BackgroundColor="White"
                    TextColor="Green" BorderRadius="0" />
        </AbsoluteLayout>
    </ContentPage.Content>
</ContentPage>
```

Notez les points suivants :

- En raison de la façon dont la page a été disposée, il est inutile pour le code de procédure pour introduire la réactivité.
- Le `ScrollView` est utilisé pour permettre l’affichage de l’étiquette même lorsque la hauteur de l’écran est inférieure à la somme des hauteurs fixes des boutons et de l’image.

### <a name="relativelayout"></a>RelativeLayout

Tenez compte de l’application suivante, affichée en portrait :

![](device-orientation-images/photo-rel-portrait.png "Photo Application in Portrait")

et paysage :

![](device-orientation-images/photo-rel-landscape.png "Photo Application in Landscape")

Ceci se produit avec le XAML suivant :

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.RelativeLayoutPageXaml"
Title="RelativeLayout - XAML"
BackgroundImageSource="deer.jpg">
    <ContentPage.Content>
        <RelativeLayout x:Name="outerLayout">
            <BoxView BackgroundColor="#AA1A7019"
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=1}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1}"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=0,Constant=0}" />
            <ScrollView
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=1}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1,Constant=-60}"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=0,Constant=0}">
                <RelativeLayout>
                    <Image Source="deer.jpg" x:Name="imageDeer"
                        RelativeLayout.WidthConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=.8}"
                        RelativeLayout.XConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=.1}"
                        RelativeLayout.YConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Height,Factor=0,Constant=10}" />
                    <Label Text="deer.jpg" HorizontalTextAlignment="Center"
                        RelativeLayout.WidthConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=1}"
                        RelativeLayout.HeightConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Height,Factor=0,Constant=75}"
                        RelativeLayout.XConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                        RelativeLayout.YConstraint="{ConstraintExpression
                            Type=RelativeToView,ElementName=imageDeer,Property=Height,Factor=1,Constant=20}" />
                </RelativeLayout>

            </ScrollView>

            <Button Text="Previous" BackgroundColor="White" TextColor="Green" BorderRadius="0"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1,Constant=-60}"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=60}"
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=.5}"
                 />
            <Button Text="Next" BackgroundColor="White" TextColor="Green" BorderRadius="0"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=.5}"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1,Constant=-60}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=60}"
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=.5}"
                />
        </RelativeLayout>
    </ContentPage.Content>
</ContentPage>

```

Notez les points suivants :

- En raison de la façon dont la page a été disposée, il est inutile pour le code de procédure pour introduire la réactivité.
- Le `ScrollView` est utilisé pour permettre l’affichage de l’étiquette même lorsque la hauteur de l’écran est inférieure à la somme des hauteurs fixes des boutons et de l’image.

### <a name="grid"></a>Grille

Tenez compte de l’application suivante, affichée en portrait :

![](device-orientation-images/photo-grid-portrait.png "Photo Application in Portrait")

et paysage :

![](device-orientation-images/photo-grid-landscape.png "Photo Application in Landscape")

Ceci se produit avec le XAML suivant :

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.GridPageXaml"
Title="Grid - XAML">
    <ContentPage.Content>
        <Grid x:Name="outerGrid">
            <Grid.RowDefinitions>
                <RowDefinition Height="*" />
                <RowDefinition Height="60" />
            </Grid.RowDefinitions>
            <Grid x:Name="innerGrid" Grid.Row="0" Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="*" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Image Source="deer.jpg" Grid.Row="0" Grid.Column="0" HeightRequest="300" WidthRequest="300" />
                <Grid x:Name="controlsGrid" Grid.Row="0" Grid.Column="1" >
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto" />
                        <RowDefinition Height="Auto" />
                        <RowDefinition Height="Auto" />
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="Auto" />
                        <ColumnDefinition Width="*" />
                    </Grid.ColumnDefinitions>
                    <Label Text="Name:" Grid.Row="0" Grid.Column="0" />
                    <Label Text="Date:" Grid.Row="1" Grid.Column="0" />
                    <Label Text="Tags:" Grid.Row="2" Grid.Column="0" />
                    <Entry Grid.Row="0" Grid.Column="1" />
                    <Entry Grid.Row="1" Grid.Column="1" />
                    <Entry Grid.Row="2" Grid.Column="1" />
                </Grid>
            </Grid>
            <Grid x:Name="buttonsGrid" Grid.Row="1">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Previous" Grid.Column="0" />
                <Button Text="Save" Grid.Column="1" />
                <Button Text="Next" Grid.Column="2" />
            </Grid>
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

En même temps que le code procédural suivant pour gérer les changements de rotation :

```csharp
private double width;
private double height;

protected override void OnSizeAllocated (double width, double height){
    base.OnSizeAllocated (width, height);
    if (width != this.width || height != this.height) {
        this.width = width;
        this.height = height;
        if (width > height) {
            innerGrid.RowDefinitions.Clear();
            innerGrid.ColumnDefinitions.Clear ();
            innerGrid.RowDefinitions.Add (new RowDefinition{ Height = new GridLength (1, GridUnitType.Star) });
            innerGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
            innerGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
            innerGrid.Children.Remove (controlsGrid);
            innerGrid.Children.Add (controlsGrid, 1, 0);
        } else {
            innerGrid.RowDefinitions.Clear();
            innerGrid.ColumnDefinitions.Clear ();
            innerGrid.ColumnDefinitions.Add (new ColumnDefinition{ Width = new GridLength (1, GridUnitType.Star) });
            innerGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Auto) });
            innerGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
            innerGrid.Children.Remove (controlsGrid);
            innerGrid.Children.Add (controlsGrid, 0, 1);
        }
    }
}
```

Notez les points suivants :

- En raison de la façon dont la page a été disposée, il existe une méthode pour modifier la position de grille des contrôles.

## <a name="related-links"></a>Liens connexes

- [Disposition (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Exemple BusinessTumble (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
- [Disposition réactive (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-responsivelayout)
- [Afficher une image en fonction de l’orientation de l’écran](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/screen-orientation)
